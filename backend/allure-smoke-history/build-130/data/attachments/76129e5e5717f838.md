# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-status-currency.spec.ts >> Smoke â€" Dashboard Status & Currency >> TC-DSH-005 â€" byMethod: MPESA + CARD entries with all four amount/count fields
- Location: tests/API/smoke/smoke-dashboard-status-currency.spec.ts:525:7

# Error details

```
Error: byMethod must be an array

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

# Test source

```ts
  451 | 
  452 |         expect(typeof item.label,      `item[${i}].label must be a string`).toBe('string');
  453 |         expect(typeof item.percentage, `item[${i}].percentage must be a number`).toBe('number');
  454 |         expect(item.percentage as number, `item[${i}].percentage must be >= 0`).toBeGreaterThanOrEqual(0);
  455 |         expect(item.percentage as number, `item[${i}].percentage must be <= 100`).toBeLessThanOrEqual(100);
  456 | 
  457 |         // â"€â"€ DASH-ISSUE-2 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  458 |         if (typeof item.amount === 'string') {
  459 |           flagIssue('TC-DSH-004', 'DASH-ISSUE-2',
  460 |             `currencyDistribution[${i}].amount is a locale-formatted string â€" should be a number`,
  461 |             item.amount,
  462 |           );
  463 |         }
  464 |         expect.soft(typeof item.amount,
  465 |           `[DASH-ISSUE-2] currencyDistribution[${i}].amount should be a number, not a string`,
  466 |         ).toBe('number');
  467 | 
  468 |         if (typeof item.count === 'string') {
  469 |           flagIssue('TC-DSH-004', 'DASH-ISSUE-2',
  470 |             `currencyDistribution[${i}].count is a string â€" should be a number`,
  471 |             item.count,
  472 |           );
  473 |         }
  474 |         expect.soft(typeof item.count,
  475 |           `[DASH-ISSUE-2] currencyDistribution[${i}].count should be a number, not a string`,
  476 |         ).toBe('number');
  477 | 
  478 |         // â"€â"€ DASH-ISSUE-6 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  479 |         if (item.color !== null) {
  480 |           flagIssue('TC-DSH-004', 'DASH-ISSUE-6',
  481 |             `currencyDistribution[${i}].color is a UI hex code â€" should not be in API response`,
  482 |             item.color,
  483 |           );
  484 |         }
  485 |         expect.soft(item.color,
  486 |           `[DASH-ISSUE-6] currencyDistribution[${i}].color should not be returned`,
  487 |         ).toBeNull();
  488 | 
  489 |         allure.parameter(`currencyDistribution[${i}].label`,      String(item.label));
  490 |         allure.parameter(`currencyDistribution[${i}].percentage`,  String(item.percentage) + '%');
  491 |         allure.parameter(`currencyDistribution[${i}].amount`,      String(item.amount));
  492 |         allure.parameter(`currencyDistribution[${i}].count`,       String(item.count));
  493 |       }
  494 |       logger.pass('All assertions passed');
  495 |     });
  496 | 
  497 |     await logger.step('Step 5 â€" Validate known currencies present and percentages sum to ~100 %', async () => {
  498 |       const d      = res!.body?.data ?? res!.body;
  499 |       const dist   = d.currencyDistribution as Array<{ label: string; percentage: number }>;
  500 |       const labels = dist.map(c => c.label);
  501 | 
  502 |       logger.info('Asserting: KES and USD are present and percentages sum to ~100%');
  503 |       for (const currency of KNOWN_CURRENCIES) {
  504 |         expect(labels, `"${currency}" must be present in currencyDistribution`).toContain(currency);
  505 |       }
  506 | 
  507 |       const totalPct = dist.reduce((sum, c) => sum + c.percentage, 0);
  508 |       expect(totalPct, 'currencyDistribution percentages must sum to ~100 %').toBeGreaterThanOrEqual(99);
  509 |       expect(totalPct, 'currencyDistribution percentages must sum to ~100 %').toBeLessThanOrEqual(101);
  510 | 
  511 |       const kesEntry = dist.find(c => c.label === 'KES');
  512 |       const usdEntry = dist.find(c => c.label === 'USD');
  513 |       expect(kesEntry?.percentage, 'KES percentage must be > 0').toBeGreaterThan(0);
  514 |       expect(usdEntry?.percentage, 'USD percentage must be > 0').toBeGreaterThan(0);
  515 | 
  516 |       allure.parameter('currencyDistribution labels', labels.join(', '));
  517 |       allure.parameter('total percentage',            String(totalPct.toFixed(1)) + '%');
  518 |       allure.parameter('KES percentage',              String(kesEntry?.percentage ?? 'MISSING') + '%');
  519 |       allure.parameter('USD percentage',              String(usdEntry?.percentage ?? 'MISSING') + '%');
  520 |       logger.pass('All assertions passed');
  521 |     });
  522 |   });
  523 | 
  524 |   // â"€â"€ TC-DSH-005 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  525 |   test('TC-DSH-005 â€" byMethod: MPESA + CARD entries with all four amount/count fields', async ({ logger }) => {
  526 |     smokeLabels('TC-DSH-005', 'Status Currency Endpoint', 'byMethod â€" Structure & Field Validation');
  527 | 
  528 |     let res: supertest.Response;
  529 | 
  530 |     await logger.step('Step 1 â€" Build request', async () => {
  531 |       allure.parameter('Base URL', BASE_URL);
  532 |       allure.parameter('Endpoint', ENDPOINT);
  533 |       allure.parameter('Method',   'GET');
  534 |       logger.info('Asserting: request parameters are recorded');
  535 |       logger.pass('All assertions passed');
  536 |     });
  537 | 
  538 |     await logger.step('Step 2 â€" Send GET /status-currency', async () => {
  539 |       logger.info('GET /api/v1/dashboard/status-currency');
  540 |       res = await get();
  541 |       logger.pass('HTTP ' + res.status + ' received');
  542 |       logResponse('TC-DSH-005', {}, res);
  543 |       allure.parameter('HTTP Status', String(res.status));
  544 |     });
  545 | 
  546 |     await logger.step('Step 3 â€" Validate HTTP 200 and byMethod is a non-empty array', async () => {
  547 |       logger.info('Asserting: HTTP 200 and byMethod is a non-empty array');
  548 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  549 |       const d   = res!.body?.data ?? res!.body;
  550 |       const bym = d.byMethod as Array<Record<string, unknown>>;
> 551 |       expect(Array.isArray(bym), 'byMethod must be an array').toBe(true);
      |                                                               ^ Error: byMethod must be an array
  552 |       expect(bym.length,         'byMethod must have at least one entry').toBeGreaterThan(0);
  553 |       allure.parameter('byMethod item count', String(bym.length));
  554 |       logger.pass('All assertions passed');
  555 |     });
  556 | 
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
```