# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-today-payments.spec.ts >> Smoke â€" Dashboard Today Payments >> TC-DTPAY-011 â€" BUG-TPAY-11: Percentage format must be consistent across sections
- Location: tests/API/smoke/smoke-dashboard-today-payments.spec.ts:972:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 503
```

# Test source

```ts
  886  | 
  887  |       const allowsDelete = acam?.toUpperCase().includes('DELETE');
  888  |       const allowsPost   = acam?.toUpperCase().includes('POST');
  889  |       const allowsPut    = acam?.toUpperCase().includes('PUT');
  890  |       const allowsPatch  = acam?.toUpperCase().includes('PATCH');
  891  | 
  892  |       if (allowsDelete || allowsPost || allowsPut || allowsPatch) {
  893  |         flagIssue('TC-DTPAY-009', 'BUG-TPAY-08b',
  894  |           `CORS allows destructive methods (${[allowsPost && 'POST', allowsPut && 'PUT', allowsDelete && 'DELETE', allowsPatch && 'PATCH'].filter(Boolean).join(', ')}) from any origin â€" for a read-only dashboard endpoint, only GET, HEAD, OPTIONS are needed`,
  895  |           { 'access-control-allow-methods': acam, risk: 'Any site can trigger POST/PUT/DELETE on behalf of authenticated users' },
  896  |         );
  897  |       }
  898  | 
  899  |       logger.info('Asserting: CORS does not allow DELETE or POST on this read-only endpoint');
  900  |       // This is a GET-only endpoint â€" only GET, HEAD, OPTIONS should be CORS-allowed
  901  |       expect.soft(allowsDelete, '[BUG-TPAY-08b] CORS must not allow DELETE on a read-only endpoint').toBe(false);
  902  |       expect.soft(allowsPost,   '[BUG-TPAY-08b] CORS must not allow POST on a read-only endpoint').toBe(false);
  903  |       logger.pass('All assertions passed');
  904  |     });
  905  |   });
  906  | 
  907  |   // â"€â"€ TC-DTPAY-010 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  908  |   test('TC-DTPAY-010 â€" BUG-TPAY-09: Unknown query parameters must not be silently ignored', async ({ logger }) => {
  909  |     smokeLabels('TC-DTPAY-010', 'BUG-TPAY-09 â€" Unknown Query Params Silently Ignored', 'minor');
  910  | 
  911  |     let resDate:    supertest.Response;
  912  |     let resUnknown: supertest.Response;
  913  | 
  914  |     await logger.step('Step 1 â€" Send GET with unknown date parameter', async () => {
  915  |       logger.info('GET /api/v1/dashboard/today-payments?date=2026-06-01');
  916  |       resDate = await supertest(BASE_URL)
  917  |         .get(`${ENDPOINT}?date=2026-06-01`)
  918  |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  919  |         .set('Accept', 'application/json');
  920  |       logger.pass('HTTP ' + resDate.status + ' received');
  921  | 
  922  |       console.log(`[TC-DTPAY-010] ?date=2026-06-01  -> ${resDate.status}`);
  923  |       allure.parameter('?date=2026-06-01 status', String(resDate.status));
  924  |     });
  925  | 
  926  |     await logger.step('Step 2 â€" Send GET with completely unknown parameter', async () => {
  927  |       logger.info('GET /api/v1/dashboard/today-payments?foo=bar&baz=qux');
  928  |       resUnknown = await supertest(BASE_URL)
  929  |         .get(`${ENDPOINT}?foo=bar&baz=qux`)
  930  |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  931  |         .set('Accept', 'application/json');
  932  |       logger.pass('HTTP ' + resUnknown.status + ' received');
  933  | 
  934  |       console.log(`[TC-DTPAY-010] ?foo=bar  -> ${resUnknown.status}`);
  935  |       allure.parameter('?foo=bar&baz=qux status', String(resUnknown.status));
  936  |     });
  937  | 
  938  |     await logger.step('Step 3 â€" BUG-TPAY-09: Unknown params should not silently return 200', async () => {
  939  |       // The ?date param is silently ignored â€" if date filtering is intended, this is a missing feature
  940  |       // If date filtering is NOT intended, unknown params should return 400 Bad Request
  941  |       if (resDate!.status === 200) {
  942  |         flagIssue('TC-DTPAY-010', 'BUG-TPAY-09a',
  943  |           'GET /today-payments?date=2026-06-01 returns 200 and silently ignores the date parameter â€" ' +
  944  |           'if date filtering is supported, data should reflect that date; if not, the server should return 400 for unknown params',
  945  |           { actual_status: 200, query_param: 'date=2026-06-01', body_unchanged: true },
  946  |         );
  947  |       }
  948  | 
  949  |       if (resUnknown!.status === 200) {
  950  |         flagIssue('TC-DTPAY-010', 'BUG-TPAY-09b',
  951  |           'GET /today-payments?foo=bar returns 200 â€" unknown query parameters should return 400 Bad Request, not silently succeed',
  952  |           { actual_status: 200, query_param: 'foo=bar&baz=qux' },
  953  |         );
  954  |       }
  955  | 
  956  |       logger.info('Asserting: unknown query parameters are rejected with 400, not silently ignored');
  957  |       expect.soft(resDate!.status,
  958  |         '[BUG-TPAY-09a] ?date= parameter is silently ignored â€" endpoint should filter by date or reject the param with 400',
  959  |       ).not.toBe(200);
  960  | 
  961  |       expect.soft(resUnknown!.status,
  962  |         '[BUG-TPAY-09b] Unknown query params should return 400, not 200',
  963  |       ).not.toBe(200);
  964  | 
  965  |       logger.pass('All assertions passed');
  966  |       attachResponse('TC-DTPAY-010', resDate!,    { scenario: '?date=2026-06-01', bug: 'BUG-TPAY-09' });
  967  |       attachResponse('TC-DTPAY-010', resUnknown!, { scenario: '?foo=bar', bug: 'BUG-TPAY-09' });
  968  |     });
  969  |   });
  970  | 
  971  |   // â"€â"€ TC-DTPAY-011 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  972  |   test('TC-DTPAY-011 â€" BUG-TPAY-11: Percentage format must be consistent across sections', async ({ logger }) => {
  973  |     smokeLabels('TC-DTPAY-011', 'BUG-TPAY-11 â€" Inconsistent Percentage Format', 'minor');
  974  | 
  975  |     let res: supertest.Response;
  976  | 
  977  |     await logger.step('Step 1 â€" Fetch today-payments response', async () => {
  978  |       logger.info('GET /api/v1/dashboard/today-payments');
  979  |       res = await get();
  980  |       logger.pass('HTTP ' + res.status + ' received');
  981  |       attachResponse('TC-DTPAY-011', res, { bug: 'BUG-TPAY-11' });
  982  |     });
  983  | 
  984  |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  985  |       logger.info('Asserting: HTTP 200');
> 986  |       expect(res!.status).toBe(200);
       |                           ^ Error: expect(received).toBe(expected) // Object.is equality
  987  |       logger.pass('All assertions passed');
  988  |     });
  989  | 
  990  |     await logger.step('Step 3 â€" BUG-TPAY-11: All postiveValue fields must use consistent decimal formatting', async () => {
  991  |       const payPct = String(res!.body.data.payments.postiveValue ?? '');
  992  |       const refPct = String(res!.body.data.refunds.postiveValue  ?? '');
  993  |       const revPct = String(res!.body.data.revenue.postiveValue  ?? '');
  994  | 
  995  |       allure.parameter('payments.postiveValue', payPct);
  996  |       allure.parameter('refunds.postiveValue',  refPct);
  997  |       allure.parameter('revenue.postiveValue',  revPct);
  998  | 
  999  |       console.log(`[TC-DTPAY-011] payments: "${payPct}", refunds: "${refPct}", revenue: "${revPct}"`);
  1000 | 
  1001 |       // Check format consistency: all should use the same decimal format
  1002 |       const PCT_WITH_DECIMAL = /^-?\d+\.\d+%$/;  // e.g. "-100.0%" or "0.0%"
  1003 |       const PCT_NO_DECIMAL   = /^-?\d+%$/;        // e.g. "0%" or "-100%"
  1004 | 
  1005 |       const payHasDecimal = PCT_WITH_DECIMAL.test(payPct);
  1006 |       const refHasDecimal = PCT_WITH_DECIMAL.test(refPct);
  1007 |       const revHasDecimal = PCT_WITH_DECIMAL.test(revPct);
  1008 | 
  1009 |       const allDecimal   = payHasDecimal && refHasDecimal && revHasDecimal;
  1010 |       const allNoDecimal = !payHasDecimal && !refHasDecimal && !revHasDecimal;
  1011 |       const isConsistent = allDecimal || allNoDecimal;
  1012 | 
  1013 |       allure.parameter('payments has decimal', String(payHasDecimal));
  1014 |       allure.parameter('refunds has decimal',  String(refHasDecimal));
  1015 |       allure.parameter('revenue has decimal',  String(revHasDecimal));
  1016 |       allure.parameter('Format consistent',    String(isConsistent));
  1017 | 
  1018 |       if (!isConsistent) {
  1019 |         flagIssue('TC-DTPAY-011', 'BUG-TPAY-11',
  1020 |           `Inconsistent percentage format: payments="${payPct}" (decimal), refunds="${refPct}" (no decimal), revenue="${revPct}" â€" all 3 sections must use the same format`,
  1021 |           { payments: payPct, refunds: refPct, revenue: revPct, expected: 'consistent decimal format, e.g. all "0.0%" or all "0%"' },
  1022 |         );
  1023 |       }
  1024 | 
  1025 |       logger.info('Asserting: all postiveValue fields use consistent decimal formatting and match percentage pattern');
  1026 |       expect.soft(isConsistent,
  1027 |         `[BUG-TPAY-11] Percentage format is inconsistent: payments="${payPct}", refunds="${refPct}", revenue="${revPct}" â€" must be uniform`,
  1028 |       ).toBe(true);
  1029 | 
  1030 |       // All values must match percentage pattern
  1031 |       const PCT_PATTERN = /^-?\d+(\.\d+)?%$/;
  1032 |       expect(PCT_PATTERN.test(payPct), `payments.postiveValue "${payPct}" must match N% or N.N% format`).toBe(true);
  1033 |       expect(PCT_PATTERN.test(refPct), `refunds.postiveValue "${refPct}" must match N% or N.N% format`).toBe(true);
  1034 |       expect(PCT_PATTERN.test(revPct), `revenue.postiveValue "${revPct}" must match N% or N.N% format`).toBe(true);
  1035 |       logger.pass('All assertions passed');
  1036 |     });
  1037 |   });
  1038 | 
  1039 |   // â"€â"€ TC-DTPAY-012 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  1040 |   test('TC-DTPAY-012 â€" Response-Time SLA: dashboard endpoint must respond within 2 000 ms', async ({ logger }) => {
  1041 |     smokeLabels('TC-DTPAY-012', 'Response-Time SLA', 'minor');
  1042 | 
  1043 |     const SLA_MS = 2000;
  1044 |     let durationMs = 0;
  1045 |     let res: supertest.Response;
  1046 | 
  1047 |     await logger.step('Step 1 â€" Measure round-trip response time', async () => {
  1048 |       logger.info('GET /api/v1/dashboard/today-payments (measuring response time)');
  1049 |       const t0 = Date.now();
  1050 |       res = await get();
  1051 |       durationMs = Date.now() - t0;
  1052 |       logger.pass('HTTP ' + res.status + ' received');
  1053 | 
  1054 |       console.log(`[TC-DTPAY-012] Response time: ${durationMs}ms  (SLA: ${SLA_MS}ms)`);
  1055 |       allure.parameter('Response Time (ms)',  String(durationMs));
  1056 |       allure.parameter('SLA Threshold (ms)',  String(SLA_MS));
  1057 |       allure.parameter('SLA Pass',            String(durationMs < SLA_MS));
  1058 |     });
  1059 | 
  1060 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  1061 |       logger.info('Asserting: HTTP 200');
  1062 |       expect(res!.status).toBe(200);
  1063 |       logger.pass('All assertions passed');
  1064 |     });
  1065 | 
  1066 |     await logger.step(`Step 3 â€" Response time must be under ${SLA_MS}ms`, async () => {
  1067 |       if (durationMs >= SLA_MS) {
  1068 |         flagIssue('TC-DTPAY-012', 'PERF-TPAY-01',
  1069 |           `Response time ${durationMs}ms exceeds ${SLA_MS}ms SLA for a dashboard widget endpoint`,
  1070 |           { actual_ms: durationMs, sla_ms: SLA_MS, endpoint: `GET ${ENDPOINT}` },
  1071 |         );
  1072 |       }
  1073 |       logger.info(`Asserting: response time ${durationMs}ms is under the ${SLA_MS}ms SLA`);
  1074 |       expect(durationMs, `Response time ${durationMs}ms must be < ${SLA_MS}ms`).toBeLessThan(SLA_MS);
  1075 |       logger.pass('All assertions passed');
  1076 |     });
  1077 |   });
  1078 | 
  1079 | });
  1080 | 
```