---
title : "Dọn dẹp Tài nguyên AWS"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.9.2 </b> "
---

Chúc mừng bạn đã hoàn thành bài workshop **Serverless Personal Expense Tracker**! 

Qua bài thực hành này, bạn đã thiết kế và triển khai một hệ sinh thái đám mây không máy chủ hoàn chỉnh đạt chuẩn doanh nghiệp:
+ Quản lý danh tính người dùng và cấp phát token JWT qua **Amazon Cognito**.
+ Cơ sở dữ liệu tài liệu NoSQL phân vùng hiệu năng cao với **Amazon DynamoDB**.
+ Xử lý logic nghiệp vụ hướng sự kiện bằng **AWS Lambda** dưới sự kiểm soát của chính sách **IAM phân quyền tối thiểu**.
+ Cổng giao tiếp RESTful, quản lý CORS và bảo vệ vành đai bằng JWT Authorizer trên **Amazon API Gateway**.
+ Giám sát vận hành và theo dõi nhật ký thực thi thông qua **Amazon CloudWatch**.
+ Phát hành ứng dụng ra Internet toàn cầu với quy trình tự động hóa của **AWS Amplify Hosting**.

Để tránh phát sinh chi phí duy trì tài nguyên lưu trữ và các điểm cuối dịch vụ trên tài khoản AWS của bạn, hãy thực hiện quy trình dọn dẹp theo đúng thứ tự phụ thuộc dưới đây.

---

#### Bước 1: Xóa Ứng dụng AWS Amplify

1. Truy cập [AWS Amplify Console](https://us-east-1.console.aws.amazon.com/amplify/home?region=us-east-1).
2. Chọn ứng dụng `expense-tracker-frontend`.
3. Tại menu bên trái, chọn **App settings** -> **General**.
4. Cuộn xuống cuối trang và bấm **Delete app**.
5. Nhập chữ `delete` để xác nhận và nhấn nút **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-01-delete-amplify.png`  
*(Cần chụp khi triển khai: Thao tác xóa ứng dụng frontend trên giao diện AWS Amplify Console)*
{{% /notice %}}
<!-- ![Xóa Amplify App](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-01-delete-amplify.png) -->

---

#### Bước 2: Xóa Amazon API Gateway HTTP API

1. Truy cập [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
2. Tìm `ExpenseTrackerApi` trong danh sách các API.
3. Đánh dấu chọn vào ô vuông bên cạnh tên `ExpenseTrackerApi`.
4. Bấm nút **Delete** trên thanh công cụ phía trên.
5. Xác nhận xóa bằng cách nhập tên API hoặc nhấn **Confirm**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-02-delete-apigw.png`  
*(Cần chụp khi triển khai: Thao tác xóa ExpenseTrackerApi trên API Gateway Console)*
{{% /notice %}}
<!-- ![Xóa API Gateway](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-02-delete-apigw.png) -->

---

#### Bước 3: Xóa Hàm AWS Lambda

1. Truy cập [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
2. Tích chọn hàm `expenseTrackerFunction`.
3. Chọn menu **Actions** -> **Delete**.
4. Nhập chữ `delete` vào hộp thoại xác nhận và bấm **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-03-delete-lambda.png`  
*(Cần chụp khi triển khai: Thao tác xóa hàm expenseTrackerFunction trên Lambda Console)*
{{% /notice %}}
<!-- ![Xóa Hàm Lambda](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-03-delete-lambda.png) -->

---

#### Bước 4: Xóa IAM Role & Chính sách

1. Truy cập [AWS IAM Console](https://console.aws.amazon.com/iam/home#/roles).
2. Tìm kiếm và bấm chọn vai trò `ExpenseTrackerLambdaRole`.
3. Tại thẻ **Permissions**:
   + Mở rộng chính sách nội tuyến `ExpenseTrackerDynamoDBPolicy` và bấm **Delete**.
   + Tích chọn chính sách `AWSLambdaBasicExecutionRole` và bấm **Remove**.
4. Tại góc trên cùng của trang thông tin vai trò, bấm nút **Delete role**.
5. Xác nhận xóa vai trò.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-04-delete-iam-role.png`  
*(Cần chụp khi triển khai: Thao tác xóa vai trò ExpenseTrackerLambdaRole trên IAM Console)*
{{% /notice %}}
<!-- ![Xóa IAM Role](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-04-delete-iam-role.png) -->

---

#### Bước 5: Xóa Bảng Amazon DynamoDB

1. Truy cập [Amazon DynamoDB Console](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables).
2. Trong danh sách Tables, tích chọn bảng `ExpenseTable`.
3. Nhấn nút **Delete**.
4. Nhập `confirm` (hoặc `delete`) vào ô xác nhận và chọn **Delete table**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-05-delete-dynamodb.png`  
*(Cần chụp khi triển khai: Thao tác xóa bảng ExpenseTable trên DynamoDB Console)*
{{% /notice %}}
<!-- ![Xóa Bảng DynamoDB](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-05-delete-dynamodb.png) -->

---

#### Bước 6: Xóa Amazon Cognito User Pool

1. Truy cập [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
2. Tích chọn `expense-tracker-user-pool`.
3. Chọn mục **Delete user pool**.
4. Nhập tên user pool `expense-tracker-user-pool` vào ô xác nhận và bấm **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-06-delete-cognito.png`  
*(Cần chụp khi triển khai: Thao tác xóa user pool expense-tracker-user-pool trên Cognito Console)*
{{% /notice %}}
<!-- ![Xóa Cognito User Pool](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-06-delete-cognito.png) -->

---

#### Bước 7: Xóa Nhóm Nhật ký CloudWatch

1. Truy cập [Amazon CloudWatch Console](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1).
2. Tại menu bên trái, mở rộng mục **Logs** và chọn **Log groups**.
3. Tích chọn ô vuông bên cạnh nhóm `/aws/lambda/expenseTrackerFunction`.
4. Chọn menu **Actions** -> **Delete log group(s)**.
5. Xác nhận xóa bằng cách nhấn **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-07-delete-cloudwatch.png`  
*(Cần chụp khi triển khai: Thao tác xóa nhóm nhật ký /aws/lambda/expenseTrackerFunction)*
{{% /notice %}}
<!-- ![Xóa CloudWatch Log Group](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-07-delete-cloudwatch.png) -->

{{% notice tip %}}
**Hoàn tất Dọn dẹp:** Toàn bộ các tài nguyên đám mây đã được xóa sạch hoàn toàn, đảm bảo không phát sinh bất kỳ khoản phí ngoài ý muốn nào trên tài khoản AWS của bạn.
{{% /notice %}}
