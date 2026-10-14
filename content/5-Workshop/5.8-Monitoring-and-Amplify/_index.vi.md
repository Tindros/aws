---
title : "Giám sát với CloudWatch & Triển khai Amplify Hosting"
date : 2026-10-14 
weight : 8 
chapter : false
pre : " <b> 5.8. </b> "
---

#### Giám sát Vận hành & Phát hành Ứng dụng Toàn cầu

Việc xây dựng một hệ thống Serverless hoàn thiện đòi hỏi vừa phải có khả năng quan sát (observability) sâu sắc vào quá trình thực thi backend, vừa phải đảm bảo tốc độ tải trang nhanh và ổn định cho người dùng frontend trên toàn cầu.

+ **Amazon CloudWatch:** Do kiến trúc Serverless hoạt động theo các đơn vị tính toán phân tán, Amazon CloudWatch đảm nhận việc thu thập và tập trung hóa nhật ký thực thi, thời gian phản hồi và dấu vết lỗi từ AWS Lambda. Lập trình viên có thể chẩn đoán và khắc phục sự cố tức thì mà không cần truy cập SSH hay cài đặt phần mềm thu thập log phức tạp.
+ **AWS Amplify Hosting:** Dịch vụ lưu trữ web được quản lý toàn diện dành riêng cho các framework hiện đại (React, Vite, Vue, Next.js). Amplify cung cấp mạng phân phối nội dung (CDN) toàn cầu dựa trên Amazon CloudFront, tích hợp quy trình build tự động qua tệp `amplify.yml`, cấp chứng chỉ SSL/TLS miễn phí và hỗ trợ triển khai linh hoạt theo nhánh Git.

---

#### Nội dung Chi tiết

Trong phần này, bạn sẽ theo dõi nhật ký thực thi của Lambda trên CloudWatch và triển khai ứng dụng React lên môi trường thực tế với AWS Amplify:

1. [Theo dõi Nhật ký & Chỉ số Vận hành với CloudWatch](5.8.1-cloudwatch/)
2. [Triển khai Frontend React lên AWS Amplify Hosting](5.8.2-amplify-hosting/)
