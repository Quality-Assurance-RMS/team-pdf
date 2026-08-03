# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-014 â€” BUG-SUM-09/10/11: empty period, case sensitivity, incomplete error message
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:873:7

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
  825  |   });
  826  | 
  827  |   // â”€â”€ TC-DSUM-013 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  828  |   test('TC-DSUM-013 â€” BUG-SUM-08: wrong HTTP methods return 500 instead of 405', async ({ logger }) => {
  829  |     smokeLabels('TC-DSUM-013', 'BUG-SUM-08 â€” Wrong HTTP Methods Return 500 Not 405');
  830  | 
  831  |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  832  | 
  833  |     for (const verb of wrongMethods) {
  834  |       await logger.step(`Step â€” ${verb.toUpperCase()} must return 405 Method Not Allowed`, async () => {
  835  |         logger.info(`GET ${ENDPOINT}`);
  836  |         const res = await method(verb, { period: 'week' });
  837  |         logger.pass('HTTP ' + res.status + ' received');
  838  |         console.log(`\n[TC-DSUM-013] ${verb.toUpperCase()}  -> ${res.status}`);
  839  | 
  840  |         if (res.status === 500) {
  841  |           flagIssue('TC-DSUM-013', 'BUG-SUM-08',
  842  |             `${verb.toUpperCase()} /dashboard/summary returns 500 â€” must return 405 Method Not Allowed with Allow: GET, HEAD, OPTIONS header`,
  843  |             { method: verb.toUpperCase(), status: res.status },
  844  |           );
  845  |         }
  846  |         logger.info('Asserting: response structure and values');
  847  |         expect.soft(res.status,
  848  |           `[BUG-SUM-08] ${verb.toUpperCase()} must return 405 not 500`,
  849  |         ).toBe(405);
  850  | 
  851  |         allure.parameter(`${verb.toUpperCase()} status`, String(res.status) + (res.status === 500 ? ' âš  BUG-SUM-08' : ''));
  852  |         logger.pass('All assertions passed');
  853  |       });
  854  |     }
  855  | 
  856  |     await logger.step('Step â€” HEAD and OPTIONS must return 200 (valid methods)', async () => {
  857  |       logger.info(`GET ${ENDPOINT}`);
  858  |       const headRes    = await method('head');
  859  |       logger.pass('HTTP ' + headRes.status + ' received');
  860  |       logger.info(`GET ${ENDPOINT}`);
  861  |       const optionRes  = await method('options');
  862  |       logger.pass('HTTP ' + optionRes.status + ' received');
  863  |       logger.info('Asserting: response structure and values');
  864  |       expect(headRes.status,   'HEAD must return 200').toBe(200);
  865  |       expect(optionRes.status, 'OPTIONS must return 200').toBe(200);
  866  |       allure.parameter('HEAD status',    String(headRes.status));
  867  |       allure.parameter('OPTIONS status', String(optionRes.status));
  868  |       logger.pass('All assertions passed');
  869  |     });
  870  |   });
  871  | 
  872  |   // â”€â”€ TC-DSUM-014 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  873  |   test('TC-DSUM-014 â€” BUG-SUM-09/10/11: empty period, case sensitivity, incomplete error message', async ({ logger }) => {
  874  |     smokeLabels('TC-DSUM-014', 'BUG-SUM-09/10/11 â€” Period Param Edge Cases', 'normal');
  875  | 
  876  |     await logger.step('Step 1 â€” BUG-SUM-09: empty period= must return 400', async () => {
  877  |       logger.info(`GET ${ENDPOINT}`);
  878  |       const res = await get({ period: '' });
  879  |       logger.pass('HTTP ' + res.status + ' received');
  880  |       if (res.status === 200) {
  881  |         flagIssue('TC-DSUM-014', 'BUG-SUM-09',
  882  |           'period= (empty string) returns 200 â€” behaviour is undefined. Must either return 400 or document what default period is applied.',
  883  |           { status: res.status },
  884  |         );
  885  |       }
  886  |       logger.info('Asserting: BUG-SUM-09: empty period= must return 400');
  887  |       expect.soft(res.status, '[BUG-SUM-09] period= must return 400').toBe(400);
  888  |       allure.parameter('Empty period status', String(res.status) + (res.status === 200 ? ' âš  BUG-SUM-09' : ''));
  889  |       logger.pass('All assertions passed');
  890  |     });
  891  | 
  892  |     await logger.step('Step 2 â€” BUG-SUM-10: period=WEEK uppercase must return 200 (case-insensitive)', async () => {
  893  |       logger.info(`GET ${ENDPOINT}`);
  894  |       const res = await get({ period: 'WEEK' });
  895  |       logger.pass('HTTP ' + res.status + ' received');
  896  |       if (res.status === 400) {
  897  |         flagIssue('TC-DSUM-014', 'BUG-SUM-10',
  898  |           'period=WEEK (uppercase) returns 400 â€” period values should not be case-sensitive',
  899  |           { status: res.status, message: res.body.message },
  900  |         );
  901  |       }
  902  |       logger.info('Asserting: BUG-SUM-10: period=WEEK uppercase must return 200');
  903  |       expect.soft(res.status, '[BUG-SUM-10] period=WEEK should return 200').toBe(200);
  904  |       allure.parameter('WEEK uppercase status', String(res.status) + (res.status === 400 ? ' âš  BUG-SUM-10' : ''));
  905  |       logger.pass('All assertions passed');
  906  |     });
  907  | 
  908  |     await logger.step('Step 3 â€” BUG-SUM-11: invalid period error must include today and last-3-month', async () => {
  909  |       logger.info(`GET ${ENDPOINT}`);
  910  |       const res = await get({ period: 'bad_value' });
  911  |       logger.pass('HTTP ' + res.status + ' received');
  912  |       logger.info('Asserting: BUG-SUM-11: invalid period error must include tod');
  913  |       expect(res.status, 'Invalid period  -> 400').toBe(400);
  914  |       const msg = String(res.body.message ?? '').toLowerCase();
  915  | 
  916  |       const shouldList = ['today', 'week', 'month', 'last-3-month', 'year', 'custom'];
  917  |       const missing    = shouldList.filter(v => !msg.includes(v));
  918  | 
  919  |       if (missing.length) {
  920  |         flagIssue('TC-DSUM-014', 'BUG-SUM-11',
  921  |           `Error message omits valid values: [${missing.join(', ')}]. Actual: "${res.body.message}"`,
  922  |           { message: res.body.message, missing },
  923  |         );
  924  |       }
> 925  |       expect.soft(missing.length, `[BUG-SUM-11] Error message must list all valid periods â€” missing: [${missing.join(', ')}]`).toBe(0);
       |                                                                                                                                  ^ Error: [BUG-SUM-11] Error message must list all valid periods â€” missing: [today, week, month, last-3-month, year, custom]
  926  | 
  927  |       allure.parameter('Error message',        String(res.body.message));
  928  |       allure.parameter('Missing from message', missing.join(', ') || 'none âœ“');
  929  |       logger.pass('All assertions passed');
  930  |     });
  931  |   });
  932  | 
  933  |   // â”€â”€ TC-DSUM-015 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  934  |   test('TC-DSUM-015 â€” BUG-SUM-12/13/14: latency SLA, cache headers, CORS configuration', async ({ logger }) => {
  935  |     smokeLabels('TC-DSUM-015', 'BUG-SUM-12/13/14 â€” Latency, Cache Headers, CORS', 'normal');
  936  | 
  937  |     let firstResponseMs = 0;
  938  | 
  939  |     await logger.step('Step 1 â€” BUG-SUM-12: measure response time (P95 SLA < 2000ms)', async () => {
  940  |       const times: number[] = [];
  941  | 
  942  |       for (let i = 0; i < 3; i++) {
  943  |         const start = Date.now();
  944  |         logger.info(`GET ${ENDPOINT}`);
  945  |         const res   = await get({ period: 'week' });
  946  |         logger.pass('HTTP ' + res.status + ' received');
  947  |         const ms    = Date.now() - start;
  948  |         times.push(ms);
  949  |         logger.info('Asserting: BUG-SUM-12: measure response time (P95 SLA < 2000');
  950  |         expect(res.status, `Run ${i + 1} must return 200`).toBe(200);
  951  |         if (i === 0) firstResponseMs = ms;
  952  |         console.log(`[TC-DSUM-015] Run ${i + 1}: ${ms}ms`);
  953  |         allure.parameter(`Response time run ${i + 1}`, `${ms}ms`);
  954  |       }
  955  | 
  956  |       const maxMs = Math.max(...times);
  957  |       const avgMs = Math.round(times.reduce((a, b) => a + b, 0) / times.length);
  958  | 
  959  |       if (maxMs > 2000) {
  960  |         flagIssue('TC-DSUM-015', 'BUG-SUM-12',
  961  |           `Max response time ${maxMs}ms exceeds 2000ms SLA. Individual runs: [${times.join(', ')}]ms. Cold-start spike observed at ${firstResponseMs}ms.`,
  962  |           { times, maxMs, avgMs },
  963  |         );
  964  |       }
  965  |       expect.soft(maxMs,
  966  |         `[BUG-SUM-12] Max response time ${maxMs}ms must be < 2000ms (cold-start spike observed at ${firstResponseMs}ms)`,
  967  |       ).toBeLessThan(2000);
  968  | 
  969  |       allure.parameter('Max response time',   `${maxMs}ms` + (maxMs > 2000 ? ' âš  BUG-SUM-12' : ''));
  970  |       allure.parameter('Avg response time',   `${avgMs}ms`);
  971  |       allure.parameter('First request (cold)', `${firstResponseMs}ms`);
  972  |       logger.pass('All assertions passed');
  973  |     });
  974  | 
  975  |     await logger.step('Step 2 â€” BUG-SUM-13: response must include Cache-Control header', async () => {
  976  |       logger.info(`GET ${ENDPOINT}`);
  977  |       const res          = await get({ period: 'week' });
  978  |       logger.pass('HTTP ' + res.status + ' received');
  979  |       const cacheControl = res.headers['cache-control'];
  980  |       const etag         = res.headers['etag'];
  981  |       const lastModified = res.headers['last-modified'];
  982  | 
  983  |       if (!cacheControl) {
  984  |         flagIssue('TC-DSUM-015', 'BUG-SUM-13',
  985  |           'No Cache-Control header on aggregated metrics endpoint â€” every request triggers full re-computation. Add Cache-Control: max-age=60, private.',
  986  |           { 'cache-control': cacheControl ?? 'MISSING', etag: etag ?? 'MISSING', 'last-modified': lastModified ?? 'MISSING' },
  987  |         );
  988  |       }
  989  |       logger.info('Asserting: BUG-SUM-13: response must include Cache-Control h');
  990  |       expect.soft(cacheControl,
  991  |         '[BUG-SUM-13] Cache-Control header must be present on aggregated metrics endpoint',
  992  |       ).toBeTruthy();
  993  | 
  994  |       allure.parameter('Cache-Control',  cacheControl ?? 'MISSING âš  BUG-SUM-13');
  995  |       allure.parameter('ETag',           etag         ?? 'MISSING');
  996  |       allure.parameter('Last-Modified',  lastModified ?? 'MISSING');
  997  |       logger.pass('All assertions passed');
  998  |     });
  999  | 
  1000 |     await logger.step('Step 3 â€” BUG-SUM-14: CORS must not use wildcard origin on a financial API', async () => {
  1001 |       logger.info(`GET ${ENDPOINT}`);
  1002 |       const res             = await get({ period: 'week' });
  1003 |       logger.pass('HTTP ' + res.status + ' received');
  1004 |       const allowOrigin     = res.headers['access-control-allow-origin'];
  1005 |       const allowMethods    = res.headers['access-control-allow-methods'] ?? '';
  1006 | 
  1007 |       if (allowOrigin === '*') {
  1008 |         flagIssue('TC-DSUM-015', 'BUG-SUM-14',
  1009 |           'Access-Control-Allow-Origin: * allows any website to make requests using the user\'s token â€” should restrict to known frontend origins only.',
  1010 |           { 'access-control-allow-origin': allowOrigin },
  1011 |         );
  1012 |       }
  1013 |       logger.info('Asserting: BUG-SUM-14: CORS must not use wildcard origin on');
  1014 |       expect.soft(allowOrigin,
  1015 |         '[BUG-SUM-14] Access-Control-Allow-Origin must not be wildcard * on a financial API',
  1016 |       ).not.toBe('*');
  1017 | 
  1018 |       const dangerousMethods = ['DELETE', 'PUT', 'PATCH', 'POST'];
  1019 |       const advertised       = dangerousMethods.filter(m => allowMethods.toUpperCase().includes(m));
  1020 |       if (advertised.length > 0) {
  1021 |         flagIssue('TC-DSUM-015', 'BUG-SUM-14',
  1022 |           `Access-Control-Allow-Methods advertises destructive methods [${advertised.join(', ')}] on a read-only endpoint â€” all return 500 when called. Should only list: GET, HEAD, OPTIONS.`,
  1023 |           { 'access-control-allow-methods': allowMethods, advertised },
  1024 |         );
  1025 |       }
```