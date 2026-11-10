---
title: "Worklog Tuần 8"
date: 2026-10-26
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

**Mục tiêu tuần 8:**

* Tích hợp frontend React với backend AWS.
* Thay thế mock service bằng API thực tế.
* Hoàn thiện authentication và các chức năng CRUD trên frontend.

**Các công việc cần triển khai trong tuần này:**

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tích hợp Amazon Cognito vào frontend.<br>- Xử lý đăng nhập và token. | 27/10/2026 | 27/10/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) |
| 3 | - Cập nhật expenseService.ts để gọi API Gateway thay cho mock service.<br>- Gửi JWT Access Token trong Authorization header. | 28/10/2026 | 28/10/2026 | [Frontend Integration with API Gateway](https://000135.awsstudygroup.com/) |
| 4 | - Kết nối Dashboard với API thực tế.<br>- Tích hợp thêm, xem, sửa và xóa expense. | 29/10/2026 | 29/10/2026 | [Build Frontend to call API Gateway](https://000079.awsstudygroup.com/) |
| 5 | - Kiểm thử toàn bộ frontend và backend.<br>- Kiểm tra luồng Add → Dashboard → Edit → Dashboard → Delete → Dashboard. | 30/10/2026 | 30/10/2026 | [Frontend Integration with API Gateway](https://000135.awsstudygroup.com/) |

**Kết quả đạt được tuần 8:**

* Tích hợp Amazon Cognito vào frontend.
* Frontend có thể xác thực người dùng và nhận JWT Access Token.
* Kết nối thành công React frontend với API Gateway.
* Thay thế mock expense service bằng API thực tế.
* Hoàn thiện các chức năng CRUD trên frontend.
* Kết nối Dashboard với dữ liệu expense và summary từ backend.
* Kiểm thử thành công luồng thêm, xem, sửa và xóa expense.
