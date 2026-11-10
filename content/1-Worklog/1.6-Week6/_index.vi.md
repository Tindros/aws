---
title: "Worklog Tuần 6"
date: 2026-10-12
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**### Mục tiêu tuần 6:**

* Xây dựng kiến trúc AWS cho Serverless Personal Expense Tracker.
* Bắt đầu triển khai các dịch vụ AWS cần thiết cho project.
* Thiết lập cơ sở dữ liệu và cơ chế xác thực người dùng.

**### Các công việc cần triển khai trong tuần này:**

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rà soát kiến trúc Serverless Personal Expense Tracker.<br>- Xác định vai trò của Cognito, API Gateway, Lambda, DynamoDB, CloudWatch và IAM trong hệ thống. | 13/10/2026 | 13/10/2026 | |
| 3 | - Tạo và cấu hình Amazon Cognito User Pool.<br>- Tạo App Client và cấu hình authentication cho frontend. | 14/10/2026 | 14/10/2026 | |
| 4 | - Tạo Amazon DynamoDB table cho dữ liệu expense.<br>- Thiết kế partition key và sort key theo userId và expenseId. | 15/10/2026 | 15/10/2026 | |
| 5 | - Tạo IAM Role cho Lambda.<br>- Cấu hình quyền truy cập DynamoDB và CloudWatch Logs. | 16/10/2026 | 16/10/2026 | |

**### Kết quả đạt được tuần 6:**

* Hoàn thiện kiến trúc Serverless cho Personal Expense Tracker.
* Tạo thành công Cognito User Pool và App Client.
* Tạo DynamoDB table `ExpenseTable`.
* Thiết kế dữ liệu sử dụng `userId` làm partition key và `expenseId` làm sort key.
* Tạo Lambda Execution Role và cấu hình các quyền cần thiết.
* Hiểu rõ luồng xác thực từ frontend → Cognito → JWT và cách JWT được sử dụng để bảo vệ API.