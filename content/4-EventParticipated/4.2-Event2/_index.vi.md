---
title: "Sự kiện 2"
date: 2026-06-27
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

![Mô tả cho ảnh](/images/4-Event/2.png)



### Thông tin Sự kiện

| Trường | Chi tiết |
|-------|---------|
| **Tên sự kiện** | AWS GameDay - 8 đội tranh tài trực tiếp về kiến thức AWS |
| **Ngày & Giờ** | 09:00, 20/06/2026 |
| **Địa điểm** | Trực tiếp (On-site) |
| **Vai trò** | Người tham dự (Attendee) |

---


### Mô tả Sự kiện

Sự kiện AWS GameDay đã quy tụ tám đội tham gia vào một cuộc tranh tài trực tiếp, căng thẳng nhằm kiểm tra kiến thức toàn diện của chúng mình về Amazon Web Services. Thể thức thi đấu đã thúc đẩy những người tham gia vượt ra khỏi lý thuyết chứng chỉ cơ bản bằng cách đưa ra các thử thách kiến trúc thực tế, phức tạp và các tình huống khắc phục sự cố (troubleshooting) trên nhiều lĩnh vực khác nhau.

#### 1. Các thử thách Kiến trúc Đa lĩnh vực (Cross-Domain)
Cuộc thi có một loạt các câu hỏi đa dạng bao trùm các trụ cột cốt lõi của AWS. Các đội phải phân tích và giải quyết các vấn đề liên quan đến:
*   **Tính toán & Container (Compute & Containers):** Quyết định lựa chọn giữa EC2, ECS (Fargate), và EKS dựa trên các yêu cầu mở rộng quy mô cụ thể và các ràng buộc về chi phí.
*   **Mạng & Bảo mật (Networking & Security):** Khắc phục sự cố VPC peering, bảng định tuyến (routing tables), và cấu hình các chính sách Security Group và IAM nghiêm ngặt để bảo mật dữ liệu đang truyền tải (data in transit).
*   **Cơ sở dữ liệu & Lưu trữ (Databases & Storage):** Lựa chọn dịch vụ được quản lý phù hợp (RDS vs. DynamoDB vs. ElastiCache) cho các mẫu khối lượng công việc khác nhau và các thiết lập tính sẵn sàng cao.
*   **Serverless (Không máy chủ):** Thiết kế các kiến trúc hướng sự kiện sử dụng API Gateway, Lambda, và SQS/SNS cho các microservices được phân tách (decoupled).

#### 2. Thực thi Tình huống Thực tế (Real-World Scenario Execution)
Thay vì các câu hỏi trắc nghiệm, sự kiện tập trung vào ứng dụng thực tế. Các đội được đánh giá dựa trên khả năng nhanh chóng xác định các điểm nghẽn (bottlenecks), di chuyển (migrate) các hệ thống cũ và áp dụng các nguyên tắc của Khung Kiến trúc Chuẩn AWS (AWS Well-Architected Framework: Bảo mật, Độ tin cậy, Hiệu suất hoạt động, Tối ưu hóa chi phí) dưới một giới hạn thời gian nghiêm ngặt.

---

### Kết quả và Giá trị thu được

- **Mở rộng Tầm nhìn Kỹ thuật:** Bản chất hỏi đáp nhanh của cuộc thi đã buộc mình phải nhớ lại và áp dụng kiến thức trên nhiều lĩnh vực AWS cùng một lúc. Cách tiếp cận toàn diện này đã làm sâu sắc thêm đáng kể sự hiểu biết của mình về cách các dịch vụ khác nhau tương tác trong một hệ sinh thái hoàn chỉnh, thay vì chỉ xem xét chúng một cách riêng lẻ.

- **Giải quyết Vấn đề Thực tế:** Việc đối mặt với các câu hỏi thực hành, dựa trên tình huống là vô cùng giá trị. Nó làm nổi bật khoảng cách giữa việc biết một dịch vụ làm gì và biết *khi nào* cũng như *cách nào* để triển khai nó hiệu quả nhằm giải quyết một vấn đề kinh doanh cụ thể.

- **Xác định Lỗ hổng Kiến thức:** Cuộc thi đóng vai trò như một công cụ chẩn đoán tuyệt vời, giúp mình xác định chính xác các lĩnh vực cụ thể (như mạng nâng cao hoặc các kỹ thuật tối ưu hóa cơ sở dữ liệu) nơi mình cần tập trung nỗ lực học tập trong tương lai.

- **Góp ý Xây dựng về Thể thức Sự kiện:** Mặc dù khía cạnh cạnh tranh rất hấp dẫn và mang tính giáo dục, mình cảm thấy sự kiện còn thiếu sót trong giai đoạn đánh giá (review). Các mentor đã đưa ra câu trả lời đúng nhưng không dành đủ thời gian để giải thích *lý do (rationale)* đằng sau những lựa chọn đó. Việc đi sâu hơn vào lý do tại sao một kiến trúc cụ thể được chọn, những ưu điểm của nó, và sự đánh đổi (trade-offs) của các giải pháp thay thế sẽ giúp khuếch đại trải nghiệm học tập lên rất nhiều.