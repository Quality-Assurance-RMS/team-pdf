# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Transactions/method-filter-dropdown.spec.ts >> Transactions — Method Filter Dropdown >> TC3 — Method filter dropdown is visible
- Location: tests/UI/Transactions/method-filter-dropdown.spec.ts:19:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: locator('select').filter({ hasText: /All Methods/i }).first()
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for locator('select').filter({ hasText: /All Methods/i }).first()

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
  2  |  * @file method-filter-dropdown.spec.ts
  3  |  * @testId TXN-FLT-TC3
  4  |  * @feature Transactions — Method Filter
  5  |  * @priority P1 / Critical
  6  |  *
  7  |  * Verifies that the payment Method filter dropdown is visible on the Transactions page.
  8  |  */
  9  | 
  10 | import { test, expect } from '../fixtures';
  11 | import type { TestInfo } from '@playwright/test';
  12 | import { allure } from 'allure-playwright';
  13 | import { TransactionPage } from '../../pages/TransactionPage';
  14 | import { attachScreenshot } from '../../utils/screenshotHelper';
  15 | 
  16 | test.describe('Transactions — Method Filter Dropdown', () => {
  17 |   test.setTimeout(120000);
  18 | 
  19 |   test('TC3 — Method filter dropdown is visible', async ({ page, logger }, testInfo: TestInfo) => {
  20 |     await allure.description('Verifies the "All Methods" filter dropdown is rendered on the Transactions page.');
  21 |     await allure.label('feature',  'Transactions');
  22 |     await allure.label('story',    'Method Filter');
  23 |     await allure.label('severity', 'critical');
  24 |     await allure.label('priority', 'P1');
  25 |     await allure.label('testId',   'TXN-FLT-TC3');
  26 | 
  27 |     const txnPage = new TransactionPage(page);
  28 | 
  29 |     await logger.step('Step 1 — Navigate to Transactions page', async () => {
  30 |       logger.info('Opening the Transactions page');
  31 |       await txnPage.goto();
  32 |       logger.pass('Navigation to Transactions page initiated successfully');
  33 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  34 |     });
  35 | 
  36 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  37 |       logger.info('Waiting for all Transactions page elements to render');
  38 |       await txnPage.assertPageLoaded();
  39 |       logger.pass('Transactions page is fully loaded with all required elements');
  40 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  41 |     });
  42 | 
  43 |     await logger.step('Step 3 — Method filter showing "All Methods" is visible', async () => {
  44 |       // Target <select> elements specifically (avoids matching <option> children in strict mode)
  45 |       logger.info('Checking filter dropdown is interactive and visible');
  46 |       await expect(
  47 |         page.locator('select').filter({ hasText: /All Methods/i }).first()
> 48 |       ).toBeVisible();
     |         ^ Error: expect(locator).toBeVisible() failed
  49 |       logger.pass('Filter dropdown is visible and accessible');
  50 |       await attachScreenshot(page, testInfo, '03 — Method filter All Methods visible', page.locator('select').filter({ hasText: /All Methods/i }).first());
  51 |     });
  52 |   });
  53 | });
  54 | 
```