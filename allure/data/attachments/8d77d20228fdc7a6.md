# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-bank-tc1-settlement-by-bank-heading.spec.ts >> Settlements — Settlement by Bank Heading >> TC1 — "Settlement by Bank" heading is visible
- Location: tests/UI/Settlements/setl-bank-tc1-settlement-by-bank-heading.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/Settlement by Bank/i)
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByText(/Settlement by Bank/i)

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
  2  |  * @file settlement-by-bank-heading.spec.ts
  3  |  * @testId SETL-BANK-TC1
  4  |  * @feature Settlements — Settlement by Bank
  5  |  * @priority P1 / Critical
  6  |  *
  7  |  * Verifies that the "Settlement by Bank" section heading is visible below
  8  |  * the summary cards on the Settlements page.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettlementsPage } from '../../pages/SettlementsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settlements — Settlement by Bank Heading', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — "Settlement by Bank" heading is visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies the Settlement by Bank section heading renders on the Settlements page.');
  22 |     await allure.label('feature',  'Settlements');
  23 |     await allure.label('story',    'Settlement by Bank');
  24 |     await allure.label('severity', 'critical');
  25 |     await allure.label('priority', 'P1');
  26 |     await allure.label('testId',   'SETL-BANK-TC1');
  27 | 
  28 |     await logger.step('Step 1 — Verifies the Settlement by Bank section heading renders on the Settlements page', async () => {
  29 |       logger.info('Test scope: Verifies the Settlement by Bank section heading renders on the Settlements page');
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
  48 |     await logger.step('Step 4 — "Settlement by Bank" heading is visible', async () => {
  49 |       logger.info('Checking Settlement by Bank section panel is rendered');
> 50 |       await expect(settlementsPage.settlementByBankHeading).toBeVisible();
     |                                                             ^ Error: expect(locator).toBeVisible() failed
  51 |       logger.pass('Settlement by Bank section is visible and accessible');
  52 |       await attachScreenshot(page, testInfo, '03 — Settlement by Bank heading visible', settlementsPage.settlementByBankHeading);
  53 |     });
  54 |   });
  55 | });
  56 | 
```