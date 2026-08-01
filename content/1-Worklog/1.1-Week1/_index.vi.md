---
title: "Báo cáo công việc Tuần 1"
date: 2026-06-08
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu Tuần 1:

*   Kết nối và làm quen với các thành viên của First Cloud AI Journey.
*   Tham gia buổi họp kick-off của nhóm và thống nhất về đề xuất kiến trúc hybrid AWS.
*   Đọc tổng quan về các dịch vụ AWS được tham chiếu trong sơ đồ kiến trúc dự án.
*   Tạo tài khoản AWS.

---

### Tìm hiểu Kiến thức Chung về Dịch vụ Đám mây AWS

Tuần đầu tiên được dành để nghiên cứu hệ sinh thái tổng thể của Amazon Web Services (AWS).

![AWS Introduction by official aws utube account](/images/1-Worklog/1.1-Week1/2.png)
Giới thiệu về AWS từ kênh YouTube chính thức của AWS

Dưới đây là phân tích chi tiết các nhóm dịch vụ cốt lõi đã tìm hiểu:

**1. Dịch vụ Máy tính (Compute Services)**
*   **Amazon EC2 (Elastic Compute Cloud):** Thành phần cơ bản để khởi tạo các máy chủ ảo (instances) trên đám mây.
*   **Các dịch vụ được quản lý (Managed Services):** Các dịch vụ như **Elastic Beanstalk** (PaaS giúp triển khai nhanh) và **Amazon Lightsail** (lý tưởng cho các ứng dụng web đơn giản) giúp trừu tượng hóa việc quản lý hạ tầng bên dưới.
*   **Kiến trúc Serverless:** **AWS Lambda** cho phép thực thi mã để phản hồi lại các sự kiện mà không cần cung cấp máy chủ. Mô hình này chỉ tính phí cho thời gian tính toán chính xác đã tiêu thụ (đo bằng mili giây).
*   **Containers:** **ECS** và **EKS** (dành cho Kubernetes) quản lý các ứng dụng được container hóa, thường kết hợp với **AWS Fargate** như một engine serverless compute để loại bỏ việc quản lý EC2 instance.

**2. Dịch vụ Lưu trữ (Storage Services)**
*   **Amazon S3 (Simple Storage Service):** Dịch vụ lưu trữ đối tượng có khả năng mở rộng cao, tiết kiệm chi phí, dùng cho các tài nguyên tĩnh (hình ảnh, HTML, CSS, video).
*   **EBS (Elastic Block Store):** Cung cấp lưu trữ khối (block storage) hiệu suất cao gắn trực tiếp vào một compute instance.
*   **EFS (Elastic File System):** Sử dụng cho lưu trữ tệp dùng chung trên nhiều instance cùng lúc.

**3. Dịch vụ Cơ sở dữ liệu (Database Services)**
*   **Cơ sở dữ liệu Quan hệ:** **Amazon RDS** tự động hóa các tác vụ quản trị cho SQL databases. **Amazon Aurora** là cơ sở dữ liệu quan hệ độc quyền, hiệu suất cao với biến thể serverless có thể scale xuống 0.
*   **Cơ sở dữ liệu NoSQL:** **DynamoDB** là cơ sở dữ liệu key-value store được quản lý hoàn toàn với độ trễ tính bằng một chữ số mili giây. **DocumentDB** đóng vai trò là cơ sở dữ liệu tài liệu tương thích với MongoDB.
*   **Caching:** **ElastiCache** và **MemoryDB** giảm tải cho database bằng cách giữ các dữ liệu truy cập thường xuyên trong bộ nhớ.

**4. Mạng & Phân phối Nội dung (Networking & Content Delivery)**
*   **Route 53:** Xử lý quản lý DNS và định tuyến lưu lượng.
*   **CloudFront:** Hoạt động như một Mạng phân phối nội dung (CDN), cache các tài nguyên tĩnh từ S3 đến các edge location trên toàn thế giới để giảm thiểu độ trễ cho người dùng toàn cầu.

![Các dịch vụ quan trọng nhất của AWS](/images/1-Worklog/1.1-Week1/1.png)

Đây là một video YouTube về các dịch vụ AWS quan trọng nhất cần học, nhìn chung giúp nắm bắt được quy mô của hệ thống. 

[Mình để link ở đây để các bạn xem, rất đáng để tham khảo](https://www.youtube.com/watch?v=OGYEXGy8ca4)

---

### Tạo tài khoản AWS Console & Tổng quan về Management Console

*   Quá trình đăng ký yêu cầu liên kết thẻ tín dụng/ghi nợ để xác minh danh tính và ngăn chặn gian lận. 
*   Sẽ không có khoản phí nào phát sinh miễn là mức sử dụng nằm hoàn toàn trong giới hạn của Free Tier (Gói miễn phí). 
*   Gói "Free Basic Support" (Hỗ trợ cơ bản miễn phí) đã được chọn để hoàn tất quy trình.

Sau khi đăng nhập, **AWS Management Console** đóng vai trò là bảng điều khiển trung tâm. Các khái niệm vận hành chính bao gồm:

*   **Regions (Khu vực):** Nằm ở góc trên bên phải, đại diện cho các vị trí trung tâm dữ liệu vật lý riêng biệt. Việc chọn Region phù hợp (ví dụ: `us-east-1` N. Virginia thay vì `us-west-1` N. California) là rất quan trọng để giảm thiểu độ trễ và tối ưu chi phí.
*   **Cấp phát tài nguyên (Resource Provisioning):** Thông qua các mini-lab thực hành, quá trình khởi chạy một EC2 instance đã hoàn tất (sử dụng HĐH Amazon Linux và cấu hình Security Group để mở port 8080).
*   **Tạo Lưu trữ & Database:** Một S3 Bucket đã được tạo (tắt `Block all public access` và gắn Bucket Policy để cho phép xem public), cùng với việc cấp phát cơ sở dữ liệu PostgreSQL thông qua Amazon RDS.

![Mô tả cho ảnh](/images/1-Worklog/1.1-Week1/3.png)

[Đây là link video hướng dẫn](https://www.youtube.com/watch?v=gUaNRNjLkcM)

Mặc dù đây là một video giới thiệu tốt về console, nhưng nó đã khá cũ. Mình khuyên những ai đọc bài này nên tìm kiếm các video mới hơn vì AWS rất hay thay đổi giao diện console của họ.

---

### Khám phá AWS Budget và làm Nhiệm vụ phụ (Sidequests) để nhận thêm 100$ Credits

Việc sử dụng các dịch vụ đám mây đòi hỏi sự kiểm soát nghiêm ngặt về tài chính. Mô hình Pay-As-You-Go (Dùng bao nhiêu trả bấy nhiêu) có thể nhanh chóng tích lũy chi phí nếu quản lý sai. 

*   Ngay sau khi tạo tài khoản, dashboard **Billing and Cost Management** đã được kiểm tra.
*   **AWS Budgets** và **Billing Alarms** đã được cấu hình qua CloudWatch. Việc thiết lập ngưỡng ngân sách (ví dụ: $1) đảm bảo sẽ có email cảnh báo ngay lập tức nếu mức sử dụng hàng tháng vô tình vượt quá giới hạn Free Tier. 
*   Các nhiệm vụ phụ và chương trình khác nhau của AWS (chẳng hạn như AWS Educate) đã được nghiên cứu để lấy AWS Credits (trị giá 100$). Những credits này cung cấp một mạng lưới an toàn tài chính để thử nghiệm các dịch vụ ngoài phạm vi Free Tier.

---

### Các khái niệm cơ bản về AWS IAM

**AWS IAM (Identity and Access Management)** đóng vai trò như người gác cổng, quản lý tập trung danh tính và quyền truy cập.

Các khái niệm cốt lõi đã học:

*   **Resources (Tài nguyên):** Các thực thể được tạo trong AWS (ví dụ: EC2 instances, S3 buckets, Lambda functions).
*   **Actions (Hành động):** Các thao tác cụ thể được thực hiện trên một Resource (ví dụ: `s3:CreateBucket`, `lambda:InvokeFunction`).
*   **Policies (Chính sách):** Các tài liệu JSON định nghĩa các quyền thông qua `Effect` (Allow - Cho phép hoặc Deny - Từ chối), một `Action` và một `Resource`. 

![Mô tả cho ảnh](/images/1-Worklog/1.1-Week1/4.png)

---

### Các công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Tham gia Kick-off & Cài đặt tài khoản | 2026-06-08 | 2026-06-08 | Ghi chú Kick-off |
| 2-3 | Học các kiến thức cơ bản về AWS | 2026-06-09 | 2026-06-10 | AWS YouTube videos |
| 4 | Tìm hiểu sâu về IAM & Thực hành tốt nhất | 2026-06-11 | 2026-06-11 | Các khái niệm cốt lõi IAM |
| 5 | Cài đặt Budgets & Cảnh báo thanh toán | 2026-06-12 | 2026-06-12 | AWS Console |

### Thành quả Tuần 1:

*   Tạo và bảo mật thành công tài khoản AWS Free Tier.
*   Nắm được kiến thức toàn diện về bức tranh dịch vụ rộng lớn của AWS (Máy tính, Lưu trữ, Cơ sở dữ liệu, Mạng).
*   Áp dụng các best practice của IAM bằng cách thiết lập IAM Users/Roles tiêu chuẩn và bảo mật tài khoản Root.
*   Cấu hình AWS Budgets hiệu quả để ngăn chặn các chi phí ngoài ý muốn.

---