---
title : "Cấu hình Cognito JWT Authorizer"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.6.2 </b> "
---

Amazon API Gateway HTTP APIs cung cấp cơ chế **JWT Authorizer** tích hợp sẵn, cho phép xác thực các token tuân theo chuẩn OpenID Connect (OIDC) do các dịch vụ định danh như Amazon Cognito phát hành. Bằng cách kiểm tra tính hợp lệ của token ngay tại tầng cổng API Gateway, các yêu cầu chưa được xác thực hoặc độc hại sẽ bị chặn ngay lập tức trước khi chuyển đến tài nguyên tính toán backend, giúp tiết kiệm chi phí chạy hàm Lambda và bảo vệ an toàn cho hệ thống.

---

#### Cách Thức JWT Authorizer Bảo vệ API của Bạn

Khi ứng dụng client gửi một yêu cầu HTTP có tiêu đề `Authorization: Bearer <TOKEN>`:
1. **Kiểm tra Chữ ký Số (Signature Verification):** API Gateway tự động tải bộ khóa công khai JSON Web Key Set (JWKS) từ endpoint của Cognito (`https://cognito-idp.us-east-1.amazonaws.com/<USER_POOL_ID>/.well-known/jwks.json`) và kiểm tra tính toàn vẹn của chữ ký mật mã.
2. **Kiểm tra Issuer & Thời hạn:** Xác nhận token chưa hết hạn (`exp`) và được phát hành đúng từ Cognito User Pool tin cậy (`iss`).
3. **Kiểm tra Đối tượng Nhận (Audience Check):** Đảm bảo token được tạo dành riêng cho ứng dụng web frontend của bạn (giá trị `aud` trùng khớp với App Client ID).
4. **Đưa Dữ liệu vào Ngữ cảnh:** Trích xuất các claim đã xác thực (như `sub` và `email`) và truyền vào đối tượng sự kiện của Lambda tại `event.requestContext.authorizer.jwt.claims`.

{{% notice note %}}
**Tại sao Gửi ID Token? (Phân biệt Xác thực - Authentication và Phân quyền - Authorization)**
* **ID Token so với Access Token:** Trong Amazon Cognito, **ID Token** chứa trường `aud` (Audience) mang giá trị App Client ID theo chuẩn danh tính OpenID Connect (OIDC). Ngược lại, Cognito **Access Token** chứa trường `client_id` thay vì `aud` (trừ khi cấu hình Resource Server và OAuth scope tùy chỉnh). Do HTTP API JWT Authorizer của API Gateway bắt buộc kiểm tra trường `aud` khớp với cấu hình Audience, ứng dụng frontend cần gửi Cognito ID Token trong tiêu đề `Authorization: Bearer <TOKEN>`.
* **Xác thực (Authentication - AuthN):** Được thực hiện tại tầng biên API Gateway thông qua JWT Authorizer. Cơ chế này xác nhận *bạn là ai* bằng cách kiểm tra chữ ký số, nhà phát hành (issuer) và thời hạn token, ngăn chặn triệt để các yêu cầu nặc danh chạm tới Lambda.
* **Phân quyền (Authorization - AuthZ):** Được thực hiện trong mã nguồn Lambda và thiết kế DynamoDB. Cơ chế này quyết định *bạn được phép thao tác gì*. Hàm Lambda trích xuất `sub` (User ID) đã được xác minh từ ngữ cảnh yêu cầu và giới hạn mọi truy vấn/thay đổi dữ liệu trong DynamoDB chỉ thuộc về người dùng đó, bảo đảm cô lập dữ liệu người dùng tuyệt đối.
{{% /notice %}}

---

#### Các Bước Cấu hình Chi tiết

1. Tại [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1), chọn `ExpenseTrackerApi`.
2. Tại menu bên trái dưới mục **Develop**, chọn **Authorization**.
3. Chọn thẻ **Manage authorizers**, sau đó nhấn **Create**.
4. Điền các trường thông tin cấu hình authorizer:
   + **Authorizer type:** Chọn **JWT**.
   + **Name:** Nhập `CognitoJWTAuthorizer`.
   + **Identity source:** Nhập `$request.header.Authorization`.
   + **Issuer URL:** Nhập `https://cognito-idp.us-east-1.amazonaws.com/<YOUR_USER_POOL_ID>` (Thay `<YOUR_USER_POOL_ID>` bằng ID User Pool của bạn từ Phần 5.3.1).
   + **Audience:** Nhấn **Add audience**, sau đó nhập **App Client ID** của Cognito User Pool (lấy từ Phần 5.3.1).
5. Nhấn **Create**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-03-jwt-authorizer.png`  
*(Cần chụp khi triển khai: Màn hình tạo JWT Authorizer liên kết với Issuer URL và Audience của Cognito)*
{{% /notice %}}
<!-- ![Cấu hình JWT Authorizer](/images/5-Workshop/5.6-APIGateway/apigw-03-jwt-authorizer.png) -->

{{% notice tip %}}
**Bộ Xác thực Sẵn sàng:** JWT Authorizer hiện đã được thiết lập và sẵn sàng để gắn vào các tuyến đường của ứng dụng trong bài tiếp theo.
{{% /notice %}}
