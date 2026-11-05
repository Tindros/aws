---
title : "Create HTTP API & Configure CORS"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.6.1 </b> "
---

In this module, you will initialize the **Amazon API Gateway HTTP API** and configure global **Cross-Origin Resource Sharing (CORS)** rules to allow browser-based HTTP communication from your local development server and AWS Amplify domains.

---

#### Step 1: Create HTTP API

1. Open the [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
2. Choose **Create API**.
3. Under **HTTP API**, choose **Build**.
4. In the **Create an API** configuration wizard:
   + **API name:** Enter `ExpenseTrackerApi`.
   + Keep **Integrations** empty for now (we will attach integrations to individual routes in subsequent steps).
5. Click **Next**.
6. On the **Configure routes** page, click **Next** without adding default routes.
7. On the **Define stages** page:
   + Stage name: Keep `$default`.
   + Verify **Auto-deploy** is enabled (ensures configuration changes go live automatically without manual redeployment).
8. Click **Next**, review the settings, and choose **Create**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-01-create-http-api.png`  
*(To be captured during deployment: Creating HTTP API named ExpenseTrackerApi with $default stage)*
{{% /notice %}}
<!-- ![Create HTTP API](/images/5-Workshop/5.6-APIGateway/apigw-01-create-http-api.png) -->

---

#### Step 2: Configure CORS (Cross-Origin Resource Sharing)

Modern web browsers enforce the Same-Origin Policy. When your frontend running on `http://localhost:5173` or an AWS Amplify domain calls API Gateway, the browser may send an HTTP `OPTIONS` preflight request. Configure CORS in API Gateway; when deploying, allow only the exact frontend origins you use rather than a broad wildcard.

1. In the API Gateway navigation pane, select **CORS** under **Develop**.
2. Click **Configure**.
3. Configure the following parameters:
   + **Access-Control-Allow-Origin:** For a local learning test, you may temporarily enter `*`; preferably enter exact origins such as `http://localhost:5173` and your deployed Amplify origin. Do not use a wildcard in production when you know the frontend origin(s).
   + **Access-Control-Allow-Headers:** Enter `Content-Type`, then press Enter. Enter `Authorization`, then press Enter.
   + **Access-Control-Allow-Methods:** Select `GET`, `POST`, `PUT`, `DELETE`, and `OPTIONS`.
   + **Access-Control-Max-Age:** Enter `300` (caches preflight responses for 5 minutes to reduce unnecessary OPTIONS calls).
4. Click **Save**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-02-cors-configuration.png`  
*(To be captured during deployment: API Gateway CORS configuration showing allowed origins, headers, and methods)*
{{% /notice %}}
<!-- ![Configure CORS](/images/5-Workshop/5.6-APIGateway/apigw-02-cors-configuration.png) -->
