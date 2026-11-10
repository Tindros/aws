---
title: "Worklog Tuần 7"
date: 2026-10-19
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

**Mục tiêu tuần 7:**

* Hoàn thiện backend Serverless cho Expense Tracker.
* Xây dựng Lambda xử lý business logic và kết nối với DynamoDB.
* Cấu hình API Gateway và JWT authentication.

**Các công việc cần triển khai trong tuần này:**

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo Lambda function expenseTrackerFunction.<br>- Cấu hình Node.js runtime và environment variable cho DynamoDB table. | 20/10/2026 | 20/10/2026 | [Serverless Backend with Lambda, S3, and DynamoDB](https://000078.awsstudygroup.com/) |
| 3 | - Xây dựng các API GET, POST, GET by ID, PUT, DELETE và summary.<br>- Kết nối Lambda với DynamoDB. | 21/10/2026 | 21/10/2026 | [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| 4 | - Tạo HTTP API bằng API Gateway.<br>- Tạo Lambda integration và các route cho expense.<br>- Cấu hình CORS. | 22/10/2026 | 22/10/2026 | [Build Frontend to call API Gateway](https://000079.awsstudygroup.com/) |
| 5 | - Cấu hình Cognito JWT Authorizer.<br>- Thiết lập issuer và audience bằng Cognito User Pool và App Client. | 23/10/2026 | 23/10/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) |

**Kết quả đạt được tuần 7:**

* Tạo và cấu hình thành công Lambda function expenseTrackerFunction.
* Xây dựng backend xử lý CRUD với DynamoDB.
* Xây dựng API summary tính tổng chi tiêu, số lượng expense và phân loại theo category.
* Tạo HTTP API và kết nối API Gateway với Lambda.
* Cấu hình Cognito JWT Authorizer cho API.
* Bảo vệ các API route bằng JWT authentication.
* Hoàn thiện phần backend chính của ứng dụng.
