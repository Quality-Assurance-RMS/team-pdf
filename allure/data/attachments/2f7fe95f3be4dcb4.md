# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Login/login-val-tc1-empty-form-validation.spec.ts >> Login — Validation >> TC1 — Empty form submission shows validation errors
- Location: tests/UI/Login/login-val-tc1-empty-form-validation.spec.ts:16:7

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.click: Test timeout of 120000ms exceeded.
Call log:
  - waiting for getByRole('button', { name: /log in/i })
    - locator resolved to <button disabled type="submit" class=" bg-[#059669] rounded-lg flex items-center py-2 text-white flex justify-center↵                        hover:cursor-pointer disabled:opacity-60 disabled:cursor-not-allowed">Log In →</button>
  - attempting click action
    2 × waiting for element to be visible, enabled and stable
      - element is not enabled
    - retrying click action
    - waiting 20ms
    2 × waiting for element to be visible, enabled and stable
      - element is not enabled
    - retrying click action
      - waiting 100ms
    254 × waiting for element to be visible, enabled and stable
        - element is not enabled
      - retrying click action
        - waiting 500ms

```

# Page snapshot

```yaml
- generic [ref=e2]:
  - generic [ref=e3]:
    - generic [ref=e4]:
      - img "Unified Control" [ref=e5]
      - paragraph [ref=e8]:
        - text: Government services, now
        - text: at your fingertips
    - generic [ref=e10]:
      - generic [ref=e11]:
        - generic [ref=e12]:
          - img [ref=e13]
          - generic [ref=e20]:
            - paragraph [ref=e21]: RMS
            - paragraph [ref=e22]: National Treasury
        - heading "Login" [level=1] [ref=e23]
        - paragraph [ref=e24]: Access Revenue Management System (RMS)
      - generic [ref=e25]:
        - generic [ref=e26]:
          - generic [ref=e27]: Email address
          - textbox "Enter Your email" [disabled] [ref=e28]
          - paragraph
        - generic [ref=e29]:
          - generic [ref=e30]: Password
          - generic [ref=e31]:
            - textbox "Enter Password" [disabled] [ref=e32]
            - button [disabled] [ref=e33]:
              - img [ref=e34]
          - paragraph
        - generic [ref=e39]:
          - generic [ref=e40]:
            - checkbox "Remember for 30 days" [disabled] [ref=e41]
            - text: Remember for 30 days
          - generic [ref=e42]:
            - link "Forgot Password?":
              - /url: /auth/forgot-password
        - button "Log In →" [disabled] [ref=e43]
        - generic [ref=e44]: Or
        - button "Login with SSO →" [ref=e47]
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | import { test, expect } from '../fixtures';
  2  | import { allure } from 'allure-playwright';
  3  | import { LoginPage } from '../../pages/LoginPage';
  4  | 
  5  | test.describe('Login — Validation', () => {
  6  |   test.use({ storageState: { cookies: [], origins: [] } });
  7  | 
  8  |   let loginPage: LoginPage;
  9  | 
  10 |   test.beforeEach(async ({ page }) => {
  11 |     loginPage = new LoginPage(page);
  12 |     await loginPage.goto();
  13 |     await loginPage.assertPageLoaded();
  14 |   });
  15 | 
  16 |   test('TC1 — Empty form submission shows validation errors', async ({ page, logger }) => {
  17 |     await allure.description('Verifies that submitting the login form with no input shows inline validation messages.');
  18 |     await allure.label('feature',  'Login');
  19 |     await allure.label('story',    'Validation');
  20 |     await allure.label('severity', 'normal');
  21 |     await allure.label('priority', 'P2');
  22 |     await allure.label('testId',   'LOGIN-VAL-TC1');
  23 | 
  24 |     await logger.step('Step 1 — Verifies that submitting the login form with no input shows inline validation messages', async () => {
  25 |       logger.info('Test scope: empty-form validation messaging');
  26 |     });
  27 | 
  28 |     await logger.step('Step 2 — Click login with empty form and verify validation messages', async () => {
  29 |       logger.info('Clicking the login button without entering any credentials');
> 30 |       await loginPage.loginButton.click();
     |                                   ^ Error: locator.click: Test timeout of 120000ms exceeded.
  31 |       logger.info('Verifying the app stays on the login page');
  32 |       // App shows custom inline validation messages (not native HTML5 required)
  33 |       await expect(page).toHaveURL(/\/auth\/login/);
  34 |       logger.pass('URL is still the login page');
  35 |       logger.info('Checking for inline validation messages');
  36 |       await expect(page.getByText('Invalid input')).toBeVisible();
  37 |       logger.pass('"Invalid input" validation message is visible');
  38 |       await expect(page.getByText('Password is required')).toBeVisible();
  39 |       logger.pass('"Password is required" validation message is visible');
  40 |     });
  41 |   });
  42 | });
  43 | 
```