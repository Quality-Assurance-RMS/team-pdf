# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Login/login-nav-tc1-forgot-password-link.spec.ts >> Login — Navigation >> TC1 — "Forgot Password" link navigates to the reset page
- Location: tests/UI/Login/login-nav-tc1-forgot-password-link.spec.ts:16:7

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.click: Test timeout of 120000ms exceeded.
Call log:
  - waiting for getByText('Forgot Password?')
    - locator resolved to <a data-discover="true" href="/auth/forgot-password" class="text-lg text-[#059669] hover:underline↵                                    pointer-events-none opacity-50 cursor-not-allowed">Forgot Password?</a>
  - attempting click action
    2 × waiting for element to be visible, enabled and stable
      - element is visible, enabled and stable
      - scrolling into view if needed
      - done scrolling
      - <div class="flex items-center gap-2">…</div> intercepts pointer events
    - retrying click action
    - waiting 20ms
    2 × waiting for element to be visible, enabled and stable
      - element is visible, enabled and stable
      - scrolling into view if needed
      - done scrolling
      - <div class="flex items-center gap-2">…</div> intercepts pointer events
    - retrying click action
      - waiting 100ms
    270 × waiting for element to be visible, enabled and stable
        - element is visible, enabled and stable
        - scrolling into view if needed
        - done scrolling
        - <div class="flex items-center gap-2">…</div> intercepts pointer events
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
  5  | test.describe('Login — Navigation', () => {
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
  16 |   test('TC1 — "Forgot Password" link navigates to the reset page', async ({ page, logger }) => {
  17 |     await allure.description('Verifies that clicking the Forgot Password link navigates away from the login page.');
  18 |     await allure.label('feature',  'Login');
  19 |     await allure.label('story',    'Navigation');
  20 |     await allure.label('severity', 'normal');
  21 |     await allure.label('priority', 'P2');
  22 |     await allure.label('testId',   'LOGIN-NAV-TC1');
  23 | 
  24 |     await logger.step('Step 1 — Verifies that clicking the Forgot Password link navigates away from the login page', async () => {
  25 |       logger.info('Test scope: Forgot Password link navigation');
  26 |     });
  27 | 
  28 |     await logger.step('Step 2 — Click Forgot Password link and verify navigation', async () => {
  29 |       logger.info('Clicking the Forgot Password link on the login page');
> 30 |       await loginPage.forgotPasswordLink.click();
     |                                          ^ Error: locator.click: Test timeout of 120000ms exceeded.
  31 |       logger.info('Verifying the URL has changed away from the login page');
  32 |       await expect(page).not.toHaveURL(/\/auth\/login/, { timeout: 8000 });
  33 |       logger.pass('Successfully navigated away from login page via Forgot Password link');
  34 |     });
  35 |   });
  36 | });
  37 | 
```