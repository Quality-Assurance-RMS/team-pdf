# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-014 â€” BUG-SUM-09/10/11: empty period, case sensitivity, incomplete error message
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:881:7

# Error details

```
Error: [BUG-SUM-10] period=WEEK should return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

```
Error: [BUG-SUM-11] Error message must list all valid periods â€” missing: [today, week, month, last-3-month, year, custom]

expect(received).toBe(expected) // Object.is equality

Expected: 0
Received: 6
```

# Test source

```ts
  833  |   });
  834  | 
  835  |   // â”€â”€ TC-DSUM-013 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  836  |   test('TC-DSUM-013 â€” BUG-SUM-08: wrong HTTP methods return 500 instead of 405', async ({ logger }) => {
  837  |     backendLabels('TC-DSUM-013', 'BUG-SUM-08 â€” Wrong HTTP Methods Return 500 Not 405');
  838  | 
  839  |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  840  | 
  841  |     for (const verb of wrongMethods) {
  842  |       await logger.step(`Step â€” ${verb.toUpperCase()} must return 405 Method Not Allowed`, async () => {
  843  |         logger.info(`GET ${ENDPOINT}`);
  844  |         const res = await method(verb, { period: 'week' });
  845  |         logger.pass('HTTP ' + res.status + ' received');
  846  |         console.log(`\n[TC-DSUM-013] ${verb.toUpperCase()}  -> ${res.status}`);
  847  | 
  848  |         if (res.status === 500) {
  849  |           flagIssue('TC-DSUM-013', 'BUG-SUM-08',
  850  |             `${verb.toUpperCase()} /dashboard/summary returns 500 â€” must return 405 Method Not Allowed with Allow: GET, HEAD, OPTIONS header`,
  851  |             { method: verb.toUpperCase(), status: res.status },
  852  |           );
  853  |         }
  854  |         logger.info('Asserting: response structure and values');
  855  |         expect.soft(res.status,
  856  |           `[BUG-SUM-08] ${verb.toUpperCase()} must return 405 not 500`,
  857  |         ).toBe(405);
  858  | 
  859  |         allure.parameter(`${verb.toUpperCase()} status`, String(res.status) + (res.status === 500 ? ' âš  BUG-SUM-08' : ''));
  860  |         logger.pass('All assertions passed');
  861  |       });
  862  |     }
  863  | 
  864  |     await logger.step('Step â€” HEAD and OPTIONS must return 200 (valid methods)', async () => {
  865  |       logger.info(`GET ${ENDPOINT}`);
  866  |       const headRes    = await method('head');
  867  |       logger.pass('HTTP ' + headRes.status + ' received');
  868  |       logger.info(`GET ${ENDPOINT}`);
  869  |       const optionRes  = await method('options');
  870  |       logger.pass('HTTP ' + optionRes.status + ' received');
  871  |       logger.info('Asserting: response structure and values');
  872  |       expect(headRes.status,   'HEAD must return 200').toBe(200);
  873  |       expect(optionRes.status, 'OPTIONS must return 200').toBe(200);
  874  |       allure.parameter('HEAD status',    String(headRes.status));
  875  |       allure.parameter('OPTIONS status', String(optionRes.status));
  876  |       logger.pass('All assertions passed');
  877  |     });
  878  |   });
  879  | 
  880  |   // â”€â”€ TC-DSUM-014 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  881  |   test('TC-DSUM-014 â€” BUG-SUM-09/10/11: empty period, case sensitivity, incomplete error message', async ({ logger }) => {
  882  |     backendLabels('TC-DSUM-014', 'BUG-SUM-09/10/11 â€” Period Param Edge Cases', 'normal');
  883  | 
  884  |     await logger.step('Step 1 â€” BUG-SUM-09: empty period= must return 400', async () => {
  885  |       logger.info(`GET ${ENDPOINT}`);
  886  |       const res = await get({ period: '' });
  887  |       logger.pass('HTTP ' + res.status + ' received');
  888  |       if (res.status === 200) {
  889  |         flagIssue('TC-DSUM-014', 'BUG-SUM-09',
  890  |           'period= (empty string) returns 200 â€” behaviour is undefined. Must either return 400 or document what default period is applied.',
  891  |           { status: res.status },
  892  |         );
  893  |       }
  894  |       logger.info('Asserting: BUG-SUM-09: empty period= must return 400');
  895  |       expect.soft(res.status, '[BUG-SUM-09] period= must return 400').toBe(400);
  896  |       allure.parameter('Empty period status', String(res.status) + (res.status === 200 ? ' âš  BUG-SUM-09' : ''));
  897  |       logger.pass('All assertions passed');
  898  |     });
  899  | 
  900  |     await logger.step('Step 2 â€” BUG-SUM-10: period=WEEK uppercase must return 200 (case-insensitive)', async () => {
  901  |       logger.info(`GET ${ENDPOINT}`);
  902  |       const res = await get({ period: 'WEEK' });
  903  |       logger.pass('HTTP ' + res.status + ' received');
  904  |       if (res.status === 400) {
  905  |         flagIssue('TC-DSUM-014', 'BUG-SUM-10',
  906  |           'period=WEEK (uppercase) returns 400 â€” period values should not be case-sensitive',
  907  |           { status: res.status, message: res.body.message },
  908  |         );
  909  |       }
  910  |       logger.info('Asserting: BUG-SUM-10: period=WEEK uppercase must return 200');
  911  |       expect.soft(res.status, '[BUG-SUM-10] period=WEEK should return 200').toBe(200);
  912  |       allure.parameter('WEEK uppercase status', String(res.status) + (res.status === 400 ? ' âš  BUG-SUM-10' : ''));
  913  |       logger.pass('All assertions passed');
  914  |     });
  915  | 
  916  |     await logger.step('Step 3 â€” BUG-SUM-11: invalid period error must include today and last-3-month', async () => {
  917  |       logger.info(`GET ${ENDPOINT}`);
  918  |       const res = await get({ period: 'bad_value' });
  919  |       logger.pass('HTTP ' + res.status + ' received');
  920  |       logger.info('Asserting: BUG-SUM-11: invalid period error must include tod');
  921  |       expect(res.status, 'Invalid period  -> 400').toBe(400);
  922  |       const msg = String(res.body.message ?? '').toLowerCase();
  923  | 
  924  |       const shouldList = ['today', 'week', 'month', 'last-3-month', 'year', 'custom'];
  925  |       const missing    = shouldList.filter(v => !msg.includes(v));
  926  | 
  927  |       if (missing.length) {
  928  |         flagIssue('TC-DSUM-014', 'BUG-SUM-11',
  929  |           `Error message omits valid values: [${missing.join(', ')}]. Actual: "${res.body.message}"`,
  930  |           { message: res.body.message, missing },
  931  |         );
  932  |       }
> 933  |       expect.soft(missing.length, `[BUG-SUM-11] Error message must list all valid periods â€” missing: [${missing.join(', ')}]`).toBe(0);
       |                                                                                                                                  ^ Error: [BUG-SUM-11] Error message must list all valid periods â€” missing: [today, week, month, last-3-month, year, custom]
  934  | 
  935  |       allure.parameter('Error message',        String(res.body.message));
  936  |       allure.parameter('Missing from message', missing.join(', ') || 'none âœ“');
  937  |       logger.pass('All assertions passed');
  938  |     });
  939  |   });
  940  | 
  941  |   // â”€â”€ TC-DSUM-015 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  942  |   test('TC-DSUM-015 â€” BUG-SUM-12/13/14: latency SLA, cache headers, CORS configuration', async ({ logger }) => {
  943  |     backendLabels('TC-DSUM-015', 'BUG-SUM-12/13/14 â€” Latency, Cache Headers, CORS', 'normal');
  944  | 
  945  |     let firstResponseMs = 0;
  946  | 
  947  |     await logger.step('Step 1 â€” BUG-SUM-12: measure response time (P95 SLA < 2000ms)', async () => {
  948  |       const times: number[] = [];
  949  | 
  950  |       for (let i = 0; i < 3; i++) {
  951  |         const start = Date.now();
  952  |         logger.info(`GET ${ENDPOINT}`);
  953  |         const res   = await get({ period: 'week' });
  954  |         logger.pass('HTTP ' + res.status + ' received');
  955  |         const ms    = Date.now() - start;
  956  |         times.push(ms);
  957  |         logger.info('Asserting: BUG-SUM-12: measure response time (P95 SLA < 2000');
  958  |         expect(res.status, `Run ${i + 1} must return 200`).toBe(200);
  959  |         if (i === 0) firstResponseMs = ms;
  960  |         console.log(`[TC-DSUM-015] Run ${i + 1}: ${ms}ms`);
  961  |         allure.parameter(`Response time run ${i + 1}`, `${ms}ms`);
  962  |       }
  963  | 
  964  |       const maxMs = Math.max(...times);
  965  |       const avgMs = Math.round(times.reduce((a, b) => a + b, 0) / times.length);
  966  | 
  967  |       if (maxMs > 2000) {
  968  |         flagIssue('TC-DSUM-015', 'BUG-SUM-12',
  969  |           `Max response time ${maxMs}ms exceeds 2000ms SLA. Individual runs: [${times.join(', ')}]ms. Cold-start spike observed at ${firstResponseMs}ms.`,
  970  |           { times, maxMs, avgMs },
  971  |         );
  972  |       }
  973  |       expect.soft(maxMs,
  974  |         `[BUG-SUM-12] Max response time ${maxMs}ms must be < 2000ms (cold-start spike observed at ${firstResponseMs}ms)`,
  975  |       ).toBeLessThan(2000);
  976  | 
  977  |       allure.parameter('Max response time',   `${maxMs}ms` + (maxMs > 2000 ? ' âš  BUG-SUM-12' : ''));
  978  |       allure.parameter('Avg response time',   `${avgMs}ms`);
  979  |       allure.parameter('First request (cold)', `${firstResponseMs}ms`);
  980  |       logger.pass('All assertions passed');
  981  |     });
  982  | 
  983  |     await logger.step('Step 2 â€” BUG-SUM-13: response must include Cache-Control header', async () => {
  984  |       logger.info(`GET ${ENDPOINT}`);
  985  |       const res          = await get({ period: 'week' });
  986  |       logger.pass('HTTP ' + res.status + ' received');
  987  |       const cacheControl = res.headers['cache-control'];
  988  |       const etag         = res.headers['etag'];
  989  |       const lastModified = res.headers['last-modified'];
  990  | 
  991  |       if (!cacheControl) {
  992  |         flagIssue('TC-DSUM-015', 'BUG-SUM-13',
  993  |           'No Cache-Control header on aggregated metrics endpoint â€” every request triggers full re-computation. Add Cache-Control: max-age=60, private.',
  994  |           { 'cache-control': cacheControl ?? 'MISSING', etag: etag ?? 'MISSING', 'last-modified': lastModified ?? 'MISSING' },
  995  |         );
  996  |       }
  997  |       logger.info('Asserting: BUG-SUM-13: response must include Cache-Control h');
  998  |       expect.soft(cacheControl,
  999  |         '[BUG-SUM-13] Cache-Control header must be present on aggregated metrics endpoint',
  1000 |       ).toBeTruthy();
  1001 | 
  1002 |       allure.parameter('Cache-Control',  cacheControl ?? 'MISSING âš  BUG-SUM-13');
  1003 |       allure.parameter('ETag',           etag         ?? 'MISSING');
  1004 |       allure.parameter('Last-Modified',  lastModified ?? 'MISSING');
  1005 |       logger.pass('All assertions passed');
  1006 |     });
  1007 | 
  1008 |     await logger.step('Step 3 â€” BUG-SUM-14: CORS must not use wildcard origin on a financial API', async () => {
  1009 |       logger.info(`GET ${ENDPOINT}`);
  1010 |       const res             = await get({ period: 'week' });
  1011 |       logger.pass('HTTP ' + res.status + ' received');
  1012 |       const allowOrigin     = res.headers['access-control-allow-origin'];
  1013 |       const allowMethods    = res.headers['access-control-allow-methods'] ?? '';
  1014 | 
  1015 |       if (allowOrigin === '*') {
  1016 |         flagIssue('TC-DSUM-015', 'BUG-SUM-14',
  1017 |           'Access-Control-Allow-Origin: * allows any website to make requests using the user\'s token â€” should restrict to known frontend origins only.',
  1018 |           { 'access-control-allow-origin': allowOrigin },
  1019 |         );
  1020 |       }
  1021 |       logger.info('Asserting: BUG-SUM-14: CORS must not use wildcard origin on');
  1022 |       expect.soft(allowOrigin,
  1023 |         '[BUG-SUM-14] Access-Control-Allow-Origin must not be wildcard * on a financial API',
  1024 |       ).not.toBe('*');
  1025 | 
  1026 |       const dangerousMethods = ['DELETE', 'PUT', 'PATCH', 'POST'];
  1027 |       const advertised       = dangerousMethods.filter(m => allowMethods.toUpperCase().includes(m));
  1028 |       if (advertised.length > 0) {
  1029 |         flagIssue('TC-DSUM-015', 'BUG-SUM-14',
  1030 |           `Access-Control-Allow-Methods advertises destructive methods [${advertised.join(', ')}] on a read-only endpoint â€” all return 500 when called. Should only list: GET, HEAD, OPTIONS.`,
  1031 |           { 'access-control-allow-methods': allowMethods, advertised },
  1032 |         );
  1033 |       }
```