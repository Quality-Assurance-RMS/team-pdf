# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settings/set-btn-tc1-save-changes-button-visible.spec.ts >> Settings — Save Changes Button >> TC1 — "Save Changes" button is visible and enabled
- Location: tests/UI/Settings/set-btn-tc1-save-changes-button-visible.spec.ts:20:7

# Error details

```
Error: expect(page).toHaveURL(expected) failed

Expected pattern: /\/settings/
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
  3  | export class SettingsPage {
  4  |   readonly page: Page;
  5  | 
  6  |   readonly heading: Locator;
  7  | 
  8  |   // Profile card
  9  |   readonly profileAvatar: Locator;
  10 |   readonly profileName: Locator;
  11 |   readonly profileEmail: Locator;
  12 |   readonly platformBadge: Locator; // shows the logged-in user's role (test account is ADMIN)
  13 | 
  14 |   // Tabs
  15 |   readonly profileSettingsTab: Locator;
  16 |   readonly securityTab: Locator;
  17 |   readonly notificationsTab: Locator;
  18 | 
  19 |   // Profile form fields
  20 |   readonly fullNameField: Locator;
  21 |   readonly emailAddressField: Locator;
  22 |   readonly phoneNumberField: Locator;
  23 |   readonly groupField: Locator;  // now "Role" field in new UI
  24 |   readonly roleField: Locator;
  25 |   readonly saveChangesButton: Locator;
  26 | 
  27 |   constructor(page: Page) {
  28 |     this.page = page;
  29 | 
  30 |     this.heading = page.getByRole('heading', { name: /^Settings$/i });
  31 | 
  32 |     this.profileAvatar = page.locator('[class*="rounded-full"], [class*="bg-"], [class*="flex"][class*="items-center"][class*="justify-center"]').first();
  33 |     this.profileName   = page.locator('[class*="profile"], [class*="card"]').getByText(/Ashil/i).first();
  34 |     this.profileEmail  = page.locator('[class*="profile"], [class*="card"]').getByText(/@fortunesoftit\.com/i).first();
  35 |     // NOTE: was hardcoded to /^SUPER_ADMIN$/i, but the configured LOGIN_EMAIL test
  36 |     // account (see .env) actually has the ADMIN role — confirmed live on the
  37 |     // Settings page. Updated to match the real current test account's role.
  38 |     this.platformBadge = page.getByText(/^ADMIN$/i).first();
  39 | 
  40 |     this.profileSettingsTab = page.getByRole('tab', { name: /Profile Settings/i }).or(
  41 |       page.getByText(/Profile Settings/i).first()
  42 |     );
  43 |     this.securityTab = page.getByRole('tab', { name: /Security/i }).or(
  44 |       page.getByText(/^Security$/i).first()
  45 |     );
  46 |     this.notificationsTab = page.getByRole('tab', { name: /Notifications/i }).or(
  47 |       page.getByText(/^Notifications$/i).first()
  48 |     );
  49 | 
  50 |     this.fullNameField     = page.getByLabel(/Full Name/i).or(page.locator('input[placeholder*="Full Name"], input[name*="name"]').first());
  51 |     this.emailAddressField = page.getByLabel(/Email Address/i).or(page.locator('input[type="email"], input[placeholder*="email"]').first());
  52 |     this.phoneNumberField  = page.getByLabel(/Phone Number/i).or(page.locator('input[placeholder*="phone"]').first());
  53 |     this.roleField         = page.getByLabel(/^Role$/i).or(page.locator('input[placeholder*="role"], input[name*="role"]').first());
  54 |     this.groupField        = this.roleField; // "Group" renamed to "Role" in new UI
  55 |     this.saveChangesButton = page.getByRole('button', { name: /Save Changes/i });
  56 |   }
  57 | 
  58 |   async goto() {
  59 |     await this.page.goto('/settings');
  60 |     await this.page.waitForLoadState('networkidle');
  61 |   }
  62 | 
  63 |   async assertPageLoaded() {
> 64 |     await expect(this.page).toHaveURL(/\/settings/, { timeout: 15000 });
     |                             ^ Error: expect(page).toHaveURL(expected) failed
  65 |     await expect(this.heading).toBeVisible({ timeout: 10000 });
  66 |   }
  67 | }
  68 | 
```