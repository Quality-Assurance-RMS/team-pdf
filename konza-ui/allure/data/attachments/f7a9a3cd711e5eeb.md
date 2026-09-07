# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-nav-tc7-nav-logout-redirect.spec.ts >> Dashboard — Sidebar Navigation >> TC7 — Clicking "Logout" redirects to the login page
- Location: tests/UI/Dashboard/dash-nav-tc7-nav-logout-redirect.spec.ts:37:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/auth\/login/
Received string:  "https://rms.uat.konza/dashboard"
Timeout: 10000ms

Call log:
  - Expect "toHaveURL" with timeout 10000ms
    14 × unexpected value "https://rms.uat.konza/dashboard"

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
    - link "Reconciliation":
      - /url: /reconciliation
      - img
      - text: Reconciliation
    - button "Expand Reconciliation":
      - img
    - link "Settlements":
      - /url: /settlements
      - img
      - text: Settlements
    - link "Payouts":
      - /url: /payouts
      - img
      - text: Payouts
    - link "SLA":
      - /url: /service-level-agreement/list
      - img
      - text: SLA
    - link "Reports":
      - /url: /reports
      - img
      - text: Reports
    - link "Administration":
      - /url: /psp-files
      - img
      - text: Administration
    - button "Expand Administration":
      - img
  - text: Awaiting Settlement
  - paragraph: Awaiting Settlement
  - button:
    - img
  - button:
    - img
  - text: Refresh status
  - button "Logout":
    - img
    - text: Logout
  - button "Collapse sidebar":
    - img
- banner:
  - button "Notifications"
  - text: A Ashil SUPER_ADMIN PLATFORM
- main:
  - heading "Hi, Ashil" [level=2]
  - paragraph: Here's what's happening with eCitizen Last 7 Days.
  - combobox:
    - option "Today"
    - option "Last 7 Days" [selected]
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - img
  - paragraph: Revenue generated
  - paragraph: Revenue across all MCDA'S
  - button "View Details →"
  - paragraph: Total Revenue (KES) & (USD)
  - img
  - text: KES 103.3K KES 103,308.00 -99%
  - img
  - img
  - text: USD 0 USD 0.00 0.0 —
  - img
  - paragraph: No. of Transactions
  - paragraph: 9,771 transactions
  - button "View Transactions →"
  - paragraph: Total Transactions (KES) & (USD)
  - img
  - text: 9,771 +100%
  - img
  - img
  - text: 0 0.0 —
  - img
  - paragraph: Total Transactions
  - paragraph: 9,771 Transactions
  - button "View Details →"
  - text: Successful Transactions 0.0 —
  - paragraph: 100%
  - paragraph: 9,771
  - text: Failed Transactions 0.0 —
  - paragraph: 0%
  - paragraph: "0"
  - heading "Gateway Payments" [level=2]
  - paragraph: "9771"
  - paragraph: Transactions (last 7 Days)
  - paragraph: 100%
  - paragraph: Success Rate
  - paragraph: 100%
  - paragraph: Availability
  - paragraph: 0 /sec
  - paragraph: P95 Latency
  - paragraph: 0%
  - paragraph: Timeout Rate
  - paragraph: Transactions by Payment Method
  - img: Card Mobile money
  - paragraph: Success vs Failure by Method
  - text: Success Failure
  - img: Card Mobile money
  - paragraph: Share of Payment Methods
  - paragraph: Card
  - paragraph: 0.02%
  - paragraph: Mobile money
  - paragraph: 99.98%
  - paragraph: Monthly Revenue for All Services
  - img
  - text: Showing last 12 months · auto-updates daily
  - button "KES"
  - button "USD"
  - text: KES 60.0M KES 59,984,146.31 Gross Amount KES 54.0M KES 53,964,994.07 Net Amount KES 6.0M KES 6,019,152.24 Access Fee
  - paragraph: 100%
  - text: Transaction Success Ratio Gross KES Net KES Access Fee
  - img: Oct Nov Dec Jan Feb Mar Apr May Jun Jul Aug Sep 0 10.0M 20.0M 30.0M
  - img
  - text: "Access Fee: 799,969.64"
  - img
  - text: "Gross KES: 2,123,894.37"
  - img
  - text: "Net KES: 2,102,655.39"
  - img
  - text: "Access Fee: 21,238.98"
  - img
  - text: "Gross KES: 388,099.60"
  - img
  - text: "Net KES: 330,360.12"
  - img
  - text: "Access Fee: 57,739.48"
  - img
  - text: "Gross KES: 1,500,501.22"
  - img
  - text: "Net KES: 1,260,426.74"
  - img
  - text: "Access Fee: 240,074.48"
  - img
  - text: "Gross KES: 1,998,946.98"
  - img
  - text: "Net KES: 1,679,031.22"
  - img
  - text: "Access Fee: 319,915.76"
  - img
  - text: "Gross KES: 1,998,369.88"
  - img
  - text: "Net KES: 1,678,669.66"
  - img
  - text: "Access Fee: 319,700.22"
  - img
  - text: "Access Fee: 2,398,134.05"
  - img
  - text: "Gross KES: 35,168.49"
  - img
  - text: "Net KES: 30,064.23"
  - img
  - text: "Access Fee: 5,104.26"
  - img
  - text: "Access Fee: 1,233.00"
  - img
  - text: "Access Fee: 1,855,966.73"
  - img
  - text: "Gross KES: 7,623.63"
  - img
  - text: "Net KES: 7,547.99"
  - img
  - text: "Access Fee: 75.64"
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file nav-logout-redirect.spec.ts
  3  |  * @testId DASH-NAV-TC7
  4  |  * @feature Dashboard — Sidebar Navigation
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that clicking the "Logout" button terminates the user session
  10 |  * and redirects the browser to the login page (/auth/login). This is a
  11 |  * critical security check — if logout does not redirect correctly, the
  12 |  * session may remain active and accessible in shared-workstation scenarios.
  13 |  *
  14 |  * EXPECTED BEHAVIOUR
  15 |  * ------------------
  16 |  *  1. User clicks Logout in the sidebar.
  17 |  *  2. The application clears the session token / cookie.
  18 |  *  3. The browser is redirected to /auth/login.
  19 |  *  4. The login form is displayed (the user cannot return to /dashboard
  20 |  *     without re-authenticating).
  21 |  *
  22 |  * PRE-CONDITIONS
  23 |  * --------------
  24 |  *  - Authenticated session (OTP login)
  25 |  *  - "Logout" button visible in the sidebar
  26 |  */
  27 | 
  28 | import { test, expect } from '../fixtures';
  29 | import type { TestInfo } from '@playwright/test';
  30 | import { allure } from 'allure-playwright';
  31 | import { DashboardPage } from '../../pages/DashboardPage';
  32 | import { attachScreenshot } from '../../utils/screenshotHelper';
  33 | 
  34 | test.describe('Dashboard — Sidebar Navigation', () => {
  35 |   test.setTimeout(120000);
  36 | 
  37 |   test('TC7 — Clicking "Logout" redirects to the login page', async ({ page, logger }, testInfo: TestInfo) => {
  38 |     // ── Allure metadata ──────────────────────────────────────────────────────
  39 |     await allure.description('Verifies that clicking the Logout button ends the session and redirects to /auth/login.');
  40 |     await allure.label('feature',  'Dashboard');
  41 |     await allure.label('story',    'Sidebar Navigation');
  42 |     await allure.label('severity', 'blocker');
  43 |     await allure.label('priority', 'P0');
  44 |     await allure.label('testId',   'DASH-NAV-TC7');
  45 | 
  46 |     await logger.step('Step 1 — Verifies that clicking the Logout button ends the session and redirects to /auth/login', async () => {
  47 |       logger.info('Test scope: Verifies that clicking the Logout button ends the session and redirects to /auth/login');
  48 |     });
  49 | 
  50 |     // ── Setup ────────────────────────────────────────────────────────────────
  51 |     const dashboard = new DashboardPage(page);
  52 | 
  53 |     await logger.step('Step 2 — Navigate to Dashboard page', async () => {
  54 |       logger.info('Opening the Dashboard page');
  55 |       await dashboard.goto();
  56 |       logger.pass('Navigation initiated successfully');
  57 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  58 |     });
  59 | 
  60 |     await logger.step('Step 3 — Verify page loads successfully', async () => {
  61 |       logger.info('Waiting for all page elements to render');
  62 |       await dashboard.assertPageLoaded();
  63 |       logger.pass('Page is fully loaded with all required elements visible');
  64 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  65 |     });
  66 | 
  67 |     // ── Step 3: Trigger logout ───────────────────────────────────────────────
  68 |     await logger.step('Step 4 — Click Logout', async () => {
  69 |       logger.info('Clicking the Logout button in the sidebar navigation');
  70 |       // dashboard.navLogout targets either the logout <button> or the logout
  71 |       // <a> text node depending on the current UI implementation
  72 |       await dashboard.navLogout.first().click();
  73 |       logger.pass('Logout button clicked — session termination initiated');
  74 |       await attachScreenshot(page, testInfo, '03 — Logout clicked');
  75 |     });
  76 | 
  77 |     // ── Step 4: Verify redirect to login ─────────────────────────────────────
  78 |     await logger.step('Step 5 — Redirected to /auth/login', async () => {
  79 |       logger.info('Waiting for redirect to /auth/login after logout');
  80 |       // The redirect may involve a short animation or API call to invalidate
  81 |       // the session token — a 10 s timeout accommodates both
> 82 |       await expect(page).toHaveURL(/\/auth\/login/, { timeout: 10000 });
     |                          ^ Error: expect(page).toHaveURL(expected) failed
  83 |       logger.pass('Redirect confirmed — application navigated to login page: ' + page.url());
  84 |       // Record the final URL in Allure for post-run verification
  85 |       allure.parameter('URL after logout', page.url());
  86 |       await attachScreenshot(page, testInfo, '04 — Redirected to login page');
  87 |     });
  88 |   });
  89 | });
  90 | 
```