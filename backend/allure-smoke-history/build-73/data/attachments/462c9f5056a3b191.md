# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-summary.spec.ts >> Smoke â€" Transaction Summary >> TC-SMK-003 â€" Summary: period=custom returns 200 with expected shape
- Location: tests/API/smoke/smoke-summary.spec.ts:243:7

# Error details

```
Error: Token fetch failed: HTTP 404
```

# Test source

```ts
  1  | let _cachedToken: string | null = null;
  2  | 
  3  | export async function getToken(): Promise<string> {
  4  |   if (_cachedToken) return _cachedToken;
  5  | 
  6  |   const baseURL = process.env.BASE_URL ?? 'https://api.rms.dev.demo-fsit.com';
  7  |   const email   = process.env.LOGIN_EMAIL ?? 'ashil.shaji@fortunesoftit.com';
  8  | 
  9  |   const res = await fetch(`${baseURL}/api/v1/auth/test/token`, {
  10 |     method:  'POST',
  11 |     headers: { 'Content-Type': 'application/json' },
  12 |     body:    JSON.stringify({ email }),
  13 |   });
  14 | 
> 15 |   if (!res.ok) throw new Error(`Token fetch failed: HTTP ${res.status}`);
     |                      ^ Error: Token fetch failed: HTTP 404
  16 | 
  17 |   const data = await res.json() as { accessToken: string };
  18 |   _cachedToken = data.accessToken;
  19 |   return _cachedToken;
  20 | }
  21 | 
```