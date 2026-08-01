---
title: "Báo cáo công việc Tuần 7"
date: 2026-07-20
weight: 7
chapter: false
pre: " <b> 1.6. </b> "
---

### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 

[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Ứng dụng full-stack đầu tiên trên Đám mây AWS

Báo cáo này trình bày chi tiết từng bước triển khai ứng dụng web full-stack **Pokemon_unboxing** lên Amazon Web Services (AWS). Kiến trúc phân tách nghiêm ngặt các tầng frontend, backend và cơ sở dữ liệu để đảm bảo tính bảo mật, khả năng mở rộng và dễ dàng bảo trì.

### Kiến trúc Hạ tầng:

*   **Tầng Cơ sở dữ liệu (Database Tier):** Amazon RDS (PostgreSQL) nằm trong một private subnet.
*   **Tầng Tính toán (Compute Tier):** REST API viết bằng Java Spring Boot được container hóa qua Docker và điều phối trên Amazon ECS (AWS Fargate) đằng sau một Application Load Balancer (ALB).
*   **Tầng Trình diễn (Presentation Tier):** Ứng dụng React SPA (Vite) được lưu trữ (host) trên AWS Amplify với CDN toàn cầu.

---

## Giai đoạn 1: Cấp phát Cơ sở dữ liệu (Amazon RDS)

Để lưu trữ dữ liệu người dùng và thẻ bài một cách an toàn, cần có một instance PostgreSQL được quản lý.

1.  Điều hướng đến **Amazon RDS > Create database**.
2.  **Engine:** PostgreSQL.
3.  **Templates:** Free tier (hoặc Production cho môi trường thực tế).
4.  **Cài đặt (Settings):**
    *   **DB instance identifier (Định danh DB):** `pokemon-db`
    *   **Master username:** `postgres`
    *   **Master password:** *(Mật khẩu an toàn)*
5.  **Kết nối (Connectivity):**
    *   **Public access:** No *(Điều này cực kỳ quan trọng cho bảo mật)*.
    *   **VPC security group:** Tạo một nhóm mới tên là `pokemon-db-sg`.
6.  **Hành động:** Lưu lại URL Endpoint của cơ sở dữ liệu sau khi quá trình tạo hoàn tất.

---

## Giai đoạn 2: Container hóa Backend

Backend Spring Boot yêu cầu một Dockerfile multi-stage (đa giai đoạn) để biên dịch mã nguồn bằng Maven wrapper và đóng gói nó vào một image JRE gọn nhẹ.

**Boilerplate Dockerfile** *(Đặt tệp này bên trong thư mục `backend/project/`)*:

```dockerfile
# Stage 1: Build the application
FROM eclipse-temurin:17-jdk-alpine as builder
WORKDIR /app
COPY .mvn/ .mvn/
COPY mvnw pom.xml ./

# Fetch dependencies to optimize build cache
RUN ./mvnw dependency:go-offline
COPY src ./src
RUN ./mvnw clean package -DskipTests

# Stage 2: Create the runtime container
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## Giai đoạn 3: Amazon Elastic Container Registry (ECR)

1.  Điều hướng đến **Amazon ECR > Create repository**. Đặt tên cho nó là `pokemon-backend`.
2.  Xác thực terminal ở local của bạn với AWS ECR.

> **Ghi chú Cấu hình:** Nếu sử dụng Git Bash (Môi trường Linux trên Windows), hãy sử dụng lệnh AWS CLI tiêu chuẩn để tránh làm hỏng token (lỗi 400 Bad Request):

```bash
aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin <YOUR_ACCOUNT_ID>.dkr.ecr.ap-southeast-2.amazonaws.com
```

3.  Build và push image:

```bash
cd backend/project
docker build -t pokemon-backend .
docker tag pokemon-backend:latest <YOUR_ACCOUNT_ID>[.dkr.ecr.ap-southeast-2.amazonaws.com/pokemon-backend:latest](https://.dkr.ecr.ap-southeast-2.amazonaws.com/pokemon-backend:latest)
docker push <YOUR_ACCOUNT_ID>[.dkr.ecr.ap-southeast-2.amazonaws.com/pokemon-backend:latest](https://.dkr.ecr.ap-southeast-2.amazonaws.com/pokemon-backend:latest)
```

---

## Giai đoạn 4: Mạng & Cân bằng tải (ALB)

1.  Điều hướng đến **EC2 > Load Balancers > Create Load Balancer > Application Load Balancer**.
2.  **Tên (Name):** `pokemon-alb` (Internet-facing, IPv4).
3.  **Security Group:** Tạo `alb-sg` (Cho phép Inbound HTTP Port `80` từ `0.0.0.0/0`).
4.  **Lắng nghe và Định tuyến (Listeners and Routing):** Tạo một Target Group (Nhóm mục tiêu):
    *   **Loại mục tiêu (Target type):** IP addresses *(Bắt buộc đối với Fargate)*.
    *   **Tên Target group:** `pokemon-tg`.
    *   **Port:** `8080` (Mặc định của Spring Boot).

![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/9.png)

---

## Giai đoạn 5: Điều phối Tính toán (Amazon ECS Fargate)

### 1. Tạo Task Definition

*   **Family:** `pokemon-backend-task` (Loại khởi chạy Fargate).
*   **Phần cứng (Hardware):** 0.5 vCPU, 1 GB Memory.
*   **Container Port:** `8080` (TCP).
*   **Biến môi trường (Environment Variables):** Sử dụng Bulk Editor để truyền (inject) các cấu hình cơ sở dữ liệu một cách an toàn:

```env
PORT=8080
DATABASE_URL=jdbc:postgresql://<YOUR_RDS_ENDPOINT>:5432/pokemon_db
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=<YOUR_RDS_PASSWORD>
DDL_AUTO=update
REDIS_HOST=localhost
REDIS_PORT=6379
```

### 2. Triển khai ECS Service

1.  Tạo một Service mới trong ECS Cluster sử dụng Task Definition vừa tạo.
2.  **Mạng (Networking):** Gán một Security Group mới tên là `ecs-backend-sg`. Chỉ cho phép (exclusively) Inbound Port `8080` từ `alb-sg`.
3.  **Bước Quan trọng:** Cập nhật Security Group của RDS (`pokemon-db-sg`) để cho phép Inbound Port `5432` từ `ecs-backend-sg`.
4.  **Cân bằng tải (Load Balancing):** Đính kèm ALB (`pokemon-alb`) và trỏ listener tới target group `pokemon-tg`.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/6.png)

---

## Giai đoạn 6: Triển khai Frontend (AWS Amplify)

Ứng dụng React nằm trong cấu trúc monorepo ở thư mục `frontend/`.

1.  Điều hướng đến **AWS Amplify > Host web app > Connect GitHub repository**.
2.  **Cấu hình Monorepo:** Tích chọn "Connecting a monorepo?" và nhập `frontend` làm thư mục gốc (root directory).
3.  **Biến môi trường (Environment Variables):** Khai báo ALB DNS vào để frontend có thể giao tiếp với API của backend.
    *   `VITE_API_BASE_URL` = `http://<ALB_DNS_NAME>`


![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/7.png)

### Cài đặt Build (`amplify.yml`)

Đảm bảo Amplify nhắm mục tiêu vào thư mục đầu ra `dist` của Vite.

```yaml
version: 1
applications:
  - frontend:
      phases:
        preBuild:
          commands:
            - npm ci
        build:
          commands:
            - npm run build
      artifacts:
        baseDirectory: dist
        files:
          - '**/*'
      cache:
        paths:
          - node_modules/**/*
    appRoot: frontend
```


## Giai đoạn 7: Khởi tạo Ứng dụng (Đổ dữ liệu - Data Seeding)

Để tự động đưa dữ liệu vào cơ sở dữ liệu ngay trong lần khởi động container đầu tiên, một component `CommandLineRunner` được sử dụng. Đoạn mã bên dưới xử lý các giới hạn API, phân tích cú pháp JSON an toàn bằng `ObjectMapper` và mã hóa URI đúng cách để tránh lỗi *500 Internal Server Error* khi gọi dữ liệu từ external API Pokémon TCG.

**Boilerplate DataSeeder.java** *(Đặt trong package `com.pokemon_backend.project.component`)*:

```java
@Component
public class DataSeeder implements CommandLineRunner {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private CardRepository cardRepository;

    @Override
    public void run(String... args) throws Exception {
        
        // 1. Khởi tạo Test User (Giải pháp tạm thời với mật khẩu không mã hóa)
        if (userRepository.count() == 0) {
            System.out.println("Seeding Test User...");
            User testUser = new User();
            testUser.setUsername("Testing-user");
            testUser.setPassword("password123"); 
            userRepository.save(testUser);
        }

        // 2. Fetch thẻ bài Pokémon
        if (cardRepository.count() == 0) {
            System.out.println("🌱 Seeding Pokémon Cards from API...");
            RestTemplate restTemplate = new RestTemplate();
            ObjectMapper objectMapper = new ObjectMapper(); 

            HttpHeaders headers = new HttpHeaders();
            headers.set("User-Agent", "PokemonUnboxingApp/1.0");

            HttpEntity<String> entity = new HttpEntity<>(headers);
            String[] targetSets = {"base1", "sv3pt5", "swsh7"};
            List<Card> cardsToSave = new ArrayList<>();

            for (String setId : targetSets) {
                System.out.println("Fetching set: " + setId + "...");
                String apiUrl = "[https://api.pokemontcg.io/v2/cards?q=set.id](https://api.pokemontcg.io/v2/cards?q=set.id):" + setId + "&pageSize=250";

                try {
                    // Dùng đối tượng URI để tránh việc RestTemplate tự động mã hóa URL bị lỗi
                    URI uri = new URI(apiUrl);

                    ResponseEntity<String> response = restTemplate.exchange(
                            uri,
                            HttpMethod.GET,
                            entity,
                            String.class
                    );

                    JsonNode root = objectMapper.readTree(response.getBody());
                    JsonNode dataArray = root.get("data");

                    if (dataArray != null && dataArray.isArray()) {
                        for (JsonNode node : dataArray) {
                            Card card = new Card();
                            card.setId(node.get("id").asText());
                            card.setName(node.get("name").asText());

                            if (node.has("images") && node.get("images").has("small")) {
                                card.setImageUrl(node.get("images").get("small").asText());
                            }

                            if (node.has("rarity") && !node.get("rarity").isNull()) {
                                card.setRarity(node.get("rarity").asText());
                            } else {
                                card.setRarity("Common");
                            }

                            BigDecimal price = new BigDecimal("0.50");
                            if (node.has("tcgplayer") && node.get("tcgplayer").has("prices")) {
                                JsonNode prices = node.get("tcgplayer").get("prices");

                                if (prices.has("normal") && prices.get("normal").has("market") && !prices.get("normal").get("market").isNull()) {
                                    price = BigDecimal.valueOf(prices.get("normal").get("market").asDouble());
                                } else if (prices.has("holofoil") && prices.get("holofoil").has("market") && !prices.get("holofoil").get("market").isNull()) {
                                    price = BigDecimal.valueOf(prices.get("holofoil").get("market").asDouble());
                                }
                            }
                            card.setPrice(price);
                            cardsToSave.add(card);
                        }
                    }
                } catch (Exception e) {
                    System.err.println("Failed to fetch set " + setId + ": " + e.getMessage());
                }
            }

            if (!cardsToSave.isEmpty()) {
                cardRepository.saveAll(cardsToSave);
                System.out.println("Successfully saved " + cardsToSave.size() + " cards to the database!");
            }
        }
    }
}
```


## Giai đoạn 8: Kết quả 

![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/2.png)

Frontend đang được host và chạy ổn định.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/3.png)

Khi đăng nhập, người dùng có thể mở một gói (pack) gồm 10 thẻ, trong đó đảm bảo chắc chắn sẽ mở được một thẻ hiếm (rare card) ở lá bài thứ 10.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/4.png)

Sau khi mở gói xong, người dùng có thể xem những gì họ đã mở. Mỗi người dùng có thể mở 1 gói mỗi 1 giờ, điều này giúp duy trì sự hào hứng và trải nghiệm.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.7-Week7/5.png)

Cuối cùng, có một bộ sưu tập (binder) nơi tất cả các thẻ mà người dùng đã thu thập sẽ xuất hiện, cùng với số lượng và giá thị trường của chúng. Một dashboard đơn giản cũng được tích hợp để thống kê.