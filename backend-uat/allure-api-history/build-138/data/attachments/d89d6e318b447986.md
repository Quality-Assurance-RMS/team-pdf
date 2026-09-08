# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-revenue-trend.spec.ts >> Backend (Konza) â€” Dashboard Revenue Trend >> TC-DREVT-011 â€” BUG-REVT-10/12: Cache-Control and security headers must be present
- Location: tests/API/backend-tests/backend-dashboard-revenue-trend.spec.ts:874:7

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
  844  |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  845  | 
  846  |       if (hasWhitelabel) {
  847  |         flagIssue('TC-DREVT-010', 'BUG-REVT-09',
  848  |           'GET /revenue-trend with Accept: text/html returns Spring Boot Whitelabel Error Page â€” ' +
  849  |           'exposes framework identity, server timezone (EAT), and internal path in public response',
  850  |           {
  851  |             status:       resHtml!.status,
  852  |             content_type: resHtml!.headers['content-type'],
  853  |             info_leaked:  ['Spring Boot framework', '/api/v1/dashboard/revenue-trend path', 'Server timezone (EAT)', 'Spring error page structure'],
  854  |             body_excerpt: htmlBody.substring(0, 400),
  855  |           },
  856  |         );
  857  |       }
  858  | 
  859  |       logger.info('Asserting: BUG-REVT-09: 406 error body must NOT expose White');
  860  |       expect.soft(hasWhitelabel,
  861  |         '[BUG-REVT-09] 406 error response must NOT contain Spring Boot Whitelabel Error Page',
  862  |       ).toBe(false);
  863  | 
  864  |       expect.soft(resHtml!.headers['content-type'],
  865  |         '[BUG-REVT-09] 406 error body should be application/json, not text/html',
  866  |       ).toContain('application/json');
  867  | 
  868  |       allure.attachment('[TC-DREVT-010] 406 Response Body', htmlBody, 'text/plain');
  869  |       logger.pass('All assertions passed');
  870  |     });
  871  |   });
  872  | 
  873  |   // â”€â”€ TC-DREVT-011 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  874  |   test('TC-DREVT-011 â€” BUG-REVT-10/12: Cache-Control and security headers must be present', async ({ logger }) => {
  875  |     backendLabels('TC-DREVT-011', 'BUG-REVT-10/12 â€” Missing Cache-Control & Security Headers', 'normal');
  876  | 
  877  |     let res: supertest.Response;
  878  | 
  879  |     await logger.step('Step 1 â€” Fetch response and inspect all security headers', async () => {
  880  |       logger.info(`GET ${ENDPOINT}`);
  881  |       res = await get();
  882  |       logger.pass('HTTP ' + res.status + ' received');
  883  |       console.log(`[TC-DREVT-011] All response headers:\n${JSON.stringify(res.headers, null, 2)}`);
  884  |       attachResponse('TC-DREVT-011', res, { bug: 'BUG-REVT-10/BUG-REVT-12' });
  885  |     });
  886  | 
  887  |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  888  |       logger.info('Asserting: Validate HTTP 200');
  889  |       expect(res!.status).toBe(200);
  890  |       logger.pass('All assertions passed');
  891  |     });
  892  | 
  893  |     await logger.step('Step 3 â€” BUG-REVT-10: Cache-Control must be present and prohibit caching', async () => {
  894  |       const cc = res!.headers['cache-control'] as string | undefined;
  895  |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  896  | 
  897  |       if (!cc) {
  898  |         flagIssue('TC-DREVT-011', 'BUG-REVT-10',
  899  |           'Cache-Control header is missing on /revenue-trend â€” revenue data changes as transactions happen. ' +
  900  |           'Without Cache-Control: no-store, intermediary proxies and CDNs may serve stale financial data to users.',
  901  |           { expected: 'Cache-Control: no-store, no-cache', actual: 'MISSING' },
  902  |         );
  903  |       }
  904  | 
  905  |       logger.info('Asserting: BUG-REVT-10: Cache-Control must be present and pr');
  906  |       expect.soft(cc, '[BUG-REVT-10] Cache-Control header must be present').toBeDefined();
  907  |       if (cc) {
  908  |         const preventsCache = cc.includes('no-store') || cc.includes('no-cache') || cc.includes('private');
  909  |         expect.soft(preventsCache,
  910  |           `[BUG-REVT-10] Cache-Control="${cc}" must include no-store or no-cache for financial data`,
  911  |         ).toBe(true);
  912  |       }
  913  |       logger.pass('All assertions passed');
  914  |     });
  915  | 
  916  |     await logger.step('Step 4 â€” BUG-REVT-12a: X-Content-Type-Options must be nosniff', async () => {
  917  |       const h = res!.headers['x-content-type-options'] as string | undefined;
  918  |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  919  | 
  920  |       if (!h) {
  921  |         flagIssue('TC-DREVT-011', 'BUG-REVT-12a',
  922  |           'X-Content-Type-Options header missing â€” must be "nosniff" to prevent MIME-type sniffing',
  923  |           { expected: 'X-Content-Type-Options: nosniff', actual: 'MISSING' },
  924  |         );
  925  |       }
  926  | 
  927  |       logger.info('Asserting: BUG-REVT-12a: X-Content-Type-Options must be nosn');
  928  |       expect.soft(h, '[BUG-REVT-12a] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  929  |       logger.pass('All assertions passed');
  930  |     });
  931  | 
  932  |     await logger.step('Step 5 â€” BUG-REVT-12b: X-Frame-Options must be present', async () => {
  933  |       const h = res!.headers['x-frame-options'] as string | undefined;
  934  |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  935  | 
  936  |       if (!h) {
  937  |         flagIssue('TC-DREVT-011', 'BUG-REVT-12b',
  938  |           'X-Frame-Options header missing â€” must be "DENY" to prevent clickjacking attacks on financial dashboards',
  939  |           { expected: 'X-Frame-Options: DENY', actual: 'MISSING' },
  940  |         );
  941  |       }
  942  | 
  943  |       logger.info('Asserting: BUG-REVT-12b: X-Frame-Options must be present');
> 944  |       expect.soft(h, '[BUG-REVT-12b] X-Frame-Options: DENY must be present').toBeDefined();
       |                                                                              ^ Error: [BUG-REVT-12b] X-Frame-Options: DENY must be present
  945  |       logger.pass('All assertions passed');
  946  |     });
  947  | 
  948  |     await logger.step('Step 6 â€” Full security header audit', async () => {
  949  |       const audit = {
  950  |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  951  |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  952  |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  953  |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  954  |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  955  |         'referrer-policy':           res!.headers['referrer-policy']           ?? 'MISSING',
  956  |       };
  957  |       allure.attachment('[TC-DREVT-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  958  |       console.log('[TC-DREVT-011] Security audit:', JSON.stringify(audit, null, 2));
  959  |     });
  960  |   });
  961  | 
  962  |   // â”€â”€ TC-DREVT-012 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  963  |   test('TC-DREVT-012 â€” BUG-REVT-11: CORS wildcard origin is insecure for financial trend data', async ({ logger }) => {
  964  |     backendLabels('TC-DREVT-012', 'BUG-REVT-11 â€” Overly Permissive CORS', 'normal');
  965  | 
  966  |     let res: supertest.Response;
  967  | 
  968  |     await logger.step('Step 1 â€” Fetch with Origin header from a test attacker domain', async () => {
  969  |       logger.info(`GET ${ENDPOINT}`);
  970  |       res = await get('', { Origin: 'https://evil-attacker.example.com' });
  971  |       logger.pass('HTTP ' + res.status + ' received');
  972  |       attachResponse('TC-DREVT-012', res, { bug: 'BUG-REVT-11' });
  973  |     });
  974  | 
  975  |     await logger.step('Step 2 â€” Log server CORS response (403 or 200 both possible)', async () => {
  976  |       // NOTE: The server may return 403 for unknown Origin headers (origin allowlist enforcement).
  977  |       // That is acceptable security behavior. We log the actual response and check what CORS
  978  |       // headers are present â€” the real concern is the ACAO: * header on no-Origin requests.
  979  |       console.log(`[TC-DREVT-012] Status with evil-origin: ${res!.status}`);
  980  |       console.log(`[TC-DREVT-012] ACAO header: ${res!.headers['access-control-allow-origin'] ?? 'MISSING'}`);
  981  |       allure.parameter('Status with foreign Origin', String(res!.status));
  982  |       allure.parameter('ACAO with foreign Origin',   res!.headers['access-control-allow-origin'] ?? 'MISSING');
  983  | 
  984  |       if (res!.status === 403) {
  985  |         flagIssue('TC-DREVT-012', 'BUG-REVT-11-INFO',
  986  |           'Server returns 403 for unknown Origin â€” this indicates an origin allowlist is enforced (good). ' +
  987  |           'However, the same endpoint returns Access-Control-Allow-Origin: * when NO Origin header is sent, ' +
  988  |           'which is an inconsistency that may mislead clients about CORS policy.',
  989  |           { status_with_foreign_origin: 403, acao_on_no_origin_requests: '*', note: 'allowlist enforcement appears active' },
  990  |         );
  991  |       }
  992  | 
  993  |       // Server must return either 200 (with restricted ACAO) or 403 (blocked) â€” never 200 + ACAO: *
  994  |       logger.info('Asserting: response structure and values');
  995  |       expect([200, 403, 401],
  996  |         `Status ${res!.status} with foreign Origin header must be 200 (restricted ACAO), 403, or 401`,
  997  |       ).toContain(res!.status);
  998  |       logger.pass('All assertions passed');
  999  |     });
  1000 | 
  1001 |     await logger.step('Step 3 â€” BUG-REVT-11a: Access-Control-Allow-Origin must not be wildcard on a no-Origin request', async () => {
  1002 |       // Check ACAO on a plain request (no Origin header) â€” this is what non-browser clients see
  1003 |       logger.info(`GET ${ENDPOINT}`);
  1004 |       const resNoOrigin = await get();
  1005 |       logger.pass('HTTP ' + resNoOrigin.status + ' received');
  1006 |       const acao = resNoOrigin.headers['access-control-allow-origin'] as string | undefined;
  1007 |       allure.parameter('ACAO (no Origin header)', acao ?? 'MISSING');
  1008 | 
  1009 |       if (acao === '*') {
  1010 |         flagIssue('TC-DREVT-012', 'BUG-REVT-11a',
  1011 |           'Access-Control-Allow-Origin: * is set on responses without an Origin header. ' +
  1012 |           'Even if the origin allowlist blocks cross-origin browsers, the ACAO: * header is ' +
  1013 |           'misleading and violates the principle of explicit CORS allowlisting on financial APIs.',
  1014 |           {
  1015 |             actual:   'Access-Control-Allow-Origin: *',
  1016 |             expected: 'Specific trusted origin or absent when no Origin header in request',
  1017 |             risk:     'Clients and security scanners see ACAO: * and may incorrectly conclude the API is fully public',
  1018 |           },
  1019 |         );
  1020 |       }
  1021 | 
  1022 |       expect.soft(acao,
  1023 |         '[BUG-REVT-11a] Access-Control-Allow-Origin must not be wildcard "*" â€” must list specific trusted origins',
  1024 |       ).not.toBe('*');
  1025 |       logger.pass('All assertions passed');
  1026 |     });
  1027 | 
  1028 |     await logger.step('Step 4 â€” BUG-REVT-11b: Destructive methods must not be CORS-allowed on a GET-only endpoint', async () => {
  1029 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  1030 |       allure.parameter('Access-Control-Allow-Methods', acam ?? 'MISSING');
  1031 | 
  1032 |       const allowsDelete = acam?.toUpperCase().includes('DELETE') ?? false;
  1033 |       const allowsPost   = acam?.toUpperCase().includes('POST')   ?? false;
  1034 |       const allowsPut    = acam?.toUpperCase().includes('PUT')    ?? false;
  1035 |       const allowsPatch  = acam?.toUpperCase().includes('PATCH')  ?? false;
  1036 | 
  1037 |       if (allowsDelete || allowsPost || allowsPut || allowsPatch) {
  1038 |         flagIssue('TC-DREVT-012', 'BUG-REVT-11b',
  1039 |           `CORS allows destructive methods from any origin on a GET-only endpoint: ${acam}. ` +
  1040 |           'Only GET, HEAD, OPTIONS are needed for /revenue-trend.',
  1041 |           {
  1042 |             'access-control-allow-methods': acam,
  1043 |             risk: 'Cross-site requests can use POST/PUT/DELETE on behalf of authenticated users',
  1044 |           },
```