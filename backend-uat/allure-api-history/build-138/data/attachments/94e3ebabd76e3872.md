# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-today-payments.spec.ts >> Backend (Konza) â€" Dashboard Today Payments >> TC-DTPAY-008 â€" BUG-TPAY-07/10: Cache-Control and security headers must be present
- Location: tests/API/backend-tests/backend-dashboard-today-payments.spec.ts:764:7

# Error details

```
Error: [BUG-TPAY-07] Cache-Control header must be present for real-time financial data

expect(received).toBeDefined()

Received: undefined
```

```
Error: [BUG-TPAY-10a] X-Content-Type-Options: nosniff must be present

expect(received).toBe(expected) // Object.is equality

Expected: "nosniff"
Received: undefined
```

```
Error: [BUG-TPAY-10b] X-Frame-Options: DENY must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
  734 | 
  735 |       allure.parameter('Whitelabel page exposed', String(hasWhitelabel));
  736 | 
  737 |       if (hasWhitelabel) {
  738 |         flagIssue('TC-DTPAY-007', 'BUG-TPAY-06',
  739 |           'GET /today-payments with Accept: text/html returns Spring Boot Whitelabel Error Page â€" exposes framework identity, server timezone, and internal error path',
  740 |           {
  741 |             status:       resHtml!.status,
  742 |             content_type: resHtml!.headers['content-type'],
  743 |             body_excerpt: htmlBody.substring(0, 400),
  744 |             info_leaked:  ['Spring Boot framework', 'Internal server path', 'Server timezone (EAT)'],
  745 |           },
  746 |         );
  747 |       }
  748 | 
  749 |       logger.info('Asserting: 406 error body does not expose Spring Boot Whitelabel page or framework details');
  750 |       expect.soft(hasWhitelabel, '[BUG-TPAY-06] 406 error must NOT expose Spring Boot Whitelabel Error Page').toBe(false);
  751 |       expect.soft(hasSpringBoot, '[BUG-TPAY-06] 406 error must NOT reveal Spring Boot framework').toBe(false);
  752 | 
  753 |       // 406 error SHOULD return a JSON error body, not HTML
  754 |       expect.soft(resHtml!.headers['content-type'],
  755 |         '[BUG-TPAY-06] 406 error body should be JSON, not HTML',
  756 |       ).toContain('application/json');
  757 |       logger.pass('All assertions passed');
  758 |     });
  759 | 
  760 |     allure.attachment('[TC-DTPAY-007] 406 Response Body (text/html)', resHtml!.text ?? '', 'text/plain');
  761 |   });
  762 | 
  763 |   // â"€â"€ TC-DTPAY-008 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  764 |   test('TC-DTPAY-008 â€" BUG-TPAY-07/10: Cache-Control and security headers must be present', async ({ logger }) => {
  765 |     backendLabels('TC-DTPAY-008', 'BUG-TPAY-07/10 â€" Missing Cache-Control & Security Headers', 'normal');
  766 | 
  767 |     let res: supertest.Response;
  768 | 
  769 |     await logger.step('Step 1 â€" Fetch response and inspect headers', async () => {
  770 |       logger.info('GET /api/v1/dashboard/today-payments');
  771 |       res = await get();
  772 |       logger.pass('HTTP ' + res.status + ' received');
  773 |       console.log(`[TC-DTPAY-008] Response headers:`);
  774 |       console.log(JSON.stringify(res.headers, null, 2));
  775 |       attachResponse('TC-DTPAY-008', res, { bug: 'BUG-TPAY-07/BUG-TPAY-10' });
  776 |     });
  777 | 
  778 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  779 |       logger.info('Asserting: HTTP 200');
  780 |       expect(res!.status).toBe(200);
  781 |       logger.pass('All assertions passed');
  782 |     });
  783 | 
  784 |     await logger.step('Step 3 â€" BUG-TPAY-07: Cache-Control must prohibit caching of real-time financial data', async () => {
  785 |       const cacheControl = res!.headers['cache-control'] as string | undefined;
  786 | 
  787 |       allure.parameter('Cache-Control header', cacheControl ?? 'MISSING');
  788 | 
  789 |       if (!cacheControl) {
  790 |         flagIssue('TC-DTPAY-008', 'BUG-TPAY-07',
  791 |           'Cache-Control header is missing â€" "today-payments" is real-time financial data that must never be cached by proxies or browsers',
  792 |           { expected: 'Cache-Control: no-store, no-cache', actual: 'MISSING' },
  793 |         );
  794 |       }
  795 | 
  796 |       logger.info('Asserting: Cache-Control header is present and prevents caching of real-time financial data');
  797 |       expect.soft(cacheControl, '[BUG-TPAY-07] Cache-Control header must be present for real-time financial data').toBeDefined();
  798 | 
  799 |       if (cacheControl) {
  800 |         const preventsCache = cacheControl.includes('no-store') || cacheControl.includes('no-cache') || cacheControl.includes('private');
  801 |         expect.soft(preventsCache, `[BUG-TPAY-07] Cache-Control="${cacheControl}" must include no-store or no-cache`).toBe(true);
  802 |       }
  803 |       logger.pass('All assertions passed');
  804 |     });
  805 | 
  806 |     await logger.step('Step 4 â€" BUG-TPAY-10: X-Content-Type-Options must be set to nosniff', async () => {
  807 |       const headerVal = res!.headers['x-content-type-options'] as string | undefined;
  808 |       allure.parameter('X-Content-Type-Options', headerVal ?? 'MISSING');
  809 | 
  810 |       if (!headerVal) {
  811 |         flagIssue('TC-DTPAY-008', 'BUG-TPAY-10a',
  812 |           'X-Content-Type-Options header is missing â€" must be "nosniff" to prevent MIME-type sniffing attacks',
  813 |           { expected: 'X-Content-Type-Options: nosniff', actual: 'MISSING' },
  814 |         );
  815 |       }
  816 | 
  817 |       logger.info('Asserting: X-Content-Type-Options is set to "nosniff"');
  818 |       expect.soft(headerVal, '[BUG-TPAY-10a] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  819 |       logger.pass('All assertions passed');
  820 |     });
  821 | 
  822 |     await logger.step('Step 5 â€" BUG-TPAY-10: X-Frame-Options must be set', async () => {
  823 |       const headerVal = res!.headers['x-frame-options'] as string | undefined;
  824 |       allure.parameter('X-Frame-Options', headerVal ?? 'MISSING');
  825 | 
  826 |       if (!headerVal) {
  827 |         flagIssue('TC-DTPAY-008', 'BUG-TPAY-10b',
  828 |           'X-Frame-Options header is missing â€" must be "DENY" to prevent clickjacking attacks',
  829 |           { expected: 'X-Frame-Options: DENY', actual: 'MISSING' },
  830 |         );
  831 |       }
  832 | 
  833 |       logger.info('Asserting: X-Frame-Options header is present');
> 834 |       expect.soft(headerVal, '[BUG-TPAY-10b] X-Frame-Options: DENY must be present').toBeDefined();
      |                                                                                      ^ Error: [BUG-TPAY-10b] X-Frame-Options: DENY must be present
  835 |       logger.pass('All assertions passed');
  836 |     });
  837 | 
  838 |     await logger.step('Step 6 â€" Log all security-relevant headers for audit', async () => {
  839 |       const securityHeaders = {
  840 |         'cache-control':           res!.headers['cache-control']           ?? 'MISSING',
  841 |         'x-content-type-options':  res!.headers['x-content-type-options']  ?? 'MISSING',
  842 |         'x-frame-options':         res!.headers['x-frame-options']         ?? 'MISSING',
  843 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  844 |         'content-security-policy': res!.headers['content-security-policy'] ?? 'MISSING',
  845 |       };
  846 | 
  847 |       allure.attachment('[TC-DTPAY-008] Security Headers Audit', JSON.stringify(securityHeaders, null, 2), 'application/json');
  848 |       console.log(`[TC-DTPAY-008] Security headers:`, JSON.stringify(securityHeaders, null, 2));
  849 |       logger.info('Asserting: all security-relevant headers are logged for audit');
  850 |       logger.pass('All assertions passed');
  851 |     });
  852 |   });
  853 | 
  854 |   // â"€â"€ TC-DTPAY-009 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  855 |   test('TC-DTPAY-009 â€" BUG-TPAY-08: CORS wildcard origin is insecure for financial API', async ({ logger }) => {
  856 |     backendLabels('TC-DTPAY-009', 'BUG-TPAY-08 â€" Overly Permissive CORS', 'normal');
  857 | 
  858 |     let res: supertest.Response;
  859 | 
  860 |     await logger.step('Step 1 â€" Fetch response and inspect CORS headers', async () => {
  861 |       logger.info('GET /api/v1/dashboard/today-payments (Origin: https://evil-attacker.example.com)');
  862 |       res = await get({ Origin: 'https://evil-attacker.example.com' });
  863 |       logger.pass('HTTP ' + res.status + ' received');
  864 |       attachResponse('TC-DTPAY-009', res, { bug: 'BUG-TPAY-08' });
  865 |     });
  866 | 
  867 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  868 |       logger.info('Asserting: HTTP 200');
  869 |       expect(res!.status).toBe(200);
  870 |       logger.pass('All assertions passed');
  871 |     });
  872 | 
  873 |     await logger.step('Step 3 â€" BUG-TPAY-08a: Access-Control-Allow-Origin must not be wildcard *', async () => {
  874 |       const acao = res!.headers['access-control-allow-origin'] as string | undefined;
  875 |       allure.parameter('Access-Control-Allow-Origin', acao ?? 'MISSING');
  876 | 
  877 |       if (acao === '*') {
  878 |         flagIssue('TC-DTPAY-009', 'BUG-TPAY-08a',
  879 |           'Access-Control-Allow-Origin: * allows any website to read financial dashboard data from authenticated users â€" must be restricted to known origins',
  880 |           { actual: 'Access-Control-Allow-Origin: *', expected: 'Specific trusted domains (e.g. https://rms.dev.demo-fsit.com)', risk: 'Cross-site data leakage' },
  881 |         );
  882 |       }
  883 | 
  884 |       logger.info('Asserting: Access-Control-Allow-Origin is not wildcard "*"');
  885 |       expect.soft(acao,
  886 |         '[BUG-TPAY-08a] Access-Control-Allow-Origin must not be wildcard "*" for a financial API',
  887 |       ).not.toBe('*');
  888 |       logger.pass('All assertions passed');
  889 |     });
  890 | 
  891 |     await logger.step('Step 4 â€" BUG-TPAY-08b: Destructive methods must not be CORS-allowed', async () => {
  892 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  893 |       allure.parameter('Access-Control-Allow-Methods', acam ?? 'MISSING');
  894 | 
  895 |       const allowsDelete = acam?.toUpperCase().includes('DELETE');
  896 |       const allowsPost   = acam?.toUpperCase().includes('POST');
  897 |       const allowsPut    = acam?.toUpperCase().includes('PUT');
  898 |       const allowsPatch  = acam?.toUpperCase().includes('PATCH');
  899 | 
  900 |       if (allowsDelete || allowsPost || allowsPut || allowsPatch) {
  901 |         flagIssue('TC-DTPAY-009', 'BUG-TPAY-08b',
  902 |           `CORS allows destructive methods (${[allowsPost && 'POST', allowsPut && 'PUT', allowsDelete && 'DELETE', allowsPatch && 'PATCH'].filter(Boolean).join(', ')}) from any origin â€" for a read-only dashboard endpoint, only GET, HEAD, OPTIONS are needed`,
  903 |           { 'access-control-allow-methods': acam, risk: 'Any site can trigger POST/PUT/DELETE on behalf of authenticated users' },
  904 |         );
  905 |       }
  906 | 
  907 |       logger.info('Asserting: CORS does not allow DELETE or POST on this read-only endpoint');
  908 |       // This is a GET-only endpoint â€" only GET, HEAD, OPTIONS should be CORS-allowed
  909 |       expect.soft(allowsDelete, '[BUG-TPAY-08b] CORS must not allow DELETE on a read-only endpoint').toBe(false);
  910 |       expect.soft(allowsPost,   '[BUG-TPAY-08b] CORS must not allow POST on a read-only endpoint').toBe(false);
  911 |       logger.pass('All assertions passed');
  912 |     });
  913 |   });
  914 | 
  915 |   // â"€â"€ TC-DTPAY-010 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  916 |   test('TC-DTPAY-010 â€" BUG-TPAY-09: Unknown query parameters must not be silently ignored', async ({ logger }) => {
  917 |     backendLabels('TC-DTPAY-010', 'BUG-TPAY-09 â€" Unknown Query Params Silently Ignored', 'minor');
  918 | 
  919 |     let resDate:    supertest.Response;
  920 |     let resUnknown: supertest.Response;
  921 | 
  922 |     await logger.step('Step 1 â€" Send GET with unknown date parameter', async () => {
  923 |       logger.info('GET /api/v1/dashboard/today-payments?date=2026-06-01');
  924 |       resDate = await supertest(BASE_URL)
  925 |         .get(`${ENDPOINT}?date=2026-06-01`)
  926 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  927 |         .set('Accept', 'application/json');
  928 |       logger.pass('HTTP ' + resDate.status + ' received');
  929 | 
  930 |       console.log(`[TC-DTPAY-010] ?date=2026-06-01  -> ${resDate.status}`);
  931 |       allure.parameter('?date=2026-06-01 status', String(resDate.status));
  932 |     });
  933 | 
  934 |     await logger.step('Step 2 â€" Send GET with completely unknown parameter', async () => {
```