# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-analytics-dashboard-summary.spec.ts >> Backend (Konza) – Analytics Dashboard Summary >> TC-ASUM-007 – BUG-ASUM-06: All query params (?date=, ?period=) silently ignored
- Location: tests/API/backend-tests/backend-analytics-dashboard-summary.spec.ts:588:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  497 |       logger.pass('All assertions passed');
  498 |     });
  499 |   });
  500 | 
  501 |   // ── TC-ASUM-006 ─────────────────────────────────────────────────────────────
  502 |   test('TC-ASUM-006 – BUG-ASUM-05: ?mcda_id= query param is completely ignored', async ({ logger }) => {
  503 |     backendLabels('TC-ASUM-006', 'BUG-ASUM-05 – mcda_id Filter Silently Ignored', 'critical');
  504 | 
  505 |     let resBase:     supertest.Response;
  506 |     let resPlatform: supertest.Response;
  507 |     let resInvalid:  supertest.Response;
  508 | 
  509 |     await logger.step('Step 1 – Baseline (no mcda_id param)', async () => {
  510 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  511 |       resBase = await get();
  512 |       logger.pass('HTTP ' + resBase.status + ' received');
  513 |       logger.info('Asserting: HTTP 200');
  514 |       expect(resBase.status).toBe(200);
  515 |       logger.pass('All assertions passed');
  516 |       allure.parameter('baseline mcda_id in response', String(resBase.body.mcda_id));
  517 |     });
  518 | 
  519 |     await logger.step('Step 2 – ?mcda_id=PLATFORM', async () => {
  520 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=PLATFORM');
  521 |       resPlatform = await get('mcda_id=PLATFORM');
  522 |       logger.pass('HTTP ' + resPlatform.status + ' received');
  523 |       allure.parameter('?mcda_id=PLATFORM status', String(resPlatform.status));
  524 |       allure.parameter('?mcda_id=PLATFORM response mcda_id', String(resPlatform.body?.mcda_id));
  525 |     });
  526 | 
  527 |     await logger.step('Step 3 – ?mcda_id=invalid_value', async () => {
  528 |       logger.info('GET /api/v1/analytics/dashboard/summary?mcda_id=invalid_value');
  529 |       resInvalid = await get('mcda_id=invalid_value');
  530 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  531 |       allure.parameter('?mcda_id=invalid status', String(resInvalid.status));
  532 |       allure.parameter('?mcda_id=invalid response mcda_id', String(resInvalid.body?.mcda_id));
  533 |     });
  534 | 
  535 |     await logger.step('Step 4 – BUG-ASUM-05a: mcda_id param must filter data', async () => {
  536 |       const baseData     = JSON.stringify(resBase!.body.today);
  537 |       const platformData = JSON.stringify(resPlatform!.body.today);
  538 |       const identical    = baseData === platformData;
  539 | 
  540 |       if (identical && resPlatform!.body.mcda_id === 'ALL') {
  541 |         flagIssue('TC-ASUM-006', 'BUG-ASUM-05a',
  542 |           '?mcda_id=PLATFORM returns identical data and mcda_id="ALL" – the filter is completely ignored. ' +
  543 |           'The response should show data scoped to the PLATFORM mcda, not ALL mcda combined.',
  544 |           {
  545 |             param: 'mcda_id=PLATFORM',
  546 |             response_mcda_id: resPlatform!.body.mcda_id,
  547 |             expected_mcda_id: 'PLATFORM',
  548 |             data_identical_to_ALL: identical,
  549 |           },
  550 |         );
  551 |       }
  552 | 
  553 |       logger.info('Asserting: response mcda_id echoes PLATFORM and data differs from baseline');
  554 |       expect.soft(resPlatform!.body.mcda_id,
  555 |         '[BUG-ASUM-05a] Response mcda_id must echo back "PLATFORM" when ?mcda_id=PLATFORM',
  556 |       ).toBe('PLATFORM');
  557 | 
  558 |       expect.soft(identical,
  559 |         '[BUG-ASUM-05a] ?mcda_id=PLATFORM must return PLATFORM-scoped data, not ALL-mcda data',
  560 |       ).toBe(false);
  561 |       logger.pass('All assertions passed');
  562 |     });
  563 | 
  564 |     await logger.step('Step 5 – BUG-ASUM-05b: ?mcda_id=invalid must return 400 or empty data, not ALL data', async () => {
  565 |       if (resInvalid!.status === 200 && resInvalid!.body.mcda_id === 'ALL') {
  566 |         flagIssue('TC-ASUM-006', 'BUG-ASUM-05b',
  567 |           '?mcda_id=invalid_value returns 200 with full ALL-mcda data – invalid mcda_id must return 400 or 404, not fall back to ALL',
  568 |           {
  569 |             param:           'mcda_id=invalid_value',
  570 |             actual_status:   resInvalid!.status,
  571 |             response_mcda_id: resInvalid!.body.mcda_id,
  572 |             expected_status: 400,
  573 |           },
  574 |         );
  575 |       }
  576 |       logger.info('Asserting: ?mcda_id=invalid returns 400');
  577 |       expect.soft(resInvalid!.status,
  578 |         '[BUG-ASUM-05b] ?mcda_id=invalid must return 400 Bad Request, not 200',
  579 |       ).toBe(400);
  580 |       logger.pass('All assertions passed');
  581 |     });
  582 | 
  583 |     attachResponse('TC-ASUM-006', resPlatform!, { query: 'mcda_id=PLATFORM', bug: 'BUG-ASUM-05' });
  584 |     attachResponse('TC-ASUM-006', resInvalid!,  { query: 'mcda_id=invalid',   bug: 'BUG-ASUM-05' });
  585 |   });
  586 | 
  587 |   // ── TC-ASUM-007 ─────────────────────────────────────────────────────────────
  588 |   test('TC-ASUM-007 – BUG-ASUM-06: All query params (?date=, ?period=) silently ignored', async ({ logger }) => {
  589 |     backendLabels('TC-ASUM-007', 'BUG-ASUM-06 – All Query Params Silently Ignored', 'normal');
  590 |     let resBase: supertest.Response;
  591 | 
  592 |     await logger.step('Step 1 – Baseline', async () => {
  593 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  594 |       resBase = await get();
  595 |       logger.pass('HTTP ' + resBase.status + ' received');
  596 |       logger.info('Asserting: HTTP 200');
> 597 |       expect(resBase.status).toBe(200);
      |                              ^ Error: expect(received).toBe(expected) // Object.is equality
  598 |       logger.pass('All assertions passed');
  599 |     });
  600 | 
  601 |     const paramScenarios = [
  602 |       { query: 'date=2026-05-31', label: 'Yesterday date filter' },
  603 |       { query: 'date=2026-06-01', label: 'Today explicit date' },
  604 |       { query: 'period=today',    label: 'Period=today' },
  605 |       { query: 'period=week',     label: 'Period=week' },
  606 |       { query: 'foo=bar',         label: 'Unknown param' },
  607 |     ];
  608 | 
  609 |     for (const scenario of paramScenarios) {
  610 |       await logger.step(`?${scenario.query} – ${scenario.label}`, async () => {
  611 |         logger.info(`GET /api/v1/analytics/dashboard/summary?${scenario.query}`);
  612 |         const res = await get(scenario.query);
  613 |         logger.pass('HTTP ' + res.status + ' received');
  614 |         const identical = JSON.stringify(res.body) === JSON.stringify(resBase!.body);
  615 | 
  616 |         allure.parameter(`?${scenario.query} status`, String(res.status));
  617 |         allure.parameter(`?${scenario.query} identical to baseline`, String(identical));
  618 | 
  619 |         if (identical && res.status === 200) {
  620 |           flagIssue('TC-ASUM-007', 'BUG-ASUM-06',
  621 |             `?${scenario.query} returns IDENTICAL response to no-param call – parameter is silently ignored. ` +
  622 |             'If filtering by date/period is supported, data must reflect the filter. ' +
  623 |             'If not supported, unknown params must return 400 Bad Request.',
  624 |             { query: scenario.query, label: scenario.label, identical_to_baseline: identical },
  625 |           );
  626 |         }
  627 | 
  628 |         if (scenario.query.startsWith('date=') || scenario.query.startsWith('period=')) {
  629 |           logger.info(`Asserting: ?${scenario.query} returns filtered data or 400, not identical baseline`);
  630 |           expect.soft(identical,
  631 |             `[BUG-ASUM-06] ?${scenario.query} must return filtered data or 400, not identical baseline data`,
  632 |           ).toBe(false);
  633 |           logger.pass('All assertions passed');
  634 |         }
  635 |       });
  636 |     }
  637 |   });
  638 | 
  639 |   // ── TC-ASUM-008 ─────────────────────────────────────────────────────────────
  640 |   test('TC-ASUM-008 – BUG-ASUM-07: Response must have top-level "data" wrapper (consistent with all other endpoints)', async ({ logger }) => {
  641 |     backendLabels('TC-ASUM-008', 'BUG-ASUM-07 – Missing "data" Wrapper Envelope', 'normal');
  642 |     let res: supertest.Response;
  643 | 
  644 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  645 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  646 |       res = await get();
  647 |       logger.pass('HTTP ' + res.status + ' received');
  648 |       logger.info('Asserting: HTTP 200');
  649 |       expect(res.status).toBe(200);
  650 |       logger.pass('All assertions passed');
  651 |       attachResponse('TC-ASUM-008', res, { bug: 'BUG-ASUM-07' });
  652 |     });
  653 | 
  654 |     await logger.step('Step 2 – BUG-ASUM-07: response must be wrapped in a "data" envelope', async () => {
  655 |       const hasDataWrapper = 'data' in res!.body;
  656 |       const topLevelKeys   = Object.keys(res!.body);
  657 | 
  658 |       allure.parameter('Top-level keys',    topLevelKeys.join(', '));
  659 |       allure.parameter('Has "data" wrapper', String(hasDataWrapper));
  660 | 
  661 |       if (!hasDataWrapper) {
  662 |         flagIssue('TC-ASUM-008', 'BUG-ASUM-07',
  663 |           'This endpoint returns {mcda_id, today:{...}} without a top-level "data" wrapper. ' +
  664 |           'All other dashboard/analytics endpoints use {data:{...}} as their response envelope ' +
  665 |           '(e.g. /dashboard/today-payments, /dashboard/monthly-revenue, /dashboard/revenue-trend). ' +
  666 |           'Inconsistent response envelopes force clients to handle multiple response shapes – ' +
  667 |           'a shared API contract requires a uniform envelope.',
  668 |           {
  669 |             actual_structure:   topLevelKeys,
  670 |             expected_structure: '{ data: { mcda_id, today: {...} } }',
  671 |             other_endpoints:    '{ data: { ... } }',
  672 |           },
  673 |         );
  674 |       }
  675 | 
  676 |       logger.info('Asserting: response has top-level "data" wrapper');
  677 |       expect.soft(hasDataWrapper,
  678 |         '[BUG-ASUM-07] Response must use a top-level "data" wrapper consistent with all other endpoints',
  679 |       ).toBe(true);
  680 |       logger.pass('All assertions passed');
  681 |     });
  682 |   });
  683 | 
  684 |   // ── TC-ASUM-009 ─────────────────────────────────────────────────────────────
  685 |   test('TC-ASUM-009 – BUG-ASUM-08: CORS Allow-Methods advertises POST/DELETE but server returns 405', async ({ logger }) => {
  686 |     backendLabels('TC-ASUM-009', 'BUG-ASUM-08 – CORS Allow-Methods Inconsistent with Actual Routing', 'normal');
  687 |     let res: supertest.Response;
  688 | 
  689 |     await logger.step('Step 1 – Fetch response headers', async () => {
  690 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  691 |       res = await get();
  692 |       logger.pass('HTTP ' + res.status + ' received');
  693 |       attachResponse('TC-ASUM-009', res, { bug: 'BUG-ASUM-08' });
  694 |     });
  695 | 
  696 |     await logger.step('Step 2 – BUG-ASUM-08: CORS ACAM must match actual supported methods', async () => {
  697 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
```