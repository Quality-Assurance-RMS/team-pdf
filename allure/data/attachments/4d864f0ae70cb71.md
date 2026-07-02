# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/heading-visible.spec.ts >> Settlements — Page Heading >> TC1 — "Settlements" heading and count are visible
- Location: tests/UI/Settlements/heading-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: locator('h1, h2, h3').filter({ hasText: /^Settlements$/i }).or(getByText('Settlements').first())
Expected: visible
Error: strict mode violation: locator('h1, h2, h3').filter({ hasText: /^Settlements$/i }).or(getByText('Settlements').first()) resolved to 2 elements:
    1) <span class="text-md font-medium">Settlements</span> aka getByRole('link', { name: 'Settlements' })
    2) <h2 class="text-xl sm:text-2xl text-gray-600 font-semibold">Settlements</h2> aka getByRole('heading', { name: 'Settlements' })

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for locator('h1, h2, h3').filter({ hasText: /^Settlements$/i }).or(getByText('Settlements').first())

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
            - generic [ref=e60]:
              - heading "Settlements" [level=2] [ref=e61]
              - paragraph [ref=e62]: 0 settlements have been initiated.
            - combobox [ref=e64]:
              - option "Today"
              - option "Last 7 Days" [selected]
              - option "Last 30 Days"
              - option "Last 90 Days"
              - option "Custom Range"
          - generic [ref=e65]:
            - generic [ref=e66]:
              - generic [ref=e67]:
                - img "icon" [ref=e69]
                - generic [ref=e70]:
                  - heading "Gross Collected" [level=2] [ref=e71]
                  - text: 0% vs prior 7 days
              - generic [ref=e73]:
                - generic [ref=e74]:
                  - img [ref=e76]
                  - generic [ref=e84]:
                    - generic [ref=e85]: KES 0
                    - generic: KES 0.00
                - generic [ref=e86]:
                  - img [ref=e88]
                  - generic [ref=e98]:
                    - generic [ref=e99]: USD 0
                    - generic: USD 0.00
            - generic [ref=e100]:
              - generic [ref=e101]:
                - img "icon" [ref=e103]
                - generic [ref=e104]:
                  - heading "Total Fees" [level=2] [ref=e105]
                  - paragraph [ref=e106]: Avg 0%
              - generic [ref=e108]:
                - generic [ref=e109]:
                  - img [ref=e111]
                  - generic [ref=e119]:
                    - generic [ref=e120]: KES 0
                    - generic: KES 0.00
                - generic [ref=e121]:
                  - img [ref=e123]
                  - generic [ref=e133]:
                    - generic [ref=e134]: USD 0
                    - generic: USD 0.00
            - generic [ref=e135]:
              - generic [ref=e136]:
                - img "icon" [ref=e138]
                - generic [ref=e139]:
                  - heading "Net Settlement" [level=2] [ref=e140]
                  - paragraph [ref=e141]: Settlement Activity Summary
              - generic [ref=e143]:
                - generic [ref=e144]:
                  - img [ref=e146]
                  - generic [ref=e154]:
                    - generic [ref=e155]: KES 0
                    - generic: KES 0.00
                - generic [ref=e156]:
                  - img [ref=e158]
                  - generic [ref=e168]:
                    - generic [ref=e169]: USD 0
                    - generic: USD 0.00
            - generic [ref=e170]:
              - generic [ref=e171]:
                - img "icon" [ref=e173]
                - heading "Last Settlement" [level=2] [ref=e175]
              - generic [ref=e181]:
                - text: No settlement available
                - text: for the selected period
          - generic [ref=e182]:
            - paragraph [ref=e183]: Settlement by Bank
            - generic [ref=e184]:
              - table [ref=e187]:
                - rowgroup [ref=e188]:
                  - row "Service Name Gross Collected Fees Net Settlement Last Settlement Status" [ref=e189]:
                    - columnheader "Service Name" [ref=e190]
                    - columnheader "Gross Collected" [ref=e191]
                    - columnheader "Fees" [ref=e192]
                    - columnheader "Net Settlement" [ref=e193]
                    - columnheader "Last Settlement" [ref=e194]
                    - columnheader "Status" [ref=e195]
                - rowgroup [ref=e196]:
                  - row "No Data Found" [ref=e197]:
                    - cell "No Data Found" [ref=e198]
              - generic [ref=e199]:
                - heading "Selected Bank Details" [level=3] [ref=e200]
                - paragraph [ref=e201]: select the settlement data
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file heading-visible.spec.ts
  3  |  * @testId SETL-HDR-TC1
  4  |  * @feature Settlements — Page Heading
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * Verifies that the "Settlements" heading and the initiated-count sub-heading
  8  |  * are visible after navigating to /settlements.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettlementsPage } from '../../pages/SettlementsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settlements — Page Heading', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — "Settlements" heading and count are visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies the Settlements heading and "X settlements have been initiated" text render on page load.');
  22 |     await allure.label('feature',  'Settlements');
  23 |     await allure.label('story',    'Page Heading');
  24 |     await allure.label('severity', 'blocker');
  25 |     await allure.label('priority', 'P0');
  26 |     await allure.label('testId',   'SETL-HDR-TC1');
  27 | 
  28 |     const settlementsPage = new SettlementsPage(page);
  29 | 
  30 |     await logger.step('Step 1 — Navigate to Settlements page', async () => {
  31 |       logger.info('Opening the Settlements page');
  32 |       await settlementsPage.goto();
  33 |       logger.pass('Navigation to Settlements page initiated successfully');
  34 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  35 |     });
  36 | 
  37 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  38 |       logger.info('Waiting for all Settlements page elements to render');
  39 |       await settlementsPage.assertPageLoaded();
  40 |       logger.pass('Settlements page is fully loaded with all required elements');
  41 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  42 |     });
  43 | 
  44 |     await logger.step('Step 3 — "Settlements" heading is visible', async () => {
  45 |       logger.info('Asserting visibility: Settlements page heading');
> 46 |       await expect(settlementsPage.heading).toBeVisible();
     |                                             ^ Error: expect(locator).toBeVisible() failed
  47 |       logger.pass('Settlements page heading is visible on the page');
  48 |       await attachScreenshot(page, testInfo, '03 — Settlement heading visible', settlementsPage.heading);
  49 |     });
  50 | 
  51 |     await logger.step('Step 4 — Settlement initiated count is visible', async () => {
  52 |       logger.info('Asserting visibility: Settlement initiated count sub-heading');
  53 |       await expect(settlementsPage.settlementSubCount).toBeVisible();
  54 |       logger.pass('Settlement initiated count sub-heading is visible on the page');
  55 |       await attachScreenshot(page, testInfo, '04 — Settlement initiated count visible', settlementsPage.settlementSubCount);
  56 |     });
  57 |   });
  58 | });
  59 | 
```