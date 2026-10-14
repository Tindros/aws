---
title : "Tạo Cognito User Pool & App Client"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.3.1 </b> "
---

Trong phần này, bạn sẽ từng bước tạo và cấu hình **Amazon Cognito User Pool** cùng **App Client** để phục vụ việc xác thực từ ứng dụng web frontend.

---

#### Bước 1: Mở Cognito Console & Bắt đầu Khởi tạo

1. Đăng nhập vào AWS Management Console và truy cập [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
2. Kiểm tra thanh định hướng góc trên bên phải để chắc chắn bạn đang ở Region **US East (N. Virginia) `us-east-1`**.
3. Tại menu bên trái, chọn **User pools**, sau đó nhấn **Create user pool**.

---

#### Bước 2: Cấu hình Trải nghiệm Đăng nhập (Sign-in Experience)

1. Tại mục **Authentication providers**, chọn **Cognito user pool**.
2. Tại mục **Cognito user pool sign-in options**, đánh dấu chọn **Email**:
   + Người dùng sẽ dùng địa chỉ email làm tên đăng nhập duy nhất khi đăng ký và đăng nhập vào ứng dụng.
   + Bỏ chọn các ô *User name* và *Phone number*.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-01-sign-in-experience.png`  
*(Cần chụp khi triển khai: Giao diện Cognito bước 1 với tùy chọn Email được chọn)*
{{% /notice %}}
<!-- ![Cấu hình Sign-in Experience](/images/5-Workshop/5.3-Cognito/cognito-01-sign-in-experience.png) -->

3. Nhấn **Next**.

---

#### Bước 3: Cấu hình Yêu cầu Bảo mật (Security Requirements)

1. **Chính sách mật khẩu (Password policy):** Chọn **Cognito defaults**:
   + Độ dài mật khẩu tối thiểu: `8` ký tự.
   + Yêu cầu có ít nhất 1 chữ hoa, 1 chữ thường, 1 chữ số và 1 ký tự đặc biệt.
2. **Xác thực đa yếu tố (MFA):** Chọn **No MFA**.
   + *Lưu ý:* Mặc dù MFA rất cần thiết cho môi trường thực tế, việc chọn No MFA giúp đơn giản hóa quy trình và tránh phát sinh chi phí SMS trong phạm vi bài thực hành.
3. **Khôi phục tài khoản (User account recovery):** Giữ chọn **Self-service account recovery** qua phương thức **Email**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-02-security-requirements.png`  
*(Cần chụp khi triển khai: Thiết lập mật khẩu mặc định và MFA ở chế độ No MFA)*
{{% /notice %}}
<!-- ![Cấu hình Security Requirements](/images/5-Workshop/5.3-Cognito/cognito-02-security-requirements.png) -->

4. Nhấn **Next**.

---

#### Bước 4: Cấu hình Trải nghiệm Đăng ký (Sign-up Experience)

1. Tại mục **Self-service sign-up**, đảm bảo tùy chọn **Enable self-registration** được kích hoạt để người dùng có thể tự đăng ký từ giao diện frontend.
2. Tại mục **Cognito-assisted verification and confirmation**, chọn **Send email message, verify email address**.
3. Tại mục **Required attributes**, kiểm tra thuộc tính **email** đã được đặt làm bắt buộc.
4. Nhấn **Next**.

---

#### Bước 5: Cấu hình Gửi Thông báo (Message Delivery)

1. Tại mục **Email provider**, chọn **Send email with Cognito**.
   + Tùy chọn này sử dụng dịch vụ email mặc định của Cognito, cung cấp hạn ngạch gửi thư miễn phí hằng ngày vừa đủ cho mục đích học tập mà không cần cấu hình xác thực tên miền trên Amazon SES.
2. Nhấn **Next**.

---

#### Bước 6: Tích hợp Ứng dụng & Cấu hình App Client

1. **User pool name:** Nhập `expense-tracker-user-pool`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-03-user-pool-name.png`  
*(Cần chụp khi triển khai: Đặt tên User Pool là expense-tracker-user-pool)*
{{% /notice %}}
<!-- ![Đặt tên User Pool](/images/5-Workshop/5.3-Cognito/cognito-03-user-pool-name.png) -->

2. Tại mục **Initial app client**:
   + **App client name:** Nhập `expense-tracker-web-client`.
   + **Client secret:** Chọn **Don't generate a client secret**.

{{% notice warning %}}
**Nguyên tắc Kiến trúc Quan trọng:** Tuyệt đối không tạo Client Secret cho ứng dụng Single Page Application (SPA) chạy trực tiếp trên trình duyệt. Mã chạy trên trình duyệt người dùng không thể bảo mật mã bí mật này. Việc không tạo Client Secret cho phép React app tương tác trực tiếp với Cognito qua các luồng xác thực công khai an toàn.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-04-app-client.png`  
*(Cần chụp khi triển khai: Cấu hình App Client chọn "Don't generate a client secret")*
{{% /notice %}}
<!-- ![Cấu hình App Client](/images/5-Workshop/5.3-Cognito/cognito-04-app-client.png) -->

3. Mở rộng phần **Advanced app client settings**:
   + Đảm bảo danh sách **Authentication flows** có các tùy chọn:
     + `ALLOW_USER_SRP_AUTH`
     + `ALLOW_REFRESH_TOKEN_AUTH`
     + `ALLOW_USER_PASSWORD_AUTH`
4. Nhấn **Next**.

---

#### Bước 7: Xem lại & Khởi tạo

1. Kiểm tra lại toàn bộ thông tin đã cấu hình qua các bước.
2. Nhấn **Create user pool**.
3. Sau khi khởi tạo thành công, bấm vào `expense-tracker-user-pool` để mở trang thông tin chi tiết.
4. Ghi lại các thông số sau để sử dụng cho các bước cấu hình API Gateway và Frontend tiếp theo:
   + **User pool ID:** (Ví dụ: `us-east-1_xxxxxxxxx`)
   + Chuyển sang thẻ **App integration**, cuộn xuống mục **App clients and analytics** và sao chép **Client ID** (chuỗi 26 ký tự chữ và số).

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-05-user-pool-overview.png`  
*(Cần chụp khi triển khai: Trang tổng quan User pool hiển thị User pool ID và App client ID)*
{{% /notice %}}
<!-- ![Tổng quan User Pool](/images/5-Workshop/5.3-Cognito/cognito-05-user-pool-overview.png) -->
