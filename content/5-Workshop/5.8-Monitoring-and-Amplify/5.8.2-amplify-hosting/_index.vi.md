---
title : "Triển khai Frontend React lên AWS Amplify Hosting"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.8.2 </b> "
---

Trong phần này, bạn sẽ triển khai bản phát hành hoàn thiện của ứng dụng **React + Vite** lên dịch vụ **AWS Amplify Hosting**, giúp ứng dụng sẵn sàng phục vụ người dùng trên toàn cầu qua giao thức HTTPS bảo mật dựa trên mạng phân phối Amazon CloudFront.

---

#### Bước 1: Khởi tạo Ứng dụng trên AWS Amplify

1. Truy cập [AWS Amplify Console](https://us-east-1.console.aws.amazon.com/amplify/home?region=us-east-1).
2. Nhấn nút **Deploy an app** (hoặc **Host web app**).
3. Lựa chọn nguồn mã nguồn triển khai:
   + **Triển khai qua Git (khuyến nghị cho workshop này):** Kết nối nhà cung cấp Git chứa repository. Ứng dụng React nằm trong thư mục con `frontend/`, vì vậy hãy cấu hình monorepo app root của Amplify là `frontend` và đặt `AMPLIFY_MONOREPO_APP_ROOT=frontend` nếu Console yêu cầu.
   + **Phương án tải lên trực tiếp:** Chạy `npm run build` trong thư mục `frontend/`, sau đó tải nội dung build trong `frontend/dist/` lên theo quy trình manual deployment hiện hành của Amplify. Tệp `amplify.yml` dưới đây dành cho build qua Git, không dùng cho phương án ZIP.
4. **App name:** Nhập `expense-tracker-frontend`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-01-create-app.png`  
*(Cần chụp khi triển khai: Bắt đầu thiết lập lưu trữ ứng dụng web trên giao diện AWS Amplify)*
{{% /notice %}}
<!-- ![Khởi tạo Amplify App](/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-01-create-app.png) -->

---

#### Bước 2: Cấu hình Quá trình Xây dựng (`amplify.yml`)

1. Vì ứng dụng nằm trong thư mục con `frontend/` của repository, hãy đặt app root của Amplify là `frontend` và dùng cấu hình build sau cho triển khai qua Git:

```yaml
version: 1
applications:
  - appRoot: frontend
    frontend:
      phases:
        preBuild:
          commands:
            - npm ci
        build:
          commands:
            - npm run build
      artifacts:
        baseDirectory: dist
        files:
          - '**/*'
      cache:
        paths:
          - node_modules/**/*
```

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-02-build-settings.png`  
*(Cần chụp khi triển khai: Cấu hình xây dựng hiển thị tệp amplify.yml chuẩn cho dự án Vite)*
{{% /notice %}}
<!-- ![Cấu hình Build Amplify](/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-02-build-settings.png) -->

---

#### Bước 3: Thêm Biến Môi trường Sản xuất

1. Mở rộng phần **Advanced settings** (hoặc chuyển sang mục **App settings** -> **Environment variables**).
2. Khai báo 4 biến môi trường của dự án:
   + `VITE_AWS_REGION`: `us-east-1`
   + `VITE_COGNITO_USER_POOL_ID`: `<YOUR_USER_POOL_ID>`
   + `VITE_COGNITO_CLIENT_ID`: `<YOUR_APP_CLIENT_ID>`
   + `VITE_API_BASE_URL`: `<YOUR_API_GATEWAY_INVOKE_URL>`

> Mọi biến `VITE_*` đều được đóng gói vào frontend và người dùng có thể xem được. Chỉ đặt các cấu hình công khai như Region, User Pool ID, App Client ID và API URL ở đây—tuyệt đối không đặt access key, client secret, mật khẩu hoặc thông tin xác thực khác.
3. Nhấn **Save and deploy**.

---

#### Bước 4: Cấu hình Định tuyến Lại (Rewrites & Redirects) cho SPA

Vì React Router quản lý việc chuyển trang phía client (`/login`, `/expenses`, `/dashboard`), chúng ta cần cấu hình để Amplify tự động chuyển hướng các trang con về `index.html`:

1. Tại menu bên trái của Amplify, chọn **Rewrites and redirects**.
2. Kiểm tra hoặc thêm quy tắc định tuyến sau:
   + **Source address:** `</^[^.]+$|\.(?!(css|gif|ico|jpg|js|png|txt|svg|woff|woff2|ttf|map|json)$)([^.]+$)/>`
   + **Target address:** `/index.html`
   + **Type:** `200 (Rewrite)`

---

#### Bước 5: Xác minh Triển khai Thực tế

1. Theo dõi chu trình tự động hóa của Amplify qua các giai đoạn:
   + **Provision** -> **Build** -> **Deploy** -> **Verify**.
2. Sau khi quá trình hoàn tất thành công, bấm vào đường dẫn HTTPS công khai được cấp (ví dụ: `https://main.xxxx.amplifyapp.com`).
3. Xác nhận frontend đã triển khai có thể tải, đăng nhập bằng tài khoản Cognito đã xác nhận và đọc/tạo khoản chi qua API đã cấu hình. Nếu build thành công nhưng API lỗi, hãy kiểm tra biến môi trường, API URL, danh sách CORS allowlist và cấu hình JWT authorizer.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-03-deployed-overview.png`  
*(Cần chụp khi triển khai: Trang tổng quan Amplify Hosting hiển thị trạng thái hoàn thành và tên miền hoạt động)*
{{% /notice %}}
<!-- ![Tổng quan Triển khai](/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-03-deployed-overview.png) -->

---

#### Bước Tiếp theo

Chuyển sang phần [Kiểm thử Toàn trình, Bảo mật & Dọn dẹp Tài nguyên](../../5.9-Testing-and-Cleanup/) để xác minh các hàng rào bảo mật và thực hiện quy trình giải phóng tài nguyên.
