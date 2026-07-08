# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-status-currency.spec.ts >> Smoke â€" Dashboard Status & Currency >> TC-DSH-006 â€" peakMetrics: all nine fields present with correct types
- Location: tests/API/smoke/smoke-dashboard-status-currency.spec.ts:611:7

# Error details

```
Error: peakMetrics.peakTxnDayUp must be present

expect(received).toHaveProperty(path)

Expected path: "peakTxnDayUp"
Received path: []

Received value: {"avgTPM": 14263.3, "avgTpmChange": 100, "avgTpmDirection": "up", "maxTPM": 27786, "maxTpmChange": 100, "maxTpmDirection": "up", "peakHourWindow": "12:00 PM – 12:59 PM", "peakHourWindowChange": 100, "peakHourWindowDirection": "up", "peakTransactionDate": "2026-07-02", "peakTransactionsPerDay": 72110, "peakTxnDayChange": 100, "peakTxnDayDirection": "up"}
```

# Test source

```ts
  557 |     await logger.step('Step 4 â€" Validate each byMethod item has all required fields', async () => {
  558 |       const d   = res!.body?.data ?? res!.body;
  559 |       const bym = d.byMethod as Array<Record<string, unknown>>;
  560 | 
  561 |       logger.info('Asserting: each byMethod item has method, successKES, successCount, failKES, and failCount fields');
  562 |       for (const [i, item] of bym.entries()) {
  563 |         expect(item, `byMethod[${i}] must have "method" field`).toHaveProperty('method');
  564 |         expect(item, `byMethod[${i}] must have "successKES" field`).toHaveProperty('successKES');
  565 |         expect(item, `byMethod[${i}] must have "successCount" field`).toHaveProperty('successCount');
  566 |         expect(item, `byMethod[${i}] must have "failKES" field`).toHaveProperty('failKES');
  567 |         expect(item, `byMethod[${i}] must have "failCount" field`).toHaveProperty('failCount');
  568 | 
  569 |         expect(typeof item.method, `byMethod[${i}].method must be a string`).toBe('string');
  570 |         expect((item.method as string).length, `byMethod[${i}].method must not be empty`).toBeGreaterThan(0);
  571 | 
  572 |         // â"€â"€ DASH-ISSUE-2 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  573 |         for (const field of ['successKES', 'successCount', 'failKES', 'failCount'] as const) {
  574 |           if (typeof item[field] === 'string') {
  575 |             flagIssue('TC-DSH-005', 'DASH-ISSUE-2',
  576 |               `byMethod[${i}].${field} is a string â€" should be a number for arithmetic`,
  577 |               item[field],
  578 |             );
  579 |           }
  580 |           expect.soft(typeof item[field],
  581 |             `[DASH-ISSUE-2] byMethod[${i}].${field} should be a number, not a string`,
  582 |           ).toBe('number');
  583 |         }
  584 | 
  585 |         allure.parameter(`byMethod[${i}].method`,       String(item.method));
  586 |         allure.parameter(`byMethod[${i}].successKES`,   String(item.successKES));
  587 |         allure.parameter(`byMethod[${i}].successCount`, String(item.successCount));
  588 |         allure.parameter(`byMethod[${i}].failKES`,      String(item.failKES));
  589 |         allure.parameter(`byMethod[${i}].failCount`,    String(item.failCount));
  590 |       }
  591 |       logger.pass('All assertions passed');
  592 |     });
  593 | 
  594 |     await logger.step('Step 5 â€" Validate known payment methods are present', async () => {
  595 |       const d       = res!.body?.data ?? res!.body;
  596 |       const bym     = d.byMethod as Array<{ method: string }>;
  597 |       const methods = bym.map(m => m.method);
  598 | 
  599 |       logger.info('Asserting: MPESA and CARD payment methods are present in byMethod');
  600 |       for (const expected of KNOWN_METHODS) {
  601 |         expect(methods, `"${expected}" must be in byMethod`).toContain(expected);
  602 |       }
  603 | 
  604 |       allure.parameter('byMethod methods', methods.join(', '));
  605 |       allure.parameter('Expected methods', KNOWN_METHODS.join(', ') + ' â€" PASS');
  606 |       logger.pass('All assertions passed');
  607 |     });
  608 |   });
  609 | 
  610 |   // â"€â"€ TC-DSH-006 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  611 |   test('TC-DSH-006 â€" peakMetrics: all nine fields present with correct types', async ({ logger }) => {
  612 |     smokeLabels('TC-DSH-006', 'Status Currency Endpoint', 'peakMetrics â€" Field Presence & Type Validation');
  613 | 
  614 |     let res: supertest.Response;
  615 | 
  616 |     await logger.step('Step 1 â€" Build request', async () => {
  617 |       allure.parameter('Base URL', BASE_URL);
  618 |       allure.parameter('Endpoint', ENDPOINT);
  619 |       allure.parameter('Method',   'GET');
  620 |       logger.info('Asserting: request parameters are recorded');
  621 |       logger.pass('All assertions passed');
  622 |     });
  623 | 
  624 |     await logger.step('Step 2 â€" Send GET /status-currency', async () => {
  625 |       logger.info('GET /api/v1/dashboard/status-currency');
  626 |       res = await get();
  627 |       logger.pass('HTTP ' + res.status + ' received');
  628 |       logResponse('TC-DSH-006', {}, res);
  629 |       allure.parameter('HTTP Status', String(res.status));
  630 |     });
  631 | 
  632 |     await logger.step('Step 3 â€" Validate HTTP 200 and peakMetrics is an object', async () => {
  633 |       logger.info('Asserting: HTTP 200 and peakMetrics is a defined non-array object');
  634 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  635 |       const d  = res!.body?.data ?? res!.body;
  636 |       const pm = d.peakMetrics;
  637 |       expect(pm,                      'peakMetrics must be defined').toBeDefined();
  638 |       expect(typeof pm,               'peakMetrics must be an object').toBe('object');
  639 |       expect(Array.isArray(pm),       'peakMetrics must not be an array').toBe(false);
  640 |       logger.pass('All assertions passed');
  641 |     });
  642 | 
  643 |     await logger.step('Step 4 â€" Validate all nine peakMetrics fields are present', async () => {
  644 |       const d  = res!.body?.data ?? res!.body;
  645 |       const pm = d.peakMetrics as Record<string, unknown>;
  646 | 
  647 |       const expectedFields = [
  648 |         'peakTransactionsPerDay', 'peakHourWindow',
  649 |         'avgTPM',  'maxTPM',
  650 |         'peakTxnDayChange', 'peakTxnDayUp',
  651 |         'avgTpmChange',     'avgTpmUp',
  652 |         'maxTpmChange',     'maxTpmUp',
  653 |       ];
  654 | 
  655 |       logger.info('Asserting: all expected peakMetrics fields are present');
  656 |       for (const field of expectedFields) {
> 657 |         expect(pm, `peakMetrics.${field} must be present`).toHaveProperty(field);
      |                                                            ^ Error: peakMetrics.peakTxnDayUp must be present
  658 |       }
  659 | 
  660 |       allure.parameter('peakMetrics fields present', expectedFields.join(', '));
  661 |       logger.pass('All assertions passed');
  662 |     });
  663 | 
  664 |     await logger.step('Step 5 â€" Validate boolean "Up" flags are correct type', async () => {
  665 |       const d  = res!.body?.data ?? res!.body;
  666 |       const pm = d.peakMetrics as Record<string, unknown>;
  667 | 
  668 |       logger.info('Asserting: peakTxnDayUp, avgTpmUp, and maxTpmUp are booleans');
  669 |       for (const field of ['peakTxnDayUp', 'avgTpmUp', 'maxTpmUp'] as const) {
  670 |         expect(typeof pm[field], `peakMetrics.${field} must be a boolean`).toBe('boolean');
  671 |         allure.parameter(`peakMetrics.${field}`, String(pm[field]));
  672 |       }
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
```