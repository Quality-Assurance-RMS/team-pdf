# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settlements/setl-flt-tc1-date-filter-visible.spec.ts >> Settlements — Date Filter >> TC1 — Date filter defaults to "Last 7 Days"
- Location: tests/UI/Settlements/setl-flt-tc1-date-filter-visible.spec.ts:20:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/settlements/
Received string:  "https://api.rms.dev.demo-fsit.com/auth/login"
Timeout: 15000ms

Call log:
  - Expect "toHaveURL" with timeout 15000ms
    33 × unexpected value "https://api.rms.dev.demo-fsit.com/auth/login"

```

```yaml
- img "Unified Control"
- paragraph: Government services, now at your fingertips
- img
- paragraph: RMS
- paragraph: National Treasury
- heading "Login" [level=1]
- paragraph: Access Revenue Management System (RMS)
- text: Email address
- textbox "Enter Your email"
- paragraph
- text: Password
- textbox "Enter Password"
- button
- paragraph
- checkbox "Remember for 30 days"
- text: Remember for 30 days
- link "Forgot Password?":
  - /url: /auth/forgot-password
- button "Log In →"
- text: Or
- button "Login with SSO →"
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
> 70 |     await expect(this.page).toHaveURL(/\/settlements/, { timeout: 15000 });
     |                             ^ Error: expect(page).toHaveURL(expected) failed
  71 |     await expect(this.page.getByText('Settlements').first()).toBeVisible({ timeout: 10000 });
  72 |   }
  73 | 
  74 |   // The default date filter ("Last 7 Days") can have zero settlements on this
  75 |   // demo/dev environment since seed data ages past that window over time.
  76 |   // Tests that need real row data should call this first to widen the range.
  77 |   async selectDateRange(label: string) {
  78 |     await this.dateFilter.selectOption({ label });
  79 |     await this.page.waitForLoadState('networkidle');
  80 |   }
  81 | }
  82 | 
```