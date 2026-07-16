# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-008 – BUG-ASUM-07: Response must have top-level "data" wrapper (consistent with all other endpoints)
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:632:7

# Error details

```
Error: [BUG-ASUM-07] Response must use a top-level "data" wrapper consistent with all other endpoints

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

# Test source

```ts
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
  607 | 
  608 |         allure.parameter(`?${scenario.query} status`, String(res.status));
  609 |         allure.parameter(`?${scenario.query} identical to baseline`, String(identical));
  610 | 
  611 |         if (identical && res.status === 200) {
  612 |           flagIssue('TC-ASUM-007', 'BUG-ASUM-06',
  613 |             `?${scenario.query} returns IDENTICAL response to no-param call – parameter is silently ignored. ` +
  614 |             'If filtering by date/period is supported, data must reflect the filter. ' +
  615 |             'If not supported, unknown params must return 400 Bad Request.',
  616 |             { query: scenario.query, label: scenario.label, identical_to_baseline: identical },
  617 |           );
  618 |         }
  619 | 
  620 |         if (scenario.query.startsWith('date=') || scenario.query.startsWith('period=')) {
  621 |           logger.info(`Asserting: ?${scenario.query} returns filtered data or 400, not identical baseline`);
  622 |           expect.soft(identical,
  623 |             `[BUG-ASUM-06] ?${scenario.query} must return filtered data or 400, not identical baseline data`,
  624 |           ).toBe(false);
  625 |           logger.pass('All assertions passed');
  626 |         }
  627 |       });
  628 |     }
  629 |   });
  630 | 
  631 |   // ── TC-ASUM-008 ─────────────────────────────────────────────────────────────
  632 |   test('TC-ASUM-008 – BUG-ASUM-07: Response must have top-level "data" wrapper (consistent with all other endpoints)', async ({ logger }) => {
  633 |     smokeLabels('TC-ASUM-008', 'BUG-ASUM-07 – Missing "data" Wrapper Envelope', 'normal');
  634 |     let res: supertest.Response;
  635 | 
  636 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
  637 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  638 |       res = await get();
  639 |       logger.pass('HTTP ' + res.status + ' received');
  640 |       logger.info('Asserting: HTTP 200');
  641 |       expect(res.status).toBe(200);
  642 |       logger.pass('All assertions passed');
  643 |       attachResponse('TC-ASUM-008', res, { bug: 'BUG-ASUM-07' });
  644 |     });
  645 | 
  646 |     await logger.step('Step 2 – BUG-ASUM-07: response must be wrapped in a "data" envelope', async () => {
  647 |       const hasDataWrapper = 'data' in res!.body;
  648 |       const topLevelKeys   = Object.keys(res!.body);
  649 | 
  650 |       allure.parameter('Top-level keys',    topLevelKeys.join(', '));
  651 |       allure.parameter('Has "data" wrapper', String(hasDataWrapper));
  652 | 
  653 |       if (!hasDataWrapper) {
  654 |         flagIssue('TC-ASUM-008', 'BUG-ASUM-07',
  655 |           'This endpoint returns {mcda_id, today:{...}} without a top-level "data" wrapper. ' +
  656 |           'All other dashboard/analytics endpoints use {data:{...}} as their response envelope ' +
  657 |           '(e.g. /dashboard/today-payments, /dashboard/monthly-revenue, /dashboard/revenue-trend). ' +
  658 |           'Inconsistent response envelopes force clients to handle multiple response shapes – ' +
  659 |           'a shared API contract requires a uniform envelope.',
  660 |           {
  661 |             actual_structure:   topLevelKeys,
  662 |             expected_structure: '{ data: { mcda_id, today: {...} } }',
  663 |             other_endpoints:    '{ data: { ... } }',
  664 |           },
  665 |         );
  666 |       }
  667 | 
  668 |       logger.info('Asserting: response has top-level "data" wrapper');
  669 |       expect.soft(hasDataWrapper,
  670 |         '[BUG-ASUM-07] Response must use a top-level "data" wrapper consistent with all other endpoints',
> 671 |       ).toBe(true);
      |         ^ Error: [BUG-ASUM-07] Response must use a top-level "data" wrapper consistent with all other endpoints
  672 |       logger.pass('All assertions passed');
  673 |     });
  674 |   });
  675 | 
  676 |   // ── TC-ASUM-009 ─────────────────────────────────────────────────────────────
  677 |   test('TC-ASUM-009 – BUG-ASUM-08: CORS Allow-Methods advertises POST/DELETE but server returns 405', async ({ logger }) => {
  678 |     smokeLabels('TC-ASUM-009', 'BUG-ASUM-08 – CORS Allow-Methods Inconsistent with Actual Routing', 'normal');
  679 |     let res: supertest.Response;
  680 | 
  681 |     await logger.step('Step 1 – Fetch response headers', async () => {
  682 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  683 |       res = await get();
  684 |       logger.pass('HTTP ' + res.status + ' received');
  685 |       attachResponse('TC-ASUM-009', res, { bug: 'BUG-ASUM-08' });
  686 |     });
  687 | 
  688 |     await logger.step('Step 2 – BUG-ASUM-08: CORS ACAM must match actual supported methods', async () => {
  689 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  690 |       allure.parameter('Access-Control-Allow-Methods (CORS)', acam ?? 'MISSING');
  691 | 
  692 |       // Server correctly returns 405 for POST/PUT/DELETE/PATCH
  693 |       // But CORS header says these are allowed – inconsistency
  694 |       const corsAllowsDestructive =
  695 |         acam?.toUpperCase().includes('POST')   ||
  696 |         acam?.toUpperCase().includes('DELETE') ||
  697 |         acam?.toUpperCase().includes('PUT')    ||
  698 |         acam?.toUpperCase().includes('PATCH');
  699 | 
  700 |       if (corsAllowsDestructive) {
  701 |         flagIssue('TC-ASUM-009', 'BUG-ASUM-08',
  702 |           `Access-Control-Allow-Methods: ${acam} – CORS advertises POST/PUT/DELETE/PATCH as allowed, ` +
  703 |           'but the server correctly returns 405 for all of them. ' +
  704 |           'The CORS configuration and the actual method routing are out of sync. ' +
  705 |           'ACAM should only list: GET, HEAD, OPTIONS.',
  706 |           {
  707 |             'access-control-allow-methods': acam,
  708 |             actual_supported: 'GET only (others return 405)',
  709 |             should_be: 'GET, HEAD, OPTIONS',
  710 |           },
  711 |         );
  712 |       }
  713 | 
  714 |       logger.info('Asserting: CORS ACAM must not advertise POST/DELETE/PUT/PATCH');
  715 |       expect.soft(corsAllowsDestructive,
  716 |         `[BUG-ASUM-08] CORS ACAM must not advertise POST/DELETE/PUT/PATCH when server returns 405 for them. Got: ${acam}`,
  717 |       ).toBe(false);
  718 |       logger.pass('All assertions passed');
  719 |     });
  720 | 
  721 |     await logger.step('Step 3 – Confirm POST returns 405 (method routing is correct)', async () => {
  722 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  723 |       const resPost = await getWithMethod('post');
  724 |       logger.pass('HTTP ' + resPost.status + ' received');
  725 |       logger.info('Asserting: POST returns 405 and Allow header contains GET');
  726 |       expect(resPost.status, 'POST must return 405 (correct behavior)').toBe(405);
  727 |       expect(resPost.headers['allow'], '405 response must include Allow: GET').toContain('GET');
  728 |       logger.pass('All assertions passed');
  729 |       allure.parameter('POST status (correct 405)', String(resPost.status));
  730 |       allure.parameter('Allow header in 405', String(resPost.headers['allow'] ?? 'MISSING'));
  731 |     });
  732 |   });
  733 | 
  734 |   // ── TC-ASUM-010 ─────────────────────────────────────────────────────────────
  735 |   test('TC-ASUM-010 – BUG-ASUM-09: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  736 |     smokeLabels('TC-ASUM-010', 'BUG-ASUM-09 – Framework Info Leak on 406', 'normal');
  737 | 
  738 |     await logger.step('Step 1 – Send GET with Accept: text/html', async () => {
  739 |       logger.info('GET /api/v1/analytics/dashboard/summary (Accept: text/html)');
  740 |       const res = await supertest(ANALYTICS_BASE)
  741 |         .get(ENDPOINT)
  742 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  743 |         .set('Accept', 'text/html');
  744 |       logger.pass('HTTP ' + res.status + ' received');
  745 | 
  746 |       console.log(`[TC-ASUM-010] text/html → ${res.status}, body excerpt: ${String(res.text).substring(0, 300)}`);
  747 |       allure.parameter('text/html status', String(res.status));
  748 | 
  749 |       logger.info('Asserting: status 406, no Whitelabel page');
  750 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
  751 | 
  752 |       const hasWhitelabel = String(res.text ?? '').includes('Whitelabel Error Page');
  753 |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  754 | 
  755 |       if (hasWhitelabel) {
  756 |         flagIssue('TC-ASUM-010', 'BUG-ASUM-09',
  757 |           'GET /rms-analytics/.../summary with Accept: text/html returns Spring Boot Whitelabel Error Page – ' +
  758 |           'leaks Spring Boot framework identity, server timezone (EAT), and internal request path',
  759 |           {
  760 |             status:       res.status,
  761 |             info_leaked:  ['Spring Boot', 'EAT timezone', '/api/v1/analytics/dashboard/summary path'],
  762 |             body_excerpt: String(res.text).substring(0, 400),
  763 |           },
  764 |         );
  765 |       }
  766 | 
  767 |       expect.soft(hasWhitelabel, '[BUG-ASUM-09] 406 error must NOT expose Whitelabel Error Page').toBe(false);
  768 |       expect.soft(res.headers['content-type'], '[BUG-ASUM-09] 406 error body should be application/json').toContain('application/json');
  769 |       logger.pass('All assertions passed');
  770 |       allure.attachment('[TC-ASUM-010] 406 Body', String(res.text ?? ''), 'text/plain');
  771 |     });
```