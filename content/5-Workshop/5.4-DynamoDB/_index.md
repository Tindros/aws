---
title : "Amazon DynamoDB Table & Key Design"
date : 2026-10-14 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### Why Amazon DynamoDB for Serverless Applications?

**Amazon DynamoDB** is a fully managed, serverless, NoSQL database service that provides consistent, single-digit millisecond latency at any scale. Traditional relational databases (such as MySQL or PostgreSQL) require persistent connection pooling and running compute instances, which can become bottlenecks or cost drivers when coupled with ephemeral AWS Lambda invocations. DynamoDB integrates natively with AWS Lambda via HTTPS API calls and IAM authentication, making it the ideal persistent storage engine for serverless applications.

---

#### Data Modeling & Key Design

In DynamoDB, optimal performance and strict user isolation depend on key design:

| Element | Attribute Name | Type | Description |
| :--- | :--- | :--- | :--- |
| **Partition Key (PK)** | `userId` | String (`S`) | The unique user subject UUID (`sub`) provided by Amazon Cognito. Partitions all records belonging to a specific user together. |
| **Sort Key (SK)** | `expenseId` | String (`S`) | A unique identifier for the individual expense transaction (UUID or nano-id). |

```
+---------------------------------------------------------------------------------------------------------+
|                                              ExpenseTable                                               |
+---------------------------------------+------------------+--------------+--------+---------------+------+
| userId (PK)                           | expenseId (SK)   | description  | amount | category      | date |
+---------------------------------------+------------------+--------------+--------+---------------+------+
| a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d  | exp-9f2b1a0e     | Groceries    | 85.20  | Food          | 2026-10-06
| a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d  | exp-4c8d7e2a     | Metro card   | 15.00  | Transportation| 2026-10-05
| b9c8d7e6-f5a4-3b2c-1d0e-9f8a7b6c5d4e  | exp-1a2b3c4d     | Coffee       | 4.50   | Food          | 2026-10-06
+---------------------------------------+------------------+--------------+--------+---------------+------+
```

##### Supported Access Patterns

1. **List all expenses for the authenticated user:**
   + **Operation:** `Query`
   + **Condition:** `KeyConditionExpression: "userId = :uid"`
   + **Efficiency:** Scans only the specific partition belonging to the authenticated user. Never inspects records of other users.
2. **Fetch a specific expense by ID:**
   + **Operation:** `GetItem`
   + **Key:** `{ userId: "<UID>", expenseId: "<EXPENSE_ID>" }`
   + **Efficiency:** Direct O(1) point-lookup.
3. **Add new expense record:**
   + **Operation:** `PutItem`
4. **Update existing expense:**
   + **Operation:** `UpdateItem`
5. **Delete expense:**
   + **Operation:** `DeleteItem`

---

#### Step 1: Create the DynamoDB Table

1. Open the [Amazon DynamoDB Console](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables).
2. Verify your region is set to **US East (N. Virginia) `us-east-1`**.
3. In the navigation pane, choose **Tables**, then click **Create table**.
4. Under **Table details**:
   + **Table name:** Enter `ExpenseTable`.
   + **Partition key:** Enter `userId` and select **String**.
   + **Sort key:** Enter `expenseId` and select **String**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-01-create-table.png`  
*(To be captured during deployment: DynamoDB table creation screen showing ExpenseTable with userId and expenseId)*
{{% /notice %}}
<!-- ![Create Table](/images/5-Workshop/5.4-DynamoDB/dynamodb-01-create-table.png) -->

---

#### Step 2: Configure Capacity Mode

1. Under **Table settings**, select **Customize settings**.
2. Under **Capacity calculator**, select **On-demand**:
   + Automatically accommodates workloads as they ramp up or down.
   + Charges only for read and write requests executed, ensuring zero cost when idle.
3. Keep default settings for **Secondary indexes**, **Encryption at rest** (Amazon DynamoDB owned key), and **Deletion protection**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-02-capacity-settings.png`  
*(To be captured during deployment: Setting table capacity mode to On-demand)*
{{% /notice %}}
<!-- ![Configure Capacity Mode](/images/5-Workshop/5.4-DynamoDB/dynamodb-02-capacity-settings.png) -->

4. Scroll down and choose **Create table**.

---

#### Step 3: Verify Table Status

1. Wait a few seconds until the table status transitions from *Creating* to **Active**.
2. Click on `ExpenseTable` to view the **General information** panel.
3. Note down the **Amazon Resource Name (ARN)**:
   + Format: `arn:aws:dynamodb:us-east-1:<ACCOUNT_ID>:table/ExpenseTable`

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-03-table-active.png`  
*(To be captured during deployment: ExpenseTable overview showing Active status and table ARN)*
{{% /notice %}}
<!-- ![Table Active](/images/5-Workshop/5.4-DynamoDB/dynamodb-03-table-active.png) -->

4. Select the **Explore items** tab to observe the empty table state ready to receive incoming items.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.4-DynamoDB/dynamodb-04-explore-items.png`  
*(To be captured during deployment: Explore items tab showing empty ExpenseTable schema)*
{{% /notice %}}
<!-- ![Explore Items](/images/5-Workshop/5.4-DynamoDB/dynamodb-04-explore-items.png) -->

---

#### Next Step

Proceed to [AWS Lambda Implementation & Execution Role](../5.5-Lambda/) to implement the backend compute logic and configure least-privilege IAM permissions.
