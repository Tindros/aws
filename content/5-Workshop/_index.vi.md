---
title: "Workshop"
date: 2026-10-14
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Ứng dụng Theo dõi Chi tiêu Cá nhân Không máy chủ (Serverless Personal Expense Tracker) trên AWS

#### Tổng quan

**Serverless Personal Expense Tracker** là ứng dụng đám mây hỗ trợ ghi chép, phân loại, theo dõi và tổng hợp chi tiêu cá nhân. Ứng dụng sử dụng các dịch vụ AWS được quản lý để bạn không phải tự vận hành máy chủ ảo. Khả năng sẵn sàng, co giãn và chi phí thực tế còn phụ thuộc vào cấu hình và mức sử dụng; kiến trúc serverless không đồng nghĩa mọi tài nguyên đều miễn phí khi nhàn rỗi.

Trong bài workshop thực hành này, bạn sẽ từng bước triển khai và tích hợp toàn diện các thành phần trong kiến trúc:
+ **Quản lý Định danh & Xác thực:** Khởi tạo **Amazon Cognito User Pool** cùng App Client dành cho Single Page Application (SPA), bảo mật bằng cơ chế JWT token.
+ **Thiết kế Cơ sở Dữ liệu:** Xây dựng bảng **Amazon DynamoDB** theo chế độ On-Demand, phân vùng dữ liệu theo từng người dùng dựa trên khóa phân vùng (`userId`) và khóa sắp xếp (`expenseId`).
+ **Xử lý Logic Nghiệp vụ:** Triển khai hàm **AWS Lambda** (Node.js 22.x, AWS SDK v3) được cấp quyền tối thiểu thông qua **IAM Execution Role**.
+ **Cổng Giao tiếp API:** Thiết lập **Amazon API Gateway HTTP API** bảo vệ bằng **JWT Authorizer** liên kết với Cognito, cấu hình CORS và tích hợp Lambda proxy.
+ **Tích hợp Ứng dụng Frontend:** Kết nối giao diện người dùng xây dựng bằng **React + Vite + TypeScript** với luồng xác thực và API thực tế.
+ **Giám sát & Triển khai Hosting:** Thu thập nhật ký và theo dõi chỉ số vận hành với **Amazon CloudWatch**, đồng thời phát hành ứng dụng lên môi trường Internet bằng **AWS Amplify Hosting**.
+ **Kiểm thử & Dọn dẹp:** Thực hiện kiểm thử toàn trình (E2E), xác thực cơ chế chặn truy cập trái phép và tiến hành giải phóng tài nguyên AWS.

{{% notice info %}}
**Lưu ý về Trạng thái Triển khai:** Tài liệu workshop này là hướng dẫn thực hành chi tiết theo kế hoạch kiến trúc của dự án. Tài liệu cung cấp ví dụ triển khai và hướng dẫn cấu hình cần được kiểm chứng trong tài khoản AWS của bạn. Hãy rà soát quyền IAM trước khi áp dụng và chỉ triển khai sau khi đã hiểu chi phí dự kiến cũng như các bước dọn dẹp tài nguyên. Các ảnh chụp màn hình tương ứng với các bước chưa thao tác trên AWS Console được đánh dấu rõ ràng là **`[Pending Deployment]`** và sẽ được bổ sung khi triển khai thực tế.
{{% /notice %}}

#### Nội dung

1. [Tổng quan về Workshop & Kiến trúc](5.1-Workshop-overview/)
2. [Chuẩn bị & Thiết lập Môi trường](5.2-Prerequiste/)
3. [Cấu hình Amazon Cognito & Xác thực Người dùng](5.3-Cognito/)
4. [Thiết kế Bảng Dữ liệu Amazon DynamoDB](5.4-DynamoDB/)
5. [Triển khai Hàm AWS Lambda & IAM Role](5.5-Lambda/)
6. [Cấu hình API Gateway, JWT Authorizer & Tích hợp](5.6-APIGateway/)
7. [Tích hợp Frontend React với Backend AWS](5.7-Frontend-integration/)
8. [Giám sát với CloudWatch & Triển khai Amplify Hosting](5.8-Monitoring-and-Amplify/)
9. [Kiểm thử Toàn trình, Bảo mật & Dọn dẹp Tài nguyên](5.9-Testing-and-Cleanup/)
