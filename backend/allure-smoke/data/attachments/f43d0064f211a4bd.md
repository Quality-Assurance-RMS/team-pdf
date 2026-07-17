# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-002 – BUG-ASUM-01 (BLOCKER): Complete auth bypass – endpoint is publicly accessible
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:220:7

# Error details

```
Error: [BUG-ASUM-01] "No Authorization header" must return 401 Unauthorized, got 200

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 200
```

```
Error: [BUG-ASUM-01] "Invalid Bearer token" must return 401 Unauthorized, got 200

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 200
```

```
Error: [BUG-ASUM-01] "Bearer empty string" must return 401 Unauthorized, got 200

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 200
```

```
Error: [BUG-ASUM-01] "Bearer null literal" must return 401 Unauthorized, got 200

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 200
```

```
Error: [BUG-ASUM-01] "Wrong scheme (Basic)" must return 401 Unauthorized, got 200

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 200
```

```
Error: [BUG-ASUM-01] "Clearly expired token" must return 401 Unauthorized, got 200

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 200
```

```
Error: [BUG-ASUM-01] 6 of 6 auth-bypass scenarios returned 200 – endpoint must require valid JWT

expect(received).toBe(expected) // Object.is equality

Expected: 0
Received: 6
```

# Test source

```ts
  183 |     });
  184 | 
  185 |     await logger.step('Step 3 – Validate HTTP 200 and Content-Type', async () => {
  186 |       logger.info('Asserting: HTTP 200, body defined, Content-Type application/json');
  187 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  188 |       expect(res!.body,   'Body must be defined').toBeDefined();
  189 |       expect(res!.headers['content-type'], 'Content-Type must be application/json').toContain('application/json');
  190 |       logger.pass('All assertions passed');
  191 |     });
  192 | 
  193 |     await logger.step('Step 4 – Validate top-level fields: mcda_id and today', async () => {
  194 |       logger.info('Asserting: mcda_id present, today present, types correct');
  195 |       expect(res!.body, 'Response must have "mcda_id" field').toHaveProperty('mcda_id');
  196 |       expect(res!.body, 'Response must have "today" field').toHaveProperty('today');
  197 |       expect(typeof res!.body.mcda_id, '"mcda_id" must be a string').toBe('string');
  198 |       expect(res!.body.today, '"today" must be an object').toBeInstanceOf(Object);
  199 |       allure.parameter('mcda_id', String(res!.body.mcda_id));
  200 |       logger.pass('All assertions passed');
  201 |     });
  202 | 
  203 |     await logger.step('Step 5 – Validate all 8 required fields in today object', async () => {
  204 |       const t = res!.body.today as TodayStats;
  205 |       const requiredFields = [
  206 |         'total_transactions', 'success_rate_pct', 'total_amount_kes',
  207 |         'successful_transactions', 'settlement_total_kes', 'failed_transactions',
  208 |         'recon_exceptions', 'recon_matched',
  209 |       ];
  210 |       logger.info('Asserting: all 8 required today.* fields present');
  211 |       for (const field of requiredFields) {
  212 |         expect(t, `today.${field} must be present`).toHaveProperty(field);
  213 |         allure.parameter(`today.${field}`, String((t as unknown as Record<string,unknown>)[field]));
  214 |       }
  215 |       logger.pass('All assertions passed');
  216 |     });
  217 |   });
  218 | 
  219 |   // ── TC-ASUM-002 ─────────────────────────────────────────────────────────────
  220 |   test('TC-ASUM-002 – BUG-ASUM-01 (BLOCKER): Complete auth bypass – endpoint is publicly accessible', async ({ logger }) => {
  221 |     smokeLabels('TC-ASUM-002', 'BUG-ASUM-01 – Complete Authentication Bypass', 'blocker');
  222 | 
  223 |     const authBypassScenarios: Array<{ label: string; req: () => supertest.Test }> = [
  224 |       { label: 'No Authorization header',         req: () => getNoAuth() },
  225 |       { label: 'Invalid Bearer token',             req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer invalidtoken.bad.sig').set('Accept', 'application/json') },
  226 |       { label: 'Bearer empty string',              req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer ').set('Accept', 'application/json') },
  227 |       { label: 'Bearer null literal',              req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer null').set('Accept', 'application/json') },
  228 |       { label: 'Wrong scheme (Basic)',             req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Basic dXNlcjpwYXNz').set('Accept', 'application/json') },
  229 |       { label: 'Clearly expired token',            req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MDAwMDAwMDB9.abc123').set('Accept', 'application/json') },
  230 |     ];
  231 | 
  232 |     let bypassCount = 0;
  233 | 
  234 |     for (const scenario of authBypassScenarios) {
  235 |       await logger.step(`Auth bypass: "${scenario.label}"`, async () => {
  236 |         logger.info(`GET /api/v1/analytics/dashboard/summary (${scenario.label})`);
  237 |         const res = await scenario.req();
  238 |         logger.pass('HTTP ' + res.status + ' received');
  239 | 
  240 |         console.log(`[TC-ASUM-002] "${scenario.label}" → ${res.status}`);
  241 |         allure.parameter(scenario.label, String(res.status));
  242 | 
  243 |         if (res.status === 200) {
  244 |           bypassCount++;
  245 |           flagIssue('TC-ASUM-002', 'BUG-ASUM-01',
  246 |             `CRITICAL SECURITY: "${scenario.label}" returns 200 OK with live financial analytics data. ` +
  247 |             'The /rms-analytics/api/v1/analytics/dashboard/summary endpoint has NO authentication guard. ' +
  248 |             'Any unauthenticated internet request can read transaction counts and financial summaries. ' +
  249 |             'All other RMS endpoints (dashboard-api) correctly return 401 for this scenario.',
  250 |             {
  251 |               scenario:       scenario.label,
  252 |               actual_status:  res.status,
  253 |               expected_status: 401,
  254 |               data_exposed:   res.body,
  255 |               impact:         'FINANCIAL DATA PUBLICLY ACCESSIBLE without credentials',
  256 |               other_endpoints: 'dashboard-api/api/v1/dashboard/* all correctly return 401',
  257 |             },
  258 |           );
  259 |         }
  260 | 
  261 |         logger.info('Asserting: status must be 401');
  262 |         expect.soft(res.status,
  263 |           `[BUG-ASUM-01] "${scenario.label}" must return 401 Unauthorized, got ${res.status}`,
  264 |         ).toBe(401);
  265 |       });
  266 |     }
  267 | 
  268 |     await logger.step('Confirm total bypass count', async () => {
  269 |       allure.parameter('Auth bypass scenarios tested', String(authBypassScenarios.length));
  270 |       allure.parameter('Bypass count (all should be 0)', String(bypassCount));
  271 | 
  272 |       if (bypassCount === authBypassScenarios.length) {
  273 |         flagIssue('TC-ASUM-002', 'BUG-ASUM-01-SUMMARY',
  274 |           `ALL ${bypassCount} authentication bypass attempts returned 200 with real data. ` +
  275 |           'The endpoint has no authentication middleware at all – it is completely unprotected.',
  276 |           { bypass_count: bypassCount, total_tested: authBypassScenarios.length, endpoint: ENDPOINT },
  277 |         );
  278 |       }
  279 | 
  280 |       logger.info('Asserting: bypass count must be 0');
  281 |       expect.soft(bypassCount,
  282 |         `[BUG-ASUM-01] ${bypassCount} of ${authBypassScenarios.length} auth-bypass scenarios returned 200 – endpoint must require valid JWT`,
> 283 |       ).toBe(0);
      |         ^ Error: [BUG-ASUM-01] 6 of 6 auth-bypass scenarios returned 200 – endpoint must require valid JWT
  284 |       logger.pass('All assertions passed');
  285 |     });
  286 |   });
  287 | 
  288 |   // ── TC-ASUM-003 ─────────────────────────────────────────────────────────────
  289 |   test('TC-ASUM-003 – BUG-ASUM-02: total_transactions must equal successful + failed + pending', async ({ logger }) => {
  290 |     smokeLabels('TC-ASUM-003', 'BUG-ASUM-02 – Transaction Count Discrepancy', 'critical');
  291 |     let res: supertest.Response;
  292 | 
  293 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  294 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  295 |       res = await get();
  296 |       logger.pass('HTTP ' + res.status + ' received');
  297 |       logger.info('Asserting: HTTP 200');
  298 |       expect(res.status).toBe(200);
  299 |       logger.pass('All assertions passed');
  300 |       logResponse('TC-ASUM-003', res, '(transaction count check)');
  301 |       attachResponse('TC-ASUM-003', res, { bug: 'BUG-ASUM-02' });
  302 |     });
  303 | 
  304 |     await logger.step('Step 2 – BUG-ASUM-02: successful + failed must equal total_transactions', async () => {
  305 |       const t = res!.body.today as TodayStats;
  306 | 
  307 |       const classified   = t.successful_transactions + t.failed_transactions;
  308 |       const unclassified = t.total_transactions - classified;
  309 | 
  310 |       allure.parameter('total_transactions',      String(t.total_transactions));
  311 |       allure.parameter('successful_transactions', String(t.successful_transactions));
  312 |       allure.parameter('failed_transactions',     String(t.failed_transactions));
  313 |       allure.parameter('successful + failed',     String(classified));
  314 |       allure.parameter('Unclassified (gap)',       String(unclassified));
  315 | 
  316 |       if (unclassified !== 0) {
  317 |         flagIssue('TC-ASUM-003', 'BUG-ASUM-02',
  318 |           `total_transactions=${t.total_transactions} but successful(${t.successful_transactions}) + failed(${t.failed_transactions}) = ${classified}. ` +
  319 |           `${unclassified} transactions are unaccounted for – they are likely PENDING/IN-PROGRESS transactions. ` +
  320 |           'The API is missing a "pending_transactions" field to classify these. ' +
  321 |           'As-is, the response cannot answer "where are the other 135 transactions?"',
  322 |           {
  323 |             total_transactions:      t.total_transactions,
  324 |             successful_transactions: t.successful_transactions,
  325 |             failed_transactions:     t.failed_transactions,
  326 |             unclassified:            unclassified,
  327 |             missing_field:           'pending_transactions (or equivalent)',
  328 |           },
  329 |         );
  330 |       }
  331 | 
  332 |       logger.info('Asserting: successful + failed equals total_transactions');
  333 |       expect.soft(classified,
  334 |         `[BUG-ASUM-02] successful(${t.successful_transactions}) + failed(${t.failed_transactions}) = ${classified} must equal total_transactions(${t.total_transactions})`,
  335 |       ).toBe(t.total_transactions);
  336 |       logger.pass('All assertions passed');
  337 |     });
  338 | 
  339 |     await logger.step('Step 3 – Verify pending_transactions field is absent (documents the gap)', async () => {
  340 |       const t = res!.body.today as TodayStats;
  341 |       const hasPending = 'pending_transactions' in t;
  342 |       allure.parameter('pending_transactions field present', String(hasPending));
  343 | 
  344 |       if (!hasPending && t.total_transactions > (t.successful_transactions + t.failed_transactions)) {
  345 |         flagIssue('TC-ASUM-003', 'BUG-ASUM-02b',
  346 |           '"today" object is missing a "pending_transactions" field. ' +
  347 |           'Without it, the gap between total and (success+failed) is invisible to consumers.',
  348 |           { fields_present: Object.keys(t), missing: 'pending_transactions' },
  349 |         );
  350 |       }
  351 |     });
  352 |   });
  353 | 
  354 |   // ── TC-ASUM-004 ─────────────────────────────────────────────────────────────
  355 |   test('TC-ASUM-004 – BUG-ASUM-03: total_amount_kes=0.0 while total_transactions=135 is suspicious', async ({ logger }) => {
  356 |     smokeLabels('TC-ASUM-004', 'BUG-ASUM-03 – Zero Amount With Non-Zero Transactions', 'critical');
  357 |     let res: supertest.Response;
  358 | 
  359 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  360 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  361 |       res = await get();
  362 |       logger.pass('HTTP ' + res.status + ' received');
  363 |       logger.info('Asserting: HTTP 200');
  364 |       expect(res.status).toBe(200);
  365 |       logger.pass('All assertions passed');
  366 |       attachResponse('TC-ASUM-004', res, { bug: 'BUG-ASUM-03' });
  367 |     });
  368 | 
  369 |     await logger.step('Step 2 – BUG-ASUM-03: total_amount_kes must not be 0.0 when transactions exist', async () => {
  370 |       const t = res!.body.today as TodayStats;
  371 | 
  372 |       allure.parameter('total_transactions', String(t.total_transactions));
  373 |       allure.parameter('total_amount_kes',   String(t.total_amount_kes));
  374 |       allure.parameter('settlement_total_kes', String(t.settlement_total_kes));
  375 | 
  376 |       const hasTransactions = t.total_transactions > 0;
  377 |       const hasZeroAmount   = t.total_amount_kes === 0;
  378 | 
  379 |       if (hasTransactions && hasZeroAmount) {
  380 |         flagIssue('TC-ASUM-004', 'BUG-ASUM-03',
  381 |           `total_amount_kes=0.0 while total_transactions=${t.total_transactions}. ` +
  382 |           'Every financial transaction must have an associated KES amount. ' +
  383 |           'Possible causes: (1) amounts not aggregated for PENDING transactions, ' +
```