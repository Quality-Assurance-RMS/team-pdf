# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/revenue-chart-month-labels.spec.ts >> Dashboard — Monthly Revenue Chart >> TC5 — All 12 month labels (Jan–Dec) are present in the chart
- Location: tests/UI/Dashboard/revenue-chart-month-labels.spec.ts:30:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText('Jan', { exact: true })
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByText('Jan', { exact: true })

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
    - link "User Management":
      - /url: /user
      - img
      - text: User Management
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
  - text: A Ashil SUPER_ADMIN PLATFORM
- main:
  - heading "Hi, Ashil" [level=2]
  - paragraph: Here's what's happening with eCitizen today.
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - img
  - paragraph: Revenue generated
  - paragraph: Revenue across all ministries
  - button "View Details →"
  - paragraph: Total Revenue (KES) & (USD)
  - img
  - text: KES 0 0%
  - img
  - img
  - text: USD 0 0%
  - img
  - img
  - paragraph: No of Transactions
  - paragraph: 0 transactions
  - button "View Transactions →"
  - paragraph: Total Transactions (KES) & (USD)
  - img
  - text: 0 0%
  - img
  - img
  - text: 0 0%
  - img
  - img
  - paragraph: Payments
  - paragraph: 0 payments
  - button "View Details →"
  - text: Successful Payments 0%
  - img
  - paragraph: 0%
  - paragraph: "0"
  - text: Failed Payments 0%
  - img
  - paragraph: 0%
  - paragraph: "0"
  - paragraph: Monthly Revenue for All Services
  - img
  - text: Showing last 12 months · auto-updates daily
  - button "KES"
  - button "USD"
  - paragraph: KES 81.2M
  - text: Gross Amount
  - paragraph: KES 80.7M
  - text: Net Amount
  - paragraph: KES 532.1K
  - text: Commission
  - paragraph: 99.99%
  - text: Transaction Success Ratio Gross KES Net KES Commission KES
  - img: Jul '25 Aug '25 Sep '25 Oct '25 Nov '25 Dec '25 Jan '26 Feb '26 Mar '26 Apr '26 May '26 Jun '26 0 25.0M 50.0M 75.0M 100.…
  - heading "Gateway Payments" [level=2]
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - paragraph: "0"
  - paragraph: Transactions (today)
  - paragraph: 0%
  - paragraph: Success Rate
  - paragraph: 0%
  - paragraph: Availability
  - paragraph: "0"
  - paragraph: P95 Latency
  - paragraph: 0%
  - paragraph: Timeout Rate
  - paragraph: Transactions by Payment Method
  - text: No Transaction Data Available There is no data to display for this period.
  - paragraph: Success vs Failure by Method
  - text: Success Failure No Success / Failure Data Available There is no data to display for this period.
  - paragraph: Share of Payment Methods
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file revenue-chart-month-labels.spec.ts
  3  |  * @testId DASH-RC-TC5
  4  |  * @feature Dashboard — Monthly Revenue Chart
  5  |  * @priority P2 / Normal
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that all 12 month abbreviations (Jan–Dec) are rendered as X-axis
  10 |  * labels on the Monthly Revenue chart. Missing month labels would indicate
  11 |  * that the chart's X-axis configuration is broken or that the data range
  12 |  * was not correctly set to a full calendar year.
  13 |  *
  14 |  * PRE-CONDITIONS
  15 |  * --------------
  16 |  *  - Authenticated session (OTP login)
  17 |  *  - Chart canvas rendered (TC4 must pass)
  18 |  *  - The chart is configured to display a full 12-month view
  19 |  */
  20 | 
  21 | import { test, expect } from '../fixtures';
  22 | import type { TestInfo } from '@playwright/test';
  23 | import { allure } from 'allure-playwright';
  24 | import { DashboardPage } from '../../pages/DashboardPage';
  25 | import { attachScreenshot } from '../../utils/screenshotHelper';
  26 | 
  27 | test.describe('Dashboard — Monthly Revenue Chart', () => {
  28 |   test.setTimeout(120000);
  29 | 
  30 |   test('TC5 — All 12 month labels (Jan–Dec) are present in the chart', async ({ page }, testInfo: TestInfo) => {
  31 |     // ── Allure metadata ──────────────────────────────────────────────────────
  32 |     await allure.description('Verifies that the X-axis of the revenue chart shows all 12 calendar month labels.');
  33 |     await allure.label('feature',  'Dashboard');
  34 |     await allure.label('story',    'Revenue Chart');
  35 |     await allure.label('severity', 'normal');
  36 |     await allure.label('priority', 'P2');
  37 |     await allure.label('testId',   'DASH-RC-TC5');
  38 | 
  39 |     // ── Setup ────────────────────────────────────────────────────────────────
  40 |     const dashboard = new DashboardPage(page);
  41 | 
  42 |     await test.step('Step 1 — Navigate to Dashboard page', async () => {
  43 |       await dashboard.goto();
  44 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  45 |     });
  46 | 
  47 |     await test.step('Step 2 — Verify page loads successfully', async () => {
  48 |       await dashboard.assertPageLoaded();
  49 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  50 |     });
  51 | 
  52 |     // ── Full list of expected X-axis month labels ────────────────────────────
  53 |     const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
  54 | 
  55 |     // ── Assertions: one step per month for clear failure identification ───────
  56 |     let stepNum = 3;
  57 |     for (const month of months) {
  58 |       const stepLabel = `Step ${stepNum}`;
  59 |       const screenshotPrefix = String(stepNum).padStart(2, '0');
  60 |       await test.step(`${stepLabel} — Month label "${month}" is visible`, async () => {
  61 |         // exact: true prevents "Jan" matching "January" if the UI uses full names
> 62 |         await expect(page.getByText(month, { exact: true })).toBeVisible({ timeout: 5000 });
     |                                                              ^ Error: expect(locator).toBeVisible() failed
  63 |         await attachScreenshot(page, testInfo, `${screenshotPrefix} — ${month} label visible`, page.getByText(month, { exact: true }));
  64 |       });
  65 |       stepNum++;
  66 |     }
  67 |   });
  68 | });
  69 | 
```