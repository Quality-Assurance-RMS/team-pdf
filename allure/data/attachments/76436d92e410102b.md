# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/kpi-cards-successful-payments-range.spec.ts >> Dashboard — KPI Cards >> TC5 — Successful Payments percentage is between 0 and 100
- Location: tests/UI/Dashboard/kpi-cards-successful-payments-range.spec.ts:34:7

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.innerText: Target page, context or browser has been closed
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
  2  |  * @file kpi-cards-successful-payments-range.spec.ts
  3  |  * @testId DASH-KPI-TC5
  4  |  * @feature Dashboard — KPI Cards
  5  |  * @priority P1 / Critical
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that the Successful Payments KPI value is a valid percentage
  10 |  * within the range 0–100%. A value outside this range (e.g. "150%") would
  11 |  * indicate a calculation error in the analytics backend.
  12 |  *
  13 |  * WHY THIS MATTERS
  14 |  * ----------------
  15 |  * The Successful Payments card drives SLA reporting. An out-of-range value
  16 |  * could be misread by automated monitoring as 100% even when the real
  17 |  * success rate is lower, masking a production incident.
  18 |  *
  19 |  * PRE-CONDITIONS
  20 |  * --------------
  21 |  *  - Authenticated session (OTP login)
  22 |  *  - At least one completed transaction exists for the current period
  23 |  */
  24 | 
  25 | import { test, expect } from '../fixtures';
  26 | import type { TestInfo } from '@playwright/test';
  27 | import { allure } from 'allure-playwright';
  28 | import { DashboardPage } from '../../pages/DashboardPage';
  29 | import { attachScreenshot } from '../../utils/screenshotHelper';
  30 | 
  31 | test.describe('Dashboard — KPI Cards', () => {
  32 |   test.setTimeout(120000);
  33 | 
  34 |   test('TC5 — Successful Payments percentage is between 0 and 100', async ({ page, logger }, testInfo: TestInfo) => {
  35 |     // ── Allure metadata ──────────────────────────────────────────────────────
  36 |     await allure.description('Verifies that the Successful Payments KPI value is a valid percentage within 0–100%.');
  37 |     await allure.label('feature',  'Dashboard');
  38 |     await allure.label('story',    'KPI Cards');
  39 |     await allure.label('severity', 'critical');
  40 |     await allure.label('priority', 'P1');
  41 |     await allure.label('testId',   'DASH-KPI-TC5');
  42 | 
  43 |     // ── Setup ────────────────────────────────────────────────────────────────
  44 |     const dashboard = new DashboardPage(page);
  45 | 
  46 |     await logger.step('Step 1 — Navigate to Dashboard page', async () => {
  47 |       logger.info('Opening the Dashboard page');
  48 |       await dashboard.goto();
  49 |       logger.pass('Navigation to Dashboard initiated successfully');
  50 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  51 |     });
  52 | 
  53 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  54 |       logger.info('Waiting for all Dashboard elements to render');
  55 |       await dashboard.assertPageLoaded();
  56 |       logger.pass('Dashboard page is fully loaded with all required elements visible');
  57 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  58 |     });
  59 | 
  60 |     // ── Assertion ────────────────────────────────────────────────────────────
  61 |     await logger.step('Step 3 — Read Successful Payments value and validate range', async () => {
  62 |       logger.info('Reading Successful Payments percentage from the page');
  63 |       // Walk up from the label to capture the card's full text block including the value
  64 |       const el   = page.getByText(/Successful Payments/i);
  65 |       const text = await el.locator('xpath=ancestor::*[3]').innerText()
  66 |         .catch(() => el.locator('xpath=ancestor::*[2]').innerText())
  67 |         .catch(() => el.locator('xpath=ancestor::*[1]').innerText())
> 68 |         .catch(() => el.innerText());
     |                         ^ Error: locator.innerText: Target page, context or browser has been closed
  69 | 
  70 |       const match = text.match(/(\d+(\.\d+)?)%/);
  71 |       expect(match, 'Card text should contain a % value').not.toBeNull();
  72 | 
  73 |       const value = parseFloat(match![1]);
  74 | 
  75 |       logger.pass('Successful Payments percentage captured: ' + String(value));
  76 |       // Log the extracted value for easy debugging in the Allure report
  77 |       allure.parameter('Successful Payments %', String(value));
  78 | 
  79 |       // A percentage must always sit within the mathematical range of 0–100
  80 |       expect(value).toBeGreaterThanOrEqual(0);
  81 |       expect(value).toBeLessThanOrEqual(100);
  82 |       await attachScreenshot(page, testInfo, '03 — Successful Payments range validated');
  83 |     });
  84 |   });
  85 | });
  86 | 
```