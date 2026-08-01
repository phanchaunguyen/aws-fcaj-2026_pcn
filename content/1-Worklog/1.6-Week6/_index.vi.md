---
title: "Báo cáo công việc Tuần 6"
date: 2026-07-13
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 

[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Mục tiêu Tuần 6:

*   Nắm vững các khái niệm về Kiến trúc Serverless (Không máy chủ) và điện toán hướng sự kiện (event-driven computing).
*   Phân tích bài toán Cold Start (Khởi động lạnh) trong AWS Lambda và so sánh các môi trường thực thi (Java vs. Python).
*   Hiểu về Kiến trúc Hướng Sự kiện (EDA) để phân tách hệ thống (decoupling) sử dụng Amazon SQS và Amazon SNS.
*   Khám phá các dịch vụ Điều phối Container: Amazon ECS và Amazon EKS.
*   Xác định các thực hành tốt nhất (best practices) để chạy framework Spring Boot trong môi trường AWS.

---

### Nền tảng Serverless & AWS Lambda

Kiến trúc Serverless chuyển dời các trách nhiệm vận hành như cấp phát và quản lý máy chủ sang AWS, cho phép tập trung hoàn toàn vào mã nguồn ứng dụng.

**1. AWS Lambda & Các tác vụ chạy ngầm**
*   AWS Lambda thực thi code để phản hồi lại các trigger (ví dụ: các HTTP request qua API Gateway, tải file lên S3, hoặc các quy tắc dựa trên lịch trình).
*   Nó mang lại hiệu quả cao cho các tác vụ nền bất đồng bộ, tự động mở rộng để xử lý hàng ngàn lần thực thi đồng thời.

**2. Thử thách Cold Start: Java vs. Python**
*   "Cold Start" (Khởi động lạnh) xảy ra khi Lambda phải cấp phát một môi trường thực thi mới để xử lý một request gửi đến, làm tăng độ trễ.
*   **Python:** Là một ngôn ngữ thông dịch nhẹ, các hàm Python khởi động rất nhanh, giảm thiểu độ trễ cold start.
*   **Java:** Các ứng dụng Java gặp tình trạng cold start lâu hơn đáng kể do sự nặng nề trong việc khởi tạo Java Virtual Machine (JVM) và thời gian tải framework.
*   **Chiến lược giảm thiểu:** Sử dụng **AWS Lambda SnapStart** (lưu cache một bản snapshot của bộ nhớ đã khởi tạo và trạng thái JVM) hoặc cấu hình **Provisioned Concurrency** (Đồng thời được cấp phát) giúp giữ cho các instance luôn "ấm" (warm) và sẵn sàng phản hồi ngay lập tức.


![So sánh các loại EC2 Instance](/images/1-Worklog/1.6-Week6/2.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.6-Week6/3.png)

---

### Kiến trúc Hướng Sự kiện (EDA)

Các kiến trúc Monolith (nguyên khối) thường gặp vấn đề liên kết chặt chẽ (tight coupling), nơi sự cố của một thành phần sẽ kéo theo sự sụp đổ của toàn bộ hệ thống. EDA (Event-Driven Architecture) giải quyết vấn đề này bằng cách phân tách (decoupling) các dịch vụ.

**1. Amazon SNS (Simple Notification Service)**
*   Một dịch vụ nhắn tin Pub/Sub (Publish/Subscribe) được quản lý.
*   Một tin nhắn được publish có thể ngay lập tức được "fan out" (phân tán) đến nhiều dịch vụ subscribe (ví dụ: kích hoạt nhiều hàm Lambda hoặc gửi cảnh báo email đồng thời).

**2. Amazon SQS (Simple Queue Service)**
*   Một dịch vụ hàng đợi tin nhắn (message queuing) được quản lý dùng để phân tách giữa producer và consumer.
*   Tin nhắn được giữ an toàn trong hàng đợi cho đến khi dịch vụ consumer sẵn sàng xử lý chúng. Điều này ngăn chặn các hệ thống backend bị quá tải trong các đợt lưu lượng tăng đột biến (đóng vai trò như bộ đệm - buffering).
*   Kết hợp SNS và SQS là một pattern (mẫu) đám mây tiêu chuẩn: SNS phân tán (fan out) tin nhắn, và SQS đưa nó vào hàng đợi để xử lý bất đồng bộ, đáng tin cậy.


---

### Điều phối Container: ECS và EKS

Mặc dù Lambda hoàn hảo cho các tác vụ ngắn hạn, nhưng các ứng dụng chạy liên tục (như các API backend mạnh mẽ) thường phù hợp hơn với các container.

**1. Amazon ECS (Elastic Container Service)**
*   Một dịch vụ điều phối container native của AWS, có khả năng mở rộng cao.
*   Nó được tích hợp sâu với hệ sinh thái AWS (ALB, IAM, CloudWatch) và có đường cong học tập (learning curve) đơn giản hơn cho việc triển khai Docker container.
*   Khi kết hợp với **AWS Fargate**, nó hoạt động như một engine container serverless, loại bỏ nhu cầu quản lý hạ tầng EC2 bên dưới.

**2. Amazon EKS (Elastic Kubernetes Service)**
*   Một dịch vụ Kubernetes được quản lý để chạy các workload Kubernetes mã nguồn mở.
*   Nó cung cấp sự linh hoạt to lớn và tránh tình trạng bị khóa chặt vào một nhà cung cấp đám mây (cloud vendor lock-in), khiến nó trở thành tiêu chuẩn công nghiệp cho các kiến trúc microservices phức tạp ở cấp độ doanh nghiệp.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.6-Week6/5.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.6-Week6/4.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.6-Week6/6.png)

---

### Nghiên cứu về việc chạy Spring Boot trên AWS

Spring Boot là một framework mạnh mẽ, nhưng để triển khai nó hiệu quả trên AWS đòi hỏi phải chọn đúng mô hình compute dựa trên nhu cầu của ứng dụng.

*   **Amazon EC2 / Elastic Beanstalk:** Phương pháp truyền thống. File `.jar` của Spring Boot được triển khai lên các máy chủ ảo đã được cấp phát. Dễ hiểu nhưng yêu cầu phải quản lý việc mở rộng và cập nhật máy chủ.
*   **Amazon ECS với Fargate:** Phương pháp hiện đại được khuyến nghị cao. Ứng dụng Spring Boot được đóng gói vào một Docker container. ECS quản lý việc triển khai, và Fargate xử lý các tài nguyên tính toán tự động, cung cấp khả năng mở rộng ngang (horizontal scaling) tuyệt vời.
*   **AWS Lambda (Serverless Spring):** Việc chạy toàn bộ ứng dụng Spring Boot trên Lambda theo truyền thống gặp phải vấn đề cold start nghiêm trọng. Tuy nhiên, sử dụng **Spring Cloud Function** kết hợp với **GraalVM Native Images** sẽ biên dịch ứng dụng thành một executable (tệp thực thi) độc lập, gọn nhẹ, đạt được thời gian khởi động nhanh như chớp phù hợp với môi trường serverless.

> *[GHI CHÚ CHÈN ẢNH: Chèn một sơ đồ kiến trúc hiển thị một Docker container Spring Boot được triển khai trên Amazon ECS (Fargate) đằng sau một Application Load Balancer.]*



### Buổi họp nhóm — 19/07/2026

**Người tham dự:** Hiếu, Thanh, Nguyên, Danh, Hùng
**Vắng mặt:** Không

**Trình bày**

- **Hiếu** demo bộ khung (scaffold) backend: cấu trúc repo, 7 schema migration được áp dụng vào Postgres ở local, và hướng dẫn từng bước quy trình triển khai (chiến lược CI/CD 2 repo, 2 tài khoản).
- **Nguyên** đề xuất bổ sung 6 endpoint mở rộng cho thiết kế API thống nhất (trong tài liệu làm việc `ADJ_APIs.md`): **Hoạt động của Admin** (hàng đợi duyệt sân, duyệt/từ chối, quản lý vai trò người dùng), **Phân tích cho Quản lý** (doanh thu), và **Hồ sơ Người dùng** (xem/cập nhật — được hoãn lại vì độ ưu tiên thấp).

**Tóm tắt đánh giá về các API bổ sung của Nguyên**

1. **Các endpoint duyệt của admin lấp đầy một khoảng trống thực tế**: Phần §6.5 đã giới thiệu vòng đời của sân từ `PENDING` → `ACTIVE`/`REJECTED` nhưng không định nghĩa API admin nào để điều khiển nó. `GET /admin/courts` (hàng đợi duyệt) + một endpoint duyệt (review) sẽ hoàn thiện luồng này, kèm theo một thông báo SNS cho quản lý sân khi có quyết định.
2. **Endpoint doanh thu hỗ trợ cho dashboard của quản lý** như đã hứa ở §6.5. Định nghĩa được thống nhất: tổng hợp từ bảng **`payments` với `status = 'SUCCESS'`** (các khoản hoàn tiền tự động bị loại ra) được join với các sân của người gọi — không phải từ tổng số lượng booking — và được giới hạn bởi `courts.owner_id` theo quy tắc IDOR, với tùy chọn `group_by=day|court` để vẽ biểu đồ.
3. **Việc thay đổi Role (vai trò) phải đi qua Cognito trước**: `users.role` chỉ là một cache — endpoint này phải gọi `AdminAddUserToGroup`/`AdminRemoveUserFromGroup` của Cognito, sau đó mới cập nhật hàng trong DB, nếu không thì thông tin claims của JWT và DB sẽ bị lệch nhau.
4. **Chuẩn hóa đặt tên**: `GET/PUT /users/me` (khớp với pattern `/bookings/me` hiện tại) thay vì kiểu đặt tên động từ `/users/update-profile`.
5. **Cần bổ sung Schema**: thêm `courts.rejection_reason` (có thể null) để lý do một sân bị từ chối được lưu trữ lại sau khi thông báo đã được gửi.

**Quyết định & Phân công khối lượng công việc**

Tất cả 6 endpoint đã được thông qua với các điều chỉnh như trên (được tích hợp vào Đề xuất tại phần [§6.6](/2-proposal/2.1-architecture/)). Hiếu ưu tiên việc thiết lập CI/CD, vì vậy việc lập trình tính năng sẽ được phân chia theo quyền sở hữu domain:

| # | Hành động | Phụ trách | Ghi chú |
| - | ------ | ----- | ----- |
| 1 | **Thiết lập CI/CD**: `ci.yml` ở cả hai repo → branch protection (bảo vệ nhánh) → các môi trường (environments); sau đó triển khai bộ khung walking-skeleton lên tài khoản dev (hướng dẫn Phần 0–1) | Hiếu | Ưu tiên hàng đầu — gỡ bỏ rào cản (unblock) ở bước quality gate PR cho mọi người. |
| 2 | Model + migration cho `courts.rejection_reason` (tác vụ đầu tiên — làm theo hướng dẫn Alembic), sau đó là **Router cho Hoạt động Admin** (hàng đợi, duyệt + SNS) và **Endpoint xử lý role ưu tiên Cognito trước** | Nguyên | Đề xuất §6.6 của Nguyên; Cognito hoàn toàn phù hợp với domain auth của bạn ấy. `ADJ_APIs.md` được thay thế bởi §6.6. |
| 3 | Các **Router cho Booking** trên schema đã được dựng khung; sau đó là **Endpoint doanh thu** (tính tổng `SUM` dựa trên payment theo định nghĩa tại §6.6) | Thanh | Doanh thu là một tập hợp read-only — bước đi tiếp theo rất tự nhiên sau khi đã quen với các câu query booking. |
| 4 | **Chứng minh khả năng kết nối** giữa FE–BE (health check thông qua CORS); xây dựng **Màn hình duyệt của admin** và **Biểu đồ doanh thu** dựa trên dữ liệu mock; generate (tạo) lại các type khi contract cập nhật (luồng §4.3) | Danh & Hùng | Ưu tiên làm dữ liệu Mock (Mock-first) đồng nghĩa với việc không cần phải đợi các endpoint backend hoàn thiện. |
| 5 | Các endpoint hồ sơ người dùng được hoãn lại (độ ưu tiên thấp, sẽ quay lại xem xét sau khi xong các tính năng cốt lõi) | — | |
| 6 | Phân quyền admin cho Cognito (`AdminAddUserToGroup`, v.v.) đã được ghi chú cho IAM role của instance EC2 ở giai đoạn triển khai (deploy) | Hiếu | Bề mặt IAM mới — được theo dõi trong danh sách kiểm tra (checklist) bàn giao ở hướng dẫn CI/CD. |

---
### Glossary

| Abbreviation | Meaning |
| --- | --- |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| BE | Backend — the server-side part of the application |
| CI/CD | Continuous Integration / Continuous Delivery — automated building, testing, and deployment |
| CORS | Cross-Origin Resource Sharing — browser mechanism allowing a page to call APIs on another origin |
| DB | Database |
| ERD | Entity-Relationship Diagram — visual model of database tables and their relationships |
| FE | Frontend — the client-side part of the application |
| FK | Foreign Key — a column referencing another table's primary key |
| IaC | Infrastructure as Code — declaring infrastructure in versioned files |
| IDOR | Insecure Direct Object Reference — accessing another user's resources by manipulating object IDs |
| JWT | JSON Web Token — signed token carrying identity claims |
| SNS | Amazon Simple Notification Service — pub/sub messaging and notifications |