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
        - link "Dashboard" [ref=e12] [cursor=pointer]:
          - /url: /dashboard
          - img [ref=e13]
          - generic [ref=e15]: Dashboard
        - link "Transactions" [ref=e18] [cursor=pointer]:
          - /url: /transaction
          - img [ref=e19]
          - generic [ref=e21]: Transactions
        - generic [ref=e23]:
          - link "Settlements" [ref=e24] [cursor=pointer]:
            - /url: /settlements
            - img [ref=e25]
            - generic [ref=e27]: Settlements
          - button "Expand submenu" [ref=e28]:
            - img [ref=e29]
        - link "Settings" [ref=e33] [cursor=pointer]:
          - /url: /settings
          - img [ref=e34]
          - generic [ref=e36]: Settings
      - button "Logout" [ref=e38] [cursor=pointer]:
        - img [ref=e39]
        - generic [ref=e41]: Logout
      - button "Collapse sidebar" [ref=e42]:
        - img [ref=e43]
    - generic [ref=e45]:
      - banner [ref=e46]:
        - generic [ref=e47]:
          - generic [ref=e50]: A
          - generic [ref=e52]:
            - generic [ref=e53]: Ashil
            - generic [ref=e54]:
              - generic [ref=e55]: ADMIN
              - generic [ref=e56]: PLATFORM
      - main [ref=e58]:
        - generic [ref=e59]:
          - generic [ref=e60]:
            - generic [ref=e62]:
              - heading "Settlements" [level=2] [ref=e63]
              - paragraph [ref=e64]: 0 settlements, data are showing based on last-7-days
            - generic [ref=e65]:
              - textbox "Search Batch name..." [ref=e66]
              - combobox [ref=e67]:
                - option "Today"
                - option "Last 7 Days" [selected]
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
              - combobox [ref=e68]:
                - option "Status" [selected]
                - option "Settlement"
                - option "Payout Initiated"
                - option "Payout Completed"
                - option "Cancelled"
              - combobox [ref=e69]:
                - option "Currency" [selected]
                - option "KES"
                - option "USD"
          - generic [ref=e70]:
            - generic [ref=e71]:
              - generic [ref=e72]:
                - img [ref=e74]
                - generic [ref=e75]:
                  - heading "239" [level=2] [ref=e76]
                  - paragraph [ref=e77]: Total Batches Count
              - generic [ref=e79]:
                - generic [ref=e80]:
                  - generic [ref=e81]:
                    - img [ref=e82]
                    - generic [ref=e90]: "152"
                  - generic [ref=e91]: 0.0 —
                - generic [ref=e92]:
                  - generic [ref=e93]:
                    - img [ref=e94]
                    - generic [ref=e104]: "87"
                  - generic [ref=e105]: 0.0 —
            - generic [ref=e106]:
              - generic [ref=e107]:
                - img [ref=e109]
                - generic [ref=e114]:
                  - heading "73" [level=2] [ref=e115]
                  - paragraph [ref=e116]: Settlements Count
              - generic [ref=e118]:
                - generic [ref=e119]:
                  - generic [ref=e120]:
                    - img [ref=e121]
                    - generic [ref=e129]: "45"
                  - generic [ref=e130]: 0.0 —
                - generic [ref=e131]:
                  - generic [ref=e132]:
                    - img [ref=e133]
                    - generic [ref=e143]: "28"
                  - generic [ref=e144]: 0.0 —
            - generic [ref=e145]:
              - generic [ref=e146]:
                - img [ref=e148]
                - generic [ref=e151]:
                  - heading "25.3K 25,311" [level=2] [ref=e152]:
                    - generic [ref=e153]:
                      - generic [ref=e154]: 25.3K
                      - generic: 25,311
                  - paragraph [ref=e155]: Total Transactions Amount
              - generic [ref=e157]:
                - generic [ref=e158]:
                  - generic [ref=e159]:
                    - img [ref=e160]
                    - generic [ref=e169]:
                      - generic [ref=e170]: 18.5K
                      - generic: KES 18,542.00
                  - generic [ref=e171]:
                    - text: + 8.2%
                    - img [ref=e172]
                - generic [ref=e174]:
                  - generic [ref=e175]:
                    - img [ref=e176]
                    - generic [ref=e187]:
                      - generic [ref=e188]: 6,769
                      - generic: USD 6,769.00
                  - generic [ref=e189]:
                    - text: 1.2%
                    - img [ref=e190]
            - generic [ref=e192]:
              - generic [ref=e193]:
                - img [ref=e195]
                - generic [ref=e198]:
                  - heading "14.3M 140.8M" [level=2] [ref=e199]:
                    - generic [ref=e200]:
                      - generic [ref=e201]: 14.3M
                      - generic: 140.8M
                  - paragraph [ref=e202]: Total Settlement Amount
              - generic [ref=e204]:
                - generic [ref=e205]:
                  - generic [ref=e206]:
                    - img [ref=e207]
                    - generic [ref=e216]:
                      - generic [ref=e217]: KES 248.8K
                      - generic: KES 248,800.00
                  - generic [ref=e218]:
                    - text: + 8.2%
                    - img [ref=e219]
                - generic [ref=e221]:
                  - generic [ref=e222]:
                    - img [ref=e223]
                    - generic [ref=e234]:
                      - generic [ref=e235]: USD 884.8K
                      - generic: USD 884,800.00
                  - generic [ref=e236]:
                    - text: + 8.2%
                    - img [ref=e237]
            - generic:
              - generic:
                - img [ref=e240]
                - generic:
                  - heading "29" [level=2]
                  - paragraph: Batch Status
              - generic:
                - generic:
                  - generic [ref=e241]: "9"
                  - generic [ref=e242]: Settled
                - generic:
                  - generic [ref=e243]: "20"
                  - generic [ref=e244]: Payout Completed
          - generic [ref=e245]: Failed to load settlements. Please try again.
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