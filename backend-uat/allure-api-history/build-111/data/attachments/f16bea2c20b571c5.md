# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Analytics API — Deep Tests >> TC-ANA-007 — Analytics HEAD method returns 200 with no body (correct behaviour)
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:659:7

# Error details

```
Error: HEAD must return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
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
  624 |   // ── TC-ANA-006 ────────────────────────────────────────────────────────────
  625 |   test('TC-ANA-006 — Analytics field types: all numeric fields must be numbers not strings', async ({ logger }) => {
  626 |     labels('Analytics API', 'TC-ANA-006', 'Field Type Validation', 'normal');
  627 |     await allure.description(
  628 |       'Verifies that every field in the `today` object is a proper JavaScript number, not a string.\n\n' +
  629 |       'Other dashboard endpoints (today-payments) have been found returning monetary values as strings. ' +
  630 |       'This test confirms whether analytics has the same issue.',
  631 |     );
  632 | 
  633 |     await logger.step('Step 1 — Fetch and validate all field types', async () => {
  634 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  635 |       const res = await GET(ANA_BASE, ANA_EP);
  636 |       logger.pass('HTTP ' + res.status + ' received');
  637 |       logger.info('Asserting: Response is 200');
  638 |       expect(res.status).toBe(200);
  639 |       const today = res.body.today as Record<string, unknown>;
  640 |       const numericFields = [
  641 |         'total_transactions','success_rate_pct','total_amount_kes',
  642 |         'successful_transactions','settlement_total_kes','failed_transactions',
  643 |         'recon_exceptions','recon_matched',
  644 |       ];
  645 |       logger.info('Asserting: All numeric fields are of type number and finite');
  646 |       for (const f of numericFields) {
  647 |         const val = today[f];
  648 |         allure.parameter(f, `${val} (${typeof val})`);
  649 |         expect(typeof val,        `today.${f} must be a number, not ${typeof val}`).toBe('number');
  650 |         expect(Number.isNaN(val), `today.${f} must not be NaN`).toBe(false);
  651 |         expect(Number.isFinite(val as number), `today.${f} must be finite`).toBe(true);
  652 |         expect(val as number, `today.${f} must be >= 0`).toBeGreaterThanOrEqual(0);
  653 |       }
  654 |       logger.pass('All assertions passed');
  655 |     });
  656 |   });
  657 | 
  658 |   // ── TC-ANA-007 ────────────────────────────────────────────────────────────
  659 |   test('TC-ANA-007 — Analytics HEAD method returns 200 with no body (correct behaviour)', async ({ logger }) => {
  660 |     labels('Analytics API', 'TC-ANA-007', 'HEAD Method Support', 'minor');
  661 |     await allure.description(
  662 |       'Verifies that HEAD requests to the analytics endpoint return 200 with no response body. ' +
  663 |       'HEAD support is required by HTTP/1.1 (RFC 7231) for any resource that supports GET.',
  664 |     );
  665 | 
  666 |     await logger.step('Step 1 — Send HEAD request', async () => {
  667 |       logger.info('HEAD /api/v1/analytics/dashboard/summary');
  668 |       const res = await supertest(ANA_BASE).head(ANA_EP).set('Accept', 'application/json');
  669 |       logger.pass('HTTP ' + res.status + ' received');
  670 |       const bodyText = JSON.stringify(res.body);
  671 |       logger.info('Asserting: HEAD returns 200 with empty body');
> 672 |       expect(res.status, 'HEAD must return 200').toBe(200);
      |                                                  ^ Error: HEAD must return 200
  673 |       expect(['{}','null',''].includes(bodyText), 'HEAD response body must be empty').toBe(true);
  674 |       allure.parameter('HEAD status', String(res.status));
  675 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  676 |       allure.parameter('Body empty', String(['{}','null',''].includes(bodyText)));
  677 |       logger.pass('All assertions passed');
  678 |     });
  679 |   });
  680 | 
  681 | });
  682 | 
  683 | // ═════════════════════════════════════════════════════════════════════════════
  684 | //  3. DASHBOARD API — EDGE CASES
  685 | //  Note: These tests require a valid DASHBOARD_API_TOKEN in .env
  686 | // ═════════════════════════════════════════════════════════════════════════════
  687 | 
  688 | test.describe('Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN)', () => {
  689 | 
  690 |   const SUMMARY   = '/api/v1/dashboard/summary';
  691 |   const MONTHLY   = '/api/v1/dashboard/monthly-revenue';
  692 |   const TREND     = '/api/v1/dashboard/revenue-trend';
  693 |   const STATUS_CY = '/api/v1/dashboard/status-currency';
  694 |   const TODAY_PAY = '/api/v1/dashboard/today-payments';
  695 | 
  696 |   // ── TC-DB-001 ─────────────────────────────────────────────────────────────
  697 |   test('[BUG-DB-001] TC-DB-001 — period="" (empty string) returns 200 with undefined default', async ({ logger }) => {
  698 |     labels('Dashboard API', 'TC-DB-001', 'Empty Period Param Returns 200', 'medium');
  699 |     await allure.description(
  700 |       '**[BUG-DB-001]**\n\n' +
  701 |       'Calling GET /dashboard/summary?period= (empty string) returns HTTP 200 instead of 400.\n\n' +
  702 |       '**Expected:** HTTP 400 Bad Request with `"message":"period must be one of: today, week, month, year, last-3-month, custom"`\n\n' +
  703 |       '**Actual:** HTTP 200 with some undefined default period applied silently.\n\n' +
  704 |       '**Impact:** Clients cannot detect they passed an invalid parameter. The response data ' +
  705 |       'is for an unspecified period — consuming it would be a silent correctness bug.',
  706 |     );
  707 | 
  708 |     await logger.step('Step 1 — GET /summary?period= (empty)', async () => {
  709 |       logger.info('GET /api/v1/dashboard/summary?period=');
  710 |       const res = await GET(DB_BASE, `${SUMMARY}?period=`, DB_TOKEN);
  711 |       logger.pass('HTTP ' + res.status + ' received');
  712 |       allure.parameter('HTTP Status', String(res.status));
  713 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 300));
  714 |       bugAttach('BUG-DB-001',
  715 |         '400 Bad Request — period must not be empty',
  716 |         `HTTP ${res.status} — empty period= silently accepted`,
  717 |         'Client does not know which time period the data covers; correctness risk',
  718 |         'Add input validation: reject empty string for period with 400 response',
  719 |       );
  720 |       logger.info('Asserting: Empty period returns 400');
  721 |       expect(res.status, '[BUG-DB-001] period="" must return 400, not 200').toBe(400);
  722 |       logger.pass('All assertions passed');
  723 |     });
  724 |   });
  725 | 
  726 |   // ── TC-DB-002 ─────────────────────────────────────────────────────────────
  727 |   test('[BUG-DB-002] TC-DB-002 — period is case-sensitive (period=WEEK → 400, no documentation)', async ({ logger }) => {
  728 |     labels('Dashboard API', 'TC-DB-002', 'Period Case Sensitivity Undocumented', 'medium');
  729 |     await allure.description(
  730 |       '**[BUG-DB-002]**\n\n' +
  731 |       'The `period` parameter is strictly case-sensitive:\n' +
  732 |       '- `period=week` → 200 OK\n' +
  733 |       '- `period=WEEK` → 400 Bad Request\n' +
  734 |       '- `period=Week` → 400 Bad Request\n\n' +
  735 |       '**Expected:** The API should either accept case-insensitive values and normalise them, ' +
  736 |       'OR document explicitly that values are case-sensitive.\n\n' +
  737 |       '**Additionally:** The 400 error message omits `today` and `last-3-month` from the valid values list, ' +
  738 |       'leaving developers guessing.\n\n' +
  739 |       '**Impact:** Developers who naturally capitalise query params or use uppercase constants will ' +
  740 |       'get unexpected 400 errors with no useful guidance.',
  741 |     );
  742 | 
  743 |     await logger.step('Step 1 — period=week (lowercase) must return 200', async () => {
  744 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  745 |       const res = await GET(DB_BASE, `${SUMMARY}?period=week`, DB_TOKEN);
  746 |       logger.pass('HTTP ' + res.status + ' received');
  747 |       logger.info('Asserting: period=week returns 200');
  748 |       expect(res.status, 'period=week must return 200').toBe(200);
  749 |       allure.parameter('period=week status', String(res.status));
  750 |       logger.pass('All assertions passed');
  751 |     });
  752 | 
  753 |     await logger.step('Step 2 — period=WEEK (uppercase) must return 200 (BUG: returns 400)', async () => {
  754 |       logger.info('GET /api/v1/dashboard/summary?period=WEEK');
  755 |       const res = await GET(DB_BASE, `${SUMMARY}?period=WEEK`, DB_TOKEN);
  756 |       logger.pass('HTTP ' + res.status + ' received');
  757 |       allure.parameter('period=WEEK status', String(res.status));
  758 |       allure.parameter('Error message', String(res.body?.message ?? 'N/A'));
  759 |       bugAttach('BUG-DB-002',
  760 |         'period=WEEK → 200 OK (case-insensitive) or 400 with complete valid-values list',
  761 |         `period=WEEK → HTTP ${res.status} with message: "${res.body?.message}"`,
  762 |         'Developers using uppercase enum constants get 400 without clear guidance',
  763 |         'Normalise period to lowercase before validation; or document case-sensitivity with full enum list',
  764 |       );
  765 |       logger.info('Asserting: period=WEEK is accepted (case-insensitive)');
  766 |       expect(res.status, '[BUG-DB-002] period=WEEK should be accepted (case-insensitive) or error message should list all valid values').toBe(200);
  767 |       logger.pass('All assertions passed');
  768 |     });
  769 | 
  770 |     await logger.step('Step 3 — Check error message lists all valid periods', async () => {
  771 |       logger.info('GET /api/v1/dashboard/summary?period=invalid');
  772 |       const res = await GET(DB_BASE, `${SUMMARY}?period=invalid`, DB_TOKEN);
```