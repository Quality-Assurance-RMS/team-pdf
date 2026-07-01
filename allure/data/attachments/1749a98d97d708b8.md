# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/revenue-chart-date-filter-default.spec.ts >> Dashboard — Monthly Revenue Chart >> TC3 — Date filter dropdown defaults to "Today"
- Location: tests/UI/Dashboard/revenue-chart-date-filter-default.spec.ts:29:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText('Today', { exact: true })
Expected: visible
Error: strict mode violation: getByText('Today', { exact: true }) resolved to 2 elements:
    1) <option value="today" class="relative flex select-none items-center ↵                rounded-sm outline-none focus:bg-accent focus:text-accent-foreground ↵                data-disabled:pointer-events-none ↵                data-disabled:opacity-50 px-4 py-3 text-lg cursor-pointer ↵                transition-colors border-b border-[#D6DBD7] last:border-0 ↵                text-[#272E28] hover:bg-[#F8F9FA]">Today</option> aka getByRole('combobox').first()
    2) <option value="today" class="relative flex select-none items-center ↵                rounded-sm outline-none focus:bg-accent focus:text-accent-foreground ↵                data-disabled:pointer-events-none ↵                data-disabled:opacity-50 px-4 py-3 text-lg cursor-pointer ↵                transition-colors border-b border-[#D6DBD7] last:border-0 ↵                text-[#272E28] hover:bg-[#F8F9FA]">Today</option> aka getByRole('combobox').nth(1)

Call log:
  - Expect "toBeVisible" with timeout 10000ms
  - waiting for getByText('Today', { exact: true })

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
        - link "Settings" [ref=e26] [cursor=pointer]:
          - /url: /settings
          - img [ref=e27]
          - generic [ref=e29]: Settings
      - button "Logout" [ref=e31] [cursor=pointer]:
        - img [ref=e32]
        - generic [ref=e34]: Logout
      - button "Collapse sidebar" [ref=e35]:
        - img [ref=e36]
    - generic [ref=e38]:
      - banner [ref=e39]:
        - generic [ref=e40]:
          - button "Notifications" [ref=e41]:
            - img [ref=e42]
            - generic [ref=e45]: "3"
          - generic [ref=e48]: A
          - generic [ref=e50]:
            - generic [ref=e51]: Ashil
            - generic [ref=e52]:
              - generic [ref=e53]: ADMIN
              - generic [ref=e54]: PLATFORM
      - main [ref=e56]:
        - generic [ref=e57]:
          - generic [ref=e58]:
            - generic [ref=e59]:
              - generic [ref=e61]:
                - heading "Hi, Ashil" [level=2] [ref=e62]
                - paragraph [ref=e63]: Here's what's happening with eCitizen today.
              - combobox [ref=e65]:
                - option "Today" [selected]
                - option "Last 7 Days"
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
            - generic [ref=e67]:
              - generic [ref=e68]:
                - generic [ref=e69]:
                  - generic [ref=e70]:
                    - img [ref=e72]
                    - generic [ref=e74]:
                      - paragraph [ref=e75]: Revenue generated
                      - paragraph [ref=e76]: Revenue across all ministries
                  - button "View Details →" [ref=e77] [cursor=pointer]
                - generic [ref=e79]:
                  - paragraph [ref=e80]: Total Revenue (KES) & (USD)
                  - generic [ref=e81]:
                    - generic [ref=e82]:
                      - img [ref=e83]
                      - generic [ref=e91]: KES 0
                    - generic [ref=e92]:
                      - generic [ref=e93]: "-100%"
                      - img [ref=e94]
                  - generic [ref=e96]:
                    - generic [ref=e97]:
                      - img [ref=e98]
                      - generic [ref=e108]: USD 0
                    - generic [ref=e109]:
                      - generic [ref=e110]: "-100%"
                      - img [ref=e111]
              - generic [ref=e113]:
                - generic [ref=e114]:
                  - generic [ref=e115]:
                    - img [ref=e117]
                    - generic [ref=e120]:
                      - paragraph [ref=e121]: No of Transactions
                      - paragraph [ref=e122]: 0 transactions
                  - button "View Transactions →" [ref=e123] [cursor=pointer]
                - generic [ref=e125]:
                  - paragraph [ref=e126]: Total Transactions (KES) & (USD)
                  - generic [ref=e127]:
                    - generic [ref=e128]:
                      - img [ref=e129]
                      - generic [ref=e137]: "0"
                    - generic [ref=e138]:
                      - generic [ref=e139]: "-100%"
                      - img [ref=e140]
                  - generic [ref=e142]:
                    - generic [ref=e143]:
                      - img [ref=e144]
                      - generic [ref=e154]: "0"
                    - generic [ref=e155]:
                      - generic [ref=e156]: "-100%"
                      - img [ref=e157]
              - generic [ref=e159]:
                - generic [ref=e160]:
                  - generic [ref=e161]:
                    - img [ref=e163]
                    - generic [ref=e167]:
                      - paragraph [ref=e168]: Total Transactions
                      - paragraph [ref=e169]: 0 Transactions
                  - button "View Details →" [ref=e170] [cursor=pointer]
                - generic [ref=e172]:
                  - generic [ref=e173]:
                    - generic [ref=e174]:
                      - generic [ref=e175]: Successful Transactions
                      - generic [ref=e176]:
                        - generic [ref=e177]: "-100%"
                        - img [ref=e178]
                    - paragraph [ref=e180]: 0%
                    - paragraph [ref=e181]: "0"
                  - generic [ref=e183]:
                    - generic [ref=e184]:
                      - generic [ref=e185]: Failed Transactions
                      - generic [ref=e186]: 0.0 —
                    - paragraph [ref=e187]: 0%
                    - paragraph [ref=e188]: "0"
          - generic [ref=e189]:
            - generic [ref=e190]:
              - generic [ref=e191]:
                - generic [ref=e192]:
                  - paragraph [ref=e193]: Monthly Revenue for All Services
                  - generic [ref=e194]:
                    - img [ref=e195]
                    - generic [ref=e198]: Showing last 12 months · auto-updates daily
                - generic [ref=e199]:
                  - button "KES" [ref=e200]
                  - button "USD" [ref=e201]
              - generic [ref=e202]:
                - generic [ref=e203]:
                  - generic [ref=e204]:
                    - paragraph [ref=e205]: KES 532.0K
                    - generic [ref=e208]: Gross Amount
                  - generic [ref=e209]:
                    - paragraph [ref=e210]: KES 80.6M
                    - generic [ref=e213]: Net Amount
                  - generic [ref=e214]:
                    - paragraph [ref=e215]: KES 532.0K
                    - generic [ref=e218]: Commission
                  - generic [ref=e219]:
                    - paragraph [ref=e220]: 99.99%
                    - generic [ref=e223]: Transaction Success Ratio
                - generic [ref=e224]:
                  - generic [ref=e225]:
                    - generic [ref=e228]: Gross KES
                    - generic [ref=e231]: Net KES
                    - generic [ref=e234]: Commission KES
                  - img [ref=e238]:
                    - generic [ref=e255]:
                      - generic [ref=e256]: Aug
                      - generic [ref=e257]: Sep
                      - generic [ref=e258]: Oct
                      - generic [ref=e259]: Nov
                      - generic [ref=e260]: Dec
                      - generic [ref=e261]: Jan
                      - generic [ref=e262]: Feb
                      - generic [ref=e263]: Mar
                      - generic [ref=e264]: Apr
                      - generic [ref=e265]: May
                      - generic [ref=e266]: Jun
                      - generic [ref=e267]: Jul
                    - generic [ref=e268]:
                      - generic [ref=e269]: "0"
                      - generic [ref=e270]: 25.0M
                      - generic [ref=e271]: 50.0M
                      - generic [ref=e272]: 75.0M
                      - generic "100.0M" [ref=e273]: 100.…
            - generic [ref=e275]:
              - generic [ref=e277]:
                - heading "Gateway Payments" [level=2] [ref=e278]
                - combobox [ref=e280]:
                  - option "Today" [selected]
                  - option "Last 7 Days"
                  - option "Last 30 Days"
                  - option "Last 90 Days"
                  - option "Custom Range"
              - generic [ref=e281]:
                - generic [ref=e282]:
                  - generic [ref=e283]:
                    - paragraph [ref=e284]: "0"
                    - paragraph [ref=e285]: Transactions (today)
                  - generic [ref=e286]:
                    - paragraph [ref=e287]: 0%
                    - paragraph [ref=e288]: Success Rate
                  - generic [ref=e289]:
                    - paragraph [ref=e290]: 0%
                    - paragraph [ref=e291]: Availability
                  - generic [ref=e292]:
                    - paragraph [ref=e293]: "0"
                    - paragraph [ref=e294]: P95 Latency
                  - generic [ref=e295]:
                    - paragraph [ref=e296]: 0%
                    - paragraph [ref=e297]: Timeout Rate
                - generic [ref=e298]:
                  - generic [ref=e300]:
                    - generic [ref=e301]:
                      - paragraph [ref=e303]: Transactions by Payment Method
                      - generic [ref=e306]:
                        - generic [ref=e307]: No Transaction Data Available
                        - generic [ref=e308]: There is no data to display for this period.
                    - generic [ref=e309]:
                      - generic [ref=e310]:
                        - paragraph [ref=e311]: Success vs Failure by Method
                        - generic [ref=e313]:
                          - generic [ref=e314]: Success
                          - generic [ref=e316]: Failure
                      - generic [ref=e319]:
                        - generic [ref=e320]: No Success / Failure Data Available
                        - generic [ref=e321]: There is no data to display for this period.
                  - paragraph [ref=e324]: Share of Payment Methods
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
  29 |   test('TC3 — Date filter dropdown defaults to "Today"', async ({ page, logger }, testInfo: TestInfo) => {
  30 |     // ── Allure metadata ──────────────────────────────────────────────────────
  31 |     await allure.description('Verifies the chart date filter shows "Today" as the default selected value.');
  32 |     await allure.label('feature',  'Dashboard');
  33 |     await allure.label('story',    'Revenue Chart');
  34 |     await allure.label('severity', 'critical');
  35 |     await allure.label('priority', 'P1');
  36 |     await allure.label('testId',   'DASH-RC-TC3');
  37 | 
  38 |     // ── Setup ────────────────────────────────────────────────────────────────
  39 |     const dashboard = new DashboardPage(page);
  40 | 
  41 |     await logger.step('Step 1 — Navigate to Dashboard page', async () => {
  42 |       logger.info('Opening the Dashboard page');
  43 |       await dashboard.goto();
  44 |       logger.pass('Navigation to Dashboard initiated successfully');
  45 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  46 |     });
  47 | 
  48 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  49 |       logger.info('Waiting for all Dashboard elements to render');
  50 |       await dashboard.assertPageLoaded();
  51 |       logger.pass('Dashboard page is fully loaded with all required elements');
  52 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  53 |     });
  54 | 
  55 |     // ── Assertion ────────────────────────────────────────────────────────────
  56 |     await logger.step('Step 3 — "Today" label is visible in the date filter', async () => {
  57 |       // exact: true prevents matching "Today's Totals" or similar substrings
  58 |       logger.info('Asserting value/data: date filter default selection');
> 59 |       await expect(page.getByText('Today', { exact: true })).toBeVisible({ timeout: 10000 });
     |                                                              ^ Error: expect(locator).toBeVisible() failed
  60 | 
  61 |       // Record confirmed default value in the Allure report
  62 |       allure.parameter('Default filter value', 'Today');
  63 |       logger.pass('Value assertion passed');
  64 |       await attachScreenshot(page, testInfo, '03 — Date filter defaults to Today', page.getByText('Today', { exact: true }));
  65 |     });
  66 |   });
  67 | });
  68 | 
```