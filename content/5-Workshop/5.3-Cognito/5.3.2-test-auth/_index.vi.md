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
   + Tại mục **Temporary password**, chọn **Set a permanent password** và nhập mật khẩu riêng, đủ mạnh và thỏa mãn chính sách. Không dùng lại mật khẩu ví dụ trong tài liệu hoặc commit thông tin đăng nhập vào repository.

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

App Client trong workshop này được cấu hình với **Choice-based sign-in** và **Secure Remote Password (SRP)**. Luồng cũ `USER_PASSWORD_AUTH` không được bật, vì vậy không sử dụng ví dụ AWS CLI `USER_PASSWORD_AUTH` tại đây. Lệnh đó sẽ thất bại nếu bạn không thay đổi cấu hình App Client, điều không nằm trong phạm vi workshop này.

```text
Việc đăng nhập sẽ được kiểm thử thông qua React SPA ở Phần 5.7.1, sử dụng luồng client tương thích SRP của Cognito. Sau khi đăng nhập thành công, kiểm tra ID token và access token được trả về trong công cụ phát triển của trình duyệt hoặc phần debug ứng dụng. Không dán token thật vào issue công khai, repository hay ảnh chụp màn hình.
```

Sau khi đăng nhập thành công qua SPA, Cognito trả về kết quả xác thực chứa các token. Phản hồi chính xác phụ thuộc vào luồng đăng nhập được chọn. Ví dụ dưới đây chỉ minh họa đã lược bỏ dữ liệu nhạy cảm, không phải kết quả để sao chép:

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
   + **Phân quyền (AuthZ - "Bạn được phép làm gì?"):** **Access Token (`AccessToken`)** là token phù hợp để gửi yêu cầu đến API và chứa claim `token_use: "access"` cùng các scope nếu được cấu hình.

2. **Tại sao Kiến trúc này Sử dụng Access Token gửi đến API Gateway:**
   + ID Token mô tả danh tính người dùng đã đăng nhập cho ứng dụng client. Access Token là token phù hợp để gửi đến API.
   + Với HTTP API JWT Authorizer, API Gateway kiểm tra claim `aud` nếu có; nếu không có `aud`, API Gateway có thể kiểm tra `client_id`. Access Token của Cognito thường sử dụng `client_id`, vì vậy hãy cấu hình Audience bằng App Client ID và gửi Access Token trong tiêu đề `Authorization: Bearer <TOKEN>`.
   + API Gateway xác minh chữ ký, issuer, thời hạn và audience/mã client trước khi chuyển các claim đã xác thực như `sub` đến Lambda.

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
**Nền tảng Bảo mật:** Claim `sub` là định danh ổn định của người dùng trong User Pool. Lambda phải sử dụng `sub` do API Gateway cung cấp sau khi xác thực token, không sử dụng `userId` do client gửi trong request body để xác định phạm vi dữ liệu DynamoDB.
{{% /notice %}}

---

#### Bước Tiếp theo

Chuyển sang phần [Thiết kế Bảng Dữ liệu Amazon DynamoDB](../../5.4-DynamoDB/) để khởi tạo cơ sở dữ liệu không máy chủ.
