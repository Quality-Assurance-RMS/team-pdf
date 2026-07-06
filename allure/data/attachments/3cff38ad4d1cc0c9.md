# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: auth.setup.ts >> authenticate and save session
- Location: tests/auth.setup.ts:12:6

# Error details

```
Error: expect(page).not.toHaveURL(expected) failed

Expected pattern: not /\/auth\/verification/
Received string: "https://api.rms.dev.demo-fsit.com/auth/verification"
Timeout: 15000ms

Call log:
  - Expect "not toHaveURL" with timeout 15000ms
    34 × unexpected value "https://api.rms.dev.demo-fsit.com/auth/verification"

```

```yaml
- img "Unified Control"
- paragraph: Government services, now at your fingertips
- img
- heading "RMS" [level=1]
- text: National Treasury
- heading "Verification" [level=1]
- paragraph: Please enter the verification code sent to your registered Email ID
- paragraph: ashil.shaji@fortunesoftit.com
- textbox
- textbox
- textbox
- textbox
- text: "OTP expires in: 02:37 Didn't Receive OTP?"
- button "Resend"
- link "Back to Login":
  - /url: /auth/login
- region "Notifications alt+T"
```

# Test source

```ts
  1  | import { test as setup, expect } from '@playwright/test';
  2  | import path from 'path';
  3  | import { LoginPage } from './pages/LoginPage';
  4  | import { VerificationPage } from './pages/VerificationPage';
  5  | import { fetchOtpFromEmail } from './utils/graphMailHelper';
  6  | 
  7  | export const SESSION_FILE = path.resolve(__dirname, '.auth/user.json');
  8  | 
  9  | const EMAIL    = process.env.LOGIN_EMAIL!;
  10 | const PASSWORD = process.env.LOGIN_PASSWORD!;
  11 | 
  12 | setup('authenticate and save session', async ({ page }) => {
  13 |   const loginPage = new LoginPage(page);
  14 |   await loginPage.goto();
  15 | 
  16 |   const loginTime = new Date();
  17 |   await loginPage.login(EMAIL, PASSWORD);
  18 | 
  19 |   const verificationPage = new VerificationPage(page);
  20 |   await verificationPage.assertPageLoaded();
  21 | 
  22 |   const otp = await fetchOtpFromEmail(EMAIL, process.env.OTP_SENDER_EMAIL!, loginTime);
  23 |   await verificationPage.enterOtp(otp);
  24 | 
> 25 |   await expect(page).not.toHaveURL(/\/auth\/verification/, { timeout: 15000 });
     |                          ^ Error: expect(page).not.toHaveURL(expected) failed
  26 | 
  27 |   await page.context().storageState({ path: SESSION_FILE });
  28 | });
  29 | 
```