# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Login/login-sso-tc1-sso-button-visible-disabled.spec.ts >> Login — SSO >> TC1 — SSO login button should be visible but disabled
- Location: tests/UI/Login/login-sso-tc1-sso-button-visible-disabled.spec.ts:16:7

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
  1  | import { test, expect } from '../fixtures';
  2  | import { allure } from 'allure-playwright';
  3  | import { LoginPage } from '../../pages/LoginPage';
  4  | 
  5  | test.describe('Login — SSO', () => {
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
  16 |   test('TC1 — SSO login button should be visible but disabled', async ({ logger }) => {
  17 |     await allure.description('Verifies the SSO login button is present and disabled on the login page (SSO integration not yet enabled).');
  18 |     await allure.label('feature',  'Login');
  19 |     await allure.label('story',    'SSO');
  20 |     await allure.label('severity', 'normal');
  21 |     await allure.label('priority', 'P2');
  22 |     await allure.label('testId',   'LOGIN-SSO-TC1');
  23 | 
  24 |     await logger.step('Step 1 — Verifies the SSO login button is present and disabled on the login page (SSO integration not yet enabled)', async () => {
  25 |       logger.info('Test scope: SSO login button visibility and disabled state');
  26 |     });
  27 | 
  28 |     // NOTE (open item): the live app currently renders this button ENABLED,
  29 |     // contradicting this assertion. Left as-is pending product/security
  30 |     // confirmation on whether SSO should be clickable yet — see prior
  31 |     // load/regression test findings.
  32 |     await logger.step('Step 2 — Verify SSO button is visible and disabled', async () => {
  33 |       logger.info('Checking that the SSO login button is present on the page');
  34 |       await expect(loginPage.ssoButton).toBeVisible();
  35 |       logger.pass('SSO login button is visible');
  36 |       logger.info('Asserting that the SSO login button is in a disabled state');
> 37 |       await expect(loginPage.ssoButton).toBeDisabled();
     |                                         ^ Error: expect(locator).toBeDisabled() failed
  38 |       logger.pass('SSO login button is disabled as expected');
  39 |     });
  40 |   });
  41 | });
  42 | 
```