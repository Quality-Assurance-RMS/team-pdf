# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-006 â€” BUG-REVT-05: change field must reflect real period-over-period delta, not always "+0%"
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:506:7

# Error details

```
Error: [BUG-REVT-05b] A 0% change must not have "+" prefix. Got "+0%", expected "0%"

expect(received).not.toBe(expected) // Object.is equality

Expected: not "+0%"
```

# Test source

```ts
  477 |       ).toBe(0);
  478 |       logger.pass('All assertions passed');
  479 |     });
  480 | 
  481 |     await logger.step('Step 4 â€” Labels must contain only valid month abbreviations', async () => {
  482 |       const labels = (res!.body.data as RevenueTrendData).labels;
  483 |       const VALID_MONTHS = new Set(['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec']);
  484 | 
  485 |       for (const label of labels) {
  486 |         // Accept "Mon" or "Mon YYYY" format
  487 |         const monthPart = label.split(' ')[0];
  488 |         logger.info('Asserting: response structure and values');
  489 |         expect(VALID_MONTHS.has(monthPart),
  490 |           `Label "${label}" must be a valid month abbreviation (Janâ€“Dec)`,
  491 |         ).toBe(true);
  492 |       }
  493 |       logger.pass('All assertions passed');
  494 |     });
  495 | 
  496 |     await logger.step('Step 5 â€” Validate array length is within reasonable range (12â€“13 months)', async () => {
  497 |       const labels = (res!.body.data as RevenueTrendData).labels;
  498 |       logger.info('Asserting: Validate array length is within reasonable range');
  499 |       expect(labels.length, 'labels must have 12 or 13 entries for a monthly trend').toBeGreaterThanOrEqual(12);
  500 |       expect(labels.length, 'labels must not exceed 13 entries for a monthly trend').toBeLessThanOrEqual(13);
  501 |       logger.pass('All assertions passed');
  502 |     });
  503 |   });
  504 | 
  505 |   // â”€â”€ TC-DREVT-006 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  506 |   test('TC-DREVT-006 â€” BUG-REVT-05: change field must reflect real period-over-period delta, not always "+0%"', async ({ logger }) => {
  507 |     smokeLabels('TC-DREVT-006', 'BUG-REVT-05 â€” change Always "+0%"', 'critical');
  508 | 
  509 |     let res: supertest.Response;
  510 | 
  511 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  512 |       logger.info(`GET ${ENDPOINT}`);
  513 |       res = await get();
  514 |       logger.pass('HTTP ' + res.status + ' received');
  515 |       logResponse('TC-DREVT-006', res, '(change field check)');
  516 |       attachResponse('TC-DREVT-006', res, { bug: 'BUG-REVT-05' });
  517 |     });
  518 | 
  519 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  520 |       logger.info('Asserting: Validate HTTP 200');
  521 |       expect(res!.status).toBe(200);
  522 |       logger.pass('All assertions passed');
  523 |     });
  524 | 
  525 |     await logger.step('Step 3 â€” BUG-REVT-05: change must not be "+0%" for every single metric', async () => {
  526 |       const summary = (res!.body.data as RevenueTrendData).summary;
  527 | 
  528 |       const allZeroChange = summary.every(s => s.change === '+0%');
  529 |       allure.parameter('All change values', summary.map(s => `${s.label}:${s.change}`).join(', '));
  530 |       allure.parameter('All changes are "+0%"', String(allZeroChange));
  531 | 
  532 |       if (allZeroChange) {
  533 |         flagIssue('TC-DREVT-006', 'BUG-REVT-05',
  534 |           'Every summary item has change="+0%" â€” this is almost certainly a calculation bug. ' +
  535 |           'With 23 929 total transactions visible in the trend, the change from a prior period cannot ' +
  536 |           'realistically be +0% for all three metrics simultaneously. ' +
  537 |           'Possible cause: no prior-period data available, division-by-zero handled as 0%, or the calculation is not implemented.',
  538 |           { summary: summary.map(s => ({ label: s.label, change: s.change, points: s.points })) },
  539 |         );
  540 |       }
  541 | 
  542 |       // At least one metric should have a non-zero change when there is real data
  543 |       logger.info('Asserting: BUG-REVT-05: change must not be');
  544 |       expect.soft(allZeroChange,
  545 |         '[BUG-REVT-05] Not all 3 change values should be "+0%" when real transaction data exists â€” this indicates a calculation bug',
  546 |       ).toBe(false);
  547 |       logger.pass('All assertions passed');
  548 |     });
  549 | 
  550 |     await logger.step('Step 4 â€” change format must match expected pattern N% or +N% or -N%', async () => {
  551 |       const summary = (res!.body.data as RevenueTrendData).summary;
  552 |       const PCT_PATTERN = /^[+-]?\d+(\.\d+)?%$/;
  553 | 
  554 |       for (const item of summary) {
  555 |         const chg = String(item.change ?? '');
  556 |         allure.parameter(`${item.label} change`, chg);
  557 |         logger.info('Asserting: response structure and values');
  558 |         expect(PCT_PATTERN.test(chg),
  559 |           `summary "${item.label}" change="${chg}" must match Â±N% pattern`,
  560 |         ).toBe(true);
  561 |       }
  562 |       logger.pass('All assertions passed');
  563 |     });
  564 | 
  565 |     await logger.step('Step 5 â€” "+0%" prefix is semantically wrong for a zero change', async () => {
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
> 577 |           ).not.toBe('+0%');
      |                 ^ Error: [BUG-REVT-05b] A 0% change must not have "+" prefix. Got "+0%", expected "0%"
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
  666 |       expect(resBase.status).toBe(200);
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
```