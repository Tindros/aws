---
title : "Implement Expense Tracker Lambda Function"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.5.2 </b> "
---

In this module, you will create the **AWS Lambda function** in **Node.js 22.x** (Active LTS runtime built on Amazon Linux 2023 with AWS SDK for JavaScript v3 pre-installed) and deploy the backend business logic that interacts with Amazon DynamoDB.

---

#### Step 1: Create the Lambda Function

1. Open the [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
2. Choose **Create function**.
3. Select **Author from scratch**.
4. In the **Basic information** pane:
   + **Function name:** Enter `expenseTrackerFunction`.
   + **Runtime:** Select **Node.js 22.x** (or **Node.js 20.x**).
   + **Architecture:** Select `x86_64`.
5. Under **Permissions**, expand **Change default execution role**:
   + Select **Use an existing role**.
   + **Existing role:** Select `ExpenseTrackerLambdaRole` created in the previous step.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-03-create-function.png`  
*(To be captured during deployment: Lambda function creation screen with Node.js 22.x and ExpenseTrackerLambdaRole selected)*
{{% /notice %}}
<!-- ![Create Lambda Function](/images/5-Workshop/5.5-Lambda/lambda-03-create-function.png) -->

6. Choose **Create function**.

---

#### Step 2: Configure Environment Variables

1. On the function page, select the **Configuration** tab.
2. In the left menu, select **Environment variables**, then click **Edit**.
3. Click **Add environment variable**:
   + **Key:** `TABLE_NAME`
   + **Value:** `ExpenseTable`
4. Choose **Save**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-04-env-variables.png`  
*(To be captured during deployment: Lambda Environment variables showing TABLE_NAME = ExpenseTable)*
{{% /notice %}}
<!-- ![Configure Environment Variables](/images/5-Workshop/5.5-Lambda/lambda-04-env-variables.png) -->

---

#### Step 3: Implement Function Code (`index.mjs`)

The Node.js 22.x Lambda runtime comes pre-packaged with the AWS SDK for JavaScript v3. We use `@aws-sdk/client-dynamodb` and `@aws-sdk/lib-dynamodb` (the Document Client) to interact with DynamoDB using native JavaScript objects without manual attribute type marshaling.

1. Select the **Code** tab.
2. In the code editor, double-click `index.mjs` and replace its entire contents with the following code:

> **CORS:** Configure CORS in API Gateway (Section 5.6.1). This Lambda therefore does not add `Access-Control-Allow-Origin: *` or handle preflight `OPTIONS` itself. In a real deployment, allow only the frontend origins you need.

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
  // Log routing metadata only; do not log JWT claims or other identity details.
  console.log("Incoming request:", JSON.stringify({
    requestId: event.requestContext?.requestId,
    routeKey: event.routeKey,
    rawPath: event.rawPath,
    httpMethod: event.requestContext?.http?.method
  }));

  // HTTP API CORS is configured in API Gateway; it handles browser preflight requests.

  // Extract authenticated user ID exclusively from verified Cognito JWT claims
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
    // ROUTE: GET /expenses - List all expenses for current user
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

    // ROUTE: POST /expenses - Create a new expense
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

    // ROUTE: GET /expenses/{id} - Get single expense
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

    // ROUTE: PUT /expenses/{id} - Update an existing expense
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

    // ROUTE: DELETE /expenses/{id} - Delete an expense
    if (routeKey === "DELETE /expenses/{id}" || (method === "DELETE" && expenseId)) {
      await docClient.send(new DeleteCommand({
        TableName: TABLE_NAME,
        Key: { userId, expenseId }
      }));
      return buildResponse(200, { message: "Expense deleted successfully", expenseId });
    }

    // ROUTE: GET /summary - Calculate spending analytics
    if (routeKey === "GET /summary" || (method === "GET" && path.endsWith("/summary"))) {
      const result = await docClient.send(new QueryCommand({
        TableName: TABLE_NAME,
        KeyConditionExpression: "userId = :uid",
        ExpressionAttributeValues: { ":uid": userId }
      }));

      const items = result.Items || [];
      const totalSpent = items.reduce((sum, item) => sum + (Number(item.amount) || 0), 0);

      // Calculate current month spending (YYYY-MM)
      const currentMonth = new Date().toISOString().slice(0, 7);
      const thisMonth = items
        .filter(item => item.date && item.date.startsWith(currentMonth))
        .reduce((sum, item) => sum + (Number(item.amount) || 0), 0);

      // Calculate category breakdown
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

3. Click **Deploy** to save and compile your function changes.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-05-code-editor.png`  
*(To be captured during deployment: Lambda code editor showing deployed index.mjs handler)*
{{% /notice %}}
<!-- ![Deploy Lambda Handler](/images/5-Workshop/5.5-Lambda/lambda-05-code-editor.png) -->

---

{{% notice warning %}}
**Query result limit:** The `GET /expenses` and `GET /summary` examples each issue one DynamoDB `Query` request. DynamoDB can stop a Query response at 1 MB and return `LastEvaluatedKey`; this sample does not paginate, so larger accounts may see incomplete lists or summaries. Add pagination before using this implementation with larger datasets.
{{% /notice %}}

#### Step 4: Test Function Execution

1. In the Lambda console, select the **Test** tab.
2. Select **Create new event**, enter Event name `CreateExpenseTest`, and paste the following test payload simulating an API Gateway v2 HTTP API proxy event with JWT claims:

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
  "body": "{\"description\":\"Groceries at supermarket\",\"amount\":45.50,\"category\":\"Food\",\"date\":\"2026-10-14\"}"
}
```

3. Click **Test**.
4. Review the **Execution results**:
   + Verify the response returns status code `201` with the saved item containing `userId` and generated `expenseId`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-06-test-execution.png`  
*(To be captured during deployment: Lambda test execution result returning status 201 Created)*
{{% /notice %}}
<!-- ![Test Function Execution](/images/5-Workshop/5.5-Lambda/lambda-06-test-execution.png) -->

---

#### Next Step

Proceed to [API Gateway Routes, JWT Authorizer & Integration](../../5.6-APIGateway/) to expose and secure these endpoints.
