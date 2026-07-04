# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Cross-Service Consistency >> [BUG-XS-004] TC-XS-004 — CORS wildcard * is present even on 401 error responses
- Location: tests/API/deep-regression-api.spec.ts:291:7

# Error details

```
Error: [BUG-XS-004] Dashboard 401 — ACAO must not be wildcard "*" on error response

expect(received).not.toBe(expected) // Object.is equality

Expected: not "*"
```

# Test source

```ts
  227 |       );
  228 |       logger.info('Asserting: Both 401 messages are identical');
  229 |       expect(noAuthMsg, '[BUG-XS-002] 401 message must be identical whether token is absent or invalid')
  230 |         .toBe(badAuthMsg);
  231 |       logger.pass('All assertions passed');
  232 |     });
  233 |   });
  234 | 
  235 |   // ── TC-XS-003 ─────────────────────────────────────────────────────────────
  236 |   test('[BUG-XS-003] TC-XS-003 — Unknown paths return 401 not 404 on dashboard and transaction APIs', async ({ logger }) => {
  237 |     labels('Cross-Service', 'TC-XS-003', 'Unknown Path Returns 401 Not 404', 'medium');
  238 |     await allure.description(
  239 |       '**[BUG-XS-003]**\n\n' +
  240 |       'When an unauthenticated request hits an unknown path, both dashboard and transaction ' +
  241 |       'APIs return 401 instead of 404. This is because the auth middleware runs before routing.\n\n' +
  242 |       '**The problem:** A developer who typos an endpoint path cannot distinguish:\n' +
  243 |       '  - "This path does not exist" (404)\n' +
  244 |       '  - "This path exists but you need auth" (401)\n\n' +
  245 |       '**Contrast:** The analytics API correctly returns 404 for unknown paths (no auth middleware).\n\n' +
  246 |       '**Expected:** Unknown paths should return 404, even to unauthenticated callers. The standard ' +
  247 |       'approach is to route first, then authenticate (known paths exist but require auth → 401; ' +
  248 |       'unknown paths → 404 regardless of auth state).\n\n' +
  249 |       '**Impact:** Developer experience is degraded; documentation gaps are hidden behind 401.',
  250 |     );
  251 | 
  252 |     await logger.step('Step 1 — GET unknown path on dashboard (no auth)', async () => {
  253 |       logger.info('GET /api/v1/dashboard/this-does-not-exist');
  254 |       const res = await GET(DB_BASE, '/api/v1/dashboard/this-does-not-exist');
  255 |       logger.pass('HTTP ' + res.status + ' received');
  256 |       allure.parameter('Dashboard unknown path status', String(res.status));
  257 |       bugAttach('BUG-XS-003',
  258 |         '404 Not Found for unknown path',
  259 |         `HTTP ${res.status} — returns 401 for unknown paths`,
  260 |         'Cannot distinguish wrong path from missing auth; developer experience degraded',
  261 |         'Route before authenticating; return 404 for genuinely unknown routes',
  262 |       );
  263 |       logger.info('Asserting: Unknown dashboard path returns 404');
  264 |       expect(res.status, '[BUG-XS-003] Unknown dashboard path must return 404, not 401').toBe(404);
  265 |       logger.pass('All assertions passed');
  266 |     });
  267 | 
  268 |     await logger.step('Step 2 — GET unknown path on transaction (no auth)', async () => {
  269 |       logger.info('GET /api/v1/transaction/this-does-not-exist');
  270 |       const res = await GET(TXN_BASE, '/api/v1/transaction/this-does-not-exist');
  271 |       logger.pass('HTTP ' + res.status + ' received');
  272 |       allure.parameter('Transaction unknown path status', String(res.status));
  273 |       logger.info('Asserting: Unknown transaction path returns 404');
  274 |       expect(res.status, '[BUG-XS-003] Unknown transaction path must return 404, not 401').toBe(404);
  275 |       logger.pass('All assertions passed');
  276 |     });
  277 | 
  278 |     await logger.step('Step 3 — GET unknown path on analytics (no auth) — should correctly return 404', async () => {
  279 |       logger.info('GET /api/v1/analytics/this-does-not-exist');
  280 |       const res = await GET(ANA_BASE, '/api/v1/analytics/this-does-not-exist');
  281 |       logger.pass('HTTP ' + res.status + ' received');
  282 |       allure.parameter('Analytics unknown path status', String(res.status));
  283 |       allure.parameter('Analytics correctly returns 404', String(res.status === 404));
  284 |       logger.info('Asserting: Analytics correctly returns 404 for unknown path');
  285 |       expect(res.status, 'Analytics correctly returns 404 for unknown path (correct behaviour)').toBe(404);
  286 |       logger.pass('All assertions passed');
  287 |     });
  288 |   });
  289 | 
  290 |   // ── TC-XS-004 ─────────────────────────────────────────────────────────────
  291 |   test('[BUG-XS-004] TC-XS-004 — CORS wildcard * is present even on 401 error responses', async ({ logger }) => {
  292 |     labels('Cross-Service', 'TC-XS-004', 'CORS Wildcard on Error Responses', 'high');
  293 |     await allure.description(
  294 |       '**[BUG-XS-004]**\n\n' +
  295 |       'The Access-Control-Allow-Origin: * header is returned on ALL responses — including 401 ' +
  296 |       'Unauthorized error responses. This means the error body is readable cross-origin by any website.\n\n' +
  297 |       '**What this enables:**\n' +
  298 |       'A malicious page can make a cross-origin request with no token, read the 401 body ' +
  299 |       '(`"Missing Authorization token"`), and use the distinct error messages (BUG-XS-002) ' +
  300 |       'to enumerate token validity from the user\'s browser — all without any credentials.\n\n' +
  301 |       '**Expected:** CORS headers should only be present on successful responses to allowed origins.\n' +
  302 |       'Error responses should not have CORS headers, or should restrict them to the trusted origin.',
  303 |     );
  304 | 
  305 |     const endpoints = [
  306 |       { base: DB_BASE,  path: '/api/v1/dashboard/summary?period=week',           label: 'Dashboard 401' },
  307 |       { base: TXN_BASE, path: '/api/v1/transaction/summary?period=week',          label: 'Transaction 401' },
  308 |       { base: ANA_BASE, path: '/api/v1/analytics/dashboard/summary',              label: 'Analytics 200 (no auth needed)' },
  309 |     ];
  310 | 
  311 |     for (const ep of endpoints) {
  312 |       await logger.step(`Check CORS on ${ep.label}`, async () => {
  313 |         logger.info('GET ' + ep.path);
  314 |         const res = await GET(ep.base, ep.path);
  315 |         logger.pass('HTTP ' + res.status + ' received');
  316 |         const acao = String(res.headers['access-control-allow-origin'] ?? '');
  317 |         allure.parameter(`${ep.label} ACAO`, acao || 'NOT SET');
  318 |         allure.parameter(`${ep.label} status`, String(res.status));
  319 |         bugAttach('BUG-XS-004',
  320 |           'No CORS headers on error responses (or restrict to trusted origin)',
  321 |           `HTTP ${res.status} with ACAO: ${acao}`,
  322 |           'Cross-origin error body is readable by malicious pages; enables enumeration via CORS',
  323 |           'Remove CORS headers from error responses or restrict ACAO to specific RMS frontend origin',
  324 |         );
  325 |         logger.info('Asserting: CORS Access-Control-Allow-Origin is not wildcard on error response');
  326 |         expect(acao, `[BUG-XS-004] ${ep.label} — ACAO must not be wildcard "*" on error response`)
> 327 |           .not.toBe('*');
      |                ^ Error: [BUG-XS-004] Dashboard 401 — ACAO must not be wildcard "*" on error response
  328 |         logger.pass('All assertions passed');
  329 |       });
  330 |     }
  331 |   });
  332 | 
  333 | });
  334 | 
  335 | // ═════════════════════════════════════════════════════════════════════════════
  336 | //  2. ANALYTICS API — DEEP TESTS
  337 | // ═════════════════════════════════════════════════════════════════════════════
  338 | 
  339 | test.describe('Analytics API — Deep Tests', () => {
  340 | 
  341 |   const ANA_EP = '/api/v1/analytics/dashboard/summary';
  342 | 
  343 |   // ── TC-ANA-001 ────────────────────────────────────────────────────────────
  344 |   test('[BUG-ANA-001] TC-ANA-001 — Spring Boot Whitelabel page leaks EAT server timezone on every 406', async ({ logger }) => {
  345 |     labels('Analytics API', 'TC-ANA-001', 'Framework Info Leak via 406 Error', 'medium');
  346 |     await allure.description(
  347 |       '**[BUG-ANA-001]**\n\n' +
  348 |       'When GET /rms-analytics/api/v1/analytics/dashboard/summary is called with `Accept: text/html`, ' +
  349 |       'the server returns 406 Not Acceptable but the body is a Spring Boot Whitelabel Error Page:\n\n' +
  350 |       '```html\n' +
  351 |       '<html><body><h1>Whitelabel Error Page</h1>...\n' +
  352 |       '<div id=\'created\'>Tue Jun 09 15:24:09 EAT 2026</div>...\n' +
  353 |       '```\n\n' +
  354 |       '**Information leaked:**\n' +
  355 |       '1. Spring Boot framework (confirms Java stack)\n' +
  356 |       '2. Server timezone: **EAT** (East Africa Time, UTC+3) — reveals the server\'s geographic region\n' +
  357 |       '3. Exact server timestamp at time of request\n' +
  358 |       '4. Internal URL path: `/api/v1/analytics/dashboard/summary`\n\n' +
  359 |       '**Expected:** 406 with a JSON error body (`{"status":406,"error":"Not Acceptable","message":"..."}`)\n\n' +
  360 |       '**Fix:** Register a global `HttpMediaTypeNotAcceptableException` handler that returns JSON.',
  361 |     );
  362 | 
  363 |     await logger.step('Step 1 — Send GET with Accept: text/html', async () => {
  364 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  365 |       const res = await GET(ANA_BASE, ANA_EP, undefined, { Accept: 'text/html' });
  366 |       logger.pass('HTTP ' + res.status + ' received');
  367 |       allure.parameter('HTTP Status', String(res.status));
  368 |       const body = String(res.text ?? res.body ?? '');
  369 | 
  370 |       const hasWhitelabel   = body.includes('Whitelabel Error Page');
  371 |       const hasSpringBoot   = body.includes('Whitelabel') || body.includes('application has no explicit mapping');
  372 |       const hasEATTimezone  = body.includes('EAT');
  373 |       const hasTimestamp    = /\w{3} \w{3} \d{2} \d{2}:\d{2}:\d{2}/.test(body);
  374 | 
  375 |       allure.parameter('Whitelabel page exposed',   String(hasWhitelabel));
  376 |       allure.parameter('Spring Boot detected',      String(hasSpringBoot));
  377 |       allure.parameter('EAT timezone leaked',       String(hasEATTimezone));
  378 |       allure.parameter('Server timestamp leaked',   String(hasTimestamp));
  379 |       allure.attachment('406 Response Body', body.slice(0, 1000), 'text/plain');
  380 | 
  381 |       bugAttach('BUG-ANA-001',
  382 |         '406 JSON: {"status":406,"error":"Not Acceptable","message":"Only application/json is supported"}',
  383 |         hasWhitelabel ? 'Spring Boot Whitelabel HTML with EAT timezone and server timestamp' : body.slice(0,200),
  384 |         'Exposes Java/Spring Boot stack, server timezone (EAT), and exact server time to any caller',
  385 |         'Add @ControllerAdvice for HttpMediaTypeNotAcceptableException returning JSON 406 response',
  386 |       );
  387 | 
  388 |       logger.info('Asserting: 406 returned without Spring Boot Whitelabel page or EAT timezone leak');
  389 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
  390 |       expect(hasWhitelabel, '[BUG-ANA-001] 406 must NOT expose Spring Boot Whitelabel page').toBe(false);
  391 |       expect(hasEATTimezone, '[BUG-ANA-001] 406 must NOT leak server timezone (EAT)').toBe(false);
  392 |       logger.pass('All assertions passed');
  393 |     });
  394 |   });
  395 | 
  396 |   // ── TC-ANA-002 ────────────────────────────────────────────────────────────
  397 |   test('[BUG-ANA-002] TC-ANA-002 — Analytics 405 body missing "message" field (inconsistent with all other APIs)', async ({ logger }) => {
  398 |     labels('Analytics API', 'TC-ANA-002', '405 Error Body Missing message Field', 'medium');
  399 |     await allure.description(
  400 |       '**[BUG-ANA-002]**\n\n' +
  401 |       'The analytics API 405 body is:\n' +
  402 |       '```json\n{"timestamp":"...","status":405,"error":"Method Not Allowed","path":"..."}\n```\n\n' +
  403 |       'The dashboard and transaction APIs use:\n' +
  404 |       '```json\n{"status":401,"error":"Unauthorized","message":"Missing Authorization token"}\n```\n\n' +
  405 |       'The analytics 405 has `timestamp` and `path` but **no `message`** field.\n' +
  406 |       'The dashboard 401 has `message` but **no `timestamp` or `path`**.\n\n' +
  407 |       '**Expected:** All services must return `{status, error, message, timestamp, path}` for all errors.\n\n' +
  408 |       '**Impact:** Clients cannot reliably read `res.body.message` for analytics errors — they would get `undefined`.',
  409 |     );
  410 | 
  411 |     await logger.step('Step 1 — Trigger 405 on analytics endpoint', async () => {
  412 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  413 |       const res = await METHOD(ANA_BASE, ANA_EP, 'post');
  414 |       logger.pass('HTTP ' + res.status + ' received');
  415 |       logger.info('Asserting: Response is 405');
  416 |       expect(res.status).toBe(405);
  417 |       const body = res.body as Record<string, unknown>;
  418 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  419 |       allure.parameter('Has message',   String('message'   in body));
  420 |       allure.parameter('Has timestamp', String('timestamp' in body));
  421 |       allure.parameter('Has path',      String('path'      in body));
  422 |       bugAttach('BUG-ANA-002',
  423 |         '405 body includes "message" field consistent with all other services',
  424 |         `405 body fields: {${Object.keys(body).join(', ')}} — "message" absent`,
  425 |         'Clients get undefined when reading res.body.message on analytics errors',
  426 |         'Add a "message" field to the global error handler in rms-analytics service',
  427 |       );
```