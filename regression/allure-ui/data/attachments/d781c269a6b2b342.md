# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-hdr-tc1-heading-visible.spec.ts >> Settlements — Page Heading >> TC1 — "Settlements" heading and count are visible
- Location: tests/UI/Settlements/setl-hdr-tc1-heading-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/settlements have been initiated/i)
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByText(/settlements have been initiated/i)

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
    - link "Administrator":
      - /url: /administrator
      - img
      - text: Administrator
    - link "Settlements":
      - /url: /settlements
      - img
      - text: Settlements
    - button "Expand submenu":
      - img
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
- banner: A Ashil ADMIN PLATFORM
- main:
  - heading "Settlements" [level=2]
  - paragraph: 0 settlements, data are showing based on today
  - textbox "Search Batch name..."
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - combobox:
    - option "All Status" [selected]
    - option "Settled"
    - option "Payout Initiated"
    - option "Partial"
    - option "Payout Completed"
    - option "Failed"
  - combobox:
    - option "All Currency" [selected]
    - option "KES"
    - option "USD"
  - img
  - heading "0" [level=2]
  - paragraph: Total Batches Count
  - img
  - text: 0 0 —
  - img
  - text: 0 0 —
  - img
  - heading "0" [level=2]
  - paragraph: Settlements Count
  - img
  - text: 0 0 —
  - img
  - text: 0 0 —
  - img
  - heading "0 0" [level=2]
  - paragraph: Total Transactions Count
  - img
  - text: 0 KES 0.00 0 —
  - img
  - text: 0 USD 0.00 0 —
  - img
  - heading "0 0" [level=2]
  - paragraph: Total Settlement Amount
  - img
  - text: KES 0 KES 0.00 0 —
  - img
  - text: USD 0 USD 0.00 0 —
  - img
  - heading "0" [level=2]
  - paragraph: Batch Status
  - paragraph: Settlement
  - table:
    - rowgroup:
      - row "Batch Name Settlement Date Start Time End Time Currency Net Amount Status":
        - columnheader "Batch Name"
        - columnheader "Settlement Date"
        - columnheader "Start Time"
        - columnheader "End Time"
        - columnheader "Currency"
        - columnheader "Net Amount"
        - columnheader "Status"
    - rowgroup:
      - row "No Data Found":
        - cell "No Data Found"
  - heading "Settlement Details" [level=3]
  - paragraph: select the settlement data
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
  28 |     await logger.step('Step 1 — Verifies the Settlements heading and "X settlements have been initiated" text render on page load', async () => {
  29 |       logger.info('Test scope: Verifies the Settlements heading and "X settlements have been initiated" text render on page load');
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
  48 |     await logger.step('Step 4 — "Settlements" heading is visible', async () => {
  49 |       logger.info('Asserting visibility: Settlements page heading');
  50 |       await expect(settlementsPage.heading).toBeVisible();
  51 |       logger.pass('Settlements page heading is visible on the page');
  52 |       await attachScreenshot(page, testInfo, '03 — Settlement heading visible', settlementsPage.heading);
  53 |     });
  54 | 
  55 |     await logger.step('Step 5 — Settlement initiated count is visible', async () => {
  56 |       logger.info('Asserting visibility: Settlement initiated count sub-heading');
> 57 |       await expect(settlementsPage.settlementSubCount).toBeVisible();
     |                                                        ^ Error: expect(locator).toBeVisible() failed
  58 |       logger.pass('Settlement initiated count sub-heading is visible on the page');
  59 |       await attachScreenshot(page, testInfo, '04 — Settlement initiated count visible', settlementsPage.settlementSubCount);
  60 |     });
  61 |   });
  62 | });
  63 | 
```