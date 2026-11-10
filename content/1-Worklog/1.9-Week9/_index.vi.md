---
title: "Worklog Tuần 9"
date: 2026-11-02
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

**Mục tiêu tuần 9:**

* Kiểm thử và ổn định toàn bộ hệ thống.
* Kiểm tra JWT authentication, authorization và data isolation.
* Rà soát và hoàn thiện nội dung Workshop.

**Các công việc cần triển khai trong tuần này:**

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Kiểm thử Lambda với các API GET, POST, PUT, DELETE và summary.<br>- Kiểm tra truy cập dữ liệu DynamoDB. | 03/11/2026 | 03/11/2026 | [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| 3 | - Kiểm tra JWT authentication và authorizer của API Gateway.<br>- Kiểm tra request có và không có authentication hợp lệ. | 04/11/2026 | 04/11/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) |
| 4 | - Kiểm tra data isolation giữa các user.<br>- Xác nhận Lambda sử dụng Cognito sub để truy cập đúng dữ liệu của user. | 05/11/2026 | 05/11/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) · [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| 5 | - Rà soát các trang Workshop từ 5.1 đến 5.9.<br>- Kiểm tra tính nhất quán giữa phiên bản tiếng Việt và tiếng Anh. | 06/11/2026 | 06/11/2026 | [The First Cloud Journey](https://cloudjourney.awsstudygroup.com/) |

**Kết quả đạt được tuần 9:**

* Kiểm thử thành công các API CRUD và API summary.
* Kiểm tra thành công JWT authentication và API Gateway authorization.
* Xác nhận dữ liệu của từng user được cô lập thông qua Cognito sub.
* Kiểm tra user này không thể truy cập dữ liệu expense của user khác.
* Kiểm tra DELETE đối với dữ liệu không thuộc quyền sở hữu.
* Rà soát nội dung chính của Workshop từ 5.1 đến 5.9.
* Duy trì tính nhất quán giữa hai phiên bản tiếng Việt và tiếng Anh.
