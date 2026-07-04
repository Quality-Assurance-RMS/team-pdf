# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Reports/report-status-badges.spec.ts >> Reports — Conversion Ratio >> TC1 — Conversion Ratio is visible in the Settlement & Payout Overview
- Location: tests/UI/Reports/report-status-badges.spec.ts:20:7

# Error details

```
Error: Conversion Ratio should contain a % value

expect(received).not.toBeNull()

Received: null
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
            - generic [ref=e61]:
              - heading "Reports" [level=2] [ref=e62]
              - paragraph
            - combobox [ref=e65]:
              - option "Today" [selected]
              - option "Last 7 days"
              - option "Last 30 days"
              - option "Last 1 year"
              - option "Custom Range"
          - generic [ref=e68]:
            - generic [ref=e69]:
              - generic [ref=e70]:
                - img [ref=e72]
                - generic [ref=e76]:
                  - paragraph [ref=e77]: Transactions received
                  - paragraph [ref=e78]: 0 transactions
              - separator [ref=e79]
              - generic [ref=e80]:
                - generic [ref=e81]:
                  - generic [ref=e82]:
                    - generic [ref=e83]:
                      - img [ref=e84]
                      - generic [ref=e92]: KES 0
                    - generic [ref=e93]: 0.0 —
                  - generic [ref=e94]:
                    - generic [ref=e95]: "0"
                    - generic [ref=e96]: 0.0 —
                - separator [ref=e97]
                - generic [ref=e98]:
                  - generic [ref=e99]:
                    - generic [ref=e100]:
                      - img [ref=e101]
                      - generic [ref=e111]: USD 0
                    - generic [ref=e112]: 0.0 —
                  - generic [ref=e113]:
                    - generic [ref=e114]: "0"
                    - generic [ref=e115]: 0.0 —
            - generic [ref=e116]:
              - generic [ref=e117]:
                - img [ref=e119]
                - generic [ref=e121]:
                  - paragraph [ref=e122]: Transaction recon
                  - paragraph [ref=e123]: 0 reconciled
              - separator [ref=e124]
              - generic [ref=e125]:
                - generic [ref=e126]:
                  - generic [ref=e127]:
                    - generic [ref=e128]:
                      - img [ref=e129]
                      - generic [ref=e137]: KES 0
                    - generic [ref=e138]: 0.0 —
                  - generic [ref=e139]:
                    - generic [ref=e140]: "0"
                    - generic [ref=e141]: 0.0 —
                - separator [ref=e142]
                - generic [ref=e143]:
                  - generic [ref=e144]:
                    - generic [ref=e145]:
                      - img [ref=e146]
                      - generic [ref=e156]: UES 0
                    - generic [ref=e157]: 0.0 —
                  - generic [ref=e158]:
                    - generic [ref=e159]: "0"
                    - generic [ref=e160]: 0.0 —
            - generic [ref=e161]:
              - generic [ref=e162]:
                - img [ref=e164]
                - generic [ref=e167]:
                  - paragraph [ref=e168]: Transaction settled
                  - paragraph [ref=e169]: 0 settled
              - separator [ref=e170]
              - generic [ref=e171]:
                - generic [ref=e172]:
                  - generic [ref=e173]:
                    - generic [ref=e174]:
                      - img [ref=e175]
                      - generic [ref=e183]: KES 0
                    - generic [ref=e184]: 0.0 —
                  - generic [ref=e185]:
                    - generic [ref=e186]: "0"
                    - generic [ref=e187]: 0.0 —
                - separator [ref=e188]
                - generic [ref=e189]:
                  - generic [ref=e190]:
                    - generic [ref=e191]:
                      - img [ref=e192]
                      - generic [ref=e202]: USD 0
                    - generic [ref=e203]: 0.0 —
                  - generic [ref=e204]:
                    - generic [ref=e205]: "0"
                    - generic [ref=e206]: 0.0 —
            - generic [ref=e207]:
              - generic [ref=e208]:
                - img [ref=e210]
                - generic [ref=e212]:
                  - paragraph [ref=e213]: Payout
                  - paragraph [ref=e214]: 0 payouts
              - separator [ref=e215]
              - generic [ref=e216]:
                - generic [ref=e217]:
                  - generic [ref=e218]:
                    - generic [ref=e219]:
                      - img [ref=e220]
                      - generic [ref=e228]: KES 0
                    - generic [ref=e229]: 0.0 —
                  - generic [ref=e230]:
                    - generic [ref=e231]: "0"
                    - generic [ref=e232]: 0.0 —
                - separator [ref=e233]
                - generic [ref=e234]:
                  - generic [ref=e235]:
                    - generic [ref=e236]:
                      - img [ref=e237]
                      - generic [ref=e247]: USD 0
                    - generic [ref=e248]: 0.0 —
                  - generic [ref=e249]:
                    - generic [ref=e250]: "0"
                    - generic [ref=e251]: 0.0 —
            - generic [ref=e252]:
              - generic [ref=e253]:
                - img [ref=e255]
                - generic [ref=e257]:
                  - paragraph [ref=e258]: Failed
                  - paragraph [ref=e259]: 0 failed
              - separator [ref=e260]
              - generic [ref=e261]:
                - generic [ref=e262]:
                  - generic [ref=e263]:
                    - generic [ref=e264]:
                      - img [ref=e265]
                      - generic [ref=e273]: KES 0
                    - generic [ref=e274]: 0.0 —
                  - generic [ref=e275]:
                    - generic [ref=e276]: "0"
                    - generic [ref=e277]: 0.0 —
                - separator [ref=e278]
                - generic [ref=e279]:
                  - generic [ref=e280]:
                    - generic [ref=e281]:
                      - img [ref=e282]
                      - generic [ref=e292]: USD 0
                    - generic [ref=e293]: 0.0 —
                  - generic [ref=e294]:
                    - generic [ref=e295]: "0"
                    - generic [ref=e296]: 0.0 —
          - generic [ref=e298]:
            - generic [ref=e299]:
              - heading "Settlement & Payout Overview" [level=2] [ref=e300]
              - generic [ref=e301]:
                - img [ref=e302]
                - generic [ref=e305]: Showing last 12 months · auto-updates daily
            - generic [ref=e307]:
              - generic [ref=e308]:
                - generic [ref=e309]:
                  - generic [ref=e310]:
                    - img [ref=e311]
                    - generic [ref=e315]: Settlement
                    - generic [ref=e317]: KES
                    - generic [ref=e319]: USD
                  - generic [ref=e320]:
                    - img [ref=e321]
                    - generic [ref=e324]: Payouts
                    - generic [ref=e326]: KES
                    - generic [ref=e328]: USD
                - generic [ref=e329]:
                  - img [ref=e330]
                  - paragraph [ref=e334]: No settlement and payout data
                  - paragraph [ref=e335]: No data available for this period
              - generic [ref=e336]:
                - generic [ref=e337]:
                  - generic [ref=e338]:
                    - img [ref=e340]
                    - generic [ref=e342]: Settlement
                  - generic [ref=e343]:
                    - generic [ref=e344]:
                      - generic [ref=e346]:
                        - text: KES 0
                        - generic: KES 0.00
                      - paragraph [ref=e347]: Total KES
                    - generic [ref=e348]:
                      - generic [ref=e350]:
                        - text: USD 0
                        - generic: USD 0.00
                      - paragraph [ref=e351]: Total USD
                - generic [ref=e352]:
                  - generic [ref=e353]:
                    - img [ref=e355]
                    - generic [ref=e358]: Payouts
                  - generic [ref=e359]:
                    - generic [ref=e360]:
                      - generic [ref=e362]:
                        - text: KES 0
                        - generic: KES 0.00
                      - paragraph [ref=e363]: Total KES
                    - generic [ref=e364]:
                      - generic [ref=e366]:
                        - text: USD 0
                        - generic: USD 0.00
                      - paragraph [ref=e367]: Total USD
                - generic [ref=e368]:
                  - paragraph [ref=e369]: "0"
                  - paragraph [ref=e370]: Conversion Ratio
          - generic [ref=e372]:
            - generic [ref=e376]:
              - heading "Financial Reports" [level=2] [ref=e377]
              - paragraph [ref=e378]: View and download system reports
            - generic [ref=e379]:
              - generic [ref=e380]:
                - heading "Settlement Report" [level=3] [ref=e382]
                - button "Select date range" [ref=e384]
                - button "Download Report" [disabled] [ref=e385]:
                  - text: Download Report
                  - img [ref=e386]
              - generic [ref=e388]:
                - heading "MCDA Payout Report" [level=3] [ref=e390]
                - button "Select date range" [ref=e392]
                - button "Download Report" [disabled] [ref=e393]:
                  - text: Download Report
                  - img [ref=e394]
              - generic [ref=e396]:
                - heading "Refund Report" [level=3] [ref=e398]
                - button "Select date range" [ref=e400]
                - button "Download Report" [disabled] [ref=e401]:
                  - text: Download Report
                  - img [ref=e402]
              - generic [ref=e404]:
                - heading "Commission Report" [level=3] [ref=e406]
                - button "Select date range" [ref=e408]
                - button "Download Report" [disabled] [ref=e409]:
                  - text: Download Report
                  - img [ref=e410]
              - generic [ref=e412]:
                - heading "Reconciliation Report" [level=3] [ref=e414]
                - button "Select date range" [ref=e416]
                - button "Download Report" [disabled] [ref=e417]:
                  - text: Download Report
                  - img [ref=e418]
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file report-status-badges.spec.ts
  3  |  * @testId RPT-BADGE-TC1
  4  |  * @feature Reports — Conversion Ratio
  5  |  * @priority P2 / Normal
  6  |  *
  7  |  * Verifies that the Conversion Ratio metric is visible in the
  8  |  * Settlement & Payout Overview section of the Reports page.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { ReportsPage } from '../../pages/ReportsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Reports — Conversion Ratio', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — Conversion Ratio is visible in the Settlement & Payout Overview', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies the Conversion Ratio metric renders in the Settlement & Payout Overview section.');
  22 |     await allure.label('feature',  'Reports');
  23 |     await allure.label('story',    'Conversion Ratio');
  24 |     await allure.label('severity', 'normal');
  25 |     await allure.label('priority', 'P2');
  26 |     await allure.label('testId',   'RPT-BADGE-TC1');
  27 | 
  28 |     const reportsPage = new ReportsPage(page);
  29 | 
  30 |     await logger.step('Step 1 — Navigate to Reports page', async () => {
  31 |       logger.info('Opening the Reports page');
  32 |       await reportsPage.goto();
  33 |       logger.pass('Navigation to Reports page initiated successfully');
  34 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  35 |     });
  36 | 
  37 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  38 |       logger.info('Waiting for all Reports page elements to render');
  39 |       await reportsPage.assertPageLoaded();
  40 |       logger.pass('Reports page is fully loaded with all required elements');
  41 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  42 |     });
  43 | 
  44 |     await logger.step('Step 3 — "Conversion Ratio" metric is visible', async () => {
  45 |       logger.info('Checking status badges are visible in the report list');
  46 |       await expect(reportsPage.conversionRatio).toBeVisible({ timeout: 15000 });
  47 |       logger.pass('Status badges are visible');
  48 |       await attachScreenshot(page, testInfo, '03 — Conversion Ratio visible', reportsPage.conversionRatio);
  49 |     });
  50 | 
  51 |     await logger.step('Step 4 — Conversion Ratio shows a valid percentage value', async () => {
  52 |       const el   = reportsPage.conversionRatio;
  53 |       const text = await el.locator('xpath=ancestor::*[2]').innerText()
  54 |         .catch(() => el.locator('xpath=ancestor::*[1]').innerText())
  55 |         .catch(() => el.innerText());
  56 |       const match = text.match(/(\d+(\.\d+)?)%/);
> 57 |       expect(match, 'Conversion Ratio should contain a % value').not.toBeNull();
     |                                                                      ^ Error: Conversion Ratio should contain a % value
  58 |       if (match) {
  59 |         allure.parameter('Conversion Ratio', match[0]);
  60 |       }
  61 |       await attachScreenshot(page, testInfo, '04 — Conversion Ratio value validated');
  62 |     });
  63 |   });
  64 | });
  65 | 
```