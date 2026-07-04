# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/kpi-cards-successful-payments-range.spec.ts >> Dashboard — KPI Cards >> TC5 — Successful Payments percentage is between 0 and 100
- Location: tests/UI/Dashboard/kpi-cards-successful-payments-range.spec.ts:34:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/Hi,/)
Expected: visible
Timeout: 10000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 10000ms
  - waiting for getByText(/Hi,/)

```

```yaml
- complementary:
  - img "eCitizen"
  - navigation:
    - link "Dashboard":
      - /url: /dashboard
      - img
      - text: Dashboard
    - link "Transactions":
      - /url: /transaction
      - img
      - text: Transactions
    - link "Settlements":
      - /url: /settlements
      - img
      - text: Settlements
    - link "Reports":
      - /url: /reports
      - img
      - text: Reports
    - link "Settings":
      - /url: /settings
      - img
      - text: Settings
  - button "Logout":
    - img
    - text: Logout
  - button "Collapse sidebar":
    - img
- banner:
  - button "Notifications": "3"
  - text: A Ashil ADMIN PLATFORM
- main:
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
- region "Notifications alt+T"
```

# Test source

```ts
  1   | import { Page, Locator, expect } from '@playwright/test';
  2   | 
  3   | export class DashboardPage {
  4   |   readonly page: Page;
  5   | 
  6   |   // Header
  7   |   readonly greeting: Locator;
  8   |   readonly subGreeting: Locator;
  9   |   readonly dateFilterDropdown: Locator;
  10  | 
  11  |   // KPI Cards
  12  |   readonly revenueKesCard: Locator;
  13  |   readonly revenueUsdCard: Locator;
  14  |   readonly transactionCountCard: Locator;
  15  |   readonly successfulPaymentsCard: Locator;
  16  |   readonly failedPaymentsCard: Locator;
  17  |   readonly allViewDetailsLinks: Locator;
  18  | 
  19  |   // Monthly Revenue Chart
  20  |   readonly revenueChartHeading: Locator;
  21  |   readonly revenueChartCanvas: Locator;
  22  |   readonly chartLegend: Locator;
  23  | 
  24  |   // Gateway Payments
  25  |   readonly gatewayPaymentsHeading: Locator;
  26  |   readonly transactionsTodayMetric: Locator;
  27  |   readonly successRateMetric: Locator;
  28  |   readonly transactionsByMethodChart: Locator;
  29  |   readonly successVsFailureChart: Locator;
  30  | 
  31  |   // Sidebar Navigation
  32  |   readonly navDashboard: Locator;
  33  |   readonly navTransactions: Locator;
  34  |   readonly navRefunds: Locator;
  35  |   readonly navSettlements: Locator;
  36  |   readonly navReports: Locator;
  37  |   readonly navUserManagement: Locator;
  38  |   readonly navSettings: Locator;
  39  |   readonly navLogout: Locator;
  40  | 
  41  |   constructor(page: Page) {
  42  |     this.page = page;
  43  | 
  44  |     // Header
  45  |     this.greeting = page.getByText(/Hi,/);
  46  |     this.subGreeting = page.getByText(/eCitizen/i).first();
  47  |     this.dateFilterDropdown = page.getByRole('combobox').or(
  48  |       page.locator('[class*="dropdown"]').filter({ hasText: /today/i }).first(),
  49  |     );
  50  | 
  51  |     // KPI Cards — new UI has 3 cards: Revenue generated, No of Transactions, Payments
  52  |     this.revenueKesCard        = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /Revenue generated/i }).first();
  53  |     this.revenueUsdCard        = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /No of Transactions/i }).first();
  54  |     this.transactionCountCard  = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /No of Transactions/i }).first();
  55  |     this.successfulPaymentsCard = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /Successful Payments/i }).first();
  56  |     this.failedPaymentsCard    = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /Failed Payments/i }).first();
  57  |     this.allViewDetailsLinks   = page.locator('a, button, [role="link"], [role="button"]').filter({ hasText: /View Details|View Transactions/i });
  58  | 
  59  |     // Monthly Revenue Chart
  60  |     this.revenueChartHeading = page.getByText('Monthly Revenue for All Services');
  61  |     this.revenueChartCanvas  = page.locator('canvas').first();
  62  |     this.chartLegend         = page.locator('[class*="legend"], [class*="chart-legend"]').first();
  63  | 
  64  |     // Gateway Payments — label changed to "Transactions (today)" in new UI
  65  |     this.gatewayPaymentsHeading    = page.getByText('Gateway Payments');
  66  |     this.transactionsTodayMetric   = page.getByText(/Transactions \(today\)/i);
  67  |     this.successRateMetric         = page.getByText(/Success Rate/i).first();
  68  |     this.transactionsByMethodChart = page.getByText('Transactions by Payment Method');
  69  |     this.successVsFailureChart     = page.getByText('Success vs Failure by Method');
  70  | 
  71  |     // Sidebar Navigation
  72  |     this.navDashboard       = page.getByRole('link', { name: /^Dashboard$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Dashboard$/i));
  73  |     this.navTransactions    = page.getByRole('link', { name: /^Transactions$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Transactions$/i));
  74  |     this.navRefunds         = page.getByRole('link', { name: /^Refunds$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Refunds$/i));
  75  |     this.navSettlements     = page.getByRole('link', { name: /^Settlements$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Settlements$/i));
  76  |     this.navReports         = page.getByRole('link', { name: /^Reports$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Reports$/i));
  77  |     this.navUserManagement  = page.getByRole('link', { name: /User Management/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/User Management/i));
  78  |     this.navSettings        = page.getByRole('link', { name: /^Settings$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Settings$/i));
  79  |     this.navLogout          = page.getByRole('button', { name: /logout/i }).or(page.getByText(/logout/i));
  80  |   }
  81  | 
  82  |   async goto() {
  83  |     await this.page.goto('/dashboard');
  84  |     await this.page.waitForLoadState('networkidle');
  85  |   }
  86  | 
  87  |   async assertPageLoaded() {
  88  |     await expect(this.page).toHaveURL(/\/dashboard/, { timeout: 15000 });
> 89  |     await expect(this.greeting).toBeVisible({ timeout: 10000 });
      |                                 ^ Error: expect(locator).toBeVisible() failed
  90  |   }
  91  | 
  92  |   async getKpiValue(cardLocator: Locator): Promise<string> {
  93  |     const text = await cardLocator.innerText();
  94  |     return text.trim();
  95  |   }
  96  | 
  97  |   async selectDateFilter(option: string) {
  98  |     await this.dateFilterDropdown.click();
  99  |     await this.page.getByRole('option', { name: option }).click();
  100 |   }
  101 | }
  102 | 
```