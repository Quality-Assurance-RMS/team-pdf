# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/nav-all-items-visible.spec.ts >> Dashboard — Sidebar Navigation >> TC1 — All sidebar navigation items are visible
- Location: tests/UI/Dashboard/nav-all-items-visible.spec.ts:40:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByRole('link', { name: /User Management/i }).or(locator('nav, [class*="sidebar"]').getByText(/User Management/i)).first()
Expected: visible
Timeout: 8000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 8000ms
  - waiting for getByRole('link', { name: /User Management/i }).or(locator('nav, [class*="sidebar"]').getByText(/User Management/i)).first()

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
  - text: KES 0 0.0 —
  - img
  - text: USD 0 0.0 —
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
  - paragraph: KES 532.0K
  - text: Gross Amount
  - paragraph: KES 80.6M
  - text: Net Amount
  - paragraph: KES 532.0K
  - text: Commission
  - paragraph: 99.99%
  - text: Transaction Success Ratio Gross KES Net KES Commission KES
  - img: Aug Sep Oct Nov Dec Jan Feb Mar Apr May Jun Jul 0 25.0M 50.0M 75.0M 100.…
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
  2  |  * @file nav-all-items-visible.spec.ts
  3  |  * @testId DASH-NAV-TC1
  4  |  * @feature Dashboard — Sidebar Navigation
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that all expected sidebar navigation items are rendered and
  10 |  * visible after a successful login. A missing nav item means the operator
  11 |  * cannot reach that section of the application from the dashboard.
  12 |  *
  13 |  * ITEMS UNDER TEST
  14 |  * ----------------
  15 |  *  1. Dashboard       — current page indicator
  16 |  *  2. Transactions    — payment transaction list
  17 |  *  3. Settlements     — settlement reports
  18 |  *  4. Reports         — analytics and exports
  19 |  *  5. User Management — user and role administration
  20 |  *  6. Settings        — platform configuration
  21 |  *
  22 |  * NOTE: "Refunds" was removed from the sidebar in the updated UI.
  23 |  * NOTE: "Logout" is excluded here as it is tested separately in TC7.
  24 |  *
  25 |  * PRE-CONDITIONS
  26 |  * --------------
  27 |  *  - Authenticated session (OTP login)
  28 |  *  - Logged-in user has access to all menu items (Platform Operations role)
  29 |  */
  30 | 
  31 | import { test, expect } from '../fixtures';
  32 | import type { TestInfo } from '@playwright/test';
  33 | import { allure } from 'allure-playwright';
  34 | import { DashboardPage } from '../../pages/DashboardPage';
  35 | import { attachScreenshot } from '../../utils/screenshotHelper';
  36 | 
  37 | test.describe('Dashboard — Sidebar Navigation', () => {
  38 |   test.setTimeout(120000);
  39 | 
  40 |   test('TC1 — All sidebar navigation items are visible', async ({ page, logger }, testInfo: TestInfo) => {
  41 |     // ── Allure metadata ──────────────────────────────────────────────────────
  42 |     await allure.description('Verifies that all expected sidebar menu items render after login.');
  43 |     await allure.label('feature',  'Dashboard');
  44 |     await allure.label('story',    'Sidebar Navigation');
  45 |     await allure.label('severity', 'blocker');
  46 |     await allure.label('priority', 'P0');
  47 |     await allure.label('testId',   'DASH-NAV-TC1');
  48 | 
  49 |     // ── Setup ────────────────────────────────────────────────────────────────
  50 |     const dashboard = new DashboardPage(page);
  51 | 
  52 |     await logger.step('Step 1 — Navigate to Dashboard page', async () => {
  53 |       logger.info('Opening the Dashboard page');
  54 |       await dashboard.goto();
  55 |       logger.pass('Navigation initiated successfully');
  56 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  57 |     });
  58 | 
  59 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  60 |       logger.info('Waiting for all page elements to render');
  61 |       await dashboard.assertPageLoaded();
  62 |       logger.pass('Page is fully loaded with all required elements visible');
  63 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  64 |     });
  65 | 
  66 |     // ── Test data: nav items with their DashboardPage locators ───────────────
  67 |     // NOTE: "Refunds" has been removed from the sidebar in the new UI
  68 |     const items = [
  69 |       { label: 'Dashboard',       locator: dashboard.navDashboard },
  70 |       { label: 'Transactions',    locator: dashboard.navTransactions },
  71 |       { label: 'Settlements',     locator: dashboard.navSettlements },
  72 |       { label: 'Reports',         locator: dashboard.navReports },
  73 |       { label: 'User Management', locator: dashboard.navUserManagement },
  74 |       { label: 'Settings',        locator: dashboard.navSettings },
  75 |     ];
  76 | 
  77 |     // ── Assertions: one step per nav item for granular failure identification ─
  78 |     let stepNum = 3;
  79 |     for (const { label, locator } of items) {
  80 |       const stepLabel = `Step ${stepNum}`;
  81 |       const screenshotPrefix = String(stepNum).padStart(2, '0');
  82 |       await logger.step(`${stepLabel} — "${label}" nav item is visible`, async () => {
  83 |         logger.info(`Asserting visibility: ${label} nav item`);
  84 |         // .first() guards against duplicate matches if the sidebar renders
  85 |         // both a full-text and an icon-only version of the same link
> 86 |         await expect(locator.first()).toBeVisible({ timeout: 8000 });
     |                                       ^ Error: expect(locator).toBeVisible() failed
  87 |         logger.pass(`${label} nav item is visible`);
  88 |         await attachScreenshot(page, testInfo, `${screenshotPrefix} — ${label} nav item visible`, locator.first());
  89 |       });
  90 |       stepNum++;
  91 |     }
  92 |   });
  93 | });
  94 | 
```