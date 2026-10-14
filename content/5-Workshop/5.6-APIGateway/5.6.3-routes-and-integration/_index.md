---
title : "Define Routes & Lambda Proxy Integration"
date : 2026-10-14 
weight : 3 
chapter : false
pre : " <b> 5.6.3 </b> "
---

In this module, you will define the RESTful routes for expense management, connect them to `expenseTrackerFunction` via Lambda proxy integration, and secure each route with the `CognitoJWTAuthorizer`.

---

#### Application Route Mapping

| HTTP Method | Route Path | Description | Authorizer |
| :--- | :--- | :--- | :--- |
| `GET` | `/expenses` | Retrieves all expenses for the authenticated user | `CognitoJWTAuthorizer` |
| `POST` | `/expenses` | Creates a new expense record in DynamoDB | `CognitoJWTAuthorizer` |
| `GET` | `/expenses/{id}` | Retrieves a single expense by ID | `CognitoJWTAuthorizer` |
| `PUT` | `/expenses/{id}` | Updates existing expense attributes | `CognitoJWTAuthorizer` |
| `DELETE` | `/expenses/{id}` | Deletes an expense item | `CognitoJWTAuthorizer` |
| `GET` | `/summary` | Aggregates user spending totals & categories | `CognitoJWTAuthorizer` |

---

#### Step 1: Create Routes

1. In the [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1), open `ExpenseTrackerApi`.
2. In the navigation pane under **Develop**, choose **Routes**.
3. Click **Create**:
   + Method: Select `GET`
   + Path: Enter `/expenses`
   + Click **Create**.
4. Repeat this process to create the remaining 5 routes:
   + `POST /expenses`
   + `GET /expenses/{id}`
   + `PUT /expenses/{id}`
   + `DELETE /expenses/{id}`
   + `GET /summary`

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png`  
*(To be captured during deployment: Routes pane displaying all 6 created API routes)*
{{% /notice %}}
<!-- ![Routes List](/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png) -->

---

#### Step 2: Attach Lambda Proxy Integration

1. In the navigation pane, choose **Integrations**.
2. Select the **Manage integrations** tab, then choose **Create**:
   + **Integration target:** Select **Lambda function**.
   + **Integration method:** Select **POST** (standard internal invocation method for Lambda proxy).
   + **Lambda function:** Select `expenseTrackerFunction`.
   + Verify **Payload format version** is set to `2.0`.
3. Click **Create**.
4. Return to the **Routes** list. Select each route (`GET /expenses`, `POST /expenses`, etc.), choose **Attach integration**, and select `expenseTrackerFunction`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png`  
*(To be captured during deployment: Attaching Lambda proxy integration to routes)*
{{% /notice %}}
<!-- ![Lambda Integration](/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png) -->

{{% notice note %}}
**Verifying API Gateway Invocation Permissions:**
When creating the Lambda integration via the AWS Console, API Gateway automatically creates a **Lambda Resource-Based Policy** granting the API Gateway service principal (`apigateway.amazonaws.com`) permission to execute `lambda:InvokeFunction`.

You can verify this permission in the [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions) under **Configuration** -> **Permissions** -> **Resource-based policy statements**.

If configuring via AWS CLI or if the permission is missing, grant invocation rights scoped strictly to your HTTP API:
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

#### Step 3: Attach JWT Authorizer to Protected Routes

1. In the navigation pane under **Develop**, choose **Authorization**.
2. Under the **Routes** tab, select the first route: `GET /expenses`.
3. Under **Attach an authorizer to this route**, select `CognitoJWTAuthorizer`, then choose **Attach authorizer**.
4. Repeat this attachment for all remaining routes (`POST /expenses`, `GET /expenses/{id}`, `PUT /expenses/{id}`, `DELETE /expenses/{id}`, and `GET /summary`).

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png`  
*(To be captured during deployment: Authorization panel showing CognitoJWTAuthorizer attached to all routes)*
{{% /notice %}}
<!-- ![Authorizer Attached](/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png) -->

---

#### Step 4: Obtain Stage Invoke URL

1. In the navigation pane under **Deploy**, choose **Stages**.
2. Select the `$default` stage.
3. Locate and copy the **Invoke URL**:
   + Format: `https://<API_ID>.execute-api.us-east-1.amazonaws.com`

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png`  
*(To be captured during deployment: Stage overview displaying the public Invoke URL)*
{{% /notice %}}
<!-- ![Stage Invoke URL](/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png) -->

{{% notice tip %}}
**Save the Invoke URL:** This URL is the base API endpoint required by the frontend application in the next section.
{{% /notice %}}

---

#### Next Step

Proceed to [React Frontend Integration](../../5.7-Frontend-integration/) to connect the user interface to your live AWS backend.
