---
title: "Báo cáo công việc Tuần 4"
date: 2026-06-29
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 

[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Mục tiêu Tuần 4:

*   Hiểu rõ sự khác biệt giữa các họ lưu trữ của AWS: Lưu trữ Block, Object và File.
*   Tìm hiểu sâu về Amazon S3, khám phá các storage classes (lớp lưu trữ), lifecycle policies (chính sách vòng đời), và cách tải lên an toàn thông qua Presigned URLs.
*   So sánh Amazon EBS (Elastic Block Store) và Amazon EFS (Elastic File System).
*   Phân tích bài toán Kinh tế Đám mây (Cloud Economics) bằng cách tối ưu hóa việc phân phối frontend sử dụng S3 và CloudFront.
*   Nắm vững AWS Amplify Gen 2 để tạo nguyên mẫu (prototyping) ứng dụng full-stack nhanh chóng trong các dự án tương lai.

---

### Hệ sinh thái Lưu trữ AWS: Object, Block, và File

AWS phân loại lưu trữ thành ba họ chính để đáp ứng các nhu cầu khác nhau về hiệu suất, tính bền vững và kiến trúc.

**1. Lưu trữ Object (Đối tượng): Amazon S3**
*   **Khái niệm:** S3 (Simple Storage Service) là dịch vụ lưu trữ đối tượng được thiết kế để lưu trữ và bảo vệ lượng dữ liệu bất kỳ, từ các tệp phương tiện đến các data lakes (hồ dữ liệu) khổng lồ.
*   **Bảo mật & Presigned URLs:** Theo mặc định, các S3 buckets chặn mọi quyền truy cập công khai. Việc tải lên và tải xuống an toàn có thể được quản lý bằng **Presigned URLs** (URL được ký trước) tạo qua AWS SDK. Cơ chế này cho phép các ứng dụng client (như trình duyệt web) tải tệp trực tiếp lên S3 trong một khung thời gian giới hạn. Nó ngăn chặn việc lộ thông tin xác thực AWS và tránh tình trạng các payload tệp nặng làm tắc nghẽn máy chủ backend.
*   **Các lớp lưu trữ (Storage Classes):** S3 cung cấp nhiều tier (bậc) để tối ưu chi phí dựa trên tần suất truy cập:
    *   *S3 Standard:* Tính sẵn sàng cao và độ trễ thấp đối với dữ liệu truy cập thường xuyên (hot data).
    *   *S3 Standard-Infrequent Access (IA):* Chi phí lưu trữ thấp hơn cho dữ liệu cũ nhưng vẫn yêu cầu truy cập nhanh khi cần.
    *   *S3 Glacier / Deep Archive:* Lưu trữ chi phí thấp nhất, được thiết kế cho việc lưu trữ dài hạn (archiving) và tuân thủ các quy định.
*   **Quy tắc vòng đời (Lifecycle Rules):** Việc tối ưu hóa chi phí được tự động hóa hoàn toàn bằng cách cấu hình các quy tắc vòng đời. Các quy tắc này tự động chuyển đổi các đối tượng giữa các lớp lưu trữ (ví dụ: chuyển dữ liệu từ Standard sang IA sau 30 ngày, và sang Glacier sau 90 ngày).
*   **Kiến trúc hướng sự kiện (Event-Driven Architecture):** S3 có thể phát ra các sự kiện (events) khi tạo hoặc xóa đối tượng. Các sự kiện này có thể kích hoạt mượt mà các hàm AWS Lambda để thực hiện các tác vụ như thay đổi kích thước ảnh hoặc trích xuất metadata (siêu dữ liệu).

**2. Lưu trữ Block vs. Lưu trữ File**
*   **Amazon EBS (Elastic Block Store):** Dịch vụ lưu trữ khối được thiết kế để gắn vào một EC2 instance duy nhất tại một thời điểm. Nó hoạt động giống như một ổ cứng vật lý (SSD/HDD), hiệu năng cực cao đối với các khối lượng công việc giao dịch và cơ sở dữ liệu quan hệ. Dữ liệu trên EBS tồn tại độc lập với vòng đời của instance.
*   **Amazon EFS (Elastic File System):** Dịch vụ lưu trữ tệp serverless (không máy chủ) và co giãn hoàn toàn, sử dụng giao thức NFS (lý tưởng cho môi trường Linux). Khác với EBS, EFS có thể được mount (gắn) đồng thời trên hàng trăm EC2 instances, khiến nó trở nên hoàn hảo cho các khối lượng công việc chia sẻ dữ liệu và các ứng dụng phân tán.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/1.png)


![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/2.png)

---

### Kinh tế Đám mây & Tối ưu hóa Hiệu suất

Tối ưu hóa chi phí đám mây đòi hỏi sự phân tách kiến trúc và các chiến lược phân phối nội dung thông minh.

*   **Tách rời (Decoupling) Frontend và Backend:** Lưu trữ các tài nguyên tĩnh (như bản build React hoặc Vue.js) trên các EC2 instances gây ra chi phí tính toán đắt đỏ và không cần thiết. Thay vào đó, các tài nguyên tĩnh này được tải lên một S3 bucket cấu hình riêng cho việc lưu trữ trang web tĩnh (static website hosting).
*   **Tích hợp Amazon CloudFront:** S3 bucket được kết hợp với CloudFront, một Mạng phân phối nội dung (CDN) toàn cầu. CloudFront cache (lưu trữ bộ nhớ tạm) ứng dụng React tại các edge locations trên toàn thế giới.
*   **Lợi ích về Chi phí & Hiệu suất:** 
    *   Kiến trúc này hoàn toàn giảm tải lưu lượng truy cập tĩnh khỏi các máy chủ backend.
    *   Các instance tính toán (EC2/ECS) chỉ phải xử lý các request API động. Điều này cho phép thu hẹp quy mô cơ sở hạ tầng backend, giảm thiểu đáng kể chi phí EC2 hàng tháng.
    *   Người dùng cuối (End-users) trải nghiệm thời gian tải trang gần như tức thì, bất kể vị trí địa lý của họ.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/3.png)

---

### AWS Amplify Gen 2: Tăng tốc Lập trình Full-Stack

AWS Amplify đơn giản hóa việc phát triển full-stack bằng cách cấp phát tự động các dịch vụ backend. Gen 2 giới thiệu một phương pháp tiếp cận code-first (ưu tiên mã nguồn), thiết lập nó như một công cụ quan trọng để xây dựng khung (scaffolding) cho các dự án tương lai.

**1. Các tính năng cốt lõi của Amplify Gen 2**
*   **Infrastructure as Code (IaC) dựa trên TypeScript:** Khác với cách tiếp cận CLI/Studio của Gen 1, Gen 2 định nghĩa tất cả các tài nguyên backend (Xác thực, Dữ liệu, Lưu trữ) bằng TypeScript (ví dụ: trong file `resource.ts`). Lập trình viên có thể sử dụng các ngôn ngữ lập trình quen thuộc thay vì phải học các cú pháp CloudFormation hoặc Terraform phức tạp.
*   **Xác thực Tự động:** Việc tích hợp Amazon Cognito được đơn giản hóa triệt để. Vài dòng code sẽ tự động tạo ra một UI component đăng nhập/đăng ký đầy đủ chức năng (cái bọc `Authenticator` trong React) đi kèm với các identity pools ở backend cực kỳ an toàn.
*   **Quản lý Mô hình Dữ liệu:** Việc định nghĩa một data model (mô hình dữ liệu) sẽ tự động cấp phát một bảng Amazon DynamoDB và một AWS AppSync GraphQL API. Frontend ngay lập tức có được các thao tác CRUD (Tạo, Đọc, Cập nhật, Xóa) có sẵn (out-of-the-box) với khả năng gợi ý kiểu dữ liệu đầy đủ (IntelliSense).

**2. CI/CD và Môi trường Sandbox**
*   **Sandboxes cho từng lập trình viên:** Amplify Gen 2 giới thiệu cloud sandboxes (`npx amx sandbox`). Mỗi lập trình viên sẽ có một cloud backend bị cách ly trong quá trình phát triển ở local. Điều này ngăn chặn hoàn toàn việc xung đột cơ sở dữ liệu và ghi đè code giữa các thành viên trong nhóm.
*   **Triển khai liên tục (Continuous Deployment):** Amplify kết nối trực tiếp với các kho lưu trữ GitHub. Việc push code lên một nhánh cụ thể sẽ tự động kích hoạt một pipeline (đường ống) build frontend React và đồng thời cập nhật cơ sở hạ tầng cloud backend tương ứng.

**3. Ứng dụng trong các dự án tương lai**
*   Amplify sẽ được sử dụng nhiều để tạo nguyên mẫu nhanh (rapid prototype) cho các ứng dụng web.
*   Nó giúp giảm đáng kể thời gian cài đặt boilerplate (khung mã nền) cho việc cấu hình xác thực, APIs, và cấp phát cơ sở dữ liệu.
*   Các môi trường sandbox cách ly sẽ đảm bảo luồng công việc phát triển nhóm an toàn, không xung đột và mang tính cộng tác cao.

![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/4.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/5.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/6.png)

![So sánh các loại EC2 Instance](/images/1-Worklog/1.4-Week4/7.png)

---

### Các công việc hoàn thành trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Nghiên cứu các dịch vụ Lưu trữ (S3, EBS, EFS) | 2026-06-29 | 2026-06-29 | So sánh Lưu trữ AWS |
| 2 | Cấu hình Presigned URLs qua SDK | 2026-06-30 | 2026-06-30 | Hướng dẫn Bảo mật S3 |
| 3 | Tối ưu chi phí với S3 & CloudFront | 2026-07-01 | 2026-07-01 | Tài liệu Cloud Economics |
| 4 | Khám phá tính năng AWS Amplify Gen 2 | 2026-07-02 | 2026-07-02 | Hướng dẫn Amplify React |
| 5 | Xây dựng ứng dụng Prototype Full-Stack | 2026-07-03 | 2026-07-03 | Kiểm thử Local Sandbox |

### Thành quả Tuần 4:

*   Phân biệt được chính xác các trường hợp sử dụng (use cases) cho lưu trữ Object, Block và File trên AWS.
*   Khái niệm hóa một kiến trúc tải tệp lên an toàn sử dụng S3 Presigned URLs để vượt qua sự tắc nghẽn mạng ở backend.
*   Thiết kế một kiến trúc tối ưu chi phí bằng cách di chuyển các tài nguyên frontend tĩnh sang mô hình Serverless CloudFront + S3.
*   Nắm vững cách tiếp cận TypeScript-first trong AWS Amplify Gen 2 để cấp phát nhanh xác thực Cognito và cơ sở dữ liệu DynamoDB.
*   Xác thực được tiện ích của Amplify Sandboxes cho các luồng công việc phát triển nhóm bị cách ly, không xung đột.


---

### Buổi họp nhóm — 05/07/2026

**Người tham dự:** Hiếu, Nguyên, Danh, Hùng
**Vắng mặt:** Thanh

**Trình bày**

- **Thanh** (chia sẻ bất đồng bộ) trình bày thiết kế API cho tính năng **Đặt sân (Booking)**: <https://d2kk6nff0gmlot.cloudfront.net/1-worklog/1.3-week3/>
- **Nguyên** chia sẻ thiết kế API cho tính năng **Xác thực người dùng (User Authentication)**: [GitHub PR #1](https://github.com/minyryo/aws-fcaj-2026/pull/1/changes/b495a65faa1e3bc638aac259dc7008c1d0fccf5a)
- **Hiếu** hiển thị phiên bản mới của sơ đồ kiến trúc, ứng dụng **AWS Amplify** để xử lý giao diện người dùng (UI).

**Biểu quyết Tech Stack**

Tất cả các thành viên đã biểu quyết chọn tech stack chính:

| Lớp (Layer) | Lựa chọn                      |
| -------- | ----------------------------- |
| Frontend | TypeScript (React)            |
| Backend  | FastAPI (Python) + PostgreSQL |

**Phân công công việc**

| Hạng mục / Nhóm                | Phụ trách      | Sản phẩm bàn giao (Deliverable)                                                                        |
| ------------------------------ | -------------- | ------------------------------------------------------------------------------------------------------ |
| Frontend — Thiết kế UI         | Danh & Hùng    | Thống nhất & cải thiện thiết kế UI sao cho khớp với tài liệu API                                       |
| Backend — Tech Stack           | Toàn bộ nhóm BE| Đề xuất tech stack cho việc lập trình BE                                                               |
| Backend — Thống nhất API & DB  | Hiếu           | Thống nhất các API & thiết kế cơ sở dữ liệu                                                            |
| Backend — Repo & Triển khai    | Hiếu           | Khởi tạo code repo và cung cấp hướng dẫn triển khai (deployment) cho cả nhóm                           |
| Backend — Review API & Nghiên cứu| Thanh & Nguyên | Đánh giá tài liệu API hiện có & nghiên cứu cách viết code API bằng FastAPI (Python)                    |


---

### Glossary

| Abbreviation | Meaning |
| --- | --- |
| AI | Artificial Intelligence |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| EC2 | Amazon Elastic Compute Cloud — virtual servers on AWS |
| EFS | Amazon Elastic File System — shared, elastic file storage |
| ELB | Elastic Load Balancer — distributes incoming traffic across instances |
| IAM | AWS Identity and Access Management — users, roles, and permissions |
| IDE | Integrated Development Environment |
| RDS | Amazon Relational Database Service — managed SQL databases |
| REST | Representational State Transfer — architectural style for HTTP APIs |
| S3 | Amazon Simple Storage Service — object storage |
| VPC | Amazon Virtual Private Cloud — an isolated virtual network on AWS |