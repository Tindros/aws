---
title : "Triển khai Hàm Lambda Xử lý Chi tiêu"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.5.2 </b> "
---

Trong phần này, bạn sẽ tạo hàm **AWS Lambda** sử dụng môi trường thực thi **Node.js 22.x** (phiên bản Active LTS chạy trên Amazon Linux 2023 có tích hợp sẵn AWS SDK v3) và triển khai toàn bộ mã nguồn xử lý nghiệp vụ chi tiêu, giao tiếp với DynamoDB.

---

#### Bước 1: Tạo Hàm Lambda

1. Truy cập [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
2. Nhấn chọn **Create function**.
3. Chọn tùy chọn **Author from scratch**.
4. Tại mục **Basic information**:
   + **Function name:** Nhập `expenseTrackerFunction`.
   + **Runtime:** Chọn **Node.js 22.x** (hoặc **Node.js 20.x**).
   + **Architecture:** Chọn `x86_64`.
5. Tại mục **Permissions**, mở rộng phần **Change default execution role**:
   + Chọn **Use an existing role**.
   + **Existing role:** Chọn `ExpenseTrackerLambdaRole` đã tạo ở bước trước.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-03-create-function.png`  
*(Cần chụp khi triển khai: Màn hình khởi tạo hàm Lambda với runtime Node.js 22.x và role ExpenseTrackerLambdaRole)*
{{% /notice %}}
<!-- ![Khởi tạo Hàm Lambda](/images/5-Workshop/5.5-Lambda/lambda-03-create-function.png) -->

6. Nhấn nút **Create function**.

---

#### Bước 2: Cấu hình Biến Môi trường (Environment Variables)

1. Tại trang chi tiết của hàm vừa tạo, chọn thẻ **Configuration**.
2. Tại menu bên trái, chọn **Environment variables**, sau đó nhấn **Edit**.
3. Bấm **Add environment variable**:
   + **Key:** `TABLE_NAME`
   + **Value:** `ExpenseTable`
4. Nhấn **Save**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-04-env-variables.png`  
*(Cần chụp khi triển khai: Cấu hình biến môi trường TABLE_NAME = ExpenseTable trong Lambda)*
{{% /notice %}}
<!-- ![Cấu hình Biến Môi trường](/images/5-Workshop/5.5-Lambda/lambda-04-env-variables.png) -->

---

#### Bước 3: Viết Mã Nguồn Xử lý (`index.mjs`)

Môi trường Node.js 22.x trên Lambda đã được AWS tích hợp sẵn bộ thư viện AWS SDK v3. Chúng ta sử dụng `@aws-sdk/client-dynamodb` kết hợp với `@aws-sdk/lib-dynamodb` (Document Client) để thao tác với DynamoDB bằng các đối tượng JavaScript thông thường mà không cần chuyển đổi kiểu dữ liệu thủ công.

1. Chuyển sang thẻ **Code**.
2. Trong cây thư mục mã nguồn, nhấp đúp vào tệp `index.mjs` và thay thế toàn bộ nội dung bằng đoạn mã sau:

> **CORS:** Cấu hình CORS tại API Gateway (mục 5.6.1). Vì vậy Lambda không tự thêm `Access-Control-Allow-Origin: *` hoặc xử lý preflight `OPTIONS`. Khi triển khai thực tế, chỉ cho phép các origin frontend cần thiết.

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { 
  DynamoDBDocumentClient, 
  QueryCommand, 
  GetCommand, 
  PutCommand, 
  UpdateCommand, 
  DeleteCommand 
} from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "node:crypto";

const client = new DynamoDBClient({});
const docClient = DynamoDBDocumentClient.from(client);
const TABLE_NAME = process.env.TABLE_NAME || "ExpenseTable";

const corsHeaders = {
  "Content-Type": "application/json"
};

const validateExpensePayload = (payload, { partial = false } = {}) => {
  if (!payload || typeof payload !== "object" || Array.isArray(payload)) {
    return "Request body must be a JSON object";
  }
  const has = (key) => Object.prototype.hasOwnProperty.call(payload, key);

  if (!partial || has("description")) {
    if (typeof payload.description !== "string" || payload.description.trim().length === 0 || payload.description.trim().length > 200) {
      return "description must be a non-empty string of at most 200 characters";
    }
  }
  if (!partial || has("amount")) {
    const amount = Number(payload.amount);
    if (payload.amount === "" || payload.amount === null || !Number.isFinite(amount) || amount <= 0) {
      return "amount must be a finite number greater than 0";
    }
  }
  if (!partial || has("category")) {
    if (typeof payload.category !== "string" || payload.category.trim().length === 0 || payload.category.trim().length > 50) {
      return "category must be a non-empty string of at most 50 characters";
    }
  }
  if (!partial || has("date")) {
    if (typeof payload.date !== "string" || !/^\d{4}-\d{2}-\d{2}$/.test(payload.date)) {
      return "date must use YYYY-MM-DD format";
    }
    const parsedDate = new Date(`${payload.date}T00:00:00.000Z`);
    if (Number.isNaN(parsedDate.getTime()) || parsedDate.toISOString().slice(0, 10) !== payload.date) {
      return "date must be a valid calendar date";
    }
  }
  return null;
};

const buildResponse = (statusCode, body) => ({
  statusCode,
  headers: corsHeaders,
  body: JSON.stringify(body)
});

export const handler = async (event) => {
  // Chỉ ghi metadata định tuyến; không ghi JWT claims hay thông tin định danh người dùng vào log.
  console.log("Incoming request:", JSON.stringify({
    requestId: event.requestContext?.requestId,
    routeKey: event.routeKey,
    rawPath: event.rawPath,
    httpMethod: event.requestContext?.http?.method
  }));

  // CORS của HTTP API được cấu hình tại API Gateway; API Gateway xử lý preflight của trình duyệt.

  // Trích xuất định danh người dùng độc quyền từ Cognito JWT claims đã xác thực
  const userId = event.requestContext?.authorizer?.jwt?.claims?.sub;

  if (!userId) {
    return buildResponse(401, { message: "Unauthorized: Missing verified user identity" });
  }

  const method = event.requestContext?.http?.method;
  const path = event.rawPath || "";
  const routeKey = event.routeKey || `${method} ${path}`;
  const pathParams = event.pathParameters || {};
  const expenseId = pathParams.id;

  try {
    // ROUTE: GET /expenses - Lấy toàn bộ danh sách chi tiêu của người dùng
    if (routeKey === "GET /expenses" || (method === "GET" && path.endsWith("/expenses"))) {
      const command = new QueryCommand({
        TableName: TABLE_NAME,
        KeyConditionExpression: "userId = :uid",
        ExpressionAttributeValues: { ":uid": userId }
      });
      const result = await docClient.send(command);
      const items = result.Items || [];
      items.sort((a, b) => new Date(b.date) - new Date(a.date));
      return buildResponse(200, items);
    }

    // ROUTE: POST /expenses - Tạo mới một khoản chi tiêu
    if (routeKey === "POST /expenses" || (method === "POST" && path.endsWith("/expenses"))) {
      const payload = JSON.parse(event.body || "{}");
      const validationError = validateExpensePayload(payload);
      if (validationError) {
        return buildResponse(400, { message: validationError });
      }

      const newExpense = {
        userId,
        expenseId: randomUUID(),
        description: payload.description.trim(),
        amount: Number(payload.amount),
        category: payload.category.trim(),
        date: payload.date,
        createdAt: new Date().toISOString()
      };

      await docClient.send(new PutCommand({
        TableName: TABLE_NAME,
        Item: newExpense
      }));

      return buildResponse(201, newExpense);
    }

    // ROUTE: GET /expenses/{id} - Lấy chi tiết một khoản chi
    if (routeKey === "GET /expenses/{id}" || (method === "GET" && expenseId)) {
      const result = await docClient.send(new GetCommand({
        TableName: TABLE_NAME,
        Key: { userId, expenseId }
      }));
      if (!result.Item) {
        return buildResponse(404, { message: "Expense not found" });
      }
      return buildResponse(200, result.Item);
    }

    // ROUTE: PUT /expenses/{id} - Cập nhật thông tin khoản chi
    if (routeKey === "PUT /expenses/{id}" || (method === "PUT" && expenseId)) {
      const payload = JSON.parse(event.body || "{}");
      const validationError = validateExpensePayload(payload, { partial: true });
      if (validationError) {
        return buildResponse(400, { message: validationError });
      }
      const updateExp = [];
      const expValues = {};
      const expNames = {};

      if (payload.description !== undefined) {
        updateExp.push("#desc = :desc");
        expValues[":desc"] = String(payload.description).trim();
        expNames["#desc"] = "description";
      }
      if (payload.amount !== undefined) {
        updateExp.push("amount = :amt");
        expValues[":amt"] = Number(payload.amount);
      }
      if (payload.category !== undefined) {
        updateExp.push("category = :cat");
        expValues[":cat"] = payload.category.trim();
      }
      if (payload.date !== undefined) {
        updateExp.push("#dt = :dt");
        expValues[":dt"] = payload.date;
        expNames["#dt"] = "date";
      }

      if (updateExp.length === 0) {
        return buildResponse(400, { message: "No updatable fields provided" });
      }

      const updateResult = await docClient.send(new UpdateCommand({
        TableName: TABLE_NAME,
        Key: { userId, expenseId },
        UpdateExpression: "SET " + updateExp.join(", "),
        ExpressionAttributeValues: expValues,
        ...(Object.keys(expNames).length > 0 && { ExpressionAttributeNames: expNames }),
        ConditionExpression: "attribute_exists(userId)",
        ReturnValues: "ALL_NEW"
      }));

      return buildResponse(200, updateResult.Attributes);
    }

    // ROUTE: DELETE /expenses/{id} - Xóa khoản chi
    if (routeKey === "DELETE /expenses/{id}" || (method === "DELETE" && expenseId)) {
      await docClient.send(new DeleteCommand({
        TableName: TABLE_NAME,
        Key: { userId, expenseId }
      }));
      return buildResponse(200, { message: "Expense deleted successfully", expenseId });
    }

    // ROUTE: GET /summary - Tính toán số liệu thống kê bảng điều khiển
    if (routeKey === "GET /summary" || (method === "GET" && path.endsWith("/summary"))) {
      const result = await docClient.send(new QueryCommand({
        TableName: TABLE_NAME,
        KeyConditionExpression: "userId = :uid",
        ExpressionAttributeValues: { ":uid": userId }
      }));

      const items = result.Items || [];
      const totalSpent = items.reduce((sum, item) => sum + (Number(item.amount) || 0), 0);

      // Tính tổng chi tiêu trong tháng hiện tại (định dạng YYYY-MM)
      const currentMonth = new Date().toISOString().slice(0, 7);
      const thisMonth = items
        .filter(item => item.date && item.date.startsWith(currentMonth))
        .reduce((sum, item) => sum + (Number(item.amount) || 0), 0);

      // Tính toán phân bổ chi tiêu theo từng danh mục
      const catTotals = {};
      items.forEach(item => {
        catTotals[item.category] = (catTotals[item.category] || 0) + (Number(item.amount) || 0);
      });

      let topCategory = "None";
      let maxCategoryAmount = 0;
      const categoryBreakdown = Object.entries(catTotals).map(([cat, amount]) => {
        if (amount > maxCategoryAmount) {
          maxCategoryAmount = amount;
          topCategory = cat;
        }
        return {
          category: cat,
          amount,
          percentage: totalSpent > 0 ? (amount / totalSpent) * 100 : 0
        };
      }).sort((a, b) => b.amount - a.amount);

      return buildResponse(200, {
        totalSpent,
        thisMonth,
        numberOfExpenses: items.length,
        topCategory,
        categoryBreakdown
      });
    }

    return buildResponse(404, { message: `Route not found: ${routeKey}` });
  } catch (error) {
    console.error("Handler error:", error);
    if (error.name === "ConditionalCheckFailedException") {
      return buildResponse(404, { message: "Expense not found" });
    }
    if (error instanceof SyntaxError) {
      return buildResponse(400, { message: "Request body must contain valid JSON" });
    }
    return buildResponse(500, { message: "Internal server error" });
  }
};
```

3. Nhấn **Deploy** để lưu và biên dịch hàm.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-05-code-editor.png`  
*(Cần chụp khi triển khai: Trình soạn thảo mã nguồn Lambda hiển thị tệp index.mjs đã deploy)*
{{% /notice %}}
<!-- ![Triển khai Mã nguồn Lambda](/images/5-Workshop/5.5-Lambda/lambda-05-code-editor.png) -->

---

#### Bước 4: Kiểm thử Thực thi Hàm

1. Tại Lambda console, chuyển sang thẻ **Test**.
2. Chọn **Create new event**, nhập Event name `CreateExpenseTest`, và dán payload giả lập sự kiện HTTP API proxy kèm thông tin JWT claims:

```json
{
  "rawPath": "/expenses",
  "routeKey": "POST /expenses",
  "requestContext": {
    "http": {
      "method": "POST"
    },
    "authorizer": {
      "jwt": {
        "claims": {
          "sub": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d"
        }
      }
    }
  },
  "body": "{\"description\":\"Đi siêu thị\",\"amount\":45.50,\"category\":\"Food\",\"date\":\"2026-10-14\"}"
}
```

3. Nhấn nút **Test**.
4. Quan sát kết quả trong bảng **Execution results**:
   + Kiểm tra hàm phản hồi mã trạng thái `201` cùng đối tượng chi tiêu mới đã được gán `userId` và sinh `expenseId`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-06-test-execution.png`  
*(Cần chụp khi triển khai: Kết quả kiểm thử hàm trả về mã HTTP 201 Created)*
{{% /notice %}}
<!-- ![Kiểm thử Thực thi](/images/5-Workshop/5.5-Lambda/lambda-06-test-execution.png) -->

---

#### Bước Tiếp theo

Chuyển sang phần [Cấu hình API Gateway, JWT Authorizer & Tích hợp](../../5.6-APIGateway/) để mở các endpoint API và thiết lập bảo vệ bằng JWT.
