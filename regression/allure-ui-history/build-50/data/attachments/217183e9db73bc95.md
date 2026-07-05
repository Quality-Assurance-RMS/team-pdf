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

Locator: getByRole('columnheader', { name: /Action/i })
Expected: visible
Error: strict mode violation: getByRole('columnheader', { name: /Action/i }) resolved to 2 elements:
    1) <th class="p-3 text-center font-normal">Transaction ID</th> aka getByRole('columnheader', { name: 'Transaction ID' })
    2) <th class="p-3 text-center font-normal">Action</th> aka getByRole('columnheader', { name: 'Action', exact: true })

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByRole('columnheader', { name: /Action/i })

```

# Page snapshot

```yaml
- generic [ref=e2]:
  - generic [ref=e3]:
    - complementary [ref=e4]:
      - img "eCitizen" [ref=e8] [cursor=pointer]
      - navigation [ref=e9]:
        - link "Dashboard" [ref=e10] [cursor=pointer]:
          - /url: /dashboard
          - img [ref=e11]
          - generic [ref=e13]: Dashboard
        - link "Transactions" [ref=e14] [cursor=pointer]:
          - /url: /transaction
          - img [ref=e15]
          - generic [ref=e17]: Transactions
        - link "Settlements" [ref=e18] [cursor=pointer]:
          - /url: /settlements
          - img [ref=e19]
          - generic [ref=e21]: Settlements
        - link "Reports" [ref=e22] [cursor=pointer]:
          - /url: /reports
          - img [ref=e23]
          - generic [ref=e25]: Reports
        - link "Settings" [ref=e26] [cursor=pointer]:
          - /url: /settings
          - img [ref=e27]
          - generic [ref=e29]: Settings
      - button "Logout" [ref=e31] [cursor=pointer]:
        - img [ref=e32]
        - generic [ref=e34]: Logout
      - button "Collapse sidebar" [ref=e35]:
        - img [ref=e36]
    - generic [ref=e38]:
      - banner [ref=e39]:
        - generic [ref=e40]:
          - button "Notifications" [ref=e41]:
            - img [ref=e42]
            - generic [ref=e45]: "3"
          - generic [ref=e48]: A
          - generic [ref=e50]:
            - generic [ref=e51]: Ashil
            - generic [ref=e52]:
              - generic [ref=e53]: ADMIN
              - generic [ref=e54]: PLATFORM
      - main [ref=e56]:
        - generic [ref=e58]:
          - generic [ref=e59]:
            - generic [ref=e61]:
              - heading "Transactions" [level=2] [ref=e62]
              - paragraph [ref=e63]: 0 transactions
            - generic [ref=e64]:
              - generic [ref=e65]:
                - textbox "Search Transaction id..." [ref=e66]
                - img [ref=e68]
              - combobox [ref=e70]:
                - option "Today" [selected]
                - option "Last 7 Days"
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
              - combobox [ref=e71]:
                - option "All Status" [selected]
                - option "Failed"
                - option "Pending"
                - option "Success"
              - combobox [ref=e72]:
                - option "All Methods" [selected]
                - option "MOBILE_MONEY"
                - option "CARD"
                - option "CARD_PAYMENT"
          - generic [ref=e75]:
            - generic [ref=e76]:
              - generic [ref=e77]:
                - img [ref=e79]
                - paragraph [ref=e82]: Total Transactions Amount
              - separator [ref=e83]
              - generic [ref=e84]:
                - generic [ref=e85]:
                  - generic [ref=e86]:
                    - img [ref=e87]
                    - generic [ref=e96]:
                      - generic [ref=e97]: KES 0
                      - generic: KES 0.00
                  - generic [ref=e98]: 0 —
                - generic [ref=e99]:
                  - generic [ref=e100]:
                    - img [ref=e101]
                    - generic [ref=e112]:
                      - generic [ref=e113]: USD 0
                      - generic: USD 0.00
                  - generic [ref=e114]: 0 —
            - generic [ref=e115]:
              - generic [ref=e116]:
                - img [ref=e118]
                - paragraph [ref=e122]: Success Transactions Count
              - separator [ref=e123]
              - generic [ref=e124]:
                - generic [ref=e125]:
                  - generic [ref=e126]:
                    - img [ref=e127]
                    - generic [ref=e135]: "0"
                  - generic [ref=e136]: 0 —
                - generic [ref=e137]:
                  - generic [ref=e138]:
                    - img [ref=e139]
                    - generic [ref=e149]: "0"
                  - generic [ref=e150]: 0 —
            - generic [ref=e151]:
              - generic [ref=e152]:
                - img [ref=e154]
                - paragraph [ref=e159]: Failure Transaction Count
              - separator [ref=e160]
              - generic [ref=e161]:
                - generic [ref=e162]:
                  - generic [ref=e163]:
                    - img [ref=e164]
                    - generic [ref=e172]: "0"
                  - generic [ref=e173]: 0 —
                - generic [ref=e174]:
                  - generic [ref=e175]:
                    - img [ref=e176]
                    - generic [ref=e186]: "0"
                  - generic [ref=e187]: 0 —
          - table [ref=e191]:
            - rowgroup [ref=e192]:
              - row "Transaction ID Invoice No Date MCDA Department Method Currency Amount Status Action" [ref=e193]:
                - columnheader "Transaction ID" [ref=e194]
                - columnheader "Invoice No" [ref=e195]
                - columnheader "Date" [ref=e196]
                - columnheader "MCDA" [ref=e197]
                - columnheader "Department" [ref=e198]
                - columnheader "Method" [ref=e199]
                - columnheader "Currency" [ref=e200]
                - columnheader "Amount" [ref=e201]
                - columnheader "Status" [ref=e202]
                - columnheader "Action" [ref=e203]
            - rowgroup [ref=e204]:
              - row "No Data Found" [ref=e205]:
                - cell "No Data Found" [ref=e206]
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