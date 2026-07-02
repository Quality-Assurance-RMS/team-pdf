# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> OAuth Token Service >> [BUG-OT-009] TC-OT-006 — GET /api/v1/token: Response must include Cache-Control: no-store
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:357:7

# Error details

```
Error: [BUG-OT-009] Cache-Control must contain "no-store"

expect(received).toContain(expected) // indexOf

Expected substring: "no-store"
Received string:    ""
```

# Test source

```ts
  294 |       logger.info('Asserting: HTTP status is 200');
  295 |       assertOk(res!, 'TC-OT-004');
  296 |       logger.pass('All assertions passed');
  297 |     });
  298 |     await logger.step('Step 3 — Parse expires_at and assert it is in the future', async () => {
  299 |       logger.info('Asserting: expires_at is a parseable timestamp in the future');
  300 |       const exp  = new Date(res!.body.expires_at as string);
  301 |       expect(isNaN(exp.getTime()), 'expires_at must be parseable').toBe(false);
  302 |       expect(exp.getTime(), 'expires_at must be in the future').toBeGreaterThan(new Date().getTime());
  303 |       const hoursLeft = ((exp.getTime() - Date.now()) / 3_600_000).toFixed(2);
  304 |       allure.parameter('expires_at',        String(res!.body.expires_at));
  305 |       allure.parameter('Hours until expiry', hoursLeft);
  306 |       logger.pass('All assertions passed');
  307 |     });
  308 |   });
  309 | 
  310 |   // ──────────────────────────────────────────────────────────────────────────
  311 |   // TC-OT-005 — Time window: issued_at < now < expires_at
  312 |   // ──────────────────────────────────────────────────────────────────────────
  313 |   test('TC-OT-005 — GET /api/v1/token: issued_at < now < expires_at (valid time window)', async ({ logger }) => {
  314 |     labelsOT('TC-OT-005', 'Token Time Window', 'normal');
  315 |     await allure.description(
  316 |       '**What this test does:**\n' +
  317 |       'Validates the token time window is logically consistent:\n' +
  318 |       '- issued_at is in the past (token was issued before now)\n' +
  319 |       '- expires_at is in the future (token has not yet expired)\n' +
  320 |       '- issued_at < expires_at (the window is non-zero and correctly ordered)\n\n' +
  321 |       '**Why it matters:** An inverted or zero-width time window indicates a clock-skew ' +
  322 |       'problem or a misconfigured token lifetime, which would immediately invalidate the token.',
  323 |     );
  324 |     let res: supertest.Response;
  325 |     await logger.step('Step 1 — Send GET /api/v1/token', async () => {
  326 |       logger.info('GET /api/v1/token');
  327 |       res = await GET(OAUTH_BASE, '/api/v1/token', BEARER);
  328 |       logger.pass('HTTP ' + res.status + ' received');
  329 |       attach('Request & Response', 'GET', OAUTH_BASE, '/api/v1/token', res);
  330 |     });
  331 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  332 |       logger.info('Asserting: HTTP status is 200');
  333 |       assertOk(res!, 'TC-OT-005');
  334 |       logger.pass('All assertions passed');
  335 |     });
  336 |     await logger.step('Step 3 — Validate issued_at < now < expires_at', async () => {
  337 |       logger.info('Asserting: issued_at is in the past and expires_at is in the future');
  338 |       const iat = new Date(res!.body.issued_at  as string);
  339 |       const exp = new Date(res!.body.expires_at as string);
  340 |       const now = new Date();
  341 |       expect(isNaN(iat.getTime()), 'issued_at must be parseable').toBe(false);
  342 |       expect(isNaN(exp.getTime()), 'expires_at must be parseable').toBe(false);
  343 |       expect(iat.getTime(), 'issued_at must be in the past').toBeLessThan(now.getTime());
  344 |       expect(exp.getTime(), 'expires_at must be in the future').toBeGreaterThan(now.getTime());
  345 |       expect(iat.getTime(), 'issued_at must be before expires_at').toBeLessThan(exp.getTime());
  346 |       const lifetimeHours = ((exp.getTime() - iat.getTime()) / 3_600_000).toFixed(2);
  347 |       allure.parameter('issued_at',              String(res!.body.issued_at));
  348 |       allure.parameter('expires_at',             String(res!.body.expires_at));
  349 |       allure.parameter('Lifetime (hours)',        lifetimeHours);
  350 |       logger.pass('All assertions passed');
  351 |     });
  352 |   });
  353 | 
  354 |   // ──────────────────────────────────────────────────────────────────────────
  355 |   // TC-OT-006 — [BUG-OT-009] No Cache-Control on token response
  356 |   // ──────────────────────────────────────────────────────────────────────────
  357 |   test('[BUG-OT-009] TC-OT-006 — GET /api/v1/token: Response must include Cache-Control: no-store', async ({ logger }) => {
  358 |     labelsOT('TC-OT-006', 'Cache-Control Header on Token Response', 'medium');
  359 |     await allure.description(
  360 |       '**[KNOWN BUG — BUG-OT-009]**\n\n' +
  361 |       '**What this test does:**\n' +
  362 |       'Checks that the GET /api/v1/token response includes a Cache-Control: no-store header.\n\n' +
  363 |       '**Why it matters:**\n' +
  364 |       'The response contains a sensitive Bearer token (access_token). Without Cache-Control: no-store, ' +
  365 |       'HTTP proxies, CDNs, and browser caches may store this token. Any system that intercepts ' +
  366 |       'or caches this response gains access to the KCB API token.\n\n' +
  367 |       '**RFC 6749 §10.3 (OAuth 2.0):** Token responses that contain access tokens MUST include ' +
  368 |       '"Cache-Control: no-store" and "Pragma: no-cache" to prevent token leakage through caches.\n\n' +
  369 |       '**Actual behaviour:** The response contains no Cache-Control header at all.',
  370 |     );
  371 |     let res: supertest.Response;
  372 |     await logger.step('Step 1 — Send GET /api/v1/token', async () => {
  373 |       logger.info('GET /api/v1/token');
  374 |       res = await GET(OAUTH_BASE, '/api/v1/token', BEARER);
  375 |       logger.pass('HTTP ' + res.status + ' received');
  376 |       attach('Request & Response', 'GET', OAUTH_BASE, '/api/v1/token', res);
  377 |       allure.parameter('Cache-Control header', String(res.headers['cache-control'] ?? 'NOT PRESENT'));
  378 |       allure.parameter('Pragma header',        String(res.headers['pragma']        ?? 'NOT PRESENT'));
  379 |     });
  380 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  381 |       logger.info('Asserting: HTTP status is 200');
  382 |       assertOk(res!, 'TC-OT-006');
  383 |       logger.pass('All assertions passed');
  384 |     });
  385 |     await logger.step('Step 3 — Assert Cache-Control: no-store is present (BUG: header absent)', async () => {
  386 |       logger.info('Asserting: Cache-Control header contains "no-store"');
  387 |       const cc = String(res!.headers['cache-control'] ?? '').toLowerCase();
  388 |       bugEvidence('BUG-OT-009',
  389 |         'Cache-Control: no-store (RFC 6749 §10.3)',
  390 |         `Cache-Control: "${cc || 'NOT PRESENT'}"`,
  391 |         'Token response may be cached by proxies/CDNs, leaking the KCB access_token',
  392 |         'Add Cache-Control: no-store, Pragma: no-cache to token endpoint response headers',
  393 |       );
> 394 |       expect(cc, '[BUG-OT-009] Cache-Control must contain "no-store"').toContain('no-store');
      |                                                                        ^ Error: [BUG-OT-009] Cache-Control must contain "no-store"
  395 |       logger.pass('All assertions passed');
  396 |     });
  397 |   });
  398 | 
  399 |   // ──────────────────────────────────────────────────────────────────────────
  400 |   // TC-OT-007 — DELETE /token/cache with valid auth → 204
  401 |   // ──────────────────────────────────────────────────────────────────────────
  402 |   test('TC-OT-007 — DELETE /api/v1/token/cache: Returns 204 No Content with valid auth', async ({ logger }) => {
  403 |     labelsOT('TC-OT-007', 'Token Cache Eviction', 'normal');
  404 |     await allure.description(
  405 |       '**What this test does:**\n' +
  406 |       'Calls DELETE /api/v1/token/cache with a valid Bearer token and verifies the response is ' +
  407 |       '204 No Content with an empty body.\n\n' +
  408 |       '**Why it matters:** Cache eviction is the operational mechanism for forcing a token refresh ' +
  409 |       'before the normal expiry (e.g. after a secret rotation or a token compromise). ' +
  410 |       'If this endpoint is broken, operators cannot force-refresh the KCB token on demand.',
  411 |     );
  412 |     let res: supertest.Response;
  413 |     await logger.step('Step 1 — Send DELETE /api/v1/token/cache with auth', async () => {
  414 |       allure.parameter('Endpoint', 'DELETE /oauth-token/api/v1/token/cache');
  415 |       allure.parameter('Auth',     'Valid Bearer token');
  416 |       logger.info('DELETE /api/v1/token/cache');
  417 |       res = await DELETE(OAUTH_BASE, '/api/v1/token/cache', BEARER);
  418 |       logger.pass('HTTP ' + res.status + ' received');
  419 |       attach('Request & Response', 'DELETE', OAUTH_BASE, '/api/v1/token/cache', res);
  420 |       allure.parameter('HTTP Status', String(res.status));
  421 |     });
  422 |     await logger.step('Step 2 — Assert HTTP 204', async () => {
  423 |       logger.info('Asserting: HTTP status is 204');
  424 |       expect(res!.status, `DELETE /token/cache must return 204, got ${res!.status}`).toBe(204);
  425 |       logger.pass('All assertions passed');
  426 |     });
  427 |     await logger.step('Step 3 — Assert response body is empty', async () => {
  428 |       logger.info('Asserting: response body is empty for 204');
  429 |       const bodyStr = JSON.stringify(res!.body);
  430 |       expect(['{}', 'null', ''].includes(bodyStr), '204 body must be empty').toBe(true);
  431 |       logger.pass('All assertions passed');
  432 |     });
  433 |   });
  434 | 
  435 |   // ──────────────────────────────────────────────────────────────────────────
  436 |   // TC-OT-008 — Cache eviction + re-fetch cycle
  437 |   // ──────────────────────────────────────────────────────────────────────────
  438 |   test('TC-OT-008 — Cache eviction + re-fetch: new token issued_at >= original issued_at', async ({ logger }) => {
  439 |     labelsOT('TC-OT-008', 'Cache Eviction and Token Refresh Cycle', 'high');
  440 |     await allure.description(
  441 |       '**What this test does:**\n' +
  442 |       'Executes the full cache eviction cycle in three steps:\n' +
  443 |       '1. GET /token → record Token A (issued_at = TA)\n' +
  444 |       '2. DELETE /token/cache → evict the cached token (expect 204)\n' +
  445 |       '3. GET /token → record Token B (issued_at = TB)\n\n' +
  446 |       'Asserts that TB >= TA — Token B is at least as fresh as Token A, meaning the eviction ' +
  447 |       'caused the service to re-fetch or serve the same still-valid token.\n\n' +
  448 |       '**Why it matters:** This proves the eviction-then-refresh path works end-to-end.',
  449 |     );
  450 |     let issuedAtA = '';
  451 |     let tokenA    = '';
  452 |     let issuedAtB = '';
  453 |     let tokenB    = '';
  454 |     await logger.step('Step 1 — GET /token → capture Token A', async () => {
  455 |       logger.info('GET /api/v1/token');
  456 |       const r = await GET(OAUTH_BASE, '/api/v1/token', BEARER);
  457 |       logger.pass('HTTP ' + r.status + ' received');
  458 |       assertOk(r, 'TC-OT-008 Token A');
  459 |       tokenA    = String(r.body.access_token ?? '');
  460 |       issuedAtA = String(r.body.issued_at    ?? '');
  461 |       allure.parameter('Token A issued_at', issuedAtA);
  462 |       logger.pass('All assertions passed');
  463 |     });
  464 |     await logger.step('Step 2 — DELETE /token/cache → evict cache', async () => {
  465 |       logger.info('DELETE /api/v1/token/cache');
  466 |       const r = await DELETE(OAUTH_BASE, '/api/v1/token/cache', BEARER);
  467 |       logger.pass('HTTP ' + r.status + ' received');
  468 |       logger.info('Asserting: cache eviction returns 204');
  469 |       expect(r.status, 'Cache eviction must return 204').toBe(204);
  470 |       allure.parameter('Eviction status', String(r.status));
  471 |       logger.pass('All assertions passed');
  472 |     });
  473 |     await logger.step('Step 3 — GET /token → capture Token B', async () => {
  474 |       logger.info('GET /api/v1/token');
  475 |       const r = await GET(OAUTH_BASE, '/api/v1/token', BEARER);
  476 |       logger.pass('HTTP ' + r.status + ' received');
  477 |       assertOk(r, 'TC-OT-008 Token B');
  478 |       tokenB    = String(r.body.access_token ?? '');
  479 |       issuedAtB = String(r.body.issued_at    ?? '');
  480 |       allure.parameter('Token B issued_at', issuedAtB);
  481 |       logger.pass('All assertions passed');
  482 |     });
  483 |     await logger.step('Step 4 — Assert Token B is fresh (TB >= TA)', async () => {
  484 |       logger.info('Asserting: Token B issued_at >= Token A issued_at');
  485 |       const dateA = new Date(issuedAtA).getTime();
  486 |       const dateB = new Date(issuedAtB).getTime();
  487 |       expect(dateB, 'Token B issued_at must be >= Token A issued_at after eviction').toBeGreaterThanOrEqual(dateA);
  488 |       allure.parameter('Token renewed', tokenA !== tokenB ? 'YES — new token issued' : 'SAME token (still cached)');
  489 |       logger.pass('All assertions passed');
  490 |     });
  491 |   });
  492 | 
  493 |   // ──────────────────────────────────────────────────────────────────────────
  494 |   // TC-OT-009 — [BUG-OT-001] No auth → expect 401, actual 504
```