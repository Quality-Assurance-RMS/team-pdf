# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Analytics API — Deep Tests >> [BUG-ANA-004] TC-ANA-004 — mcda_id=invalid_value returns 200 instead of 400 (no input validation)
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:492:7

# Error details

```
Error: No mcda_id param — request must not error

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  423 |       logger.info('Asserting: Response is 405');
  424 |       expect(res.status).toBe(405);
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
> 523 |         expect(res.status, `${s.label} — request must not error`).toBe(200);
      |                                                                   ^ Error: No mcda_id param — request must not error
  524 |         responses.push(res.body as Record<string, unknown>);
  525 |         allure.parameter(`${s.label} mcda_id in response`, String((res.body as {mcda_id?:string}).mcda_id ?? 'N/A'));
  526 |         allure.parameter(`${s.label} status`, String(res.status));
  527 |         logger.pass('All assertions passed');
  528 |       });
  529 |     }
  530 | 
  531 |     await logger.step('Assert PLATFORM mcda_id is echoed back (BUG: returns "ALL")', async () => {
  532 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=PLATFORM');
  533 |       const platformRes = await GET(ANA_BASE, `${ANA_EP}?mcda_id=PLATFORM`);
  534 |       logger.pass('HTTP ' + platformRes.status + ' received');
  535 |       const actualMcda  = (platformRes.body as {mcda_id?:string}).mcda_id;
  536 |       bugAttach('BUG-ANA-004',
  537 |         'mcda_id=PLATFORM → response mcda_id="PLATFORM" with filtered data',
  538 |         `mcda_id=PLATFORM → response mcda_id="${actualMcda}" (param ignored)`,
  539 |         'Analytics data cannot be scoped by MCDA; filtering feature is completely non-functional',
  540 |         'Implement mcda_id filtering in analytics query; return 400 for unknown mcda_id values',
  541 |       );
  542 |       logger.info('Asserting: mcda_id=PLATFORM is echoed back in response');
  543 |       expect(actualMcda, '[BUG-ANA-004] ?mcda_id=PLATFORM must return mcda_id="PLATFORM"').toBe('PLATFORM');
  544 |       logger.pass('All assertions passed');
  545 |     });
  546 | 
  547 |     await logger.step('Assert invalid mcda_id returns 400 (BUG: returns 200)', async () => {
  548 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=invalid_xyz');
  549 |       const invalidRes = await GET(ANA_BASE, `${ANA_EP}?mcda_id=invalid_xyz`);
  550 |       logger.pass('HTTP ' + invalidRes.status + ' received');
  551 |       bugAttach('BUG-ANA-004',
  552 |         'mcda_id=invalid_xyz → 400 Bad Request',
  553 |         `mcda_id=invalid_xyz → HTTP ${invalidRes.status} with mcda_id="ALL"`,
  554 |         'Invalid filter values accepted silently — API has no input validation on mcda_id',
  555 |         'Validate mcda_id against known enum values; return 400 for unrecognised values',
  556 |       );
  557 |       logger.info('Asserting: Invalid mcda_id returns 400');
  558 |       expect(invalidRes.status, '[BUG-ANA-004] invalid mcda_id must return 400').toBe(400);
  559 |       logger.pass('All assertions passed');
  560 |     });
  561 |   });
  562 | 
  563 |   // ── TC-ANA-005 ────────────────────────────────────────────────────────────
  564 |   test('[BUG-ANA-005] TC-ANA-005 — Analytics returns all-zero counts (possible aggregation or data pipeline issue)', async ({ logger }) => {
  565 |     labels('Analytics API', 'TC-ANA-005', 'All-Zero Analytics Counts', 'high');
  566 |     await allure.description(
  567 |       '**[BUG-ANA-005]**\n\n' +
  568 |       'Every call to the analytics endpoint returns all zeros:\n' +
  569 |       '```json\n' +
  570 |       '{"today":{"total_transactions":0,"recon_matched":0,"recon_exceptions":0,\n' +
  571 |       '"failed_transactions":0,"settlement_total_kes":0.0,"successful_transactions":0,\n' +
  572 |       '"total_amount_kes":0.0,"success_rate_pct":0.0},"mcda_id":"ALL"}\n' +
  573 |       '```\n\n' +
  574 |       '**Observation:** The smoke test file (smoke-analytics-dashboard-summary.spec.ts) recorded ' +
  575 |       '`total_transactions=135` in a prior session, proving data does exist. The current ' +
  576 |       'all-zero response suggests one of:\n' +
  577 |       '1. The aggregation job runs on a schedule and has not run yet today\n' +
  578 |       '2. The analytics data pipeline is broken/not running\n' +
  579 |       '3. The "today" aggregation uses UTC midnight but the server is in EAT (UTC+3) — ' +
  580 |       'meaning the first 3 hours of EAT day are counted as "yesterday" in UTC\n\n' +
  581 |       '**Impact:** The analytics dashboard always shows zeros in early-morning hours (EAT) ' +
  582 |       'because the UTC day boundary has not yet caught up with the local day.',
  583 |     );
  584 | 
  585 |     await logger.step('Step 1 — Call analytics and confirm zeros', async () => {
  586 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  587 |       const res = await GET(ANA_BASE, ANA_EP);
  588 |       logger.pass('HTTP ' + res.status + ' received');
  589 |       logger.info('Asserting: Response is 200');
  590 |       expect(res.status).toBe(200);
  591 |       const today = res.body.today as Record<string, number>;
  592 |       const allZero = Object.values(today).every(v => v === 0 || v === 0.0);
  593 |       allure.parameter('all fields zero', String(allZero));
  594 |       allure.parameter('total_transactions', String(today.total_transactions));
  595 |       allure.parameter('total_amount_kes',   String(today.total_amount_kes));
  596 |       allure.attachment('Analytics Response', JSON.stringify(res.body, null, 2), 'application/json');
  597 | 
  598 |       if (allZero) {
  599 |         bugAttach('BUG-ANA-005',
  600 |           'Non-zero counts reflecting actual transaction data',
  601 |           'All fields = 0 despite historical data existing (smoke tests recorded 135 transactions)',
  602 |           'Dashboard always shows zeros; analytics data is unreliable or stale',
  603 |           'Verify aggregation job schedule; check if UTC vs EAT timezone is causing the "today" boundary issue',
  604 |         );
  605 |       }
  606 |       allure.parameter('BUG-ANA-005 triggered', String(allZero));
  607 |       logger.pass('All assertions passed');
  608 |     });
  609 | 
  610 |     await logger.step('Step 2 — Test with explicit date param to check if date-scoping is the issue', async () => {
  611 |       const yesterday = new Date(Date.now() - 86400000).toISOString().split('T')[0];
  612 |       logger.info('GET /api/v1/analytics/dashboard/summary?date=' + yesterday);
  613 |       const res = await GET(ANA_BASE, `${ANA_EP}?date=${yesterday}`);
  614 |       logger.pass('HTTP ' + res.status + ' received');
  615 |       logger.info('Asserting: Response is 200 with explicit date param');
  616 |       expect(res.status).toBe(200);
  617 |       const today = res.body.today as Record<string, number>;
  618 |       allure.parameter('?date=yesterday total_transactions', String(today.total_transactions));
  619 |       allure.parameter('Date param causes different result', String(JSON.stringify(res.body) !== '{"today":{"total_transactions":0}}'));
  620 |       logger.pass('All assertions passed');
  621 |     });
  622 |   });
  623 | 
```