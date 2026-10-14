---
title : "Amazon API Gateway"
date : 2026-10-14 
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---

#### Vai trò của Amazon API Gateway trong Kiến trúc Serverless

**Amazon API Gateway** đóng vai trò là cánh cổng giao tiếp bảo mật và có khả năng mở rộng cao, kết nối các ứng dụng client với các dịch vụ xử lý backend. Đối với các ứng dụng web Serverless yêu cầu các endpoint RESTful và cơ chế xác thực token, **API Gateway HTTP APIs (v2)** đem lại nhiều ưu điểm vượt trội so với phiên bản REST API truyền thống:
+ **Tối ưu Chi phí & Hiệu năng Cao:** Giảm tới 71% chi phí vận hành và giảm độ trễ xử lý tới 70% so với REST API.
+ **Ủy quyền JWT Tích hợp Sẵn:** Hỗ trợ cơ chế JWT Authorizer nguyên bản, tự động kiểm tra tính hợp lệ của token Cognito ngay tại cổng API Gateway mà không cần viết thêm hàm Lambda Authorizer tùy biến.
+ **Quản lý CORS Đơn giản:** Cấu hình Cross-Origin Resource Sharing (CORS) theo phương thức khai báo trực quan, loại bỏ các lỗi xung đột nguồn gốc thường gặp trên Single Page Application.
+ **Tích hợp Lambda Proxy Tự nhiên:** Tự động đóng gói và giải nén dữ liệu yêu cầu/phản hồi HTTP giữa trình duyệt người dùng và hàm AWS Lambda.

---

#### Nội dung Chi tiết

Trong phần này, bạn sẽ cấu hình HTTP API, thiết lập bộ xác thực JWT Authorizer và gán các tuyến đường API:

1. [Tạo HTTP API & Cấu hình CORS](5.6.1-create-http-api/)
2. [Cấu hình Cognito JWT Authorizer](5.6.2-jwt-authorizer/)
3. [Tạo Tuyến đường (Routes) & Tích hợp Lambda Proxy](5.6.3-routes-and-integration/)
