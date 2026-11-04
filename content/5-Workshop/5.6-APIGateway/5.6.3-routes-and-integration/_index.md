---
title : "Define Routes & Lambda Proxy Integration"
date : 2026-10-14
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---

In this module, you will create the six HTTP API routes used by the Expense Tracker, connect every route to the existing `expenseTrackerFunction` Lambda function, and require the `CognitoJWTAuthorizer` for every application route.

Before you begin, confirm that you have completed:

- [Create HTTP API & Configure CORS](../5.6.1-create-http-api/).
- [Configure Cognito JWT Authorizer](../5.6.2-jwt-authorizer/).
- [Create the Lambda function](../../5.5-Lambda/).

---

#### Application Route Mapping

Create the following route keys exactly as shown. The method and path must match the routes handled by the Lambda function.

| HTTP Method | Route path | Purpose |
| :--- | :--- | :--- |
| `GET` | `/expenses` | List the authenticated user's expenses |
| `POST` | `/expenses` | Create an expense |
| `GET` | `/expenses/{id}` | Retrieve one expense by ID |
| `PUT` | `/expenses/{id}` | Update one expense |
| `DELETE` | `/expenses/{id}` | Delete one expense |
| `GET` | `/summary` | Retrieve the authenticated user's spending summary |

All six routes will use `CognitoJWTAuthorizer`. Do not create a public route for these operations.

---

#### Step 1: Create the API Routes

1. Open the [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
2. Select `ExpenseTrackerApi`.
3. In the navigation pane, under **Develop**, choose **Routes**.
4. Choose **Create**.
5. For the first route, select:
   - **Method:** `GET`
   - **Path:** `/expenses`
6. Choose **Create**.
7. Repeat the process for the remaining five route keys:
   - `POST /expenses`
   - `GET /expenses/{id}`
   - `PUT /expenses/{id}`
   - `DELETE /expenses/{id}`
   - `GET /summary`

Check the Routes list and make sure all six route keys appear exactly as shown in the table.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png`  
*(Capture the Routes page showing all six route keys after they have been created.)*
{{% /notice %}}
<!-- ![Routes List](/images/5-Workshop/5.6-APIGateway/apigw-04-routes-list.png) -->

---

#### Step 2: Create a Lambda Proxy Integration

1. In the API navigation pane, choose **Integrations**.
2. Open **Manage integrations**, then choose **Create**.
3. Configure the integration:
   - **Integration type / target:** Select **Lambda function**.
   - **Lambda function:** Select `expenseTrackerFunction` in the same AWS Region.
   - **Payload format version:** Select `2.0`.
4. If the console asks for an integration method, keep the Lambda invocation method as `POST`. This is the method API Gateway uses to invoke Lambda; it does not change the HTTP methods exposed by your API routes.
5. Choose **Create**.

{{% notice note %}}
**Why use payload format version 2.0?** The Lambda handler in this workshop reads HTTP API v2 event fields, including `event.requestContext.http.method`, `event.rawPath`, `event.pathParameters`, and `event.requestContext.authorizer.jwt.claims.sub`. The integration must therefore use payload format version `2.0`.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png`  
*(Capture the Lambda integration details showing expenseTrackerFunction and payload format version 2.0.)*
{{% /notice %}}
<!-- ![Lambda Integration](/images/5-Workshop/5.6-APIGateway/apigw-05-lambda-integration.png) -->

---

#### Step 3: Attach the Integration to All Routes

Creating an integration does not automatically connect it to every route. Attach it to each route:

1. Return to **Develop → Routes**.
2. Select `GET /expenses`.
3. In the route details, choose **Attach integration**.
4. Select the integration for `expenseTrackerFunction`, then confirm.
5. Repeat for all remaining routes:
   - `POST /expenses`
   - `GET /expenses/{id}`
   - `PUT /expenses/{id}`
   - `DELETE /expenses/{id}`
   - `GET /summary`

After attaching, inspect each route and verify that its integration points to `expenseTrackerFunction`. Every route must have an integration before it can invoke the Lambda function.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-05b-route-integrations.png`  
*(Capture the Routes page or route details showing the Lambda integration attached to the routes.)*
{{% /notice %}}

---

#### Step 4: Verify API Gateway Can Invoke Lambda

When you create a Lambda integration through the API Gateway console, the console normally adds a resource-based permission to the Lambda function so that API Gateway can invoke it.

To verify the permission:

1. Open the [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
2. Select `expenseTrackerFunction`.
3. Open **Configuration → Permissions**.
4. Under **Resource-based policy statements**, look for a statement that allows the API Gateway service principal, `apigateway.amazonaws.com`, to perform `lambda:InvokeFunction`.

If you create the integration using the AWS Console and this permission is present, **do not add another permission**. If you create the integration using another method, or the permission is missing, add a permission scoped to this API and its default stage. Replace the placeholders with your own values:

```bash
aws lambda add-permission \
  --function-name expenseTrackerFunction \
  --statement-id apigateway-invoke-expensetracker-api \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:us-east-1:<YOUR_ACCOUNT_ID>:<API_ID>/$default/*/*"
```

{{% notice warning %}}
Use the command only if the required permission is missing. The `statement-id` must be unique in the Lambda function's resource policy. Do not replace an existing permission or broaden the source ARN unnecessarily.
{{% /notice %}}

---

#### Step 5: Attach the Cognito JWT Authorizer to Every Route

The authorizer must be attached to each route individually. Creating the authorizer in Section 5.6.2 does not automatically protect the routes.

1. In the API navigation pane, under **Develop**, choose **Authorization**.
2. Open the **Routes** tab.
3. Select `GET /expenses`.
4. Under the authorizer settings for the selected route, choose `CognitoJWTAuthorizer`.
5. Choose **Attach authorizer**.
6. Repeat for all five remaining routes:
   - `POST /expenses`
   - `GET /expenses/{id}`
   - `PUT /expenses/{id}`
   - `DELETE /expenses/{id}`
   - `GET /summary`

Return to the Routes authorization view and confirm that all six routes show `CognitoJWTAuthorizer`, not `NONE`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png`  
*(Capture the authorization settings showing CognitoJWTAuthorizer attached to all six routes.)*
{{% /notice %}}
<!-- ![Authorizer Attached](/images/5-Workshop/5.6-APIGateway/apigw-06-authorizer-attached.png) -->

---

#### Step 6: Review CORS and the Default Stage

1. Open **Develop → CORS** and review the settings from Section 5.6.1.
2. Make sure the allowed origins include the exact frontend origin you will use, such as `http://localhost:5173` and your deployed Amplify domain.
3. Make sure the allowed headers include `Authorization` and `Content-Type`, and the allowed methods include `GET`, `POST`, `PUT`, and `DELETE`.
4. API Gateway handles CORS preflight requests for an HTTP API when CORS is configured. You do not need to create a separate application `OPTIONS` route for this workshop.
5. Under **Deploy → Stages**, select `$default` and confirm that **Auto-deploy** is enabled.

{{% notice note %}}
When API-level CORS is configured, API Gateway manages the preflight response. Keep the allowed origins limited to the frontend domains you trust. The Lambda function in this workshop does not need to add its own CORS response headers.
{{% /notice %}}

---

#### Step 7: Copy the API Invoke URL

1. In the navigation pane, under **Deploy**, choose **Stages**.
2. Select the `$default` stage.
3. Copy the **Invoke URL**. It has this format:

```text
https://<API_ID>.execute-api.us-east-1.amazonaws.com
```

4. Save this value. The React frontend will use it as its API base URL in Section 5.7.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png`  
*(Capture the $default stage overview showing the Invoke URL.)*
{{% /notice %}}
<!-- ![Stage Invoke URL](/images/5-Workshop/5.6-APIGateway/apigw-07-stage-invoke-url.png) -->

---

#### Configuration Checklist

Before proceeding, verify that:

- [ ] The API is named `ExpenseTrackerApi` and uses the `$default` stage.
- [ ] All six route keys match the route mapping table.
- [ ] Every route is attached to the `expenseTrackerFunction` Lambda integration.
- [ ] The Lambda integration uses payload format version `2.0`.
- [ ] Every route uses `CognitoJWTAuthorizer`; no expense route is left with authorization type `NONE`.
- [ ] API Gateway has permission to invoke `expenseTrackerFunction`.
- [ ] CORS allows the frontend origin, `Authorization` and `Content-Type` headers, and the required HTTP methods.
- [ ] You have saved the API Invoke URL for the frontend configuration.

{{% notice warning %}}
**Testing note:** This checklist verifies the configuration steps; it does not mean that the API has been deployed or tested in a live AWS account. Perform the authorized-request, missing-token, invalid-token, and user-data-isolation tests in Section 5.9 after completing the frontend integration. Do not mark screenshots or live tests as completed until you have actually performed them.
{{% /notice %}}

---

#### Next Step

Proceed to [React Frontend Integration](../../5.7-Frontend-integration/) to configure the frontend to call the API using the Cognito Access Token.
