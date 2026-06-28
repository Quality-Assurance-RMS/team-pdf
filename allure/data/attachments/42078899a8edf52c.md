# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/login.spec.ts >> RMS Login Page >> SSO login button should be visible but disabled
- Location: tests/UI/login.spec.ts:98:7

# Error details

```
Error: expect(locator).toBeDisabled() failed

Locator:  getByRole('button', { name: /login with sso/i })
Expected: disabled
Received: enabled
Timeout:  5000ms

Call log:
  - Expect "toBeDisabled" with timeout 5000ms
  - waiting for getByRole('button', { name: /login with sso/i })
    13 × locator resolved to <button type="button" class="border border-[#059669] bg-[#E7FAEE] rounded-lg↵                        py-2 text-[#059669] hover:cursor-pointer">Login with SSO →</button>
       - unexpected value "enabled"

```

```yaml
- button "Login with SSO →"
```

# Test source

```ts
  1   | ﻿import { test, expect } from './fixtures';
  2   | import { LoginPage } from '../pages/LoginPage';
  3   | 
  4   | const EMAIL = process.env.LOGIN_EMAIL!;
  5   | const PASSWORD = process.env.LOGIN_PASSWORD!;
  6   | 
  7   | test.describe('RMS Login Page', () => {
  8   |   // These tests exercise the unauthenticated login flow, so the global
  9   |   // storageState must be cleared to prevent an automatic redirect to /dashboard.
  10  |   test.use({ storageState: { cookies: [], origins: [] } });
  11  | 
  12  |   let loginPage: LoginPage;
  13  | 
  14  |   test.beforeEach(async ({ page }) => {
  15  |     loginPage = new LoginPage(page);
  16  |     await loginPage.goto();
  17  |     await loginPage.assertPageLoaded();
  18  |   });
  19  | 
  20  |   test('should display all login page elements', async () => {
  21  |     await expect(loginPage.emailInput).toBeVisible();
  22  |     await expect(loginPage.passwordInput).toBeVisible();
  23  |     await expect(loginPage.rememberCheckbox).toBeVisible();
  24  |     await expect(loginPage.forgotPasswordLink).toBeVisible();
  25  |     await expect(loginPage.loginButton).toBeVisible();
  26  |     await expect(loginPage.ssoButton).toBeVisible();
  27  |   });
  28  | 
  29  |   test('should login successfully with valid credentials', async ({ page }) => {
  30  |     await loginPage.login(EMAIL, PASSWORD);
  31  | 
  32  |     // After successful login the URL should no longer be the login page
  33  |     await expect(page).not.toHaveURL(/\/auth\/login/, { timeout: 15000 });
  34  |   });
  35  | 
  36  |   test('should show error with invalid credentials', async ({ page }) => {
  37  |     // Use a password that passes format validation (upper + lower + number + special)
  38  |     // but credentials that don't exist in the system
  39  |     await loginPage.login('invalid@example.com', 'WrongP@ssword1');
  40  | 
  41  |     // App should remain on login page and surface an error
  42  |     await expect(page).toHaveURL(/\/auth\/login/, { timeout: 10000 });
  43  |     const errorMessage = page.getByText(/invalid|incorrect|wrong|not found|failed|error/i).first();
  44  |     await expect(errorMessage).toBeVisible({ timeout: 10000 });
  45  |   });
  46  | 
  47  |   test('should show validation when submitting empty form', async ({ page }) => {
  48  |     await loginPage.loginButton.click();
  49  | 
  50  |     // App shows custom inline validation messages (not native HTML5 required)
  51  |     await expect(page).toHaveURL(/\/auth\/login/);
  52  |     await expect(page.getByText('Invalid input')).toBeVisible();
  53  |     await expect(page.getByText('Password is required')).toBeVisible();
  54  |   });
  55  | 
  56  |   test('should show validation when submitting with email only', async ({ page }) => {
  57  |     await loginPage.emailInput.fill(EMAIL);
  58  |     await loginPage.loginButton.click();
  59  | 
  60  |     await expect(page).toHaveURL(/\/auth\/login/);
  61  |     await expect(page.getByText('Password is required')).toBeVisible();
  62  |   });
  63  | 
  64  |   test('should toggle password visibility', async ({ page }) => {
  65  |     await loginPage.passwordInput.fill(PASSWORD);
  66  | 
  67  |     // Password should be hidden by default
  68  |     await expect(loginPage.passwordInput).toHaveAttribute('type', 'password');
  69  | 
  70  |     // Find the visibility toggle icon next to the password field
  71  |     const eyeIcon = page.locator('input[placeholder="Enter Password"] ~ *').first();
  72  |     if (await eyeIcon.isVisible()) {
  73  |       await eyeIcon.click();
  74  |       await expect(loginPage.passwordInput).toHaveAttribute('type', 'text');
  75  |     }
  76  |   });
  77  | 
  78  |   test('should navigate to forgot password page', async ({ page }) => {
  79  |     await loginPage.forgotPasswordLink.click();
  80  |     await expect(page).not.toHaveURL(/\/auth\/login/, { timeout: 8000 });
  81  |   });
  82  | 
  83  |   test('should check remember me checkbox', async () => {
  84  |     await expect(loginPage.rememberCheckbox).not.toBeChecked();
  85  |     await loginPage.rememberCheckbox.check();
  86  |     await expect(loginPage.rememberCheckbox).toBeChecked();
  87  |     await loginPage.rememberCheckbox.uncheck();
  88  |     await expect(loginPage.rememberCheckbox).not.toBeChecked();
  89  |   });
  90  | 
  91  |   test('should login with remember me checked', async ({ page }) => {
  92  |     await loginPage.rememberCheckbox.check();
  93  |     await expect(loginPage.rememberCheckbox).toBeChecked();
  94  |     await loginPage.login(EMAIL, PASSWORD);
  95  |     await expect(page).not.toHaveURL(/\/auth\/login/, { timeout: 15000 });
  96  |   });
  97  | 
  98  |   test('SSO login button should be visible but disabled', async () => {
  99  |     await expect(loginPage.ssoButton).toBeVisible();
> 100 |     await expect(loginPage.ssoButton).toBeDisabled();
      |                                       ^ Error: expect(locator).toBeDisabled() failed
  101 |   });
  102 | 
  103 |   test('should reject login with valid email and wrong password', async ({ page }) => {
  104 |     await loginPage.login(EMAIL, 'WrongPassword@999');
  105 | 
  106 |     await expect(page).toHaveURL(/\/auth\/login/, { timeout: 10000 });
  107 |   });
  108 | });
  109 | 
```