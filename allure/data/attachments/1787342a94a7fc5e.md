# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-rc-tc3-revenue-chart-date-filter-default.spec.ts >> Dashboard — Monthly Revenue Chart >> TC3 — Date filter dropdown defaults to "Last 90 Days"
- Location: tests/UI/Dashboard/dash-rc-tc3-revenue-chart-date-filter-default.spec.ts:29:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/dashboard/
Received string:  "https://api.rms.dev.demo-fsit.com/auth/login"
Timeout: 15000ms

Call log:
  - Expect "toHaveURL" with timeout 15000ms
    31 × unexpected value "https://api.rms.dev.demo-fsit.com/auth/login"

```

```yaml
- img "Unified Control"
- paragraph: Government services, now at your fingertips
- img
- paragraph: RMS
- paragraph: National Treasury
- heading "Login" [level=1]
- paragraph: Access Revenue Management System (RMS)
- text: Email address
- textbox "Enter Your email"
- paragraph
- text: Password
- textbox "Enter Password"
- button
- paragraph
- checkbox "Remember for 30 days"
- text: Remember for 30 days
- link "Forgot Password?":
  - /url: /auth/forgot-password
- button "Log In →"
- text: Or
- button "Login with SSO →"
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
  34  |   readonly navSettlements: Locator;
  35  |   readonly navReports: Locator;
  36  |   readonly navUserManagement: Locator;
  37  |   readonly navSettings: Locator;
  38  |   readonly navLogout: Locator;
  39  | 
  40  |   constructor(page: Page) {
  41  |     this.page = page;
  42  | 
  43  |     // Header
  44  |     this.greeting = page.getByText(/Hi,/);
  45  |     this.subGreeting = page.getByText(/eCitizen/i).first();
  46  |     this.dateFilterDropdown = page.getByRole('combobox').or(
  47  |       page.locator('[class*="dropdown"]').filter({ hasText: /today/i }).first(),
  48  |     );
  49  | 
  50  |     // KPI Cards — new UI has 3 cards: Revenue generated, No of Transactions, Payments
  51  |     this.revenueKesCard        = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /Revenue generated/i }).first();
  52  |     this.revenueUsdCard        = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /No of Transactions/i }).first();
  53  |     this.transactionCountCard  = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /No of Transactions/i }).first();
  54  |     this.successfulPaymentsCard = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /Successful Payments/i }).first();
  55  |     this.failedPaymentsCard    = page.locator('[class*="card"], [class*="widget"], [class*="stat"]').filter({ hasText: /Failed Payments/i }).first();
  56  |     this.allViewDetailsLinks   = page.locator('a, button, [role="link"], [role="button"]').filter({ hasText: /View Details|View Transactions/i });
  57  | 
  58  |     // Monthly Revenue Chart
  59  |     this.revenueChartHeading = page.getByText('Monthly Revenue for All Services');
  60  |     // The chart is rendered with an SVG-based library, not <canvas> — broadened
  61  |     // to accept either so this doesn't depend on the specific charting library.
  62  |     this.revenueChartCanvas  = page.locator('canvas, svg').first();
  63  |     this.chartLegend         = page.locator('[class*="legend"], [class*="chart-legend"]').first();
  64  | 
  65  |     // Gateway Payments — label changed to "Transactions (today)" in new UI
  66  |     this.gatewayPaymentsHeading    = page.getByText('Gateway Payments');
  67  |     this.transactionsTodayMetric   = page.getByText(/Transactions \(today\)/i);
  68  |     this.successRateMetric         = page.getByText(/Success Rate/i).first();
  69  |     this.transactionsByMethodChart = page.getByText('Transactions by Payment Method');
  70  |     this.successVsFailureChart     = page.getByText('Success vs Failure by Method');
  71  | 
  72  |     // Sidebar Navigation
  73  |     this.navDashboard       = page.getByRole('link', { name: /^Dashboard$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Dashboard$/i));
  74  |     this.navTransactions    = page.getByRole('link', { name: /^Transactions$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Transactions$/i));
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
> 88  |     await expect(this.page).toHaveURL(/\/dashboard/, { timeout: 15000 });
      |                             ^ Error: expect(page).toHaveURL(expected) failed
  89  |     await expect(this.greeting).toBeVisible({ timeout: 10000 });
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