---
title : "Tích hợp Frontend React với Backend AWS"
date : 2026-10-14 
weight : 7 
chapter : false
pre : " <b> 5.7. </b> "
---

#### Chuyển đổi từ Bản Mẫu Giả lập (Mock) sang Hạ tầng Đám mây AWS

Giao diện người dùng của **Serverless Personal Expense Tracker** là một ứng dụng Single Page Application (SPA) hiện đại được xây dựng bằng **React 19**, **Vite** và **TypeScript**. Trong giai đoạn phát triển ban đầu, ứng dụng sử dụng các dịch vụ giả lập nội bộ (`authService.ts` và `expenseService.ts`) với mảng dữ liệu mẫu và độ trễ mạng giả định.

Trong phần này, bạn sẽ kết nối toàn diện mã nguồn frontend với hạ tầng đám mây AWS đã triển khai:
1. **Cấu hình Biến Môi trường:** Khai báo các tham số triển khai AWS vào tệp biến môi trường Vite (`.env`).
2. **Xác thực Thực tế:** Thay thế logic đăng nhập/đăng ký giả lập bằng thư viện chuẩn của Amazon Cognito (`amazon-cognito-identity-js` hoặc AWS Amplify Auth), lưu trữ các token JWT trả về vào bộ nhớ trình duyệt.
3. **Kết nối REST API Thực tế:** Liên kết dịch vụ quản lý chi tiêu với địa chỉ Invoke URL của API Gateway, tự động đính kèm tiêu đề `Authorization: Bearer <TOKEN>` vào mọi yêu cầu gửi đi.

---

#### Nội dung Chi tiết

1. [Kết nối Xác thực Amazon Cognito](5.7.1-cognito-auth/)
2. [Kết nối Cổng API Gateway & Dữ liệu DynamoDB](5.7.2-api-service/)
