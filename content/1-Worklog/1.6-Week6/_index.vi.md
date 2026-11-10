---
title: "Worklog Tuần 6"
date: 2026-10-12
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**### Mục tiêu tuần 6:**

* Xây dựng kiến trúc AWS cho Serverless Personal Expense Tracker.
* Cấu hình các dịch vụ AWS cần thiết cho authentication và lưu trữ dữ liệu.
* Chuẩn bị nền tảng backend cho ứng dụng.

**### Các công việc cần triển khai trong tuần này:**

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rà soát kiến trúc Serverless Personal Expense Tracker.<br>- Xác định vai trò của Cognito, API Gateway, Lambda, DynamoDB, CloudWatch và IAM. | 13/10/2026 | 13/10/2026 | [The First Cloud Journey](https://cloudjourney.awsstudygroup.com/) |
| 3 | - Tạo và cấu hình Amazon Cognito User Pool.<br>- Tạo App Client cho ứng dụng web.<br>- Tìm hiểu luồng authentication và JWT token. | 14/10/2026 | 14/10/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) |
| 4 | - Tạo DynamoDB table cho dữ liệu expense.<br>- Thiết kế partition key và sort key theo userId và expenseId. | 15/10/2026 | 15/10/2026 | [Work with Amazon DynamoDB](https://000060.awsstudygroup.com/) |
| 5 | - Tạo IAM Role cho Lambda.<br>- Cấu hình quyền truy cập DynamoDB và CloudWatch Logs. | 16/10/2026 | 16/10/2026 | [IAM](https://000002.awsstudygroup.com/) · [CloudWatch](https://000008.awsstudygroup.com/) |

**### Kết quả đạt được tuần 6:**

* Hoàn thiện kiến trúc Serverless cho Personal Expense Tracker.
* Tạo thành công Cognito User Pool và App Client.
* Tạo DynamoDB table ExpenseTable.
* Thiết kế dữ liệu sử dụng userId làm partition key và expenseId làm sort key.
* Tạo Lambda Execution Role và cấu hình các quyền cần thiết.
* Hiểu rõ luồng authentication từ frontend → Cognito → JWT và cách JWT được sử dụng để bảo vệ API.
