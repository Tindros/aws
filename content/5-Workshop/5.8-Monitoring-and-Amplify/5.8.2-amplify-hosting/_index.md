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
   + **Git-based deployment (recommended for this workshop):** Connect the Git provider that contains the repository. This repository contains the React frontend at its root, so no monorepo app root is required.
   + **Direct upload alternative:** Build the frontend locally with `npm run build` from the `expense-tracker-frontend` repository, then upload the generated `dist/` output using the current Amplify manual deployment flow. The `amplify.yml` below is for Git-based builds, not the ZIP upload path.
4. Enter **App name:** `expense-tracker-frontend`.


---

#### Step 2: Configure Build Settings (`amplify.yml`)

1. Because `expense-tracker-frontend` is a single-application repository, use this build specification for the Git-based deployment:

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


---

#### Step 3: Add Production Environment Variables

1. Expand **Advanced settings** (or navigate to **App settings** -> **Environment variables**).
2. Add your four environment variables:
   + `VITE_COGNITO_USER_POOL_ID`: `<YOUR_USER_POOL_ID>`
   + `VITE_COGNITO_CLIENT_ID`: `<YOUR_APP_CLIENT_ID>`
   + `VITE_API_URL`: `<YOUR_API_GATEWAY_INVOKE_URL>`

> All `VITE_*` values are included in the browser build and are visible to users. Only put public configuration such as region, User Pool ID, App Client ID, and API URL here—never access keys, client secrets, passwords, or other credentials.
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
3. Verify that the deployed frontend loads, can sign in with a confirmed Cognito user, and can retrieve/create expenses through the configured API. If the build succeeds but API calls fail, check the environment variables, API URL, CORS allowlist, and JWT authorizer settings.


---

#### Next Step

Proceed to [End-to-End Testing, Security & Resource Cleanup](../../5.9-Testing-and-Cleanup/) to verify system security controls and execute the resource teardown procedure.
