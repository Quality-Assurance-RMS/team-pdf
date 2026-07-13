# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-tbl-tc1-table-columns-visible.spec.ts >> Settlements — Table Columns >> TC1 — All Settlement by Bank table columns are visible
- Location: tests/UI/Settlements/setl-tbl-tc1-table-columns-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByRole('columnheader', { name: /Service Name/i })
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByRole('columnheader', { name: /Service Name/i })

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
  2  |  * @file table-columns-visible.spec.ts
  3  |  * @testId SETL-TBL-TC1
  4  |  * @feature Settlements — Table Columns
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * Verifies that all six column headers (Service Name, Gross Collected, Fees,
  8  |  * Net Settlement, Last Settlement, Status) are visible in the Settlement by Bank table.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettlementsPage } from '../../pages/SettlementsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settlements — Table Columns', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — All Settlement by Bank table columns are visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies all 6 column headers are present in the Settlement by Bank table.');
  22 |     await allure.label('feature',  'Settlements');
  23 |     await allure.label('story',    'Table Columns');
  24 |     await allure.label('severity', 'blocker');
  25 |     await allure.label('priority', 'P0');
  26 |     await allure.label('testId',   'SETL-TBL-TC1');
  27 | 
  28 |     await logger.step('Step 1 — Verifies all 6 column headers are present in the Settlement by Bank table', async () => {
  29 |       logger.info('Test scope: Verifies all 6 column headers are present in the Settlement by Bank table');
  30 |     });
  31 | 
  32 |     const settlementsPage = new SettlementsPage(page);
  33 | 
  34 |     await logger.step('Step 2 — Navigate to Settlements page', async () => {
  35 |       logger.info('Opening the Settlements page');
  36 |       await settlementsPage.goto();
  37 |       logger.pass('Navigation to Settlements page initiated successfully');
  38 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  39 |     });
  40 | 
  41 |     await logger.step('Step 3 — Verify page loads successfully', async () => {
  42 |       logger.info('Waiting for all Settlements page elements to render');
  43 |       await settlementsPage.assertPageLoaded();
  44 |       logger.pass('Settlements page is fully loaded with all required elements');
  45 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  46 |     });
  47 | 
  48 |     await logger.step('Step 4 — "Service Name" column is visible', async () => {
  49 |       logger.info('Verifying table structure and data are visible');
> 50 |       await expect(settlementsPage.tableColumnServiceName).toBeVisible();
     |                                                            ^ Error: expect(locator).toBeVisible() failed
  51 |       logger.pass('Table rows and columns rendered correctly');
  52 |       await attachScreenshot(page, testInfo, '03 — Service Name column visible', settlementsPage.tableColumnServiceName);
  53 |     });
  54 | 
  55 |     await logger.step('Step 5 — "Gross Collected" column is visible', async () => {
  56 |       logger.info('Verifying table structure and data are visible');
  57 |       await expect(settlementsPage.tableColumnGrossCollected).toBeVisible();
  58 |       logger.pass('Table rows and columns rendered correctly');
  59 |       await attachScreenshot(page, testInfo, '04 — Gross Collected column visible', settlementsPage.tableColumnGrossCollected);
  60 |     });
  61 | 
  62 |     await logger.step('Step 6 — "Fees" column is visible', async () => {
  63 |       logger.info('Verifying table structure and data are visible');
  64 |       await expect(settlementsPage.tableColumnFees).toBeVisible();
  65 |       logger.pass('Table rows and columns rendered correctly');
  66 |       await attachScreenshot(page, testInfo, '05 — Fees column visible', settlementsPage.tableColumnFees);
  67 |     });
  68 | 
  69 |     await logger.step('Step 7 — "Net Settlement" column is visible', async () => {
  70 |       logger.info('Verifying table structure and data are visible');
  71 |       await expect(settlementsPage.tableColumnNetSettlement).toBeVisible();
  72 |       logger.pass('Table rows and columns rendered correctly');
  73 |       await attachScreenshot(page, testInfo, '06 — Net Settlement column visible', settlementsPage.tableColumnNetSettlement);
  74 |     });
  75 | 
  76 |     await logger.step('Step 8 — "Last Settlement" column is visible', async () => {
  77 |       logger.info('Verifying table structure and data are visible');
  78 |       await expect(settlementsPage.tableColumnLastSettlement).toBeVisible();
  79 |       logger.pass('Table rows and columns rendered correctly');
  80 |       await attachScreenshot(page, testInfo, '07 — Last Settlement column visible', settlementsPage.tableColumnLastSettlement);
  81 |     });
  82 | 
  83 |     await logger.step('Step 9 — "Status" column is visible', async () => {
  84 |       logger.info('Verifying table structure and data are visible');
  85 |       await expect(settlementsPage.tableColumnStatus).toBeVisible();
  86 |       logger.pass('Table rows and columns rendered correctly');
  87 |       await attachScreenshot(page, testInfo, '08 — Status column visible', settlementsPage.tableColumnStatus);
  88 |     });
  89 |   });
  90 | });
  91 | 
```