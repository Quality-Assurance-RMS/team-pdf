# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Analytics API — Deep Tests >> [BUG-ANA-002] TC-ANA-002 — Analytics 405 body missing "message" field (inconsistent with all other APIs)
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:405:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 405
Received: 502
```

# Test source

```ts
  324 |         const acao = String(res.headers['access-control-allow-origin'] ?? '');
  325 |         allure.parameter(`${ep.label} ACAO`, acao || 'NOT SET');
  326 |         allure.parameter(`${ep.label} status`, String(res.status));
  327 |         bugAttach('BUG-XS-004',
  328 |           'No CORS headers on error responses (or restrict to trusted origin)',
  329 |           `HTTP ${res.status} with ACAO: ${acao}`,
  330 |           'Cross-origin error body is readable by malicious pages; enables enumeration via CORS',
  331 |           'Remove CORS headers from error responses or restrict ACAO to specific RMS frontend origin',
  332 |         );
  333 |         logger.info('Asserting: CORS Access-Control-Allow-Origin is not wildcard on error response');
  334 |         expect(acao, `[BUG-XS-004] ${ep.label} — ACAO must not be wildcard "*" on error response`)
  335 |           .not.toBe('*');
  336 |         logger.pass('All assertions passed');
  337 |       });
  338 |     }
  339 |   });
  340 | 
  341 | });
  342 | 
  343 | // ═════════════════════════════════════════════════════════════════════════════
  344 | //  2. ANALYTICS API — DEEP TESTS
  345 | // ═════════════════════════════════════════════════════════════════════════════
  346 | 
  347 | test.describe('Analytics API — Deep Tests', () => {
  348 | 
  349 |   const ANA_EP = '/api/v1/analytics/dashboard/summary';
  350 | 
  351 |   // ── TC-ANA-001 ────────────────────────────────────────────────────────────
  352 |   test('[BUG-ANA-001] TC-ANA-001 — Spring Boot Whitelabel page leaks EAT server timezone on every 406', async ({ logger }) => {
  353 |     labels('Analytics API', 'TC-ANA-001', 'Framework Info Leak via 406 Error', 'medium');
  354 |     await allure.description(
  355 |       '**[BUG-ANA-001]**\n\n' +
  356 |       'When GET /rms-analytics/api/v1/analytics/dashboard/summary is called with `Accept: text/html`, ' +
  357 |       'the server returns 406 Not Acceptable but the body is a Spring Boot Whitelabel Error Page:\n\n' +
  358 |       '```html\n' +
  359 |       '<html><body><h1>Whitelabel Error Page</h1>...\n' +
  360 |       '<div id=\'created\'>Tue Jun 09 15:24:09 EAT 2026</div>...\n' +
  361 |       '```\n\n' +
  362 |       '**Information leaked:**\n' +
  363 |       '1. Spring Boot framework (confirms Java stack)\n' +
  364 |       '2. Server timezone: **EAT** (East Africa Time, UTC+3) — reveals the server\'s geographic region\n' +
  365 |       '3. Exact server timestamp at time of request\n' +
  366 |       '4. Internal URL path: `/api/v1/analytics/dashboard/summary`\n\n' +
  367 |       '**Expected:** 406 with a JSON error body (`{"status":406,"error":"Not Acceptable","message":"..."}`)\n\n' +
  368 |       '**Fix:** Register a global `HttpMediaTypeNotAcceptableException` handler that returns JSON.',
  369 |     );
  370 | 
  371 |     await logger.step('Step 1 — Send GET with Accept: text/html', async () => {
  372 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  373 |       const res = await GET(ANA_BASE, ANA_EP, undefined, { Accept: 'text/html' });
  374 |       logger.pass('HTTP ' + res.status + ' received');
  375 |       allure.parameter('HTTP Status', String(res.status));
  376 |       const body = String(res.text ?? res.body ?? '');
  377 | 
  378 |       const hasWhitelabel   = body.includes('Whitelabel Error Page');
  379 |       const hasSpringBoot   = body.includes('Whitelabel') || body.includes('application has no explicit mapping');
  380 |       const hasEATTimezone  = body.includes('EAT');
  381 |       const hasTimestamp    = /\w{3} \w{3} \d{2} \d{2}:\d{2}:\d{2}/.test(body);
  382 | 
  383 |       allure.parameter('Whitelabel page exposed',   String(hasWhitelabel));
  384 |       allure.parameter('Spring Boot detected',      String(hasSpringBoot));
  385 |       allure.parameter('EAT timezone leaked',       String(hasEATTimezone));
  386 |       allure.parameter('Server timestamp leaked',   String(hasTimestamp));
  387 |       allure.attachment('406 Response Body', body.slice(0, 1000), 'text/plain');
  388 | 
  389 |       bugAttach('BUG-ANA-001',
  390 |         '406 JSON: {"status":406,"error":"Not Acceptable","message":"Only application/json is supported"}',
  391 |         hasWhitelabel ? 'Spring Boot Whitelabel HTML with EAT timezone and server timestamp' : body.slice(0,200),
  392 |         'Exposes Java/Spring Boot stack, server timezone (EAT), and exact server time to any caller',
  393 |         'Add @ControllerAdvice for HttpMediaTypeNotAcceptableException returning JSON 406 response',
  394 |       );
  395 | 
  396 |       logger.info('Asserting: 406 returned without Spring Boot Whitelabel page or EAT timezone leak');
  397 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
  398 |       expect(hasWhitelabel, '[BUG-ANA-001] 406 must NOT expose Spring Boot Whitelabel page').toBe(false);
  399 |       expect(hasEATTimezone, '[BUG-ANA-001] 406 must NOT leak server timezone (EAT)').toBe(false);
  400 |       logger.pass('All assertions passed');
  401 |     });
  402 |   });
  403 | 
  404 |   // ── TC-ANA-002 ────────────────────────────────────────────────────────────
  405 |   test('[BUG-ANA-002] TC-ANA-002 — Analytics 405 body missing "message" field (inconsistent with all other APIs)', async ({ logger }) => {
  406 |     labels('Analytics API', 'TC-ANA-002', '405 Error Body Missing message Field', 'medium');
  407 |     await allure.description(
  408 |       '**[BUG-ANA-002]**\n\n' +
  409 |       'The analytics API 405 body is:\n' +
  410 |       '```json\n{"timestamp":"...","status":405,"error":"Method Not Allowed","path":"..."}\n```\n\n' +
  411 |       'The dashboard and transaction APIs use:\n' +
  412 |       '```json\n{"status":401,"error":"Unauthorized","message":"Missing Authorization token"}\n```\n\n' +
  413 |       'The analytics 405 has `timestamp` and `path` but **no `message`** field.\n' +
  414 |       'The dashboard 401 has `message` but **no `timestamp` or `path`**.\n\n' +
  415 |       '**Expected:** All services must return `{status, error, message, timestamp, path}` for all errors.\n\n' +
  416 |       '**Impact:** Clients cannot reliably read `res.body.message` for analytics errors — they would get `undefined`.',
  417 |     );
  418 | 
  419 |     await logger.step('Step 1 — Trigger 405 on analytics endpoint', async () => {
  420 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  421 |       const res = await METHOD(ANA_BASE, ANA_EP, 'post');
  422 |       logger.pass('HTTP ' + res.status + ' received');
  423 |       logger.info('Asserting: Response is 405');
> 424 |       expect(res.status).toBe(405);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
  425 |       const body = res.body as Record<string, unknown>;
  426 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  427 |       allure.parameter('Has message',   String('message'   in body));
  428 |       allure.parameter('Has timestamp', String('timestamp' in body));
  429 |       allure.parameter('Has path',      String('path'      in body));
  430 |       bugAttach('BUG-ANA-002',
  431 |         '405 body includes "message" field consistent with all other services',
  432 |         `405 body fields: {${Object.keys(body).join(', ')}} — "message" absent`,
  433 |         'Clients get undefined when reading res.body.message on analytics errors',
  434 |         'Add a "message" field to the global error handler in rms-analytics service',
  435 |       );
  436 |       logger.info('Asserting: 405 body includes message field');
  437 |       expect('message' in body, '[BUG-ANA-002] Analytics 405 body must include a "message" field').toBe(true);
  438 |       logger.pass('All assertions passed');
  439 |     });
  440 |   });
  441 | 
  442 |   // ── TC-ANA-003 ────────────────────────────────────────────────────────────
  443 |   test('[BUG-ANA-003] TC-ANA-003 — CORS Access-Control-Allow-Methods advertises POST/DELETE but all return 405', async ({ logger }) => {
  444 |     labels('Analytics API', 'TC-ANA-003', 'CORS Method Mismatch vs Actual Routing', 'normal');
  445 |     await allure.description(
  446 |       '**[BUG-ANA-003]**\n\n' +
  447 |       'The analytics API response headers include:\n' +
  448 |       '`Access-Control-Allow-Methods: GET,POST,PUT,DELETE,PATCH,OPTIONS`\n\n' +
  449 |       'But every method except GET and OPTIONS returns 405 Method Not Allowed.\n\n' +
  450 |       '**Expected:** `Access-Control-Allow-Methods: GET, OPTIONS` — only the methods the endpoint actually supports.\n\n' +
  451 |       '**Impact:** CORS-aware browsers see POST/DELETE as "allowed" and may not pre-flight; ' +
  452 |       'the subsequent request is then rejected by the server with 405. This is confusing and ' +
  453 |       'can break CORS-aware HTTP clients that rely on the advertised methods list.',
  454 |     );
  455 | 
  456 |     await logger.step('Step 1 — Read CORS methods from GET response', async () => {
  457 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  458 |       const res = await GET(ANA_BASE, ANA_EP);
  459 |       logger.pass('HTTP ' + res.status + ' received');
  460 |       const acam = String(res.headers['access-control-allow-methods'] ?? '');
  461 |       allure.parameter('CORS Allow-Methods', acam);
  462 | 
  463 |       const advertisesPost   = acam.toUpperCase().includes('POST');
  464 |       const advertisesDelete = acam.toUpperCase().includes('DELETE');
  465 |       allure.parameter('Advertises POST',   String(advertisesPost));
  466 |       allure.parameter('Advertises DELETE', String(advertisesDelete));
  467 | 
  468 |       bugAttach('BUG-ANA-003',
  469 |         'Access-Control-Allow-Methods: GET, OPTIONS',
  470 |         `Access-Control-Allow-Methods: ${acam}`,
  471 |         'Browser clients may skip preflight for POST/DELETE and then get 405; confusing behavior',
  472 |         'Set CORS allow-methods to only GET, HEAD, OPTIONS in APISIX plugin for analytics routes',
  473 |       );
  474 |       logger.info('Asserting: CORS does not advertise POST or DELETE');
  475 |       expect(advertisesPost,   '[BUG-ANA-003] CORS must not advertise POST when it returns 405').toBe(false);
  476 |       expect(advertisesDelete, '[BUG-ANA-003] CORS must not advertise DELETE when it returns 405').toBe(false);
  477 |       logger.pass('All assertions passed');
  478 |     });
  479 | 
  480 |     await logger.step('Step 2 — Confirm POST actually returns 405 (proves the mismatch)', async () => {
  481 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  482 |       const res = await METHOD(ANA_BASE, ANA_EP, 'post');
  483 |       logger.pass('HTTP ' + res.status + ' received');
  484 |       logger.info('Asserting: POST returns 405 confirming CORS mismatch');
  485 |       expect(res.status, 'POST must return 405 — confirms CORS mismatch').toBe(405);
  486 |       allure.parameter('POST actual status', String(res.status));
  487 |       logger.pass('All assertions passed');
  488 |     });
  489 |   });
  490 | 
  491 |   // ── TC-ANA-004 ────────────────────────────────────────────────────────────
  492 |   test('[BUG-ANA-004] TC-ANA-004 — mcda_id=invalid_value returns 200 instead of 400 (no input validation)', async ({ logger }) => {
  493 |     labels('Analytics API', 'TC-ANA-004', 'Invalid mcda_id Accepted Without Validation', 'normal');
  494 |     await allure.description(
  495 |       '**[BUG-ANA-004]**\n\n' +
  496 |       'The `mcda_id` query parameter is supposed to filter analytics data by MCDA organisation.\n\n' +
  497 |       '**Test scenarios:**\n' +
  498 |       '- `?mcda_id=PLATFORM` → 200 with `mcda_id: "ALL"` (filter ignored, no error)\n' +
  499 |       '- `?mcda_id=invalid_xyz` → 200 with `mcda_id: "ALL"` (invalid value silently ignored)\n' +
  500 |       '- `?mcda_id=` (empty) → 200 with `mcda_id: "ALL"` (empty silently ignored)\n\n' +
  501 |       '**Expected:**\n' +
  502 |       '- Valid known mcda_id → returns filtered data with `mcda_id` echoed back\n' +
  503 |       '- Unknown mcda_id → 400 Bad Request (not silently fall back to ALL)\n\n' +
  504 |       '**Impact:** Filtering is completely non-functional. API consumers cannot scope analytics ' +
  505 |       'to a specific MCDA organisation. Invalid values never trigger validation errors.',
  506 |     );
  507 | 
  508 |     const scenarios = [
  509 |       { q: '',                    label: 'No mcda_id param'    },
  510 |       { q: 'mcda_id=PLATFORM',    label: 'Valid org PLATFORM'  },
  511 |       { q: 'mcda_id=invalid_xyz', label: 'Invalid org value'   },
  512 |       { q: 'mcda_id=',            label: 'Empty mcda_id'       },
  513 |     ];
  514 | 
  515 |     const responses: Record<string, unknown>[] = [];
  516 | 
  517 |     for (const s of scenarios) {
  518 |       await logger.step(`GET ?${s.q || '(no param)'} — ${s.label}`, async () => {
  519 |         logger.info('GET ' + (s.q ? `${ANA_EP}?${s.q}` : ANA_EP));
  520 |         const res = await GET(ANA_BASE, s.q ? `${ANA_EP}?${s.q}` : ANA_EP);
  521 |         logger.pass('HTTP ' + res.status + ' received');
  522 |         logger.info('Asserting: Request returns 200 without error');
  523 |         expect(res.status, `${s.label} — request must not error`).toBe(200);
  524 |         responses.push(res.body as Record<string, unknown>);
```