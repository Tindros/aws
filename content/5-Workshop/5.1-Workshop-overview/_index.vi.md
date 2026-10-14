---
title : "Giới thiệu & Kiến trúc"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Bối cảnh Dự án

Quản lý tài chính cá nhân hiệu quả đòi hỏi khả năng ghi chép nhanh chóng, phân loại chi tiêu rõ ràng và cung cấp cái nhìn trực quan về thói quen chi tiêu hàng tháng. Các phương pháp thủ công như sổ tay ghi chép hay bảng tính Excel thường dễ gặp sai sót khi tổng hợp số liệu và thiếu tính linh hoạt khi truy cập trên nhiều thiết bị. Mặt khác, việc duy trì một máy chủ truyền thống luôn hoạt động (như máy chủ EC2 hay cụm container chạy liên tục) sẽ phát sinh chi phí vận hành nhàn rỗi không cần thiết đối với một ứng dụng quy mô cá nhân.

Dự án **Serverless Personal Expense Tracker** giải quyết bài toán này bằng cách ứng dụng kiến trúc Không máy chủ (Serverless) hoàn toàn trên nền tảng điện toán đám mây AWS. Hệ thống tự động cấp phát tài nguyên tính toán và cơ sở dữ liệu chỉ khi có yêu cầu API từ người dùng, giảm nhu cầu tự quản lý máy chủ luôn bật. Chi phí thực tế phụ thuộc vào cấu hình và mức sử dụng; bảo mật phụ thuộc vào việc triển khai và kiểm thử đúng từng cơ chế kiểm soát.

---

#### Kiến trúc Giải pháp

Hệ thống được xây dựng theo mô hình kiến trúc đám mây 3 lớp tách biệt:
1. **Lớp Giao diện & Trình bày (Presentation Layer):** Ứng dụng Single Page Application (SPA) xây dựng trên nền tảng React 19, Vite và TypeScript. Được lưu trữ và phân phối toàn cầu thông qua dịch vụ **AWS Amplify Hosting**.
2. **Lớp Xác thực & Quản lý Cổng API (Authentication & API Management Layer):**
   + **Amazon Cognito:** Quản lý quy trình đăng ký, đăng nhập, chính sách mật khẩu và phát hành mã định danh JSON Web Token (JWT) theo chuẩn công nghiệp.
   + **Amazon API Gateway (HTTP API):** Đóng vai trò cổng API trung tâm, cung cấp các endpoint RESTful, kiểm soát chính sách CORS và xác thực quyền truy cập token người dùng thông qua cơ chế **JWT Authorizer** tích hợp sẵn.
3. **Lớp Tính toán & Lưu trữ Dữ liệu (Compute & Data Persistence Layer):**
   + **AWS Lambda:** Chứa logic nghiệp vụ backend (Node.js 22.x). Kiểm tra dữ liệu đầu vào, đọc claim Cognito `sub` đã được xác thực từ request context của API Gateway và dùng giá trị đó làm partition key `userId` trong DynamoDB cho các thao tác CRUD và tổng hợp.
   + **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL được quản lý hoàn toàn hoạt động ở chế độ On-Demand, lưu trữ hồ sơ chi tiêu được phân vùng an toàn theo danh tính người dùng.
   + **Amazon CloudWatch:** Tự động thu thập nhật ký thực thi Lambda, các chỉ số vận hành hệ thống, tần suất gọi hàm và dấu vết lỗi.
   + **AWS IAM:** Gắn hàm Lambda với Execution Role tuân thủ nguyên tắc cấp quyền tối thiểu (Least Privilege).

![Kiến trúc Serverless Personal Expense Tracker](/images/5-Workshop/5.1-Workshop-overview/architecture.jpeg)

---

#### Luồng Hoạt động & Luân chuyển Dữ liệu

1. **Đăng nhập người dùng:** Người dùng truy cập ứng dụng web được lưu trữ trên AWS Amplify. Trình duyệt gửi thông tin xác thực trực tiếp đến **Amazon Cognito User Pool** thông qua giao thức SRP. Sau khi xác thực thành công, Cognito phản hồi về trình duyệt các token JWT bao gồm `idToken` và `accessToken`.
2. **Gửi yêu cầu API:** Khi người dùng thực hiện các thao tác (ví dụ: thêm khoản chi hoặc xem bảng điều khiển tổng quan), ứng dụng frontend gửi yêu cầu HTTPS đến **Amazon API Gateway** kèm tiêu đề `Authorization: Bearer <ACCESS_TOKEN>`. JWT authorizer trong workshop này được cấu hình để xác thực Cognito Access Token.
3. **Xác thực JWT:** API Gateway tự động giải mã và kiểm tra tính hợp lệ của token với Issuer URL của Cognito User Pool và Client ID. Các yêu cầu thiếu hoặc có token không hợp lệ sẽ được JWT authorizer từ chối bằng HTTP `401 Unauthorized` trước khi gọi Lambda.
4. **Thực thi Hàm Lambda:** Đối với các yêu cầu hợp lệ, API Gateway chuyển tiếp yêu cầu đến hàm **AWS Lambda** qua cơ chế proxy integration, cung cấp thông tin định danh người dùng (`sub`) trong đối tượng ngữ cảnh yêu cầu (request context).
5. **Truy vấn Cơ sở dữ liệu:** Hàm Lambda thực hiện các câu lệnh đọc/ghi vào **Amazon DynamoDB** bằng cách sử dụng giá trị Cognito `sub` đã xác thực làm partition key `userId`.
6. **Ghi nhật ký Vận hành:** Nhật ký thực thi Lambda và chỉ số dịch vụ có thể được xem trong **Amazon CloudWatch** để khắc phục sự cố. Không ghi access token, toàn bộ JWT claims, mật khẩu hoặc dữ liệu nhạy cảm khác vào log.

---

#### Mục tiêu Đạt được sau Workshop

Sau khi hoàn thành bài workshop này, bạn sẽ nắm vững:
- Cách thức cấu hình Amazon Cognito an toàn cho ứng dụng SPA mà không để lộ Client Secret.
- Kỹ thuật thiết kế bảng dữ liệu đơn (Single-table design) trên DynamoDB đáp ứng yêu cầu phân vùng dữ liệu người dùng độc lập.
- Cách lập trình hàm AWS Lambda xử lý các tác vụ REST CRUD và tính toán tổng hợp sử dụng AWS SDK v3.
- Cách thiết lập HTTP API trên API Gateway với chính sách CORS và cơ chế JWT Authorizer.
- Cách thức liên kết ứng dụng frontend React/TypeScript với hệ thống backend đám mây AWS thực tế.
- Giám sát ứng dụng với CloudWatch và triển khai ứng dụng web lên AWS Amplify Hosting.
- Kiểm thử các hàng rào bảo mật và thực hiện quy trình dọn dẹp tài nguyên an toàn.
