# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Analytics API — Deep Tests >> TC-ANA-007 — Analytics HEAD method returns 200 with no body (correct behaviour)
- Location: tests/API/deep-regression-api.spec.ts:651:7

# Error details

```
Error: HEAD must return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
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
  582 |       expect(res.status).toBe(200);
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
> 664 |       expect(res.status, 'HEAD must return 200').toBe(200);
      |                                                  ^ Error: HEAD must return 200
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
  683 |   const MONTHLY   = '/api/v1/dashboard/monthly-revenue';
  684 |   const TREND     = '/api/v1/dashboard/revenue-trend';
  685 |   const STATUS_CY = '/api/v1/dashboard/status-currency';
  686 |   const TODAY_PAY = '/api/v1/dashboard/today-payments';
  687 | 
  688 |   // ── TC-DB-001 ─────────────────────────────────────────────────────────────
  689 |   test('[BUG-DB-001] TC-DB-001 — period="" (empty string) returns 200 with undefined default', async ({ logger }) => {
  690 |     labels('Dashboard API', 'TC-DB-001', 'Empty Period Param Returns 200', 'medium');
  691 |     await allure.description(
  692 |       '**[BUG-DB-001]**\n\n' +
  693 |       'Calling GET /dashboard/summary?period= (empty string) returns HTTP 200 instead of 400.\n\n' +
  694 |       '**Expected:** HTTP 400 Bad Request with `"message":"period must be one of: today, week, month, year, last-3-month, custom"`\n\n' +
  695 |       '**Actual:** HTTP 200 with some undefined default period applied silently.\n\n' +
  696 |       '**Impact:** Clients cannot detect they passed an invalid parameter. The response data ' +
  697 |       'is for an unspecified period — consuming it would be a silent correctness bug.',
  698 |     );
  699 | 
  700 |     await logger.step('Step 1 — GET /summary?period= (empty)', async () => {
  701 |       logger.info('GET /api/v1/dashboard/summary?period=');
  702 |       const res = await GET(DB_BASE, `${SUMMARY}?period=`, DB_TOKEN);
  703 |       logger.pass('HTTP ' + res.status + ' received');
  704 |       allure.parameter('HTTP Status', String(res.status));
  705 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 300));
  706 |       bugAttach('BUG-DB-001',
  707 |         '400 Bad Request — period must not be empty',
  708 |         `HTTP ${res.status} — empty period= silently accepted`,
  709 |         'Client does not know which time period the data covers; correctness risk',
  710 |         'Add input validation: reject empty string for period with 400 response',
  711 |       );
  712 |       logger.info('Asserting: Empty period returns 400');
  713 |       expect(res.status, '[BUG-DB-001] period="" must return 400, not 200').toBe(400);
  714 |       logger.pass('All assertions passed');
  715 |     });
  716 |   });
  717 | 
  718 |   // ── TC-DB-002 ─────────────────────────────────────────────────────────────
  719 |   test('[BUG-DB-002] TC-DB-002 — period is case-sensitive (period=WEEK → 400, no documentation)', async ({ logger }) => {
  720 |     labels('Dashboard API', 'TC-DB-002', 'Period Case Sensitivity Undocumented', 'medium');
  721 |     await allure.description(
  722 |       '**[BUG-DB-002]**\n\n' +
  723 |       'The `period` parameter is strictly case-sensitive:\n' +
  724 |       '- `period=week` → 200 OK\n' +
  725 |       '- `period=WEEK` → 400 Bad Request\n' +
  726 |       '- `period=Week` → 400 Bad Request\n\n' +
  727 |       '**Expected:** The API should either accept case-insensitive values and normalise them, ' +
  728 |       'OR document explicitly that values are case-sensitive.\n\n' +
  729 |       '**Additionally:** The 400 error message omits `today` and `last-3-month` from the valid values list, ' +
  730 |       'leaving developers guessing.\n\n' +
  731 |       '**Impact:** Developers who naturally capitalise query params or use uppercase constants will ' +
  732 |       'get unexpected 400 errors with no useful guidance.',
  733 |     );
  734 | 
  735 |     await logger.step('Step 1 — period=week (lowercase) must return 200', async () => {
  736 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  737 |       const res = await GET(DB_BASE, `${SUMMARY}?period=week`, DB_TOKEN);
  738 |       logger.pass('HTTP ' + res.status + ' received');
  739 |       logger.info('Asserting: period=week returns 200');
  740 |       expect(res.status, 'period=week must return 200').toBe(200);
  741 |       allure.parameter('period=week status', String(res.status));
  742 |       logger.pass('All assertions passed');
  743 |     });
  744 | 
  745 |     await logger.step('Step 2 — period=WEEK (uppercase) must return 200 (BUG: returns 400)', async () => {
  746 |       logger.info('GET /api/v1/dashboard/summary?period=WEEK');
  747 |       const res = await GET(DB_BASE, `${SUMMARY}?period=WEEK`, DB_TOKEN);
  748 |       logger.pass('HTTP ' + res.status + ' received');
  749 |       allure.parameter('period=WEEK status', String(res.status));
  750 |       allure.parameter('Error message', String(res.body?.message ?? 'N/A'));
  751 |       bugAttach('BUG-DB-002',
  752 |         'period=WEEK → 200 OK (case-insensitive) or 400 with complete valid-values list',
  753 |         `period=WEEK → HTTP ${res.status} with message: "${res.body?.message}"`,
  754 |         'Developers using uppercase enum constants get 400 without clear guidance',
  755 |         'Normalise period to lowercase before validation; or document case-sensitivity with full enum list',
  756 |       );
  757 |       logger.info('Asserting: period=WEEK is accepted (case-insensitive)');
  758 |       expect(res.status, '[BUG-DB-002] period=WEEK should be accepted (case-insensitive) or error message should list all valid values').toBe(200);
  759 |       logger.pass('All assertions passed');
  760 |     });
  761 | 
  762 |     await logger.step('Step 3 — Check error message lists all valid periods', async () => {
  763 |       logger.info('GET /api/v1/dashboard/summary?period=invalid');
  764 |       const res = await GET(DB_BASE, `${SUMMARY}?period=invalid`, DB_TOKEN);
```