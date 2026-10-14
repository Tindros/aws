---
title : "Theo dõi Nhật ký & Chỉ số Vận hành với CloudWatch"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.8.1 </b> "
---

Khả năng quan sát (Observability) là yếu tố không thể thiếu để phát hiện lỗi, theo dõi độ trễ của API và kiểm soát tài nguyên tiêu thụ trong các hệ thống Serverless. Trong phần này, bạn sẽ kiểm tra nhật ký thực thi và phân tích các chỉ số vận hành bằng **Amazon CloudWatch**.

---

#### Bước 1: Xác định Nhóm Nhật ký (Log Group) của Lambda

1. Truy cập [Amazon CloudWatch Console](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1).
2. Tại menu bên trái, mở rộng mục **Logs**, sau đó chọn **Log groups**.
3. Trong ô tìm kiếm, nhập `/aws/lambda/expenseTrackerFunction`:
   + Nhóm nhật ký này được AWS tự động khởi tạo khi hàm Lambda thực hiện lệnh ghi log đầu tiên.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-01-log-groups.png`  
*(Cần chụp khi triển khai: Danh sách CloudWatch Log groups hiển thị nhóm /aws/lambda/expenseTrackerFunction)*
{{% /notice %}}
<!-- ![Nhóm Nhật ký CloudWatch](/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-01-log-groups.png) -->

---

#### Bước 2: Kiểm tra Nhật ký Chi tiết của Yêu cầu

1. Bấm chọn nhóm `/aws/lambda/expenseTrackerFunction`.
2. Tại thẻ **Log streams**, bấm vào luồng nhật ký mới nhất ở trên cùng.
3. Quan sát các dòng nhật ký có cấu trúc được xuất ra trong quá trình xử lý yêu cầu:
   + **Thông tin Yêu cầu:** Hiển thị `routeKey`, `httpMethod` và mã định danh Cognito `sub` đã giải mã.
   + **Dấu vết Thực thi:** Thể hiện các lệnh tương tác với DynamoDB (`QueryCommand`, `PutCommand`).
   + **Dòng Báo cáo Tiêu chuẩn (REPORT line):** Cung cấp các thông số hiệu năng và chi phí cho mỗi lượt gọi hàm:
     ```
     REPORT RequestId: 4f1a2b3c-... Duration: 42.15 ms Billed Duration: 43 ms Memory Size: 128 MB Max Memory Used: 78 MB
     ```

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-02-log-stream-event.png`  
*(Cần chụp khi triển khai: Chi tiết luồng sự kiện CloudWatch hiển thị ngữ cảnh yêu cầu và dòng REPORT)*
{{% /notice %}}
<!-- ![Chi tiết Luồng Nhật ký](/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-02-log-stream-event.png) -->

---

#### Bước 3: Theo dõi Chỉ số Vận hành (Metrics) của Lambda

1. Tại menu bên trái của CloudWatch, chọn **Metrics**, sau đó chọn **All metrics**.
2. Chọn **Lambda** -> **By Function Name**.
3. Tìm kiếm hàm `expenseTrackerFunction` và tích chọn các chỉ số cốt lõi:
   + **Invocations:** Tổng số lượt gọi thực thi hàm từ các yêu cầu API.
   + **Duration:** Thời gian thực thi trung bình tính bằng mili-giây (thường giảm xuống dưới 30ms sau các lượt gọi warm-up).
   + **Errors:** Số lượng lỗi ngoại lệ chưa xử lý (luôn được duy trì ở mức 0).
   + **Throttles:** Đánh dấu các trường hợp chạm ngưỡng giới hạn thực thi đồng thời.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-03-lambda-metrics.png`  
*(Cần chụp khi triển khai: Biểu đồ CloudWatch Metrics thể hiện Invocations, Duration và Errors)*
{{% /notice %}}
<!-- ![Chỉ số Vận hành Lambda](/images/5-Workshop/5.8-Monitoring-and-Amplify/cloudwatch-03-lambda-metrics.png) -->

{{% notice tip %}}
**Đánh giá Hiệu năng:** Do cả DynamoDB và Lambda đều tự động co giãn theo nhu cầu, tốc độ phản hồi của hệ thống luôn duy trì ở mức rất nhanh và ổn định ngay cả khi có nhiều người dùng thao tác đồng thời.
{{% /notice %}}
