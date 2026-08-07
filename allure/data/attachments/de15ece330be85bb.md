# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Login/login-auth-tc1-valid-credentials-redirect.spec.ts >> Login — Authentication >> TC1 — Valid credentials redirect away from the login page
- Location: tests/UI/Login/login-auth-tc1-valid-credentials-redirect.spec.ts:19:7

# Error details

```
Error: expect(page).not.toHaveURL(expected) failed

Expected pattern: not /\/auth\/login/
Received string: "https://api.rms.dev.demo-fsit.com/auth/login"
Timeout: 15000ms

Call log:
  - Expect "not toHaveURL" with timeout 15000ms
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
- textbox "Enter Your email": ashil.shaji@fortunesoftit.com
- paragraph
- text: Password
- textbox "Enter Password": RMS@fortune@123
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
  1  | import { test, expect } from '../fixtures';
  2  | import { allure } from 'allure-playwright';
  3  | import { LoginPage } from '../../pages/LoginPage';
  4  | 
  5  | const EMAIL = process.env.LOGIN_EMAIL!;
  6  | const PASSWORD = process.env.LOGIN_PASSWORD!;
  7  | 
  8  | test.describe('Login — Authentication', () => {
  9  |   test.use({ storageState: { cookies: [], origins: [] } });
  10 | 
  11 |   let loginPage: LoginPage;
  12 | 
  13 |   test.beforeEach(async ({ page }) => {
  14 |     loginPage = new LoginPage(page);
  15 |     await loginPage.goto();
  16 |     await loginPage.assertPageLoaded();
  17 |   });
  18 | 
  19 |   test('TC1 — Valid credentials redirect away from the login page', async ({ page, logger }) => {
  20 |     await allure.description('Verifies that submitting valid credentials logs the user in and redirects away from the login page.');
  21 |     await allure.label('feature',  'Login');
  22 |     await allure.label('story',    'Authentication');
  23 |     await allure.label('severity', 'blocker');
  24 |     await allure.label('priority', 'P0');
  25 |     await allure.label('testId',   'LOGIN-AUTH-TC1');
  26 | 
  27 |     await logger.step('Step 1 — Verifies that submitting valid credentials logs the user in and redirects away from the login page', async () => {
  28 |       logger.info('Test scope: successful login with valid credentials');
  29 |     });
  30 | 
  31 |     await logger.step('Step 2 — Submit valid credentials and verify redirect away from login', async () => {
  32 |       logger.info('Submitting valid email and password credentials');
  33 |       await loginPage.login(EMAIL, PASSWORD);
  34 |       logger.info('Verifying that the URL is no longer the login page');
> 35 |       await expect(page).not.toHaveURL(/\/auth\/login/, { timeout: 15000 });
     |                              ^ Error: expect(page).not.toHaveURL(expected) failed
  36 |       logger.pass('Login successful — user redirected away from login page');
  37 |     });
  38 |   });
  39 | });
  40 | 
```