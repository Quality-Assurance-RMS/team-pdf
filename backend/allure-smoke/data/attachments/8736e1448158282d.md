# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-015 – Response-Time SLA: must respond within 2 000 ms
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:955:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  876 |     });
  877 | 
  878 |     await logger.step('Step 2 – All numeric fields must be numbers >= 0', async () => {
  879 |       const t = res!.body.today as TodayStats;
  880 |       const numericFields: (keyof TodayStats)[] = [
  881 |         'total_transactions', 'success_rate_pct', 'total_amount_kes',
  882 |         'successful_transactions', 'settlement_total_kes', 'failed_transactions',
  883 |         'recon_exceptions', 'recon_matched',
  884 |       ];
  885 | 
  886 |       logger.info('Asserting: all numeric today.* fields are numbers >= 0, non-NaN, finite');
  887 |       for (const field of numericFields) {
  888 |         const val = t[field];
  889 |         allure.parameter(field, `${val} (${typeof val})`);
  890 |         expect(typeof val, `today.${field} must be a number`).toBe('number');
  891 |         expect(val as number, `today.${field} must be >= 0`).toBeGreaterThanOrEqual(0);
  892 |         expect(Number.isNaN(val), `today.${field} must not be NaN`).toBe(false);
  893 |         expect(Number.isFinite(val as number), `today.${field} must be finite`).toBe(true);
  894 |       }
  895 |       logger.pass('All assertions passed');
  896 |     });
  897 | 
  898 |     await logger.step('Step 3 – mcda_id must be a non-empty string', async () => {
  899 |       const mcda = res!.body.mcda_id as string;
  900 |       logger.info('Asserting: mcda_id is a non-empty string');
  901 |       expect(typeof mcda, 'mcda_id must be a string').toBe('string');
  902 |       expect(mcda.length, 'mcda_id must not be empty').toBeGreaterThan(0);
  903 |       logger.pass('All assertions passed');
  904 |       allure.parameter('mcda_id value', mcda);
  905 |     });
  906 | 
  907 |     await logger.step('Step 4 – Count fields must be integers', async () => {
  908 |       const t = res!.body.today as TodayStats;
  909 |       const countFields: (keyof TodayStats)[] = [
  910 |         'total_transactions', 'successful_transactions', 'failed_transactions',
  911 |         'recon_exceptions', 'recon_matched',
  912 |       ];
  913 |       logger.info('Asserting: count fields are all integers');
  914 |       for (const field of countFields) {
  915 |         expect(Number.isInteger(t[field]),
  916 |           `today.${field} must be an integer (got ${t[field]})`,
  917 |         ).toBe(true);
  918 |       }
  919 |       logger.pass('All assertions passed');
  920 |     });
  921 |   });
  922 | 
  923 |   // ── TC-ASUM-014 ─────────────────────────────────────────────────────────────
  924 |   test('TC-ASUM-014 – HTTP method guard: POST/PUT/DELETE/PATCH → 405 + Allow: GET header', async ({ logger }) => {
  925 |     smokeLabels('TC-ASUM-014', 'HTTP Method Guard (correctly returns 405)', 'normal');
  926 | 
  927 |     // NOTE: Unlike all dashboard-api endpoints that return 500, this analytics endpoint
  928 |     // correctly returns 405. This test documents that correct behavior.
  929 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  930 | 
  931 |     for (const method of wrongMethods) {
  932 |       await logger.step(`${method.toUpperCase()} must return 405 with Allow header`, async () => {
  933 |         logger.info(`${method.toUpperCase()} /api/v1/analytics/dashboard/summary`);
  934 |         const res = await getWithMethod(method);
  935 |         logger.pass('HTTP ' + res.status + ' received');
  936 | 
  937 |         console.log(`[TC-ASUM-014] ${method.toUpperCase()} → ${res.status} Allow: ${res.headers['allow'] ?? 'MISSING'}`);
  938 |         allure.parameter(`${method.toUpperCase()} status`, String(res.status));
  939 |         allure.parameter(`${method.toUpperCase()} Allow header`, String(res.headers['allow'] ?? 'MISSING'));
  940 |         allure.parameter('Note', 'This endpoint CORRECTLY returns 405 (unlike dashboard-api which returns 500)');
  941 | 
  942 |         logger.info(`Asserting: ${method.toUpperCase()} returns 405 with Allow header and correct body`);
  943 |         expect(res.status, `${method.toUpperCase()} must return 405 Method Not Allowed`).toBe(405);
  944 |         expect(res.headers['allow'], '405 must include Allow: GET header').toContain('GET');
  945 | 
  946 |         // Confirm 405 error body structure
  947 |         expect(res.body.status, '405 body.status must be 405').toBe(405);
  948 |         expect(res.body.error,  '405 body.error must indicate method not allowed').toContain('Method Not Allowed');
  949 |         logger.pass('All assertions passed');
  950 |       });
  951 |     }
  952 |   });
  953 | 
  954 |   // ── TC-ASUM-015 ─────────────────────────────────────────────────────────────
  955 |   test('TC-ASUM-015 – Response-Time SLA: must respond within 2 000 ms', async ({ logger }) => {
  956 |     smokeLabels('TC-ASUM-015', 'Response-Time SLA', 'minor');
  957 | 
  958 |     const SLA_MS = 2000;
  959 |     let durationMs = 0;
  960 |     let res: supertest.Response;
  961 | 
  962 |     await logger.step('Step 1 – Measure round-trip time', async () => {
  963 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  964 |       const t0 = Date.now();
  965 |       res = await get();
  966 |       durationMs = Date.now() - t0;
  967 |       logger.pass('HTTP ' + res.status + ' received');
  968 |       console.log(`[TC-ASUM-015] Response time: ${durationMs}ms  (SLA: ${SLA_MS}ms)`);
  969 |       allure.parameter('Response Time (ms)', String(durationMs));
  970 |       allure.parameter('SLA Threshold (ms)', String(SLA_MS));
  971 |       allure.parameter('SLA Pass',           String(durationMs < SLA_MS));
  972 |     });
  973 | 
  974 |     await logger.step('Step 2 – HTTP 200', async () => {
  975 |       logger.info('Asserting: HTTP 200');
> 976 |       expect(res!.status).toBe(200);
      |                           ^ Error: expect(received).toBe(expected) // Object.is equality
  977 |       logger.pass('All assertions passed');
  978 |     });
  979 | 
  980 |     await logger.step(`Step 3 – Response time < ${SLA_MS}ms`, async () => {
  981 |       if (durationMs >= SLA_MS) {
  982 |         flagIssue('TC-ASUM-015', 'PERF-ASUM-01',
  983 |           `Response time ${durationMs}ms exceeds ${SLA_MS}ms SLA`,
  984 |           { actual_ms: durationMs, sla_ms: SLA_MS, endpoint: `GET ${ENDPOINT}` },
  985 |         );
  986 |       }
  987 |       logger.info(`Asserting: response time ${durationMs}ms < ${SLA_MS}ms SLA`);
  988 |       expect(durationMs, `Response time ${durationMs}ms must be < ${SLA_MS}ms`).toBeLessThan(SLA_MS);
  989 |       logger.pass('All assertions passed');
  990 |     });
  991 |   });
  992 | 
  993 | });
  994 | 
```