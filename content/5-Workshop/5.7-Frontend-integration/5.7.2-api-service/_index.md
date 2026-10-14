---
title : "Connect API Gateway & DynamoDB Endpoints"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.7.2 </b> "
---

In this module, you will replace the local mock data in `src/services/expenseService.ts` with real asynchronous HTTP requests to your **Amazon API Gateway HTTP API**, passing the Cognito Access Token and persisting data to **Amazon DynamoDB**.

---

#### Step 1: Implement the Cloud-Connected Expense Service

Open `frontend/src/services/expenseService.ts` and replace its mock array implementation with live REST API calls:

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

if (!API_BASE_URL) {
  throw new Error('VITE_API_BASE_URL is missing. Configure it in the frontend .env file.');
}

/**
 * Helper to dispatch authenticated HTTPS requests with JWT Authorization headers
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
    // Token expired or invalid - clear session and redirect to login
    localStorage.removeItem('auth_token');
    localStorage.removeItem('access_token');
    localStorage.removeItem('user_email');
    window.location.href = '/login';
    throw new Error('Session expired. Please log in again.');
  }

  if (!response.ok) {
    const errorBody = await response.json().catch(() => ({}));
    throw new Error(errorBody.message || `API error: ${response.statusText}`);
  }

  return response.json();
}

export const expenseService = {
  // Fetch real-time dashboard analytics
  async getDashboardSummary(): Promise<DashboardSummary> {
    const summary = await apiRequest<any>('/summary');
    return {
      totalSpent: summary.totalSpent || 0,
      thisMonth: summary.thisMonth || 0,
      numberOfExpenses: summary.numberOfExpenses || 0,
      topCategory: summary.topCategory || 'None'
    };
  },

  // Fetch all expenses for the authenticated user from DynamoDB
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

  // Fetch recent expenses for the dashboard
  async getRecentExpenses(limit: number = 5): Promise<Expense[]> {
    const expenses = await this.getExpenses();
    return expenses.slice(0, limit);
  },

  // Fetch category breakdown for visual charts
  async getCategoryBreakdown(): Promise<CategorySummary[]> {
    const summary = await apiRequest<any>('/summary');
    return summary.categoryBreakdown || [];
  },

  // Generate spending timeline for recent months
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

  // Get single expense by ID
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
    } catch (error) {
      // Treat only a genuine 404/not-found response as a missing expense; do not hide auth or network failures.
      if (error instanceof Error && error.message === 'Expense not found') {
        return null;
      }
      throw error;
    }
  },

  // Create new expense record
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

  // Update existing expense record
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

  // Delete expense record
  async deleteExpense(id: string): Promise<void> {
    await apiRequest<void>(`/expenses/${id}`, {
      method: 'DELETE'
    });
  }
};
```

---

#### Step 2: Test End-to-End User Experience Locally

1. Ensure the development server is active (`npm run dev`).
2. Log into the application using your verified account.
3. Observe the live **Dashboard**:
   + Metrics load dynamically from the `/summary` API Gateway endpoint.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-03-dashboard-live.png`  
*(To be captured during deployment: Dashboard view displaying real financial summaries fetched from API Gateway)*
{{% /notice %}}
<!-- ![Live Dashboard](/images/5-Workshop/5.7-Frontend-integration/frontend-03-dashboard-live.png) -->

4. Navigate to **New Expense** (`/expenses/new`), fill out the form (e.g., Description: `Office Supplies`, Amount: `35.00`, Category: `Shopping`, Date: `2026-10-14`), and click **Save Expense**:
   + The frontend dispatches a `POST /expenses` request to API Gateway.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-04-add-expense.png`  
*(To be captured during deployment: Adding a new expense via the React frontend form)*
{{% /notice %}}
<!-- ![Add Expense Form](/images/5-Workshop/5.7-Frontend-integration/frontend-04-add-expense.png) -->

5. Navigate to **Expenses** (`/expenses`):
   + The table displays the newly created record persisted in DynamoDB.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.7-Frontend-integration/frontend-05-expenses-table.png`  
*(To be captured during deployment: Expenses list page populated with live records from DynamoDB)*
{{% /notice %}}
<!-- ![Expenses Table](/images/5-Workshop/5.7-Frontend-integration/frontend-05-expenses-table.png) -->

---

#### Next Step

Proceed to [CloudWatch Monitoring & Amplify Hosting](../../5.8-Monitoring-and-Amplify/) to inspect runtime logs and deploy your application to the global web.
