# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-analytics-dashboard-summary.spec.ts >> Backend (Konza) – Analytics Dashboard Summary >> TC-ASUM-004 – BUG-ASUM-03: total_amount_kes=0.0 while total_transactions=135 is suspicious
- Location: tests/API/backend-tests/backend-analytics-dashboard-summary.spec.ts:363:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  272 |         ).toBe(401);
  273 |       });
  274 |     }
  275 | 
  276 |     await logger.step('Confirm total bypass count', async () => {
  277 |       allure.parameter('Auth bypass scenarios tested', String(authBypassScenarios.length));
  278 |       allure.parameter('Bypass count (all should be 0)', String(bypassCount));
  279 | 
  280 |       if (bypassCount === authBypassScenarios.length) {
  281 |         flagIssue('TC-ASUM-002', 'BUG-ASUM-01-SUMMARY',
  282 |           `ALL ${bypassCount} authentication bypass attempts returned 200 with real data. ` +
  283 |           'The endpoint has no authentication middleware at all – it is completely unprotected.',
  284 |           { bypass_count: bypassCount, total_tested: authBypassScenarios.length, endpoint: ENDPOINT },
  285 |         );
  286 |       }
  287 | 
  288 |       logger.info('Asserting: bypass count must be 0');
  289 |       expect.soft(bypassCount,
  290 |         `[BUG-ASUM-01] ${bypassCount} of ${authBypassScenarios.length} auth-bypass scenarios returned 200 – endpoint must require valid JWT`,
  291 |       ).toBe(0);
  292 |       logger.pass('All assertions passed');
  293 |     });
  294 |   });
  295 | 
  296 |   // ── TC-ASUM-003 ─────────────────────────────────────────────────────────────
  297 |   test('TC-ASUM-003 – BUG-ASUM-02: total_transactions must equal successful + failed + pending', async ({ logger }) => {
  298 |     backendLabels('TC-ASUM-003', 'BUG-ASUM-02 – Transaction Count Discrepancy', 'critical');
  299 |     let res: supertest.Response;
  300 | 
  301 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  302 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  303 |       res = await get();
  304 |       logger.pass('HTTP ' + res.status + ' received');
  305 |       logger.info('Asserting: HTTP 200');
  306 |       expect(res.status).toBe(200);
  307 |       logger.pass('All assertions passed');
  308 |       logResponse('TC-ASUM-003', res, '(transaction count check)');
  309 |       attachResponse('TC-ASUM-003', res, { bug: 'BUG-ASUM-02' });
  310 |     });
  311 | 
  312 |     await logger.step('Step 2 – BUG-ASUM-02: successful + failed must equal total_transactions', async () => {
  313 |       const t = res!.body.today as TodayStats;
  314 | 
  315 |       const classified   = t.successful_transactions + t.failed_transactions;
  316 |       const unclassified = t.total_transactions - classified;
  317 | 
  318 |       allure.parameter('total_transactions',      String(t.total_transactions));
  319 |       allure.parameter('successful_transactions', String(t.successful_transactions));
  320 |       allure.parameter('failed_transactions',     String(t.failed_transactions));
  321 |       allure.parameter('successful + failed',     String(classified));
  322 |       allure.parameter('Unclassified (gap)',       String(unclassified));
  323 | 
  324 |       if (unclassified !== 0) {
  325 |         flagIssue('TC-ASUM-003', 'BUG-ASUM-02',
  326 |           `total_transactions=${t.total_transactions} but successful(${t.successful_transactions}) + failed(${t.failed_transactions}) = ${classified}. ` +
  327 |           `${unclassified} transactions are unaccounted for – they are likely PENDING/IN-PROGRESS transactions. ` +
  328 |           'The API is missing a "pending_transactions" field to classify these. ' +
  329 |           'As-is, the response cannot answer "where are the other 135 transactions?"',
  330 |           {
  331 |             total_transactions:      t.total_transactions,
  332 |             successful_transactions: t.successful_transactions,
  333 |             failed_transactions:     t.failed_transactions,
  334 |             unclassified:            unclassified,
  335 |             missing_field:           'pending_transactions (or equivalent)',
  336 |           },
  337 |         );
  338 |       }
  339 | 
  340 |       logger.info('Asserting: successful + failed equals total_transactions');
  341 |       expect.soft(classified,
  342 |         `[BUG-ASUM-02] successful(${t.successful_transactions}) + failed(${t.failed_transactions}) = ${classified} must equal total_transactions(${t.total_transactions})`,
  343 |       ).toBe(t.total_transactions);
  344 |       logger.pass('All assertions passed');
  345 |     });
  346 | 
  347 |     await logger.step('Step 3 – Verify pending_transactions field is absent (documents the gap)', async () => {
  348 |       const t = res!.body.today as TodayStats;
  349 |       const hasPending = 'pending_transactions' in t;
  350 |       allure.parameter('pending_transactions field present', String(hasPending));
  351 | 
  352 |       if (!hasPending && t.total_transactions > (t.successful_transactions + t.failed_transactions)) {
  353 |         flagIssue('TC-ASUM-003', 'BUG-ASUM-02b',
  354 |           '"today" object is missing a "pending_transactions" field. ' +
  355 |           'Without it, the gap between total and (success+failed) is invisible to consumers.',
  356 |           { fields_present: Object.keys(t), missing: 'pending_transactions' },
  357 |         );
  358 |       }
  359 |     });
  360 |   });
  361 | 
  362 |   // ── TC-ASUM-004 ─────────────────────────────────────────────────────────────
  363 |   test('TC-ASUM-004 – BUG-ASUM-03: total_amount_kes=0.0 while total_transactions=135 is suspicious', async ({ logger }) => {
  364 |     backendLabels('TC-ASUM-004', 'BUG-ASUM-03 – Zero Amount With Non-Zero Transactions', 'critical');
  365 |     let res: supertest.Response;
  366 | 
  367 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  368 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  369 |       res = await get();
  370 |       logger.pass('HTTP ' + res.status + ' received');
  371 |       logger.info('Asserting: HTTP 200');
> 372 |       expect(res.status).toBe(200);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
  373 |       logger.pass('All assertions passed');
  374 |       attachResponse('TC-ASUM-004', res, { bug: 'BUG-ASUM-03' });
  375 |     });
  376 | 
  377 |     await logger.step('Step 2 – BUG-ASUM-03: total_amount_kes must not be 0.0 when transactions exist', async () => {
  378 |       const t = res!.body.today as TodayStats;
  379 | 
  380 |       allure.parameter('total_transactions', String(t.total_transactions));
  381 |       allure.parameter('total_amount_kes',   String(t.total_amount_kes));
  382 |       allure.parameter('settlement_total_kes', String(t.settlement_total_kes));
  383 | 
  384 |       const hasTransactions = t.total_transactions > 0;
  385 |       const hasZeroAmount   = t.total_amount_kes === 0;
  386 | 
  387 |       if (hasTransactions && hasZeroAmount) {
  388 |         flagIssue('TC-ASUM-004', 'BUG-ASUM-03',
  389 |           `total_amount_kes=0.0 while total_transactions=${t.total_transactions}. ` +
  390 |           'Every financial transaction must have an associated KES amount. ' +
  391 |           'Possible causes: (1) amounts not aggregated for PENDING transactions, ' +
  392 |           '(2) amount calculation bug, or (3) transactions recorded without amounts. ' +
  393 |           'Cross-reference: /dashboard-api/today-payments also shows 0.0 KES today – ' +
  394 |           'this may be legitimate if all 135 are pending (no settled amounts yet).',
  395 |           {
  396 |             total_transactions:   t.total_transactions,
  397 |             total_amount_kes:     t.total_amount_kes,
  398 |             settlement_total_kes: t.settlement_total_kes,
  399 |             note: 'If transactions are all PENDING, 0.0 KES may be expected – but must be clarified',
  400 |           },
  401 |         );
  402 |       }
  403 | 
  404 |       // When transactions exist AND amounts are 0, at minimum flag for investigation
  405 |       if (hasTransactions) {
  406 |         logger.info('Asserting: total_amount_kes must not be 0.0 when transactions exist');
  407 |         expect.soft(hasZeroAmount,
  408 |           `[BUG-ASUM-03] total_amount_kes=0.0 with ${t.total_transactions} transactions – amounts must be populated or transactions must be pending with a stated reason`,
  409 |         ).toBe(false);
  410 |         logger.pass('All assertions passed');
  411 |       }
  412 |     });
  413 | 
  414 |     await logger.step('Step 3 – settlement_total_kes must be <= total_amount_kes', async () => {
  415 |       const t = res!.body.today as TodayStats;
  416 |       // Settlement can only be for successful transactions, so settlement <= total
  417 |       if (t.total_amount_kes > 0) {
  418 |         logger.info('Asserting: settlement_total_kes <= total_amount_kes');
  419 |         expect(t.settlement_total_kes, 'settlement_total_kes must be <= total_amount_kes').toBeLessThanOrEqual(t.total_amount_kes);
  420 |         logger.pass('All assertions passed');
  421 |       }
  422 |       allure.parameter('total_amount_kes vs settlement_total_kes',
  423 |         `total=${t.total_amount_kes} >= settlement=${t.settlement_total_kes}: ${t.total_amount_kes >= t.settlement_total_kes}`);
  424 |     });
  425 |   });
  426 | 
  427 |   // ── TC-ASUM-005 ─────────────────────────────────────────────────────────────
  428 |   test('TC-ASUM-005 – BUG-ASUM-04: recon_matched + recon_exceptions must cover some transactions', async ({ logger }) => {
  429 |     backendLabels('TC-ASUM-005', 'BUG-ASUM-04 – All Transactions Outside Reconciliation', 'critical');
  430 |     let res: supertest.Response;
  431 | 
  432 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  433 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  434 |       res = await get();
  435 |       logger.pass('HTTP ' + res.status + ' received');
  436 |       logger.info('Asserting: HTTP 200');
  437 |       expect(res.status).toBe(200);
  438 |       logger.pass('All assertions passed');
  439 |       attachResponse('TC-ASUM-005', res, { bug: 'BUG-ASUM-04' });
  440 |     });
  441 | 
  442 |     await logger.step('Step 2 – BUG-ASUM-04: recon coverage must not be 0 when transactions exist', async () => {
  443 |       const t = res!.body.today as TodayStats;
  444 | 
  445 |       const reconCoverage   = t.recon_matched + t.recon_exceptions;
  446 |       const reconUncovered  = t.total_transactions - reconCoverage;
  447 | 
  448 |       allure.parameter('recon_matched',            String(t.recon_matched));
  449 |       allure.parameter('recon_exceptions',         String(t.recon_exceptions));
  450 |       allure.parameter('recon total covered',      String(reconCoverage));
  451 |       allure.parameter('total_transactions',       String(t.total_transactions));
  452 |       allure.parameter('recon_uncovered (gap)',     String(reconUncovered));
  453 | 
  454 |       if (t.total_transactions > 0 && reconCoverage === 0) {
  455 |         flagIssue('TC-ASUM-005', 'BUG-ASUM-04',
  456 |           `recon_matched(${t.recon_matched}) + recon_exceptions(${t.recon_exceptions}) = 0, ` +
  457 |           `yet total_transactions = ${t.total_transactions}. ` +
  458 |           'All 135 transactions are outside the reconciliation system. ' +
  459 |           'Possible causes: (1) recon not yet run for today, (2) recon not available for PENDING transactions, ' +
  460 |           '(3) recon data is not being aggregated correctly.',
  461 |           {
  462 |             total_transactions: t.total_transactions,
  463 |             recon_matched:      t.recon_matched,
  464 |             recon_exceptions:   t.recon_exceptions,
  465 |             recon_coverage:     reconCoverage,
  466 |             uncovered:          reconUncovered,
  467 |           },
  468 |         );
  469 |       }
  470 | 
  471 |       // When transactions exist, recon_matched + recon_exceptions should cover at least some
  472 |       if (t.total_transactions > 0) {
```