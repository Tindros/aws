---
title : "Kết nối Cổng API Gateway & Dữ liệu DynamoDB"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.7.2 </b> "
---

Trong phần này, bạn sẽ thay thế toàn bộ mảng dữ liệu giả lập trong tệp `src/services/expenseService.ts` bằng các yêu cầu HTTP bất đồng bộ thực tế gửi đến **Amazon API Gateway HTTP API**, truyền kèm mã định danh JWT và lưu trữ dữ liệu thực vào **Amazon DynamoDB**.

---

#### Bước 1: Triển khai Dịch vụ Quản lý Chi tiêu Thực tế

Mở tệp `frontend/src/services/expenseService.ts` và thay thế mã nguồn bằng đoạn mã gọi REST API thực tế sau:

```typescript
import type { 
  Expense, 
  DashboardSummary, 
  CategorySummary, 
  SpendingPeriod, 
  CreateExpenseInput, 
  UpdateExpenseInput 
} from '../types/expense';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || '';

/**
 * Hàm bổ trợ gửi các yêu cầu HTTPS có gắn kèm tiêu đề xác thực JWT
 */
async function apiRequest<T>(endpoint: string, options: RequestInit = {}): Promise<T> {
  const token = localStorage.getItem('auth_token');

  const headers: Record<string, string> = {
    'Content-Type': 'application/json',
    ...(options.headers as Record<string, string> || {}),
  };

  if (token) {
    headers['Authorization'] = `Bearer ${token}`;
  }

  const response = await fetch(`${API_BASE_URL}${endpoint}`, {
    ...options,
    headers
  });

  if (response.status === 401) {
    // Token đã hết hạn hoặc không hợp lệ - xóa phiên làm việc và chuyển hướng về trang đăng nhập
    localStorage.removeItem('auth_token');
    window.location.href = '/login';
    throw new Error('Phiên làm việc đã hết hạn. Vui lòng đăng nhập lại.');
  }

  if (!response.ok) {
    const errorBody = await response.json().catch(() => ({}));
    throw new Error(errorBody.message || `Lỗi API: ${response.statusText}`);
  }

  return response.json();
}

export const expenseService = {
  // Lấy dữ liệu phân tích thống kê theo thời gian thực cho bảng điều khiển
  async getDashboardSummary(): Promise<DashboardSummary> {
    const summary = await apiRequest<any>('/summary');
    return {
      totalSpent: summary.totalSpent || 0,
      thisMonth: summary.thisMonth || 0,
      numberOfExpenses: summary.numberOfExpenses || 0,
      topCategory: summary.topCategory || 'None'
    };
  },

  // Lấy toàn bộ danh sách chi tiêu của người dùng từ DynamoDB
  async getExpenses(): Promise<Expense[]> {
    const items = await apiRequest<any[]>('/expenses');
    return items.map(item => ({
      id: item.expenseId,
      description: item.description,
      amount: Number(item.amount),
      category: item.category,
      date: item.date
    }));
  },

  // Lấy danh sách các khoản chi gần đây nhất cho bảng điều khiển
  async getRecentExpenses(limit: number = 5): Promise<Expense[]> {
    const expenses = await this.getExpenses();
    return expenses.slice(0, limit);
  },

  // Lấy dữ liệu phân bổ theo danh mục cho biểu đồ trực quan
  async getCategoryBreakdown(): Promise<CategorySummary[]> {
    const summary = await apiRequest<any>('/summary');
    return summary.categoryBreakdown || [];
  },

  // Tạo dòng thời gian chi tiêu cho các tháng gần nhất
  async getSpendingOverview(): Promise<SpendingPeriod[]> {
    const expenses = await this.getExpenses();
    const periods: SpendingPeriod[] = [];
    const now = new Date();

    for (let i = 5; i >= 0; i--) {
      const d = new Date(now.getFullYear(), now.getMonth() - i, 1);
      const periodLabel = d.toLocaleString('en-US', { month: 'short' });
      const yearMonth = `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, '0')}`;

      const amount = expenses
        .filter(e => e.date.startsWith(yearMonth))
        .reduce((sum, e) => sum + e.amount, 0);

      periods.push({ period: periodLabel, amount });
    }
    return periods;
  },

  // Lấy thông tin một khoản chi theo ID
  async getExpenseById(id: string): Promise<Expense | null> {
    try {
      const item = await apiRequest<any>(`/expenses/${id}`);
      return {
        id: item.expenseId,
        description: item.description,
        amount: Number(item.amount),
        category: item.category,
        date: item.date
      };
    } catch {
      return null;
    }
  },

  // Tạo mới một khoản chi tiêu
  async createExpense(input: CreateExpenseInput): Promise<Expense> {
    const item = await apiRequest<any>('/expenses', {
      method: 'POST',
      body: JSON.stringify(input)
    });
    return {
      id: item.expenseId,
      description: item.description,
      amount: Number(item.amount),
      category: item.category,
      date: item.date
    };
  },

  // Cập nhật thông tin khoản chi tiêu
  async updateExpense(id: string, input: UpdateExpenseInput): Promise<Expense> {
    const item = await apiRequest<any>(`/expenses/${id}`, {
      method: 'PUT',
      body: JSON.stringify(input)
    });
    return {
      id: item.expenseId,
      description: item.description,
      amount: Number(item.amount),
      category: item.category,
      date: item.date
    };
  },

  // Xóa khoản chi tiêu
  async deleteExpense(id: string): Promise<void> {
    await apiRequest<void>(`/expenses/${id}`, {
      method: 'DELETE'
    });
  }
};
```

---

#### Bước 2: Kiểm thử Trải nghiệm Người dùng Toàn diện Cục bộ

1. Đảm bảo máy chủ phát triển đang chạy (`npm run dev`).
2. Đăng nhập vào ứng dụng bằng tài khoản đã được xác thực của bạn.
3. Quan sát trang **Dashboard** thời gian thực:
   + Các chỉ số tài chính được tải động từ endpoint `/summary` của API Gateway.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-03-dashboard-live.png`  
*(Cần chụp khi triển khai: Giao diện Dashboard hiển thị số liệu tài chính thực tế tải từ API Gateway)*
{{% /notice %}}
<!-- ![Dashboard Thực tế](/images/5-Workshop/5.7-Frontend-integration/frontend-03-dashboard-live.png) -->

4. Chuyển sang mục **New Expense** (`/expenses/new`), điền thông tin (ví dụ: Mô tả: `Văn phòng phẩm`, Số tiền: `35.00`, Danh mục: `Shopping`, Ngày: `2026-10-14`), sau đó nhấn **Save Expense**:
   + Frontend sẽ gửi yêu cầu `POST /expenses` đến API Gateway để lưu vào DynamoDB.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-04-add-expense.png`  
*(Cần chụp khi triển khai: Thao tác tạo mới khoản chi tiêu qua biểu mẫu React frontend)*
{{% /notice %}}
<!-- ![Biểu mẫu Tạo Chi tiêu](/images/5-Workshop/5.7-Frontend-integration/frontend-04-add-expense.png) -->

5. Chuyển sang mục **Expenses** (`/expenses`):
   + Bảng danh sách hiển thị bản ghi chi tiêu vừa tạo được lưu trữ an toàn trong DynamoDB.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-05-expenses-table.png`  
*(Cần chụp khi triển khai: Bảng danh sách hiển thị các bản ghi chi tiêu thực tế từ DynamoDB)*
{{% /notice %}}
<!-- ![Bảng Danh sách Chi tiêu](/images/5-Workshop/5.7-Frontend-integration/frontend-05-expenses-table.png) -->

---

#### Bước Tiếp theo

Chuyển sang phần [Giám sát với CloudWatch & Triển khai Amplify Hosting](../../5.8-Monitoring-and-Amplify/) để theo dõi nhật ký thực thi và triển khai ứng dụng web lên Internet.
