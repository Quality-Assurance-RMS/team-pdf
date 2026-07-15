# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-card-tc1-summary-cards-visible.spec.ts >> Settlements — Summary Cards >> TC1 — All four summary cards are visible
- Location: tests/UI/Settlements/setl-card-tc1-summary-cards-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/Gross Collected/i).first()
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByText(/Gross Collected/i).first()

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
    - option "Settlement"
    - option "Payout Initiated"
    - option "Payout Completed"
    - option "Cancelled"
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
  2  |  * @file summary-cards-visible.spec.ts
  3  |  * @testId SETL-CARD-TC1
  4  |  * @feature Settlements — Summary Cards
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * Verifies all four summary metric cards (Gross Collected, Total Fees,
  8  |  * Net Settlement, Last Settlement) are visible on the Settlements page.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettlementsPage } from '../../pages/SettlementsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settlements — Summary Cards', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — All four summary cards are visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies Gross Collected, Total Fees, Net Settlement, and Last Settlement cards render on the Settlements page.');
  22 |     await allure.label('feature',  'Settlements');
  23 |     await allure.label('story',    'Summary Cards');
  24 |     await allure.label('severity', 'blocker');
  25 |     await allure.label('priority', 'P0');
  26 |     await allure.label('testId',   'SETL-CARD-TC1');
  27 | 
  28 |     await logger.step('Step 1 — Verifies Gross Collected, Total Fees, Net Settlement, and Last Settlement cards render on the Settlements page', async () => {
  29 |       logger.info('Test scope: Verifies Gross Collected, Total Fees, Net Settlement, and Last Settlement cards render on the Settlements page');
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
  48 |     await logger.step('Step 4 — "Gross Collected" card is visible', async () => {
  49 |       logger.info('Asserting visibility: Gross Collected summary card');
> 50 |       await expect(settlementsPage.grossCollectedCard).toBeVisible();
     |                                                        ^ Error: expect(locator).toBeVisible() failed
  51 |       logger.pass('Gross Collected summary card is visible on the page');
  52 |       await attachScreenshot(page, testInfo, '03 — Gross Collected card visible', settlementsPage.grossCollectedCard);
  53 |     });
  54 | 
  55 |     await logger.step('Step 5 — "Total Fees" card is visible', async () => {
  56 |       logger.info('Asserting visibility: Total Fees summary card');
  57 |       await expect(settlementsPage.totalFeesCard).toBeVisible();
  58 |       logger.pass('Total Fees summary card is visible on the page');
  59 |       await attachScreenshot(page, testInfo, '04 — Total Fees card visible', settlementsPage.totalFeesCard);
  60 |     });
  61 | 
  62 |     await logger.step('Step 6 — "Net Settlement" card is visible', async () => {
  63 |       logger.info('Asserting visibility: Net Settlement summary card');
  64 |       await expect(settlementsPage.netSettlementCard).toBeVisible();
  65 |       logger.pass('Net Settlement summary card is visible on the page');
  66 |       await attachScreenshot(page, testInfo, '05 — Net Settlement card visible', settlementsPage.netSettlementCard);
  67 |     });
  68 | 
  69 |     await logger.step('Step 7 — "Last Settlement" card is visible', async () => {
  70 |       logger.info('Asserting visibility: Last Settlement summary card');
  71 |       await expect(settlementsPage.lastSettlementCard).toBeVisible();
  72 |       logger.pass('Last Settlement summary card is visible on the page');
  73 |       await attachScreenshot(page, testInfo, '06 — Last Settlement card visible', settlementsPage.lastSettlementCard);
  74 |     });
  75 |   });
  76 | });
  77 | 
```