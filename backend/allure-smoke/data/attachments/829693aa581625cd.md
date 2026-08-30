# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-006 – BUG-ASUM-05: ?mcda_id= query param is completely ignored
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:494:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
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
  465 |         logger.info('Asserting: recon_matched + recon_exceptions > 0 when transactions exist');
  466 |         expect.soft(reconCoverage,
  467 |           `[BUG-ASUM-04] recon_matched + recon_exceptions = 0 for ${t.total_transactions} transactions – reconciliation data not populated`,
  468 |         ).toBeGreaterThan(0);
  469 |         logger.pass('All assertions passed');
  470 |       }
  471 |     });
  472 | 
  473 |     await logger.step('Step 3 – success_rate_pct must be calculable from reported counts', async () => {
  474 |       const t = res!.body.today as TodayStats;
  475 |       allure.parameter('success_rate_pct', String(t.success_rate_pct));
  476 |       logger.info('Asserting: success_rate_pct is a number between 0 and 100');
  477 |       expect(typeof t.success_rate_pct, 'success_rate_pct must be a number').toBe('number');
  478 |       expect(t.success_rate_pct, 'success_rate_pct must be >= 0').toBeGreaterThanOrEqual(0);
  479 |       expect(t.success_rate_pct, 'success_rate_pct must be <= 100').toBeLessThanOrEqual(100);
  480 | 
  481 |       // If successful_transactions > 0, rate must be > 0
  482 |       if (t.successful_transactions > 0 && t.total_transactions > 0) {
  483 |         const expectedRate = (t.successful_transactions / t.total_transactions) * 100;
  484 |         const tolerance    = 0.01;
  485 |         expect(Math.abs(t.success_rate_pct - expectedRate),
  486 |           `success_rate_pct(${t.success_rate_pct}) must equal successful/total*100 (${expectedRate.toFixed(2)})`,
  487 |         ).toBeLessThan(tolerance);
  488 |       }
  489 |       logger.pass('All assertions passed');
  490 |     });
  491 |   });
  492 | 
  493 |   // ── TC-ASUM-006 ─────────────────────────────────────────────────────────────
  494 |   test('TC-ASUM-006 – BUG-ASUM-05: ?mcda_id= query param is completely ignored', async ({ logger }) => {
  495 |     smokeLabels('TC-ASUM-006', 'BUG-ASUM-05 – mcda_id Filter Silently Ignored', 'critical');
  496 | 
  497 |     let resBase:     supertest.Response;
  498 |     let resPlatform: supertest.Response;
  499 |     let resInvalid:  supertest.Response;
  500 | 
  501 |     await logger.step('Step 1 – Baseline (no mcda_id param)', async () => {
  502 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  503 |       resBase = await get();
  504 |       logger.pass('HTTP ' + resBase.status + ' received');
  505 |       logger.info('Asserting: HTTP 200');
> 506 |       expect(resBase.status).toBe(200);
      |                              ^ Error: expect(received).toBe(expected) // Object.is equality
  507 |       logger.pass('All assertions passed');
  508 |       allure.parameter('baseline mcda_id in response', String(resBase.body.mcda_id));
  509 |     });
  510 | 
  511 |     await logger.step('Step 2 – ?mcda_id=PLATFORM', async () => {
  512 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=PLATFORM');
  513 |       resPlatform = await get('mcda_id=PLATFORM');
  514 |       logger.pass('HTTP ' + resPlatform.status + ' received');
  515 |       allure.parameter('?mcda_id=PLATFORM status', String(resPlatform.status));
  516 |       allure.parameter('?mcda_id=PLATFORM response mcda_id', String(resPlatform.body?.mcda_id));
  517 |     });
  518 | 
  519 |     await logger.step('Step 3 – ?mcda_id=invalid_value', async () => {
  520 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=invalid_value');
  521 |       resInvalid = await get('mcda_id=invalid_value');
  522 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  523 |       allure.parameter('?mcda_id=invalid status', String(resInvalid.status));
  524 |       allure.parameter('?mcda_id=invalid response mcda_id', String(resInvalid.body?.mcda_id));
  525 |     });
  526 | 
  527 |     await logger.step('Step 4 – BUG-ASUM-05a: mcda_id param must filter data', async () => {
  528 |       const baseData     = JSON.stringify(resBase!.body.today);
  529 |       const platformData = JSON.stringify(resPlatform!.body.today);
  530 |       const identical    = baseData === platformData;
  531 | 
  532 |       if (identical && resPlatform!.body.mcda_id === 'ALL') {
  533 |         flagIssue('TC-ASUM-006', 'BUG-ASUM-05a',
  534 |           '?mcda_id=PLATFORM returns identical data and mcda_id="ALL" – the filter is completely ignored. ' +
  535 |           'The response should show data scoped to the PLATFORM mcda, not ALL mcda combined.',
  536 |           {
  537 |             param: 'mcda_id=PLATFORM',
  538 |             response_mcda_id: resPlatform!.body.mcda_id,
  539 |             expected_mcda_id: 'PLATFORM',
  540 |             data_identical_to_ALL: identical,
  541 |           },
  542 |         );
  543 |       }
  544 | 
  545 |       logger.info('Asserting: response mcda_id echoes PLATFORM and data differs from baseline');
  546 |       expect.soft(resPlatform!.body.mcda_id,
  547 |         '[BUG-ASUM-05a] Response mcda_id must echo back "PLATFORM" when ?mcda_id=PLATFORM',
  548 |       ).toBe('PLATFORM');
  549 | 
  550 |       expect.soft(identical,
  551 |         '[BUG-ASUM-05a] ?mcda_id=PLATFORM must return PLATFORM-scoped data, not ALL-mcda data',
  552 |       ).toBe(false);
  553 |       logger.pass('All assertions passed');
  554 |     });
  555 | 
  556 |     await logger.step('Step 5 – BUG-ASUM-05b: ?mcda_id=invalid must return 400 or empty data, not ALL data', async () => {
  557 |       if (resInvalid!.status === 200 && resInvalid!.body.mcda_id === 'ALL') {
  558 |         flagIssue('TC-ASUM-006', 'BUG-ASUM-05b',
  559 |           '?mcda_id=invalid_value returns 200 with full ALL-mcda data – invalid mcda_id must return 400 or 404, not fall back to ALL',
  560 |           {
  561 |             param:           'mcda_id=invalid_value',
  562 |             actual_status:   resInvalid!.status,
  563 |             response_mcda_id: resInvalid!.body.mcda_id,
  564 |             expected_status: 400,
  565 |           },
  566 |         );
  567 |       }
  568 |       logger.info('Asserting: ?mcda_id=invalid returns 400');
  569 |       expect.soft(resInvalid!.status,
  570 |         '[BUG-ASUM-05b] ?mcda_id=invalid must return 400 Bad Request, not 200',
  571 |       ).toBe(400);
  572 |       logger.pass('All assertions passed');
  573 |     });
  574 | 
  575 |     attachResponse('TC-ASUM-006', resPlatform!, { query: 'mcda_id=PLATFORM', bug: 'BUG-ASUM-05' });
  576 |     attachResponse('TC-ASUM-006', resInvalid!,  { query: 'mcda_id=invalid',   bug: 'BUG-ASUM-05' });
  577 |   });
  578 | 
  579 |   // ── TC-ASUM-007 ─────────────────────────────────────────────────────────────
  580 |   test('TC-ASUM-007 – BUG-ASUM-06: All query params (?date=, ?period=) silently ignored', async ({ logger }) => {
  581 |     smokeLabels('TC-ASUM-007', 'BUG-ASUM-06 – All Query Params Silently Ignored', 'normal');
  582 |     let resBase: supertest.Response;
  583 | 
  584 |     await logger.step('Step 1 – Baseline', async () => {
  585 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  586 |       resBase = await get();
  587 |       logger.pass('HTTP ' + resBase.status + ' received');
  588 |       logger.info('Asserting: HTTP 200');
  589 |       expect(resBase.status).toBe(200);
  590 |       logger.pass('All assertions passed');
  591 |     });
  592 | 
  593 |     const paramScenarios = [
  594 |       { query: 'date=2026-05-31', label: 'Yesterday date filter' },
  595 |       { query: 'date=2026-06-01', label: 'Today explicit date' },
  596 |       { query: 'period=today',    label: 'Period=today' },
  597 |       { query: 'period=week',     label: 'Period=week' },
  598 |       { query: 'foo=bar',         label: 'Unknown param' },
  599 |     ];
  600 | 
  601 |     for (const scenario of paramScenarios) {
  602 |       await logger.step(`?${scenario.query} – ${scenario.label}`, async () => {
  603 |         logger.info(`GET /api/v1/analytics/dashboard/summary?${scenario.query}`);
  604 |         const res = await get(scenario.query);
  605 |         logger.pass('HTTP ' + res.status + ' received');
  606 |         const identical = JSON.stringify(res.body) === JSON.stringify(resBase!.body);
```