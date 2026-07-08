# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Analytics API — Deep Tests >> [BUG-ANA-003] TC-ANA-003 — CORS Access-Control-Allow-Methods advertises POST/DELETE but all return 405
- Location: tests/API/deep-regression-api.spec.ts:435:7

# Error details

```
Error: [BUG-ANA-003] CORS must not advertise POST when it returns 405

expect(received).toBe(expected) // Object.is equality

Expected: false
Received: true
```

# Test source

```ts
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
  428 |       logger.info('Asserting: 405 body includes message field');
  429 |       expect('message' in body, '[BUG-ANA-002] Analytics 405 body must include a "message" field').toBe(true);
  430 |       logger.pass('All assertions passed');
  431 |     });
  432 |   });
  433 | 
  434 |   // ── TC-ANA-003 ────────────────────────────────────────────────────────────
  435 |   test('[BUG-ANA-003] TC-ANA-003 — CORS Access-Control-Allow-Methods advertises POST/DELETE but all return 405', async ({ logger }) => {
  436 |     labels('Analytics API', 'TC-ANA-003', 'CORS Method Mismatch vs Actual Routing', 'normal');
  437 |     await allure.description(
  438 |       '**[BUG-ANA-003]**\n\n' +
  439 |       'The analytics API response headers include:\n' +
  440 |       '`Access-Control-Allow-Methods: GET,POST,PUT,DELETE,PATCH,OPTIONS`\n\n' +
  441 |       'But every method except GET and OPTIONS returns 405 Method Not Allowed.\n\n' +
  442 |       '**Expected:** `Access-Control-Allow-Methods: GET, OPTIONS` — only the methods the endpoint actually supports.\n\n' +
  443 |       '**Impact:** CORS-aware browsers see POST/DELETE as "allowed" and may not pre-flight; ' +
  444 |       'the subsequent request is then rejected by the server with 405. This is confusing and ' +
  445 |       'can break CORS-aware HTTP clients that rely on the advertised methods list.',
  446 |     );
  447 | 
  448 |     await logger.step('Step 1 — Read CORS methods from GET response', async () => {
  449 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  450 |       const res = await GET(ANA_BASE, ANA_EP);
  451 |       logger.pass('HTTP ' + res.status + ' received');
  452 |       const acam = String(res.headers['access-control-allow-methods'] ?? '');
  453 |       allure.parameter('CORS Allow-Methods', acam);
  454 | 
  455 |       const advertisesPost   = acam.toUpperCase().includes('POST');
  456 |       const advertisesDelete = acam.toUpperCase().includes('DELETE');
  457 |       allure.parameter('Advertises POST',   String(advertisesPost));
  458 |       allure.parameter('Advertises DELETE', String(advertisesDelete));
  459 | 
  460 |       bugAttach('BUG-ANA-003',
  461 |         'Access-Control-Allow-Methods: GET, OPTIONS',
  462 |         `Access-Control-Allow-Methods: ${acam}`,
  463 |         'Browser clients may skip preflight for POST/DELETE and then get 405; confusing behavior',
  464 |         'Set CORS allow-methods to only GET, HEAD, OPTIONS in APISIX plugin for analytics routes',
  465 |       );
  466 |       logger.info('Asserting: CORS does not advertise POST or DELETE');
> 467 |       expect(advertisesPost,   '[BUG-ANA-003] CORS must not advertise POST when it returns 405').toBe(false);
      |                                                                                                  ^ Error: [BUG-ANA-003] CORS must not advertise POST when it returns 405
  468 |       expect(advertisesDelete, '[BUG-ANA-003] CORS must not advertise DELETE when it returns 405').toBe(false);
  469 |       logger.pass('All assertions passed');
  470 |     });
  471 | 
  472 |     await logger.step('Step 2 — Confirm POST actually returns 405 (proves the mismatch)', async () => {
  473 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  474 |       const res = await METHOD(ANA_BASE, ANA_EP, 'post');
  475 |       logger.pass('HTTP ' + res.status + ' received');
  476 |       logger.info('Asserting: POST returns 405 confirming CORS mismatch');
  477 |       expect(res.status, 'POST must return 405 — confirms CORS mismatch').toBe(405);
  478 |       allure.parameter('POST actual status', String(res.status));
  479 |       logger.pass('All assertions passed');
  480 |     });
  481 |   });
  482 | 
  483 |   // ── TC-ANA-004 ────────────────────────────────────────────────────────────
  484 |   test('[BUG-ANA-004] TC-ANA-004 — mcda_id=invalid_value returns 200 instead of 400 (no input validation)', async ({ logger }) => {
  485 |     labels('Analytics API', 'TC-ANA-004', 'Invalid mcda_id Accepted Without Validation', 'normal');
  486 |     await allure.description(
  487 |       '**[BUG-ANA-004]**\n\n' +
  488 |       'The `mcda_id` query parameter is supposed to filter analytics data by MCDA organisation.\n\n' +
  489 |       '**Test scenarios:**\n' +
  490 |       '- `?mcda_id=PLATFORM` → 200 with `mcda_id: "ALL"` (filter ignored, no error)\n' +
  491 |       '- `?mcda_id=invalid_xyz` → 200 with `mcda_id: "ALL"` (invalid value silently ignored)\n' +
  492 |       '- `?mcda_id=` (empty) → 200 with `mcda_id: "ALL"` (empty silently ignored)\n\n' +
  493 |       '**Expected:**\n' +
  494 |       '- Valid known mcda_id → returns filtered data with `mcda_id` echoed back\n' +
  495 |       '- Unknown mcda_id → 400 Bad Request (not silently fall back to ALL)\n\n' +
  496 |       '**Impact:** Filtering is completely non-functional. API consumers cannot scope analytics ' +
  497 |       'to a specific MCDA organisation. Invalid values never trigger validation errors.',
  498 |     );
  499 | 
  500 |     const scenarios = [
  501 |       { q: '',                    label: 'No mcda_id param'    },
  502 |       { q: 'mcda_id=PLATFORM',    label: 'Valid org PLATFORM'  },
  503 |       { q: 'mcda_id=invalid_xyz', label: 'Invalid org value'   },
  504 |       { q: 'mcda_id=',            label: 'Empty mcda_id'       },
  505 |     ];
  506 | 
  507 |     const responses: Record<string, unknown>[] = [];
  508 | 
  509 |     for (const s of scenarios) {
  510 |       await logger.step(`GET ?${s.q || '(no param)'} — ${s.label}`, async () => {
  511 |         logger.info('GET ' + (s.q ? `${ANA_EP}?${s.q}` : ANA_EP));
  512 |         const res = await GET(ANA_BASE, s.q ? `${ANA_EP}?${s.q}` : ANA_EP);
  513 |         logger.pass('HTTP ' + res.status + ' received');
  514 |         logger.info('Asserting: Request returns 200 without error');
  515 |         expect(res.status, `${s.label} — request must not error`).toBe(200);
  516 |         responses.push(res.body as Record<string, unknown>);
  517 |         allure.parameter(`${s.label} mcda_id in response`, String((res.body as {mcda_id?:string}).mcda_id ?? 'N/A'));
  518 |         allure.parameter(`${s.label} status`, String(res.status));
  519 |         logger.pass('All assertions passed');
  520 |       });
  521 |     }
  522 | 
  523 |     await logger.step('Assert PLATFORM mcda_id is echoed back (BUG: returns "ALL")', async () => {
  524 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=PLATFORM');
  525 |       const platformRes = await GET(ANA_BASE, `${ANA_EP}?mcda_id=PLATFORM`);
  526 |       logger.pass('HTTP ' + platformRes.status + ' received');
  527 |       const actualMcda  = (platformRes.body as {mcda_id?:string}).mcda_id;
  528 |       bugAttach('BUG-ANA-004',
  529 |         'mcda_id=PLATFORM → response mcda_id="PLATFORM" with filtered data',
  530 |         `mcda_id=PLATFORM → response mcda_id="${actualMcda}" (param ignored)`,
  531 |         'Analytics data cannot be scoped by MCDA; filtering feature is completely non-functional',
  532 |         'Implement mcda_id filtering in analytics query; return 400 for unknown mcda_id values',
  533 |       );
  534 |       logger.info('Asserting: mcda_id=PLATFORM is echoed back in response');
  535 |       expect(actualMcda, '[BUG-ANA-004] ?mcda_id=PLATFORM must return mcda_id="PLATFORM"').toBe('PLATFORM');
  536 |       logger.pass('All assertions passed');
  537 |     });
  538 | 
  539 |     await logger.step('Assert invalid mcda_id returns 400 (BUG: returns 200)', async () => {
  540 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=invalid_xyz');
  541 |       const invalidRes = await GET(ANA_BASE, `${ANA_EP}?mcda_id=invalid_xyz`);
  542 |       logger.pass('HTTP ' + invalidRes.status + ' received');
  543 |       bugAttach('BUG-ANA-004',
  544 |         'mcda_id=invalid_xyz → 400 Bad Request',
  545 |         `mcda_id=invalid_xyz → HTTP ${invalidRes.status} with mcda_id="ALL"`,
  546 |         'Invalid filter values accepted silently — API has no input validation on mcda_id',
  547 |         'Validate mcda_id against known enum values; return 400 for unrecognised values',
  548 |       );
  549 |       logger.info('Asserting: Invalid mcda_id returns 400');
  550 |       expect(invalidRes.status, '[BUG-ANA-004] invalid mcda_id must return 400').toBe(400);
  551 |       logger.pass('All assertions passed');
  552 |     });
  553 |   });
  554 | 
  555 |   // ── TC-ANA-005 ────────────────────────────────────────────────────────────
  556 |   test('[BUG-ANA-005] TC-ANA-005 — Analytics returns all-zero counts (possible aggregation or data pipeline issue)', async ({ logger }) => {
  557 |     labels('Analytics API', 'TC-ANA-005', 'All-Zero Analytics Counts', 'high');
  558 |     await allure.description(
  559 |       '**[BUG-ANA-005]**\n\n' +
  560 |       'Every call to the analytics endpoint returns all zeros:\n' +
  561 |       '```json\n' +
  562 |       '{"today":{"total_transactions":0,"recon_matched":0,"recon_exceptions":0,\n' +
  563 |       '"failed_transactions":0,"settlement_total_kes":0.0,"successful_transactions":0,\n' +
  564 |       '"total_amount_kes":0.0,"success_rate_pct":0.0},"mcda_id":"ALL"}\n' +
  565 |       '```\n\n' +
  566 |       '**Observation:** The smoke test file (smoke-analytics-dashboard-summary.spec.ts) recorded ' +
  567 |       '`total_transactions=135` in a prior session, proving data does exist. The current ' +
```