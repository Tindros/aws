---
title : "Triển khai Hàm AWS Lambda & IAM Role"
date : 2026-10-14 
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---

#### Vai trò của AWS Lambda trong Tính toán Không máy chủ

**AWS Lambda** là dịch vụ tính toán hướng sự kiện (event-driven serverless compute) cho phép thực thi mã nguồn xử lý nghiệp vụ backend mà không cần cấu hình máy chủ, vá lỗi hệ điều hành hay quản lý tiến trình nền. Lambda có thể co giãn theo nhu cầu, trong giới hạn quota và concurrency của tài khoản. Trong workshop này, hàm được gọi khi yêu cầu đi qua Amazon API Gateway.

Trong kiến trúc của **Serverless Personal Expense Tracker**, AWS Lambda đảm nhận vai trò bộ điều khiển trung tâm (Controller):
+ **Trích xuất Định danh:** Đọc mã định danh Cognito (`sub`) đã được xác thực từ request context của API Gateway và dùng giá trị đó làm `userId` khi thao tác với DynamoDB.
+ **Kiểm tra Tính Hợp lệ:** Xác thực dữ liệu đầu vào (số tiền, danh mục, ngày phát sinh, mô tả) trước khi lưu trữ vào cơ sở dữ liệu.
+ **Tương tác với DynamoDB:** Thực hiện các thao tác truy vấn và cập nhật trên bảng `ExpenseTable` sử dụng bộ công cụ AWS SDK v3 (`@aws-sdk/client-dynamodb` và `@aws-sdk/lib-dynamodb`).
+ **Tổng hợp Số liệu:** Tính toán các chỉ số thống kê phục vụ bảng điều khiển (tổng chi tiêu, chi tiêu trong tháng, phân bổ theo danh mục, nhóm chi tiêu cao nhất).
+ **Thực thi với Quyền Tối thiểu:** Chạy dưới sự kiểm soát của IAM Role chuyên dụng, chỉ được phép truy cập bảng DynamoDB của dự án và xuất nhật ký ra CloudWatch.

---

#### Nội dung Chi tiết

1. [Tạo IAM Lambda Execution Role](5.5.1-iam-role/)
2. [Triển khai Hàm Lambda Xử lý Chi tiêu](5.5.2-create-function/)
