# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-analytics-dashboard-summary.spec.ts >> Backend (Konza) – Analytics Dashboard Summary >> TC-ASUM-013 – Field types: all today.* fields must be non-negative numbers
- Location: tests/API/backend-tests/backend-analytics-dashboard-summary.spec.ts:872:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  781 | 
  782 |   // ── TC-ASUM-011 ─────────────────────────────────────────────────────────────
  783 |   test('TC-ASUM-011 – BUG-ASUM-10: Cache-Control and security headers must be present', async ({ logger }) => {
  784 |     backendLabels('TC-ASUM-011', 'BUG-ASUM-10 – Missing Security Headers', 'normal');
  785 |     let res: supertest.Response;
  786 | 
  787 |     await logger.step('Step 1 – Fetch response headers', async () => {
  788 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  789 |       res = await get();
  790 |       logger.pass('HTTP ' + res.status + ' received');
  791 |       console.log(`[TC-ASUM-011] Headers:\n${JSON.stringify(res.headers, null, 2)}`);
  792 |       attachResponse('TC-ASUM-011', res, { bug: 'BUG-ASUM-10' });
  793 |     });
  794 | 
  795 |     await logger.step('Step 2 – Cache-Control must prevent caching of real-time analytics', async () => {
  796 |       const cc = res!.headers['cache-control'] as string | undefined;
  797 |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  798 |       if (!cc) {
  799 |         flagIssue('TC-ASUM-011', 'BUG-ASUM-10a', 'Cache-Control missing on real-time analytics endpoint',
  800 |           { expected: 'no-store, no-cache', actual: 'MISSING' });
  801 |       }
  802 |       logger.info('Asserting: Cache-Control header is present');
  803 |       expect.soft(cc, '[BUG-ASUM-10a] Cache-Control must be present').toBeDefined();
  804 |       logger.pass('All assertions passed');
  805 |     });
  806 | 
  807 |     await logger.step('Step 3 – X-Content-Type-Options: nosniff must be present', async () => {
  808 |       const h = res!.headers['x-content-type-options'] as string | undefined;
  809 |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  810 |       if (!h) flagIssue('TC-ASUM-011', 'BUG-ASUM-10b', 'X-Content-Type-Options missing', { expected: 'nosniff', actual: 'MISSING' });
  811 |       logger.info('Asserting: X-Content-Type-Options: nosniff present');
  812 |       expect.soft(h, '[BUG-ASUM-10b] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  813 |       logger.pass('All assertions passed');
  814 |     });
  815 | 
  816 |     await logger.step('Step 4 – X-Frame-Options must be present', async () => {
  817 |       const h = res!.headers['x-frame-options'] as string | undefined;
  818 |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  819 |       if (!h) flagIssue('TC-ASUM-011', 'BUG-ASUM-10c', 'X-Frame-Options missing', { expected: 'DENY', actual: 'MISSING' });
  820 |       logger.info('Asserting: X-Frame-Options header present');
  821 |       expect.soft(h, '[BUG-ASUM-10c] X-Frame-Options: DENY must be present').toBeDefined();
  822 |       logger.pass('All assertions passed');
  823 |     });
  824 | 
  825 |     await logger.step('Step 5 – Full security header audit', async () => {
  826 |       const audit = {
  827 |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  828 |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  829 |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  830 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  831 |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  832 |         'vary':                      res!.headers['vary']                      ?? 'MISSING',
  833 |       };
  834 |       allure.attachment('[TC-ASUM-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  835 |       console.log('[TC-ASUM-011] Security audit:', JSON.stringify(audit, null, 2));
  836 |     });
  837 |   });
  838 | 
  839 |   // ── TC-ASUM-012 ─────────────────────────────────────────────────────────────
  840 |   test('TC-ASUM-012 – BUG-ASUM-11: CORS wildcard origin on analytics financial endpoint', async ({ logger }) => {
  841 |     backendLabels('TC-ASUM-012', 'BUG-ASUM-11 – Overly Permissive CORS', 'normal');
  842 |     let res: supertest.Response;
  843 | 
  844 |     await logger.step('Step 1 – Fetch response CORS headers', async () => {
  845 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  846 |       res = await get();
  847 |       logger.pass('HTTP ' + res.status + ' received');
  848 |       const acao = res.headers['access-control-allow-origin'] as string | undefined;
  849 |       allure.parameter('Access-Control-Allow-Origin', acao ?? 'MISSING');
  850 |       attachResponse('TC-ASUM-012', res, { bug: 'BUG-ASUM-11' });
  851 | 
  852 |       if (acao === '*') {
  853 |         flagIssue('TC-ASUM-012', 'BUG-ASUM-11',
  854 |           'Access-Control-Allow-Origin: * on an analytics endpoint that is ALSO unauthenticated (BUG-ASUM-01). ' +
  855 |           'Combined with the auth bypass, any webpage on the internet can read financial analytics data ' +
  856 |           'by making a simple GET request – no token, no CORS restriction, no protection at all.',
  857 |           {
  858 |             'access-control-allow-origin': acao,
  859 |             combined_with: 'BUG-ASUM-01 (auth bypass)',
  860 |             risk: 'CRITICAL – completely unprotected financial data API: no auth + no CORS restriction',
  861 |           },
  862 |         );
  863 |       }
  864 | 
  865 |       logger.info('Asserting: ACAO must not be wildcard *');
  866 |       expect.soft(acao, '[BUG-ASUM-11] ACAO must not be wildcard "*"').not.toBe('*');
  867 |       logger.pass('All assertions passed');
  868 |     });
  869 |   });
  870 | 
  871 |   // ── TC-ASUM-013 ─────────────────────────────────────────────────────────────
  872 |   test('TC-ASUM-013 – Field types: all today.* fields must be non-negative numbers', async ({ logger }) => {
  873 |     backendLabels('TC-ASUM-013', 'Field Type Validation', 'normal');
  874 |     let res: supertest.Response;
  875 | 
  876 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  877 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  878 |       res = await get();
  879 |       logger.pass('HTTP ' + res.status + ' received');
  880 |       logger.info('Asserting: HTTP 200');
> 881 |       expect(res.status).toBe(200);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
  882 |       logger.pass('All assertions passed');
  883 |       attachResponse('TC-ASUM-013', res);
  884 |     });
  885 | 
  886 |     await logger.step('Step 2 – All numeric fields must be numbers >= 0', async () => {
  887 |       const t = res!.body.today as TodayStats;
  888 |       const numericFields: (keyof TodayStats)[] = [
  889 |         'total_transactions', 'success_rate_pct', 'total_amount_kes',
  890 |         'successful_transactions', 'settlement_total_kes', 'failed_transactions',
  891 |         'recon_exceptions', 'recon_matched',
  892 |       ];
  893 | 
  894 |       logger.info('Asserting: all numeric today.* fields are numbers >= 0, non-NaN, finite');
  895 |       for (const field of numericFields) {
  896 |         const val = t[field];
  897 |         allure.parameter(field, `${val} (${typeof val})`);
  898 |         expect(typeof val, `today.${field} must be a number`).toBe('number');
  899 |         expect(val as number, `today.${field} must be >= 0`).toBeGreaterThanOrEqual(0);
  900 |         expect(Number.isNaN(val), `today.${field} must not be NaN`).toBe(false);
  901 |         expect(Number.isFinite(val as number), `today.${field} must be finite`).toBe(true);
  902 |       }
  903 |       logger.pass('All assertions passed');
  904 |     });
  905 | 
  906 |     await logger.step('Step 3 – mcda_id must be a non-empty string', async () => {
  907 |       const mcda = res!.body.mcda_id as string;
  908 |       logger.info('Asserting: mcda_id is a non-empty string');
  909 |       expect(typeof mcda, 'mcda_id must be a string').toBe('string');
  910 |       expect(mcda.length, 'mcda_id must not be empty').toBeGreaterThan(0);
  911 |       logger.pass('All assertions passed');
  912 |       allure.parameter('mcda_id value', mcda);
  913 |     });
  914 | 
  915 |     await logger.step('Step 4 – Count fields must be integers', async () => {
  916 |       const t = res!.body.today as TodayStats;
  917 |       const countFields: (keyof TodayStats)[] = [
  918 |         'total_transactions', 'successful_transactions', 'failed_transactions',
  919 |         'recon_exceptions', 'recon_matched',
  920 |       ];
  921 |       logger.info('Asserting: count fields are all integers');
  922 |       for (const field of countFields) {
  923 |         expect(Number.isInteger(t[field]),
  924 |           `today.${field} must be an integer (got ${t[field]})`,
  925 |         ).toBe(true);
  926 |       }
  927 |       logger.pass('All assertions passed');
  928 |     });
  929 |   });
  930 | 
  931 |   // ── TC-ASUM-014 ─────────────────────────────────────────────────────────────
  932 |   test('TC-ASUM-014 – HTTP method guard: POST/PUT/DELETE/PATCH → 405 + Allow: GET header', async ({ logger }) => {
  933 |     backendLabels('TC-ASUM-014', 'HTTP Method Guard (correctly returns 405)', 'normal');
  934 | 
  935 |     // NOTE: Unlike all dashboard-api endpoints that return 500, this analytics endpoint
  936 |     // correctly returns 405. This test documents that correct behavior.
  937 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  938 | 
  939 |     for (const method of wrongMethods) {
  940 |       await logger.step(`${method.toUpperCase()} must return 405 with Allow header`, async () => {
  941 |         logger.info(`${method.toUpperCase()} /api/v1/analytics/dashboard/summary`);
  942 |         const res = await getWithMethod(method);
  943 |         logger.pass('HTTP ' + res.status + ' received');
  944 | 
  945 |         console.log(`[TC-ASUM-014] ${method.toUpperCase()} → ${res.status} Allow: ${res.headers['allow'] ?? 'MISSING'}`);
  946 |         allure.parameter(`${method.toUpperCase()} status`, String(res.status));
  947 |         allure.parameter(`${method.toUpperCase()} Allow header`, String(res.headers['allow'] ?? 'MISSING'));
  948 |         allure.parameter('Note', 'This endpoint CORRECTLY returns 405 (unlike dashboard-api which returns 500)');
  949 | 
  950 |         logger.info(`Asserting: ${method.toUpperCase()} returns 405 with Allow header and correct body`);
  951 |         expect(res.status, `${method.toUpperCase()} must return 405 Method Not Allowed`).toBe(405);
  952 |         expect(res.headers['allow'], '405 must include Allow: GET header').toContain('GET');
  953 | 
  954 |         // Confirm 405 error body structure
  955 |         expect(res.body.status, '405 body.status must be 405').toBe(405);
  956 |         expect(res.body.error,  '405 body.error must indicate method not allowed').toContain('Method Not Allowed');
  957 |         logger.pass('All assertions passed');
  958 |       });
  959 |     }
  960 |   });
  961 | 
  962 |   // ── TC-ASUM-015 ─────────────────────────────────────────────────────────────
  963 |   test('TC-ASUM-015 – Response-Time SLA: must respond within 2 000 ms', async ({ logger }) => {
  964 |     backendLabels('TC-ASUM-015', 'Response-Time SLA', 'minor');
  965 | 
  966 |     const SLA_MS = 2000;
  967 |     let durationMs = 0;
  968 |     let res: supertest.Response;
  969 | 
  970 |     await logger.step('Step 1 – Measure round-trip time', async () => {
  971 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  972 |       const t0 = Date.now();
  973 |       res = await get();
  974 |       durationMs = Date.now() - t0;
  975 |       logger.pass('HTTP ' + res.status + ' received');
  976 |       console.log(`[TC-ASUM-015] Response time: ${durationMs}ms  (SLA: ${SLA_MS}ms)`);
  977 |       allure.parameter('Response Time (ms)', String(durationMs));
  978 |       allure.parameter('SLA Threshold (ms)', String(SLA_MS));
  979 |       allure.parameter('SLA Pass',           String(durationMs < SLA_MS));
  980 |     });
  981 | 
```