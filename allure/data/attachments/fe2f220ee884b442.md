# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Transactions/table-columns-visible.spec.ts >> Transactions — Table Columns >> TC1 — All table column headers are visible
- Location: tests/UI/Transactions/table-columns-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByRole('columnheader', { name: /Transaction ID/i })
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByRole('columnheader', { name: /Transaction ID/i })

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
  - heading "Transactions" [level=2]
  - paragraph: 0 transactions
  - textbox "Search Transaction id..."
  - img
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file table-columns-visible.spec.ts
  3  |  * @testId TXN-TBL-TC1
  4  |  * @feature Transactions — Table
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * Verifies that all required column headers are rendered in the Transactions table.
  8  |  * Columns: Transaction ID, Invoice No, Date, MCDA, Department, Method, Currency, Amount, Status, Action
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { TransactionPage } from '../../pages/TransactionPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Transactions — Table Columns', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — All table column headers are visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies that Transaction ID, Invoice No, Date, MCDA, Department, Method, Currency, Amount, Status, and Action columns are present.');
  22 |     await allure.label('feature',  'Transactions');
  23 |     await allure.label('story',    'Table');
  24 |     await allure.label('severity', 'blocker');
  25 |     await allure.label('priority', 'P0');
  26 |     await allure.label('testId',   'TXN-TBL-TC1');
  27 | 
  28 |     const txnPage = new TransactionPage(page);
  29 | 
  30 |     await logger.step('Step 1 — Navigate to Transactions page', async () => {
  31 |       logger.info('Opening the Transactions page');
  32 |       await txnPage.goto();
  33 |       logger.pass('Navigation to Transactions page initiated successfully');
  34 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  35 |     });
  36 | 
  37 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  38 |       logger.info('Waiting for all Transactions page elements to render');
  39 |       await txnPage.assertPageLoaded();
  40 |       logger.pass('Transactions page is fully loaded with all required elements');
  41 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  42 |     });
  43 | 
  44 |     const expectedColumns = [
  45 |       'Transaction ID',
  46 |       'Invoice No',
  47 |       'Date',
  48 |       'MCDA',
  49 |       'Department',
  50 |       'Method',
  51 |       'Currency',
  52 |       'Amount',
  53 |       'Status',
  54 |       'Action',
  55 |     ];
  56 | 
  57 |     let stepIndex = 3;
  58 |     for (const col of expectedColumns) {
  59 |       const stepNum = String(stepIndex).padStart(2, '0');
  60 |       await logger.step(`Step ${stepIndex} — Column "${col}" header is visible`, async () => {
  61 |         logger.info(`Asserting visibility: ${col} column header`);
> 62 |         await expect(page.getByRole('columnheader', { name: new RegExp(col, 'i') })).toBeVisible();
     |                                                                                      ^ Error: expect(locator).toBeVisible() failed
  63 |         logger.pass(`${col} column header is visible`);
  64 |         await attachScreenshot(page, testInfo, `${stepNum} — Column ${col} header visible`);
  65 |       });
  66 |       stepIndex++;
  67 |     }
  68 |   });
  69 | });
  70 | 
```