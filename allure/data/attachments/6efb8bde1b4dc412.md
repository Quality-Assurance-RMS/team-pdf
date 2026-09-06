# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-rc-tc5-revenue-chart-month-labels.spec.ts >> Dashboard — Monthly Revenue Chart >> TC5 — All 12 month labels (Jan–Dec) are present in the chart
- Location: tests/UI/Dashboard/dash-rc-tc5-revenue-chart-month-labels.spec.ts:30:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/dashboard/
Received string:  "https://api.rms.dev.demo-fsit.com/auth/login"
Timeout: 15000ms

Call log:
  - Expect "toHaveURL" with timeout 15000ms
    33 × unexpected value "https://api.rms.dev.demo-fsit.com/auth/login"

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
- textbox "Enter Your email" [disabled]
- paragraph
- text: Password
- textbox "Enter Password" [disabled]
- button [disabled]
- paragraph
- checkbox "Remember for 30 days" [disabled]
- text: Remember for 30 days
- link "Forgot Password?":
  - /url: /auth/forgot-password
- button "Log In →" [disabled]
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
  35  |   readonly navPayouts: Locator;
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
  61  |     // The chart is rendered with an SVG-based library, not <canvas> — broadened
  62  |     // to accept either so this doesn't depend on the specific charting library.
  63  |     this.revenueChartCanvas  = page.locator('canvas, svg').first();
  64  |     this.chartLegend         = page.locator('[class*="legend"], [class*="chart-legend"]').first();
  65  | 
  66  |     // Gateway Payments — label changed to "Transactions (today)" in new UI
  67  |     this.gatewayPaymentsHeading    = page.getByText('Gateway Payments');
  68  |     this.transactionsTodayMetric   = page.getByText(/Transactions \(today\)/i);
  69  |     this.successRateMetric         = page.getByText(/Success Rate/i).first();
  70  |     this.transactionsByMethodChart = page.getByText('Transactions by Payment Method');
  71  |     this.successVsFailureChart     = page.getByText('Success vs Failure by Method');
  72  | 
  73  |     // Sidebar Navigation
  74  |     this.navDashboard       = page.getByRole('link', { name: /^Dashboard$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Dashboard$/i));
  75  |     this.navTransactions    = page.getByRole('link', { name: /^Transactions$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Transactions$/i));
  76  |     this.navSettlements     = page.getByRole('link', { name: /^Settlements$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Settlements$/i));
  77  |     // "Payouts" is a sub-item nested under Settlements, only visible once Settlements is active/expanded.
  78  |     this.navPayouts         = page.getByRole('link', { name: /^Payouts$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Payouts$/i));
  79  |     this.navReports         = page.getByRole('link', { name: /^Reports$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Reports$/i));
  80  |     this.navUserManagement  = page.getByRole('link', { name: /User Management/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/User Management/i));
  81  |     this.navSettings        = page.getByRole('link', { name: /^Settings$/i }).or(page.locator('nav, [class*="sidebar"]').getByText(/^Settings$/i));
  82  |     this.navLogout          = page.getByRole('button', { name: /logout/i }).or(page.getByText(/logout/i));
  83  |   }
  84  | 
  85  |   async goto() {
  86  |     await this.page.goto('/dashboard');
  87  |     await this.page.waitForLoadState('networkidle');
  88  |   }
  89  | 
  90  |   async assertPageLoaded() {
> 91  |     await expect(this.page).toHaveURL(/\/dashboard/, { timeout: 15000 });
      |                             ^ Error: expect(page).toHaveURL(expected) failed
  92  |     await expect(this.greeting).toBeVisible({ timeout: 10000 });
  93  |   }
  94  | 
  95  |   async getKpiValue(cardLocator: Locator): Promise<string> {
  96  |     const text = await cardLocator.innerText();
  97  |     return text.trim();
  98  |   }
  99  | 
  100 |   async selectDateFilter(option: string) {
  101 |     // This is a native <select>, not a custom listbox — selectOption()
  102 |     // talks to it directly. The previous click-to-open-then-click-option
  103 |     // approach was waiting on the <option> becoming visible, which native
  104 |     // <select> options never do outside the browser's own OS-level popup,
  105 |     // so every call timed out.
  106 |     await this.dateFilterDropdown.selectOption({ label: option });
  107 |     await this.page.waitForLoadState('networkidle');
  108 |   }
  109 | }
  110 | 
```