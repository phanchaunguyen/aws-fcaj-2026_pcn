---
title: "Báo cáo công việc Tuần 8"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Lộ trình học AWS được đề xuất từ roadmap.sh
Vì hệ sinh thái AWS quá rộng lớn để có thể bao quát toàn bộ, mình quyết định làm theo lộ trình từ trang roadmap.sh để định hướng con đường học tập. Từ giờ trở đi, mình sẽ cập nhật tiến độ của mình tại đây. 

[Theo dõi hành trình học tập của mình tại đây](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)

### Mục tiêu Tuần 8:
*   Phối hợp với nhóm để đẩy nhanh tiến độ của dự án FCAJ.
*   Đề xuất một sơ đồ kiến trúc mạnh mẽ hơn, cấu hình các region, vpc, subnets, IGW, v.v...
*   Trau chuốt lại trang web báo cáo FCAJ, thêm video, hình ảnh và sơ đồ.

### 1. Đồng bộ hóa Nhóm & Thống nhất Kiến trúc

Tuần này đánh dấu một bước chuyển mình lớn khi chúng mình kết hợp các kinh nghiệm triển khai AWS cá nhân vào việc thực thi dự án nhóm: **Hệ thống Đặt sân (Court Booking System)**. 

Vào **ngày 28 tháng 7**, nhóm đã tổ chức một cuộc họp đồng bộ quan trọng để chốt lại kiến trúc hệ thống và giải quyết các lỗi tích hợp ban đầu. Các kết quả chính từ cuộc họp bao gồm:

*   **Thống nhất Kiến trúc:** Bọn mình đã đồng ý về sự phân tách các mối quan tâm (separation of concerns) một cách nghiêm ngặt. Frontend (SPA) sẽ không tương tác trực tiếp với các dịch vụ được quản lý của AWS. Thay vào đó, Backend sẽ đóng vai trò như một API Gateway tập trung, bao bọc lại tất cả các lệnh gọi dịch vụ AWS (bao gồm cả các hoạt động Xác thực và Cơ sở dữ liệu).
*   **Sửa lỗi & Đồng nhất Môi trường:** Nhóm đã giải quyết một số lỗi liên quan đến việc môi trường phát triển local không khớp với hạ tầng cloud. Bọn mình đã thiết lập một quy ước biến `APP_ENV` nghiêm ngặt (`local` so với `dev`) để đảm bảo quá trình chuyển đổi mượt mà giữa các dịch vụ mock ở local và các tài nguyên AWS thật.

---

### 2. Thiết lập Xác thực (Cognito)

Amazon Cognito là **cơ quan có thẩm quyền về xác thực (auth authority)** của dự án: nó lưu trữ người dùng, xác minh mật khẩu và phát hành JWT. Một quyết định thiết kế quan trọng được thống nhất trong cuộc họp ngày 28 tháng 7 định hình mọi thứ ở đây:

{{% notice note %}}
**Frontend không bao giờ giao tiếp với Cognito.** Không có SDK Amplify Auth nào trong SPA. Backend sẽ *bọc* Cognito lại (`app/cognito.py`): FE gọi `POST /api/v1/auth/login` tới API của bọn mình; BE sẽ gọi Cognito, trả về các token; FE chỉ việc lưu trữ chúng và gửi kèm `Authorization: Bearer <token>` trên mỗi request. Kết quả: không có bất kỳ cấu hình Cognito nào ở frontend, và luồng xác thực có thể được tráo đổi hoặc mock ở phía server-side.
{{% /notice %}}

#### 2.1. User Pool, App Client, và Groups

Ba tài nguyên đã được tạo thông qua AWS CLI để thiết lập nền tảng xác thực:

~~~bash
# 1. Pool — email là username, tự động xác minh
POOL_ID=$(aws cognito-idp create-user-pool --pool-name court-booking \
  --username-attributes email --auto-verified-attributes email \
  --query 'UserPool.Id' --output text --profile thanh)

# 2. Một app client CÔNG KHAI (PUBLIC) cho SPA — không có secret (trình duyệt không thể giữ bí mật)
CLIENT_ID=$(aws cognito-idp create-user-pool-client --user-pool-id $POOL_ID \
  --client-name court-booking-spa --no-generate-secret \
  --explicit-auth-flows ALLOW_USER_PASSWORD_AUTH ALLOW_REFRESH_TOKEN_AUTH ALLOW_USER_SRP_AUTH \
  --query 'UserPoolClient.ClientId' --output text --profile thanh)

# 3. Groups = các vai trò (roles) trong ứng dụng
for g in player court_manager admin; do
  aws cognito-idp create-group --user-pool-id $POOL_ID --group-name "$g" --profile thanh
done
~~~

Ba group này phản ánh chính xác mô hình role: group của một người dùng sẽ xuất hiện trong JWT của họ dưới dạng claim `cognito:groups`; backend sẽ cache nó vào `users.role`, mà các dependencies như `require_role("admin")` / `require_manager` sẽ kiểm tra trên mọi endpoint được bảo vệ. Việc thay đổi role của một người dùng trên môi trường production đồng nghĩa với việc thay đổi Cognito group của họ trước, sau đó mới đến DB cache — Cognito vẫn là nguồn sự thật (source of truth).

---

### 3. Kết nối Cognito với Backend

Để đảm bảo việc triển khai diễn ra suôn sẻ và tránh việc hardcode các ID nhạy cảm, các ID của pool và client được đưa vào AWS Systems Manager (SSM) Parameter Store và tiếp cận ứng dụng thông qua cầu nối biến môi trường:

~~~bash
aws ssm put-parameter --name /court-booking/dev/COGNITO_USER_POOL_ID --type String --value "$POOL_ID"   --overwrite --profile thanh
aws ssm put-parameter --name /court-booking/dev/COGNITO_CLIENT_ID    --type String --value "$CLIENT_ID" --overwrite --profile thanh
~~~

**Thực thi dựa trên Môi trường:**
Việc chuyển đổi diễn ra tự động. Trong khi `APP_ENV=local`, script `auth.py` sử dụng một chế độ dự phòng ở local (local-mode fallback) (một Bearer token được khớp trực tiếp với các giá trị `cognito_sub` đã được seed — đây là thứ cho phép bản deploy walking-skeleton hoạt động trước khi Cognito tồn tại). Một khi `APP_ENV=dev` và hai ID kia có mặt trong SSM, `cognito.py` sẽ gọi pool thật. Thao tác này sử dụng chính xác các endpoint và code frontend như cũ, nhưng phát hành JWT thật.

{{% notice tip %}}
**Phạm Phạm vi Tương lai:** Google OAuth (`POST /auth/oauth`) bổ sung yêu cầu một domain hosted-UI của Cognito + nhà cung cấp danh tính (identity provider). Xác thực bằng email/mật khẩu thì không cần những thứ đó, vì vậy đăng nhập bằng mạng xã hội được coi là một bước độc lập, thực hiện sau và được hoãn lại cho các sprint sắp tới.
{{% /notice %}}


### Buổi họp nhóm — 28/07/2026

**Người tham dự:** Hiếu, Thanh, Nguyên, Danh, Hùng
**Vắng mặt:** Không

**Trình bày & Thảo luận**

- **Hiếu & Thanh** trình bày **Sơ đồ Kiến trúc (Architecture Diagram)** đã được chốt, đảm bảo sự đồng thuận của nhóm về luồng mạng từ người dùng cuối đi qua CDN toàn cầu xuống các subnet riêng tư của cơ sở dữ liệu.
- **Danh** dẫn dắt một phiên làm việc về **Chia lại VPC & CIDR Block** để tránh cạn kiệt IP và xung đột định tuyến, thiết lập một ranh giới nghiêm ngặt giữa các tài nguyên công cộng (public) và riêng tư (private).
- **Cả nhóm** đã tiến hành kiểm toán toàn diện các cấu hình **Security Group (SG)** để đảm bảo sự giao tiếp liên dịch vụ an toàn và sửa các lỗi timeout do tích hợp sớm.
- **Hiếu** demo CI pipeline (ruff hỗ trợ Postgres + pytest trên mọi PR), trình bày về thiết lập danh tính chéo tài khoản (cross-account identity) (MFA AssumeRole + OIDC deploy roles), và hiển thị bề mặt API backend đã hoàn thiện với contract `openapi.json` được generate lại.
- **Thanh** và **Nguyên** review các endpoint thuộc domain của họ đã được triển khai, xác nhận contract khớp với thiết kế ở mục §6.5 / §6.6.
- **Danh & Hùng** trình diễn việc frontend consume (tiêu thụ) các endpoint thật đằng sau công tắc mock/real, với khả năng generate type từ `openapi.json`.

**Tóm tắt đánh giá về các điều chỉnh Kiến trúc & Mạng**

1. **Đồng thuận Kiến trúc**: Tái khẳng định việc phân tách nghiêm ngặt các mối quan tâm. Frontend (SPA) sẽ không tương tác trực tiếp với các dịch vụ được quản lý nội bộ của AWS. Backend (ECS Fargate) đóng vai trò là API Gateway tập trung, được bọc sau một Application Load Balancer (ALB).
2. **Chiến lược VPC CIDR**: Đã đồng ý về VPC CIDR cơ sở là `10.0.0.0/16` để đảm bảo lượng IP dồi dào.
   - **Public Subnets** (`10.0.1.0/24`, `10.0.2.0/24`) được dành riêng hoàn toàn cho NAT Gateways và ALB hướng ra internet (internet-facing).
   - **Private Subnets** (`10.0.10.0/24`, `10.0.11.0/24`) được dành riêng cho các ECS task của Backend và instance RDS để đảm bảo sự cách ly tuyệt đối khỏi truy cập internet trực tiếp.
3. **Chuỗi Security Group (SG Chaining)**: Đã giải quyết các khối chặn giao tiếp liên ứng dụng bằng cách thực thi việc tham chiếu SG một cách nghiêm ngặt thay vì hardcode các dải IP:
   - **ALB-SG**: Cho phép Inbound HTTP/HTTPS (Port `80`/`443`) từ `0.0.0.0/0`.
   - **ECS-Backend-SG**: Cho phép Inbound TCP Port `8080` *dành riêng (exclusively)* từ `ALB-SG`.
   - **RDS-DB-SG**: Cho phép Inbound PostgreSQL Port `5432` *dành riêng (exclusively)* từ `ECS-Backend-SG`.
4. **Đồng nhất Môi trường**: Đã giải quyết các bug khi cài đặt local không phản ánh đúng networking trên cloud. Bọn mình đã thiết lập một quy ước biến `APP_ENV` nghiêm ngặt (`local` so với `dev`) để backend có thể chuyển đổi chuỗi kết nối mượt mà giữa các dịch vụ mock ở local và các AWS VPC endpoint thật.


**Quyết định & Phân công khối lượng công việc**

| # | Hành động | Phụ trách | Ghi chú |
| - | ------ | ----- | ----- |
| 1 | Nắm mảng **hạ tầng AWS + triển khai** vào tuần tới (mạng, RDS, Cognito, SSM, EC2 + CI/CD deploy) | Hiếu | Ưu tiên hàng đầu — app đã hoàn thiện về mặt code; chỗ thiếu hụt hiện tại là hạ tầng |
| 2 | Review + tinh chỉnh router **Hoạt động Admin (Admin Operations)** và endpoint **ưu tiên Cognito cho role (Cognito-first role)** bám theo mục §6.6; nắm phần tách **payment-Lambda** khi bắt đầu làm mảng serverless | Nguyên | Đây là domain về auth/admin của bạn ấy |
| 3 | Review + củng cố các endpoint **đặt sân (booking) + doanh thu (revenue)**; xác thực (validate) constraint loại trừ việc đặt trùng sân trong điều kiện đồng thời (concurrency) | Thanh | Đây là domain booking của bạn ấy |
| 4 | Xây dựng các màn hình UI cho **quản lý (manager)/admin/hồ sơ (profile)** dựa trên các endpoint thực; chuẩn bị Amplify hosting | Danh & Hùng | Domain của FE |