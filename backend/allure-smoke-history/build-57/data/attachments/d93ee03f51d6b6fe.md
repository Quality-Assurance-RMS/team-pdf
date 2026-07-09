# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-011 â€” BUG-REVT-10/12: Cache-Control and security headers must be present
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:866:7

# Error details

```
Error: [BUG-REVT-12a] X-Content-Type-Options: nosniff must be present

expect(received).toBe(expected) // Object.is equality

Expected: "nosniff"
Received: undefined
```

```
Error: [BUG-REVT-12b] X-Frame-Options: DENY must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
  836  |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  837  | 
  838  |       if (hasWhitelabel) {
  839  |         flagIssue('TC-DREVT-010', 'BUG-REVT-09',
  840  |           'GET /revenue-trend with Accept: text/html returns Spring Boot Whitelabel Error Page â€” ' +
  841  |           'exposes framework identity, server timezone (EAT), and internal path in public response',
  842  |           {
  843  |             status:       resHtml!.status,
  844  |             content_type: resHtml!.headers['content-type'],
  845  |             info_leaked:  ['Spring Boot framework', '/api/v1/dashboard/revenue-trend path', 'Server timezone (EAT)', 'Spring error page structure'],
  846  |             body_excerpt: htmlBody.substring(0, 400),
  847  |           },
  848  |         );
  849  |       }
  850  | 
  851  |       logger.info('Asserting: BUG-REVT-09: 406 error body must NOT expose White');
  852  |       expect.soft(hasWhitelabel,
  853  |         '[BUG-REVT-09] 406 error response must NOT contain Spring Boot Whitelabel Error Page',
  854  |       ).toBe(false);
  855  | 
  856  |       expect.soft(resHtml!.headers['content-type'],
  857  |         '[BUG-REVT-09] 406 error body should be application/json, not text/html',
  858  |       ).toContain('application/json');
  859  | 
  860  |       allure.attachment('[TC-DREVT-010] 406 Response Body', htmlBody, 'text/plain');
  861  |       logger.pass('All assertions passed');
  862  |     });
  863  |   });
  864  | 
  865  |   // â”€â”€ TC-DREVT-011 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  866  |   test('TC-DREVT-011 â€” BUG-REVT-10/12: Cache-Control and security headers must be present', async ({ logger }) => {
  867  |     smokeLabels('TC-DREVT-011', 'BUG-REVT-10/12 â€” Missing Cache-Control & Security Headers', 'normal');
  868  | 
  869  |     let res: supertest.Response;
  870  | 
  871  |     await logger.step('Step 1 â€” Fetch response and inspect all security headers', async () => {
  872  |       logger.info(`GET ${ENDPOINT}`);
  873  |       res = await get();
  874  |       logger.pass('HTTP ' + res.status + ' received');
  875  |       console.log(`[TC-DREVT-011] All response headers:\n${JSON.stringify(res.headers, null, 2)}`);
  876  |       attachResponse('TC-DREVT-011', res, { bug: 'BUG-REVT-10/BUG-REVT-12' });
  877  |     });
  878  | 
  879  |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  880  |       logger.info('Asserting: Validate HTTP 200');
  881  |       expect(res!.status).toBe(200);
  882  |       logger.pass('All assertions passed');
  883  |     });
  884  | 
  885  |     await logger.step('Step 3 â€” BUG-REVT-10: Cache-Control must be present and prohibit caching', async () => {
  886  |       const cc = res!.headers['cache-control'] as string | undefined;
  887  |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  888  | 
  889  |       if (!cc) {
  890  |         flagIssue('TC-DREVT-011', 'BUG-REVT-10',
  891  |           'Cache-Control header is missing on /revenue-trend â€” revenue data changes as transactions happen. ' +
  892  |           'Without Cache-Control: no-store, intermediary proxies and CDNs may serve stale financial data to users.',
  893  |           { expected: 'Cache-Control: no-store, no-cache', actual: 'MISSING' },
  894  |         );
  895  |       }
  896  | 
  897  |       logger.info('Asserting: BUG-REVT-10: Cache-Control must be present and pr');
  898  |       expect.soft(cc, '[BUG-REVT-10] Cache-Control header must be present').toBeDefined();
  899  |       if (cc) {
  900  |         const preventsCache = cc.includes('no-store') || cc.includes('no-cache') || cc.includes('private');
  901  |         expect.soft(preventsCache,
  902  |           `[BUG-REVT-10] Cache-Control="${cc}" must include no-store or no-cache for financial data`,
  903  |         ).toBe(true);
  904  |       }
  905  |       logger.pass('All assertions passed');
  906  |     });
  907  | 
  908  |     await logger.step('Step 4 â€” BUG-REVT-12a: X-Content-Type-Options must be nosniff', async () => {
  909  |       const h = res!.headers['x-content-type-options'] as string | undefined;
  910  |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  911  | 
  912  |       if (!h) {
  913  |         flagIssue('TC-DREVT-011', 'BUG-REVT-12a',
  914  |           'X-Content-Type-Options header missing â€” must be "nosniff" to prevent MIME-type sniffing',
  915  |           { expected: 'X-Content-Type-Options: nosniff', actual: 'MISSING' },
  916  |         );
  917  |       }
  918  | 
  919  |       logger.info('Asserting: BUG-REVT-12a: X-Content-Type-Options must be nosn');
  920  |       expect.soft(h, '[BUG-REVT-12a] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  921  |       logger.pass('All assertions passed');
  922  |     });
  923  | 
  924  |     await logger.step('Step 5 â€” BUG-REVT-12b: X-Frame-Options must be present', async () => {
  925  |       const h = res!.headers['x-frame-options'] as string | undefined;
  926  |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  927  | 
  928  |       if (!h) {
  929  |         flagIssue('TC-DREVT-011', 'BUG-REVT-12b',
  930  |           'X-Frame-Options header missing â€” must be "DENY" to prevent clickjacking attacks on financial dashboards',
  931  |           { expected: 'X-Frame-Options: DENY', actual: 'MISSING' },
  932  |         );
  933  |       }
  934  | 
  935  |       logger.info('Asserting: BUG-REVT-12b: X-Frame-Options must be present');
> 936  |       expect.soft(h, '[BUG-REVT-12b] X-Frame-Options: DENY must be present').toBeDefined();
       |                                                                              ^ Error: [BUG-REVT-12b] X-Frame-Options: DENY must be present
  937  |       logger.pass('All assertions passed');
  938  |     });
  939  | 
  940  |     await logger.step('Step 6 â€” Full security header audit', async () => {
  941  |       const audit = {
  942  |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  943  |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  944  |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  945  |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  946  |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  947  |         'referrer-policy':           res!.headers['referrer-policy']           ?? 'MISSING',
  948  |       };
  949  |       allure.attachment('[TC-DREVT-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  950  |       console.log('[TC-DREVT-011] Security audit:', JSON.stringify(audit, null, 2));
  951  |     });
  952  |   });
  953  | 
  954  |   // â”€â”€ TC-DREVT-012 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  955  |   test('TC-DREVT-012 â€” BUG-REVT-11: CORS wildcard origin is insecure for financial trend data', async ({ logger }) => {
  956  |     smokeLabels('TC-DREVT-012', 'BUG-REVT-11 â€” Overly Permissive CORS', 'normal');
  957  | 
  958  |     let res: supertest.Response;
  959  | 
  960  |     await logger.step('Step 1 â€” Fetch with Origin header from a test attacker domain', async () => {
  961  |       logger.info(`GET ${ENDPOINT}`);
  962  |       res = await get('', { Origin: 'https://evil-attacker.example.com' });
  963  |       logger.pass('HTTP ' + res.status + ' received');
  964  |       attachResponse('TC-DREVT-012', res, { bug: 'BUG-REVT-11' });
  965  |     });
  966  | 
  967  |     await logger.step('Step 2 â€” Log server CORS response (403 or 200 both possible)', async () => {
  968  |       // NOTE: The server may return 403 for unknown Origin headers (origin allowlist enforcement).
  969  |       // That is acceptable security behavior. We log the actual response and check what CORS
  970  |       // headers are present â€” the real concern is the ACAO: * header on no-Origin requests.
  971  |       console.log(`[TC-DREVT-012] Status with evil-origin: ${res!.status}`);
  972  |       console.log(`[TC-DREVT-012] ACAO header: ${res!.headers['access-control-allow-origin'] ?? 'MISSING'}`);
  973  |       allure.parameter('Status with foreign Origin', String(res!.status));
  974  |       allure.parameter('ACAO with foreign Origin',   res!.headers['access-control-allow-origin'] ?? 'MISSING');
  975  | 
  976  |       if (res!.status === 403) {
  977  |         flagIssue('TC-DREVT-012', 'BUG-REVT-11-INFO',
  978  |           'Server returns 403 for unknown Origin â€” this indicates an origin allowlist is enforced (good). ' +
  979  |           'However, the same endpoint returns Access-Control-Allow-Origin: * when NO Origin header is sent, ' +
  980  |           'which is an inconsistency that may mislead clients about CORS policy.',
  981  |           { status_with_foreign_origin: 403, acao_on_no_origin_requests: '*', note: 'allowlist enforcement appears active' },
  982  |         );
  983  |       }
  984  | 
  985  |       // Server must return either 200 (with restricted ACAO) or 403 (blocked) â€” never 200 + ACAO: *
  986  |       logger.info('Asserting: response structure and values');
  987  |       expect([200, 403, 401],
  988  |         `Status ${res!.status} with foreign Origin header must be 200 (restricted ACAO), 403, or 401`,
  989  |       ).toContain(res!.status);
  990  |       logger.pass('All assertions passed');
  991  |     });
  992  | 
  993  |     await logger.step('Step 3 â€” BUG-REVT-11a: Access-Control-Allow-Origin must not be wildcard on a no-Origin request', async () => {
  994  |       // Check ACAO on a plain request (no Origin header) â€” this is what non-browser clients see
  995  |       logger.info(`GET ${ENDPOINT}`);
  996  |       const resNoOrigin = await get();
  997  |       logger.pass('HTTP ' + resNoOrigin.status + ' received');
  998  |       const acao = resNoOrigin.headers['access-control-allow-origin'] as string | undefined;
  999  |       allure.parameter('ACAO (no Origin header)', acao ?? 'MISSING');
  1000 | 
  1001 |       if (acao === '*') {
  1002 |         flagIssue('TC-DREVT-012', 'BUG-REVT-11a',
  1003 |           'Access-Control-Allow-Origin: * is set on responses without an Origin header. ' +
  1004 |           'Even if the origin allowlist blocks cross-origin browsers, the ACAO: * header is ' +
  1005 |           'misleading and violates the principle of explicit CORS allowlisting on financial APIs.',
  1006 |           {
  1007 |             actual:   'Access-Control-Allow-Origin: *',
  1008 |             expected: 'Specific trusted origin or absent when no Origin header in request',
  1009 |             risk:     'Clients and security scanners see ACAO: * and may incorrectly conclude the API is fully public',
  1010 |           },
  1011 |         );
  1012 |       }
  1013 | 
  1014 |       expect.soft(acao,
  1015 |         '[BUG-REVT-11a] Access-Control-Allow-Origin must not be wildcard "*" â€” must list specific trusted origins',
  1016 |       ).not.toBe('*');
  1017 |       logger.pass('All assertions passed');
  1018 |     });
  1019 | 
  1020 |     await logger.step('Step 4 â€” BUG-REVT-11b: Destructive methods must not be CORS-allowed on a GET-only endpoint', async () => {
  1021 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  1022 |       allure.parameter('Access-Control-Allow-Methods', acam ?? 'MISSING');
  1023 | 
  1024 |       const allowsDelete = acam?.toUpperCase().includes('DELETE') ?? false;
  1025 |       const allowsPost   = acam?.toUpperCase().includes('POST')   ?? false;
  1026 |       const allowsPut    = acam?.toUpperCase().includes('PUT')    ?? false;
  1027 |       const allowsPatch  = acam?.toUpperCase().includes('PATCH')  ?? false;
  1028 | 
  1029 |       if (allowsDelete || allowsPost || allowsPut || allowsPatch) {
  1030 |         flagIssue('TC-DREVT-012', 'BUG-REVT-11b',
  1031 |           `CORS allows destructive methods from any origin on a GET-only endpoint: ${acam}. ` +
  1032 |           'Only GET, HEAD, OPTIONS are needed for /revenue-trend.',
  1033 |           {
  1034 |             'access-control-allow-methods': acam,
  1035 |             risk: 'Cross-site requests can use POST/PUT/DELETE on behalf of authenticated users',
  1036 |           },
```