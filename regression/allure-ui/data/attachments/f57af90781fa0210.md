# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Reports/rpt-badge-tc1-report-status-badges.spec.ts >> Reports — Conversion Ratio >> TC1 — Conversion Ratio is visible in the Settlement & Payout Overview
- Location: tests/UI/Reports/rpt-badge-tc1-report-status-badges.spec.ts:20:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/reports/
Received string:  "https://api.rms.dev.demo-fsit.com/dashboard"
Timeout: 15000ms

Call log:
  - Expect "toHaveURL" with timeout 15000ms
    26 × unexpected value "https://api.rms.dev.demo-fsit.com/dashboard"

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
  - heading "Hi, Ashil" [level=2]
  - paragraph: Here's what's happening with eCitizen today.
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - img
  - paragraph: Revenue generated
  - paragraph: Revenue across all ministries
  - button "View Details →"
  - paragraph: Total Revenue (KES) & (USD)
  - img
  - text: KES 0 KES 0.00 0.0 —
  - img
  - text: USD 0 USD 0.00 0.0 —
  - img
  - paragraph: No of Transactions
  - paragraph: 0 transactions
  - button "View Transactions →"
  - paragraph: Total Transactions (KES) & (USD)
  - img
  - text: 0 0.0 —
  - img
  - text: 0 0.0 —
  - img
  - paragraph: Total Transactions
  - paragraph: 0 Transactions
  - button "View Details →"
  - text: Successful Transactions 0.0 —
  - paragraph: 0%
  - paragraph: "0"
  - text: Failed Transactions 0.0 —
  - paragraph: 0%
  - paragraph: "0"
  - paragraph: Monthly Revenue for All Services
  - img
  - text: Showing last 12 months · auto-updates daily
  - button "KES"
  - button "USD"
  - text: KES 513.4M KES 4,810,992.50 Gross Amount KES 508.5M KES 508,547,995.00 Net Amount KES 4.8M KES 4,810,992.50 Commission
  - paragraph: 100%
  - text: Transaction Success Ratio Gross KES Net KES Commission KES
  - img: Aug Sep Oct Nov Dec Jan Feb Mar Apr May Jun Jul 0 200.… 400.… 600.…
  - heading "Gateway Payments" [level=2]
  - combobox:
    - option "Today" [selected]
    - option "Last 7 Days"
    - option "Last 30 Days"
    - option "Last 90 Days"
    - option "Custom Range"
  - paragraph: "0"
  - paragraph: Transactions (today)
  - paragraph: 0%
  - paragraph: Success Rate
  - paragraph: 0%
  - paragraph: Availability
  - paragraph: "0"
  - paragraph: P95 Latency
  - paragraph: 0%
  - paragraph: Timeout Rate
  - paragraph: Transactions by Payment Method
  - text: No Transaction Data Available There is no data to display for this period.
  - paragraph: Success vs Failure by Method
  - text: Success Failure No Success / Failure Data Available There is no data to display for this period.
  - paragraph: Share of Payment Methods
- region "Notifications alt+T"
```

# Test source

```ts
  1  | import { Page, Locator, expect } from '@playwright/test';
  2  | 
  3  | export class ReportsPage {
  4  |   readonly page: Page;
  5  | 
  6  |   readonly heading: Locator;
  7  |   readonly dateFilter: Locator;
  8  | 
  9  |   // Transaction status cards (new UI)
  10 |   readonly transactionsReceivedCard: Locator;
  11 |   readonly transactionReconCard: Locator;
  12 |   readonly transactionSettledCard: Locator;
  13 |   readonly payoutCard: Locator;
  14 |   readonly failedCard: Locator;
  15 | 
  16 |   // Settlement & Payout Overview section
  17 |   readonly settlementPayoutOverviewHeading: Locator;
  18 |   readonly settlementOverviewCanvas: Locator;
  19 |   readonly conversionRatio: Locator;
  20 | 
  21 |   // Legacy aliases kept for compatibility
  22 |   readonly totalRevenueCard: Locator;
  23 |   readonly totalTransactionsCard: Locator;
  24 |   readonly successfulPaymentsCard: Locator;
  25 |   readonly failedPaymentsCard: Locator;
  26 |   readonly revenueChartHeading: Locator;
  27 |   readonly revenueChartCanvas: Locator;
  28 |   readonly financialReportsHeading: Locator;
  29 |   readonly settlementReportCard: Locator;
  30 |   readonly commissionReportCard: Locator;
  31 |   readonly reconciliationReportCard: Locator;
  32 |   readonly mcdaPayoutReportCard: Locator;
  33 |   readonly refundReportCard: Locator;
  34 |   readonly readyBadges: Locator;
  35 |   readonly notGeneratedBadge: Locator;
  36 |   readonly comingSoonBadge: Locator;
  37 |   readonly downloadButtons: Locator;
  38 | 
  39 |   constructor(page: Page) {
  40 |     this.page = page;
  41 | 
  42 |     this.heading    = page.locator('h1, h2, h3, [class*="title"], [class*="heading"]').filter({ hasText: /^Reports$/i }).or(page.getByRole('heading', { name: /^Reports$/i }));
  43 |     this.dateFilter = page.getByRole('combobox').or(
  44 |       page.locator('[class*="dropdown"], select').filter({ hasText: /Today/i }).first()
  45 |     );
  46 | 
  47 |     // Transaction status cards
  48 |     this.transactionsReceivedCard = page.getByText(/Transactions received/i).first();
  49 |     this.transactionReconCard     = page.getByText(/Transaction recon/i).first();
  50 |     this.transactionSettledCard   = page.getByText(/Transaction settled/i).first();
  51 |     this.payoutCard               = page.getByText(/^Payout$/i).first();
  52 |     this.failedCard               = page.getByText(/^Failed$/i).first();
  53 | 
  54 |     // Settlement & Payout Overview
  55 |     this.settlementPayoutOverviewHeading = page.getByText(/Settlement.*Payout Overview/i);
  56 |     this.settlementOverviewCanvas        = page.locator('canvas, svg').first();
  57 |     this.conversionRatio                 = page.getByText(/Conversion Ratio/i).first();
  58 | 
  59 |     // Legacy aliases pointing to new equivalents
  60 |     this.totalRevenueCard       = this.transactionsReceivedCard;
  61 |     this.totalTransactionsCard  = this.transactionReconCard;
  62 |     this.successfulPaymentsCard = this.transactionSettledCard;
  63 |     this.failedPaymentsCard     = this.failedCard;
  64 |     this.revenueChartHeading    = this.settlementPayoutOverviewHeading;
  65 |     this.revenueChartCanvas     = this.settlementOverviewCanvas;
  66 |     this.financialReportsHeading  = this.settlementPayoutOverviewHeading;
  67 |     this.settlementReportCard     = this.transactionsReceivedCard;
  68 |     this.commissionReportCard     = this.transactionReconCard;
  69 |     this.reconciliationReportCard = this.transactionSettledCard;
  70 |     this.mcdaPayoutReportCard     = this.payoutCard;
  71 |     this.refundReportCard         = this.failedCard;
  72 |     this.readyBadges      = page.getByText(/0 transactions/i);
  73 |     this.notGeneratedBadge = page.getByText(/0 reconciled/i).first();
  74 |     this.comingSoonBadge  = page.getByText(/0 settled/i).first();
  75 |     this.downloadButtons  = page.getByRole('button', { name: /Download/i });
  76 |   }
  77 | 
  78 |   async goto() {
  79 |     await this.page.goto('/reports');
  80 |     await this.page.waitForLoadState('networkidle');
  81 |   }
  82 | 
  83 |   async assertPageLoaded() {
> 84 |     await expect(this.page).toHaveURL(/\/reports/, { timeout: 15000 });
     |                             ^ Error: expect(page).toHaveURL(expected) failed
  85 |     await expect(this.heading.first()).toBeVisible({ timeout: 10000 });
  86 |   }
  87 | }
  88 | 
```