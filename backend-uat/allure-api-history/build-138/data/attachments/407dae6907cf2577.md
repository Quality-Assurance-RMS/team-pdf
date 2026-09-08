# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-status-currency.spec.ts >> Backend (Konza) â€" Dashboard Status & Currency >> TC-DSH-005 â€" byMethod: MPESA + CARD entries with all four amount/count fields
- Location: tests/API/backend-tests/backend-dashboard-status-currency.spec.ts:533:7

# Error details

```
Error: byMethod must be an array

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

# Test source

```ts
  459 | 
  460 |         expect(typeof item.label,      `item[${i}].label must be a string`).toBe('string');
  461 |         expect(typeof item.percentage, `item[${i}].percentage must be a number`).toBe('number');
  462 |         expect(item.percentage as number, `item[${i}].percentage must be >= 0`).toBeGreaterThanOrEqual(0);
  463 |         expect(item.percentage as number, `item[${i}].percentage must be <= 100`).toBeLessThanOrEqual(100);
  464 | 
  465 |         // â"€â"€ DASH-ISSUE-2 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  466 |         if (typeof item.amount === 'string') {
  467 |           flagIssue('TC-DSH-004', 'DASH-ISSUE-2',
  468 |             `currencyDistribution[${i}].amount is a locale-formatted string â€" should be a number`,
  469 |             item.amount,
  470 |           );
  471 |         }
  472 |         expect.soft(typeof item.amount,
  473 |           `[DASH-ISSUE-2] currencyDistribution[${i}].amount should be a number, not a string`,
  474 |         ).toBe('number');
  475 | 
  476 |         if (typeof item.count === 'string') {
  477 |           flagIssue('TC-DSH-004', 'DASH-ISSUE-2',
  478 |             `currencyDistribution[${i}].count is a string â€" should be a number`,
  479 |             item.count,
  480 |           );
  481 |         }
  482 |         expect.soft(typeof item.count,
  483 |           `[DASH-ISSUE-2] currencyDistribution[${i}].count should be a number, not a string`,
  484 |         ).toBe('number');
  485 | 
  486 |         // â"€â"€ DASH-ISSUE-6 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  487 |         if (item.color !== null) {
  488 |           flagIssue('TC-DSH-004', 'DASH-ISSUE-6',
  489 |             `currencyDistribution[${i}].color is a UI hex code â€" should not be in API response`,
  490 |             item.color,
  491 |           );
  492 |         }
  493 |         expect.soft(item.color,
  494 |           `[DASH-ISSUE-6] currencyDistribution[${i}].color should not be returned`,
  495 |         ).toBeNull();
  496 | 
  497 |         allure.parameter(`currencyDistribution[${i}].label`,      String(item.label));
  498 |         allure.parameter(`currencyDistribution[${i}].percentage`,  String(item.percentage) + '%');
  499 |         allure.parameter(`currencyDistribution[${i}].amount`,      String(item.amount));
  500 |         allure.parameter(`currencyDistribution[${i}].count`,       String(item.count));
  501 |       }
  502 |       logger.pass('All assertions passed');
  503 |     });
  504 | 
  505 |     await logger.step('Step 5 â€" Validate known currencies present and percentages sum to ~100 %', async () => {
  506 |       const d      = res!.body?.data ?? res!.body;
  507 |       const dist   = d.currencyDistribution as Array<{ label: string; percentage: number }>;
  508 |       const labels = dist.map(c => c.label);
  509 | 
  510 |       logger.info('Asserting: KES and USD are present and percentages sum to ~100%');
  511 |       for (const currency of KNOWN_CURRENCIES) {
  512 |         expect(labels, `"${currency}" must be present in currencyDistribution`).toContain(currency);
  513 |       }
  514 | 
  515 |       const totalPct = dist.reduce((sum, c) => sum + c.percentage, 0);
  516 |       expect(totalPct, 'currencyDistribution percentages must sum to ~100 %').toBeGreaterThanOrEqual(99);
  517 |       expect(totalPct, 'currencyDistribution percentages must sum to ~100 %').toBeLessThanOrEqual(101);
  518 | 
  519 |       const kesEntry = dist.find(c => c.label === 'KES');
  520 |       const usdEntry = dist.find(c => c.label === 'USD');
  521 |       expect(kesEntry?.percentage, 'KES percentage must be > 0').toBeGreaterThan(0);
  522 |       expect(usdEntry?.percentage, 'USD percentage must be > 0').toBeGreaterThan(0);
  523 | 
  524 |       allure.parameter('currencyDistribution labels', labels.join(', '));
  525 |       allure.parameter('total percentage',            String(totalPct.toFixed(1)) + '%');
  526 |       allure.parameter('KES percentage',              String(kesEntry?.percentage ?? 'MISSING') + '%');
  527 |       allure.parameter('USD percentage',              String(usdEntry?.percentage ?? 'MISSING') + '%');
  528 |       logger.pass('All assertions passed');
  529 |     });
  530 |   });
  531 | 
  532 |   // â"€â"€ TC-DSH-005 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  533 |   test('TC-DSH-005 â€" byMethod: MPESA + CARD entries with all four amount/count fields', async ({ logger }) => {
  534 |     backendLabels('TC-DSH-005', 'Status Currency Endpoint', 'byMethod â€" Structure & Field Validation');
  535 | 
  536 |     let res: supertest.Response;
  537 | 
  538 |     await logger.step('Step 1 â€" Build request', async () => {
  539 |       allure.parameter('Base URL', BASE_URL);
  540 |       allure.parameter('Endpoint', ENDPOINT);
  541 |       allure.parameter('Method',   'GET');
  542 |       logger.info('Asserting: request parameters are recorded');
  543 |       logger.pass('All assertions passed');
  544 |     });
  545 | 
  546 |     await logger.step('Step 2 â€" Send GET /status-currency', async () => {
  547 |       logger.info('GET /api/v1/dashboard/status-currency');
  548 |       res = await get();
  549 |       logger.pass('HTTP ' + res.status + ' received');
  550 |       logResponse('TC-DSH-005', {}, res);
  551 |       allure.parameter('HTTP Status', String(res.status));
  552 |     });
  553 | 
  554 |     await logger.step('Step 3 â€" Validate HTTP 200 and byMethod is a non-empty array', async () => {
  555 |       logger.info('Asserting: HTTP 200 and byMethod is a non-empty array');
  556 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  557 |       const d   = res!.body?.data ?? res!.body;
  558 |       const bym = d.byMethod as Array<Record<string, unknown>>;
> 559 |       expect(Array.isArray(bym), 'byMethod must be an array').toBe(true);
      |                                                               ^ Error: byMethod must be an array
  560 |       expect(bym.length,         'byMethod must have at least one entry').toBeGreaterThan(0);
  561 |       allure.parameter('byMethod item count', String(bym.length));
  562 |       logger.pass('All assertions passed');
  563 |     });
  564 | 
  565 |     await logger.step('Step 4 â€" Validate each byMethod item has all required fields', async () => {
  566 |       const d   = res!.body?.data ?? res!.body;
  567 |       const bym = d.byMethod as Array<Record<string, unknown>>;
  568 | 
  569 |       logger.info('Asserting: each byMethod item has method, successKES, successCount, failKES, and failCount fields');
  570 |       for (const [i, item] of bym.entries()) {
  571 |         expect(item, `byMethod[${i}] must have "method" field`).toHaveProperty('method');
  572 |         expect(item, `byMethod[${i}] must have "successKES" field`).toHaveProperty('successKES');
  573 |         expect(item, `byMethod[${i}] must have "successCount" field`).toHaveProperty('successCount');
  574 |         expect(item, `byMethod[${i}] must have "failKES" field`).toHaveProperty('failKES');
  575 |         expect(item, `byMethod[${i}] must have "failCount" field`).toHaveProperty('failCount');
  576 | 
  577 |         expect(typeof item.method, `byMethod[${i}].method must be a string`).toBe('string');
  578 |         expect((item.method as string).length, `byMethod[${i}].method must not be empty`).toBeGreaterThan(0);
  579 | 
  580 |         // â"€â"€ DASH-ISSUE-2 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  581 |         for (const field of ['successKES', 'successCount', 'failKES', 'failCount'] as const) {
  582 |           if (typeof item[field] === 'string') {
  583 |             flagIssue('TC-DSH-005', 'DASH-ISSUE-2',
  584 |               `byMethod[${i}].${field} is a string â€" should be a number for arithmetic`,
  585 |               item[field],
  586 |             );
  587 |           }
  588 |           expect.soft(typeof item[field],
  589 |             `[DASH-ISSUE-2] byMethod[${i}].${field} should be a number, not a string`,
  590 |           ).toBe('number');
  591 |         }
  592 | 
  593 |         allure.parameter(`byMethod[${i}].method`,       String(item.method));
  594 |         allure.parameter(`byMethod[${i}].successKES`,   String(item.successKES));
  595 |         allure.parameter(`byMethod[${i}].successCount`, String(item.successCount));
  596 |         allure.parameter(`byMethod[${i}].failKES`,      String(item.failKES));
  597 |         allure.parameter(`byMethod[${i}].failCount`,    String(item.failCount));
  598 |       }
  599 |       logger.pass('All assertions passed');
  600 |     });
  601 | 
  602 |     await logger.step('Step 5 â€" Validate known payment methods are present', async () => {
  603 |       const d       = res!.body?.data ?? res!.body;
  604 |       const bym     = d.byMethod as Array<{ method: string }>;
  605 |       const methods = bym.map(m => m.method);
  606 | 
  607 |       logger.info('Asserting: MPESA and CARD payment methods are present in byMethod');
  608 |       for (const expected of KNOWN_METHODS) {
  609 |         expect(methods, `"${expected}" must be in byMethod`).toContain(expected);
  610 |       }
  611 | 
  612 |       allure.parameter('byMethod methods', methods.join(', '));
  613 |       allure.parameter('Expected methods', KNOWN_METHODS.join(', ') + ' â€" PASS');
  614 |       logger.pass('All assertions passed');
  615 |     });
  616 |   });
  617 | 
  618 |   // â"€â"€ TC-DSH-006 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  619 |   test('TC-DSH-006 â€" peakMetrics: all nine fields present with correct types', async ({ logger }) => {
  620 |     backendLabels('TC-DSH-006', 'Status Currency Endpoint', 'peakMetrics â€" Field Presence & Type Validation');
  621 | 
  622 |     let res: supertest.Response;
  623 | 
  624 |     await logger.step('Step 1 â€" Build request', async () => {
  625 |       allure.parameter('Base URL', BASE_URL);
  626 |       allure.parameter('Endpoint', ENDPOINT);
  627 |       allure.parameter('Method',   'GET');
  628 |       logger.info('Asserting: request parameters are recorded');
  629 |       logger.pass('All assertions passed');
  630 |     });
  631 | 
  632 |     await logger.step('Step 2 â€" Send GET /status-currency', async () => {
  633 |       logger.info('GET /api/v1/dashboard/status-currency');
  634 |       res = await get();
  635 |       logger.pass('HTTP ' + res.status + ' received');
  636 |       logResponse('TC-DSH-006', {}, res);
  637 |       allure.parameter('HTTP Status', String(res.status));
  638 |     });
  639 | 
  640 |     await logger.step('Step 3 â€" Validate HTTP 200 and peakMetrics is an object', async () => {
  641 |       logger.info('Asserting: HTTP 200 and peakMetrics is a defined non-array object');
  642 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  643 |       const d  = res!.body?.data ?? res!.body;
  644 |       const pm = d.peakMetrics;
  645 |       expect(pm,                      'peakMetrics must be defined').toBeDefined();
  646 |       expect(typeof pm,               'peakMetrics must be an object').toBe('object');
  647 |       expect(Array.isArray(pm),       'peakMetrics must not be an array').toBe(false);
  648 |       logger.pass('All assertions passed');
  649 |     });
  650 | 
  651 |     await logger.step('Step 4 â€" Validate all nine peakMetrics fields are present', async () => {
  652 |       const d  = res!.body?.data ?? res!.body;
  653 |       const pm = d.peakMetrics as Record<string, unknown>;
  654 | 
  655 |       const expectedFields = [
  656 |         'peakTransactionsPerDay', 'peakHourWindow',
  657 |         'avgTPM',  'maxTPM',
  658 |         'peakTxnDayChange', 'peakTxnDayUp',
  659 |         'avgTpmChange',     'avgTpmUp',
```