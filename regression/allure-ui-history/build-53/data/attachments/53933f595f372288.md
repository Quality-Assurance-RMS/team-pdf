# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-badge-tc1-settled-status-badge.spec.ts >> Settlements — Settled Status Badge >> TC1 — "Settled" status badge is visible in the table
- Location: tests/UI/Settlements/setl-badge-tc1-settled-status-badge.spec.ts:20:7

# Error details

```
Error: locator.selectOption: Error: strict mode violation: getByRole('combobox').or(locator('[class*="dropdown"], select').filter({ hasText: /Last 7 Days/i }).first()) resolved to 3 elements:
    1) <select class="flex w-full sm:w-38 items-center gap-2 px-5 py-2↵                rounded-lg border transition-all hover:bg-gray-50↵                focus:outline-none focus:ring-0.8 focus:ring-[#E2E8F0]↵                border-[#E2E8F0] bg-[#F8FAFC] hover:cursor-pointer">…</select> aka getByRole('combobox').first()
    2) <select class="flex w-full sm:w-38 items-center gap-2 px-5 py-2↵                rounded-lg border transition-all hover:bg-gray-50↵                focus:outline-none focus:ring-0.8 focus:ring-[#E2E8F0]↵                border-[#E2E8F0] bg-[#F8FAFC] hover:cursor-pointer">…</select> aka getByRole('combobox').nth(1)
    3) <select class="flex w-full sm:w-38 items-center gap-2 px-5 py-2↵                rounded-lg border transition-all hover:bg-gray-50↵                focus:outline-none focus:ring-0.8 focus:ring-[#E2E8F0]↵                border-[#E2E8F0] bg-[#F8FAFC] hover:cursor-pointer">…</select> aka getByRole('combobox').nth(2)

Call log:
  - waiting for getByRole('combobox').or(locator('[class*="dropdown"], select').filter({ hasText: /Last 7 Days/i }).first())

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
          - generic [ref=e43]: A
          - generic [ref=e45]:
            - generic [ref=e46]: Ashil
            - generic [ref=e47]:
              - generic [ref=e48]: ADMIN
              - generic [ref=e49]: PLATFORM
      - main [ref=e51]:
        - generic [ref=e52]:
          - generic [ref=e53]:
            - generic [ref=e55]:
              - heading "Settlements" [level=2] [ref=e56]
              - paragraph [ref=e57]: 0 settlements, data are showing based on last-7-days
            - generic [ref=e58]:
              - textbox "Search Batch name..." [ref=e59]
              - combobox [ref=e60]:
                - option "Today"
                - option "Last 7 Days" [selected]
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
              - combobox [ref=e61]:
                - option "Status" [selected]
                - option "Settlement"
                - option "Payout Initiated"
                - option "Payout Completed"
                - option "Cancelled"
              - combobox [ref=e62]:
                - option "Currency" [selected]
                - option "KES"
                - option "USD"
          - generic [ref=e63]:
            - generic [ref=e64]:
              - generic [ref=e65]:
                - img [ref=e67]
                - generic [ref=e68]:
                  - heading "239" [level=2] [ref=e69]
                  - paragraph [ref=e70]: Total Batches Count
              - generic [ref=e72]:
                - generic [ref=e73]:
                  - generic [ref=e74]:
                    - img [ref=e75]
                    - generic [ref=e83]: "152"
                  - generic [ref=e84]: 0.0 —
                - generic [ref=e85]:
                  - generic [ref=e86]:
                    - img [ref=e87]
                    - generic [ref=e97]: "87"
                  - generic [ref=e98]: 0.0 —
            - generic [ref=e99]:
              - generic [ref=e100]:
                - img [ref=e102]
                - generic [ref=e107]:
                  - heading "73" [level=2] [ref=e108]
                  - paragraph [ref=e109]: Settlements Count
              - generic [ref=e111]:
                - generic [ref=e112]:
                  - generic [ref=e113]:
                    - img [ref=e114]
                    - generic [ref=e122]: "45"
                  - generic [ref=e123]: 0.0 —
                - generic [ref=e124]:
                  - generic [ref=e125]:
                    - img [ref=e126]
                    - generic [ref=e136]: "28"
                  - generic [ref=e137]: 0.0 —
            - generic [ref=e138]:
              - generic [ref=e139]:
                - img [ref=e141]
                - generic [ref=e144]:
                  - heading "25.3K 25,311" [level=2] [ref=e145]:
                    - generic [ref=e146]:
                      - generic [ref=e147]: 25.3K
                      - generic: 25,311
                  - paragraph [ref=e148]: Total Transactions Amount
              - generic [ref=e150]:
                - generic [ref=e151]:
                  - generic [ref=e152]:
                    - img [ref=e153]
                    - generic [ref=e162]:
                      - generic [ref=e163]: 18.5K
                      - generic: KES 18,542.00
                  - generic [ref=e164]:
                    - text: + 8.2%
                    - img [ref=e165]
                - generic [ref=e167]:
                  - generic [ref=e168]:
                    - img [ref=e169]
                    - generic [ref=e180]:
                      - generic [ref=e181]: 6,769
                      - generic: USD 6,769.00
                  - generic [ref=e182]:
                    - text: 1.2%
                    - img [ref=e183]
            - generic [ref=e185]:
              - generic [ref=e186]:
                - img [ref=e188]
                - generic [ref=e191]:
                  - heading "14.3M 140.8M" [level=2] [ref=e192]:
                    - generic [ref=e193]:
                      - generic [ref=e194]: 14.3M
                      - generic: 140.8M
                  - paragraph [ref=e195]: Total Settlement Amount
              - generic [ref=e197]:
                - generic [ref=e198]:
                  - generic [ref=e199]:
                    - img [ref=e200]
                    - generic [ref=e209]:
                      - generic [ref=e210]: KES 248.8K
                      - generic: KES 248,800.00
                  - generic [ref=e211]:
                    - text: + 8.2%
                    - img [ref=e212]
                - generic [ref=e214]:
                  - generic [ref=e215]:
                    - img [ref=e216]
                    - generic [ref=e227]:
                      - generic [ref=e228]: USD 884.8K
                      - generic: USD 884,800.00
                  - generic [ref=e229]:
                    - text: + 8.2%
                    - img [ref=e230]
            - generic:
              - generic:
                - img [ref=e233]
                - generic:
                  - heading "29" [level=2]
                  - paragraph: Batch Status
              - generic:
                - generic:
                  - generic [ref=e234]: "9"
                  - generic [ref=e235]: Settled
                - generic:
                  - generic [ref=e236]: "20"
                  - generic [ref=e237]: Payout Completed
          - generic [ref=e238]:
            - paragraph [ref=e239]: Settlement by Bank
            - generic [ref=e240]:
              - table [ref=e243]:
                - rowgroup [ref=e244]:
                  - row "Batch Name Settlement Date Start Time End Time Currency Net Amount Status" [ref=e245]:
                    - columnheader "Batch Name" [ref=e246]
                    - columnheader "Settlement Date" [ref=e247]
                    - columnheader "Start Time" [ref=e248]
                    - columnheader "End Time" [ref=e249]
                    - columnheader "Currency" [ref=e250]
                    - columnheader "Net Amount" [ref=e251]
                    - columnheader "Status" [ref=e252]
                - rowgroup [ref=e253]:
                  - row "No Data Found" [ref=e254]:
                    - cell "No Data Found" [ref=e255]
              - generic [ref=e256]:
                - heading "Settlement Details" [level=3] [ref=e257]
                - paragraph [ref=e258]: select the settlement data
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | import { Page, Locator, expect } from '@playwright/test';
  2  | 
  3  | export class SettlementsPage {
  4  |   readonly page: Page;
  5  | 
  6  |   readonly heading: Locator;
  7  |   readonly settlementSubCount: Locator;
  8  |   readonly dateFilter: Locator;
  9  | 
  10 |   // Summary cards
  11 |   readonly grossCollectedCard: Locator;
  12 |   readonly totalFeesCard: Locator;
  13 |   readonly netSettlementCard: Locator;
  14 |   readonly lastSettlementCard: Locator;
  15 | 
  16 |   // Settlement by Bank section
  17 |   readonly settlementByBankHeading: Locator;
  18 |   readonly tableColumnServiceName: Locator;
  19 |   readonly tableColumnGrossCollected: Locator;
  20 |   readonly tableColumnFees: Locator;
  21 |   readonly tableColumnNetSettlement: Locator;
  22 |   readonly tableColumnLastSettlement: Locator;
  23 |   readonly tableColumnStatus: Locator;
  24 |   readonly tableRows: Locator;
  25 | 
  26 |   // Selected Bank Details panel
  27 |   readonly selectedBankDetailsPanel: Locator;
  28 | 
  29 |   // Status badges
  30 |   readonly settledBadge: Locator;
  31 | 
  32 |   constructor(page: Page) {
  33 |     this.page = page;
  34 | 
  35 |     // NOTE: previously combined with .or(page.getByText('Settlements').first()) as a
  36 |     // "fallback" — but Playwright's .or() returns the UNION of both matches, not a
  37 |     // fallback. Since the sidebar nav link is also literally text "Settlements",
  38 |     // that union always resolved to 2 elements (heading + nav link), causing a
  39 |     // strict-mode violation. The heading-role/tag filter alone is sufficient.
  40 |     this.heading             = page.locator('h1, h2, h3').filter({ hasText: /^Settlements$/i });
  41 |     this.settlementSubCount  = page.getByText(/settlements have been initiated/i);
  42 |     this.dateFilter          = page.getByRole('combobox').or(
  43 |       page.locator('[class*="dropdown"], select').filter({ hasText: /Last 7 Days/i }).first()
  44 |     );
  45 | 
  46 |     this.grossCollectedCard  = page.getByText(/Gross Collected/i).first();
  47 |     this.totalFeesCard       = page.getByText(/Total Fees/i).first();
  48 |     this.netSettlementCard   = page.getByText(/Net Settlement/i).first();
  49 |     this.lastSettlementCard  = page.getByText(/Last Settlement/i).first();
  50 | 
  51 |     this.settlementByBankHeading  = page.getByText(/Settlement by Bank/i);
  52 |     this.tableColumnServiceName    = page.getByRole('columnheader', { name: /Service Name/i });
  53 |     this.tableColumnGrossCollected = page.getByRole('columnheader', { name: /Gross Collected/i });
  54 |     this.tableColumnFees           = page.getByRole('columnheader', { name: /Fees/i });
  55 |     this.tableColumnNetSettlement  = page.getByRole('columnheader', { name: /Net Settlement/i });
  56 |     this.tableColumnLastSettlement = page.getByRole('columnheader', { name: /Last Settlement/i });
  57 |     this.tableColumnStatus         = page.getByRole('columnheader', { name: /Status/i });
  58 |     this.tableRows                 = page.locator('table tbody tr');
  59 | 
  60 |     this.selectedBankDetailsPanel = page.getByText(/Selected Bank Details/i);
  61 |     this.settledBadge             = page.locator('table tbody').getByText(/^Settled$/i).first();
  62 |   }
  63 | 
  64 |   async goto() {
  65 |     await this.page.goto('/settlements');
  66 |     await this.page.waitForLoadState('networkidle');
  67 |   }
  68 | 
  69 |   async assertPageLoaded() {
  70 |     await expect(this.page).toHaveURL(/\/settlements/, { timeout: 15000 });
  71 |     await expect(this.page.getByText('Settlements').first()).toBeVisible({ timeout: 10000 });
  72 |   }
  73 | 
  74 |   // The default date filter ("Last 7 Days") can have zero settlements on this
  75 |   // demo/dev environment since seed data ages past that window over time.
  76 |   // Tests that need real row data should call this first to widen the range.
  77 |   async selectDateRange(label: string) {
> 78 |     await this.dateFilter.selectOption({ label });
     |                           ^ Error: locator.selectOption: Error: strict mode violation: getByRole('combobox').or(locator('[class*="dropdown"], select').filter({ hasText: /Last 7 Days/i }).first()) resolved to 3 elements:
  79 |     await this.page.waitForLoadState('networkidle');
  80 |   }
  81 | }
  82 | 
```