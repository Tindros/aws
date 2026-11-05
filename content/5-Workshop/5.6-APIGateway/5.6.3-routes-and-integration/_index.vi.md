---
title : "Tạo Tuyến đường (Routes) & Tích hợp Lambda Proxy"
date : 2026-10-14
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---

Trong bài này, bạn sẽ tạo sáu tuyến đường HTTP API cho ứng dụng Expense Tracker, kết nối từng tuyến đường với hàm Lambda `expenseTrackerFunction` đã tạo và yêu cầu tất cả các tuyến đường ứng dụng sử dụng `CognitoJWTAuthorizer`.

Trước khi bắt đầu, hãy đảm bảo bạn đã hoàn thành:

- [Tạo HTTP API & Cấu hình CORS](../5.6.1-create-http-api/).
- [Cấu hình Cognito JWT Authorizer](../5.6.2-jwt-authorizer/).
- [Tạo hàm Lambda](../../5.5-Lambda/).

---

#### Bảng Ánh xạ Tuyến đường API

Tạo chính xác các route key như bảng dưới đây. Phương thức HTTP và đường dẫn phải khớp với những route mà hàm Lambda xử lý.

| Phương thức HTTP | Đường dẫn | Mục đích |
| :--- | :--- | :--- |
| `GET` | `/expenses` | Lấy danh sách chi tiêu của người dùng đã xác thực |
| `POST` | `/expenses` | Tạo một khoản chi tiêu mới |
| `GET` | `/expenses/{id}` | Lấy một khoản chi tiêu theo ID |
| `PUT` | `/expenses/{id}` | Cập nhật một khoản chi tiêu |
| `DELETE` | `/expenses/{id}` | Xóa một khoản chi tiêu |
| `GET` | `/summary` | Lấy báo cáo tổng hợp chi tiêu của người dùng đã xác thực |

Cả sáu tuyến đường đều phải sử dụng `CognitoJWTAuthorizer`. Không tạo route công khai cho các thao tác này.

---

#### Bước 1: Tạo các API Routes

1. Mở [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
2. Chọn API `ExpenseTrackerApi`.
3. Trong menu điều hướng, tại mục **Develop**, chọn **Routes**.
4. Nhấn **Create**.
5. Cấu hình tuyến đường đầu tiên:
   - **Method:** `GET`
   - **Path:** `/expenses`
6. Nhấn **Create**.
7. Lặp lại thao tác để tạo năm route key còn lại:
   - `POST /expenses`
   - `GET /expenses/{id}`
   - `PUT /expenses/{id}`
   - `DELETE /expenses/{id}`
   - `GET /summary`

Kiểm tra danh sách Routes và đảm bảo cả sáu route key xuất hiện chính xác như bảng phía trên.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png`  
*(Chụp trang Routes hiển thị đầy đủ sáu route key sau khi tạo.)*
{{% /notice %}}
<!-- ![Danh sách Routes](/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png) -->

---

#### Bước 2: Tạo Lambda Proxy Integration

1. Trong menu điều hướng của API, chọn **Integrations**.
2. Mở **Manage integrations**, sau đó nhấn **Create**.
3. Cấu hình integration:
   - **Integration type / target:** Chọn **Lambda function**.
   - **Lambda function:** Chọn `expenseTrackerFunction` trong cùng AWS Region.
   - **Payload format version:** Chọn `2.0`.
4. Nếu giao diện yêu cầu chọn integration method, giữ phương thức gọi Lambda là `POST`. Đây là phương thức API Gateway sử dụng nội bộ để gọi Lambda; nó không làm thay đổi các HTTP method mà API route cung cấp cho client.
5. Nhấn **Create**.

{{% notice note %}}
**Tại sao sử dụng payload format version 2.0?** Lambda handler trong workshop này đọc các trường của HTTP API v2 event, bao gồm `event.requestContext.http.method`, `event.rawPath`, `event.pathParameters` và `event.requestContext.authorizer.jwt.claims.sub`. Vì vậy integration cần sử dụng payload format version `2.0`.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png`  
*(Chụp thông tin Lambda integration, thể hiện expenseTrackerFunction và payload format version 2.0.)*
{{% /notice %}}
<!-- ![Lambda Integration](/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png) -->

---

#### Bước 3: Gán Integration cho Tất cả Routes

Việc tạo integration không tự động kết nối integration đó với mọi route. Bạn cần gán integration cho từng route:

1. Quay lại **Develop → Routes**.
2. Chọn route `GET /expenses`.
3. Trong phần chi tiết route, chọn **Attach integration**.
4. Chọn integration của `expenseTrackerFunction`, sau đó xác nhận.
5. Lặp lại thao tác cho năm route còn lại:
   - `POST /expenses`
   - `GET /expenses/{id}`
   - `PUT /expenses/{id}`
   - `DELETE /expenses/{id}`
   - `GET /summary`

Sau khi gán xong, mở từng route để xác minh integration đều trỏ đến `expenseTrackerFunction`. Mỗi route phải có integration thì mới có thể gọi hàm Lambda.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-05b-route-integrations.png`  
*(Chụp trang Routes hoặc chi tiết route thể hiện Lambda integration đã được gán.)*
{{% /notice %}}

---

#### Bước 4: Xác minh API Gateway Có Quyền Gọi Lambda

Khi tạo Lambda integration bằng API Gateway Console, thông thường console sẽ tự động thêm resource-based permission vào hàm Lambda để API Gateway có thể gọi hàm đó.

Để kiểm tra quyền:

1. Mở [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
2. Chọn `expenseTrackerFunction`.
3. Mở **Configuration → Permissions**.
4. Trong mục **Resource-based policy statements**, tìm policy cho phép service principal `apigateway.amazonaws.com` thực hiện hành động `lambda:InvokeFunction`.

Nếu bạn tạo integration bằng AWS Console và quyền này đã tồn tại, **không cần thêm permission khác**. Nếu bạn tạo integration bằng phương thức khác hoặc permission còn thiếu, hãy thêm quyền với phạm vi giới hạn cho API và stage mặc định. Thay các placeholder bằng thông tin của bạn:

```bash
aws lambda add-permission \
  --function-name expenseTrackerFunction \
  --statement-id apigateway-invoke-expensetracker-api \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:us-east-1:<YOUR_ACCOUNT_ID>:<API_ID>/$default/*/*"
```

{{% notice warning %}}
Chỉ chạy lệnh khi permission cần thiết đang bị thiếu. Giá trị `statement-id` phải là duy nhất trong resource policy của hàm Lambda. Không thay thế permission đang có hoặc mở rộng source ARN quá mức cần thiết.
{{% /notice %}}

---

#### Bước 5: Gán Cognito JWT Authorizer cho Từng Route

Authorizer cần được gán riêng cho từng route. Việc tạo authorizer trong Phần 5.6.2 không tự động bảo vệ các route.

1. Trong menu điều hướng của API, tại mục **Develop**, chọn **Authorization**.
2. Mở thẻ **Routes**.
3. Chọn route `GET /expenses`.
4. Trong phần cài đặt authorizer của route đã chọn, chọn `CognitoJWTAuthorizer`.
5. Nhấn **Attach authorizer**.
6. Lặp lại cho năm route còn lại:
   - `POST /expenses`
   - `GET /expenses/{id}`
   - `PUT /expenses/{id}`
   - `DELETE /expenses/{id}`
   - `GET /summary`

Quay lại trang Routes trong phần Authorization và xác nhận cả sáu route đều hiển thị `CognitoJWTAuthorizer`, không phải `NONE`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png`  
*(Chụp phần cấu hình Authorization thể hiện CognitoJWTAuthorizer đã được gán cho cả sáu route.)*
{{% /notice %}}
<!-- ![Gán Authorizer](/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png) -->

---

#### Bước 6: Kiểm tra lại CORS và Default Stage

1. Mở **Develop → CORS** và kiểm tra lại cấu hình đã thực hiện ở Phần 5.6.1.
2. Đảm bảo danh sách allowed origins có origin frontend chính xác mà bạn sẽ sử dụng, ví dụ `http://localhost:5173` và domain Amplify sau khi triển khai.
3. Đảm bảo allowed headers có `Authorization` và `Content-Type`; allowed methods có `GET`, `POST`, `PUT` và `DELETE`.
4. Khi CORS đã được cấu hình cho HTTP API, API Gateway sẽ xử lý các yêu cầu preflight. Workshop này không cần tạo riêng một route ứng dụng `OPTIONS`.
5. Trong **Deploy → Stages**, chọn `$default` và xác nhận **Auto-deploy** đang được bật.

{{% notice note %}}
Khi CORS được cấu hình ở cấp API, API Gateway sẽ quản lý phản hồi preflight. Hãy giới hạn allowed origins ở các domain frontend mà bạn tin cậy. Hàm Lambda trong workshop này không cần tự thêm CORS response headers.
{{% /notice %}}

---

#### Bước 7: Sao chép API Invoke URL

1. Trong menu điều hướng, tại mục **Deploy**, chọn **Stages**.
2. Chọn stage `$default`.
3. Sao chép **Invoke URL**. URL có định dạng:

```text
https://<API_ID>.execute-api.us-east-1.amazonaws.com
```

4. Lưu lại giá trị này. Frontend React sẽ sử dụng nó làm API base URL trong Phần 5.7.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png`  
*(Chụp trang tổng quan stage $default hiển thị Invoke URL.)*
{{% /notice %}}
<!-- ![Invoke URL của Stage](/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png) -->

---

#### Danh sách Kiểm tra Cấu hình

Trước khi chuyển sang phần tiếp theo, hãy xác minh:

- [ ] API có tên `ExpenseTrackerApi` và sử dụng stage `$default`.
- [ ] Cả sáu route key khớp với bảng ánh xạ API.
- [ ] Tất cả route đều được gán Lambda integration `expenseTrackerFunction`.
- [ ] Lambda integration sử dụng payload format version `2.0`.
- [ ] Tất cả route đều sử dụng `CognitoJWTAuthorizer`; không có route chi tiêu nào để authorization type là `NONE`.
- [ ] API Gateway có quyền gọi `expenseTrackerFunction`.
- [ ] CORS cho phép frontend origin, hai header `Authorization` và `Content-Type`, cùng các HTTP method cần thiết.
- [ ] Đã lưu API Invoke URL để cấu hình frontend.

{{% notice warning %}}
**Lưu ý về kiểm thử:** Danh sách này chỉ xác nhận các bước cấu hình, không có nghĩa API đã được triển khai hoặc kiểm thử trên tài khoản AWS thực tế. Sau khi hoàn thành tích hợp frontend, hãy thực hiện các bài kiểm thử request có token hợp lệ, thiếu token, token không hợp lệ và cô lập dữ liệu giữa người dùng tại Phần 5.9. Không đánh dấu screenshot hoặc kiểm thử thực tế là hoàn thành nếu bạn chưa thực sự thực hiện.
{{% /notice %}}

---

#### Bước Tiếp theo

Chuyển sang phần [Tích hợp Frontend React](../../5.7-Frontend-integration/) để cấu hình frontend gọi API bằng Cognito Access Token.
