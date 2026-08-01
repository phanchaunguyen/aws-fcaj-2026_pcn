---
title: "Sự kiện 1"
date: 2026-06-27
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---


![Mô tả cho ảnh](/images/4-Event/1.png)


### Thông tin Sự kiện

| Trường | Chi tiết |
|-------|---------|
| **Tên sự kiện** | Rút gọn URL (URL Shortener) | Sự phù hợp văn hóa trong môi trường doanh nghiệp |
| **Ngày & Giờ** | 09:00, 13/06/2026 |
| **Địa điểm** | Trực tiếp (On-site) |
| **Vai trò** | Người tham dự (Attendee) |

---

#### 1. Xây dựng hệ thống Rút gọn URL có khả năng mở rộng trên AWS
*Diễn giả: Nhóm Kiến trúc Đám mây (Cloud Architecture Team)*

Phiên này khám phá các động lực về kinh doanh và kỹ thuật đằng sau việc xây dựng một trình rút gọn URL tùy chỉnh (tương tự như bit.ly). Diễn giả giải thích rằng mục đích chính của một trình rút gọn URL bao gồm việc quản lý liên kết, theo dõi phân tích lượt click, duy trì giới hạn ký tự trong SMS hoặc mạng xã hội, và nâng cao nhận diện thương hiệu với các domain tùy chỉnh. 

Để đạt được điều này trên Đám mây AWS, bài thuyết trình đã vạch ra một kiến trúc serverless (không máy chủ) có khả năng mở rộng cao:
*   **Amazon API Gateway:** Đóng vai trò là cửa trước (front door) để nhận các yêu cầu từ người dùng (ví dụ: click vào link rút gọn).
*   **AWS Lambda:** Chứa logic backend để tra cứu mã rút gọn và trả về lệnh điều hướng (redirect) HTTP 301/302 đến trình duyệt của người dùng.
*   **Amazon DynamoDB:** Đóng vai trò là cơ sở dữ liệu NoSQL nhanh, độ trễ thấp để lưu trữ ánh xạ key-value giữa mã rút gọn được tạo ra và URL gốc.
*   **Amazon Route 53:** Quản lý định tuyến DNS cho tên miền rút gọn tùy chỉnh.

#### 2. Vai trò then chốt của Sự phù hợp văn hóa (Culture Fit) trong các tập đoàn lớn
*Diễn giả: Lãnh đạo HR & Kỹ thuật*

Chuyển từ kỹ năng kỹ thuật sang phát triển sự nghiệp, bài thuyết trình này nhấn mạnh rằng chỉ riêng sự thành thạo kỹ thuật không còn đủ để thành công trong các tập đoàn lớn; "sự phù hợp văn hóa" (culture fit) cũng quan trọng tương đương, nếu không muốn nói là hơn. 

Diễn giả nhấn mạnh rằng một môi trường làm việc độc hại hoặc không phù hợp có thể nhanh chóng dẫn đến kiệt sức (burnout), bất kể mức lương hay tech stack (công nghệ) tốt đến đâu. Lời khuyên cốt lõi dành cho các ứng viên là hãy thực hiện thẩm định (due diligence) sâu sắc *trước khi* tham gia phỏng vấn. Các chiến lược được thảo luận bao gồm:
*   **Kết nối người trong cuộc (Insider Networking):** Chủ động liên hệ với các nhân viên hiện tại hoặc cựu nhân viên (ví dụ: qua LinkedIn) để hỏi những câu hỏi thẳng thắn về sự cân bằng công việc - cuộc sống, phong cách quản lý và áp lực hàng ngày.
*   **Các nền tảng đánh giá uy tín:** Nghiên cứu kỹ lưỡng công ty trên các trang web đáng tin cậy (như Glassdoor hoặc Blind) để xác định các "cờ đỏ" (red flags) lặp đi lặp lại hoặc các đặc điểm văn hóa tích cực.
*   **Phỏng vấn ngược (Reverse Interviewing):** Coi cuộc phỏng vấn là con đường hai chiều để đánh giá xem liệu các giá trị cốt lõi của công ty có thực sự phù hợp với phong cách làm việc của riêng mình hay không.

---

### Kết quả và Giá trị thu được

- **Nắm vững Kiến trúc Serverless:** Phân tích về trình rút gọn URL đã cung cấp một use case (trường hợp sử dụng) thực tế hoàn hảo cho bộ ba API Gateway + Lambda + DynamoDB. Pattern serverless này có tính ứng dụng rất cao cho các dự án của riêng mình, dạy mình cách xử lý lưu lượng đọc (read traffic) cao với chi phí thấp và quản lý hạ tầng ở mức tối thiểu.

- **Hiểu giá trị Kinh doanh trong Kỹ thuật Đám mây (Cloud Engineering):** Việc học được *lý do tại sao* các trình rút gọn URL lại tồn tại (phân tích, định vị thương hiệu, trải nghiệm người dùng) đã củng cố ý nghĩ rằng kiến trúc đám mây phải luôn phục vụ một mục đích kinh doanh rõ ràng, thay vì chỉ là bài tập thực hành ứng dụng các công nghệ ngầu.

- **Lập kế hoạch Sự nghiệp Chiến lược:** Buổi nói chuyện về sự phù hợp văn hóa đã tái cấu trúc hoàn toàn cách tiếp cận của mình trong việc tìm kiếm việc làm. Nó làm nổi bật sự cần thiết của việc nhìn xa hơn bản mô tả công việc (job description) và gói đãi ngộ để đánh giá môi trường làm việc thực tế.

- **Chuẩn bị Phỏng vấn Chủ động:** Giờ đây mình đã có một danh sách kiểm tra (checklist) rõ ràng, có thể hành động trước khi phỏng vấn với các doanh nghiệp lớn: mình phải chủ động tìm kiếm góc nhìn của "người trong cuộc" và đọc các đánh giá đã được xác minh để đảm bảo văn hóa công ty phù hợp với mục tiêu sự nghiệp dài hạn và sức khỏe tinh thần của mình.