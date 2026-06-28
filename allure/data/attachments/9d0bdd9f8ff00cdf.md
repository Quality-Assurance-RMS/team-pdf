# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/UserManagement/super-admin-role-badge.spec.ts >> User Management — SUPER_ADMIN Role Badge >> TC1 — SUPER_ADMIN badges are visible in the User Role column
- Location: tests/UI/UserManagement/super-admin-role-badge.spec.ts:19:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/user/
Received string:  "https://api.rms.dev.demo-fsit.com/auth/login"
Timeout: 15000ms

Call log:
  - Expect "toHaveURL" with timeout 15000ms
    - waiting for" https://api.rms.dev.demo-fsit.com/auth/login" navigation to finish...
    - navigated to "https://api.rms.dev.demo-fsit.com/auth/login"
    32 × unexpected value "https://api.rms.dev.demo-fsit.com/auth/login"

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
  3  | export class UserManagementPage {
  4  |   readonly page: Page;
  5  | 
  6  |   readonly heading: Locator;
  7  |   readonly userCount: Locator;
  8  |   readonly addNewUserButton: Locator;
  9  |   readonly userRoleFilter: Locator;
  10 |   readonly statusFilter: Locator;
  11 | 
  12 |   // Table columns
  13 |   readonly columnUserName: Locator;
  14 |   readonly columnUserRole: Locator;
  15 |   readonly columnEmailId: Locator;
  16 |   readonly columnStatus: Locator;
  17 |   readonly columnActivation: Locator;
  18 |   readonly columnActions: Locator;
  19 |   readonly tableRows: Locator;
  20 | 
  21 |   // Role badges
  22 |   readonly superAdminBadges: Locator;
  23 | 
  24 |   // Activation toggles
  25 |   readonly activationToggles: Locator;
  26 | 
  27 |   constructor(page: Page) {
  28 |     this.page = page;
  29 | 
  30 |     this.heading          = page.getByRole('heading', { name: /User Management/i });
  31 |     this.userCount        = page.getByText(/\d+\s*Users/i);
  32 |     this.addNewUserButton = page.getByRole('button', { name: /Add User/i });
  33 |     this.userRoleFilter   = page.getByRole('combobox').filter({ hasText: /User Role/i }).or(
  34 |       page.locator('[class*="select"], [class*="dropdown"]').filter({ hasText: /User Role/i }).first()
  35 |     );
  36 |     this.statusFilter = page.getByRole('combobox').filter({ hasText: /All Status/i }).or(
  37 |       page.locator('[class*="select"], [class*="dropdown"]').filter({ hasText: /All Status/i }).first()
  38 |     );
  39 | 
  40 |     this.columnUserName   = page.getByRole('columnheader', { name: /User Name/i });
  41 |     this.columnUserRole   = page.getByRole('columnheader', { name: /User Role/i });
  42 |     this.columnEmailId    = page.getByRole('columnheader', { name: /Email ID/i });
  43 |     this.columnStatus     = page.getByRole('columnheader', { name: /^Status$/i });
  44 |     this.columnActivation = page.getByRole('columnheader', { name: /Activation/i });
  45 |     this.columnActions    = page.getByRole('columnheader', { name: /Actions/i });
  46 |     this.tableRows        = page.locator('table tbody tr');
  47 | 
  48 |     this.superAdminBadges  = page.locator('table tbody').getByText(/SUPER_ADMIN/i);
  49 |     this.activationToggles = page.locator('table tbody input[type="checkbox"], table tbody [class*="toggle"], table tbody [role="switch"]');
  50 |   }
  51 | 
  52 |   async goto() {
  53 |     await this.page.goto('/user');
  54 |     await this.page.waitForLoadState('networkidle');
  55 |   }
  56 | 
  57 |   async assertPageLoaded() {
> 58 |     await expect(this.page).toHaveURL(/\/user/, { timeout: 15000 });
     |                             ^ Error: expect(page).toHaveURL(expected) failed
  59 |     await expect(this.heading).toBeVisible({ timeout: 10000 });
  60 |   }
  61 | }
  62 | 
```