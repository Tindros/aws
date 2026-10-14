---
title : "Kiểm thử Toàn trình & Xác minh Bảo mật"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.9.1 </b> "
---

Trong phần này, bạn sẽ thực hiện một chuỗi các kịch bản kiểm thử chức năng và bảo mật nhằm xác minh cơ chế ủy quyền, xử lý mã lỗi và chứng minh tính cô lập dữ liệu tuyệt đối giữa các người dùng.

---

#### Kịch bản 1: Xác thực Yêu cầu Hợp lệ (Authorized Call)

Kiểm tra một yêu cầu API mang token JWT hợp lệ từ Cognito có thể truy cập thành công vào hàm Lambda và đọc dữ liệu từ DynamoDB:

1. Lấy **Access Token** mới từ phiên Cognito đang hoạt động. Gửi token trong tiêu đề `Authorization: Bearer <ACCESS_TOKEN>` khi gọi API được bảo vệ.
2. Mở cửa sổ dòng lệnh (terminal) và gửi một yêu cầu HTTP `GET` đến Invoke URL của API Gateway:

```bash
curl -i -X GET "https://<API_ID>.execute-api.us-east-1.amazonaws.com/expenses" \
  -H "Authorization: Bearer <VALID_ID_TOKEN>"
```

3. **Kết quả Kỳ vọng:**
   + Mã trạng thái HTTP: `200 OK`.
   + Tiêu đề phản hồi chứa các trường CORS (`Access-Control-Allow-Origin: *`).
   + Thân phản hồi chứa mảng JSON gồm các khoản chi tiêu của người dùng được truy xuất từ DynamoDB.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/test-01-curl-authorized.png`  
*(Cần chụp khi triển khai: Terminal hoặc Postman hiển thị phản hồi HTTP 200 OK khi gửi kèm token JWT hợp lệ)*
{{% /notice %}}
<!-- ![Xác thực Yêu cầu Hợp lệ](/images/5-Workshop/5.9-Testing-and-Cleanup/test-01-curl-authorized.png) -->

---

#### Kịch bản 2: Chặn Yêu cầu Không Hợp lệ (Unauthorized Request)

Kiểm tra khả năng của API Gateway trong việc từ chối các yêu cầu thiếu thông tin xác thực hoặc token giả mạo:

1. Gửi một yêu cầu HTTP `GET` không có tiêu đề `Authorization`:

```bash
curl -i -X GET "https://<API_ID>.execute-api.us-east-1.amazonaws.com/expenses"
```

2. **Kết quả Kỳ vọng:**
   + Mã trạng thái HTTP: `401 Unauthorized`.
   + Thân phản hồi: `{"message":"Unauthorized"}`.

3. Gửi yêu cầu kèm một token bị sửa đổi hoặc đã hết hạn:

```bash
curl -i -X GET "https://<API_ID>.execute-api.us-east-1.amazonaws.com/expenses" \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.invalid.token"
```

4. **Kết quả Kỳ vọng:**
   + Mã trạng thái HTTP: `401 Unauthorized`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/test-02-curl-unauthorized.png`  
*(Cần chụp khi triển khai: Terminal hiển thị lỗi HTTP 401 Unauthorized khi gửi request thiếu hoặc sai token)*
{{% /notice %}}
<!-- ![Chặn Yêu cầu Trái phép](/images/5-Workshop/5.9-Testing-and-Cleanup/test-02-curl-unauthorized.png) -->

{{% notice tip %}}
**Không Tiêu Tốn Chi phí Tính toán:** Mở CloudWatch Logs của hàm `expenseTrackerFunction`. Bạn sẽ thấy không có bất kỳ luồng log nào phát sinh từ các yêu cầu bị chặn 401. API Gateway đã chủ động từ chối yêu cầu ngay tại vành đai bảo vệ, không kích hoạt hàm Lambda và không phát sinh chi phí tính toán.
{{% /notice %}}

---

#### Kịch bản 3: Xác minh Tính Cô lập Dữ liệu Người dùng (Data Isolation)

Chứng minh rằng người dùng không thể xem, chỉnh sửa hay xóa các khoản chi tiêu của người dùng khác:

1. Trong Cognito Console, tạo thêm người dùng thứ hai: `user-b@example.com`.
2. Đăng nhập với tài khoản `user-b@example.com` trên giao diện ứng dụng frontend.
3. Thêm một khoản chi mới (ví dụ: Mô tả: `Ăn trưa của User B`, Số tiền: `20.00`).
4. Đăng xuất và đăng nhập lại bằng tài khoản ban đầu: `demo-user@example.com`.
5. Quan sát bảng **Expenses** và **Dashboard**:
   + `demo-user@example.com` chỉ nhìn thấy các khoản chi của chính mình. Khoản chi của User B hoàn toàn không hiển thị.
   + **Nguyên lý Bảo mật:** Hàm Lambda truy vấn dữ liệu từ DynamoDB tuyệt đối dựa trên điều kiện `KeyConditionExpression: "userId = :uid"`, với `:uid` được lấy từ trường `sub` do JWT Authorizer tin cậy trích xuất. Chữ ký số ngăn client tự sửa các claim đã xác thực, nhưng việc cô lập dữ liệu vẫn phụ thuộc vào việc Lambda luôn sử dụng claim `sub` đã xác thực cho mọi thao tác đọc, cập nhật và xóa.
