---
title : "Đăng ký Người dùng, Đăng nhập & Xác minh JWT Token"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.3.2 </b> "
---

Trước khi thực hiện tích hợp mã nguồn frontend hoặc thiết lập các route trên API Gateway, chúng ta cần xác minh rằng Cognito User Pool đã hoạt động chính xác và phát hành các JSON Web Token (JWT) hợp lệ.

---

#### Bước 1: Tạo Người dùng Thử nghiệm trên Console

Bạn có thể chủ động tạo một tài khoản người dùng trực tiếp từ AWS Console mà không cần trải qua bước xác thực mã gửi qua email:

1. Tại [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1), chọn `expense-tracker-user-pool`.
2. Chuyển sang thẻ **Users**, sau đó chọn **Create user**.
3. Điền các thông tin trong hộp thoại:
   + Chọn **Don't send an email invite** (để thử nghiệm nhanh cục bộ).
   + **Email address:** Nhập `demo-user@example.com`.
   + Đánh dấu tích vào **Mark email address as verified**.
   + Tại mục **Temporary password**, chọn **Set a permanent password** và nhập mật khẩu thỏa mãn chính sách (ví dụ: `ExpenseTracker2026!`).

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-06-create-test-user.png`  
*(Cần chụp khi triển khai: Thao tác tạo tài khoản thử nghiệm có email đã xác thực trên Cognito)*
{{% /notice %}}
<!-- ![Tạo người dùng thử nghiệm](/images/5-Workshop/5.3-Cognito/cognito-06-create-test-user.png) -->

4. Nhấn **Create user**.
5. Xác nhận tài khoản `demo-user@example.com` xuất hiện trong danh sách người dùng với trạng thái **Confirmed**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-07-test-user-confirmed.png`  
*(Cần chụp khi triển khai: Danh sách người dùng hiển thị demo-user@example.com ở trạng thái Confirmed)*
{{% /notice %}}
<!-- ![Người dùng được xác nhận](/images/5-Workshop/5.3-Cognito/cognito-07-test-user-confirmed.png) -->

---

#### Bước 2: Kiểm tra Đăng nhập Bằng AWS CLI

Bạn có thể giả lập luồng đăng nhập từ ứng dụng client thông qua lệnh AWS CLI `initiate-auth` cùng Client ID đã tạo:

```bash
aws cognito-idp initiate-auth \
  --region us-east-1 \
  --client-id <YOUR_APP_CLIENT_ID> \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=demo-user@example.com,PASSWORD=ExpenseTracker2026!
```

Khi thông tin đăng nhập chính xác, Cognito sẽ trả về khối `AuthenticationResult`:

```json
{
  "AuthenticationResult": {
    "AccessToken": "eyJraWQiOiJ...",
    "ExpiresIn": 3600,
    "TokenType": "Bearer",
    "RefreshToken": "eyJraWQiOiJ...",
    "IdToken": "eyJraWQiOiJ..."
  }
}
```

---

#### Bước 3: Tìm hiểu Cấu trúc JWT Claims & Phân biệt Xác thực (AuthN) với Phân quyền (AuthZ)

Amazon Cognito phát hành hai loại token với mục đích hoàn toàn khác nhau sau khi đăng nhập thành công:

1. **Phân biệt Xác thực (Authentication) và Phân quyền (Authorization):**
   + **Xác thực (AuthN - "Bạn là ai?"):** **ID Token (`IdToken`)** là token định danh chuẩn OpenID Connect (OIDC) chứng minh danh tính của người dùng. Token này chứa các thông tin cá nhân như `sub` (mã định danh người dùng duy nhất), `email` và `email_verified`.
   + **Phân quyền (AuthZ - "Bạn được phép làm gì?"):** **Access Token (`AccessToken`)** chứng minh các quyền hạn và phạm vi truy cập (OAuth 2.0 scopes) được cấp cho ứng dụng (`token_use: "access"`).

2. **Tại sao Kiến trúc này Sử dụng ID Token gửi đến API Gateway:**
   + **Kiểm tra Đối tượng Nhận (Audience `aud`):** Trong Amazon Cognito, **ID Token** chứa mã Client ID của App Client trong trường `aud` chuẩn (`"aud": "<YOUR_APP_CLIENT_ID>"`). Ngược lại, Cognito Access Token tiêu chuẩn định danh client qua trường `client_id` và không chứa trường `aud` tương ứng với App Client ID (trừ khi cấu hình Resource Server và OAuth scope tùy chỉnh).
   + Do cơ chế JWT Authorizer của API Gateway HTTP API được cấu hình kiểm tra `Audience = <APP_CLIENT_ID>` nhằm chặn token từ các ứng dụng khác, API Gateway yêu cầu token gửi lên phải có trường `aud` tương ứng. Việc gửi ID Token giúp API Gateway xác thực thành công danh tính người gọi và chuyển tiếp thông tin `sub` an toàn sang hàm AWS Lambda.

3. **Ví dụ Payload của ID Token sau khi giải mã:**
   ```json
   {
     "sub": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
     "email_verified": true,
     "iss": "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_xxxxxxxxx",
     "cognito:username": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
     "aud": "<YOUR_APP_CLIENT_ID>",
     "event_id": "8f8b...",
     "token_use": "id",
     "auth_time": 1760440000,
     "exp": 1760443600,
     "email": "demo-user@example.com"
   }
   ```

{{% notice tip %}}
**Nền tảng Bảo mật:** Giá trị trường `sub` (`"a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d"`) là một chuỗi UUID bất biến đại diện cho người dùng trên toàn bộ hệ thống backend AWS. Trong các phần sau, hàm Lambda sẽ trích xuất trực tiếp giá trị này từ token đã được xác thực để làm khóa phân vùng dữ liệu trong DynamoDB, ngăn chặn tuyệt đối tình trạng người dùng truy cập nhầm dữ liệu của nhau.
{{% /notice %}}

---

#### Bước Tiếp theo

Chuyển sang phần [Thiết kế Bảng Dữ liệu Amazon DynamoDB](../../5.4-DynamoDB/) để khởi tạo cơ sở dữ liệu không máy chủ.
