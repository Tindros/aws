---
title : "Amazon API Gateway"
date : 2026-10-14
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Vai trò của Amazon API Gateway trong Kiến trúc Serverless

**Amazon API Gateway** cung cấp một cổng giao tiếp được quản lý để ứng dụng kết nối với các dịch vụ backend. Trong dự án Expense Tracker, **HTTP APIs (API Gateway v2)** cung cấp các tính năng cần thiết để kết nối React frontend với AWS Lambda:

- **Xác thực JWT:** JWT Authorizer tích hợp sẵn có thể kiểm tra token do các nhà cung cấp danh tính như Amazon Cognito phát hành.
- **Cấu hình CORS:** Thiết lập quyền truy cập cross-origin cho frontend chạy tại máy cá nhân và ứng dụng Amplify sau khi triển khai.
- **Lambda proxy integration:** Chuyển tiếp yêu cầu HTTP đến hàm Lambda và trả phản hồi của hàm về client.
- **Tự động triển khai:** Stage của HTTP API có thể tự động triển khai các thay đổi về routes và integrations khi bật auto-deploy.

---

#### Nội dung Chi tiết

Trong phần này, bạn sẽ cấu hình HTTP API, tạo Cognito JWT Authorizer, kết nối các route của ứng dụng với Lambda và lấy API endpoint:

1. [Tạo HTTP API & Cấu hình CORS](5.6.1-create-http-api/)
2. [Cấu hình Cognito JWT Authorizer](5.6.2-jwt-authorizer/)
3. [Tạo Tuyến đường (Routes) & Tích hợp Lambda Proxy](5.6.3-routes-and-integration/)
