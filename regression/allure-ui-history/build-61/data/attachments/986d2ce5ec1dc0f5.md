# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-nav-tc6-nav-reports-link.spec.ts >> Dashboard — Sidebar Navigation >> TC6 — Clicking "Reports" navigates to the Reports page
- Location: tests/UI/Dashboard/dash-nav-tc6-nav-reports-link.spec.ts:29:7

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.click: Test timeout of 120000ms exceeded.
Call log:
  - waiting for getByRole('link', { name: /^Reports$/i }).or(locator('nav, [class*="sidebar"]').getByText(/^Reports$/i)).first()

```

# Page snapshot

```yaml
- generic [ref=e2]:
  - generic [ref=e3]:
    - complementary [ref=e4]:
      - img "eCitizen" [ref=e8] [cursor=pointer]
      - navigation [ref=e9]:
        - link "Dashboard" [ref=e12] [cursor=pointer]:
          - /url: /dashboard
          - img [ref=e13]
          - generic [ref=e15]: Dashboard
        - link "Transactions" [ref=e18] [cursor=pointer]:
          - /url: /transaction
          - img [ref=e19]
          - generic [ref=e21]: Transactions
        - generic [ref=e23]:
          - link "Settlements" [ref=e24] [cursor=pointer]:
            - /url: /settlements
            - img [ref=e25]
            - generic [ref=e27]: Settlements
          - button "Expand submenu" [ref=e28]:
            - img [ref=e29]
        - link "Settings" [ref=e33] [cursor=pointer]:
          - /url: /settings
          - img [ref=e34]
          - generic [ref=e36]: Settings
      - button "Logout" [ref=e38] [cursor=pointer]:
        - img [ref=e39]
        - generic [ref=e41]: Logout
      - button "Collapse sidebar" [ref=e42]:
        - img [ref=e43]
    - generic [ref=e45]:
      - banner [ref=e46]:
        - generic [ref=e47]:
          - generic [ref=e50]: A
          - generic [ref=e52]:
            - generic [ref=e53]: Ashil
            - generic [ref=e54]:
              - generic [ref=e55]: ADMIN
              - generic [ref=e56]: PLATFORM
      - main [ref=e58]:
        - generic [ref=e59]:
          - generic [ref=e60]:
            - generic [ref=e61]:
              - generic [ref=e63]:
                - heading "Hi, Ashil" [level=2] [ref=e64]
                - paragraph [ref=e65]: Here's what's happening with eCitizen today.
              - combobox [ref=e67]:
                - option "Today" [selected]
                - option "Last 7 Days"
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
            - generic [ref=e69]:
              - generic [ref=e70]:
                - generic [ref=e71]:
                  - generic [ref=e72]:
                    - img [ref=e74]
                    - generic [ref=e76]:
                      - paragraph [ref=e77]: Revenue generated
                      - paragraph [ref=e78]: Revenue across all ministries
                  - button "View Details →" [ref=e79] [cursor=pointer]
                - generic [ref=e81]:
                  - paragraph [ref=e82]: Total Revenue (KES) & (USD)
                  - generic [ref=e83]:
                    - generic [ref=e84]:
                      - generic [ref=e85]:
                        - img [ref=e86]
                        - generic [ref=e94]: KES 0
                      - generic: KES 0.00
                    - generic [ref=e95]: 0.0 —
                  - generic [ref=e96]:
                    - generic [ref=e97]:
                      - generic [ref=e98]:
                        - img [ref=e99]
                        - generic [ref=e109]: USD 0
                      - generic: USD 0.00
                    - generic [ref=e110]: 0.0 —
              - generic [ref=e111]:
                - generic [ref=e112]:
                  - generic [ref=e113]:
                    - img [ref=e115]
                    - generic [ref=e118]:
                      - paragraph [ref=e119]: No of Transactions
                      - paragraph [ref=e120]: 0 transactions
                  - button "View Transactions →" [ref=e121] [cursor=pointer]
                - generic [ref=e123]:
                  - paragraph [ref=e124]: Total Transactions (KES) & (USD)
                  - generic [ref=e125]:
                    - generic [ref=e126]:
                      - img [ref=e127]
                      - generic [ref=e135]: "0"
                    - generic [ref=e136]: 0.0 —
                  - generic [ref=e137]:
                    - generic [ref=e138]:
                      - img [ref=e139]
                      - generic [ref=e149]: "0"
                    - generic [ref=e150]: 0.0 —
              - generic [ref=e151]:
                - generic [ref=e152]:
                  - generic [ref=e153]:
                    - img [ref=e155]
                    - generic [ref=e159]:
                      - paragraph [ref=e160]: Total Transactions
                      - paragraph [ref=e161]: 0 Transactions
                  - button "View Details →" [ref=e162] [cursor=pointer]
                - generic [ref=e164]:
                  - generic [ref=e165]:
                    - generic [ref=e166]:
                      - generic [ref=e167]: Successful Transactions
                      - generic [ref=e168]: 0.0 —
                    - paragraph [ref=e169]: 0%
                    - paragraph [ref=e170]: "0"
                  - generic [ref=e172]:
                    - generic [ref=e173]:
                      - generic [ref=e174]: Failed Transactions
                      - generic [ref=e175]: 0.0 —
                    - paragraph [ref=e176]: 0%
                    - paragraph [ref=e177]: "0"
          - generic [ref=e178]:
            - generic [ref=e179]:
              - generic [ref=e180]:
                - generic [ref=e181]:
                  - paragraph [ref=e182]: Monthly Revenue for All Services
                  - generic [ref=e183]:
                    - img [ref=e184]
                    - generic [ref=e187]: Showing last 12 months · auto-updates daily
                - generic [ref=e188]:
                  - button "KES" [ref=e189]
                  - button "USD" [ref=e190]
              - generic [ref=e191]:
                - generic [ref=e192]:
                  - generic [ref=e193]:
                    - generic [ref=e195]:
                      - generic [ref=e196]: KES 513.4M
                      - generic: KES 4,810,992.50
                    - generic [ref=e199]: Gross Amount
                  - generic [ref=e200]:
                    - generic [ref=e202]:
                      - generic [ref=e203]: KES 508.5M
                      - generic: KES 508,547,995.00
                    - generic [ref=e206]: Net Amount
                  - generic [ref=e207]:
                    - generic [ref=e209]:
                      - generic [ref=e210]: KES 4.8M
                      - generic: KES 4,810,992.50
                    - generic [ref=e213]: Commission
                  - generic [ref=e214]:
                    - paragraph [ref=e215]: 100%
                    - generic [ref=e218]: Transaction Success Ratio
                - generic [ref=e219]:
                  - generic [ref=e220]:
                    - generic [ref=e223]: Gross KES
                    - generic [ref=e226]: Net KES
                    - generic [ref=e229]: Commission KES
                  - img [ref=e233]:
                    - generic [ref=e250]:
                      - generic [ref=e251]: Aug
                      - generic [ref=e252]: Sep
                      - generic [ref=e253]: Oct
                      - generic [ref=e254]: Nov
                      - generic [ref=e255]: Dec
                      - generic [ref=e256]: Jan
                      - generic [ref=e257]: Feb
                      - generic [ref=e258]: Mar
                      - generic [ref=e259]: Apr
                      - generic [ref=e260]: May
                      - generic [ref=e261]: Jun
                      - generic [ref=e262]: Jul
                    - generic [ref=e263]:
                      - generic [ref=e264]: "0"
                      - generic "200.0M" [ref=e265]: 200.…
                      - generic "400.0M" [ref=e266]: 400.…
                      - generic "600.0M" [ref=e267]: 600.…
            - generic [ref=e269]:
              - generic [ref=e271]:
                - heading "Gateway Payments" [level=2] [ref=e272]
                - combobox [ref=e274]:
                  - option "Today" [selected]
                  - option "Last 7 Days"
                  - option "Last 30 Days"
                  - option "Last 90 Days"
                  - option "Custom Range"
              - generic [ref=e275]:
                - generic [ref=e276]:
                  - generic [ref=e277]:
                    - paragraph [ref=e278]: "0"
                    - paragraph [ref=e279]: Transactions (today)
                  - generic [ref=e280]:
                    - paragraph [ref=e281]: 0%
                    - paragraph [ref=e282]: Success Rate
                  - generic [ref=e283]:
                    - paragraph [ref=e284]: 0%
                    - paragraph [ref=e285]: Availability
                  - generic [ref=e286]:
                    - paragraph [ref=e287]: "0"
                    - paragraph [ref=e288]: P95 Latency
                  - generic [ref=e289]:
                    - paragraph [ref=e290]: 0%
                    - paragraph [ref=e291]: Timeout Rate
                - generic [ref=e292]:
                  - generic [ref=e294]:
                    - generic [ref=e295]:
                      - paragraph [ref=e297]: Transactions by Payment Method
                      - generic [ref=e300]:
                        - generic [ref=e301]: No Transaction Data Available
                        - generic [ref=e302]: There is no data to display for this period.
                    - generic [ref=e303]:
                      - generic [ref=e304]:
                        - paragraph [ref=e305]: Success vs Failure by Method
                        - generic [ref=e307]:
                          - generic [ref=e308]: Success
                          - generic [ref=e310]: Failure
                      - generic [ref=e313]:
                        - generic [ref=e314]: No Success / Failure Data Available
                        - generic [ref=e315]: There is no data to display for this period.
                  - paragraph [ref=e318]: Share of Payment Methods
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file nav-reports-link.spec.ts
  3  |  * @testId DASH-NAV-TC6
  4  |  * @feature Dashboard — Sidebar Navigation
  5  |  * @priority P2 / Normal
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that clicking the "Reports" item in the sidebar navigates the
  10 |  * user to the Reports page (/report). Reports are used by finance and
  11 |  * management teams to export transaction data. A broken link prevents
  12 |  * scheduled report generation workflows from starting.
  13 |  *
  14 |  * PRE-CONDITIONS
  15 |  * --------------
  16 |  *  - Authenticated session (OTP login)
  17 |  *  - "Reports" nav item visible (TC1 must pass)
  18 |  */
  19 | 
  20 | import { test, expect } from '../fixtures';
  21 | import type { TestInfo } from '@playwright/test';
  22 | import { allure } from 'allure-playwright';
  23 | import { DashboardPage } from '../../pages/DashboardPage';
  24 | import { attachScreenshot } from '../../utils/screenshotHelper';
  25 | 
  26 | test.describe('Dashboard — Sidebar Navigation', () => {
  27 |   test.setTimeout(120000);
  28 | 
  29 |   test('TC6 — Clicking "Reports" navigates to the Reports page', async ({ page, logger }, testInfo: TestInfo) => {
  30 |     // ── Allure metadata ──────────────────────────────────────────────────────
  31 |     await allure.description('Verifies the Reports sidebar link navigates to the Reports page.');
  32 |     await allure.label('feature',  'Dashboard');
  33 |     await allure.label('story',    'Sidebar Navigation');
  34 |     await allure.label('severity', 'normal');
  35 |     await allure.label('priority', 'P2');
  36 |     await allure.label('testId',   'DASH-NAV-TC6');
  37 | 
  38 |     await logger.step('Step 1 — Verifies the Reports sidebar link navigates to the Reports page', async () => {
  39 |       logger.info('Test scope: Verifies the Reports sidebar link navigates to the Reports page');
  40 |     });
  41 | 
  42 |     // ── Setup ────────────────────────────────────────────────────────────────
  43 |     const dashboard = new DashboardPage(page);
  44 | 
  45 |     await logger.step('Step 2 — Navigate to Dashboard page', async () => {
  46 |       logger.info('Opening the Dashboard page');
  47 |       await dashboard.goto();
  48 |       logger.pass('Navigation initiated successfully');
  49 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  50 |     });
  51 | 
  52 |     await logger.step('Step 3 — Verify page loads successfully', async () => {
  53 |       logger.info('Waiting for all page elements to render');
  54 |       await dashboard.assertPageLoaded();
  55 |       logger.pass('Page is fully loaded with all required elements visible');
  56 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  57 |     });
  58 | 
  59 |     // ── Step 3: Click the nav item ───────────────────────────────────────────
  60 |     await logger.step('Step 4 — Click Reports nav item', async () => {
  61 |       logger.info('Clicking: Reports nav item');
> 62 |       await dashboard.navReports.first().click();
     |                                          ^ Error: locator.click: Test timeout of 120000ms exceeded.
  63 |       logger.pass('Click performed successfully');
  64 |       await attachScreenshot(page, testInfo, '03 — Reports nav item clicked');
  65 |     });
  66 | 
  67 |     // ── Step 4: Verify the URL ───────────────────────────────────────────────
  68 |     await logger.step('Step 5 — URL contains /report', async () => {
  69 |       logger.info('Verifying URL matches expected pattern');
  70 |       await expect(page).toHaveURL(/\/report/, { timeout: 10000 });
  71 |       logger.pass('URL assertion passed: ' + page.url());
  72 |       allure.parameter('URL', page.url());
  73 |       await attachScreenshot(page, testInfo, '04 — Navigated to Reports page');
  74 |     });
  75 |   });
  76 | });
  77 | 
```