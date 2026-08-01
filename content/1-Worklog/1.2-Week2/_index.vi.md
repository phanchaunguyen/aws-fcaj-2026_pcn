---
title: "Báo cáo công việc Tuần 2"
date: 2026-06-15
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu Tuần 2:

*   Đi sâu vào AWS Identity and Access Management (IAM) và các cấu hình truy cập chéo tài khoản (cross-account access).
*   Hiểu các thành phần cơ bản của Mạng AWS (AWS Networking).
*   Khám phá kiến trúc Virtual Private Cloud (VPC).
*   Học cách cấu hình địa chỉ IP, định tuyến, public/private subnets, và quyền truy cập internet.


### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 


[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)

![Lộ trình AWS](/images/1-Worklog/1.2-Week2/1.png)

Bạn có thể tự tạo một bản cho riêng mình để theo dõi tiến độ...

---

### Mạng AWS & VPC

Virtual Private Cloud (VPC) hoạt động như một mạng ảo được cách ly về mặt logic, triển khai bên trong một tài khoản AWS cụ thể. Nó có chức năng tương tự như một trung tâm dữ liệu tại chỗ (on-premises) truyền thống nhưng tận dụng được cơ sở hạ tầng có khả năng mở rộng cao của đám mây.

**1. VPC và Regions (Khu vực)**
*   Một VPC gắn liền với một Region AWS duy nhất nhưng trải dài trên nhiều Availability Zones (AZ - Vùng sẵn sàng) bên trong Region đó. 
*   Thiết kế multi-AZ này rất quan trọng để đảm bảo tính sẵn sàng cao và khả năng chịu lỗi cho các ứng dụng.

**2. CIDR Blocks và Cấp phát địa chỉ IP**
*   Khi tạo một VPC, một dải địa chỉ IPv4 phải được cấp phát bằng cách sử dụng **CIDR block** (Định tuyến liên miền không phân lớp), ví dụ như `10.0.0.0/16`.
*   Ký hiệu này quyết định tổng số lượng địa chỉ IP khả dụng cho các tài nguyên trong mạng đó. 
*   Cần lập kế hoạch cẩn thận để tránh việc các CIDR block bị trùng lặp, đặc biệt là khi kết nối nhiều VPC sau này thông qua VPC Peering hoặc Transit Gateways.


---

### Cấu trúc Mạng: Subnets (Mạng con)

Subnet là các phân khu nhỏ hơn của dải địa chỉ IP thuộc VPC. Khác với VPC, một subnet bị gắn chặt với một Availability Zone duy nhất. Subnet được sử dụng để phân nhóm các tài nguyên dựa trên các yêu cầu về bảo mật và định tuyến.

*   **Public Subnets (Mạng con công khai):** Được thiết kế cho các tài nguyên hướng ra internet (ví dụ: web servers, application load balancers). Tài nguyên triển khai ở đây được gán địa chỉ IP public và có tuyến đường (route) trực tiếp ra internet.
*   **Private Subnets (Mạng con riêng tư):** Dùng để lưu trữ các hệ thống backend và cơ sở dữ liệu (ví dụ: Amazon RDS instances, máy chủ ứng dụng nội bộ). Những subnet này không có tuyến đường trực tiếp ra internet, cung cấp một lớp bảo mật thiết yếu nhằm chống lại các truy cập trái phép từ bên ngoài.

![Ví dụ về Subnets và VPC](/images/1-Worklog/1.2-Week2/2.png)

---

### Gateways và Định tuyến lưu lượng

Để các tài nguyên giao tiếp được với thế giới bên ngoài hoặc với nhau, cần phải thiết lập các gateway và cấu hình định tuyến cụ thể.

*   **Internet Gateway (IGW):** Một thành phần có khả năng mở rộng ngang, dự phòng cao được gắn vào VPC. Nó cho phép giao tiếp hai chiều giữa các tài nguyên trong public subnet và mạng internet công cộng.
*   **NAT Gateway (Network Address Translation):** Được đặt bên trong một public subnet, NAT Gateway cho phép các instance trong private subnet khởi tạo lưu lượng truy cập ra ngoài internet (ví dụ: để cập nhật phần mềm hoặc gọi API) trong khi chặn lưu lượng truy cập từ internet đi vào.
*   **Route Tables (Bảng định tuyến):** Đóng vai trò như những "bộ định tuyến ảo" của VPC. Một route table chứa một tập hợp các quy tắc (routes) xác định nơi lưu lượng mạng từ subnet hoặc gateway được hướng đến. 
    *   Một public route table sẽ bao gồm một route trỏ `0.0.0.0/0` (tất cả lưu lượng internet) đến Internet Gateway.
    *   Một private route table sẽ hướng lưu lượng internet ra ngoài (`0.0.0.0/0`) đến NAT Gateway thay thế.

### Thực hành thiết lập VPC, Subnet, Route tables, và IGW

![Mô tả cho ảnh](/images/1-Worklog/1.2-Week2/4.png)
---


### Bảo mật Mạng: NACLs vs. Security Groups

AWS cung cấp hai lớp bảo vệ tường lửa chính để bảo mật cơ sở hạ tầng VPC. Hiểu rõ sự khác biệt giữa chúng là điều cốt lõi đối với bảo mật mạng.

*   **Security Groups (SGs - Nhóm bảo mật):** 
    *   Hoạt động ở cấp độ instance (gắn trực tiếp vào Elastic Network Interface).
    *   Hoạt động như tường lửa **stateful (có trạng thái)**. Điều này có nghĩa là nếu một yêu cầu gửi đến (inbound) được cho phép, lưu lượng phản hồi (return traffic) sẽ tự động được chấp thuận, bất kể các quy tắc gửi đi (outbound) là gì.
    *   Chỉ hỗ trợ các quy tắc "Allow" (Cho phép). Tất cả các lưu lượng khác đều bị từ chối ngầm.
*   **Network Access Control Lists (NACLs):** 
    *   Hoạt động ở cấp độ subnet.
    *   Hoạt động như tường lửa **stateless (không trạng thái)**. Cả lưu lượng vào và ra đều phải được cho phép một cách rõ ràng.
    *   Hỗ trợ cả quy tắc "Allow" (Cho phép) và "Explicit Deny" (Từ chối tường minh).
    *   Các quy tắc được xử lý theo thứ tự số học; quá trình đánh giá sẽ dừng lại ngay khi tìm thấy một quy tắc khớp.

Do quy mô của chương trình thực tập, mình quyết định không đề xuất NACLs và Security Groups vào dự án vì nó làm khối lượng dự án phình to ra rất nhiều.

---

### Các công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1-2 | Tìm hiểu sâu về các khái niệm IAM | 2026-06-15 | 2026-06-16 | Tài liệu AWS IAM |
| 3 | Học về Kiến trúc VPC & Subnet | 2026-06-17 | 2026-06-17 | Hướng dẫn Mạng AWS |
| 4 | Cấu hình IGW, NAT, và Route Tables | 2026-06-18 | 2026-06-18 | Lớp Masterclass về AWS VPC |
| 5 | Triển khai SGs và NACLs | 2026-06-19 | 2026-06-19 | Hướng dẫn Bảo mật AWS |

### Thành quả Tuần 2:

*   Nắm bắt các khái niệm cốt lõi về cấp phát địa chỉ IP và tính toán CIDR block.
*   Vạch ra thành công kiến trúc VPC tùy chỉnh trải dài trên nhiều Availability Zones.
*   Phân biệt được vai trò của Public và Private subnets trong việc bảo mật các ứng dụng multi-tier (đa tầng).
*   Hiểu cách định tuyến lưu lượng an toàn sử dụng Internet Gateways, NAT Gateways, và Route Tables.
*   Nắm vững sự khác biệt giữa Security Groups (stateful) và Network ACLs (stateless).

### Buổi họp nhóm — 20/06/2026

**Người tham dự:** Hiếu, Danh, Thanh (Vắng mặt: Nguyên, Hùng)

**Quyết định Kiến trúc**

Hiếu trình bày đề xuất kiến trúc AWS lai (hybrid) cho ứng dụng đặt sân. Cả nhóm đã thống nhất cách phân chia 3 tính năng cốt lõi như sau:

| Tính năng                                                   | Triển khai                        |
| ----------------------------------------------------------- | --------------------------------- |
| Xác thực người dùng (Đăng ký / Đăng nhập)                   | Monolith (EC2)                    |
| Quản lý đặt sân (Tạo / Sửa / Hủy / Xem / Tìm kiếm)          | Monolith (EC2)                    |
| Xử lý thanh toán                                            | Serverless (Lambda + API Gateway) |

**Phân công công việc**

| Hạng mục / Nhóm               | Phụ trách | Sản phẩm bàn giao (Deliverable)                                                                                                              |
| ----------------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Tất cả thành viên             | Mọi người | Đánh giá và đưa ra feedback về đề xuất kiến trúc.                                                                                            |
| Frontend                      | Nhóm FE   | Nghiên cứu thị trường các app tương tự; Thiết kế UX/UI có AI hỗ trợ → design system (bảng màu, typography, icon set) và danh sách màn hình.  |
| Backend — Quản lý đặt sân     | Thanh     | Tài liệu API (tên endpoint, input, output, use case) và thiết kế cơ sở dữ liệu (tables, columns, data types).                                |
| Backend — Xác thực người dùng | Nguyên    | Tài liệu API và thiết kế cơ sở dữ liệu cho tính năng xác thực.                                                                               |
| Backend — Thanh toán          | Hiếu      | Tài liệu API và thiết kế cơ sở dữ liệu cho tính năng thanh toán.                                                                             |
| Quản trị AWS                  | Admin AWS | Thiết lập AWS Organization, các tài khoản, IAM users, roles, và policies.                                                                    |

**Các bước tiếp theo**

- Nhóm FE hoàn thành nghiên cứu thị trường và tạo ra design system ban đầu vào cuối Tuần 2.
- Các thành viên BE hoàn thành tài liệu API và các bản nháp schema DB (Cơ sở dữ liệu) vào cuối Tuần 2.
- Admin AWS chuẩn bị sẵn cấu trúc tài khoản cơ bản trước khi công việc hạ tầng bắt đầu vào Tuần 3.

---

### Thuật ngữ (Glossary)

| Viết tắt | Ý nghĩa |
| --- | --- |
| AI | Trí tuệ Nhân tạo (Artificial Intelligence) |
| API | Giao diện Lập trình Ứng dụng — cầu nối giúp các phần mềm giao tiếp với nhau |
| AWS | Amazon Web Services — Nền tảng điện toán đám mây của Amazon |
| BE | Backend — Phần xử lý phía máy chủ của ứng dụng |
| CLF-C02 | Mã bài thi của chứng chỉ AWS Certified Cloud Practitioner |
| DB | Cơ sở dữ liệu (Database) |
| DVA-C02 | Mã bài thi của chứng chỉ AWS Certified Developer – Associate |
| EC2 | Amazon Elastic Compute Cloud — Máy chủ ảo trên AWS |
| ERD | Sơ đồ Quan hệ Thực thể — Mô hình trực quan của các bảng trong CSDL và mối quan hệ giữa chúng |
| FK | Khóa ngoại (Foreign Key) — Cột tham chiếu đến khóa chính của một bảng khác |
| IAM | AWS Identity and Access Management — Quản lý người dùng, vai trò, và quyền truy cập |
| JSONB | JSON Binary — Kiểu cột JSON định dạng nhị phân của PostgreSQL |
| JWT | JSON Web Token — Token được mã hóa chứa các thông tin xác thực |
| PK | Khóa chính (Primary Key) — Cột định danh duy nhất cho mỗi hàng |
| RDS | Amazon Relational Database Service — Dịch vụ quản lý cơ sở dữ liệu SQL |
| S3 | Amazon Simple Storage Service — Dịch vụ lưu trữ đối tượng (object storage) |
| SNS | Amazon Simple Notification Service — Dịch vụ tin nhắn pub/sub và thông báo |
| SQS | Amazon Simple Queue Service — Dịch vụ hàng đợi tin nhắn được quản lý |
| UI | Giao diện Người dùng (User Interface) |
| UK | Khóa duy nhất (Unique Key) — Cột hoặc các cột mà giá trị bên trong không được trùng lặp |
| URL | Uniform Resource Locator — Địa chỉ web |
| UUID | Universally Unique Identifier — Định danh duy nhất toàn cầu |
| VND | Đồng Việt Nam (Mã tiền tệ ISO) |
| VPC | Amazon Virtual Private Cloud — Một mạng ảo bị cách ly trên AWS |