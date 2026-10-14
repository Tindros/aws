---
title : "Tạo Cognito User Pool & App Client"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.3.1 </b> "
---

Trong phần này, bạn sẽ thiết lập tầng xác thực người dùng và quản lý danh tính cho ứng dụng **Serverless Personal Expense Tracker** bằng dịch vụ **Amazon Cognito**. Bạn sẽ cấu hình một **Cognito User Pool** và tạo một **App Client** dành riêng cho ứng dụng Single-Page Application (SPA) xây dựng bằng React, Vite và TypeScript.

---

#### Bước 1: Mở Cognito Console & Bắt đầu Khởi tạo

1. Đăng nhập vào AWS Management Console và truy cập [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
2. Kiểm tra thanh định hướng góc trên bên phải để chắc chắn bạn đang ở Region **US East (N. Virginia) `us-east-1`**.
3. Tại menu bên trái, chọn **User pools**, sau đó nhấn **Create user pool** để mở trình hướng dẫn thiết lập ứng dụng (application setup wizard) hiện tại của Cognito.

---

#### Bước 2: Cấu hình Loại Ứng dụng & Tùy chọn Đăng nhập trong Wizard

Trình hướng dẫn thiết lập của Amazon Cognito hiện nay tích hợp cấu hình thư mục người dùng và ứng dụng client vào một luồng trực quan duy nhất:

1. **Application type (Loại ứng dụng):** Chọn **Single-page application (SPA)**.
   + Lựa chọn này thông báo cho Cognito rằng frontend của bạn là ứng dụng chạy trực tiếp trên trình duyệt web của người dùng (ví dụ: React, Vue, Angular) mà không có máy chủ backend chuyên dụng để lưu session cookies.
2. **Name your application (Đặt tên ứng dụng):**
   + Trong giao diện khởi tạo, Cognito gợi ý một tên tạm thời được sinh tự động (ví dụ: `My SPA app - -wktua`).
   + *Lưu ý:* Trong cấu hình hoàn chỉnh của dự án, App Client này được đặt tên là `ExpenseTrackerWebClient`. Bạn có thể nhập `ExpenseTrackerWebClient` ngay tại đây hoặc giữ tên mặc định và đổi tên sau khi tạo.
3. **Configure options (Tùy chọn nhận diện đăng nhập):**
   + Tại mục **Options for sign-in identifiers**, đánh dấu chọn **Email**.
   + Bỏ chọn các ô **Phone number** và **Username**. Người dùng sẽ đăng ký và đăng nhập vào ứng dụng bằng địa chỉ email.
4. **Self-registration (Tự đăng ký):**
   + Đánh dấu chọn **Enable self-registration**. Tùy chọn này cho phép người dùng mới tự tạo tài khoản trực tiếp từ giao diện web frontend.
5. **Required attributes for sign-up (Thuộc tính bắt buộc khi đăng ký):**
   + Tại danh sách thuộc tính bắt buộc, chọn **`email`**.
   + Người dùng bắt buộc phải cung cấp địa chỉ email hợp lệ khi đăng ký tài khoản.

![Cấu hình Wizard SPA & Tùy chọn Đăng nhập](/images/5-Workshop/5.3-Cognito/cognito-01-sign-in-experience.png)

6. Nhấn nút xác nhận khởi tạo để hoàn tất việc tạo User Pool.
   + *Lưu ý về thiết lập mặc định:* Trình hướng dẫn SPA sẽ tự động áp dụng các thiết lập mặc định tối ưu: chính sách mật khẩu tiêu chuẩn, No MFA, gửi email qua dịch vụ mặc định của Cognito, và tạo một App Client công khai không dùng client secret.

---

#### Bước 3: Kiểm tra Yêu cầu Bảo mật & Chính sách Mật khẩu

Sau khi User Pool được khởi tạo thành công, bạn có thể kiểm tra các thông số bảo mật trực tiếp trên bảng điều khiển Cognito:

1. Tại danh sách User pools, nhấn vào User Pool vừa tạo để mở trang chi tiết.
2. Chuyển sang thẻ **Sign-in experience** và tìm thẻ **Password policy (Chính sách mật khẩu)**:
   + **Password minimum length (Độ dài tối thiểu):** `8 character(s)` (8 ký tự).
   + **Password requirements (Yêu cầu mật khẩu):** Phải chứa ít nhất 1 chữ số, 1 ký tự đặc biệt, 1 chữ in hoa và 1 chữ thường.
   + **Temporary passwords set by administrators expire in (Mật khẩu tạm thời do admin tạo hết hạn sau):** `7 day(s)` (7 ngày).
   + **Password reuse (Tái sử dụng mật khẩu):** Cho phép tái sử dụng mật khẩu cũ (Allow reuse of previous passwords).

![Thiết lập Chính sách Mật khẩu](/images/5-Workshop/5.3-Cognito/cognito-02-security-requirements.png)

3. **Multi-factor authentication (Xác thực đa yếu tố - MFA):**
   + Tại phần cấu hình bảo mật, xác nhận **MFA enforcement** đang ở chế độ **No MFA**.
   + {{% notice info %}}
     **Lưu ý về Giao diện Bảng điều khiển:** Trên console Amazon Cognito hiện tại, thiết lập **Password policy** và **MFA enforcement** nằm ở hai thẻ/khung hiển thị riêng biệt. Ảnh chụp màn hình phía trên tập trung vào khung Password policy. Trong khuôn khổ bài thực hành, việc chọn **No MFA** giúp tránh phát sinh chi phí gửi tin nhắn SMS và đơn giản hóa quy trình thử nghiệm.
     {{% /notice %}}
4. **Message delivery (Phương thức gửi thư thông báo):**
   + Tại phần cấu hình gửi tin nhắn, xác nhận email provider được đặt là **Send email with Cognito**.
   + *Lưu ý:* Dịch vụ email tích hợp sẵn này cung cấp hạn mức gửi thư miễn phí hằng ngày đủ cho quá trình phát triển và kiểm thử mà không cần cấu hình xác thực tên miền trên Amazon SES. Dịch vụ gửi SMS không được cấu hình.

---

#### Bước 4: Đổi tên User Pool thành `expense-tracker-user-pool`

Để đảm bảo tính nhất quán với tài liệu kiến trúc và quy chuẩn đặt tên tài nguyên dự án:

1. Quay trở lại trang **Overview (Tổng quan)** của User Pool.
2. Ở góc trên bên phải trang, nhấn nút **Rename**.
3. Trong hộp thoại **Edit user pool**:
   + Tại mục **User pool name**, nhập `expense-tracker-user-pool`.
   + Nhấn **Save changes** để lưu thay đổi.

![Hộp thoại Đổi tên User Pool](/images/5-Workshop/5.3-Cognito/cognito-03-user-pool-name.png)

---

#### Bước 5: Kiểm tra Cấu hình App Client (`ExpenseTrackerWebClient`)

Tiếp theo, kiểm tra thông số của App Client công khai được tạo cho frontend React:

1. Trong console User Pool, chuyển sang thẻ **App integration**.
2. Cuộn xuống mục **App clients** và nhấn vào client ứng dụng (tên là `ExpenseTrackerWebClient`).
3. Kiểm tra các thông số chính của App Client:
   + **App client name:** `ExpenseTrackerWebClient`.
   + **Client ID:** Sao chép chuỗi mã định danh 26 ký tự chữ và số (ví dụ: `6ngvi1al5pn81n534og2guvl27`).
   + **Client secret:** Hiển thị dấu `-` (Không có mã bí mật).

{{% notice warning %}}
**Quy tắc Kiến trúc Quan trọng — Tuyệt đối không tạo Client Secret cho SPA:**  
Ứng dụng Single-Page Application chạy hoàn toàn trên trình duyệt người dùng. Mọi mã bí mật (client secret) nhúng vào mã nguồn JavaScript đều có thể bị lộ khi người dùng xem mã nguồn hoặc theo dõi lưu lượng mạng qua công cụ Developer Tools. Việc giữ Client secret ở trạng thái không kích hoạt (`-`) cho phép React app tương tác trực tiếp với Cognito qua các luồng xác thực công khai an toàn.
{{% /notice %}}

4. Kiểm tra danh sách các luồng xác thực (**Authentication flows**) đang kích hoạt:
   + `Choice-based sign-in`
   + `Secure remote password (SRP)`
   + `Get user tokens from existing authenticated sessions`
   + *Lưu ý:* Không tự ý kích hoạt thêm các luồng xác thực khác ngoài danh sách trên. Bất kỳ tùy chỉnh nào cần thiết cho việc tích hợp frontend sẽ được hướng dẫn chi tiết trong các bài học sau.
5. Kiểm tra thời gian hết hạn token (**Token expiration settings**):
   + **Access token expiration:** `60 minutes` (60 phút).
   + **ID token expiration:** `60 minutes` (60 phút).
   + **Refresh token expiration:** `5 day(s)` (5 ngày).
   + **Authentication flow session duration:** `3 minutes` (3 phút).
6. Cài đặt nâng cao: Xác nhận hai tùy chọn **Enable token revocation** và **Enable prevent user existence errors** đang được kích hoạt.

![Chi tiết App Client - ExpenseTrackerWebClient](/images/5-Workshop/5.3-Cognito/cognito-04-app-client.png)

{{% notice tip %}}
**Phạm vi & Giới hạn Tích hợp:**  
Bạn chưa cần cấu hình Hosted UI callback URLs, sign-out URLs hay OAuth 2.0 scopes ở bước này. Ứng dụng React của chúng ta giao tiếp trực tiếp với Cognito APIs thông qua thư viện AWS SDK / Amplify Auth thay vì chuyển hướng trang đăng nhập Hosted UI. Việc tích hợp hoàn chỉnh sẽ được thực hiện tại phần [7. Tích hợp React Frontend](../../5.7-Frontend-integration/).
{{% /notice %}}

---

#### Bước 6: Xem Tổng quan User Pool & Ghi lại Thông số Cần thiết

Quay lại trang **Overview** của `expense-tracker-user-pool` để xác nhận thông tin tài nguyên đã tạo và lưu lại các tham số cần thiết:

1. Kiểm tra các thông tin chính trên trang **Overview**:
   + **User pool name:** `expense-tracker-user-pool`.
   + **User pool ID:** Hiển thị tại mục **User pool information** (dạng: `us-east-1_xxxxxxxxx`).
   + **Regions:** `US East (N. Virginia)` (`us-east-1`).
   + **OpenID Connect configuration URL:** Endpoint định danh OIDC tiêu chuẩn của Cognito.
   + **Token signing key URL:** Đường dẫn JSON Web Key Set (JWKS) dùng bởi các dịch vụ backend (như API Gateway) để giải mã và xác thực chữ ký số của token.

![Tổng quan User Pool](/images/5-Workshop/5.3-Cognito/cognito-05-user-pool-overview.png)

2. Ghi lại hai thông số quan trọng bắt buộc cho các bài thực hành tiếp theo:

| Tên Tham số | Định dạng Mẫu | Mục đích & Nơi Sử dụng |
| :--- | :--- | :--- |
| **AWS Region** | `us-east-1` | Khu vực triển khai dịch vụ xuyên suốt AWS CLI, Lambda và API Gateway |
| **User Pool ID** | `us-east-1_xxxxxxxxx` | Định danh thư mục người dùng; bắt buộc cấu hình tại API Gateway JWT Authorizer |
| **App Client ID** | `6ngvi1al5pn81n534og2guvl27` | Định danh công khai của client app; cấu hình tại API Gateway Authorizer Audience & Frontend |

{{% notice info %}}
**Phân biệt User Pool ID và App Client ID:**  
- **User Pool ID** (`us-east-1_...`): Định danh toàn bộ thư mục tài khoản người dùng và đóng vai trò là nhà phát hành token OIDC (`iss`).
- **App Client ID** (thường là chuỗi 26 ký tự chữ và số): Định danh ứng dụng client yêu cầu token. ID token của Cognito thường chứa giá trị này trong claim `aud`; Access Token của Cognito thường chứa nó trong `client_id`. JWT authorizer của API Gateway HTTP API kiểm tra `aud` nếu claim này tồn tại và có thể kiểm tra `client_id` nếu không có `aud`.
- Không nhầm lẫn hoặc hoán đổi vị trí của hai giá trị này khi cấu hình hệ thống.
{{% /notice %}}

---

#### Tình trạng Hoàn thành & Bước Tiếp theo

{{% notice info %}}
**Điểm Kiểm soát Triển khai Tài nguyên:**  
Tại bước này, Amazon Cognito User Pool và App Client công khai cần được tạo và cấu hình tại `us-east-1`. Hãy xác nhận tên User Pool đã được lưu trên trang Overview trước khi tiếp tục. Việc tạo các tài nguyên này chỉ mới thiết lập hạ tầng danh tính, chưa bao gồm việc tích hợp mã nguồn frontend hay xác thực người dùng thực tế. Việc tạo tài khoản thử nghiệm và kiểm tra cấu trúc JWT token sẽ được thực hiện trong bài học kế tiếp.
{{% /notice %}}

Chuyển sang phần tiếp theo: [5.3.2 Đăng ký Người dùng, Xác thực & Kiểm tra JWT Token](../5.3.2-test-auth/) để tiến hành tạo tài khoản người dùng thử nghiệm và phân tích cấu trúc token JWT.
