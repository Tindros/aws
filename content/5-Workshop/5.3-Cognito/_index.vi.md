---
title : "Cấu hình Amazon Cognito & Xác thực Người dùng"
date : 2026-10-14 
weight : 3 
chapter : false
pre : " <b> 5.3. </b> "
---

#### Vai trò của Amazon Cognito trong Kiến trúc Serverless

Trong một ứng dụng đám mây an toàn, việc quản lý danh tính người dùng, mã hóa mật khẩu, duy trì phiên làm việc và phát hành token xác thực đòi hỏi phải tuân thủ nghiêm ngặt các tiêu chuẩn bảo mật khắt khe. **Amazon Cognito** cung cấp giải pháp quản lý danh tính và quyền truy cập khách hàng (CIAM) được quản lý toàn diện bởi AWS, có khả năng tự động mở rộng phục vụ hàng triệu người dùng mà không cần tự xây dựng bảng lưu trữ hay thuật toán băm mật khẩu phức tạp.

Trong dự án **Serverless Personal Expense Tracker**, Amazon Cognito đảm nhận các vai trò trọng yếu:
+ **Quản lý Thư mục Người dùng:** Cung cấp cơ sở dữ liệu định danh an toàn (User Pool) cho phép người dùng đăng ký và đăng nhập bằng địa chỉ email.
+ **Xác thực Dựa trên Token:** Phát hành các JSON Web Token (JWT) tuân theo chuẩn OpenID Connect (**ID Token** và **Access Token**) chứa định danh bất biến của người dùng (`sub`).
+ **Bảo mật Tách biệt (Decoupled Security):** Trình duyệt của người dùng tương tác trực tiếp với Cognito qua giao thức SRP an toàn. Hệ thống máy chủ backend không bao giờ phải tiếp nhận hay lưu trữ mật khẩu ở dạng rõ (cleartext).
+ **Ủy quyền tại API Gateway:** Các token JWT do Cognito phát hành sẽ được kiểm tra tính hợp lệ trực tiếp tại API Gateway trước khi yêu cầu được chuyển tiếp tới hàm Lambda.

---

#### Nội dung Chi tiết

Trong phần này, bạn sẽ thực hiện các bước:

1. [Tạo Cognito User Pool & App Client](5.3.1-create-user-pool/)
2. [Đăng ký Người dùng, Đăng nhập & Xác minh JWT Token](5.3.2-test-auth/)
