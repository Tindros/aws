---
title : "Tạo IAM Lambda Execution Role"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.5.1 </b> "
---

Hàm AWS Lambda sử dụng một vai trò thực thi **IAM Execution Role** trong quá trình chạy để xác định những tài nguyên và dịch vụ AWS nào mà mã nguồn được phép tương tác. Tuân thủ Trụ cột Bảo mật (Security Pillar) của AWS Well-Architected Framework, chúng ta áp dụng **Nguyên tắc Cấp quyền Tối thiểu (Principle of Least Privilege)**: chỉ cấp chính xác các quyền cần thiết để thao tác với DynamoDB và ghi nhật ký ra CloudWatch, có giới hạn phạm vi trực tiếp tới ARN của bảng `ExpenseTable`.

---

#### Bước 1: Bắt đầu Tạo IAM Role

1. Truy cập [AWS IAM Console](https://console.aws.amazon.com/iam/home#/roles).
2. Tại menu bên trái, chọn **Roles**, sau đó bấm **Create role**.
3. Tại mục **Trusted entity type**, chọn **AWS service**.
4. Tại mục **Use case**, chọn dịch vụ **Lambda** từ danh sách các ca sử dụng phổ biến.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-01-iam-role-create.png`  
*(Cần chụp khi triển khai: Giao diện IAM Create Role chọn thực thể tin cậy là dịch vụ Lambda)*
{{% /notice %}}
<!-- ![Tạo IAM Role](/images/5-Workshop/5.5-Lambda/lambda-01-iam-role-create.png) -->

5. Nhấn **Next**.

---

#### Bước 2: Gán Chính sách Quản lý Nhật ký Cơ bản

1. Trong ô tìm kiếm chính sách **Add permissions**, nhập `AWSLambdaBasicExecutionRole`.
2. Đánh dấu tích vào chính sách **AWSLambdaBasicExecutionRole**:
   + Chính sách do AWS quản lý này cấp quyền cho hàm gửi dữ liệu nhật ký đến **Amazon CloudWatch Logs** (`logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`).
3. Nhấn **Next**.

---

#### Bước 3: Đặt Tên & Xem lại Vai trò

1. **Role name:** Nhập `ExpenseTrackerLambdaRole`.
2. **Description:** Nhập `Execution role for Expense Tracker Lambda with DynamoDB access and CloudWatch logging`.
3. Kiểm tra mục **Trust policy** cho phép `lambda.amazonaws.com` đảm nhận vai trò:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "lambda.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```
4. Nhấn **Create role**.

---

#### Bước 4: Thêm Chính sách Nội tuyến (Inline Policy) Truy cập DynamoDB

1. Trong danh sách Roles, bấm vào vai trò vừa tạo: `ExpenseTrackerLambdaRole`.
2. Tại thẻ **Permissions**, bấm **Add permissions** và chọn **Create inline policy**.
3. Tại trình soạn thảo chính sách, chuyển sang thẻ **JSON** và dán đoạn chính sách sau. Thay `<YOUR_ACCOUNT_ID>` bằng AWS Account ID gồm 12 chữ số của bạn trước khi lưu:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ExpenseTableScopedAccess",
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query"
      ],
      "Resource": "arn:aws:dynamodb:us-east-1:<YOUR_ACCOUNT_ID>:table/ExpenseTable"
    }
  ]
}
```

{{% notice tip %}}
**Đánh giá An toàn Bảo mật:**
+ Chính sách giới hạn chỉ các thao tác CRUD cơ bản và lệnh `Query` phân vùng. Các thao tác nguy hiểm như xóa bảng (`dynamodb:DeleteTable`) hay quét toàn bộ dữ liệu (`dynamodb:Scan`) đều bị chặn hoàn toàn.
+ ARN trong `Resource` giới hạn quyền vào bảng `ExpenseTable` trong vùng `us-east-1` và tài khoản của bạn. Hãy thay placeholder bằng Account ID thật; không để `*` ở phần tài khoản.
{{% /notice %}}

4. Nhấn **Next**.
5. **Policy name:** Nhập `ExpenseTrackerDynamoDBPolicy`.
6. Nhấn **Create policy**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-02-iam-policies.png`  
*(Cần chụp khi triển khai: Thẻ Permissions của ExpenseTrackerLambdaRole hiển thị đầy đủ các chính sách)*
{{% /notice %}}
<!-- ![Chính sách IAM đã gán](/images/5-Workshop/5.5-Lambda/lambda-02-iam-policies.png) -->
