---
title : "Create Cognito User Pool & App Client"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.3.1 </b> "
---

In this module, you will create and configure the **Amazon Cognito User Pool** and set up the **App Client** required by the web frontend.

---

#### Step 1: Open Cognito Console & Start Creation

1. Sign in to the AWS Management Console and open the [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
2. Confirm your region in the top navigation bar is set to **US East (N. Virginia) `us-east-1`**.
3. In the navigation pane, choose **User pools**, then click **Create user pool**.

---

#### Step 2: Configure Sign-in Experience

1. Under **Authentication providers**, choose **Cognito user pool**.
2. Under **Cognito user pool sign-in options**, check **Email**:
   + Users will use their email address as their unique username when signing up and logging in.
   + Uncheck *User name* and *Phone number*.

![Configure Sign-in Experience](/images/5-Workshop/5.3-Cognito/cognito-01-sign-in-experience.png)

3. Click **Next**.

---

#### Step 3: Configure Security Requirements

1. **Password policy:** Select **Cognito defaults**:
   + Minimum password length: `8` characters.
   + Requires at least one uppercase letter, one lowercase letter, one number, and one special character.
2. **Multi-factor authentication (MFA):** Select **No MFA**.
   + *Note:* While MFA is recommended for production environments, selecting No MFA minimizes SMS and authenticator app configuration overhead during workshop testing.
3. **User account recovery:** Keep **Self-service account recovery** checked with delivery mechanism set to **Email**.

![Configure Security Requirements](/images/5-Workshop/5.3-Cognito/cognito-02-security-requirements.png)

4. Click **Next**.

---

#### Step 4: Configure Sign-up Experience

1. Under **Self-service sign-up**, ensure **Enable self-registration** is checked so users can register via the frontend.
2. Under **Cognito-assisted verification and confirmation**, select **Send email message, verify email address**.
3. Under **Required attributes**, verify that **email** is marked as required.
4. Click **Next**.

---

#### Step 5: Configure Message Delivery

1. Under **Email provider**, select **Send email with Cognito**.
   + This uses Cognito's default email service, which provides sufficient daily message quota for development and testing without requiring custom Amazon SES domain verification.
2. Click **Next**.

---

#### Step 6: Integrate Your App & Configure App Client

1. **User pool name:** Enter `expense-tracker-user-pool`.

![User Pool Name](/images/5-Workshop/5.3-Cognito/cognito-03-user-pool-name.png)

2. Under **Initial app client**:
   + **App client name:** Enter `expense-tracker-web-client`.
   + **Client secret:** Select **Don't generate a client secret**.

{{% notice warning %}}
**Critical Architecture Requirement:** Never generate a client secret for a public Single Page Application (SPA) running in client browsers. SPAs cannot protect a secret key from browser inspection. Leaving the client secret disabled allows the React app to communicate directly with Cognito using standard public authorization flows.
{{% /notice %}}

![Configure App Client](/images/5-Workshop/5.3-Cognito/cognito-04-app-client.png)

3. Expand **Advanced app client settings**:
   + Verify **Authentication flows** includes:
     + `ALLOW_USER_SRP_AUTH`
     + `ALLOW_REFRESH_TOKEN_AUTH`
     + `ALLOW_USER_PASSWORD_AUTH`
4. Click **Next**.

---

#### Step 7: Review and Create

1. Review the configuration summary across all 6 steps.
2. Click **Create user pool**.
3. Once created, click on `expense-tracker-user-pool` to view its summary page.
4. Note down the following values for later integration steps:
   + **User pool ID:** (Format: `us-east-1_xxxxxxxxx`)
   + Under the **App integration** tab, scroll down to **App clients and analytics** and copy the **Client ID** (26-character alphanumeric string).

![User Pool Overview](/images/5-Workshop/5.3-Cognito/cognito-05-user-pool-overview.png)
