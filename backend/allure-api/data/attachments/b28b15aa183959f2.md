# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-monthly-revenue.spec.ts >> Backend (Konza) â€” Dashboard Monthly Revenue >> TC-DMREV-010 â€” BUG-MREV-07: Accept: text/html must not expose Spring Boot Whitelabel page
- Location: tests/API/backend-tests/backend-dashboard-monthly-revenue.spec.ts:667:7

# Error details

```
TypeError: fetch failed
```

# Test source

```ts
  1  | // Konza UAT presents a self-signed certificate (unlike the AWS environment's
  2  | // CA-signed cert), which Node rejects by default. This module is only ever
  3  | // imported by tests/API/backend-tests/*.spec.ts, so scope is limited to the
  4  | // Konza suite — run it via `npm run test:konza-backend` rather than folded
  5  | // into a combined `npm test` run, to avoid this leaking into AWS-facing
  6  | // worker processes.
  7  | process.env.NODE_TLS_REJECT_UNAUTHORIZED = '0';
  8  | 
  9  | let _cachedToken: string | null = null;
  10 | 
  11 | export async function getKonzaToken(): Promise<string> {
  12 |   if (_cachedToken) return _cachedToken;
  13 | 
  14 |   const staticToken = process.env.KONZA_BEARER_TOKEN ?? '';
  15 |   if (staticToken) {
  16 |     _cachedToken = staticToken;
  17 |     return _cachedToken;
  18 |   }
  19 | 
  20 |   const baseURL  = process.env.KONZA_BASE_URL ?? 'https://rms.uat.konza';
  21 |   const email    = process.env.KONZA_LOGIN_EMAIL ?? '';
  22 |   const password = process.env.KONZA_LOGIN_PASSWORD ?? '';
  23 | 
> 24 |   const res = await fetch(`${baseURL}/api/v1/auth/login`, {
     |               ^ TypeError: fetch failed
  25 |     method:  'POST',
  26 |     headers: { 'Content-Type': 'application/json' },
  27 |     body:    JSON.stringify({ email, password }),
  28 |   });
  29 | 
  30 |   if (!res.ok) throw new Error(`Konza login failed: HTTP ${res.status}`);
  31 | 
  32 |   const data = await res.json() as { accessToken: string };
  33 |   _cachedToken = data.accessToken;
  34 |   return _cachedToken;
  35 | }
  36 | 
```