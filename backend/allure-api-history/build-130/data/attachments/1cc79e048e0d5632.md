# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Analytics API — Deep Tests >> [BUG-ANA-005] TC-ANA-005 — Analytics returns all-zero counts (possible aggregation or data pipeline issue)
- Location: tests/API/deep-regression-api.spec.ts:556:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
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
  568 |       'all-zero response suggests one of:\n' +
  569 |       '1. The aggregation job runs on a schedule and has not run yet today\n' +
  570 |       '2. The analytics data pipeline is broken/not running\n' +
  571 |       '3. The "today" aggregation uses UTC midnight but the server is in EAT (UTC+3) — ' +
  572 |       'meaning the first 3 hours of EAT day are counted as "yesterday" in UTC\n\n' +
  573 |       '**Impact:** The analytics dashboard always shows zeros in early-morning hours (EAT) ' +
  574 |       'because the UTC day boundary has not yet caught up with the local day.',
  575 |     );
  576 | 
  577 |     await logger.step('Step 1 — Call analytics and confirm zeros', async () => {
  578 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  579 |       const res = await GET(ANA_BASE, ANA_EP);
  580 |       logger.pass('HTTP ' + res.status + ' received');
  581 |       logger.info('Asserting: Response is 200');
> 582 |       expect(res.status).toBe(200);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
  583 |       const today = res.body.today as Record<string, number>;
  584 |       const allZero = Object.values(today).every(v => v === 0 || v === 0.0);
  585 |       allure.parameter('all fields zero', String(allZero));
  586 |       allure.parameter('total_transactions', String(today.total_transactions));
  587 |       allure.parameter('total_amount_kes',   String(today.total_amount_kes));
  588 |       allure.attachment('Analytics Response', JSON.stringify(res.body, null, 2), 'application/json');
  589 | 
  590 |       if (allZero) {
  591 |         bugAttach('BUG-ANA-005',
  592 |           'Non-zero counts reflecting actual transaction data',
  593 |           'All fields = 0 despite historical data existing (smoke tests recorded 135 transactions)',
  594 |           'Dashboard always shows zeros; analytics data is unreliable or stale',
  595 |           'Verify aggregation job schedule; check if UTC vs EAT timezone is causing the "today" boundary issue',
  596 |         );
  597 |       }
  598 |       allure.parameter('BUG-ANA-005 triggered', String(allZero));
  599 |       logger.pass('All assertions passed');
  600 |     });
  601 | 
  602 |     await logger.step('Step 2 — Test with explicit date param to check if date-scoping is the issue', async () => {
  603 |       const yesterday = new Date(Date.now() - 86400000).toISOString().split('T')[0];
  604 |       logger.info('GET /api/v1/analytics/dashboard/summary?date=' + yesterday);
  605 |       const res = await GET(ANA_BASE, `${ANA_EP}?date=${yesterday}`);
  606 |       logger.pass('HTTP ' + res.status + ' received');
  607 |       logger.info('Asserting: Response is 200 with explicit date param');
  608 |       expect(res.status).toBe(200);
  609 |       const today = res.body.today as Record<string, number>;
  610 |       allure.parameter('?date=yesterday total_transactions', String(today.total_transactions));
  611 |       allure.parameter('Date param causes different result', String(JSON.stringify(res.body) !== '{"today":{"total_transactions":0}}'));
  612 |       logger.pass('All assertions passed');
  613 |     });
  614 |   });
  615 | 
  616 |   // ── TC-ANA-006 ────────────────────────────────────────────────────────────
  617 |   test('TC-ANA-006 — Analytics field types: all numeric fields must be numbers not strings', async ({ logger }) => {
  618 |     labels('Analytics API', 'TC-ANA-006', 'Field Type Validation', 'normal');
  619 |     await allure.description(
  620 |       'Verifies that every field in the `today` object is a proper JavaScript number, not a string.\n\n' +
  621 |       'Other dashboard endpoints (today-payments) have been found returning monetary values as strings. ' +
  622 |       'This test confirms whether analytics has the same issue.',
  623 |     );
  624 | 
  625 |     await logger.step('Step 1 — Fetch and validate all field types', async () => {
  626 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  627 |       const res = await GET(ANA_BASE, ANA_EP);
  628 |       logger.pass('HTTP ' + res.status + ' received');
  629 |       logger.info('Asserting: Response is 200');
  630 |       expect(res.status).toBe(200);
  631 |       const today = res.body.today as Record<string, unknown>;
  632 |       const numericFields = [
  633 |         'total_transactions','success_rate_pct','total_amount_kes',
  634 |         'successful_transactions','settlement_total_kes','failed_transactions',
  635 |         'recon_exceptions','recon_matched',
  636 |       ];
  637 |       logger.info('Asserting: All numeric fields are of type number and finite');
  638 |       for (const f of numericFields) {
  639 |         const val = today[f];
  640 |         allure.parameter(f, `${val} (${typeof val})`);
  641 |         expect(typeof val,        `today.${f} must be a number, not ${typeof val}`).toBe('number');
  642 |         expect(Number.isNaN(val), `today.${f} must not be NaN`).toBe(false);
  643 |         expect(Number.isFinite(val as number), `today.${f} must be finite`).toBe(true);
  644 |         expect(val as number, `today.${f} must be >= 0`).toBeGreaterThanOrEqual(0);
  645 |       }
  646 |       logger.pass('All assertions passed');
  647 |     });
  648 |   });
  649 | 
  650 |   // ── TC-ANA-007 ────────────────────────────────────────────────────────────
  651 |   test('TC-ANA-007 — Analytics HEAD method returns 200 with no body (correct behaviour)', async ({ logger }) => {
  652 |     labels('Analytics API', 'TC-ANA-007', 'HEAD Method Support', 'minor');
  653 |     await allure.description(
  654 |       'Verifies that HEAD requests to the analytics endpoint return 200 with no response body. ' +
  655 |       'HEAD support is required by HTTP/1.1 (RFC 7231) for any resource that supports GET.',
  656 |     );
  657 | 
  658 |     await logger.step('Step 1 — Send HEAD request', async () => {
  659 |       logger.info('HEAD /api/v1/analytics/dashboard/summary');
  660 |       const res = await supertest(ANA_BASE).head(ANA_EP).set('Accept', 'application/json');
  661 |       logger.pass('HTTP ' + res.status + ' received');
  662 |       const bodyText = JSON.stringify(res.body);
  663 |       logger.info('Asserting: HEAD returns 200 with empty body');
  664 |       expect(res.status, 'HEAD must return 200').toBe(200);
  665 |       expect(['{}','null',''].includes(bodyText), 'HEAD response body must be empty').toBe(true);
  666 |       allure.parameter('HEAD status', String(res.status));
  667 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  668 |       allure.parameter('Body empty', String(['{}','null',''].includes(bodyText)));
  669 |       logger.pass('All assertions passed');
  670 |     });
  671 |   });
  672 | 
  673 | });
  674 | 
  675 | // ═════════════════════════════════════════════════════════════════════════════
  676 | //  3. DASHBOARD API — EDGE CASES
  677 | //  Note: These tests require a valid DASHBOARD_API_TOKEN in .env
  678 | // ═════════════════════════════════════════════════════════════════════════════
  679 | 
  680 | test.describe('Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN)', () => {
  681 | 
  682 |   const SUMMARY   = '/api/v1/dashboard/summary';
```