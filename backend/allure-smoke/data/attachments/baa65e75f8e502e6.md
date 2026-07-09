# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-today-payments.spec.ts >> Smoke â€" Dashboard Today Payments >> TC-DTPAY-009 â€" BUG-TPAY-08: CORS wildcard origin is insecure for financial API
- Location: tests/API/smoke/smoke-dashboard-today-payments.spec.ts:847:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 403
```

# Test source

```ts
  761 |     await logger.step('Step 1 â€" Fetch response and inspect headers', async () => {
  762 |       logger.info('GET /api/v1/dashboard/today-payments');
  763 |       res = await get();
  764 |       logger.pass('HTTP ' + res.status + ' received');
  765 |       console.log(`[TC-DTPAY-008] Response headers:`);
  766 |       console.log(JSON.stringify(res.headers, null, 2));
  767 |       attachResponse('TC-DTPAY-008', res, { bug: 'BUG-TPAY-07/BUG-TPAY-10' });
  768 |     });
  769 | 
  770 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  771 |       logger.info('Asserting: HTTP 200');
  772 |       expect(res!.status).toBe(200);
  773 |       logger.pass('All assertions passed');
  774 |     });
  775 | 
  776 |     await logger.step('Step 3 â€" BUG-TPAY-07: Cache-Control must prohibit caching of real-time financial data', async () => {
  777 |       const cacheControl = res!.headers['cache-control'] as string | undefined;
  778 | 
  779 |       allure.parameter('Cache-Control header', cacheControl ?? 'MISSING');
  780 | 
  781 |       if (!cacheControl) {
  782 |         flagIssue('TC-DTPAY-008', 'BUG-TPAY-07',
  783 |           'Cache-Control header is missing â€" "today-payments" is real-time financial data that must never be cached by proxies or browsers',
  784 |           { expected: 'Cache-Control: no-store, no-cache', actual: 'MISSING' },
  785 |         );
  786 |       }
  787 | 
  788 |       logger.info('Asserting: Cache-Control header is present and prevents caching of real-time financial data');
  789 |       expect.soft(cacheControl, '[BUG-TPAY-07] Cache-Control header must be present for real-time financial data').toBeDefined();
  790 | 
  791 |       if (cacheControl) {
  792 |         const preventsCache = cacheControl.includes('no-store') || cacheControl.includes('no-cache') || cacheControl.includes('private');
  793 |         expect.soft(preventsCache, `[BUG-TPAY-07] Cache-Control="${cacheControl}" must include no-store or no-cache`).toBe(true);
  794 |       }
  795 |       logger.pass('All assertions passed');
  796 |     });
  797 | 
  798 |     await logger.step('Step 4 â€" BUG-TPAY-10: X-Content-Type-Options must be set to nosniff', async () => {
  799 |       const headerVal = res!.headers['x-content-type-options'] as string | undefined;
  800 |       allure.parameter('X-Content-Type-Options', headerVal ?? 'MISSING');
  801 | 
  802 |       if (!headerVal) {
  803 |         flagIssue('TC-DTPAY-008', 'BUG-TPAY-10a',
  804 |           'X-Content-Type-Options header is missing â€" must be "nosniff" to prevent MIME-type sniffing attacks',
  805 |           { expected: 'X-Content-Type-Options: nosniff', actual: 'MISSING' },
  806 |         );
  807 |       }
  808 | 
  809 |       logger.info('Asserting: X-Content-Type-Options is set to "nosniff"');
  810 |       expect.soft(headerVal, '[BUG-TPAY-10a] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  811 |       logger.pass('All assertions passed');
  812 |     });
  813 | 
  814 |     await logger.step('Step 5 â€" BUG-TPAY-10: X-Frame-Options must be set', async () => {
  815 |       const headerVal = res!.headers['x-frame-options'] as string | undefined;
  816 |       allure.parameter('X-Frame-Options', headerVal ?? 'MISSING');
  817 | 
  818 |       if (!headerVal) {
  819 |         flagIssue('TC-DTPAY-008', 'BUG-TPAY-10b',
  820 |           'X-Frame-Options header is missing â€" must be "DENY" to prevent clickjacking attacks',
  821 |           { expected: 'X-Frame-Options: DENY', actual: 'MISSING' },
  822 |         );
  823 |       }
  824 | 
  825 |       logger.info('Asserting: X-Frame-Options header is present');
  826 |       expect.soft(headerVal, '[BUG-TPAY-10b] X-Frame-Options: DENY must be present').toBeDefined();
  827 |       logger.pass('All assertions passed');
  828 |     });
  829 | 
  830 |     await logger.step('Step 6 â€" Log all security-relevant headers for audit', async () => {
  831 |       const securityHeaders = {
  832 |         'cache-control':           res!.headers['cache-control']           ?? 'MISSING',
  833 |         'x-content-type-options':  res!.headers['x-content-type-options']  ?? 'MISSING',
  834 |         'x-frame-options':         res!.headers['x-frame-options']         ?? 'MISSING',
  835 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  836 |         'content-security-policy': res!.headers['content-security-policy'] ?? 'MISSING',
  837 |       };
  838 | 
  839 |       allure.attachment('[TC-DTPAY-008] Security Headers Audit', JSON.stringify(securityHeaders, null, 2), 'application/json');
  840 |       console.log(`[TC-DTPAY-008] Security headers:`, JSON.stringify(securityHeaders, null, 2));
  841 |       logger.info('Asserting: all security-relevant headers are logged for audit');
  842 |       logger.pass('All assertions passed');
  843 |     });
  844 |   });
  845 | 
  846 |   // â"€â"€ TC-DTPAY-009 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  847 |   test('TC-DTPAY-009 â€" BUG-TPAY-08: CORS wildcard origin is insecure for financial API', async ({ logger }) => {
  848 |     smokeLabels('TC-DTPAY-009', 'BUG-TPAY-08 â€" Overly Permissive CORS', 'normal');
  849 | 
  850 |     let res: supertest.Response;
  851 | 
  852 |     await logger.step('Step 1 â€" Fetch response and inspect CORS headers', async () => {
  853 |       logger.info('GET /api/v1/dashboard/today-payments (Origin: https://evil-attacker.example.com)');
  854 |       res = await get({ Origin: 'https://evil-attacker.example.com' });
  855 |       logger.pass('HTTP ' + res.status + ' received');
  856 |       attachResponse('TC-DTPAY-009', res, { bug: 'BUG-TPAY-08' });
  857 |     });
  858 | 
  859 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  860 |       logger.info('Asserting: HTTP 200');
> 861 |       expect(res!.status).toBe(200);
      |                           ^ Error: expect(received).toBe(expected) // Object.is equality
  862 |       logger.pass('All assertions passed');
  863 |     });
  864 | 
  865 |     await logger.step('Step 3 â€" BUG-TPAY-08a: Access-Control-Allow-Origin must not be wildcard *', async () => {
  866 |       const acao = res!.headers['access-control-allow-origin'] as string | undefined;
  867 |       allure.parameter('Access-Control-Allow-Origin', acao ?? 'MISSING');
  868 | 
  869 |       if (acao === '*') {
  870 |         flagIssue('TC-DTPAY-009', 'BUG-TPAY-08a',
  871 |           'Access-Control-Allow-Origin: * allows any website to read financial dashboard data from authenticated users â€" must be restricted to known origins',
  872 |           { actual: 'Access-Control-Allow-Origin: *', expected: 'Specific trusted domains (e.g. https://rms.dev.demo-fsit.com)', risk: 'Cross-site data leakage' },
  873 |         );
  874 |       }
  875 | 
  876 |       logger.info('Asserting: Access-Control-Allow-Origin is not wildcard "*"');
  877 |       expect.soft(acao,
  878 |         '[BUG-TPAY-08a] Access-Control-Allow-Origin must not be wildcard "*" for a financial API',
  879 |       ).not.toBe('*');
  880 |       logger.pass('All assertions passed');
  881 |     });
  882 | 
  883 |     await logger.step('Step 4 â€" BUG-TPAY-08b: Destructive methods must not be CORS-allowed', async () => {
  884 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  885 |       allure.parameter('Access-Control-Allow-Methods', acam ?? 'MISSING');
  886 | 
  887 |       const allowsDelete = acam?.toUpperCase().includes('DELETE');
  888 |       const allowsPost   = acam?.toUpperCase().includes('POST');
  889 |       const allowsPut    = acam?.toUpperCase().includes('PUT');
  890 |       const allowsPatch  = acam?.toUpperCase().includes('PATCH');
  891 | 
  892 |       if (allowsDelete || allowsPost || allowsPut || allowsPatch) {
  893 |         flagIssue('TC-DTPAY-009', 'BUG-TPAY-08b',
  894 |           `CORS allows destructive methods (${[allowsPost && 'POST', allowsPut && 'PUT', allowsDelete && 'DELETE', allowsPatch && 'PATCH'].filter(Boolean).join(', ')}) from any origin â€" for a read-only dashboard endpoint, only GET, HEAD, OPTIONS are needed`,
  895 |           { 'access-control-allow-methods': acam, risk: 'Any site can trigger POST/PUT/DELETE on behalf of authenticated users' },
  896 |         );
  897 |       }
  898 | 
  899 |       logger.info('Asserting: CORS does not allow DELETE or POST on this read-only endpoint');
  900 |       // This is a GET-only endpoint â€" only GET, HEAD, OPTIONS should be CORS-allowed
  901 |       expect.soft(allowsDelete, '[BUG-TPAY-08b] CORS must not allow DELETE on a read-only endpoint').toBe(false);
  902 |       expect.soft(allowsPost,   '[BUG-TPAY-08b] CORS must not allow POST on a read-only endpoint').toBe(false);
  903 |       logger.pass('All assertions passed');
  904 |     });
  905 |   });
  906 | 
  907 |   // â"€â"€ TC-DTPAY-010 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  908 |   test('TC-DTPAY-010 â€" BUG-TPAY-09: Unknown query parameters must not be silently ignored', async ({ logger }) => {
  909 |     smokeLabels('TC-DTPAY-010', 'BUG-TPAY-09 â€" Unknown Query Params Silently Ignored', 'minor');
  910 | 
  911 |     let resDate:    supertest.Response;
  912 |     let resUnknown: supertest.Response;
  913 | 
  914 |     await logger.step('Step 1 â€" Send GET with unknown date parameter', async () => {
  915 |       logger.info('GET /api/v1/dashboard/today-payments?date=2026-06-01');
  916 |       resDate = await supertest(BASE_URL)
  917 |         .get(`${ENDPOINT}?date=2026-06-01`)
  918 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  919 |         .set('Accept', 'application/json');
  920 |       logger.pass('HTTP ' + resDate.status + ' received');
  921 | 
  922 |       console.log(`[TC-DTPAY-010] ?date=2026-06-01  -> ${resDate.status}`);
  923 |       allure.parameter('?date=2026-06-01 status', String(resDate.status));
  924 |     });
  925 | 
  926 |     await logger.step('Step 2 â€" Send GET with completely unknown parameter', async () => {
  927 |       logger.info('GET /api/v1/dashboard/today-payments?foo=bar&baz=qux');
  928 |       resUnknown = await supertest(BASE_URL)
  929 |         .get(`${ENDPOINT}?foo=bar&baz=qux`)
  930 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  931 |         .set('Accept', 'application/json');
  932 |       logger.pass('HTTP ' + resUnknown.status + ' received');
  933 | 
  934 |       console.log(`[TC-DTPAY-010] ?foo=bar  -> ${resUnknown.status}`);
  935 |       allure.parameter('?foo=bar&baz=qux status', String(resUnknown.status));
  936 |     });
  937 | 
  938 |     await logger.step('Step 3 â€" BUG-TPAY-09: Unknown params should not silently return 200', async () => {
  939 |       // The ?date param is silently ignored â€" if date filtering is intended, this is a missing feature
  940 |       // If date filtering is NOT intended, unknown params should return 400 Bad Request
  941 |       if (resDate!.status === 200) {
  942 |         flagIssue('TC-DTPAY-010', 'BUG-TPAY-09a',
  943 |           'GET /today-payments?date=2026-06-01 returns 200 and silently ignores the date parameter â€" ' +
  944 |           'if date filtering is supported, data should reflect that date; if not, the server should return 400 for unknown params',
  945 |           { actual_status: 200, query_param: 'date=2026-06-01', body_unchanged: true },
  946 |         );
  947 |       }
  948 | 
  949 |       if (resUnknown!.status === 200) {
  950 |         flagIssue('TC-DTPAY-010', 'BUG-TPAY-09b',
  951 |           'GET /today-payments?foo=bar returns 200 â€" unknown query parameters should return 400 Bad Request, not silently succeed',
  952 |           { actual_status: 200, query_param: 'foo=bar&baz=qux' },
  953 |         );
  954 |       }
  955 | 
  956 |       logger.info('Asserting: unknown query parameters are rejected with 400, not silently ignored');
  957 |       expect.soft(resDate!.status,
  958 |         '[BUG-TPAY-09a] ?date= parameter is silently ignored â€" endpoint should filter by date or reject the param with 400',
  959 |       ).not.toBe(200);
  960 | 
  961 |       expect.soft(resUnknown!.status,
```