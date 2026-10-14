---
title : "Connect Amazon Cognito Authentication"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.7.1 </b> "
---

In this module, you will configure your local React frontend to authenticate directly with **Amazon Cognito User Pool**, replacing the developer prototype with production-grade SRP authentication.

---

#### Step 1: Install Amazon Cognito SDK

Open your terminal in the `frontend/` directory and install the lightweight `amazon-cognito-identity-js` package:

```bash
cd frontend
npm install amazon-cognito-identity-js
```

---

#### Step 2: Configure Environment Variables

Create a `.env` file in the root of the `frontend/` folder with your AWS resource identifiers:

```ini
# AWS Deployment Configuration
VITE_AWS_REGION=us-east-1
VITE_COGNITO_USER_POOL_ID=us-east-1_xxxxxxxxx
VITE_COGNITO_CLIENT_ID=3xxxxxxxxxxxxxxxxxxxxxxxxx
VITE_API_BASE_URL=https://<API_ID>.execute-api.us-east-1.amazonaws.com
```

{{% notice warning %}}
**Security Reminder:** Replace placeholder values with your actual User Pool ID and App Client ID. Because the App Client does not use a client secret, these public configuration values can be safely built into client bundles. Never commit private AWS access keys or passwords to version control.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-01-env-file.png`  
*(To be captured during deployment: Frontend .env configuration showing Cognito and API Gateway parameters)*
{{% /notice %}}
<!-- ![Configure Frontend Environment](/images/5-Workshop/5.7-Frontend-integration/frontend-01-env-file.png) -->

---

#### Step 3: Implement Real Authentication Service

Update `src/services/authService.ts` to implement real authentication methods:

```typescript
import { 
  CognitoUserPool, 
  CognitoUser, 
  AuthenticationDetails, 
  CognitoUserAttribute 
} from 'amazon-cognito-identity-js';
import type { LoginCredentials, RegisterData, AuthResult } from '../types/auth';

const poolData = {
  UserPoolId: import.meta.env.VITE_COGNITO_USER_POOL_ID || '',
  ClientId: import.meta.env.VITE_COGNITO_CLIENT_ID || ''
};

const userPool = new CognitoUserPool(poolData);

export const authService = {
  async login(credentials: LoginCredentials): Promise<AuthResult> {
    return new Promise((resolve, reject) => {
      const authenticationDetails = new AuthenticationDetails({
        Username: credentials.email,
        Password: credentials.password
      });

      const cognitoUser = new CognitoUser({
        Username: credentials.email,
        Pool: userPool
      });

      cognitoUser.authenticateUser(authenticationDetails, {
        onSuccess: (result) => {
          const idToken = result.getIdToken().getJwtToken();
          const accessToken = result.getAccessToken().getJwtToken();

          // Store active JWT in localStorage for API requests
          localStorage.setItem('auth_token', accessToken);
          localStorage.setItem('access_token', accessToken);
          localStorage.setItem('user_email', credentials.email);

          resolve({
            success: true,
            token: accessToken,
            message: 'Login successful'
          });
        },
        onFailure: (err) => {
          console.error('Cognito login error:', err);
          resolve({
            success: false,
            message: err.message || 'Authentication failed'
          });
        }
      });
    });
  },

  async register(data: RegisterData): Promise<AuthResult> {
    return new Promise((resolve) => {
      const attributeList = [
        new CognitoUserAttribute({ Name: 'email', Value: data.email })
      ];

      userPool.signUp(data.email, data.password, attributeList, [], (err, result) => {
        if (err) {
          console.error('Cognito sign-up error:', err);
          return resolve({
            success: false,
            message: err.message || 'Registration failed'
          });
        }

        resolve({
          success: true,
          message: 'Registration successful! Please check your email for the confirmation code.'
        });
      });
    });
  },

  logout(): void {
    const currentUser = userPool.getCurrentUser();
    if (currentUser) {
      currentUser.signOut();
    }
    localStorage.removeItem('auth_token');
    localStorage.removeItem('access_token');
    localStorage.removeItem('user_email');
  },

  getToken(): string | null {
    return localStorage.getItem('auth_token');
  },

  isAuthenticated(): boolean {
    return !!this.getToken();
  }
};
```

---

#### Step 4: Verify Local Sign-In

1. Start the Vite local development server:
   ```bash
   npm run dev
   ```
2. Navigate to `http://localhost:5173/login` in your browser.
3. Enter the test account credentials created in Section 5.3.2 (`demo-user@example.com` and your password).
4. Click **Sign in**.
5. Verify that authentication succeeds and the browser stores the Access Token used for API requests. For production apps, prefer a secure session design over long-lived tokens in `localStorage`; this workshop keeps the example simple for learning.

{{% notice tip %}}
**Troubleshooting Vite & Node Global Compatibility:**  
If your browser developer console shows `Uncaught ReferenceError: global is not defined` when loading the application, modern Vite environments may need a lightweight browser shim for legacy cryptographic routines in `amazon-cognito-identity-js`.  
You can resolve this by adding the following shim at the very top of `src/main.tsx`:
```typescript
// Browser compatibility shim for amazon-cognito-identity-js
if (typeof window !== 'undefined' && !(window as any).global) {
  (window as any).global = window;
}
```
*Note: Apply this change only if you encounter this specific runtime error.*
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-02-login-success.png`  
*(To be captured during deployment: React frontend successfully logged in with active Cognito session)*
{{% /notice %}}
<!-- ![Login Success](/images/5-Workshop/5.7-Frontend-integration/frontend-02-login-success.png) -->
