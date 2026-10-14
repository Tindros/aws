---
title : "Kết nối Xác thực Amazon Cognito"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.7.1 </b> "
---

Trong phần này, bạn sẽ cấu hình ứng dụng React frontend tại máy cục bộ kết nối trực tiếp với **Amazon Cognito User Pool**, thay thế hoàn toàn dịch vụ giả lập bằng cơ chế xác thực SRP tiêu chuẩn.

---

#### Bước 1: Cài đặt Thư viện Amazon Cognito SDK

Mở cửa sổ dòng lệnh (terminal) trong thư mục `frontend/` và cài đặt gói thư viện `amazon-cognito-identity-js`:

```bash
cd frontend
npm install amazon-cognito-identity-js
```

---

#### Bước 2: Cấu hình Tệp Biến Môi trường (.env)

Tạo một tệp `.env` tại thư mục gốc của `frontend/` và điền các giá trị tài nguyên AWS của bạn:

```ini
# Cấu hình Triển khai AWS
VITE_AWS_REGION=us-east-1
VITE_COGNITO_USER_POOL_ID=us-east-1_xxxxxxxxx
VITE_COGNITO_CLIENT_ID=3xxxxxxxxxxxxxxxxxxxxxxxxx
VITE_API_BASE_URL=https://<API_ID>.execute-api.us-east-1.amazonaws.com
```

{{% notice warning %}}
**Lưu ý An toàn Bảo mật:** Thay thế các giá trị giữ chỗ bằng ID User Pool và Client ID thực tế của bạn. Do App Client không sử dụng mã bí mật (Client Secret), các thông số cấu hình công khai này có thể được nhúng an toàn vào mã nguồn ứng dụng client. Tuyệt đối không lưu các khóa bí mật (AWS Access Key/Secret Key) hoặc mật khẩu vào tệp này.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-01-env-file.png`  
*(Cần chụp khi triển khai: Tệp cấu hình .env của frontend hiển thị các thông số Cognito và API Gateway)*
{{% /notice %}}
<!-- ![Cấu hình Môi trường Frontend](/images/5-Workshop/5.7-Frontend-integration/frontend-01-env-file.png) -->

---

#### Bước 3: Cập nhật Dịch vụ Xác thực Thực tế

Chỉnh sửa tệp `src/services/authService.ts` để triển khai logic xác thực thực tế với Cognito:

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
    return new Promise((resolve) => {
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

          // Lưu token JWT vào localStorage để gắn vào tiêu đề Authorization của các request sau
          localStorage.setItem('auth_token', accessToken);
          localStorage.setItem('access_token', accessToken);
          localStorage.setItem('user_email', credentials.email);

          resolve({
            success: true,
            token: accessToken,
            message: 'Đăng nhập thành công'
          });
        },
        onFailure: (err) => {
          console.error('Cognito login error:', err);
          resolve({
            success: false,
            message: err.message || 'Xác thực không thành công'
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

      userPool.signUp(data.email, data.password, attributeList, [], (err) => {
        if (err) {
          console.error('Cognito sign-up error:', err);
          return resolve({
            success: false,
            message: err.message || 'Đăng ký không thành công'
          });
        }

        resolve({
          success: true,
          message: 'Đăng ký thành công! Vui lòng kiểm tra email để nhận mã kích hoạt tài khoản.'
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

#### Bước 4: Kiểm thử Đăng nhập Thực tế Cục bộ

1. Khởi chạy máy chủ phát triển Vite:
   ```bash
   npm run dev
   ```
2. Mở trình duyệt và truy cập `http://localhost:5173/login`.
3. Nhập thông tin tài khoản thử nghiệm đã tạo ở Phần 5.3.2 (`demo-user@example.com` và mật khẩu tương ứng).
4. Nhấn **Sign in**.
5. Xác nhận quá trình đăng nhập thành công và Access Token dùng cho API được lưu trong `localStorage`. Với ứng dụng thực tế, nên cân nhắc thiết kế phiên an toàn hơn thay vì lưu token dài hạn trong `localStorage`; ví dụ này được đơn giản hóa cho mục đích học tập.

{{% notice tip %}}
**Xử lý Sự cố Tương thích Vite & Node Global:**  
Nếu cửa sổ Console của trình duyệt xuất hiện lỗi `Uncaught ReferenceError: global is not defined` khi khởi chạy ứng dụng, môi trường Vite hiện đại có thể cần một shim nhẹ cho biến toàn cục Node.js mà thư viện `amazon-cognito-identity-js` tham chiếu đến.  
Bạn có thể khắc phục bằng cách thêm đoạn mã shim sau vào dòng đầu tiên của tệp `src/main.tsx`:
```typescript
// Khắc phục tương thích trình duyệt cho amazon-cognito-identity-js
if (typeof window !== 'undefined' && !(window as any).global) {
  (window as any).global = window;
}
```
*Lưu ý: Chỉ áp dụng thay đổi này nếu bạn gặp đúng lỗi nêu trên trong trình duyệt.*
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-02-login-success.png`  
*(Cần chụp khi triển khai: Giao diện React frontend đăng nhập thành công với phiên xác thực Cognito)*
{{% /notice %}}
<!-- ![Đăng nhập Thành công](/images/5-Workshop/5.7-Frontend-integration/frontend-02-login-success.png) -->
