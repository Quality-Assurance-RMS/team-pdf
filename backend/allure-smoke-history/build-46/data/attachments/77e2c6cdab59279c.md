# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-008 â€” BUG-REVT-07: all query parameters (?period=, ?startDate=, etc.) silently ignored
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:654:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 503
```

# Test source

```ts
  566 |       const summary = (res!.body.data as RevenueTrendData).summary;
  567 |       for (const item of summary) {
  568 |         if (item.change === '+0%') {
  569 |           flagIssue('TC-DREVT-006', 'BUG-REVT-05b',
  570 |             `summary "${item.label}" change="+0%" â€” a zero change is neutral and should not have a "+" prefix. ` +
  571 |             'Expected "0%" (no sign) or "0.0%" â€” the "+" is semantically misleading.',
  572 |             { label: item.label, change: item.change, expected: '"0%" or "0.0%"' },
  573 |           );
  574 |           logger.info('Asserting: response structure and values');
  575 |           expect.soft(item.change,
  576 |             `[BUG-REVT-05b] A 0% change must not have "+" prefix. Got "${item.change}", expected "0%"`,
  577 |           ).not.toBe('+0%');
  578 |         }
  579 |       }
  580 |       logger.pass('All assertions passed');
  581 |     });
  582 |   });
  583 | 
  584 |   // â”€â”€ TC-DREVT-007 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  585 |   test('TC-DREVT-007 â€” BUG-REVT-06: isPostive for "Failed" must not be false when change is "+0%"', async ({ logger }) => {
  586 |     smokeLabels('TC-DREVT-007', 'BUG-REVT-06 â€” isPostive vs change Contradiction for "Failed"', 'normal');
  587 | 
  588 |     let res: supertest.Response;
  589 | 
  590 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  591 |       logger.info(`GET ${ENDPOINT}`);
  592 |       res = await get();
  593 |       logger.pass('HTTP ' + res.status + ' received');
  594 |       attachResponse('TC-DREVT-007', res, { bug: 'BUG-REVT-06' });
  595 |     });
  596 | 
  597 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  598 |       logger.info('Asserting: Validate HTTP 200');
  599 |       expect(res!.status).toBe(200);
  600 |       logger.pass('All assertions passed');
  601 |     });
  602 | 
  603 |     await logger.step('Step 3 â€” BUG-REVT-06: each summary item: isPostive must agree with change direction', async () => {
  604 |       const summary = (res!.body.data as RevenueTrendData).summary;
  605 | 
  606 |       for (const item of summary) {
  607 |         const chg      = String(item.change ?? '');
  608 |         const isPos    = (item as unknown as Record<string, unknown>)['isPostive'];
  609 |         const chgValue = parseFloat(chg.replace(/[+%]/g, ''));
  610 | 
  611 |         allure.parameter(`${item.label} change`, chg);
  612 |         allure.parameter(`${item.label} isPostive`, String(isPos));
  613 | 
  614 |         // Case 1: change is "+0%" or "0%" â€” neutral; isPostive=false is wrong
  615 |         if (chgValue === 0 && isPos === false) {
  616 |           flagIssue('TC-DREVT-007', 'BUG-REVT-06',
  617 |             `summary "${item.label}" has isPostive=false but change="${chg}" (zero change is neutral, not negative). ` +
  618 |             'isPostive should be null or true for a 0% change.',
  619 |             { label: item.label, isPostive: isPos, change: chg },
  620 |           );
  621 |           logger.info('Asserting: BUG-REVT-06: each summary item: isPostive must ag');
  622 |           expect.soft(isPos,
  623 |             `[BUG-REVT-06] "${item.label}": isPostive=false when change="${chg}" â€” zero change is neutral, not negative`,
  624 |           ).not.toBe(false);
  625 |         }
  626 | 
  627 |         // Case 2: negative change â€” isPostive must be false
  628 |         if (chgValue < 0 && isPos !== false) {
  629 |           flagIssue('TC-DREVT-007', 'BUG-REVT-06',
  630 |             `summary "${item.label}" has isPostive=${isPos} but change="${chg}" â€” a negative change must be isPostive=false`,
  631 |             { label: item.label, isPostive: isPos, change: chg },
  632 |           );
  633 |           expect.soft(isPos,
  634 |             `[BUG-REVT-06] "${item.label}": negative change="${chg}" must have isPostive=false`,
  635 |           ).toBe(false);
  636 |         }
  637 | 
  638 |         // Case 3: positive change â€” isPostive must be true
  639 |         if (chgValue > 0 && isPos !== true) {
  640 |           flagIssue('TC-DREVT-007', 'BUG-REVT-06',
  641 |             `summary "${item.label}" has isPostive=${isPos} but change="${chg}" â€” a positive change must be isPostive=true`,
  642 |             { label: item.label, isPostive: isPos, change: chg },
  643 |           );
  644 |           expect.soft(isPos,
  645 |             `[BUG-REVT-06] "${item.label}": positive change="${chg}" must have isPostive=true`,
  646 |           ).toBe(true);
  647 |         }
  648 |       }
  649 |       logger.pass('All assertions passed');
  650 |     });
  651 |   });
  652 | 
  653 |   // â”€â”€ TC-DREVT-008 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  654 |   test('TC-DREVT-008 â€” BUG-REVT-07: all query parameters (?period=, ?startDate=, etc.) silently ignored', async ({ logger }) => {
  655 |     smokeLabels('TC-DREVT-008', 'BUG-REVT-07 â€” Query Params Silently Ignored', 'normal');
  656 | 
  657 |     let resBase:    supertest.Response;
  658 |     let resWeekly:  supertest.Response;
  659 |     let resInvalid: supertest.Response;
  660 |     let resDate:    supertest.Response;
  661 | 
  662 |     await logger.step('Step 1 â€” Fetch baseline (no params)', async () => {
  663 |       logger.info(`GET ${ENDPOINT}`);
  664 |       resBase   = await get();
  665 |       logger.pass('HTTP ' + resBase.status + ' received');
> 666 |       expect(resBase.status).toBe(200);
      |                              ^ Error: expect(received).toBe(expected) // Object.is equality
  667 |       logger.pass('All assertions passed');
  668 |     });
  669 | 
  670 |     await logger.step('Step 2 â€” Fetch with period=weekly', async () => {
  671 |       logger.info(`GET ${ENDPOINT}`);
  672 |       resWeekly = await get('period=weekly');
  673 |       logger.pass('HTTP ' + resWeekly.status + ' received');
  674 |       allure.parameter('?period=weekly status', String(resWeekly.status));
  675 |     });
  676 | 
  677 |     await logger.step('Step 3 â€” Fetch with period=invalid_value', async () => {
  678 |       logger.info(`GET ${ENDPOINT}`);
  679 |       resInvalid = await get('period=invalid_value');
  680 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  681 |       allure.parameter('?period=invalid_value status', String(resInvalid.status));
  682 |     });
  683 | 
  684 |     await logger.step('Step 4 â€” Fetch with startDate/endDate', async () => {
  685 |       logger.info(`GET ${ENDPOINT}`);
  686 |       resDate = await get('startDate=2026-01-01&endDate=2026-06-01');
  687 |       logger.pass('HTTP ' + resDate.status + ' received');
  688 |       allure.parameter('?startDate/endDate status', String(resDate.status));
  689 |     });
  690 | 
  691 |     await logger.step('Step 5 â€” BUG-REVT-07a: ?period=invalid_value must return 400, not 200', async () => {
  692 |       if (resInvalid!.status === 200) {
  693 |         flagIssue('TC-DREVT-008', 'BUG-REVT-07a',
  694 |           '?period=invalid_value returns 200 OK â€” invalid parameter values must return 400 Bad Request. ' +
  695 |           'Silently accepting any value gives clients no feedback that their request was malformed.',
  696 |           { actual_status: 200, query: 'period=invalid_value', expected_status: 400 },
  697 |         );
  698 |       }
  699 |       logger.info('Asserting: BUG-REVT-07a: ?period=invalid_value must return 4');
  700 |       expect.soft(resInvalid!.status,
  701 |         '[BUG-REVT-07a] Invalid ?period= value must return 400 Bad Request, not 200',
  702 |       ).toBe(400);
  703 |       logger.pass('All assertions passed');
  704 |     });
  705 | 
  706 |     await logger.step('Step 6 â€” BUG-REVT-07b: ?period=weekly must return different data than no-period (monthly)', async () => {
  707 |       // If period param is honoured, weekly data will have different labels and data arrays
  708 |       const baseBody    = JSON.stringify(resBase!.body);
  709 |       const weeklyBody  = JSON.stringify(resWeekly!.body);
  710 |       const identical   = baseBody === weeklyBody;
  711 | 
  712 |       allure.parameter('baseline === ?period=weekly', String(identical));
  713 | 
  714 |       if (identical && resWeekly!.status === 200) {
  715 |         flagIssue('TC-DREVT-008', 'BUG-REVT-07b',
  716 |           '?period=weekly returns IDENTICAL response to no-param call â€” the period filter is silently ignored. ' +
  717 |           'A weekly trend should show daily/weekly bucket labels (e.g. "Week 1", "Mon 26 May") not monthly "Jun, Julâ€¦".',
  718 |           { actual: 'identical responses', expected: 'different label granularity for weekly vs monthly periods' },
  719 |         );
  720 |       }
  721 | 
  722 |       logger.info('Asserting: BUG-REVT-07b: ?period=weekly must return differen');
  723 |       expect.soft(identical,
  724 |         '[BUG-REVT-07b] ?period=weekly must return different data from the default (monthly) view',
  725 |       ).toBe(false);
  726 |       logger.pass('All assertions passed');
  727 |     });
  728 | 
  729 |     await logger.step('Step 7 â€” BUG-REVT-07c: ?startDate/endDate must filter data range', async () => {
  730 |       const baseBody = JSON.stringify(resBase!.body);
  731 |       const dateBody = JSON.stringify(resDate!.body);
  732 |       const identical = baseBody === dateBody;
  733 | 
  734 |       allure.parameter('baseline === ?startDate/endDate', String(identical));
  735 | 
  736 |       if (identical && resDate!.status === 200) {
  737 |         flagIssue('TC-DREVT-008', 'BUG-REVT-07c',
  738 |           '?startDate=2026-01-01&endDate=2026-06-01 returns IDENTICAL response to no-param call â€” date range filter is silently ignored.',
  739 |           { actual: 'identical responses', expected: 'data filtered to Janâ€“Jun 2026 range only' },
  740 |         );
  741 |       }
  742 | 
  743 |       logger.info('Asserting: BUG-REVT-07c: ?startDate/endDate must filter data');
  744 |       expect.soft(identical,
  745 |         '[BUG-REVT-07c] ?startDate/endDate must filter the returned data range',
  746 |       ).toBe(false);
  747 |       logger.pass('All assertions passed');
  748 |     });
  749 | 
  750 |     attachResponse('TC-DREVT-008 (base)',    resBase!,    { query: '(none)',                         bug: 'BUG-REVT-07' });
  751 |     attachResponse('TC-DREVT-008 (weekly)',  resWeekly!,  { query: 'period=weekly',                  bug: 'BUG-REVT-07' });
  752 |     attachResponse('TC-DREVT-008 (invalid)', resInvalid!, { query: 'period=invalid_value',           bug: 'BUG-REVT-07' });
  753 |     attachResponse('TC-DREVT-008 (dates)',   resDate!,    { query: 'startDate=2026-01-01&endDate=2026-06-01', bug: 'BUG-REVT-07' });
  754 |   });
  755 | 
  756 |   // â”€â”€ TC-DREVT-009 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  757 |   test('TC-DREVT-009 â€” BUG-REVT-08: Wrong HTTP methods must return 405, not 500', async ({ logger }) => {
  758 |     smokeLabels('TC-DREVT-009', 'BUG-REVT-08 â€” HTTP Method Enforcement', 'normal');
  759 | 
  760 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  761 | 
  762 |     for (const method of wrongMethods) {
  763 |       await logger.step(`${method.toUpperCase()} must return 405`, async () => {
  764 |         logger.info(`GET ${ENDPOINT}`);
  765 |         const res = await getWithMethod(method);
  766 |         logger.pass('HTTP ' + res.status + ' received');
```