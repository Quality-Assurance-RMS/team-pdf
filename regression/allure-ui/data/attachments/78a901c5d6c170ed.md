# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Settings/profile-card-visible.spec.ts >> Settings — Profile Card >> TC1 — Profile card with name, email, and SUPER_ADMIN role is visible
- Location: tests/UI/Settings/profile-card-visible.spec.ts:20:7

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText(/^SUPER_ADMIN$/i).first()
Expected: visible
Timeout: 5000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 5000ms
  - waiting for getByText(/^SUPER_ADMIN$/i).first()

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
- banner:
  - button "Notifications": "3"
  - text: A Ashil ADMIN PLATFORM
- main:
  - heading "Settings" [level=2]
  - paragraph
  - text: A
  - paragraph: Ashil
  - paragraph: ashil.shaji@fortunesoftit.com
  - text: ADMIN
  - button "Profile Settings"
  - button "Security"
  - button "Notifications"
  - heading "Profile Information" [level=2]
  - text: Full Name
  - textbox "Enter Your name": Ashil
  - paragraph
  - text: Email Address
  - textbox "Enter Your email" [disabled]: ashil.shaji@fortunesoftit.com
  - paragraph
  - text: Phone Number
  - textbox "Enter Your phone number"
  - paragraph
  - text: Role
  - textbox "Enter Your role": ADMIN
  - paragraph
  - button "Save Changes"
- region "Notifications alt+T"
```

# Test source

```ts
  1  | /**
  2  |  * @file profile-card-visible.spec.ts
  3  |  * @testId SET-CARD-TC1
  4  |  * @feature Settings — Profile Card
  5  |  * @priority P0 / Blocker
  6  |  *
  7  |  * Verifies that the user profile card (containing name, email, and SUPER_ADMIN role)
  8  |  * is visible on the Settings page.
  9  |  */
  10 | 
  11 | import { test, expect } from '../fixtures';
  12 | import type { TestInfo } from '@playwright/test';
  13 | import { allure } from 'allure-playwright';
  14 | import { SettingsPage } from '../../pages/SettingsPage';
  15 | import { attachScreenshot } from '../../utils/screenshotHelper';
  16 | 
  17 | test.describe('Settings — Profile Card', () => {
  18 |   test.setTimeout(120000);
  19 | 
  20 |   test('TC1 — Profile card with name, email, and SUPER_ADMIN role is visible', async ({ page, logger }, testInfo: TestInfo) => {
  21 |     await allure.description('Verifies the user profile card renders with name, email address, and SUPER_ADMIN role.');
  22 |     await allure.label('feature',  'Settings');
  23 |     await allure.label('story',    'Profile Card');
  24 |     await allure.label('severity', 'blocker');
  25 |     await allure.label('priority', 'P0');
  26 |     await allure.label('testId',   'SET-CARD-TC1');
  27 | 
  28 |     const settingsPage = new SettingsPage(page);
  29 | 
  30 |     await logger.step('Step 1 — Navigate to Settings page', async () => {
  31 |       logger.info('Opening the Settings page');
  32 |       await settingsPage.goto();
  33 |       logger.pass('Navigation to Settings page initiated successfully');
  34 |       await attachScreenshot(page, testInfo, '01 — Page loading');
  35 |     });
  36 | 
  37 |     await logger.step('Step 2 — Verify page loads successfully', async () => {
  38 |       logger.info('Waiting for all Settings page elements to render');
  39 |       await settingsPage.assertPageLoaded();
  40 |       logger.pass('Settings page is fully loaded with all required elements');
  41 |       await attachScreenshot(page, testInfo, '02 — Page loaded and ready');
  42 |     });
  43 | 
  44 |     await logger.step('Step 3 — User name is visible on the profile card', async () => {
  45 |       logger.info('Asserting profile card is visible');
  46 |       await expect(page.getByText(/^Ashil$/i).first()).toBeVisible();
  47 |       logger.pass('Profile card is visible and rendered');
  48 |       await attachScreenshot(page, testInfo, '03 — User name visible', page.getByText(/^Ashil$/i).first());
  49 |     });
  50 | 
  51 |     await logger.step('Step 4 — User email is visible on the profile card', async () => {
  52 |       logger.info('Asserting profile card is visible');
  53 |       await expect(page.getByText(/@fortunesoftit\.com/i).first()).toBeVisible();
  54 |       logger.pass('Profile card is visible and rendered');
  55 |       await attachScreenshot(page, testInfo, '04 — User email visible', page.getByText(/@fortunesoftit\.com/i).first());
  56 |     });
  57 | 
  58 |     await logger.step('Step 5 — "SUPER_ADMIN" role is visible on the profile card', async () => {
  59 |       logger.info('Asserting profile card is visible');
> 60 |       await expect(settingsPage.platformBadge).toBeVisible();
     |                                                ^ Error: expect(locator).toBeVisible() failed
  61 |       logger.pass('Profile card is visible and rendered');
  62 |       await attachScreenshot(page, testInfo, '05 — SUPER_ADMIN role visible', settingsPage.platformBadge);
  63 |     });
  64 |   });
  65 | });
  66 | 
```