---
title : "Create Cognito User Pool & App Client"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.3.1 </b> "
---

In this module, you will provision the user authentication and identity management layer for the **Serverless Personal Expense Tracker** using **Amazon Cognito**. You will configure a **Cognito User Pool** and set up an **App Client** designed specifically for a public Single-Page Application (SPA) built with React, Vite, and TypeScript.

---

#### Step 1: Open Cognito Console & Start Creation

1. Sign in to the AWS Management Console and navigate to the [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
2. Verify that your active AWS Region in the navigation bar is set to **US East (N. Virginia) `us-east-1`**.
3. In the left navigation pane, choose **User pools**, then click **Create user pool** to launch the current application setup wizard.

---

#### Step 2: Configure Application Type & Sign-in Options in the Creation Wizard

The current Amazon Cognito creation flow provides a streamlined application setup wizard that configures the user directory and client application together in a single step:

1. **Application type:** Select **Single-page application (SPA)**.
   + This tells Cognito that your frontend is a client-side application (e.g., React, Vue, Angular) running directly in the user's web browser without a dedicated backend server serving session cookies.
2. **Name your application:**
   + In the creation wizard, Cognito suggests an auto-generated temporary application name (e.g., `My SPA app - -wktua`).
   + *Note:* In our completed setup, this client application is identified as `ExpenseTrackerWebClient`. You can enter `ExpenseTrackerWebClient` or retain the generated name and rename it after creation.
3. **Configure options (Options for sign-in identifiers):**
   + Under **Options for sign-in identifiers**, check **Email**.
   + Leave **Phone number** and **Username** unchecked. Users will register and sign in using their email address.
4. **Self-registration:**
   + Check **Enable self-registration**. This allows new users to create accounts independently directly from the frontend interface.
5. **Required attributes for sign-up:**
   + Under **Required attributes for sign-up**, select **`email`**.
   + Users must provide a valid email address during account registration.

![Configure SPA Creation Wizard & Sign-in Options](/images/5-Workshop/5.3-Cognito/cognito-01-sign-in-experience.png)

6. Click the creation confirmation button to provision the user pool.
   + *Note on automated defaults:* The SPA setup wizard automatically provisions your user pool with recommended cloud-native defaults: standard password policies, No MFA, default Cognito email delivery, and an initial public App Client without a client secret.

---

#### Step 3: Verify Security Requirements & Password Policy

Once the user pool is provisioned, you can inspect and verify its security configurations directly from the Cognito console:

1. In the left navigation pane, select your newly created user pool.
2. Navigate to the **Sign-in experience** tab and locate the **Password policy** card:
   + **Password minimum length:** `8 character(s)`.
   + **Password requirements:** Must contain at least 1 number, 1 special character, 1 uppercase letter, and 1 lowercase letter.
   + **Temporary passwords set by administrators expire in:** `7 day(s)`.
   + **Password reuse:** Allow reuse of previous passwords.

![Password Policy Settings](/images/5-Workshop/5.3-Cognito/cognito-02-security-requirements.png)

3. **Multi-factor authentication (MFA):**
   + In the console security settings, verify that **MFA enforcement** is set to **No MFA**.
   + {{% notice info %}}
     **Console Layout Note:** In the current Amazon Cognito console, the **Password policy** and **MFA enforcement** settings are displayed in separate cards and views. The screenshot above focuses on the Password policy card. For the scope of this workshop, keeping MFA set to **No MFA** eliminates SMS delivery costs and third-party authenticator setup overhead during testing.
     {{% /notice %}}
4. **Message delivery:**
   + Under message delivery settings, verify that the email provider is set to **Send email with Cognito**.
   + *Note:* This built-in service provides a daily email quota sufficient for development and workshop verification without requiring Amazon SES domain verification. SMS messaging is not configured.

---

#### Step 4: Rename User Pool to `expense-tracker-user-pool`

To ensure consistency with project naming standards and infrastructure documentation:

1. Return to the **Overview** page of your user pool.
2. Near the top right of the page, click the **Rename** button.
3. In the **Edit user pool** dialog:
   + Under **User pool name**, enter `expense-tracker-user-pool`.
   + Click **Save changes**.

![Rename User Pool to expense-tracker-user-pool](/images/5-Workshop/5.3-Cognito/cognito-03-user-pool-name.png)

---

#### Step 5: Inspect App Client Configuration (`ExpenseTrackerWebClient`)

Next, examine the public application client created for your React frontend:

1. In your user pool console, select the **App integration** tab.
2. Scroll down to the **App clients** section and click on your client application (named `ExpenseTrackerWebClient`).
3. Verify the core App Client settings:
   + **App client name:** `ExpenseTrackerWebClient`.
   + **Client ID:** Copy the 26-character alphanumeric identifier (e.g., `6ngvi1al5pn81n534og2guvl27`).
   + **Client secret:** Displays `-` (None).

{{% notice warning %}}
**Critical Architecture Rule — No Client Secret for SPAs:**  
A Single-Page Application runs directly in the end-user's browser. Any client secret bundled into frontend JavaScript code can be easily extracted by inspecting network traffic or application source bundles. Leaving the client secret disabled (`-`) allows the React application to use secure public authentication flows without compromising security.
{{% /notice %}}

4. Verify the active **Authentication flows**:
   + `Choice-based sign-in`
   + `Secure remote password (SRP)`
   + `Get user tokens from existing authenticated sessions`
   + *Note:* Do not manually enable additional or legacy flows. Any specialized flow needed during frontend integration will be configured in the corresponding module.
5. Verify **Token expiration settings**:
   + **Access token expiration:** `60 minutes`.
   + **ID token expiration:** `60 minutes`.
   + **Refresh token expiration:** `5 day(s)`.
   + **Authentication flow session duration:** `3 minutes`.
6. Advanced settings: Confirm that **Enable token revocation** and **Enable prevent user existence errors** are active.

![App Client Details - ExpenseTrackerWebClient](/images/5-Workshop/5.3-Cognito/cognito-04-app-client.png)

{{% notice tip %}}
**Scope & Integration Boundaries:**  
Do not configure Hosted UI callback URLs, sign-out URLs, or OAuth 2.0 scopes at this stage. Our React application communicates directly with Cognito APIs via AWS SDK / Amplify Auth libraries rather than Hosted UI redirects. Full client-side integration is implemented in [7. React Frontend Integration](../../5.7-Frontend-integration/).
{{% /notice %}}

---

#### Step 6: Review User Pool Overview & Record Key Identifiers

Return to the **Overview** page of `expense-tracker-user-pool` to verify the provisioned resource details and collect your deployment parameters:

1. Confirm the primary details on the **Overview** page:
   + **User pool name:** `expense-tracker-user-pool`
   + **User pool ID:** Displayed under **User pool information** (format: `us-east-1_xxxxxxxxx`).
   + **Regions:** `US East (N. Virginia)` (`us-east-1`).
   + **OpenID Connect configuration URL:** Cognito's standard discovery endpoint.
   + **Token signing key URL:** JSON Web Key Set (JWKS) URL used by downstream services (such as API Gateway) to cryptographically verify token signatures.

![User Pool Overview](/images/5-Workshop/5.3-Cognito/cognito-05-user-pool-overview.png)

2. Note down the two critical identifiers needed across subsequent workshop modules:

| Parameter Name | Example Format | Purpose & Where Used |
| :--- | :--- | :--- |
| **AWS Region** | `us-east-1` | Target region across AWS CLI, Lambda, and API Gateway |
| **User Pool ID** | `us-east-1_xxxxxxxxx` | Identifies your user directory; required by API Gateway JWT Authorizer |
| **App Client ID** | `6ngvi1al5pn81n534og2guvl27` | Public client identifier; required by API Gateway JWT Authorizer Audience & Frontend config |

{{% notice info %}}
**Distinguishing User Pool ID vs. App Client ID:**  
- **User Pool ID** (`us-east-1_...`): Identifies your entire Cognito user directory and serves as the OIDC token issuer (`iss`).
- **App Client ID** (26 characters alphanumeric): Identifies the specific client application (React SPA) requesting tokens and appears in the token audience claim (`aud`).
- Do not confuse these two parameters or use them interchangeably.
{{% /notice %}}

---

#### Completion Status & Next Steps

{{% notice info %}}
**Resource Provisioning Checkpoint:**  
At this stage, the Amazon Cognito User Pool and public App Client are successfully created and configured in `us-east-1`. Creating these resources sets up the authentication infrastructure but does not mean the web frontend is integrated or that users have authenticated yet. User provisioning and JWT token verification will be verified in the next section, followed by database and API setup.
{{% /notice %}}

Proceed to the next module: [5.3.2 User Registration, Authentication & JWT Verification](../5.3.2-test-auth/) to test user creation and inspect issued JWT claims.
