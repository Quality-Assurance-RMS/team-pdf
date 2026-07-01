# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/kpi-cards-numeric-values.spec.ts >> Dashboard — KPI Cards >> TC2 — KPI cards display non-empty numeric values
- Location: tests/UI/Dashboard/kpi-cards-numeric-values.spec.ts:35:7

# Error details

```
Error: expect(received).toMatch(expected)

Expected pattern: /KES\s*[\d,]+(\.\d+)?/i
Received string:  "Revenue generated·
Revenue across all ministries"
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
  2  |  * @file kpi-cards-numeric-values.spec.ts
  3  |  * @testId DASH-KPI-TC2
  4  |  * @feature Dashboard — KPI Cards
  5  |  * @priority P0 / Critical
  6  |  *
  7  |  * PURPOSE
  8  |  * -------
  9  |  * Verifies that every KPI card shows a non-empty, correctly formatted
  10 |  * numeric value. A card that renders its label but leaves the value blank
  11 |  * (e.g. due to a failed API call) is as bad as a missing card for operators.
  12 |  *
  13 |  * FORMAT RULES
  14 |  * ------------
  15 |  *  - Revenue generated    → "KES <number>"  e.g. "KES 105"
  16 |  *  - No of Transactions   → plain integer    e.g. "9"
  17 |  *  - Successful Payments  → percentage       e.g. "100%"
  18 |  *  - Failed Payments      → percentage       e.g. "0%"
  19 |  *
  20 |  * PRE-CONDITIONS
  21 |  * --------------
  22 |  *  - Authenticated session (OTP login)
  23 |  *  - At least one transaction exists in the system for the current period
  24 |  */
  25 | 
  26 | import { test, expect } from '../fixtures';
  27 | import type { TestInfo } from '@playwright/test';
  28 | import { allure } from 'allure-playwright';
  29 | import { DashboardPage } from '../../pages/DashboardPage';
  30 | import { attachScreenshot } from '../../utils/screenshotHelper';
  31 | 
  32 | test.describe('Dashboard — KPI Cards', () => {
  33 |   test.setTimeout(120000);
  34 | 
  35 |   test('TC2 — KPI cards display non-empty numeric values', async ({ page, logger }, testInfo: TestInfo) => {
  36 |     await allure.description('Verifies that each KPI card displays a numeric value and is not blank.');
  37 |     await allure.label('feature',  'Dashboard');
  38 |     await allure.label('story',    'KPI Cards');
  39 |     await allure.label('severity', 'critical');
  40 |     await allure.label('priority', 'P0');
  41 |     await allure.label('testId',   'DASH-KPI-TC2');
  42 | 
  43 |     const dashboard = new DashboardPage(page);
  44 | 
  45 |     await logger.step('Step 1 — Navigate to Dashboard page', async () => {
  46 |       logger.info('Opening the Dashboard page');
  47 |       await dashboard.goto();
  48 |       logger.pass('Navigation to Dashboard initiated successfully');
  49 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  50 |     });
  51 | 
  52 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  53 |       logger.info('Waiting for all Dashboard elements to render');
  54 |       await dashboard.assertPageLoaded();
  55 |       logger.pass('Dashboard page is fully loaded with all required elements visible');
  56 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  57 |     });
  58 | 
  59 |     const cards = [
  60 |       { label: 'Revenue generated',   pattern: /KES\s*[\d,]+(\.\d+)?/i },
  61 |       { label: 'No of Transactions',  pattern: /\d+/ },
  62 |       { label: 'Successful Payments', pattern: /\d+(\.\d+)?%/ },
  63 |       { label: 'Failed Payments',     pattern: /\d+(\.\d+)?%/ },
  64 |     ];
  65 | 
  66 |     let stepNum = 3;
  67 |     for (const { label, pattern } of cards) {
  68 |       const stepLabel = `Step ${stepNum}`;
  69 |       const screenshotPrefix = String(stepNum).padStart(2, '0');
  70 |       await logger.step(`${stepLabel} — ${label} — value matches expected format`, async () => {
  71 |         logger.info(`Reading ${label} value from the page`);
  72 |         const el   = page.getByText(new RegExp(label, 'i'));
  73 |         const text = await el.locator('xpath=ancestor::*[2]').innerText()
  74 |           .catch(() => el.locator('xpath=ancestor::*[1]').innerText())
  75 |           .catch(() => el.innerText());
  76 | 
  77 |         logger.pass(`${label} value captured: ` + text.trim());
  78 |         allure.parameter(label, text.trim());
> 79 |         expect(text).toMatch(pattern);
     |                      ^ Error: expect(received).toMatch(expected)
  80 |         await attachScreenshot(page, testInfo, `${screenshotPrefix} — ${label} value validated`);
  81 |       });
  82 |       stepNum++;
  83 |     }
  84 |   });
  85 | });
  86 | 
```