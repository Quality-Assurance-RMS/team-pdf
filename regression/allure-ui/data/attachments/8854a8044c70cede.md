# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/bank-details-panel-visible.spec.ts >> Settlements — Selected Bank Details Panel >> TC1 — "Selected Bank Details" panel is visible
- Location: tests/UI/Settlements/bank-details-panel-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/Select a row to see details/i)
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByText(/Select a row to see details/i)

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
  2  |  * @file bank-details-panel-visible.spec.ts
  3  |  * @testId SETL-PANEL-TC1
  4  |  * @feature Settlements — Selected Bank Details Panel
  5  |  * @priority P1 / Critical
  6  |  *
  7  |  * Verifies that the "Selected Bank Details" panel is rendered to the right
  8  |  * of the Settlement by Bank table. Initially it shows "Select a row to see details".
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettlementsPage } from '../../pages/SettlementsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settlements — Selected Bank Details Panel', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — "Selected Bank Details" panel is visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies the Selected Bank Details side panel renders on the Settlements page.');
  22 |     await allure.label('feature',  'Settlements');
  23 |     await allure.label('story',    'Bank Details Panel');
  24 |     await allure.label('severity', 'critical');
  25 |     await allure.label('priority', 'P1');
  26 |     await allure.label('testId',   'SETL-PANEL-TC1');
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
  44 |     await logger.step('Step 3 — "Selected Bank Details" panel heading is visible', async () => {
  45 |       logger.info('Checking Selected Bank Details panel is rendered');
  46 |       await expect(settlementsPage.selectedBankDetailsPanel).toBeVisible();
  47 |       logger.pass('Selected Bank Details panel is visible and accessible');
  48 |       await attachScreenshot(page, testInfo, '03 — Selected Bank Details panel visible', settlementsPage.selectedBankDetailsPanel);
  49 |     });
  50 | 
  51 |     await logger.step('Step 4 — Panel shows "Select a row to see details" placeholder', async () => {
  52 |       logger.info('Asserting visibility: Select a row to see details placeholder text');
> 53 |       await expect(page.getByText(/Select a row to see details/i)).toBeVisible();
     |                                                                    ^ Error: expect(locator).toBeVisible() failed
  54 |       logger.pass('Select a row to see details placeholder text is visible');
  55 |       await attachScreenshot(page, testInfo, '04 — Select a row placeholder visible', page.getByText(/Select a row to see details/i));
  56 |     });
  57 |   });
  58 | });
  59 | 
```