# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-analytics-dashboard-summary.spec.ts >> Backend (Konza) – Analytics Dashboard Summary >> TC-ASUM-008 – BUG-ASUM-07: Response must have top-level "data" wrapper (consistent with all other endpoints)
- Location: tests/API/backend-tests/backend-analytics-dashboard-summary.spec.ts:640:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
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
  597 |       expect(resBase.status).toBe(200);
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
> 649 |       expect(res.status).toBe(200);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
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
  698 |       allure.parameter('Access-Control-Allow-Methods (CORS)', acam ?? 'MISSING');
  699 | 
  700 |       // Server correctly returns 405 for POST/PUT/DELETE/PATCH
  701 |       // But CORS header says these are allowed – inconsistency
  702 |       const corsAllowsDestructive =
  703 |         acam?.toUpperCase().includes('POST')   ||
  704 |         acam?.toUpperCase().includes('DELETE') ||
  705 |         acam?.toUpperCase().includes('PUT')    ||
  706 |         acam?.toUpperCase().includes('PATCH');
  707 | 
  708 |       if (corsAllowsDestructive) {
  709 |         flagIssue('TC-ASUM-009', 'BUG-ASUM-08',
  710 |           `Access-Control-Allow-Methods: ${acam} – CORS advertises POST/PUT/DELETE/PATCH as allowed, ` +
  711 |           'but the server correctly returns 405 for all of them. ' +
  712 |           'The CORS configuration and the actual method routing are out of sync. ' +
  713 |           'ACAM should only list: GET, HEAD, OPTIONS.',
  714 |           {
  715 |             'access-control-allow-methods': acam,
  716 |             actual_supported: 'GET only (others return 405)',
  717 |             should_be: 'GET, HEAD, OPTIONS',
  718 |           },
  719 |         );
  720 |       }
  721 | 
  722 |       logger.info('Asserting: CORS ACAM must not advertise POST/DELETE/PUT/PATCH');
  723 |       expect.soft(corsAllowsDestructive,
  724 |         `[BUG-ASUM-08] CORS ACAM must not advertise POST/DELETE/PUT/PATCH when server returns 405 for them. Got: ${acam}`,
  725 |       ).toBe(false);
  726 |       logger.pass('All assertions passed');
  727 |     });
  728 | 
  729 |     await logger.step('Step 3 – Confirm POST returns 405 (method routing is correct)', async () => {
  730 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  731 |       const resPost = await getWithMethod('post');
  732 |       logger.pass('HTTP ' + resPost.status + ' received');
  733 |       logger.info('Asserting: POST returns 405 and Allow header contains GET');
  734 |       expect(resPost.status, 'POST must return 405 (correct behavior)').toBe(405);
  735 |       expect(resPost.headers['allow'], '405 response must include Allow: GET').toContain('GET');
  736 |       logger.pass('All assertions passed');
  737 |       allure.parameter('POST status (correct 405)', String(resPost.status));
  738 |       allure.parameter('Allow header in 405', String(resPost.headers['allow'] ?? 'MISSING'));
  739 |     });
  740 |   });
  741 | 
  742 |   // ── TC-ASUM-010 ─────────────────────────────────────────────────────────────
  743 |   test('TC-ASUM-010 – BUG-ASUM-09: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  744 |     backendLabels('TC-ASUM-010', 'BUG-ASUM-09 – Framework Info Leak on 406', 'normal');
  745 | 
  746 |     await logger.step('Step 1 – Send GET with Accept: text/html', async () => {
  747 |       logger.info('GET /api/v1/analytics/dashboard/summary (Accept: text/html)');
  748 |       const res = await supertest(ANALYTICS_BASE)
  749 |         .get(ENDPOINT)
```