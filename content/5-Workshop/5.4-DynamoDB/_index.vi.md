---
title : "Thiết kế Bảng Dữ liệu Amazon DynamoDB"
date : 2026-10-14 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### Tại sao Chọn Amazon DynamoDB cho Ứng dụng Serverless?

**Amazon DynamoDB** là dịch vụ cơ sở dữ liệu NoSQL không máy chủ được quản lý toàn diện bởi AWS, cung cấp độ trễ phản hồi tính bằng mili-giây ổn định ở bất kỳ quy mô nào. Các cơ sở dữ liệu quan hệ truyền thống (như MySQL hay PostgreSQL) yêu cầu duy trì kết nối liên tục (connection pool) và máy chủ vận hành thường trực, điều này dễ gây nghẽn tài nguyên và phát sinh chi phí cao khi kết hợp với mô hình hàm Lambda có vòng đời ngắn. DynamoDB tích hợp tự nhiên với AWS Lambda qua các lệnh gọi HTTPS API và cơ chế xác thực IAM, trở thành lựa chọn lưu trữ tối ưu nhất cho các ứng dụng Serverless.

---

#### Mô hình Hóa Dữ liệu & Thiết kế Khóa

Trong DynamoDB, hiệu năng truy vấn tối ưu và cơ chế cô lập dữ liệu người dùng phụ thuộc vào cách thiết kế khóa chính (Primary Key):

| Thành phần Khóa | Tên Thuộc tính | Kiểu Dữ liệu | Mục đích Sử dụng |
| :--- | :--- | :--- | :--- |
| **Partition Key (PK)** | `userId` | String (`S`) | Chuỗi định danh duy nhất (`sub`) được cấp từ Amazon Cognito. Gom toàn bộ các bản ghi của một người dùng vào cùng một phân vùng vật lý. |
| **Sort Key (SK)** | `expenseId` | String (`S`) | Mã định danh duy nhất cho từng khoản chi (UUID hoặc nano-id). |

```
+---------------------------------------------------------------------------------------------------------+
|                                              ExpenseTable                                               |
+---------------------------------------+------------------+--------------+--------+---------------+------+
| userId (PK)                           | expenseId (SK)   | description  | amount | category      | date |
+---------------------------------------+------------------+--------------+--------+---------------+------+
| a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d  | exp-9f2b1a0e     | Đi chợ       | 85.20  | Food          | 2026-10-06
| a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d  | exp-4c8d7e2a     | Vé xe buýt   | 15.00  | Transportation| 2026-10-05
| b9c8d7e6-f5a4-3b2c-1d0e-9f8a7b6c5d4e  | exp-1a2b3c4d     | Cà phê       | 4.50   | Food          | 2026-10-06
+---------------------------------------+------------------+--------------+--------+---------------+------+
```

##### Các Mẫu Truy cập Dữ liệu (Access Patterns)

1. **Lấy danh sách tất cả chi tiêu của người dùng hiện tại:**
   + **Thao tác:** `Query`
   + **Điều kiện:** `KeyConditionExpression: "userId = :uid"`
   + **Hiệu năng:** Chỉ quét trong phạm vi phân vùng của người dùng đang đăng nhập, không quét bảng và không đọc dữ liệu của người dùng khác.
2. **Lấy chi tiết một khoản chi cụ thể theo ID:**
   + **Thao tác:** `GetItem`
   + **Khóa:** `{ userId: "<UID>", expenseId: "<EXPENSE_ID>" }`
   + **Hiệu năng:** Truy xuất điểm trực tiếp O(1) với độ trễ thấp nhất.
3. **Thêm mới khoản chi:**
   + **Thao tác:** `PutItem`
4. **Cập nhật khoản chi:**
   + **Thao tác:** `UpdateItem`
5. **Xóa khoản chi:**
   + **Thao tác:** `DeleteItem`

---

#### Bước 1: Tạo Bảng DynamoDB

1. Truy cập [Amazon DynamoDB Console](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables).
2. Kiểm tra Region góc trên bên phải là **US East (N. Virginia) `us-east-1`**.
3. Tại menu bên trái, chọn **Tables**, sau đó nhấn nút **Create table**.
4. Tại mục **Table details**:
   + **Table name:** Nhập `ExpenseTable`.
   + **Partition key:** Nhập `userId` và chọn kiểu **String**.
   + **Sort key:** Nhập `expenseId` và chọn kiểu **String**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-01-create-table.png`  
*(Cần chụp khi triển khai: Màn hình tạo bảng DynamoDB ExpenseTable với userId và expenseId)*
{{% /notice %}}
<!-- ![Tạo Bảng DynamoDB](/images/5-Workshop/5.4-DynamoDB/dynamodb-01-create-table.png) -->

---

#### Bước 2: Cấu hình Chế độ Dung lượng (Capacity Mode)

1. Tại mục **Table settings**, chọn **Customize settings**.
2. Tại mục **Capacity calculator**, chọn chế độ **On-demand**:
   + Tự động co giãn theo khối lượng đọc ghi thực tế của ứng dụng.
   + Chỉ tính phí theo số lượng yêu cầu đọc/ghi phát sinh, hoàn toàn không tốn chi phí khi ứng dụng ở trạng thái nhàn rỗi.
3. Giữ nguyên các thiết lập mặc định về **Secondary indexes**, **Encryption at rest** (Amazon DynamoDB owned key) và **Deletion protection**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-02-capacity-settings.png`  
*(Cần chụp khi triển khai: Cấu hình bảng ở chế độ dung lượng On-demand)*
{{% /notice %}}
<!-- ![Cấu hình Capacity Mode](/images/5-Workshop/5.4-DynamoDB/dynamodb-02-capacity-settings.png) -->

4. Cuộn xuống cuối trang và nhấn **Create table**.

---

#### Bước 3: Xác minh Trạng thái Bảng

1. Đợi vài giây cho trạng thái của bảng chuyển từ *Creating* sang **Active**.
2. Bấm vào tên bảng `ExpenseTable` để xem bảng thông tin tổng quan **General information**.
3. Sao chép và lưu lại chuỗi **Amazon Resource Name (ARN)**:
   + Định dạng: `arn:aws:dynamodb:us-east-1:<ACCOUNT_ID>:table/ExpenseTable`

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-03-table-active.png`  
*(Cần chụp khi triển khai: Bảng ExpenseTable ở trạng thái Active kèm chuỗi ARN)*
{{% /notice %}}
<!-- ![Bảng Hoạt động](/images/5-Workshop/5.4-DynamoDB/dynamodb-03-table-active.png) -->

4. Chuyển sang thẻ **Explore items** để kiểm tra bảng đã sẵn sàng tiếp nhận các bản ghi đầu tiên.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-04-explore-items.png`  
*(Cần chụp khi triển khai: Thẻ Explore items hiển thị cấu trúc bảng ExpenseTable sẵn sàng)*
{{% /notice %}}
<!-- ![Khám phá Items](/images/5-Workshop/5.4-DynamoDB/dynamodb-04-explore-items.png) -->

---

#### Bước Tiếp theo

Chuyển sang phần [Triển khai Hàm AWS Lambda & IAM Role](../5.5-Lambda/) để lập trình logic xử lý và phân quyền IAM tối thiểu cho hàm Lambda.
