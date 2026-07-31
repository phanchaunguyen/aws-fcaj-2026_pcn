---
title: "Báo cáo Tuần 1"
date: 2026-06-08
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu Tuần 1:

*   Kết nối và làm quen với các thành viên trong chương trình First Cloud AI Journey.
*   Tham gia buổi họp khởi động dự án (kick-off) và thống nhất về đề xuất kiến trúc AWS lai (hybrid).
*   Đọc và tìm hiểu tổng quan về các dịch vụ AWS được đề cập trong sơ đồ kiến trúc dự án.
*   Tạo tài khoản AWS.

---

### Tìm hiểu kiến thức tổng quan về Dịch vụ Đám mây AWS

Tuần đầu tiên được dành để nghiên cứu tổng quan về hệ sinh thái Amazon Web Services (AWS). 

*   Ra mắt vào năm 2006 chỉ với 3 dịch vụ cơ bản (Compute, Storage, và Queue), AWS nay đã trở thành nền tảng điện toán đám mây toàn diện.
*   Hệ sinh thái hiện cung cấp hơn 200 dịch vụ đa dạng.
*   Việc tiếp cận AWS giống như bước vào một siêu thị khổng lồ, nơi cung cấp mọi "nguyên liệu" để xây dựng bất kỳ loại ứng dụng nào.

Dưới đây là các nhóm dịch vụ cốt lõi được tập trung tìm hiểu:

**1. Compute (Điện toán)**
*   **Amazon EC2 (Elastic Compute Cloud):** Nền tảng cơ bản nhất để tạo các máy chủ ảo (instances) trên cloud. 
*   **Dịch vụ quản lý (Managed Services):** Các dịch vụ như **Elastic Beanstalk** (PaaS) hoặc **AWS LightSail** giúp trừu tượng hóa hạ tầng, phù hợp để triển khai nhanh các ứng dụng đơn giản.
*   **Kiến trúc Serverless:** **AWS Lambda** cho phép chạy code trực tiếp dựa trên các sự kiện (event-driven) mà không cần duy trì máy chủ chạy 24/7. Chi phí chỉ được tính cho thời gian tính toán thực tế (đo bằng mili-giây).
*   **Containers:** **ECS** và **EKS** (Kubernetes) được dùng để quản lý container, thường kết hợp cùng **AWS Fargate** để chạy container mà không cần quản lý EC2 instances bên dưới.

**2. Storage (Lưu trữ)**
*   **Amazon S3 (Simple Storage Service):** Dịch vụ lưu trữ object linh hoạt và tiết kiệm, phù hợp để lưu trữ file tĩnh (hình ảnh, video, HTML, CSS). 
*   **EBS (Elastic Block Store):** Lưu trữ khối với hiệu năng cao, được gắn trực tiếp vào một máy chủ ảo.
*   **EFS (Elastic File System):** Hệ thống tệp cho phép chia sẻ file giữa nhiều máy chủ cùng lúc.

**3. Database (Cơ sở dữ liệu)**
*   **Cơ sở dữ liệu quan hệ (Relational DB):** **Amazon RDS** tự động hóa các tác vụ quản trị cho SQL DB. **Amazon Aurora** là DB quan hệ độc quyền của AWS với hiệu năng cao, tích hợp sẵn phiên bản serverless.
*   **Cơ sở dữ liệu NoSQL:** **DynamoDB** là hệ quản trị key-value với độ trễ cực thấp. **DocumentDB** được thiết kế tương thích hoàn toàn với MongoDB.
*   **Caching (Bộ đệm):** **ElastiCache** (Redis/Memcached) và **MemoryDB** giúp giảm tải cho cơ sở dữ liệu chính bằng cách lưu trữ dữ liệu thường truy cập trên RAM.

**4. Networking & Content Delivery (Mạng & Phân phối nội dung)**
*   **Route 53:** Đóng vai trò quản lý DNS và định tuyến lưu lượng mạng. 
*   **CloudFront:** Là mạng phân phối nội dung (CDN), giúp cache các file tĩnh từ S3 ra các vị trí máy chủ biên (edge locations) trên toàn thế giới nhằm giảm độ trễ.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn một bức ảnh sơ đồ (mindmap) tổng quan phân loại các nhóm dịch vụ của AWS, hoặc một ảnh chụp màn hình liệt kê các dịch vụ từ AWS Console]*

---

### Khởi tạo tài khoản AWS và Tổng quan Management Console

Để bắt đầu thực hành, quá trình tạo tài khoản AWS Free Tier đã được tiến hành. 

*   Việc đăng ký yêu cầu liên kết thẻ tín dụng để xác minh danh tính và ngăn chặn gian lận. 
*   Hệ thống sẽ không tính phí nếu các tài nguyên sử dụng nằm trong giới hạn của gói Free Tier. 
*   Gói "Free Basic Support" được lựa chọn để hoàn tất quy trình.

Sau khi đăng nhập, giao diện **AWS Management Console** sẽ xuất hiện, đóng vai trò là trung tâm điều khiển. Các cấu hình cốt lõi cần lưu ý bao gồm:

*   **Region (Khu vực):** Nằm ở góc trên bên phải, đại diện cho các trung tâm dữ liệu vật lý. Việc chọn đúng Region (ví dụ: `us-east-1` thay vì `us-west-1`) giúp giảm độ trễ cho người dùng và tối ưu đáng kể về chi phí.
*   **Khởi tạo tài nguyên:** Qua các bài lab nhỏ, luồng thao tác khởi chạy một máy chủ ảo EC2 đã được thực hiện (chọn hệ điều hành Amazon Linux, thiết lập Security Group mở port 8080). 
*   **Lưu trữ & Cơ sở dữ liệu:** Việc tạo một S3 Bucket (tắt cấu hình `Block all public access` và gắn Bucket Policy) cũng như khởi tạo một database PostgreSQL qua Amazon RDS đã được triển khai thành công.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn ảnh chụp màn hình trang chủ AWS Management Console (nhớ che Account ID) HOẶC ảnh chụp thao tác đang đổi Region ở góc trên cùng bên phải]*

---

### Thiết lập AWS Budget và Nhận 100$ Credits

Việc sử dụng Cloud đòi hỏi sự kiểm soát chặt chẽ về chi phí vì mọi dịch vụ đều tính tiền theo cơ chế Pay-as-you-go. 

*   Ngay sau khi tạo tài khoản, mục **Billing and Cost Management** đã được kiểm tra kỹ lưỡng.
*   **AWS Budgets** và **Billing Alarms** được thiết lập thông qua CloudWatch. Việc đặt ngưỡng ngân sách (ví dụ: 1$) đảm bảo hệ thống sẽ gửi email cảnh báo ngay lập tức nếu chi phí sử dụng vượt qua mức miễn phí. 
*   Việc tìm hiểu và tham gia các chương trình hỗ trợ (sidequests) như AWS Educate đã giúp thu thập được AWS Credits (trị giá 100$). Khoản credit này cung cấp sự an tâm khi thực hành các dịch vụ nằm ngoài gói Free Tier.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn ảnh chụp màn hình giao diện setup thành công AWS Budgets có ngưỡng cảnh báo (Threshold) hoặc ảnh chụp email xác nhận nhận được AWS Credits]*

---

### Khái niệm cơ bản về AWS IAM

**AWS IAM (Identity and Access Management)** là một trong những dịch vụ quan trọng nhất. Dịch vụ này quản lý tập trung việc định danh và phân quyền truy cập vào các tài nguyên trên tài khoản AWS.

Các khái niệm cốt lõi bao gồm:

*   **Resources:** Các thực thể được tạo ra trên AWS (như EC2 instance, S3 bucket).
*   **Actions:** Các hành động có thể thao tác lên Resource (ví dụ: `s3:CreateBucket`).
*   **Policies (Chính sách):** Các tệp tin định dạng JSON quy định quyền hạn, bao gồm: `Effect` (Allow hoặc Deny), `Action`, và `Resource`. 
*   **Explicit Deny (Từ chối tường minh):** Nguyên tắc này luôn đánh bại và ghi đè lên quyền Allow. Nếu không có bất kỳ quyền Allow nào được gán, hệ thống mặc định áp dụng Implicit Deny.
*   **Users & Groups:** User đại diện cho người dùng (cần Access Key và Secret Access Key để gọi API). Group giúp gom nhóm nhiều Users có chung nhiệm vụ để dễ dàng gắn Policy.
*   **Roles (Vai trò):** Cung cấp thông tin xác thực tạm thời thay vì gắn quyền vĩnh viễn. Có thể được gán cho User hoặc cho các dịch vụ AWS (ví dụ: cho phép EC2 tự động truy cập vào S3).
*   **Trust Relationships:** Định nghĩa việc ai được phép đảm nhận (assume) một Role cụ thể, thường dùng trong kịch bản truy cập chéo giữa các tài khoản (Cross-account).

**Các tiêu chuẩn bảo mật (Best Practices) của IAM:**
*   **Bảo vệ tài khoản Root:** Tính năng MFA (Xác thực đa yếu tố) phải được bật ngay lập tức. Tuyệt đối không dùng tài khoản Root cho các tác vụ hàng ngày. 
*   **Quyền tối thiểu (Least Privilege):** Chỉ cấp đúng và đủ các quyền cần thiết để hoàn thành công việc.
*   **Tránh dùng ký tự đại diện:** Không sử dụng ký tự (`*`) bừa bãi trong các file Policy.
*   **IAM Policy Simulator:** Công cụ đắc lực được sử dụng để gỡ lỗi (debug) quyền hạn.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn một đoạn code snippet của file JSON IAM Policy, HOẶC ảnh chụp màn hình giao diện IAM liệt kê các Users/Roles đã tạo]*

---

### Công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày Bắt đầu | Ngày Hoàn thành | Tài liệu tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Tham gia Kick-off & Thiết lập tài khoản | 2026-06-08 | 2026-06-08 | Ghi chú Kick-off |
| 2-3 | Học các kiến thức nền tảng AWS | 2026-06-09 | 2026-06-10 | Video AWS YouTube |
| 4 | Tìm hiểu sâu về IAM & Tiêu chuẩn bảo mật | 2026-06-11 | 2026-06-11 | Các khái niệm IAM |
| 5 | Thiết lập Budgets & Cảnh báo thanh toán | 2026-06-12 | 2026-06-12 | AWS Console |

### Thành tựu Tuần 1:

*   Đã tạo và bảo mật thành công tài khoản AWS Free Tier.
*   Nắm vững bức tranh tổng quan về các nhóm dịch vụ AWS (Điện toán, Lưu trữ, Cơ sở dữ liệu, Mạng).
*   Áp dụng các tiêu chuẩn bảo mật IAM: không sử dụng tài khoản Root cho công việc hàng ngày và thiết lập IAM Users/Roles chuẩn.
*   Cấu hình thành công AWS Budgets để ngăn chặn các chi phí phát sinh ngoài ý muốn.

---

### Thuật ngữ (Glossary)

| Viết tắt | Ý nghĩa |
| --- | --- |
| AI | Artificial Intelligence (Trí tuệ nhân tạo) |
| API | Application Programming Interface (Giao diện lập trình ứng dụng) |
| AWS | Amazon Web Services (Nền tảng điện toán đám mây của Amazon) |
| BE | Backend (Phần xử lý phía máy chủ của ứng dụng) |
| DB | Database (Cơ sở dữ liệu) |
| EC2 | Amazon Elastic Compute Cloud (Máy chủ ảo trên AWS) |
| ELB | Elastic Load Balancer (Bộ cân bằng tải) |
| FE | Frontend (Phần giao diện người dùng của ứng dụng) |
| IAM | AWS Identity and Access Management (Quản lý định danh và truy cập) |
| RDS | Amazon Relational Database Service (Dịch vụ cơ sở dữ liệu quan hệ) |
| S3 | Amazon Simple Storage Service (Lưu trữ đối tượng) |
| SNS | Amazon Simple Notification Service (Dịch vụ gửi thông báo) |
| UI | User Interface (Giao diện người dùng) |
| UX | User Experience (Trải nghiệm người dùng) |