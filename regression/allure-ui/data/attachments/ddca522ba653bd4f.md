# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-rc-tc3-revenue-chart-date-filter-default.spec.ts >> Dashboard — Monthly Revenue Chart >> TC3 — Date filter dropdown defaults to "Last 90 Days"
- Location: tests/UI/Dashboard/dash-rc-tc3-revenue-chart-date-filter-default.spec.ts:29:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator:  getByText(/Last 90 Days/i).first()
Expected: visible
Received: hidden
Timeout:  10000ms

Call log:
  - Expect "toBeVisible" with timeout 10000ms
  - waiting for getByText(/Last 90 Days/i).first()
    14 × locator resolved to <option value="last-90-days" class="relative flex select-none items-center ↵                rounded-sm outline-none focus:bg-accent focus:text-accent-foreground ↵                data-disabled:pointer-events-none ↵                data-disabled:opacity-50 px-4 py-3 text-lg cursor-pointer ↵                transition-colors border-b border-[#D6DBD7] last:border-0 ↵                text-[#272E28] hover:bg-[#F8F9FA]">Last 90 Days</option>
       - unexpected value "hidden"

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
    - link "Administrator":
      - /url: /administrator
      - img
      - text: Administrator
    - link "Settlements":
      - /url: /settlements
      - img
      - text: Settlements
    - button "Expand submenu":
      - img
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
- banner: A Ashil ADMIN PLATFORM
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
  - text: KES 0 KES 0.00 0.0 —
  - img
  - text: USD 0 USD 0.00 0.0 —
  - img
  - paragraph: No of Transactions
  - paragraph: 0 transactions
  - button "View Transactions →"
  - paragraph: Total Transactions (KES) & (USD)
  - img
  - text: 0 0.0 —
  - img
  - text: 0 0.0 —
  - img
  - paragraph: Total Transactions
  - paragraph: 0 Transactions
  - button "View Details →"
  - text: Successful Transactions 0.0 —
  - paragraph: 0%
  - paragraph: "0"
  - text: Failed Transactions 0.0 —
  - paragraph: 0%
  - paragraph: "0"
  - paragraph: Monthly Revenue for All Services
  - img
  - text: Showing last 12 months · auto-updates daily
  - button "KES"
  - button "USD"
  - text: KES 644.2M KES 6,106,192.50 Gross Amount KES 638.1M KES 638,121,785.00 Net Amount KES 6.1M KES 6,106,192.50 Commission
  - paragraph: 99.96%
  - text: Transaction Success Ratio Gross KES Net KES Commission KES
  - img: Aug Sep Oct Nov Dec Jan Feb Mar Apr May Jun Jul 0 200.… 400.… 600.…
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
  2  |  * @file revenue-chart-date-filter-default.spec.ts
  3  |  * @testId DASH-RC-TC3
  4  |  * @feature Dashboard — Monthly Revenue Chart
  5  |  * @priority P1 / Critical
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that the date range filter for the Monthly Revenue chart defaults
  10 |  * to "Today" when the dashboard is first opened. The default period
  11 |  * determines what data is fetched on load; an incorrect default (e.g. "All
  12 |  * Time") could cause the chart to render stale or misleading totals.
  13 |  *
  14 |  * PRE-CONDITIONS
  15 |  * --------------
  16 |  *  - Authenticated session (OTP login)
  17 |  *  - No previously persisted filter preference in the user's session/cookie
  18 |  */
  19 | 
  20 | import { test, expect } from '../fixtures';
  21 | import type { TestInfo } from '@playwright/test';
  22 | import { allure } from 'allure-playwright';
  23 | import { DashboardPage } from '../../pages/DashboardPage';
  24 | import { attachScreenshot } from '../../utils/screenshotHelper';
  25 | 
  26 | test.describe('Dashboard — Monthly Revenue Chart', () => {
  27 |   test.setTimeout(120000);
  28 | 
  29 |   test('TC3 — Date filter dropdown defaults to "Last 90 Days"', async ({ page, logger }, testInfo: TestInfo) => {
  30 |     // ── Allure metadata ──────────────────────────────────────────────────────
  31 |     await allure.description('Verifies the chart date filter shows "Today" as the default selected value.');
  32 |     await allure.label('feature',  'Dashboard');
  33 |     await allure.label('story',    'Revenue Chart');
  34 |     await allure.label('severity', 'critical');
  35 |     await allure.label('priority', 'P1');
  36 |     await allure.label('testId',   'DASH-RC-TC3');
  37 | 
  38 |     await logger.step('Step 1 — Verifies the chart date filter shows "Today" as the default selected value', async () => {
  39 |       logger.info('Test scope: Verifies the chart date filter shows "Today" as the default selected value');
  40 |     });
  41 | 
  42 |     // ── Setup ────────────────────────────────────────────────────────────────
  43 |     const dashboard = new DashboardPage(page);
  44 | 
  45 |     await logger.step('Step 2 — Navigate to Dashboard page', async () => {
  46 |       logger.info('Opening the Dashboard page');
  47 |       await dashboard.goto();
  48 |       logger.pass('Navigation to Dashboard initiated successfully');
  49 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  50 |     });
  51 | 
  52 |     await logger.step('Step 3 — Verify page loads successfully', async () => {
  53 |       logger.info('Waiting for all Dashboard elements to render');
  54 |       await dashboard.assertPageLoaded();
  55 |       logger.pass('Dashboard page is fully loaded with all required elements');
  56 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  57 |     });
  58 | 
  59 |     // ── Assertion ────────────────────────────────────────────────────────────
  60 |     // NOTE: the default period changed from "Today" to "Last 90 Days" (confirmed
  61 |     // live, 2026-07). Also, the dashboard renders TWO date-filter dropdowns (this
  62 |     // chart's, and the Gateway Payments section's), each with a hidden/unselected
  63 |     // "Today" <option> in its list — a bare getByText('Today') matches both and
  64 |     // throws a strict-mode violation regardless of which one is actually selected.
  65 |     // .first() resolves the ambiguity; if precise per-widget verification is
  66 |     // needed later, ask the dev team for a stable data-testid on this control.
  67 |     await logger.step('Step 4 — "Last 90 Days" label is visible in the date filter', async () => {
  68 |       logger.info('Asserting value/data: date filter default selection');
> 69 |       await expect(page.getByText(/Last 90 Days/i).first()).toBeVisible({ timeout: 10000 });
     |                                                             ^ Error: expect(locator).toBeVisible() failed
  70 | 
  71 |       // Record confirmed default value in the Allure report
  72 |       allure.parameter('Default filter value', 'Last 90 Days');
  73 |       logger.pass('Value assertion passed');
  74 |       await attachScreenshot(page, testInfo, '03 — Date filter defaults to Last 90 Days', page.getByText(/Last 90 Days/i).first());
  75 |     });
  76 |   });
  77 | });
  78 | 
```