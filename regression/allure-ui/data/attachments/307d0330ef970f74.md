# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Transactions/success-status-badge.spec.ts >> Transactions — Success Status Badge >> TC1 — "Success" status badge is rendered in the table
- Location: tests/UI/Transactions/success-status-badge.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: locator('table tbody').getByText(/^Success$/i).first()
Expected: visible
Timeout: 15000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 15000ms
  - waiting for locator('table tbody').getByText(/^Success$/i).first()

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
  - combobox:
    - option "All Status" [selected]
    - option "Failed"
    - option "Pending"
    - option "Success"
  - combobox:
    - option "All Methods" [selected]
    - option "MOBILE_MONEY"
    - option "CARD"
    - option "CARD_PAYMENT"
  - img
  - paragraph: Total Transactions Amount
  - separator
  - img
  - text: KES 0 KES 0.00 0 —
  - img
  - text: USD 0 USD 0.00 0 —
  - img
  - paragraph: Success Transactions Count
  - separator
  - img
  - text: 0 0 —
  - img
  - text: 0 0 —
  - img
  - paragraph: Failure Transaction Count
  - separator
  - img
  - text: 0 0 —
  - img
  - text: 0 0 —
  - table:
    - rowgroup:
      - row "Transaction ID Invoice No Date MCDA Department Method Currency Amount Status Action":
        - columnheader "Transaction ID"
        - columnheader "Invoice No"
        - columnheader "Date"
        - columnheader "MCDA"
        - columnheader "Department"
        - columnheader "Method"
        - columnheader "Currency"
        - columnheader "Amount"
        - columnheader "Status"
        - columnheader "Action"
    - rowgroup:
      - row "No Data Found":
        - cell "No Data Found"
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file success-status-badge.spec.ts
  3  |  * @testId TXN-BADGE-TC1
  4  |  * @feature Transactions — Status Badge
  5  |  * @priority P2 / Normal
  6  |  *
  7  |  * Verifies that at least one "Success" status badge is visible in the
  8  |  * transactions table when viewing today's data.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { TransactionPage } from '../../pages/TransactionPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Transactions — Success Status Badge', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — "Success" status badge is rendered in the table', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies that at least one Success badge is visible in the transactions table.');
  22 |     await allure.label('feature',  'Transactions');
  23 |     await allure.label('story',    'Status Badge');
  24 |     await allure.label('severity', 'normal');
  25 |     await allure.label('priority', 'P2');
  26 |     await allure.label('testId',   'TXN-BADGE-TC1');
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
  44 |     await logger.step('Step 3 — At least one "Success" badge is visible', async () => {
  45 |       logger.info('Asserting visibility: Success status badge');
  46 |       await expect(
  47 |         page.locator('table tbody').getByText(/^Success$/i).first()
> 48 |       ).toBeVisible({ timeout: 15000 });
     |         ^ Error: expect(locator).toBeVisible() failed
  49 |       logger.pass('Success status badge is visible');
  50 |       await attachScreenshot(page, testInfo, '03 — Success badge visible', page.locator('table tbody').getByText(/^Success$/i).first());
  51 |     });
  52 |   });
  53 | });
  54 | 
```