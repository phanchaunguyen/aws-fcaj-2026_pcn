---
title: "Báo cáo Tuần 4"
date: 2026-06-29
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu Tuần 4:

*   Hiểu rõ sự khác biệt giữa các họ lưu trữ của AWS: Lưu trữ Block, Object và File.
*   Tìm hiểu sâu về Amazon S3, khám phá các hạng lưu trữ (storage classes), vòng đời dữ liệu (lifecycle) và tải lên an toàn qua Presigned URLs.
*   So sánh Amazon EBS (Elastic Block Store) và Amazon EFS (Elastic File System).
*   Phân tích bài toán Kinh tế đám mây (Cloud Economics) thông qua việc phân phối giao diện người dùng bằng S3 và CloudFront.
*   Làm chủ AWS Amplify Gen 2 để phát triển nhanh các ứng dụng full-stack cho các dự án trong tương lai.

---

### Hệ sinh thái Lưu trữ AWS: Object, Block và File

AWS phân loại lưu trữ thành ba họ chính nhằm đáp ứng các nhu cầu khác nhau về hiệu năng và kiến trúc.

**1. Lưu trữ Object: Amazon S3**
*   **Khái niệm:** S3 (Simple Storage Service) là dịch vụ lưu trữ dạng đối tượng, được thiết kế để lưu trữ mọi loại dữ liệu, từ tệp đa phương tiện đến các kho dữ liệu khổng lồ (data lake).
*   **Bảo mật & Presigned URLs:** Mặc định, các S3 bucket chặn toàn bộ quyền truy cập công khai. Việc upload và download có thể được bảo mật thông qua **Presigned URLs** tạo từ AWS SDK. Cơ chế này cho phép các ứng dụng client (như trình duyệt web) upload file trực tiếp lên S3 trong một khoảng thời gian giới hạn. Nó giúp ẩn thông tin xác thực AWS và ngăn chặn việc các file dung lượng lớn làm nghẽn cổ chai máy chủ backend.
*   **Hạng lưu trữ (Storage Classes):** S3 cung cấp nhiều cấp độ để tối ưu hóa chi phí dựa trên tần suất truy cập:
    *   *S3 Standard:* Độ trễ thấp, tính sẵn sàng cao dành cho dữ liệu truy cập thường xuyên (hot data).
    *   *S3 Standard-IA (Infrequent Access):* Chi phí lưu trữ thấp hơn cho dữ liệu ít truy cập nhưng vẫn cần tốc độ lấy dữ liệu nhanh khi cần.
    *   *S3 Glacier / Deep Archive:* Lưu trữ với chi phí thấp nhất, chuyên dùng để lưu trữ dữ liệu dài hạn (archiving) và tuân thủ luật pháp.
*   **Quy tắc Vòng đời (Lifecycle Rules):** Chi phí được tối ưu hóa hoàn toàn tự động bằng cách thiết lập các quy tắc vòng đời. Dữ liệu sẽ tự động chuyển đổi giữa các hạng lưu trữ (ví dụ: chuyển từ Standard sang IA sau 30 ngày, và sang Glacier sau 90 ngày).
*   **Kiến trúc Hướng sự kiện:** S3 có thể phát ra các sự kiện khi một đối tượng được tạo hoặc xóa. Các sự kiện này có thể kích hoạt tự động các hàm AWS Lambda để thực hiện các tác vụ như thay đổi kích thước ảnh hoặc trích xuất siêu dữ liệu.

**2. Lưu trữ Block so với Lưu trữ File**
*   **Amazon EBS (Elastic Block Store):** Dịch vụ lưu trữ khối được thiết kế để gắn vào duy nhất một máy chủ EC2 tại một thời điểm. Nó hoạt động giống như một ổ cứng vật lý (SSD/HDD), có hiệu năng rất cao cho các cơ sở dữ liệu quan hệ. Dữ liệu trên EBS tồn tại độc lập với vòng đời của EC2 instance.
*   **Amazon EFS (Elastic File System):** Dịch vụ lưu trữ tệp serverless sử dụng giao thức NFS (lý tưởng cho môi trường Linux). Khác với EBS, EFS có thể được gắn đồng thời vào hàng trăm EC2 instances cùng lúc, cực kỳ hoàn hảo cho các ứng dụng phân tán cần chia sẻ dữ liệu chung.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn một bảng so sánh hoặc biểu đồ minh họa sự khác biệt về kiến trúc giữa S3, EBS và EFS, làm nổi bật khả năng kết nối đồng thời và các use-case tiêu biểu.]*

---

### Tối ưu hóa Hiệu năng & Kinh tế Đám mây (Cloud Economics)

Việc tối ưu hóa chi phí đám mây đòi hỏi sự phân tách kiến trúc và chiến lược phân phối nội dung thông minh.

*   **Phân tách Frontend và Backend:** Việc lưu trữ các tài nguyên tĩnh (như bản build của React hoặc Vue.js) trên máy chủ EC2 gây lãng phí chi phí tính toán. Thay vào đó, các tài nguyên tĩnh này được tải lên một S3 bucket đã được cấu hình tính năng Static Website Hosting.
*   **Tích hợp Amazon CloudFront:** S3 bucket được kết hợp với CloudFront - mạng phân phối nội dung toàn cầu (CDN). CloudFront sẽ lưu trữ bộ nhớ đệm (cache) ứng dụng React tại các máy chủ biên (edge locations) trên toàn thế giới.
*   **Lợi ích về Chi phí & Hiệu năng:** 
    *   Kiến trúc này giảm tải hoàn toàn lưu lượng truy cập tĩnh cho máy chủ backend.
    *   Các máy chủ tính toán (EC2/ECS) chỉ tập trung xử lý các request API động. Điều này cho phép thu nhỏ quy mô hạ tầng backend, giảm thiểu đáng kể chi phí duy trì EC2 hàng tháng.
    *   Người dùng cuối trải nghiệm thời gian tải trang gần như ngay lập tức bất kể vị trí địa lý.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn một biểu đồ kiến trúc cho thấy người dùng truy cập ứng dụng React qua CloudFront/S3, trong khi các request API động được định tuyến qua Application Load Balancer tới Backend.]*

---

### AWS Amplify Gen 2: Tăng tốc Phát triển Full-Stack

AWS Amplify đơn giản hóa việc phát triển full-stack bằng cách cung cấp tự động các dịch vụ backend. Phiên bản Gen 2 giới thiệu cách tiếp cận code-first (ưu tiên mã nguồn), trở thành một công cụ quan trọng để dựng khung (scaffolding) cho các dự án tương lai.

**1. Các Tính năng Cốt lõi của Amplify Gen 2**
*   **Cơ sở hạ tầng dưới dạng Mã nguồn bằng TypeScript (IaC):** Khác với phương pháp dùng CLI/Studio của Gen 1, Gen 2 định nghĩa toàn bộ tài nguyên backend (Xác thực, Dữ liệu, Lưu trữ) bằng TypeScript (ví dụ: trong file `resource.ts`). Các nhà phát triển có thể sử dụng ngôn ngữ lập trình quen thuộc thay vì phải học cú pháp phức tạp của CloudFormation hay Terraform.
*   **Xác thực Tự động:** Việc tích hợp Amazon Cognito được đơn giản hóa tối đa. Chỉ vài dòng code có thể tạo ra một giao diện Đăng nhập/Đăng ký hoàn chỉnh (component `Authenticator` trong React) cùng với identity pools bảo mật ở backend.
*   **Quản lý Mô hình Dữ liệu:** Khi định nghĩa một mô hình dữ liệu, Amplify sẽ tự động tạo một bảng Amazon DynamoDB và một AWS AppSync GraphQL API. Frontend lập tức có sẵn các hàm thao tác CRUD (Thêm, Đọc, Sửa, Xóa) với khả năng gợi ý kiểu dữ liệu (IntelliSense) hoàn chỉnh.

**2. CI/CD và Môi trường Sandbox**
*   **Sandbox cho Từng Nhà phát triển:** Amplify Gen 2 giới thiệu các đám mây sandbox (`npx amx sandbox`). Mỗi nhà phát triển có một môi trường backend biệt lập trên cloud trong quá trình code local. Điều này ngăn chặn hoàn toàn việc ghi đè code hoặc xung đột cơ sở dữ liệu giữa các thành viên trong nhóm.
*   **Triển khai Liên tục (CI/CD):** Amplify kết nối trực tiếp với kho lưu trữ GitHub. Việc push code lên một nhánh cụ thể sẽ tự động kích hoạt luồng pipeline: build frontend React và đồng thời cập nhật hạ tầng backend trên cloud.

**3. Ứng dụng trong các Dự án Tương lai**
*   Amplify sẽ được sử dụng mạnh mẽ để tạo mẫu nhanh (prototype) các ứng dụng web.
*   Nó giúp giảm đáng kể thời gian thiết lập các đoạn code rập khuôn (boilerplate) cho việc cấu hình xác thực, API và cơ sở dữ liệu.
*   Môi trường sandbox biệt lập sẽ đảm bảo quy trình phát triển nhóm diễn ra an toàn, không xung đột và mang tính cộng tác cao.

> *[NOTE MÀN HÌNH CHÈN ẢNH: Chèn một ảnh chụp màn hình AWS Amplify Console hiển thị một luồng triển khai CI/CD thành công, hoặc một đoạn code định nghĩa mô hình dữ liệu TypeScript (`resource.ts`).]*

---

### Công việc Hoàn thành trong Tuần:

| Ngày | Công việc | Ngày Bắt đầu | Ngày Hoàn thành | Tài liệu Tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Học về các Dịch vụ Lưu trữ (S3, EBS, EFS) | 2026-06-29 | 2026-06-29 | Bảng so sánh Lưu trữ AWS |
| 2 | Cấu hình Presigned URLs qua SDK | 2026-06-30 | 2026-06-30 | Hướng dẫn Bảo mật S3 |
| 3 | Tối ưu Chi phí với S3 & CloudFront | 2026-07-01 | 2026-07-01 | Tài liệu Kinh tế Đám mây |
| 4 | Khám phá Tính năng AWS Amplify Gen 2 | 2026-07-02 | 2026-07-02 | Tutorial Amplify React |
| 5 | Xây dựng Ứng dụng Full-Stack Mẫu | 2026-07-03 | 2026-07-03 | Kiểm thử Local Sandbox |

### Thành tựu Tuần 4:

*   Phân biệt rõ ràng các trường hợp sử dụng (use cases) của lưu trữ Object, Block và File trên AWS.
*   Lên ý tưởng cho kiến trúc tải tệp an toàn bằng S3 Presigned URLs để tránh tắc nghẽn mạng ở backend.
*   Thiết kế một kiến trúc tối ưu chi phí bằng cách di chuyển các tài nguyên frontend tĩnh sang mô hình Serverless CloudFront + S3.
*   Làm chủ phương pháp tiếp cận TypeScript-first trong AWS Amplify Gen 2 để nhanh chóng cung cấp tính năng xác thực Cognito và cơ sở dữ liệu DynamoDB.
*   Xác nhận hiệu quả của Amplify Sandboxes cho các quy trình làm việc nhóm biệt lập, không xảy ra xung đột.