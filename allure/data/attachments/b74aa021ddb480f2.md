# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/settled-status-badge.spec.ts >> Settlements — Settled Status Badge >> TC1 — "Settled" status badge is visible in the table
- Location: tests/UI/Settlements/settled-status-badge.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: locator('table tbody').getByText(/^Settled$/i).first()
Expected: visible
Timeout: 15000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 15000ms
  - waiting for locator('table tbody').getByText(/^Settled$/i).first()

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
  - heading "Settlements" [level=2]
  - paragraph: 0 settlements have been initiated.
  - combobox:
    - option "Today"
    - option "Last 7 Days" [selected]
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - img "icon"
  - heading "Gross Collected" [level=2]
  - text: 0% vs prior 7 days
  - img
  - text: KES 0 KES 0.00
  - img
  - text: USD 0 USD 0.00
  - img "icon"
  - heading "Total Fees" [level=2]
  - paragraph: Avg 0%
  - img
  - text: KES 0 KES 0.00
  - img
  - text: USD 0 USD 0.00
  - img "icon"
  - heading "Net Settlement" [level=2]
  - paragraph: Settlement Activity Summary
  - img
  - text: KES 0 KES 0.00
  - img
  - text: USD 0 USD 0.00
  - img "icon"
  - heading "Last Settlement" [level=2]
  - text: No settlement available for the selected period
  - paragraph: Settlement by Bank
  - table:
    - rowgroup:
      - row "Service Name Gross Collected Fees Net Settlement Last Settlement Status":
        - columnheader "Service Name"
        - columnheader "Gross Collected"
        - columnheader "Fees"
        - columnheader "Net Settlement"
        - columnheader "Last Settlement"
        - columnheader "Status"
    - rowgroup:
      - row "No Data Found":
        - cell "No Data Found"
  - heading "Selected Bank Details" [level=3]
  - paragraph: select the settlement data
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file settled-status-badge.spec.ts
  3  |  * @testId SETL-BADGE-TC1
  4  |  * @feature Settlements — Status Badge
  5  |  * @priority P2 / Normal
  6  |  *
  7  |  * Verifies that at least one "Settled" status badge is rendered in the
  8  |  * Settlement by Bank table.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettlementsPage } from '../../pages/SettlementsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settlements — Settled Status Badge', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — "Settled" status badge is visible in the table', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies at least one Settled badge appears in the Settlement by Bank table.');
  22 |     await allure.label('feature',  'Settlements');
  23 |     await allure.label('story',    'Status Badge');
  24 |     await allure.label('severity', 'normal');
  25 |     await allure.label('priority', 'P2');
  26 |     await allure.label('testId',   'SETL-BADGE-TC1');
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
  44 |     await logger.step('Step 3 — At least one "Settled" badge is visible', async () => {
  45 |       logger.info('Asserting visibility: Settled status badge in the table');
> 46 |       await expect(settlementsPage.settledBadge).toBeVisible({ timeout: 15000 });
     |                                                  ^ Error: expect(locator).toBeVisible() failed
  47 |       logger.pass('Settled status badge is visible on the page');
  48 |       await attachScreenshot(page, testInfo, '03 — Settled badge visible', settlementsPage.settledBadge);
  49 |     });
  50 |   });
  51 | });
  52 | 
```