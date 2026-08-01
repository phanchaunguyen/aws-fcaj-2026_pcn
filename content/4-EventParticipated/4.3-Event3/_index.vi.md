---
title: "Sự kiện 3"
date: 2026-06-27
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

![Mô tả cho ảnh](/images/4-Event/3.png)



### Thông tin Sự kiện

| Trường | Chi tiết |
|-------|---------|
| **Tên sự kiện** | Hackathon AWS AI Builder - Trình bày Dự án |
| **Ngày & Giờ** | 09:00, 25/07/2026 |
| **Địa điểm** | Trực tiếp (On-site) |
| **Vai trò** | Người tham dự (Attendee) |

---

### Mô tả Sự kiện

Sự kiện trình bày dự án thuộc Hackathon AWS AI Builder đã cung cấp một nền tảng để các đội thi thể hiện những dự án họ đã xây dựng trong suốt cuộc thi. Mặc dù chủ đề cốt lõi xoay quanh rất nhiều về Trí tuệ Nhân tạo (AI) và các mô hình Học máy (Machine Learning), các phiên trình bày vẫn mang lại những cái nhìn sâu sắc về kiến trúc đám mây và thiết kế hệ thống.

#### 1. Hạ tầng có Khả năng mở rộng cho các Khối lượng công việc AI (AI Workloads)
Ngay cả khi không có chuyên môn sâu về lĩnh vực AI, các bài thuyết trình vẫn mang tính giáo dục rất cao từ góc độ kỹ thuật hệ thống (systems engineering). Các đội đã trình bày chi tiết về quá trình ra quyết định kiến trúc của họ, giải thích cách họ thiết kế các cơ sở hạ tầng có khả năng mở rộng cao trên AWS để hỗ trợ các pipeline AI tiêu tốn nhiều tài nguyên, quản lý lượng dữ liệu khổng lồ và đảm bảo xử lý với độ trễ thấp.

#### 2. Dự án Nổi bật: Pipeline Tự động Phát hiện Gian lận & Chống Rửa tiền (AML)
Có một dự án nổi bật vô cùng xuất sắc: một pipeline tự động hóa được điều khiển bởi AI, thiết kế để chống lại gian lận tài chính và rửa tiền. Thay vì chỉ gắn cờ một giao dịch là "đáng ngờ", hệ thống đã tận dụng AI để tự động săn lùng, trích xuất và tổng hợp toàn bộ các bằng chứng kỹ thuật số liên quan (lịch sử giao dịch, nhật ký IP, các mẫu hành vi) vào một dashboard tập trung. Điều này cho phép các điều tra viên con người có thể đánh giá một hồ sơ vụ việc hoàn chỉnh đã được lắp ráp sẵn. Mặc dù lĩnh vực gian lận fintech nói chung khá quen thuộc, nhưng việc giải quyết nút thắt (bottleneck) tốn nhiều công sức cụ thể là *thu thập bằng chứng thủ công* lại là một hướng đi rất ngách và hiếm khi được nhắc tới.

---

### Kết quả và Giá trị thu được

- **Kiến trúc Vượt lên trên Kiến thức Ngành (Domain Knowledge):** Mình nhận ra rằng một người không cần phải là chuyên gia AI/ML mới có thể thiết kế hoặc hiểu được giá trị của các hệ thống vận hành chúng. Việc học cách các đội này cấu trúc hạ tầng đám mây của họ (sử dụng các dịch vụ như AWS Step Functions cho các pipelines, S3 cho data lakes và các dịch vụ compute được quản lý cho quá trình suy luận - inference) đã mở rộng đáng kể sự hiểu biết của mình về thiết kế hệ thống có khả năng mở rộng.

- **Mô hình AI có Sự tham gia của con người (Human-in-the-Loop):** Dự án phát hiện gian lận là một lớp học bậc thầy về ứng dụng AI thực tế. Nó chứng minh rằng giá trị cao nhất của AI thường không nằm ở việc đưa ra quyết định cuối cùng, mà là ở việc làm những công việc nặng nhọc như tổng hợp dữ liệu để tăng tốc và trao quyền cho việc ra quyết định của con người.

- **Xác định các Điểm nghẽn Vận hành ngách:** Dự án AML đã dạy cho mình tầm quan trọng của việc nhìn vượt ra ngoài các vấn đề ở bề nổi. Trong khi nhiều công cụ chỉ đơn thuần là phát hiện gian lận, nhóm này đã xác định được nỗi đau (pain point) thực sự của các điều tra viên chính là thời gian bỏ ra để thu thập bằng chứng thủ công. Điều này đã thay đổi góc nhìn của mình về cách định hình phạm vi (scope) cho các dự án trong tương lai — tập trung vào các nỗi đau vận hành cụ thể, mang tính ngách thay vì các khái niệm quá rộng.

- **Cảm hứng cho các Dự án Tương lai:** Việc chứng kiến các data pipelines tự động hóa và phức tạp được thực thi hiệu quả dưới áp lực thời gian của một cuộc thi hackathon đã truyền cảm hứng cho mình trong việc tích hợp nhiều hơn các điều phối luồng công việc tự động (automated workflow orchestrations) vào các dự án backend và data engineering của riêng mình.