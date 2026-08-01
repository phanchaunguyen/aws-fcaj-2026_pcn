---
title: "Báo cáo công việc Tuần 5"
date: 2026-07-06
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 

[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Mục tiêu Tuần 5:

*   Tìm hiểu sâu về kiến trúc Amazon RDS (PostgreSQL), tập trung vào Tính sẵn sàng cao (High Availability) và Connection Pooling (Hồ chứa kết nối).
*   Nắm vững việc mô hình hóa dữ liệu NoSQL sử dụng thiết kế Single-table (bảng đơn) của DynamoDB.
*   Triển khai các cơ chế bộ nhớ đệm (caching) với Amazon ElastiCache (Redis) để giảm tải cho các truy vấn cơ sở dữ liệu.
*   Khám phá toàn diện Amazon Cognito cho các mục đích xác thực người dùng (authentication), phân quyền (authorization), và tích hợp mượt mà.

---

### Cơ sở dữ liệu trong Thực tế: Relational vs. NoSQL vs. Caching

Việc lựa chọn đúng cơ sở dữ liệu và tối ưu hóa kiến trúc của nó là điều cốt lõi cho một backend có khả năng mở rộng. Tuần này tập trung vào ba chiến lược lưu trữ và truy xuất dữ liệu khác biệt.

**1. Cơ sở dữ liệu Quan hệ (Relational Database): Amazon RDS (PostgreSQL)**
*   **Kiến trúc & Phục hồi thảm họa:** Amazon RDS tự động hóa các tác vụ quản trị tốn thời gian. Để đảm bảo khả năng Phục hồi thảm họa (Disaster Recovery - DR) và Tính sẵn sàng cao (HA), **các triển khai Multi-AZ** được sử dụng. Kiến trúc này sao chép đồng bộ dữ liệu sang một instance dự phòng (standby) ở một Availability Zone khác. Trong trường hợp xảy ra lỗi hạ tầng, RDS tự động chuyển đổi dự phòng (failover) sang instance standby mà không cần can thiệp thủ công.
*   **Connection Pooling với RDS Proxy:** Các ứng dụng hiện đại (đặc biệt là kiến trúc Serverless) có thể dễ dàng làm cạn kiệt số lượng kết nối tối đa của một cơ sở dữ liệu quan hệ. **Amazon RDS Proxy** đứng giữa ứng dụng (ví dụ: EC2, Lambda) và RDS instance. Nó thiết lập và quản lý các connection pool cần thiết, cho phép các ứng dụng mở rộng quy mô một cách khó đoán mà không làm quá tải cơ sở dữ liệu PostgreSQL bên dưới.


**2. Cơ sở dữ liệu NoSQL: Amazon DynamoDB**
*   **Khái niệm cốt lõi:** DynamoDB là một cơ sở dữ liệu NoSQL serverless, được quản lý hoàn toàn, thiết kế cho các ứng dụng yêu cầu độ trễ nhất quán ở mức một chữ số mili giây ở bất kỳ quy mô nào.
*   **Thiết kế Single-Table (Bảng đơn):** Khác với các cơ sở dữ liệu quan hệ nơi dữ liệu được chuẩn hóa (normalized) qua nhiều bảng, DynamoDB phát huy sức mạnh thông qua **Single-Table Design**. Tất cả các thực thể có liên quan (ví dụ: Người dùng, Đơn hàng, Sản phẩm) đều được lưu trữ trong một bảng duy nhất.
*   **Chiến lược triển khai:** Điều này đòi hỏi phải xác định trước toàn bộ các "mẫu truy cập" (access patterns) của ứng dụng. Các truy vấn phức tạp đạt được bằng cách thiết kế cẩn thận các Partition Keys (PK) và Sort Keys (SK), đồng thời sử dụng Global Secondary Indexes (GSIs) để lấy dữ liệu đã được join sẵn chỉ trong một truy vấn duy nhất.

**3. Bộ nhớ đệm (Caching): Amazon ElastiCache (Redis)**
*   **Mục đích:** Cơ sở dữ liệu thường là nút thắt cổ chai trong các ứng dụng có lượng đọc dữ liệu lớn (read-heavy). **Amazon ElastiCache** cung cấp một môi trường Redis được quản lý hoàn toàn để đóng vai trò như một kho dữ liệu in-memory (trong bộ nhớ).
*   **Giảm tải truy vấn (Query Offloading):** Các truy vấn PostgreSQL phức tạp, được yêu cầu thường xuyên (ví dụ: tạo bảng xếp hạng, danh mục sản phẩm) sẽ được lưu cache trong Redis. Backend trước tiên sẽ kiểm tra cache; nếu dữ liệu tồn tại (Cache Hit), nó sẽ được trả về ngay lập tức, bỏ qua hoàn toàn RDS instance và giảm tải đáng kể cho cơ sở dữ liệu.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/1.png)

---

### Amazon Cognito: Quản lý Danh tính và Quyền truy cập cho Ứng dụng

Amazon Cognito cung cấp khả năng quản lý danh tính và quyền truy cập khách hàng (CIAM) cực kỳ an toàn và không rào cản cho các ứng dụng web và di động.

**1. Các Thành phần cốt lõi và Mục đích**
*   **User Pools:** Đóng vai trò như các thư mục người dùng. Chúng xử lý việc xác thực (xác minh *ai* là người dùng) và cung cấp các chức năng như đăng ký, đăng nhập, MFA (Xác thực đa yếu tố) và khôi phục tài khoản.
*   **Identity Pools:** Xử lý việc phân quyền (xác định người dùng có thể truy cập *những gì*). Chúng trao đổi các token của User Pool (hoặc token bên ngoài) để lấy các thông tin xác thực AWS tạm thời, cho phép người dùng truy cập trực tiếp vào các tài nguyên AWS (như S3 hoặc DynamoDB).

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/2.png)

Mình đã khởi tạo một Cognito identity pool, cho phép xác thực (AUTH) bằng username và mật khẩu. Ngoài ra, email và số điện thoại cũng được định cấu hình làm alias (bí danh) bởi Cognito, cho phép người dùng sử dụng một trong hai để đăng nhập.

Việc xác nhận (Confirmation) là bắt buộc theo tiêu chuẩn của Cognito, nó có thể được thực hiện qua email hoặc SMS điện thoại, tùy thuộc vào phương thức nào khả dụng (Mặc dù dịch vụ SMS cần được thiết lập và có thể tốn phí).

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/3.png)

Đây là trang mặc định Hosted UI của Cognito, chúng ta có thể cập nhật trang này bằng template CSS tùy chỉnh của mình. Tại đây cũng đã có sẵn các dịch vụ đăng ký và lấy lại mật khẩu được tích hợp.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/4.png)

**2. Mở rộng với các Nhà cung cấp Danh tính Bên ngoài (IdP)**
*   Cognito hỗ trợ liên kết danh tính (identity federation). Người dùng có thể đăng nhập thông qua các nhà cung cấp danh tính mạng xã hội (Google, Facebook, Apple) hoặc các nhà cung cấp doanh nghiệp thông qua SAML 2.0 và OpenID Connect (OIDC).
*   Cognito hoạt động như một nhà môi giới trung tâm (central broker), thống nhất các nhà cung cấp khác nhau này và trả về một bộ JSON Web Tokens (JWT) tiêu chuẩn cho ứng dụng, bất kể phương thức đăng nhập ban đầu là gì.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/5.png)

Đây là trang Google Auth Platform, bắt buộc phải có các origins và redirect URIs để Google biết rằng trang web này được phép sử dụng Google Oauth2 như một nhà cung cấp bên ngoài.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/6.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.5-Week5/7.png)

Cognito cung cấp sẵn cho lập trình viên các tin nhắn xác minh (verification message) ngay lập tức, đồng thời cũng cho phép chỉnh sửa lại tin nhắn cho phù hợp. Tại đây, mình đã thử chỉnh sửa tin nhắn mặc định thành một nội dung trang trọng và chuyên nghiệp hơn.


**3. Hosted UI vs. Custom UI (Mặc định)**
*   **Hosted UI:** Một giao diện web có sẵn, có thể tùy chỉnh do AWS cung cấp cho việc đăng ký và đăng nhập của người dùng. Nó tự động xử lý toàn bộ luồng OAuth 2.0, khiến đây trở thành cách nhanh nhất để triển khai xác thực mà không cần viết logic frontend.
*   **Custom UI:** Tự xây dựng các màn hình đăng nhập từ đầu bằng cách sử dụng AWS SDKs hoặc Amplify. Cách này cung cấp sự linh hoạt hoàn toàn về mặt thiết kế nhưng đòi hỏi phải tự xử lý quản lý state, đặt lại mật khẩu và luồng MFA thủ công bên trong code frontend.

**4. Chiến lược Tích hợp: Amplify vs. EC2**
*   **Với AWS Amplify:** Amplify trừu tượng hóa sự phức tạp đi. Bằng cách sử dụng component `Authenticator` trong React, việc tích hợp frontend đạt được chỉ trong vài phút. Amplify tự động quản lý việc lưu trữ token, tự động refresh, và gắn header Authorization vào các request API gửi đi.
*   **Với EC2 (Xác minh Backend):** Khi frontend gửi một request đến một backend được host trên EC2 (ví dụ: một Spring Boot hoặc Python API), backend phải xác minh người dùng. 
    *   Frontend truyền Cognito JWT (Access hoặc ID token) trong header `Authorization`.
    *   Backend EC2 sử dụng các thư viện (như `aws-jwt-verify`) để xác thực chữ ký của token so với các khóa công khai của Cognito (JWKS), xác minh thời gian hết hạn và trích xuất các thông tin (claims) của người dùng trước khi xử lý logic nghiệp vụ.

> *[GHI CHÚ CHÈN ẢNH: Chèn một sơ đồ luồng xác thực: Người dùng đăng nhập qua Cognito Hosted UI -> Nhận JWT -> Gửi JWT đến API Gateway/EC2 -> EC2 xác thực JWT và trả về dữ liệu được bảo vệ.]*

---

### Các công việc hoàn thành trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Cấu hình Multi-AZ RDS & Proxy | 2026-07-06 | 2026-07-06 | AWS RDS Best Practices |
| 2 | Mô hình hóa Thiết kế DynamoDB Single-Table | 2026-07-07 | 2026-07-07 | NoSQL Design Patterns |
| 3 | Tích hợp ElastiCache (Redis) | 2026-07-08 | 2026-07-08 | Caching Strategies |
| 4 | Cài đặt Cognito User & Identity Pools | 2026-07-09 | 2026-07-09 | Cognito Auth Flows |
| 5 | Xác minh JWT Tokens trên Backend (EC2) | 2026-07-10 | 2026-07-10 | JWT Validation Docs |

### Thành quả Tuần 5:

*   Thiết kế kiến trúc cho cơ sở dữ liệu PostgreSQL có khả năng phục hồi cao sử dụng Multi-AZ cho việc chuyển đổi dự phòng và RDS Proxy để tạo connection pooling hiệu quả.
*   Chuyển đổi từ chuẩn hóa quan hệ (relational normalization) sang các phương pháp thiết kế single-table của NoSQL để truy xuất dữ liệu hiệu suất cao.
*   Triển khai thành công mẫu Cache-Aside bằng Redis để giảm tải mạnh mẽ cho cơ sở dữ liệu chính.
*   Cấu hình Amazon Cognito với liên kết Google/Facebook sử dụng Hosted UI.
*   Thiết lập một luồng làm việc an toàn trên backend EC2 để đánh chặn, xác thực và xử lý các JSON Web Tokens (JWT) của Cognito gửi tới.


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