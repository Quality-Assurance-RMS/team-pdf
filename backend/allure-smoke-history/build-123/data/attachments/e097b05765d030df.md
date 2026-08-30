# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-004 – BUG-ASUM-03: total_amount_kes=0.0 while total_transactions=135 is suspicious
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:355:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
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
  283 |       ).toBe(0);
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
> 364 |       expect(res.status).toBe(200);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
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
  384 |           '(2) amount calculation bug, or (3) transactions recorded without amounts. ' +
  385 |           'Cross-reference: /dashboard-api/today-payments also shows 0.0 KES today – ' +
  386 |           'this may be legitimate if all 135 are pending (no settled amounts yet).',
  387 |           {
  388 |             total_transactions:   t.total_transactions,
  389 |             total_amount_kes:     t.total_amount_kes,
  390 |             settlement_total_kes: t.settlement_total_kes,
  391 |             note: 'If transactions are all PENDING, 0.0 KES may be expected – but must be clarified',
  392 |           },
  393 |         );
  394 |       }
  395 | 
  396 |       // When transactions exist AND amounts are 0, at minimum flag for investigation
  397 |       if (hasTransactions) {
  398 |         logger.info('Asserting: total_amount_kes must not be 0.0 when transactions exist');
  399 |         expect.soft(hasZeroAmount,
  400 |           `[BUG-ASUM-03] total_amount_kes=0.0 with ${t.total_transactions} transactions – amounts must be populated or transactions must be pending with a stated reason`,
  401 |         ).toBe(false);
  402 |         logger.pass('All assertions passed');
  403 |       }
  404 |     });
  405 | 
  406 |     await logger.step('Step 3 – settlement_total_kes must be <= total_amount_kes', async () => {
  407 |       const t = res!.body.today as TodayStats;
  408 |       // Settlement can only be for successful transactions, so settlement <= total
  409 |       if (t.total_amount_kes > 0) {
  410 |         logger.info('Asserting: settlement_total_kes <= total_amount_kes');
  411 |         expect(t.settlement_total_kes, 'settlement_total_kes must be <= total_amount_kes').toBeLessThanOrEqual(t.total_amount_kes);
  412 |         logger.pass('All assertions passed');
  413 |       }
  414 |       allure.parameter('total_amount_kes vs settlement_total_kes',
  415 |         `total=${t.total_amount_kes} >= settlement=${t.settlement_total_kes}: ${t.total_amount_kes >= t.settlement_total_kes}`);
  416 |     });
  417 |   });
  418 | 
  419 |   // ── TC-ASUM-005 ─────────────────────────────────────────────────────────────
  420 |   test('TC-ASUM-005 – BUG-ASUM-04: recon_matched + recon_exceptions must cover some transactions', async ({ logger }) => {
  421 |     smokeLabels('TC-ASUM-005', 'BUG-ASUM-04 – All Transactions Outside Reconciliation', 'critical');
  422 |     let res: supertest.Response;
  423 | 
  424 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  425 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  426 |       res = await get();
  427 |       logger.pass('HTTP ' + res.status + ' received');
  428 |       logger.info('Asserting: HTTP 200');
  429 |       expect(res.status).toBe(200);
  430 |       logger.pass('All assertions passed');
  431 |       attachResponse('TC-ASUM-005', res, { bug: 'BUG-ASUM-04' });
  432 |     });
  433 | 
  434 |     await logger.step('Step 2 – BUG-ASUM-04: recon coverage must not be 0 when transactions exist', async () => {
  435 |       const t = res!.body.today as TodayStats;
  436 | 
  437 |       const reconCoverage   = t.recon_matched + t.recon_exceptions;
  438 |       const reconUncovered  = t.total_transactions - reconCoverage;
  439 | 
  440 |       allure.parameter('recon_matched',            String(t.recon_matched));
  441 |       allure.parameter('recon_exceptions',         String(t.recon_exceptions));
  442 |       allure.parameter('recon total covered',      String(reconCoverage));
  443 |       allure.parameter('total_transactions',       String(t.total_transactions));
  444 |       allure.parameter('recon_uncovered (gap)',     String(reconUncovered));
  445 | 
  446 |       if (t.total_transactions > 0 && reconCoverage === 0) {
  447 |         flagIssue('TC-ASUM-005', 'BUG-ASUM-04',
  448 |           `recon_matched(${t.recon_matched}) + recon_exceptions(${t.recon_exceptions}) = 0, ` +
  449 |           `yet total_transactions = ${t.total_transactions}. ` +
  450 |           'All 135 transactions are outside the reconciliation system. ' +
  451 |           'Possible causes: (1) recon not yet run for today, (2) recon not available for PENDING transactions, ' +
  452 |           '(3) recon data is not being aggregated correctly.',
  453 |           {
  454 |             total_transactions: t.total_transactions,
  455 |             recon_matched:      t.recon_matched,
  456 |             recon_exceptions:   t.recon_exceptions,
  457 |             recon_coverage:     reconCoverage,
  458 |             uncovered:          reconUncovered,
  459 |           },
  460 |         );
  461 |       }
  462 | 
  463 |       // When transactions exist, recon_matched + recon_exceptions should cover at least some
  464 |       if (t.total_transactions > 0) {
```