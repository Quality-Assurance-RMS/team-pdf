# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN) >> [BUG-DB-006] TC-DB-006 — Single-day range (from_date=to_date) returns 400 (validator too strict)
- Location: tests/API/deep-regression-api.spec.ts:899:7

# Error details

```
Error: [BUG-DB-006] from_date=to_date must return 200 (single day is valid)

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  828  |       '**Root Cause:** No format validation — the malformed date string is passed directly to ' +
  829  |       'the date parser which throws an unhandled exception.\n\n' +
  830  |       '**Impact:** Developers who pass dates in DD-MM-YYYY (which is technically a valid date ' +
  831  |       'string like 01-06-2026) get a confusing 500 with no format guidance.',
  832  |     );
  833  | 
  834  |     const wrongFormats = [
  835  |       { from: '09-06-2026', to: '09-06-2026', label: 'DD-MM-YYYY' },
  836  |       { from: '2026/06/09', to: '2026/06/09', label: 'YYYY/MM/DD (slashes)' },
  837  |       { from: 'June 9 2026', to: 'June 9 2026', label: 'Long form' },
  838  |     ];
  839  | 
  840  |     for (const d of wrongFormats) {
  841  |       await logger.step(`Wrong format: ${d.label}`, async () => {
  842  |         logger.info(`GET /api/v1/dashboard/summary?period=custom&from_date=${d.from}&to_date=${d.to}`);
  843  |         const res = await GET(DB_BASE, `${SUMMARY}?period=custom&from_date=${encodeURIComponent(d.from)}&to_date=${encodeURIComponent(d.to)}`, DB_TOKEN);
  844  |         logger.pass('HTTP ' + res.status + ' received');
  845  |         allure.parameter(`${d.label} status`, String(res.status));
  846  |         if (res.status === 500) {
  847  |           bugAttach('BUG-DB-004',
  848  |             '400 Bad Request — date must be in YYYY-MM-DD format',
  849  |             `HTTP ${res.status} for date format "${d.from}"`,
  850  |             'No format guidance; 500 triggers false outage alerts',
  851  |             'Validate date format (YYYY-MM-DD) before parsing; return 400 with format example',
  852  |           );
  853  |         }
  854  |         logger.info(`Asserting: Wrong date format "${d.from}" returns 400`);
  855  |         expect(res.status, `[BUG-DB-004] Wrong date format "${d.from}" must return 400, got ${res.status}`).toBe(400);
  856  |         logger.pass('All assertions passed');
  857  |       });
  858  |     }
  859  |   });
  860  | 
  861  |   // ── TC-DB-005 ─────────────────────────────────────────────────────────────
  862  |   test('[BUG-DB-005] TC-DB-005 — SQL-like input in from_date causes HTTP 500 (unhandled exception)', async ({ logger }) => {
  863  |     labels('Dashboard API', 'TC-DB-005', 'SQL-like Input Causes 500', 'critical');
  864  |     await allure.description(
  865  |       '**[BUG-DB-005]**\n\n' +
  866  |       'Passing SQL-like strings in the `from_date` parameter causes HTTP 500 instead of 400.\n\n' +
  867  |       '**Test input:** `from_date=2026-01-01 OR 1=1`\n\n' +
  868  |       '**Expected:** HTTP 400 — the input should be rejected at the validation layer as an invalid date.\n\n' +
  869  |       '**Actual:** HTTP 500 — the server throws an unhandled exception.\n\n' +
  870  |       '**Note:** This is a validation failure test, not an active SQL injection test. ' +
  871  |       'The fact that it returns 500 instead of 400 proves the input is not validated before ' +
  872  |       'reaching the data layer. Whether actual injection occurs depends on the ORM/query builder ' +
  873  |       'being used — parameterised queries would prevent execution but should not prevent 400 validation.',
  874  |     );
  875  | 
  876  |     await logger.step('Step 1 — Send SQL-like string in from_date', async () => {
  877  |       logger.info('GET /api/v1/dashboard/summary?period=custom&from_date=2026-01-01 OR 1=1&to_date=2026-06-09');
  878  |       const res = await GET(
  879  |         DB_BASE,
  880  |         `${SUMMARY}?period=custom&from_date=${encodeURIComponent("2026-01-01 OR 1=1")}&to_date=2026-06-09`,
  881  |         DB_TOKEN,
  882  |       );
  883  |       logger.pass('HTTP ' + res.status + ' received');
  884  |       allure.parameter('HTTP Status', String(res.status));
  885  |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 200));
  886  |       bugAttach('BUG-DB-005',
  887  |         '400 Bad Request — from_date must match YYYY-MM-DD',
  888  |         `HTTP ${res.status} for input "2026-01-01 OR 1=1"`,
  889  |         'Input not validated before reaching data layer; 500 confirms no pre-processing validation',
  890  |         'Strictly validate from_date matches /^\\d{4}-\\d{2}-\\d{2}$/ before any further processing',
  891  |       );
  892  |       logger.info('Asserting: SQL-like from_date input returns 400');
  893  |       expect(res.status, '[BUG-DB-005] SQL-like from_date input must return 400, not 500').toBe(400);
  894  |       logger.pass('All assertions passed');
  895  |     });
  896  |   });
  897  | 
  898  |   // ── TC-DB-006 ─────────────────────────────────────────────────────────────
  899  |   test('[BUG-DB-006] TC-DB-006 — Single-day range (from_date=to_date) returns 400 (validator too strict)', async ({ logger }) => {
  900  |     labels('Dashboard API', 'TC-DB-006', 'Single-Day Range Incorrectly Rejected', 'medium');
  901  |     await allure.description(
  902  |       '**[BUG-DB-006]**\n\n' +
  903  |       'When `from_date` equals `to_date` (requesting a single day\'s data), the API returns ' +
  904  |       '400 Bad Request instead of 200 with the single day\'s data.\n\n' +
  905  |       '**Expected:** HTTP 200 — a single day is a valid date range (inclusive bounds).\n\n' +
  906  |       '**Root Cause:** The validator checks `from_date < to_date` (strict less-than) instead ' +
  907  |       'of `from_date <= to_date` (less-than-or-equal).\n\n' +
  908  |       '**Impact:** Users who want to view a single day\'s dashboard metrics cannot do so via ' +
  909  |       'the custom period. They must use `period=today` only for "today", with no ability ' +
  910  |       'to view historical single days.',
  911  |     );
  912  | 
  913  |     await logger.step('Step 1 — GET /summary with from_date=to_date (same day)', async () => {
  914  |       const today = new Date().toISOString().split('T')[0];
  915  |       logger.info(`GET /api/v1/dashboard/summary?period=custom&from_date=${today}&to_date=${today}`);
  916  |       const res = await GET(DB_BASE, `${SUMMARY}?period=custom&from_date=${today}&to_date=${today}`, DB_TOKEN);
  917  |       logger.pass('HTTP ' + res.status + ' received');
  918  |       allure.parameter('from_date = to_date', today);
  919  |       allure.parameter('HTTP Status', String(res.status));
  920  |       allure.parameter('Error message', String(res.body?.message ?? 'N/A'));
  921  |       bugAttach('BUG-DB-006',
  922  |         '200 OK — single day is a valid range',
  923  |         `HTTP ${res.status} — from_date=to_date rejected`,
  924  |         'Users cannot query a single historical day via the custom period filter',
  925  |         'Change date range validator from strict < to <= (from_date <= to_date)',
  926  |       );
  927  |       logger.info('Asserting: Single-day range (from_date=to_date) returns 200');
> 928  |       expect(res.status, '[BUG-DB-006] from_date=to_date must return 200 (single day is valid)').toBe(200);
       |                                                                                                  ^ Error: [BUG-DB-006] from_date=to_date must return 200 (single day is valid)
  929  |       logger.pass('All assertions passed');
  930  |     });
  931  |   });
  932  | 
  933  |   // ── TC-DB-007 ─────────────────────────────────────────────────────────────
  934  |   test('[BUG-DB-007] TC-DB-007 — successful_payments% + failed_payments% must equal 100%', async ({ logger }) => {
  935  |     labels('Dashboard API', 'TC-DB-007', 'Payment Rate Percentages Do Not Sum to 100%', 'high');
  936  |     await allure.description(
  937  |       '**[BUG-DB-007]**\n\n' +
  938  |       'The dashboard summary returns two rate fields:\n' +
  939  |       '- `successful_payments.rate_pct` — the success rate\n' +
  940  |       '- `failed_payments.rate_pct` — the failure rate\n\n' +
  941  |       'In a two-state system (success or failure), these two rates must sum to exactly 100%.\n\n' +
  942  |       '**Why they do not sum to 100%:**\n' +
  943  |       'There is a third state — PENDING transactions — that is not counted in either rate. ' +
  944  |       'The formula used is:\n' +
  945  |       '  `success_rate = successful / total` and `fail_rate = failed / total`\n\n' +
  946  |       'If there are 135 total and 0 successful and 0 failed (all pending), both rates are 0% ' +
  947  |       'and they sum to 0% instead of 100%.\n\n' +
  948  |       '**Expected:** Either include pending in the denominator and show a 3rd metric, ' +
  949  |       'or explicitly document that rates are out of (successful + failed), not all transactions.',
  950  |     );
  951  | 
  952  |     await logger.step('Step 1 — Fetch summary and check rate sum', async () => {
  953  |       logger.info('GET /api/v1/dashboard/summary?period=week');
  954  |       const res = await GET(DB_BASE, `${SUMMARY}?period=week`, DB_TOKEN);
  955  |       logger.pass('HTTP ' + res.status + ' received');
  956  |       if (res.status !== 200) { test.skip(); return; }
  957  | 
  958  |       const body = res.body as Record<string, {rate_pct?: number}>;
  959  |       const successRate = body.successful_payments?.rate_pct ?? 0;
  960  |       const failRate    = body.failed_payments?.rate_pct ?? 0;
  961  |       const sum         = successRate + failRate;
  962  | 
  963  |       allure.parameter('successful_payments.rate_pct', String(successRate));
  964  |       allure.parameter('failed_payments.rate_pct',     String(failRate));
  965  |       allure.parameter('Sum',                          String(sum));
  966  |       allure.parameter('Expected sum',                 '100');
  967  | 
  968  |       bugAttach('BUG-DB-007',
  969  |         'successful_payments.rate_pct + failed_payments.rate_pct = 100',
  970  |         `${successRate} + ${failRate} = ${sum} (not 100)`,
  971  |         'Dashboard metrics appear inconsistent; pending transactions create an unexplained gap',
  972  |         'Add pending_payments.rate_pct field, or compute rates as successful/(successful+failed)',
  973  |       );
  974  |       logger.info('Asserting: successful_payments.rate_pct + failed_payments.rate_pct equals 100%');
  975  |       expect(sum, `[BUG-DB-007] success(${successRate}%) + fail(${failRate}%) must equal 100%, got ${sum}%`).toBeCloseTo(100, 1);
  976  |       logger.pass('All assertions passed');
  977  |     });
  978  |   });
  979  | 
  980  |   // ── TC-DB-008 ─────────────────────────────────────────────────────────────
  981  |   test('[BUG-DB-008] TC-DB-008 — monthly-revenue: invalid year values accepted without 400', async ({ logger }) => {
  982  |     labels('Dashboard API', 'TC-DB-008', 'Year Parameter Lacks Bounds Validation', 'medium');
  983  |     await allure.description(
  984  |       '**[BUG-DB-008]**\n\n' +
  985  |       'The `/dashboard/monthly-revenue` endpoint accepts invalid year values without returning 400:\n\n' +
  986  |       '| Input | Expected | Actual |\n' +
  987  |       '|-------|----------|--------|\n' +
  988  |       '| year=0 | 400 | 200 (defaults to current year) |\n' +
  989  |       '| year= (empty) | 400 | 200 (defaults to current year) |\n' +
  990  |       '| year=9999 | 400 or 200 with empty data | 200 |\n' +
  991  |       '| year=abc | 400 | ? |\n\n' +
  992  |       '**Expected:** Reject years outside a sensible range (e.g. 2000–2099) with 400 Bad Request.',
  993  |     );
  994  | 
  995  |     const invalidYears = [
  996  |       { y: '0',    label: 'year=0',     expectStatus: 400 },
  997  |       { y: '',     label: 'year=empty', expectStatus: 400 },
  998  |       { y: '9999', label: 'year=9999',  expectStatus: 400 },
  999  |       { y: 'abc',  label: 'year=abc',   expectStatus: 400 },
  1000 |       { y: '-1',   label: 'year=-1',    expectStatus: 400 },
  1001 |     ];
  1002 | 
  1003 |     for (const scenario of invalidYears) {
  1004 |       await logger.step(`${scenario.label}`, async () => {
  1005 |         const url = scenario.y !== '' ? `${MONTHLY}?year=${scenario.y}` : `${MONTHLY}?year=`;
  1006 |         logger.info('GET ' + url);
  1007 |         const res = await GET(DB_BASE, url, DB_TOKEN);
  1008 |         logger.pass('HTTP ' + res.status + ' received');
  1009 |         allure.parameter(`${scenario.label} → HTTP`, String(res.status));
  1010 |         if (res.status !== 400) {
  1011 |           bugAttach('BUG-DB-008',
  1012 |             '400 Bad Request — year must be between 2000 and 2099',
  1013 |             `HTTP ${res.status} for ${scenario.label}`,
  1014 |             'Invalid year inputs return data or silently default — no validation at the input layer',
  1015 |             'Validate year is a 4-digit integer in a sensible range (e.g. 2000–2099)',
  1016 |           );
  1017 |         }
  1018 |         logger.info(`Asserting: ${scenario.label} returns 400`);
  1019 |         expect.soft(res.status, `[BUG-DB-008] ${scenario.label} must return 400`).toBe(400);
  1020 |         logger.pass('All assertions passed');
  1021 |       });
  1022 |     }
  1023 |   });
  1024 | 
  1025 |   // ── TC-DB-009 ─────────────────────────────────────────────────────────────
  1026 |   test('[BUG-DB-009] TC-DB-009 — revenue-trend labels[] contains duplicate month names (no year)', async ({ logger }) => {
  1027 |     labels('Dashboard API', 'TC-DB-009', 'Duplicate Labels in Revenue Trend Array', 'medium');
  1028 |     await allure.description(
```