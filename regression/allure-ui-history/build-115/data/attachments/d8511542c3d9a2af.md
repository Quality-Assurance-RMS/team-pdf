# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: auth.setup.ts >> authenticate via TaifaSSO and save session
- Location: tests/auth.setup.ts:15:6

# Error details

```
Error: locator.fill: value: expected string, got undefined
```

# Page snapshot

```yaml
- generic [active] [ref=e1]:
  - navigation [ref=e2]:
    - generic [ref=e3]:
      - link "TaifaSSO — Public Service Single Sign On" [ref=e4] [cursor=pointer]:
        - /url: /
        - img "TaifaSSO — Public Service Single Sign On" [ref=e5]
      - generic [ref=e6]:
        - link "Home" [ref=e7] [cursor=pointer]:
          - /url: /
        - link "About" [ref=e8] [cursor=pointer]:
          - /url: /#about
        - link "Capabilities" [ref=e9] [cursor=pointer]:
          - /url: /#capabilities
        - link "Compliance" [ref=e10] [cursor=pointer]:
          - /url: /#compliance
        - link "FAQ" [ref=e11] [cursor=pointer]:
          - /url: /#faq
      - generic:  
  - main [ref=e12]:
    - generic [ref=e14]:
      - generic [ref=e15]:
        - img [ref=e17]
        - generic [ref=e18]: Republic Of Kenya
        - generic [ref=e19]: Ministry Of National Treasury & Economic Planning
      - generic [ref=e22]:
        - generic [ref=e23]:
          - generic [ref=e24]:
            - heading "Sign In" [level=1] [ref=e25]
            - paragraph [ref=e26]: Enter your National ID or Passport number to continue.
          - generic [ref=e27]:
            - generic [ref=e28]: National ID or Passport *
            - textbox "National ID or Passport *" [ref=e29]:
              - /placeholder: e.g. 30012345
          - button "Continue" [disabled] [ref=e30]
        - text: 
  - contentinfo [ref=e31]:
    - generic [ref=e33]:
      - generic [ref=e34]:
        - strong [ref=e35]: TaifaSSO © 2026
        - text: Republic of Kenya · All Rights Reserved
      - text:  
```

# Test source

```ts
  1  | import { Page, Locator, expect } from '@playwright/test';
  2  | 
  3  | // TaifaSSO redesigned this step from one combined "Enter Credentials" form
  4  | // (ID + password together) into two separate screens: "Sign In" (National ID
  5  | // or Passport only) followed by its own "Enter Your Password" screen. The
  6  | // public API here (assertPageLoaded + submitCredentials) is kept the same as
  7  | // before so callers don't need to change — submitCredentials now drives both
  8  | // screens internally.
  9  | export class TaifaSsoPasswordPage {
  10 |   readonly page: Page;
  11 |   readonly idNumberInput: Locator;
  12 |   readonly idContinueButton: Locator;
  13 |   readonly passwordInput: Locator;
  14 |   readonly passwordContinueButton: Locator;
  15 | 
  16 |   constructor(page: Page) {
  17 |     this.page = page;
  18 |     this.idNumberInput = page.getByLabel(/national id or passport/i);
  19 |     this.idContinueButton = page.getByRole('button', { name: /^continue$/i });
  20 |     this.passwordInput = page.getByPlaceholder(/enter your password/i);
  21 |     this.passwordContinueButton = page.getByRole('button', { name: /^continue$/i });
  22 |   }
  23 | 
  24 |   async assertPageLoaded() {
  25 |     await expect(this.page.getByRole('heading', { name: /^sign in$/i })).toBeVisible({ timeout: 15000 });
  26 |     await expect(this.idNumberInput).toBeVisible();
  27 |   }
  28 | 
  29 |   async submitCredentials(idNumber: string, password: string) {
> 30 |     await this.idNumberInput.fill(idNumber);
     |                              ^ Error: locator.fill: value: expected string, got undefined
  31 |     await this.idContinueButton.click();
  32 | 
  33 |     await expect(this.page.getByRole('heading', { name: /enter your password/i })).toBeVisible({ timeout: 15000 });
  34 |     await expect(this.passwordInput).toBeVisible();
  35 |     await this.passwordInput.fill(password);
  36 |     await this.passwordContinueButton.click();
  37 |   }
  38 | }
  39 | 
```