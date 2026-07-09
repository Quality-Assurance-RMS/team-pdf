# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-nav-tc1-nav-all-items-visible.spec.ts >> Dashboard — Sidebar Navigation >> TC1 — All sidebar navigation items are visible
- Location: tests/UI/Dashboard/dash-nav-tc1-nav-all-items-visible.spec.ts:40:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByRole('link', { name: /^Reports$/i }).or(locator('nav, [class*="sidebar"]').getByText(/^Reports$/i)).first()
Expected: visible
Timeout: 8000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 8000ms
  - waiting for getByRole('link', { name: /^Reports$/i }).or(locator('nav, [class*="sidebar"]').getByText(/^Reports$/i)).first()

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
    - button "Expand submenu":
      - img
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
  - text: KES 513.4M KES 4,810,992.50 Gross Amount KES 508.5M KES 508,547,995.00 Net Amount KES 4.8M KES 4,810,992.50 Commission
  - paragraph: 100%
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
  19 |  *  5. Settings        — platform configuration
  20 |  *
  21 |  * NOTE: "Refunds" and "User Management" were removed from the sidebar in the
  22 |  * updated UI (confirmed via live page snapshot, 2026-07).
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
  49 |     await logger.step('Step 1 — Verifies that all expected sidebar menu items render after login', async () => {
  50 |       logger.info('Test scope: Verifies that all expected sidebar menu items render after login');
  51 |     });
  52 | 
  53 |     // ── Setup ────────────────────────────────────────────────────────────────
  54 |     const dashboard = new DashboardPage(page);
  55 | 
  56 |     await logger.step('Step 2 — Navigate to Dashboard page', async () => {
  57 |       logger.info('Opening the Dashboard page');
  58 |       await dashboard.goto();
  59 |       logger.pass('Navigation initiated successfully');
  60 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  61 |     });
  62 | 
  63 |     await logger.step('Step 3 — Verify page loads successfully', async () => {
  64 |       logger.info('Waiting for all page elements to render');
  65 |       await dashboard.assertPageLoaded();
  66 |       logger.pass('Page is fully loaded with all required elements visible');
  67 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  68 |     });
  69 | 
  70 |     // ── Test data: nav items with their DashboardPage locators ───────────────
  71 |     // NOTE: "Refunds" and "User Management" have been removed from the sidebar
  72 |     // in the current UI (confirmed via live page snapshot, 2026-07).
  73 |     const items = [
  74 |       { label: 'Dashboard',       locator: dashboard.navDashboard },
  75 |       { label: 'Transactions',    locator: dashboard.navTransactions },
  76 |       { label: 'Settlements',     locator: dashboard.navSettlements },
  77 |       { label: 'Reports',         locator: dashboard.navReports },
  78 |       { label: 'Settings',        locator: dashboard.navSettings },
  79 |     ];
  80 | 
  81 |     // ── Assertions: one step per nav item for granular failure identification ─
  82 |     let stepNum = 4;
  83 |     for (const { label, locator } of items) {
  84 |       const stepLabel = `Step ${stepNum}`;
  85 |       const screenshotPrefix = String(stepNum).padStart(2, '0');
  86 |       await logger.step(`${stepLabel} — "${label}" nav item is visible`, async () => {
  87 |         logger.info(`Asserting visibility: ${label} nav item`);
  88 |         // .first() guards against duplicate matches if the sidebar renders
  89 |         // both a full-text and an icon-only version of the same link
> 90 |         await expect(locator.first()).toBeVisible({ timeout: 8000 });
     |                                       ^ Error: expect(locator).toBeVisible() failed
  91 |         logger.pass(`${label} nav item is visible`);
  92 |         await attachScreenshot(page, testInfo, `${screenshotPrefix} — ${label} nav item visible`, locator.first());
  93 |       });
  94 |       stepNum++;
  95 |     }
  96 |   });
  97 | });
  98 | 
```