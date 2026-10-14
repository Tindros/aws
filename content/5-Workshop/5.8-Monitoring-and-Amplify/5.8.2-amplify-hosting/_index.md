---
title : "Deploy React Frontend to AWS Amplify Hosting"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.8.2 </b> "
---

In this module, you will deploy the production build of your **React + Vite** frontend to **AWS Amplify Hosting**, making the application accessible globally over HTTPS backed by Amazon CloudFront.

---

#### Step 1: Initialize AWS Amplify App

1. Open the [AWS Amplify Console](https://us-east-1.console.aws.amazon.com/amplify/home?region=us-east-1).
2. Choose **Deploy an app** (or **Host web app**).
3. Select your deployment source:
   + Choose your Git provider (GitHub, AWS CodeCommit, GitLab, Bitbucket), or select **Deploy without Git provider** (for direct ZIP uploads of the `dist/` directory).
4. Enter **App name:** `expense-tracker-frontend`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-01-create-app.png`  
*(To be captured during deployment: AWS Amplify console starting web app hosting setup)*
{{% /notice %}}
<!-- ![Initialize Amplify App](/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-01-create-app.png) -->

---

#### Step 2: Configure Build Settings (`amplify.yml`)

1. During repository setup, Amplify automatically detects Vite and React. Ensure your build configuration matches:

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: dist
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-02-build-settings.png`  
*(To be captured during deployment: Amplify build settings showing amplify.yml configuration for Vite)*
{{% /notice %}}
<!-- ![Amplify Build Settings](/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-02-build-settings.png) -->

---

#### Step 3: Add Production Environment Variables

1. Expand **Advanced settings** (or navigate to **App settings** -> **Environment variables**).
2. Add your four environment variables:
   + `VITE_AWS_REGION`: `us-east-1`
   + `VITE_COGNITO_USER_POOL_ID`: `<YOUR_USER_POOL_ID>`
   + `VITE_COGNITO_CLIENT_ID`: `<YOUR_APP_CLIENT_ID>`
   + `VITE_API_BASE_URL`: `<YOUR_API_GATEWAY_INVOKE_URL>`
3. Choose **Save and deploy**.

---

#### Step 4: Configure SPA Redirects

Because React Router handles client-side routing (`/login`, `/expenses`, `/dashboard`), configure Amplify to route 404 page requests to `index.html`:

1. In the Amplify navigation pane, select **Rewrites and redirects**.
2. Verify or add the following rewrite rule:
   + **Source address:** `</^[^.]+$|\.(?!(css|gif|ico|jpg|js|png|txt|svg|woff|woff2|ttf|map|json)$)([^.]+$)/>`
   + **Target address:** `/index.html`
   + **Type:** `200 (Rewrite)`

---

#### Step 5: Verify Live Deployment

1. Watch the automated deployment stages proceed through:
   + **Provision** -> **Build** -> **Deploy** -> **Verify**.
2. Once the build finishes, click the generated HTTPS URL (e.g., `https://main.xxxx.amplifyapp.com`).
3. Verify that the production application loads instantly, authenticates with Cognito, and displays live expense data from DynamoDB.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-03-deployed-overview.png`  
*(To be captured during deployment: Amplify Hosting overview showing successful build status and live domain)*
{{% /notice %}}
<!-- ![Deployed App Overview](/images/5-Workshop/5.8-Monitoring-and-Amplify/amplify-03-deployed-overview.png) -->

---

#### Next Step

Proceed to [End-to-End Testing, Security & Resource Cleanup](../../5.9-Testing-and-Cleanup/) to verify system security controls and execute the resource teardown procedure.
