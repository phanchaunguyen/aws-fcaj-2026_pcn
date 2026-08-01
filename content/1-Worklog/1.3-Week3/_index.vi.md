---
title: "Báo cáo công việc Tuần 3"
date: 2026-06-22
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 

[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Mục tiêu Tuần 3:
*   Tìm hiểu sâu về các Dịch vụ Máy tính (Compute Services) của AWS.
*   Hiểu và so sánh các loại instance EC2 khác nhau cùng các mô hình tính phí (On-Demand, Spot, Reserved, Savings Plans).
*   So sánh việc tự quản lý EC2 với các triển khai dạng container (Docker với ECS/EKS).
*   Nắm vững các khái niệm về Tính sẵn sàng cao (High Availability), Tự động phục hồi (Auto Healing), và Auto Scaling Groups (ASG).
*   Khám phá các chiến lược triển khai (Blue/Green, Rolling Update) để đạt được zero-downtime (không có thời gian chết) khi cập nhật backend.

---

### Tìm hiểu các Dịch vụ Máy tính (Compute) của AWS

Tuần này tập trung mạnh vào các kiến trúc tính toán trên AWS, chuyển đổi từ mô hình mở rộng dọc (vertical scaling) truyền thống sang các mô hình mở rộng ngang (horizontal scaling) có độ đàn hồi cao.

**1. Sự tiến hóa của Compute: EC2 vs. Serverless vs. Containers**
*   **Amazon EC2 (Elastic Compute Cloud):** Cung cấp năng lực tính toán an toàn, có thể thay đổi kích thước. Nó cho phép cấp phát các máy chủ ảo nơi bạn có toàn quyền kiểm soát hệ điều hành và các dependencies (phụ thuộc). Tuy nhiên, nó yêu cầu phải chủ động quản lý (cập nhật bản vá, mở rộng quy mô).
*   **Quản lý Container (ECS/EKS):** Thay vì triển khai thủ công các ứng dụng Spring Boot hoặc Python trực tiếp lên một instance EC2, các ứng dụng được đóng gói vào các Docker container. 
    *   **Amazon ECS (Elastic Container Service)** và **EKS (Elastic Kubernetes Service)** sẽ điều phối (orchestrate) các container này.
    *   Khi kết hợp với **AWS Fargate** (một serverless container engine), nhu cầu phải cấp phát hoặc quản lý các instance EC2 bên dưới bị loại bỏ hoàn toàn. Điều này làm giảm đáng kể gánh nặng vận hành so với các môi trường tự quản lý EC2.
*   **Serverless (AWS Lambda):** Sự trừu tượng hóa tối thượng của compute. Mã nguồn được thực thi hoàn toàn dựa trên phản hồi với các sự kiện (ví dụ: một lệnh gọi API hoặc một file tải lên S3) mà không cần quan tâm đến khái niệm hạ tầng bên dưới. Nó tự động mở rộng từ 0 lên hàng nghìn lần thực thi đồng thời.


**2. Chọn đúng loại EC2 Instance**
AWS cung cấp các họ instance cụ thể được tối ưu hóa cho các khối lượng công việc (workloads) khác nhau:
*   **General Purpose - Đa dụng (ví dụ: họ T, M):** Cân bằng giữa CPU, bộ nhớ và mạng cho các API backend thông thường.
*   **Compute Optimized - Tối ưu tính toán (họ C):** Bộ xử lý hiệu suất cao cho xử lý hàng loạt (batch processing) hoặc chuyển mã media.
*   **Memory Optimized - Tối ưu bộ nhớ (họ R, X):** Lý tưởng cho cơ sở dữ liệu in-memory (như Redis) hoặc phân tích dữ liệu lớn theo thời gian thực.
*   **Storage Optimized - Tối ưu lưu trữ (họ I, D):** Truy cập đọc/ghi tuần tự tốc độ cao vào các tập dữ liệu lớn trên bộ nhớ cục bộ.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.3-Week3/4.png)

---

### Mô hình tính phí EC2: Tối ưu chi phí Cloud

Hiểu cách trả tiền cho các tài nguyên tính toán cũng quan trọng như hiểu các thông số kỹ thuật. AWS cung cấp một số tùy chọn mua:

*   **On-Demand (Theo yêu cầu):** Trả phí theo giờ/giây mà không cần cam kết trước. Tốt nhất cho các khối lượng công việc không thể đoán trước hoặc ngắn hạn. Đây là tùy chọn đắt nhất nhưng linh hoạt nhất.
*   **Reserved Instances - RI (Instance đặt trước):** Cam kết sử dụng 1 đến 3 năm cho một loại instance cụ thể để đổi lấy mức chiết khấu lớn (lên đến 72%). Tốt nhất cho các khối lượng công việc ổn định, đã biết trước.
*   **Savings Plans (Gói tiết kiệm):** Giải pháp thay thế hiện đại cho RIs. Nó cung cấp mức chiết khấu tương tự cho cam kết 1 hoặc 3 năm nhưng mang lại sự linh hoạt. *Compute Savings Plan* áp dụng chiết khấu trên toàn bộ EC2, Fargate, và Lambda, bất kể họ instance hay Region.
*   **Spot Instances:** Đấu giá dung lượng máy chủ AWS chưa sử dụng. Tùy chọn này cung cấp mức chiết khấu cao nhất (lên đến 90% so với On-Demand) nhưng đi kèm với một điều kiện: AWS có thể thu hồi instance với cảnh báo trước 2 phút. 
    *   *Best practice:* Chỉ sử dụng Spot instances cho các workload phi trạng thái (stateless), chịu lỗi tốt hoặc xử lý hàng loạt có thể xử lý việc bị gián đoạn.
*   **EC2 Fleet:** Một tính năng cho phép cấu hình Auto Scaling Group để cấp phát kết hợp cả On-Demand và Spot instances trên các loại instance khác nhau. Điều này đảm bảo ứng dụng luôn có tính sẵn sàng cao trong khi giảm thiểu đáng kể chi phí compute.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.3-Week3/1.png)

---

### Tính sẵn sàng cao (HA), Tự động phục hồi và Auto Scaling

Trong điện toán đám mây, một lỗi instance đơn lẻ không bao giờ được phép gây ra thời gian chết (downtime) cho ứng dụng.

*   **Mở rộng ngang (Horizontal Scaling) vs. Mở rộng dọc (Vertical Scaling):** 
    *   *Mở rộng dọc* liên quan đến việc thêm nhiều CPU/RAM vào một instance duy nhất. Nó có một giới hạn cứng, trở nên đắt đỏ một cách bất hợp lý và tạo ra một điểm lỗi duy nhất (single point of failure).
    *   *Mở rộng ngang* liên quan đến việc thêm nhiều instance hơn (nhân bản ứng dụng) và phân phối lưu lượng truy cập qua một Elastic Load Balancer (ELB). Điều này đảm bảo **Tính sẵn sàng cao (High Availability)** và khả năng chịu lỗi.
*   **Auto Scaling Groups (ASG):** Cơ chế xử lý việc mở rộng ngang một cách tự động.
    *   *Scale Out (Mở rộng):* Thêm instance khi các chỉ số (như mức sử dụng CPU > 70%) vượt qua một ngưỡng nhất định.
    *   *Scale In (Thu hẹp):* Xóa bớt instance trong thời gian lưu lượng thấp để tiết kiệm chi phí.
*   **Auto Healing (Tự động phục hồi):** ASG liên tục theo dõi các health checks của instance (thường kết hợp với health checks của ELB). Nếu một instance bị sập hoặc trở nên không khỏe (unhealthy), ASG tự động chấm dứt instance lỗi đó và khởi động một instance khỏe mạnh thay thế mà không cần sự can thiệp của con người.



### Bài tập về nhà: Tạo một ASG Group

![Template EC2](/images/1-Worklog/1.3-Week3/5.png)

Trước khi tạo một nhóm ASG, bắt buộc phải có một launch template (mẫu khởi chạy). Các instance EC2 của ASG sẽ dựa trên cấu hình của template: loại instance, kích thước, số lượng vCPU, giB RAM, EBS, v.v...

![Khởi chạy EC2 ASG](/images/1-Worklog/1.3-Week3/6.png)

Nếu một instance EC2 đột nhiên bị sập, ASG sẽ tự động khởi động một instance mới ngay lập tức, đảm bảo số lượng instance luôn đạt đúng cấu hình yêu cầu. Và khi lưu lượng truy cập tăng vọt, ASG cũng sẽ tự động triển khai thêm nhiều instance EC2 để đáp ứng nhu cầu, khi qua đỉnh tải, các instance sẽ bị xóa đi để cắt giảm chi phí.

---

### AMI: Môi trường sản phẩm tức thời

![So sánh các loại EC2 Instance](/images/1-Worklog/1.3-Week3/2.png)

Thay vì triển khai một EC2 hoàn toàn mới và đi cài đặt các requirement, môi trường, đẩy container, v.v... Chúng ta có thể tạo một AMI, về cơ bản là một "bản chụp" (image) của một instance EC2 đã sẵn sàng chạy production.

Điều này giúp giảm thiểu đáng kể thời gian từ lúc EC2 khởi động cho đến lúc sẵn sàng xử lý các request, vì mọi thứ đã được cài đặt và cấu hình sẵn ngay từ đầu.


### Chiến lược triển khai Zero-Downtime

Khi triển khai một phiên bản mới của ứng dụng backend (ví dụ: cập nhật API Spring Boot từ v1 lên v2), bắt buộc phải tránh tình trạng downtime. Hai chiến lược chính được sử dụng:

**1. Rolling Updates (Cập nhật cuốn chiếu)**
*   ASG thay thế dần dần các instance cũ bằng các instance mới.
*   Ví dụ: nếu có 4 instance đang chạy, nó sẽ cập nhật 1 instance tại một thời điểm. ELB ngừng gửi lưu lượng truy cập đến instance đang được cập nhật. Khi instance mới vượt qua các health check, ELB sẽ định tuyến lưu lượng truy cập đến nó, và quá trình này lặp lại cho instance tiếp theo.
*   *Ưu điểm:* Tiết kiệm chi phí (không yêu cầu nhân đôi hạ tầng).
*   *Nhược điểm:* Quá trình triển khai mất nhiều thời gian hơn, và việc rollback (quay lại phiên bản cũ) trong trường hợp có lỗi sẽ diễn ra chậm.

**2. Blue/Green Deployments (Triển khai Xanh/Lục)**
*   Hai môi trường giống hệt nhau được duy trì: "Blue" (phiên bản live hiện tại) và "Green" (phiên bản mới).
*   Phiên bản mới được triển khai hoàn toàn lên môi trường Green. Khi nó đã được kiểm thử đầy đủ và hoạt động khỏe mạnh, lưu lượng truy cập sẽ ngay lập tức được chuyển đổi ở cấp độ Load Balancer/DNS từ Blue sang Green.
*   *Ưu điểm:* Khả năng rollback tức thì (chỉ cần chuyển lưu lượng trở lại Blue) và thực sự đạt được zero-downtime.
*   *Nhược điểm:* Tạm thời nhân đôi chi phí cơ sở hạ tầng trong khoảng thời gian diễn ra quá trình chuyển đổi.

![Kiến trúc triển khai Blue - Green](/images/1-Worklog/1.3-Week3/7.png)
---

### Các công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Nghiên cứu các loại EC2 Instance & Tính phí | 2026-06-22 | 2026-06-22 | Tài liệu AWS EC2 |
| 2 | So sánh EC2 vs. Docker (ECS/EKS) | 2026-06-23 | 2026-06-23 | Video Điều phối Container |
| 3 | Triển khai High Availability & ELB | 2026-06-24 | 2026-06-24 | Hướng dẫn Cân bằng tải AWS |
| 4 | Cấu hình Auto Scaling Groups | 2026-06-25 | 2026-06-25 | ASG Best Practices |
| 5 | Nghiên cứu các Chiến lược Triển khai | 2026-06-26 | 2026-06-26 | Blue/Green vs Rolling Update |

### Thành quả Tuần 3:

*   Nắm vững cách đánh giá các loại instance EC2 và xác định mô hình định giá hiệu quả nhất về chi phí (Spot vs. Savings Plans) cho các workload backend.
*   Hiểu các lợi thế vận hành của việc sử dụng các Docker container được điều phối bởi ECS/Fargate so với việc quản lý các instance EC2 thuần.
*   Thiết kế một kiến trúc có tính sẵn sàng cao sử dụng Load Balancers và Auto Scaling Groups qua nhiều Availability Zones để đảm bảo Auto Healing.
*   Nắm vững lý thuyết trong việc thực thi các chiến lược triển khai zero-downtime bằng phương pháp Blue/Green và Rolling Update.

---

### Buổi họp nhóm — 27/06/2026

**Người tham dự:** Hiếu, Thanh, Nguyên, Hùng
**Vắng mặt:** Danh

**Trình bày**

- **Hiếu** demo logic thanh toán từ đầu đến cuối (end-to-end) và giải thích cho cả nhóm về thiết kế cơ sở dữ liệu được đề xuất cho ứng dụng.
- **Danh** (đã nộp bất đồng bộ trước buổi họp) chia sẻ một bản nháp UI ban đầu để review.

**Phân công công việc**

| Hạng mục / Nhóm               | Phụ trách   | Sản phẩm bàn giao (Deliverable)                                                                                                              |
| ----------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Frontend — Thiết kế UI        | Danh & Hùng | Thống nhất và cải thiện thiết kế UI dựa trên bản nháp ban đầu của Danh.                                                                      |
| Frontend — Tech Stack         | Danh & Hùng | Đề xuất tech stack (công nghệ sử dụng) cho việc lập trình frontend.                                                                          |
| Backend — Tech Stack          | Nhóm BE     | Đề xuất tech stack cho việc lập trình backend.                                                                                               |
| Backend — Kiến trúc           | Hiếu        | Tích hợp Amazon Amplify làm hosting cho frontend vào kiến trúc; vẽ lại sơ đồ kiến trúc đã cập nhật.                                          |
| Backend — Quản lý đặt sân     | Thanh       | Tài liệu API (tên endpoint, input, output, use case) và thiết kế cơ sở dữ liệu (tables, columns, data types).                                |
| Backend — Xác thực người dùng | Nguyên      | Tài liệu API (tên endpoint, input, output, use case) và thiết kế cơ sở dữ liệu (tables, columns, data types).                                |

---

### Sản phẩm bàn giao của Hiếu — Tính năng Thanh toán

#### Tài liệu API

<table style="width:100%; table-layout:fixed; word-break:break-word;">
  <colgroup>
    <col style="width:3%">
    <col style="width:15%">
    <col style="width:5%">
    <col style="width:20%">
    <col style="width:15%">
    <col style="width:42%">
  </colgroup>
  <thead>
    <tr>
      <th>#</th>
      <th>Endpoint</th>
      <th>Method</th>
      <th>Input</th>
      <th>Output</th>
      <th>Use Case (Trường hợp sử dụng)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td><code>/api/v1/payments</code></td>
      <td><code>POST</code></td>
      <td><code>booking_id</code> (UUID), <code>amount</code> (decimal), <code>payment_method</code> (string)</td>
      <td><code>payment_id</code> (UUID), <code>checkout_url</code> (string), <code>status: "PENDING"</code></td>
      <td>Người chơi khởi tạo thanh toán sau khi xác nhận đặt sân. Tạo một bản ghi thanh toán ở trạng thái PENDING trong RDS và trả về URL thanh toán để điều hướng người chơi.</td>
    </tr>
    <tr>
      <td>2</td>
      <td><code>/api/v1/payments/webhook</code></td>
      <td><code>POST</code></td>
      <td><code>payment_id</code> (UUID), <code>transaction_id</code> (string), <code>status</code> (string), <code>gateway_data</code> (object)</td>
      <td><code>{ success: boolean }</code></td>
      <td>Cổng thanh toán thông báo cho hệ thống kết quả giao dịch. Đây là điểm vào của luồng serverless: API Gateway nhận callback → gọi Lambda để cập nhật trạng thái thanh toán trong RDS → kích hoạt thông báo SNS cho người chơi.</td>
    </tr>
    <tr>
      <td>3</td>
      <td><code>/api/v1/payments/{payment_id}</code></td>
      <td><code>GET</code></td>
      <td><code>payment_id</code> (path param), Bearer token (header)</td>
      <td><code>payment_id</code>, <code>booking_id</code>, <code>amount</code>, <code>currency</code>, <code>status</code>, <code>payment_method</code>, <code>transaction_id</code>, <code>created_at</code>, <code>updated_at</code></td>
      <td>Người chơi hoặc admin kiểm tra trạng thái của một giao dịch thanh toán cụ thể.</td>
    </tr>
    <tr>
      <td>4</td>
      <td><code>/api/v1/payments</code></td>
      <td><code>GET</code></td>
      <td>Bearer token (header), <code>page</code> (int), <code>limit</code> (int)</td>
      <td><code>{ data: [...payments], total, page, limit }</code></td>
      <td>Người chơi xem toàn bộ lịch sử thanh toán của họ có phân trang.</td>
    </tr>
  </tbody>
</table>

#### Thiết kế Cơ sở dữ liệu

**Bảng: `payments` (Thanh toán)**

| Cột (Column)       | Kiểu dữ liệu     | Ràng buộc (Constraints)         | Mô tả (Description)                                                                        |
| ------------------ | ---------------- | ------------------------------- | ------------------------------------------------------------------------------------------ |
| `id`               | `UUID`           | `PRIMARY KEY`                   | Mã định danh thanh toán duy nhất                                                           |
| `booking_id`       | `UUID`           | `NOT NULL`, `FK → bookings(id)` | Lịch đặt sân mà thanh toán này chi trả cho                                                 |
| `user_id`          | `UUID`           | `NOT NULL`, `FK → users(id)`    | Người chơi khởi tạo thanh toán                                                             |
| `amount`           | `DECIMAL(12, 2)` | `NOT NULL`                      | Số tiền thanh toán                                                                         |
| `currency`         | `VARCHAR(3)`     | `NOT NULL`, `DEFAULT 'VND'`     | Mã tiền tệ                                                                                 |
| `status`           | `VARCHAR(20)`    | `NOT NULL`, `DEFAULT 'PENDING'` | `PENDING` / `SUCCESS` / `FAILED` / `REFUNDED`                                              |
| `payment_method`   | `VARCHAR(50)`    | `NOT NULL`                      | `CREDIT_CARD` / `BANK_TRANSFER` / `E_WALLET`                                               |
| `transaction_id`   | `VARCHAR(255)`   | `UNIQUE`                        | Mã giao dịch được trả về bởi cổng thanh toán                                               |
| `gateway_response` | `JSONB`          |                                 | Payload phản hồi thô từ cổng thanh toán dùng để kiểm toán (audit)                          |
| `created_at`       | `TIMESTAMP`      | `NOT NULL`, `DEFAULT NOW()`     | Thời gian khởi tạo thanh toán                                                              |
| `updated_at`       | `TIMESTAMP`      | `NOT NULL`, `DEFAULT NOW()`     | Thời gian cập nhật trạng thái gần nhất                                                     |

**Ghi chú:**

- `transaction_id` có thể null cho đến khi cổng thanh toán phản hồi (vẫn là `NULL` khi trạng thái là `PENDING`).
- `gateway_response` lưu trữ toàn bộ payload thô từ cổng thanh toán — hữu ích cho việc giải quyết tranh chấp và gỡ lỗi (debug) mà không cần phải gọi lại cổng thanh toán.
- Cột `status` được cập nhật bởi hàm Lambda xác nhận (luồng webhook), chứ không phải bởi kiến trúc monolith trên EC2, giữ cho hai luồng này tách biệt rõ ràng.

---

### Thiết kế Toàn bộ Cơ sở dữ liệu

#### Các Bảng (Tables)

**`users` (Người dùng)**

| Cột             | Kiểu dữ liệu   | Ràng buộc          | Mô tả                                                                     |
| --------------- | -------------- | ------------------ | ------------------------------------------------------------------------- |
| `id`            | `UUID`         | `PRIMARY KEY`      | Mã định danh người dùng duy nhất                                          |
| `email`         | `VARCHAR(255)` | `UNIQUE, NOT NULL` | Email đăng nhập                                                           |
| `password_hash` | `VARCHAR(255)` | `NOT NULL`         | Mật khẩu đã được mã hóa bằng Bcrypt                                       |
| `full_name`     | `VARCHAR(255)` | `NOT NULL`         | Tên hiển thị                                                              |
| `phone`         | `VARCHAR(20)`  |                    | Số điện thoại liên hệ                                                     |
| `cognito_sub`   | `VARCHAR(255)` | `UNIQUE`           | ID subject của Cognito User Pool — liên kết JWT với bản ghi người dùng    |
| `role`          | `VARCHAR(20)`  | `DEFAULT 'player'` | `player` / `admin` / `court_owner`                                        |
| `avatar_url`    | `VARCHAR(500)` |                    | S3 URL của ảnh đại diện                                                   |
| `created_at`    | `TIMESTAMP`    | `DEFAULT NOW()`    | Thời gian tạo tài khoản                                                   |
| `updated_at`    | `TIMESTAMP`    | `DEFAULT NOW()`    | Cập nhật hồ sơ lần cuối                                                   |

**`courts` (Sân)**

| Cột              | Kiểu dữ liệu    | Ràng buộc          | Mô tả                                              |
| ---------------- | --------------- | ------------------ | -------------------------------------------------- |
| `id`             | `UUID`          | `PRIMARY KEY`      | Mã định danh sân duy nhất                          |
| `owner_id`       | `UUID`          | `FK → users(id)`   | Chủ sân (người dùng có role là `court_owner`)      |
| `name`           | `VARCHAR(255)`  | `NOT NULL`         | Tên sân hiển thị                                   |
| `description`    | `TEXT`          |                    | Chi tiết thông tin sân                             |
| `address`        | `VARCHAR(500)`  | `NOT NULL`         | Địa chỉ vật lý                                     |
| `sport_type`     | `VARCHAR(50)`   | `NOT NULL`         | `badminton` / `tennis` / `football` / `basketball` |
| `price_per_hour` | `DECIMAL(12,2)` | `NOT NULL`         | Giá theo giờ tính bằng VNĐ                         |
| `status`         | `VARCHAR(20)`   | `DEFAULT 'ACTIVE'` | `ACTIVE` / `INACTIVE` / `MAINTENANCE`              |
| `created_at`     | `TIMESTAMP`     | `DEFAULT NOW()`    |                                                    |
| `updated_at`     | `TIMESTAMP`     | `DEFAULT NOW()`    |                                                    |

**`court_images` (Hình ảnh sân)**

| Cột          | Kiểu dữ liệu   | Ràng buộc                   | Mô tả                           |
| ------------ | -------------- | --------------------------- | ------------------------------- |
| `id`         | `UUID`         | `PRIMARY KEY`               |                                 |
| `court_id`   | `UUID`         | `NOT NULL, FK → courts(id)` | Thuộc về sân nào                |
| `image_url`  | `VARCHAR(500)` | `NOT NULL`                  | S3 URL của bức ảnh              |
| `is_primary` | `BOOLEAN`      | `DEFAULT false`             | Đây có phải là ảnh cover không? |
| `created_at` | `TIMESTAMP`    | `DEFAULT NOW()`             |                                 |

**`bookings` (Lịch đặt sân)**

| Cột            | Kiểu dữ liệu    | Ràng buộc                   | Mô tả                                               |
| -------------- | --------------- | --------------------------- | --------------------------------------------------- |
| `id`           | `UUID`          | `PRIMARY KEY`               | Mã định danh lịch đặt duy nhất                      |
| `user_id`      | `UUID`          | `NOT NULL, FK → users(id)`  | Người chơi đã đặt lịch                              |
| `court_id`     | `UUID`          | `NOT NULL, FK → courts(id)` | Sân được đặt                                        |
| `start_time`   | `TIMESTAMP`     | `NOT NULL`                  | Thời gian bắt đầu                                   |
| `end_time`     | `TIMESTAMP`     | `NOT NULL`                  | Thời gian kết thúc                                  |
| `total_amount` | `DECIMAL(12,2)` | `NOT NULL`                  | Giá tại thời điểm đặt sân                           |
| `status`       | `VARCHAR(20)`   | `DEFAULT 'PENDING'`         | `PENDING` / `CONFIRMED` / `CANCELLED` / `COMPLETED` |
| `note`         | `TEXT`          |                             | Ghi chú tùy chọn của người chơi                     |
| `created_at`   | `TIMESTAMP`     | `DEFAULT NOW()`             |                                                     |
| `updated_at`   | `TIMESTAMP`     | `DEFAULT NOW()`             |                                                     |

> Ngăn chặn đặt trùng lịch (Double-booking): thêm một ràng buộc exclusion của PostgreSQL sử dụng `btree_gist` trên `(court_id, tsrange(start_time, end_time))` để từ chối các khoảng thời gian bị chồng chéo ngay ở cấp độ DB.

**`payments`** — Xem Sản phẩm bàn giao của Hiếu ở bên trên.

#### ERD (Sơ đồ Thực thể - Quan hệ)

{{<mermaid>}}
erDiagram
users {
UUID id PK
VARCHAR email UK
VARCHAR password_hash
VARCHAR full_name
VARCHAR phone
VARCHAR cognito_sub UK
VARCHAR role
VARCHAR avatar_url
TIMESTAMP created_at
TIMESTAMP updated_at
}
courts {
UUID id PK
UUID owner_id FK
VARCHAR name
TEXT description
VARCHAR address
VARCHAR sport_type
DECIMAL price_per_hour
VARCHAR status
TIMESTAMP created_at
TIMESTAMP updated_at
}
court_images {
UUID id PK
UUID court_id FK
VARCHAR image_url
BOOLEAN is_primary
TIMESTAMP created_at
}
bookings {
UUID id PK
UUID user_id FK
UUID court_id FK
TIMESTAMP start_time
TIMESTAMP end_time
DECIMAL total_amount
VARCHAR status
TEXT note
TIMESTAMP created_at
TIMESTAMP updated_at
}
payments {
UUID id PK
UUID booking_id FK
UUID user_id FK
DECIMAL amount
VARCHAR currency
VARCHAR status
VARCHAR payment_method
VARCHAR transaction_id UK
JSONB gateway_response
TIMESTAMP created_at
TIMESTAMP updated_at
}

    users ||--o{ courts : "owns"
    users ||--o{ bookings : "makes"
    users ||--o{ payments : "pays"
    courts ||--o{ court_images : "has"
    courts ||--o{ bookings : "reserved via"
    bookings ||--o| payments : "paid by"

{{</mermaid>}}

---

### Luồng Thanh toán (Payment Flow)

Tính năng thanh toán trải dài trên cả hai luồng triển khai. Bảng dưới đây ánh xạ từng bước cho các tác nhân (actor), hoạt động DB và dịch vụ AWS liên quan.

| Bước | Tác nhân                 | Hành động                                                                | Thao tác DB                                                                            | Dịch vụ AWS        |
| ---- | ------------------------ | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------- | ------------------ |
| 1    | Người chơi               | Tìm kiếm và chọn sân + khung giờ                                         | `SELECT` từ `courts`, kiểm tra không trùng lặp trong `bookings`                        | EC2 (FastAPI)      |
| 2    | Người chơi               | Xác nhận đặt lịch                                                        | `INSERT` vào `bookings` với `status = 'PENDING'`                                       | EC2 (FastAPI)      |
| 3    | Người chơi               | Khởi tạo thanh toán                                                      | `INSERT` vào `payments` với `status = 'PENDING'`, trả về `checkout_url`                | EC2 (FastAPI)      |
| 4    | Người chơi               | Bị điều hướng đến cổng thanh toán và hoàn tất giao dịch                  | —                                                                                      | External gateway   |
| 5    | Cổng thanh toán (Gateway)| Gửi webhook callback                                                     | —                                                                                      | Amazon API Gateway |
| 6    | Lambda (Xử lý thanh toán)| Xác thực webhook, cập nhật bản ghi thanh toán                            | `UPDATE payments SET status = 'SUCCESS', transaction_id = ..., gateway_response = ...` | AWS Lambda         |
| 7    | Lambda (Xác nhận lịch)   | Xác nhận lịch đặt sân                                                    | `UPDATE bookings SET status = 'CONFIRMED'`                                             | AWS Lambda         |
| 8    | SNS                      | Thông báo cho người chơi                                                 | —                                                                                      | Amazon SNS         |
| 9    | Người chơi               | Xem xác nhận đặt sân                                                     | `SELECT` từ `bookings` JOIN `payments`                                                 | EC2 (FastAPI)      |

**Khi thanh toán thất bại (Bước 6 trả về `FAILED`):**

- `payments.status` → `FAILED`
- `bookings.status` → `CANCELLED`
- SNS thông báo cho người chơi về việc thanh toán thất bại

**Vòng đời trạng thái (State lifecycle):**