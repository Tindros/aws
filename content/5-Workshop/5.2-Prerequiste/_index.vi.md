---
title : "Chuẩn bị"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

Trước khi bắt đầu thực hành bài workshop này, vui lòng đảm bảo bạn đã chuẩn bị đầy đủ tài khoản AWS, quyền IAM cần thiết và môi trường lập trình tại máy cục bộ.

---

#### 1. Tài khoản AWS & Lựa chọn Region

+ Một **Tài khoản AWS** đang hoạt động. Sử dụng người dùng IAM Identity Center, IAM role hoặc danh tính khác được quản trị viên phê duyệt, chỉ có các quyền cần thiết cho workshop. Không dùng tài khoản root để thao tác thường ngày.
+ **Vùng triển khai (Region):** Khuyến nghị triển khai toàn bộ tài nguyên của workshop tại **US East (N. Virginia) `us-east-1`** để đảm bảo tính đồng nhất và khả năng tương thích giữa các dịch vụ.

---

#### 2. Phân quyền IAM Cần thiết

Để tạo và cấu hình các dịch vụ AWS trong bài thực hành (Cognito, DynamoDB, Lambda, API Gateway, IAM, CloudWatch và Amplify), người dùng hoặc vai trò IAM của bạn cần được cấp các quyền tương ứng.

Chính sách dưới đây chỉ là ví dụ khởi đầu cho tài khoản học tập riêng, không bảo đảm đầy đủ mọi quyền và cũng chưa phải chính sách least-privilege hoàn chỉnh. Một số thao tác trên AWS Console có thể cần quyền bổ sung; ví dụ này vẫn có quyền API Gateway khá rộng. Ưu tiên dùng IAM role sandbox tạm thời do quản trị viên phê duyệt. Không gán chính sách này cho danh tính production. Trước khi sử dụng, thay mọi placeholder `<ACCOUNT_ID>` bằng AWS Account ID gồm 12 chữ số của bạn.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CognitoManagement",
      "Effect": "Allow",
      "Action": [
        "cognito-idp:CreateUserPool",
        "cognito-idp:DeleteUserPool",
        "cognito-idp:DescribeUserPool",
        "cognito-idp:UpdateUserPool",
        "cognito-idp:CreateUserPoolClient",
        "cognito-idp:DeleteUserPoolClient",
        "cognito-idp:DescribeUserPoolClient",
        "cognito-idp:AdminCreateUser",
        "cognito-idp:AdminSetUserPassword",
        "cognito-idp:AdminDeleteUser",
        "cognito-idp:ListUsers"
      ],
      "Resource": "*"
    },
    {
      "Sid": "DynamoDBManagement",
      "Effect": "Allow",
      "Action": [
        "dynamodb:CreateTable",
        "dynamodb:DeleteTable",
        "dynamodb:DescribeTable",
        "dynamodb:UpdateTable",
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query",
        "dynamodb:Scan"
      ],
      "Resource": "arn:aws:dynamodb:us-east-1:<ACCOUNT_ID>:table/ExpenseTable*"
    },
    {
      "Sid": "LambdaManagement",
      "Effect": "Allow",
      "Action": [
        "lambda:CreateFunction",
        "lambda:DeleteFunction",
        "lambda:GetFunction",
        "lambda:UpdateFunctionCode",
        "lambda:UpdateFunctionConfiguration",
        "lambda:AddPermission",
        "lambda:RemovePermission"
      ],
      "Resource": "arn:aws:lambda:us-east-1:<ACCOUNT_ID>:function:expenseTrackerFunction*"
    },
    {
      "Sid": "ApiGatewayManagement",
      "Effect": "Allow",
      "Action": [
        "apigateway:*"
      ],
      "Resource": "arn:aws:apigateway:us-east-1::*"
    },
    {
      "Sid": "IAMRoleManagement",
      "Effect": "Allow",
      "Action": [
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:GetRole",
        "iam:PassRole",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy",
        "iam:PutRolePolicy",
        "iam:DeleteRolePolicy"
      ],
      "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/ExpenseTrackerLambdaRole*"
    },
    {
      "Sid": "CloudWatchLogsObservability",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:DeleteLogGroup",
        "logs:DescribeLogGroups",
        "logs:DescribeLogStreams",
        "logs:GetLogEvents",
        "logs:FilterLogEvents"
      ],
      "Resource": "arn:aws:logs:us-east-1:<ACCOUNT_ID>:log-group:/aws/lambda/expenseTrackerFunction*:*"
    },
    {
      "Sid": "AmplifyHostingManagement",
      "Effect": "Allow",
      "Action": [
        "amplify:CreateApp",
        "amplify:DeleteApp",
        "amplify:GetApp",
        "amplify:ListApps",
        "amplify:CreateBranch",
        "amplify:StartDeployment"
      ],
      "Resource": "*"
    }
  ]
}
```

{{% notice warning %}}
**Lưu ý quan trọng:** Ví dụ này dùng `apigateway:*` và wildcard cho một số thao tác tạo/liệt kê tài nguyên, vì vậy chưa phải policy least-privilege nghiêm ngặt. Hãy rà soát và thu hẹp quyền cùng quản trị viên trước khi sử dụng. Không cấp quyền này cho tài khoản root hoặc danh tính production.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.2-Prerequiste/prereq-iam-policy.png`  
*(Cần chụp khi triển khai: Giao diện IAM Console hiển thị chính sách cấp quyền thực hành được gán vào tài khoản)*
{{% /notice %}}
<!-- ![Thiết lập IAM Policy](/images/5-Workshop/5.2-Prerequiste/prereq-iam-policy.png) -->

---

#### 3. Môi trường Lập trình Cục bộ

Để kiểm thử mã nguồn giao diện frontend và kiểm tra endpoint API tại máy cục bộ:

1. **Node.js & Trình quản lý Gói:**
   + Cài đặt **Node.js** phiên bản v20.x hoặc v22.x LTS.
   + Kiểm tra phiên bản trên cửa sổ dòng lệnh (terminal):
     ```bash
     node --version
     npm --version
     ```
2. **Mã nguồn Ứng dụng Frontend:**
   + Mã nguồn React nằm trong thư mục `frontend/` của dự án.
   + Xác nhận cài đặt các gói thư viện phụ thuộc:
     ```bash
     cd frontend
     npm install
     ```
3. **Công cụ Kiểm thử API:**
   + **cURL** (tích hợp sẵn trong Windows PowerShell hoặc terminal macOS/Linux) hoặc công cụ như **Postman** / **Thunder Client** để gửi các HTTP request kèm mã JWT token.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.2-Prerequiste/prereq-terminal-env.png`  
*(Cần chụp khi triển khai: Kết quả dòng lệnh hiển thị phiên bản Node.js/npm và mã nguồn frontend đã sẵn sàng)*
{{% /notice %}}
<!-- ![Môi trường cục bộ](/images/5-Workshop/5.2-Prerequiste/prereq-terminal-env.png) -->

---

#### Bước Tiếp theo

Chuyển sang phần [Cấu hình Amazon Cognito & Xác thực Người dùng](../5.3-Cognito/) để khởi tạo User Pool và cấu hình xác thực.
