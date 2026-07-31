---
title: "Week 7 Worklog"
date: 2026-07-20
weight: 7
chapter: false
pre: " <b> 1.6. </b> "
---

### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 

[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### First full-stack app on the AWS Cloud

This report details the step-by-step deployment of the **Pokemon_unboxing** full-stack web application onto Amazon Web Services (AWS). The architecture strictly isolates the frontend, backend, and database layers to ensure security, scalability, and maintainability.

### Infrastructure Architecture:

*   **Database Tier:** Amazon RDS (PostgreSQL) in a private subnet.
*   **Compute Tier:** Java Spring Boot REST API containerized via Docker and orchestrated on Amazon ECS (AWS Fargate) behind an Application Load Balancer (ALB).
*   **Presentation Tier:** React SPA (Vite) hosted on AWS Amplify with a global CDN.

---

## Phase 1: Database Provisioning (Amazon RDS)

To securely persist user and card data, a managed PostgreSQL instance is required.

1.  Navigate to **Amazon RDS > Create database**.
2.  **Engine:** PostgreSQL.
3.  **Templates:** Free tier (or Production for live environments).
4.  **Settings:**
    *   **DB instance identifier:** `pokemon-db`
    *   **Master username:** `postgres`
    *   **Master password:** *(Secure Password)*
5.  **Connectivity:**
    *   **Public access:** No *(Crucial for security)*.
    *   **VPC security group:** Create a new group named `pokemon-db-sg`.
6.  **Action:** Note the database Endpoint URL upon completion.

---

## Phase 2: Backend Containerization

The Spring Boot backend requires a multi-stage Dockerfile to compile the source code using the Maven wrapper and package it into a lightweight JRE image.

**Boilerplate Dockerfile** *(Place this inside the `backend/project/` directory)*:

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

## Phase 3: Amazon Elastic Container Registry (ECR)

1.  Navigate to **Amazon ECR > Create repository**. Name it `pokemon-backend`.
2.  Authenticate the local terminal with AWS ECR.

> **Configuration Note:** If using Git Bash (Linux environment on Windows), use the standard AWS CLI command to prevent token malformation (400 Bad Request):

```bash
aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin <YOUR_ACCOUNT_ID>.dkr.ecr.ap-southeast-2.amazonaws.com
```

3.  Build and push the image:

```bash
cd backend/project
docker build -t pokemon-backend .
docker tag pokemon-backend:latest <YOUR_ACCOUNT_ID>.dkr.ecr.ap-southeast-2.amazonaws.com/pokemon-backend:latest
docker push <YOUR_ACCOUNT_ID>.dkr.ecr.ap-southeast-2.amazonaws.com/pokemon-backend:latest
```

---

## Phase 4: Networking & Load Balancing (ALB)

1.  Navigate to **EC2 > Load Balancers > Create Load Balancer > Application Load Balancer**.
2.  **Name:** `pokemon-alb` (Internet-facing, IPv4).
3.  **Security Group:** Create `alb-sg` (Allow Inbound HTTP Port `80` from `0.0.0.0/0`).
4.  **Listeners and Routing:** Create a Target Group:
    *   **Target type:** IP addresses *(Required for Fargate)*.
    *   **Target group name:** `pokemon-tg`.
    *   **Port:** `8080` (Spring Boot default).

---

## Phase 5: Compute Orchestration (Amazon ECS Fargate)

### 1. Task Definition Creation

*   **Family:** `pokemon-backend-task` (Fargate launch type).
*   **Hardware:** 0.5 vCPU, 1 GB Memory.
*   **Container Port:** `8080` (TCP).
*   **Environment Variables:** Use the Bulk Editor to inject database configurations securely:

```env
PORT=8080
DATABASE_URL=jdbc:postgresql://<YOUR_RDS_ENDPOINT>:5432/pokemon_db
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=<YOUR_RDS_PASSWORD>
DDL_AUTO=update
REDIS_HOST=localhost
REDIS_PORT=6379
```

### 2. ECS Service Deployment

1.  Create a new Service in the ECS Cluster using the defined Task Definition.
2.  **Networking:** Assign a new Security Group `ecs-backend-sg`. Allow Inbound Port `8080` exclusively from the `alb-sg`.
3.  **Critical Step:** Update the RDS Security Group (`pokemon-db-sg`) to allow Inbound Port `5432` from `ecs-backend-sg`.
4.  **Load Balancing:** Attach the ALB (`pokemon-alb`) and point the listener to the `pokemon-tg` target group.

---

## Phase 6: Frontend Deployment (AWS Amplify)

The React application resides within the `frontend/` monorepo structure.

1.  Navigate to **AWS Amplify > Host web app > Connect GitHub repository**.
2.  **Monorepo Configuration:** Check "Connecting a monorepo?" and input `frontend` as the root directory.
3.  **Environment Variables:** Inject the ALB DNS so the frontend can communicate with the backend API.
    *   `VITE_API_BASE_URL` = `http://<ALB_DNS_NAME>`

### Build Settings (`amplify.yml`)

Ensure Amplify targets the Vite `dist` output folder.

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


## Phase 7: Application Initialization (Data Seeding)

To populate the database upon the first container startup, a `CommandLineRunner` component is used. The script below handles API limits, safely parses JSON using `ObjectMapper`, and properly encodes the URI to prevent *500 Internal Server Error* when requesting data from the external Pokémon TCG API.

**Boilerplate DataSeeder.java** *(Place in `com.pokemon_backend.project.component`)*:

```java
@Component
public class DataSeeder implements CommandLineRunner {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private CardRepository cardRepository;

    @Override
    public void run(String... args) throws Exception {
        
        // 1. Initialize Test User (Unencrypted password fallback)
        if (userRepository.count() == 0) {
            System.out.println("Seeding Test User...");
            User testUser = new User();
            testUser.setUsername("Testing-user");
            testUser.setPassword("password123"); 
            userRepository.save(testUser);
        }

        // 2. Fetch Pokémon Cards
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
                String apiUrl = "https://api.pokemontcg.io/v2/cards?q=set.id:" + setId + "&pageSize=250";

                try {
                    // Use URI object to prevent automatic RestTemplate URL encoding corruption
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