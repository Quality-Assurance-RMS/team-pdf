# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-tbl-tc2-table-rows-loaded.spec.ts >> Settlements — Table Rows Loaded >> TC2 — Settlement by Bank table loads with at least one row
- Location: tests/UI/Settlements/setl-tbl-tc2-table-rows-loaded.spec.ts:19:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: locator('table tbody tr').first()
Expected: visible
Timeout: 15000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 15000ms
  - waiting for locator('table tbody tr').first()

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
    - button "Expand submenu":
      - img
    - link "Settings":
      - /url: /settings
      - img
      - text: Settings
  - button "Logout":
    - img
    - text: Logout
  - button "Collapse sidebar":
    - img
- banner: A Ashil ADMIN PLATFORM
- main:
  - heading "Settlements" [level=2]
  - paragraph: 0 settlements, data are showing based on last-7-days
  - textbox "Search Batch name..."
  - combobox:
    - option "Today"
    - option "Last 7 Days" [selected]
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - combobox:
    - option "Status" [selected]
    - option "Settlement"
    - option "Payout Initiated"
    - option "Payout Completed"
    - option "Cancelled"
  - combobox:
    - option "Currency" [selected]
    - option "KES"
    - option "USD"
  - img
  - heading "239" [level=2]
  - paragraph: Total Batches Count
  - img
  - text: 152 0.0 —
  - img
  - text: 87 0.0 —
  - img
  - heading "73" [level=2]
  - paragraph: Settlements Count
  - img
  - text: 45 0.0 —
  - img
  - text: 28 0.0 —
  - img
  - heading "25.3K 25,311" [level=2]
  - paragraph: Total Transactions Amount
  - img
  - text: 18.5K KES 18,542.00 + 8.2%
  - img
  - img
  - text: 6,769 USD 6,769.00 1.2%
  - img
  - img
  - heading "14.3M 140.8M" [level=2]
  - paragraph: Total Settlement Amount
  - img
  - text: KES 248.8K KES 248,800.00 + 8.2%
  - img
  - img
  - text: USD 884.8K USD 884,800.00 + 8.2%
  - img
  - img
  - heading "29" [level=2]
  - paragraph: Batch Status
  - text: 9 Settled 20 Payout Completed Failed to load settlements. Please try again.
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file table-rows-loaded.spec.ts
  3  |  * @testId SETL-TBL-TC2
  4  |  * @feature Settlements — Table Data
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * Verifies the Settlement by Bank table loads with at least one data row.
  8  |  */
  9  | 
  10 | import { test, expect } from '../fixtures';
  11 | import type { TestInfo } from '@playwright/test';
  12 | import { allure } from 'allure-playwright';
  13 | import { SettlementsPage } from '../../pages/SettlementsPage';
  14 | import { attachScreenshot } from '../../utils/screenshotHelper';
  15 | 
  16 | test.describe('Settlements — Table Rows Loaded', () => {
  17 |   test.setTimeout(120000);
  18 | 
  19 |   test('TC2 — Settlement by Bank table loads with at least one row', async ({ page, logger }, testInfo: TestInfo) => {
  20 |     await allure.description('Verifies the Settlement by Bank table is populated with data rows after page load.');
  21 |     await allure.label('feature',  'Settlements');
  22 |     await allure.label('story',    'Table Data');
  23 |     await allure.label('severity', 'blocker');
  24 |     await allure.label('priority', 'P0');
  25 |     await allure.label('testId',   'SETL-TBL-TC2');
  26 | 
  27 |     await logger.step('Step 1 — Verifies the Settlement by Bank table is populated with data rows after page load', async () => {
  28 |       logger.info('Test scope: Verifies the Settlement by Bank table is populated with data rows after page load');
  29 |     });
  30 | 
  31 |     const settlementsPage = new SettlementsPage(page);
  32 | 
  33 |     await logger.step('Step 2 — Navigate to Settlements page', async () => {
  34 |       logger.info('Opening the Settlements page');
  35 |       await settlementsPage.goto();
  36 |       logger.pass('Navigation to Settlements page initiated successfully');
  37 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  38 |     });
  39 | 
  40 |     await logger.step('Step 3 — Verify page loads successfully', async () => {
  41 |       logger.info('Waiting for all Settlements page elements to render');
  42 |       await settlementsPage.assertPageLoaded();
  43 |       logger.pass('Settlements page is fully loaded with all required elements');
  44 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  45 |     });
  46 | 
  47 |     await logger.step('Step 4 — At least one table row is visible', async () => {
  48 |       logger.info('Verifying table structure and data are visible');
> 49 |       await expect(settlementsPage.tableRows.first()).toBeVisible({ timeout: 15000 });
     |                                                       ^ Error: expect(locator).toBeVisible() failed
  50 |       logger.pass('Table rows and columns rendered correctly');
  51 |       await attachScreenshot(page, testInfo, '03 — Table row visible', settlementsPage.tableRows.first());
  52 |     });
  53 |   });
  54 | });
  55 | 
```