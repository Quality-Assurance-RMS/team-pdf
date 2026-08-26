# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-status-currency.spec.ts >> Smoke â€" Dashboard Status & Currency >> TC-DSH-007 â€" Period filtering: query params must affect returned data (DASH-ISSUE-1)
- Location: tests/API/smoke/smoke-dashboard-status-currency.spec.ts:731:7

# Error details

```
Error: period=week  -> 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  673 |       logger.pass('All assertions passed');
  674 |     });
  675 | 
  676 |     await logger.step('Step 6 â€" Validate numeric metric fields and flag string-type issues', async () => {
  677 |       const d  = res!.body?.data ?? res!.body;
  678 |       const pm = d.peakMetrics as Record<string, unknown>;
  679 | 
  680 |       logger.info('Asserting: numeric metric fields are numbers and change-percentage fields do not embed units in strings');
  681 |       // â"€â"€ DASH-ISSUE-2: peakTransactionsPerDay is a string â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  682 |       if (typeof pm.peakTransactionsPerDay === 'string') {
  683 |         flagIssue('TC-DSH-006', 'DASH-ISSUE-2',
  684 |           'peakMetrics.peakTransactionsPerDay is a string â€" should be a number',
  685 |           pm.peakTransactionsPerDay,
  686 |         );
  687 |       }
  688 |       expect.soft(typeof pm.peakTransactionsPerDay,
  689 |         '[DASH-ISSUE-2] peakMetrics.peakTransactionsPerDay should be a number, not a string',
  690 |       ).toBe('number');
  691 | 
  692 |       // â"€â"€ DASH-ISSUE-3: avgTPM and maxTPM embed the unit in the string value â"€â"€â"€â"€
  693 |       for (const field of ['avgTPM', 'maxTPM'] as const) {
  694 |         const val = String(pm[field] ?? '');
  695 |         if (val.includes('TPM')) {
  696 |           flagIssue('TC-DSH-006', 'DASH-ISSUE-3',
  697 |             `peakMetrics.${field} embeds the unit "TPM" inside the string value â€" should be a plain number with a separate unit field`,
  698 |             pm[field],
  699 |           );
  700 |         }
  701 |         expect.soft(typeof pm[field],
  702 |           `[DASH-ISSUE-3] peakMetrics.${field} should be a number, not a string with embedded unit`,
  703 |         ).toBe('number');
  704 | 
  705 |         allure.parameter(`peakMetrics.${field}`, val + (val.includes('TPM') ? ' âš  DASH-ISSUE-3' : ''));
  706 |       }
  707 | 
  708 |       // â"€â"€ DASH-ISSUE-4: change percentages are formatted strings â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  709 |       for (const field of ['peakTxnDayChange', 'avgTpmChange', 'maxTpmChange'] as const) {
  710 |         const val = String(pm[field] ?? '');
  711 |         if (val.includes('%') || val.startsWith('+') || val.startsWith('-')) {
  712 |           flagIssue('TC-DSH-006', 'DASH-ISSUE-4',
  713 |             `peakMetrics.${field} is a formatted string "${val}" â€" should be a numeric value like 161.3 (consistent with other APIs returning numeric change_pct)`,
  714 |             pm[field],
  715 |           );
  716 |         }
  717 |         expect.soft(typeof pm[field],
  718 |           `[DASH-ISSUE-4] peakMetrics.${field} should be a number, not a formatted string`,
  719 |         ).toBe('number');
  720 | 
  721 |         allure.parameter(`peakMetrics.${field}`, val + (typeof pm[field] === 'string' ? ' âš  DASH-ISSUE-4' : ''));
  722 |       }
  723 | 
  724 |       allure.parameter('peakMetrics.peakTransactionsPerDay', String(pm.peakTransactionsPerDay) + (typeof pm.peakTransactionsPerDay === 'string' ? ' âš  DASH-ISSUE-2' : ''));
  725 |       allure.parameter('peakMetrics.peakHourWindow',         String(pm.peakHourWindow));
  726 |       logger.pass('All assertions passed');
  727 |     });
  728 |   });
  729 | 
  730 |   // â"€â"€ TC-DSH-007 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  731 |   test('TC-DSH-007 â€" Period filtering: query params must affect returned data (DASH-ISSUE-1)', async ({ logger }) => {
  732 |     smokeLabels('TC-DSH-007', 'Status Currency Endpoint', 'Period Filtering â€" date params respected', 'blocker');
  733 | 
  734 |     let resNoParams:     supertest.Response;
  735 |     let resPeriodWeek:   supertest.Response;
  736 |     let resPeriodToday:  supertest.Response;
  737 |     let resCustomRange:  supertest.Response;
  738 |     let resInvalidParam: supertest.Response;
  739 | 
  740 |     await logger.step('Step 1 â€" Build requests for all period variants', async () => {
  741 |       allure.parameter('Base URL',   BASE_URL);
  742 |       allure.parameter('Endpoint',   ENDPOINT);
  743 |       allure.parameter('Method',     'GET');
  744 |       allure.parameter('Scenarios',  'no-params, period=week, period=today, period=custom, period=invalid_xyz');
  745 |       logger.info('Asserting: request parameters are recorded for all period variants');
  746 |       logger.pass('All assertions passed');
  747 |     });
  748 | 
  749 |     await logger.step('Step 2 â€" Send all five requests', async () => {
  750 |       logger.info('GET /api/v1/dashboard/status-currency (5 variants: no-params, period=week, period=today, period=custom, period=invalid_xyz)');
  751 |       resNoParams     = await get();
  752 |       resPeriodWeek   = await get({ period: 'week' });
  753 |       resPeriodToday  = await get({ period: 'today' });
  754 |       resCustomRange  = await get({ period: 'custom', from_date: '2026-01-01', to_date: '2026-05-31' });
  755 |       resInvalidParam = await get({ period: 'invalid_xyz' });
  756 |       logger.pass('HTTP ' + resNoParams.status + ' received (no-params)');
  757 | 
  758 |       for (const [label, r] of [
  759 |         ['no-params', resNoParams], ['period=week', resPeriodWeek],
  760 |         ['period=today', resPeriodToday], ['period=custom', resCustomRange],
  761 |         ['period=invalid_xyz', resInvalidParam],
  762 |       ] as [string, supertest.Response][]) {
  763 |         console.log(`\n[TC-DSH-007] ${label}  -> status=${r.status}`);
  764 |         allure.attachment(`[TC-DSH-007] ${label}`,
  765 |           JSON.stringify({ status: r.status, body: r.body }, null, 2), 'application/json',
  766 |         );
  767 |       }
  768 |     });
  769 | 
  770 |     await logger.step('Step 3 â€" All valid period values must return HTTP 200', async () => {
  771 |       logger.info('Asserting: no-params, period=week, period=today, and period=custom all return 200');
  772 |       expect(resNoParams!.status,    'no-params  -> 200').toBe(200);
> 773 |       expect(resPeriodWeek!.status,  'period=week  -> 200').toBe(200);
      |                                                             ^ Error: period=week  -> 200
  774 |       expect(resPeriodToday!.status, 'period=today  -> 200').toBe(200);
  775 |       expect(resCustomRange!.status, 'period=custom  -> 200').toBe(200);
  776 |       logger.pass('All assertions passed');
  777 |     });
  778 | 
  779 |     await logger.step('Step 4 â€" Invalid period value must return 400 (flag DASH-ISSUE-1 if 200)', async () => {
  780 |       // An invalid period parameter should be rejected with 400.
  781 |       // If it returns 200 with identical data it means the param is silently ignored.
  782 |       if (resInvalidParam!.status === 200) {
  783 |         flagIssue('TC-DSH-007', 'DASH-ISSUE-1',
  784 |           'period=invalid_xyz returned HTTP 200 instead of 400 â€" invalid parameter values are silently accepted',
  785 |           { status: resInvalidParam!.status, body: resInvalidParam!.body },
  786 |         );
  787 |       }
  788 | 
  789 |       logger.info('Asserting: period=invalid_xyz returns 400, not 200 (invalid params should be rejected)');
  790 |       expect.soft(resInvalidParam!.status,
  791 |         '[DASH-ISSUE-1] period=invalid_xyz should return 400, not 200',
  792 |       ).toBe(400);
  793 | 
  794 |       allure.parameter('period=invalid_xyz status', String(resInvalidParam!.status) + (resInvalidParam!.status === 200 ? ' âš  DASH-ISSUE-1' : ''));
  795 |       logger.pass('All assertions passed');
  796 |     });
  797 | 
  798 |     await logger.step('Step 5 â€" period=today and period=week must return DIFFERENT data (flag DASH-ISSUE-1 if identical)', async () => {
  799 |       const bodyToday = JSON.stringify(resPeriodToday!.body);
  800 |       const bodyWeek  = JSON.stringify(resPeriodWeek!.body);
  801 |       const bodyNone  = JSON.stringify(resNoParams!.body);
  802 | 
  803 |       const todayEqualsWeek = bodyToday === bodyWeek;
  804 |       const todayEqualsNone = bodyToday === bodyNone;
  805 |       const weekEqualsNone  = bodyWeek  === bodyNone;
  806 | 
  807 |       if (todayEqualsWeek || todayEqualsNone || weekEqualsNone) {
  808 |         flagIssue('TC-DSH-007', 'DASH-ISSUE-1',
  809 |           'period query parameter is completely ignored â€" period=today, period=week, and no-params all return identical response bodies. Date filtering is not implemented.',
  810 |           {
  811 |             'today === week':       todayEqualsWeek,
  812 |             'today === no-params':  todayEqualsNone,
  813 |             'week === no-params':   weekEqualsNone,
  814 |           },
  815 |         );
  816 |       }
  817 | 
  818 |       logger.info('Asserting: period=today and period=week return different data from each other and from no-params');
  819 |       expect.soft(todayEqualsWeek,
  820 |         '[DASH-ISSUE-1] period=today and period=week should return DIFFERENT data â€" they are currently identical',
  821 |       ).toBe(false);
  822 | 
  823 |       expect.soft(todayEqualsNone,
  824 |         '[DASH-ISSUE-1] period=today and no-params should return DIFFERENT data â€" they are currently identical',
  825 |       ).toBe(false);
  826 | 
  827 |       allure.parameter('today === week response',       String(todayEqualsWeek)  + (todayEqualsWeek  ? ' âš  DASH-ISSUE-1 IDENTICAL' : ' â€" DIFFERENT (OK)'));
  828 |       allure.parameter('today === no-params response',  String(todayEqualsNone)  + (todayEqualsNone  ? ' âš  DASH-ISSUE-1 IDENTICAL' : ' â€" DIFFERENT (OK)'));
  829 |       allure.parameter('week === no-params response',   String(weekEqualsNone)   + (weekEqualsNone   ? ' âš  DASH-ISSUE-1 IDENTICAL' : ' â€" DIFFERENT (OK)'));
  830 |       logger.pass('All assertions passed');
  831 |     });
  832 | 
  833 |     await logger.step('Step 6 â€" period=custom date range must return different data from no-params', async () => {
  834 |       const bodyCustom = JSON.stringify(resCustomRange!.body);
  835 |       const bodyNone   = JSON.stringify(resNoParams!.body);
  836 |       const identical  = bodyCustom === bodyNone;
  837 | 
  838 |       if (identical) {
  839 |         flagIssue('TC-DSH-007', 'DASH-ISSUE-1',
  840 |           'period=custom&from_date=2026-01-01&to_date=2026-05-31 returns identical data to no-params â€" date range filtering is not applied',
  841 |           { identical },
  842 |         );
  843 |       }
  844 | 
  845 |       logger.info('Asserting: period=custom with specific date range returns different data from no-params');
  846 |       expect.soft(identical,
  847 |         '[DASH-ISSUE-1] period=custom with specific date range should return DIFFERENT data from no-params',
  848 |       ).toBe(false);
  849 | 
  850 |       allure.parameter('custom === no-params response', String(identical) + (identical ? ' âš  DASH-ISSUE-1 IDENTICAL' : ' â€" DIFFERENT (OK)'));
  851 |       logger.pass('All assertions passed');
  852 |     });
  853 |   });
  854 | 
  855 | });
  856 | 
```