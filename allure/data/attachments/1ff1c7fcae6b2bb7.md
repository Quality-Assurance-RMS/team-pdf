# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/nav-refunds-link.spec.ts >> Dashboard — Sidebar Navigation >> TC4 — Clicking "Refunds" navigates to the Refunds page
- Location: tests/UI/Dashboard/nav-refunds-link.spec.ts:29:7

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.click: Test timeout of 120000ms exceeded.
Call log:
  - waiting for getByRole('link', { name: /^Refunds$/i }).or(locator('nav, [class*="sidebar"]').getByText(/^Refunds$/i)).first()

```

# Page snapshot

```yaml
- generic [ref=e2]:
  - generic [ref=e3]:
    - complementary [ref=e4]:
      - img "eCitizen" [ref=e8] [cursor=pointer]
      - navigation [ref=e9]:
        - link "Dashboard" [ref=e10] [cursor=pointer]:
          - /url: /dashboard
          - img [ref=e11]
          - generic [ref=e13]: Dashboard
        - link "Transactions" [ref=e14] [cursor=pointer]:
          - /url: /transaction
          - img [ref=e15]
          - generic [ref=e17]: Transactions
        - link "Settlements" [ref=e18] [cursor=pointer]:
          - /url: /settlements
          - img [ref=e19]
          - generic [ref=e21]: Settlements
        - link "Reports" [ref=e22] [cursor=pointer]:
          - /url: /reports
          - img [ref=e23]
          - generic [ref=e25]: Reports
        - link "User Management" [ref=e26] [cursor=pointer]:
          - /url: /user
          - img [ref=e27]
          - generic [ref=e29]: User Management
        - link "Settings" [ref=e30] [cursor=pointer]:
          - /url: /settings
          - img [ref=e31]
          - generic [ref=e33]: Settings
      - button "Logout" [ref=e35] [cursor=pointer]:
        - img [ref=e36]
        - generic [ref=e38]: Logout
      - button "Collapse sidebar" [ref=e39]:
        - img [ref=e40]
    - generic [ref=e42]:
      - banner [ref=e43]:
        - generic [ref=e44]:
          - button "Notifications" [ref=e45]:
            - img [ref=e46]
            - generic [ref=e49]: "3"
          - generic [ref=e52]: A
          - generic [ref=e54]:
            - generic [ref=e55]: Ashil
            - generic [ref=e56]:
              - generic [ref=e57]: SUPER_ADMIN
              - generic [ref=e58]: PLATFORM
      - main [ref=e60]:
        - generic [ref=e61]:
          - generic [ref=e62]:
            - generic [ref=e63]:
              - generic [ref=e65]:
                - heading "Hi, Ashil" [level=2] [ref=e66]
                - paragraph [ref=e67]: Here's what's happening with eCitizen today.
              - combobox [ref=e69]:
                - option "Today" [selected]
                - option "Last 7 Days"
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
            - generic [ref=e71]:
              - generic [ref=e72]:
                - generic [ref=e73]:
                  - generic [ref=e74]:
                    - img [ref=e76]
                    - generic [ref=e78]:
                      - paragraph [ref=e79]: Revenue generated
                      - paragraph [ref=e80]: Revenue across all ministries
                  - button "View Details →" [ref=e81] [cursor=pointer]
                - generic [ref=e83]:
                  - paragraph [ref=e84]: Total Revenue (KES) & (USD)
                  - generic [ref=e85]:
                    - generic [ref=e86]:
                      - img [ref=e87]
                      - generic [ref=e95]: KES 0
                    - generic [ref=e96]:
                      - generic [ref=e97]: 0%
                      - img [ref=e98]
                  - generic [ref=e100]:
                    - generic [ref=e101]:
                      - img [ref=e102]
                      - generic [ref=e112]: USD 0
                    - generic [ref=e113]:
                      - generic [ref=e114]: 0%
                      - img [ref=e115]
              - generic [ref=e117]:
                - generic [ref=e118]:
                  - generic [ref=e119]:
                    - img [ref=e121]
                    - generic [ref=e124]:
                      - paragraph [ref=e125]: No of Transactions
                      - paragraph [ref=e126]: 0 transactions
                  - button "View Transactions →" [ref=e127] [cursor=pointer]
                - generic [ref=e129]:
                  - paragraph [ref=e130]: Total Transactions (KES) & (USD)
                  - generic [ref=e131]:
                    - generic [ref=e132]:
                      - img [ref=e133]
                      - generic [ref=e141]: "0"
                    - generic [ref=e142]:
                      - generic [ref=e143]: 0%
                      - img [ref=e144]
                  - generic [ref=e146]:
                    - generic [ref=e147]:
                      - img [ref=e148]
                      - generic [ref=e158]: "0"
                    - generic [ref=e159]:
                      - generic [ref=e160]: 0%
                      - img [ref=e161]
              - generic [ref=e163]:
                - generic [ref=e164]:
                  - generic [ref=e165]:
                    - img [ref=e167]
                    - generic [ref=e171]:
                      - paragraph [ref=e172]: Payments
                      - paragraph [ref=e173]: 0 payments
                  - button "View Details →" [ref=e174] [cursor=pointer]
                - generic [ref=e176]:
                  - generic [ref=e177]:
                    - generic [ref=e178]:
                      - generic [ref=e179]: Successful Payments
                      - generic [ref=e180]:
                        - generic [ref=e181]: 0%
                        - img [ref=e182]
                    - paragraph [ref=e184]: 0%
                    - paragraph [ref=e185]: "0"
                  - generic [ref=e187]:
                    - generic [ref=e188]:
                      - generic [ref=e189]: Failed Payments
                      - generic [ref=e190]:
                        - generic [ref=e191]: 0%
                        - img [ref=e192]
                    - paragraph [ref=e194]: 0%
                    - paragraph [ref=e195]: "0"
          - generic [ref=e196]:
            - generic [ref=e197]:
              - generic [ref=e198]:
                - generic [ref=e199]:
                  - paragraph [ref=e200]: Monthly Revenue for All Services
                  - generic [ref=e201]:
                    - img [ref=e202]
                    - generic [ref=e205]: Showing last 12 months · auto-updates daily
                - generic [ref=e206]:
                  - button "KES" [ref=e207]
                  - button "USD" [ref=e208]
              - generic [ref=e209]:
                - generic [ref=e210]:
                  - generic [ref=e211]:
                    - paragraph [ref=e212]: KES 81.2M
                    - generic [ref=e215]: Gross Amount
                  - generic [ref=e216]:
                    - paragraph [ref=e217]: KES 80.7M
                    - generic [ref=e220]: Net Amount
                  - generic [ref=e221]:
                    - paragraph [ref=e222]: KES 532.1K
                    - generic [ref=e225]: Commission
                  - generic [ref=e226]:
                    - paragraph [ref=e227]: 99.99%
                    - generic [ref=e230]: Transaction Success Ratio
                - generic [ref=e231]:
                  - generic [ref=e232]:
                    - generic [ref=e235]: Gross KES
                    - generic [ref=e238]: Net KES
                    - generic [ref=e241]: Commission KES
                  - img [ref=e245]:
                    - generic [ref=e262]:
                      - generic [ref=e263]: Jul '25
                      - generic [ref=e264]: Aug '25
                      - generic [ref=e265]: Sep '25
                      - generic [ref=e266]: Oct '25
                      - generic [ref=e267]: Nov '25
                      - generic [ref=e268]: Dec '25
                      - generic [ref=e269]: Jan '26
                      - generic [ref=e270]: Feb '26
                      - generic [ref=e271]: Mar '26
                      - generic [ref=e272]: Apr '26
                      - generic [ref=e273]: May '26
                      - generic [ref=e274]: Jun '26
                    - generic [ref=e275]:
                      - generic [ref=e276]: "0"
                      - generic [ref=e277]: 25.0M
                      - generic [ref=e278]: 50.0M
                      - generic [ref=e279]: 75.0M
                      - generic "100.0M" [ref=e280]: 100.…
            - generic [ref=e282]:
              - generic [ref=e284]:
                - heading "Gateway Payments" [level=2] [ref=e285]
                - combobox [ref=e287]:
                  - option "Today" [selected]
                  - option "Last 7 Days"
                  - option "Last 30 Days"
                  - option "Last 90 Days"
                  - option "Custom Range"
              - generic [ref=e288]:
                - generic [ref=e289]:
                  - generic [ref=e290]:
                    - paragraph [ref=e291]: "0"
                    - paragraph [ref=e292]: Transactions (today)
                  - generic [ref=e293]:
                    - paragraph [ref=e294]: 0%
                    - paragraph [ref=e295]: Success Rate
                  - generic [ref=e296]:
                    - paragraph [ref=e297]: 0%
                    - paragraph [ref=e298]: Availability
                  - generic [ref=e299]:
                    - paragraph [ref=e300]: "0"
                    - paragraph [ref=e301]: P95 Latency
                  - generic [ref=e302]:
                    - paragraph [ref=e303]: 0%
                    - paragraph [ref=e304]: Timeout Rate
                - generic [ref=e305]:
                  - generic [ref=e307]:
                    - generic [ref=e308]:
                      - paragraph [ref=e310]: Transactions by Payment Method
                      - generic [ref=e313]:
                        - generic [ref=e314]: No Transaction Data Available
                        - generic [ref=e315]: There is no data to display for this period.
                    - generic [ref=e316]:
                      - generic [ref=e317]:
                        - paragraph [ref=e318]: Success vs Failure by Method
                        - generic [ref=e320]:
                          - generic [ref=e321]: Success
                          - generic [ref=e323]: Failure
                      - generic [ref=e326]:
                        - generic [ref=e327]: No Success / Failure Data Available
                        - generic [ref=e328]: There is no data to display for this period.
                  - paragraph [ref=e331]: Share of Payment Methods
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file nav-refunds-link.spec.ts
  3  |  * @testId DASH-NAV-TC4
  4  |  * @feature Dashboard — Sidebar Navigation
  5  |  * @priority P1 / Critical
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that clicking the "Refunds" item in the sidebar navigates the
  10 |  * user to the Refunds page (/refund). Finance teams use the Refunds section
  11 |  * to review and approve customer refund requests — a broken nav link delays
  12 |  * refund processing.
  13 |  *
  14 |  * PRE-CONDITIONS
  15 |  * --------------
  16 |  *  - Authenticated session (OTP login)
  17 |  *  - "Refunds" nav item visible (TC1 must pass)
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
  29 |   test('TC4 — Clicking "Refunds" navigates to the Refunds page', async ({ page, logger }, testInfo: TestInfo) => {
  30 |     // ── Allure metadata ──────────────────────────────────────────────────────
  31 |     await allure.description('Verifies the Refunds sidebar link navigates to the Refunds page.');
  32 |     await allure.label('feature',  'Dashboard');
  33 |     await allure.label('story',    'Sidebar Navigation');
  34 |     await allure.label('severity', 'critical');
  35 |     await allure.label('priority', 'P1');
  36 |     await allure.label('testId',   'DASH-NAV-TC4');
  37 | 
  38 |     // ── Setup ────────────────────────────────────────────────────────────────
  39 |     const dashboard = new DashboardPage(page);
  40 | 
  41 |     await logger.step('Step 1 — Navigate to Dashboard page', async () => {
  42 |       logger.info('Opening the Dashboard page');
  43 |       await dashboard.goto();
  44 |       logger.pass('Navigation initiated successfully');
  45 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  46 |     });
  47 | 
  48 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  49 |       logger.info('Waiting for all page elements to render');
  50 |       await dashboard.assertPageLoaded();
  51 |       logger.pass('Page is fully loaded with all required elements visible');
  52 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  53 |     });
  54 | 
  55 |     // ── Step 3: Click the nav item ───────────────────────────────────────────
  56 |     await logger.step('Step 3 — Click Refunds nav item', async () => {
  57 |       logger.info('Clicking: Refunds nav item');
> 58 |       await dashboard.navRefunds.first().click();
     |                                          ^ Error: locator.click: Test timeout of 120000ms exceeded.
  59 |       logger.pass('Click performed successfully');
  60 |       await attachScreenshot(page, testInfo, '03 — Refunds nav item clicked');
  61 |     });
  62 | 
  63 |     // ── Step 4: Verify the URL ───────────────────────────────────────────────
  64 |     await logger.step('Step 4 — URL contains /refund', async () => {
  65 |       logger.info('Verifying URL matches expected pattern');
  66 |       await expect(page).toHaveURL(/\/refund/, { timeout: 10000 });
  67 |       logger.pass('URL assertion passed: ' + page.url());
  68 |       allure.parameter('URL', page.url());
  69 |       await attachScreenshot(page, testInfo, '04 — Navigated to Refunds page');
  70 |     });
  71 |   });
  72 | });
  73 | 
```