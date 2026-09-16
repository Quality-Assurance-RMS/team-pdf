# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: auth.setup.ts >> authenticate via TaifaSSO and save session
- Location: tests/auth.setup.ts:15:6

# Error details

```
Error: page.goto: net::ERR_NAME_NOT_RESOLVED at https://api.rms.dev.demo-fsit.com/auth/login
Call log:
  - navigating to "https://api.rms.dev.demo-fsit.com/auth/login", waiting until "load"

```

# Test source

```ts
  1  | import { Page, Locator, expect } from '@playwright/test';
  2  | 
  3  | export class LoginPage {
  4  |   readonly page: Page;
  5  |   readonly emailInput: Locator;
  6  |   readonly passwordInput: Locator;
  7  |   readonly rememberCheckbox: Locator;
  8  |   readonly forgotPasswordLink: Locator;
  9  |   readonly loginButton: Locator;
  10 |   readonly ssoButton: Locator;
  11 |   readonly passwordToggle: Locator;
  12 | 
  13 |   constructor(page: Page) {
  14 |     this.page = page;
  15 |     this.emailInput = page.getByPlaceholder('Enter Your email');
  16 |     this.passwordInput = page.getByPlaceholder('Enter Password');
  17 |     this.rememberCheckbox = page.getByLabel('Remember for 30 days');
  18 |     this.forgotPasswordLink = page.getByText('Forgot Password?');
  19 |     this.loginButton = page.getByRole('button', { name: /log in/i });
  20 |     this.ssoButton = page.getByRole('button', { name: /login with sso/i });
  21 |     this.passwordToggle = page.locator('input[type="password"] ~ button, input[placeholder="Enter Password"] + button').first();
  22 |   }
  23 | 
  24 |   async goto() {
> 25 |     await this.page.goto('/auth/login');
     |                     ^ Error: page.goto: net::ERR_NAME_NOT_RESOLVED at https://api.rms.dev.demo-fsit.com/auth/login
  26 |   }
  27 | 
  28 |   async login(email: string, password: string) {
  29 |     await this.emailInput.fill(email);
  30 |     await this.passwordInput.fill(password);
  31 |     await this.loginButton.click();
  32 |   }
  33 | 
  34 |   async assertPageLoaded() {
  35 |     await expect(this.page).toHaveURL(/\/auth\/login/);
  36 |     await expect(this.emailInput).toBeVisible();
  37 |     await expect(this.passwordInput).toBeVisible();
  38 |     await expect(this.loginButton).toBeVisible();
  39 |   }
  40 | }
  41 | 
```