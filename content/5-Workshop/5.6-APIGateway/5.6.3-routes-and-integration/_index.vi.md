---
title : "Tạo Tuyến đường (Routes) & Tích hợp Lambda Proxy"
date : 2026-10-14 
weight : 3 
chapter : false
pre : " <b> 5.6.3 </b> "
---

Trong phần này, bạn sẽ định nghĩa các tuyến đường (routes) RESTful cho ứng dụng theo dõi chi tiêu, kết nối các tuyến đường này với hàm `expenseTrackerFunction` thông qua cơ chế tích hợp Lambda proxy và bảo vệ toàn bộ API bằng `CognitoJWTAuthorizer`.

---

#### Sơ đồ Ánh xạ Tuyến đường API

| Phương thức HTTP | Đường dẫn Tuyến đường | Mục đích Xử lý | Cơ chế Ủy quyền |
| :--- | :--- | :--- | :--- |
| `GET` | `/expenses` | Lấy danh sách toàn bộ chi tiêu của người dùng | `CognitoJWTAuthorizer` |
| `POST` | `/expenses` | Tạo mới một bản ghi chi tiêu vào DynamoDB | `CognitoJWTAuthorizer` |
| `GET` | `/expenses/{id}` | Lấy thông tin chi tiết một khoản chi theo ID | `CognitoJWTAuthorizer` |
| `PUT` | `/expenses/{id}` | Cập nhật thông tin khoản chi | `CognitoJWTAuthorizer` |
| `DELETE` | `/expenses/{id}` | Xóa một khoản chi | `CognitoJWTAuthorizer` |
| `GET` | `/summary` | Tính toán số liệu tổng hợp chi tiêu & phân loại | `CognitoJWTAuthorizer` |

---

#### Bước 1: Khởi tạo các Tuyến đường (Routes)

1. Tại [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1), mở `ExpenseTrackerApi`.
2. Tại menu bên trái dưới mục **Develop**, chọn **Routes**.
3. Nhấn **Create**:
   + Phương thức (Method): Chọn `GET`
   + Đường dẫn (Path): Nhập `/expenses`
   + Nhấn **Create**.
4. Lặp lại thao tác trên để tạo lần lượt 5 tuyến đường còn lại:
   + `POST /expenses`
   + `GET /expenses/{id}`
   + `PUT /expenses/{id}`
   + `DELETE /expenses/{id}`
   + `GET /summary`

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png`  
*(Cần chụp khi triển khai: Bảng Routes hiển thị đầy đủ 6 tuyến đường API đã khởi tạo)*
{{% /notice %}}
<!-- ![Danh sách Routes](/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png) -->

---

#### Bước 2: Gán Tích hợp Lambda Proxy (Lambda Integration)

1. Tại menu bên trái, chọn **Integrations**.
2. Chọn thẻ **Manage integrations**, sau đó bấm **Create**:
   + **Integration target:** Chọn **Lambda function**.
   + **Integration method:** Chọn **POST** (phương thức gọi nội bộ tiêu chuẩn của Lambda proxy).
   + **Lambda function:** Chọn `expenseTrackerFunction`.
   + Đảm bảo **Payload format version** là `2.0`.
3. Nhấn **Create**.
4. Quay lại danh sách **Routes**. Chọn từng route (`GET /expenses`, `POST /expenses`, v.v.), nhấn **Attach integration** và chọn `expenseTrackerFunction`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png`  
*(Cần chụp khi triển khai: Gán tích hợp Lambda proxy vào các tuyến đường)*
{{% /notice %}}
<!-- ![Tích hợp Lambda](/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png) -->

{{% notice note %}}
**Xác minh Quyền Gọi Hàm của API Gateway:**
Khi bạn tạo tích hợp Lambda thông qua AWS Management Console, API Gateway sẽ tự động tạo một **Lambda Resource-Based Policy** (Chính sách dựa trên tài nguyên) cấp quyền cho dịch vụ API Gateway (`apigateway.amazonaws.com`) thực thi hành động `lambda:InvokeFunction`.

Bạn có thể kiểm tra quyền này trong [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions) tại mục **Configuration** -> **Permissions** -> **Resource-based policy statements**.

Nếu cấu hình qua AWS CLI hoặc nếu bị thiếu quyền, bạn có thể cấp quyền gọi hàm có phạm vi giới hạn chính xác cho HTTP API của mình bằng lệnh:
```bash
aws lambda add-permission \
  --function-name expenseTrackerFunction \
  --statement-id apigateway-invoke-permission \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:us-east-1:<YOUR_ACCOUNT_ID>:<API_ID>/*/*"
```
{{% /notice %}}

---

#### Bước 3: Gán JWT Authorizer Bảo vệ các Tuyến đường

1. Tại menu bên trái dưới mục **Develop**, chọn **Authorization**.
2. Trong thẻ **Routes**, bấm chọn tuyến đường đầu tiên: `GET /expenses`.
3. Tại ô **Attach an authorizer to this route**, chọn `CognitoJWTAuthorizer`, sau đó nhấn **Attach authorizer**.
4. Lặp lại thao tác gán authorizer này cho toàn bộ các tuyến đường còn lại (`POST /expenses`, `GET /expenses/{id}`, `PUT /expenses/{id}`, `DELETE /expenses/{id}` và `GET /summary`).

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png`  
*(Cần chụp khi triển khai: Bảng Authorization hiển thị CognitoJWTAuthorizer được gán cho toàn bộ routes)*
{{% /notice %}}
<!-- ![Gán Authorizer](/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png) -->

---

#### Bước 4: Lấy Địa chỉ Invoke URL của Stage

1. Tại menu bên trái dưới mục **Deploy**, chọn **Stages**.
2. Chọn stage `$default`.
3. Tìm và sao chép đường dẫn **Invoke URL**:
   + Định dạng: `https://<API_ID>.execute-api.us-east-1.amazonaws.com`

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png`  
*(Cần chụp khi triển khai: Trang tổng quan Stage hiển thị địa chỉ công khai Invoke URL)*
{{% /notice %}}
<!-- ![Invoke URL của Stage](/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png) -->

{{% notice tip %}}
**Lưu ý quan trọng:** Hãy ghi lại địa chỉ Invoke URL này, đây là endpoint gốc mà ứng dụng frontend React sẽ gọi đến trong phần tiếp theo.
{{% /notice %}}

---

#### Bước Tiếp theo

Chuyển sang phần [Tích hợp Frontend React với Backend AWS](../../5.7-Frontend-integration/) để kết nối giao diện người dùng với hạ tầng backend đám mây.
