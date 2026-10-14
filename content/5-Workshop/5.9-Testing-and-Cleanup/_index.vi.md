---
title : "Kiểm thử Toàn trình, Bảo mật & Dọn dẹp Tài nguyên"
date : 2026-10-14 
weight : 9 
chapter : false
pre : " <b> 5.9. </b> "
---

#### Xác thực Hệ thống & Giải phóng Tài nguyên An toàn

Trước khi kết thúc bài workshop, chúng ta cần kiểm tra toàn diện để đảm bảo kiến trúc Serverless đã đáp ứng đầy đủ cả các tiêu chí chức năng lẫn các hàng rào an toàn bảo mật. Cuối cùng, việc thực hiện dọn dẹp tài nguyên theo đúng quy trình sẽ ngăn chặn phát sinh các chi phí không mong muốn trên tài khoản AWS của bạn.

+ **Kiểm thử Toàn trình & Bảo mật:** Kiểm tra API Gateway có thực thi JWT authorization và trả về `401 Unauthorized` cho yêu cầu thiếu hoặc có token không hợp lệ hay không; đồng thời kiểm tra các thao tác DynamoDB chỉ truy cập dữ liệu thuộc người dùng đã xác thực.
+ **Dọn dẹp Tài nguyên AWS:** Cung cấp danh mục các bước hủy bỏ tài nguyên tuần tự theo đúng thứ tự phụ thuộc qua cả 7 dịch vụ AWS đã cấu hình (Amplify App, API Gateway HTTP API, Lambda function, IAM Role & policies, DynamoDB table, Cognito User Pool và CloudWatch Log Groups).

---

#### Nội dung Chi tiết

1. [Kiểm thử Toàn trình & Xác minh Bảo mật](5.9.1-testing/)
2. [Dọn dẹp Tài nguyên AWS](5.9.2-cleanup/)
