# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Transactions/txn-flt-tc3-method-filter-dropdown.spec.ts >> Transactions — Method Filter Dropdown >> TC3 — Method filter dropdown is visible
- Location: tests/UI/Transactions/txn-flt-tc3-method-filter-dropdown.spec.ts:19:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/transaction/
Received string:  "https://api.rms.dev.demo-fsit.com/auth/login"
Timeout: 5000ms

Call log:
  - Expect "toHaveURL" with timeout 5000ms
    7 × unexpected value "https://api.rms.dev.demo-fsit.com/auth/login"

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
- textbox "Enter Your email" [disabled]
- paragraph
- text: Password
- textbox "Enter Password" [disabled]
- button [disabled]
- paragraph
- checkbox "Remember for 30 days" [disabled]
- text: Remember for 30 days
- link "Forgot Password?":
  - /url: /auth/forgot-password
- button "Log In →" [disabled]
- text: Or
- button "Login with SSO →"
- region "Notifications alt+T"
```

# Test source

```ts
  1  | import { Page, Locator, expect } from '@playwright/test';
  2  | 
  3  | export class TransactionPage {
  4  |   readonly page: Page;
  5  |   readonly tableRows: Locator;
  6  |   readonly firstTransactionId: Locator;
  7  |   readonly firstActionButton: Locator;
  8  |   readonly dateFilterDropdown: Locator;
  9  |   readonly searchInput: Locator;
  10 | 
  11 |   constructor(page: Page) {
  12 |     this.page = page;
  13 |     this.tableRows = page.locator('table tbody tr, [class*="table"] [class*="row"]').first();
  14 |     this.firstTransactionId = page.locator('table tbody tr:first-child td:first-child, [class*="row"]:first-child [class*="col"]:first-child').first();
  15 |     // The action cell's clickable element is an icon/button in the LAST cell of the
  16 |     // row (labelled "View Details" via a hover tooltip) — clicking the row itself
  17 |     // does not navigate anywhere.
  18 |     this.firstActionButton = page.locator('table tbody tr:first-child td:last-child button, table tbody tr:first-child td:last-child svg, table tbody tr:first-child td:last-child [role="button"]').first();
  19 |     this.dateFilterDropdown = page.getByRole('combobox').first();
  20 |     this.searchInput = page.getByPlaceholder(/Search/i).or(page.locator('input[type="search"], input[type="text"]').first());
  21 |   }
  22 | 
  23 |   async goto() {
  24 |     await this.page.goto('/transaction');
  25 |     await this.page.waitForLoadState('networkidle');
  26 |   }
  27 | 
  28 |   async assertPageLoaded() {
> 29 |     await expect(this.page).toHaveURL(/\/transaction/);
     |                             ^ Error: expect(page).toHaveURL(expected) failed
  30 |     await expect(this.page.getByRole('heading', { name: 'Transactions' })).toBeVisible({ timeout: 10000 });
  31 |   }
  32 | 
  33 |   // The default date filter is "Today", which on this demo/dev environment
  34 |   // frequently has zero transactions (seed data is dated in the past). Tests
  35 |   // that need real row data should call this first to widen the window.
  36 |   async selectDateRange(label: string) {
  37 |     await this.dateFilterDropdown.selectOption({ label });
  38 |     await this.page.waitForLoadState('networkidle');
  39 |   }
  40 | 
  41 |   async getFirstTransactionId(): Promise<string> {
  42 |     // Get the transaction ID text from the first row first cell
  43 |     const firstRow = this.page.locator('table tbody tr').first();
  44 |     const idCell = firstRow.locator('td').first();
  45 |     const text = await idCell.innerText();
  46 |     return text.trim();
  47 |   }
  48 | 
  49 |   async searchByKeyword(keyword: string) {
  50 |     await this.searchInput.fill(keyword);
  51 |     await this.page.waitForLoadState('networkidle');
  52 |   }
  53 | 
  54 |   async clickFirstRow() {
  55 |     // Clicking the bare <tr> does not navigate on this page — the action
  56 |     // icon/button in the last cell is the actual navigation trigger.
  57 |     await this.firstActionButton.click();
  58 |   }
  59 | 
  60 |   async verifyTransactionDetailId(expectedId: string) {
  61 |     // The detail page heading shows the transaction ID
  62 |     await expect(this.page.getByText(expectedId).first()).toBeVisible({ timeout: 10000 });
  63 |   }
  64 | }
  65 | 
```