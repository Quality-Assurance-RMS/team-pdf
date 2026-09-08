# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-006 â€” Date validation: inverted range  -> 400, missing dates  -> 400
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:469:7

# Error details

```
Error: Error must mention from_date and to_date

expect(received).toMatch(expected)

Expected pattern: /from_date|to_date/i
Received string:  ""
```

# Test source

```ts
  379 |       expect(resInvalid.body.message ?? resInvalid.body.error, 'Error message must be non-empty').toBeTruthy();
  380 |       allure.parameter('invalid period status', String(resInvalid.status));
  381 |       logger.pass('All assertions passed');
  382 |     });
  383 | 
  384 |     await logger.step('Step 2 â€” BUG-SUM-11: error message must list ALL valid period values', async () => {
  385 |       const errorMsg = String(resInvalid!.body.message ?? '').toLowerCase();
  386 |       const missingValues: string[] = [];
  387 | 
  388 |       for (const v of ['today', 'week', 'month', 'last-3-month', 'year', 'custom']) {
  389 |         if (!errorMsg.includes(v)) missingValues.push(v);
  390 |       }
  391 | 
  392 |       if (missingValues.length > 0) {
  393 |         flagIssue('TC-DSUM-005', 'BUG-SUM-11',
  394 |           `Invalid period error message omits valid values: [${missingValues.join(', ')}]. Actual message: "${resInvalid!.body.message}"`,
  395 |           { message: resInvalid!.body.message, missingValues },
  396 |         );
  397 |       }
  398 |       logger.info('Asserting: BUG-SUM-11: error message must list ALL valid per');
  399 |       expect.soft(missingValues.length,
  400 |         `[BUG-SUM-11] Error message must list all valid periods â€” missing: [${missingValues.join(', ')}]`,
  401 |       ).toBe(0);
  402 | 
  403 |       allure.parameter('Error message',         String(resInvalid!.body.message));
  404 |       allure.parameter('Missing from message',  missingValues.join(', ') || 'none');
  405 |       logger.pass('All assertions passed');
  406 |     });
  407 | 
  408 |     await logger.step('Step 3 â€” BUG-SUM-10: period=WEEK (uppercase) should be accepted (case-insensitive)', async () => {
  409 |       logger.info(`GET ${ENDPOINT}`);
  410 |       resUpper = await get({ period: 'WEEK' });
  411 |       logger.pass('HTTP ' + resUpper.status + ' received');
  412 |       logResponse('TC-DSUM-005', { period: 'WEEK' }, resUpper);
  413 | 
  414 |       if (resUpper.status === 400) {
  415 |         flagIssue('TC-DSUM-005', 'BUG-SUM-10',
  416 |           'period=WEEK (uppercase) returns 400 â€” API is case-sensitive with no documentation. Should accept case-insensitively.',
  417 |           { status: resUpper.status },
  418 |         );
  419 |       }
  420 |       logger.info('Asserting: BUG-SUM-10: period=WEEK (uppercase) should be acc');
  421 |       expect.soft(resUpper.status,
  422 |         '[BUG-SUM-10] period=WEEK should return 200 â€” period values should be case-insensitive',
  423 |       ).toBe(200);
  424 | 
  425 |       allure.parameter('WEEK (uppercase) status', String(resUpper.status) + (resUpper.status === 400 ? ' âš  BUG-SUM-10' : ''));
  426 |       logger.pass('All assertions passed');
  427 |     });
  428 | 
  429 |     await logger.step('Step 4 â€” BUG-SUM-10: period=Week (mixed case) should be accepted', async () => {
  430 |       logger.info(`GET ${ENDPOINT}`);
  431 |       resMixed = await get({ period: 'Week' });
  432 |       logger.pass('HTTP ' + resMixed.status + ' received');
  433 |       if (resMixed.status === 400) {
  434 |         flagIssue('TC-DSUM-005', 'BUG-SUM-10',
  435 |           'period=Week (mixed case) returns 400 â€” case-sensitivity bug',
  436 |           { status: resMixed.status },
  437 |         );
  438 |       }
  439 |       logger.info('Asserting: BUG-SUM-10: period=Week (mixed case) should be ac');
  440 |       expect.soft(resMixed.status,
  441 |         '[BUG-SUM-10] period=Week (mixed case) should return 200',
  442 |       ).toBe(200);
  443 |       allure.parameter('Week (mixed case) status', String(resMixed.status) + (resMixed.status === 400 ? ' âš  BUG-SUM-10' : ''));
  444 |       logger.pass('All assertions passed');
  445 |     });
  446 | 
  447 |     await logger.step('Step 5 â€” BUG-SUM-09: period= (empty string) should return 400 or documented default', async () => {
  448 |       logger.info(`GET ${ENDPOINT}`);
  449 |       const resEmpty = await get({ period: '' });
  450 |       logger.pass('HTTP ' + resEmpty.status + ' received');
  451 |       logResponse('TC-DSUM-005', { period: '' }, resEmpty);
  452 | 
  453 |       if (resEmpty.status === 200) {
  454 |         flagIssue('TC-DSUM-005', 'BUG-SUM-09',
  455 |           'period= (empty string) returns 200 â€” undefined default behaviour. Should return 400 or document the default.',
  456 |           { status: resEmpty.status },
  457 |         );
  458 |       }
  459 |       logger.info('Asserting: BUG-SUM-09: period= (empty string) should return');
  460 |       expect.soft(resEmpty.status,
  461 |         '[BUG-SUM-09] period= empty string should return 400, not silently use an undocumented default',
  462 |       ).toBe(400);
  463 |       allure.parameter('Empty period status', String(resEmpty.status) + (resEmpty.status === 200 ? ' âš  BUG-SUM-09' : ''));
  464 |       logger.pass('All assertions passed');
  465 |     });
  466 |   });
  467 | 
  468 |   // â”€â”€ TC-DSUM-006 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  469 |   test('TC-DSUM-006 â€” Date validation: inverted range  -> 400, missing dates  -> 400', async ({ logger }) => {
  470 |     backendLabels('TC-DSUM-006', 'Date Validation â€” Required Fields and Range Order');
  471 | 
  472 |     await logger.step('Step 1 â€” period=custom missing both dates  -> 400', async () => {
  473 |       logger.info(`GET ${ENDPOINT}`);
  474 |       const res = await get({ period: 'custom' });
  475 |       logger.pass('HTTP ' + res.status + ' received');
  476 |       logger.info('Asserting: response structure and values');
  477 |       expect(res.status, 'Missing dates must return 400').toBe(400);
  478 |       const msg = String(res.body.message ?? '');
> 479 |       expect(msg.toLowerCase(), 'Error must mention from_date and to_date').toMatch(/from_date|to_date/i);
      |                                                                             ^ Error: Error must mention from_date and to_date
  480 |       allure.parameter('Missing both dates status', String(res.status));
  481 |       logger.pass('All assertions passed');
  482 |     });
  483 | 
  484 |     await logger.step('Step 2 â€” period=custom from_date only  -> 400', async () => {
  485 |       logger.info(`GET ${ENDPOINT}`);
  486 |       const res = await get({ period: 'custom', from_date: '2026-01-01' });
  487 |       logger.pass('HTTP ' + res.status + ' received');
  488 |       logger.info('Asserting: response structure and values');
  489 |       expect(res.status, 'Missing to_date must return 400').toBe(400);
  490 |       allure.parameter('Missing to_date status', String(res.status));
  491 |       logger.pass('All assertions passed');
  492 |     });
  493 | 
  494 |     await logger.step('Step 3 â€” period=custom to_date only  -> 400', async () => {
  495 |       logger.info(`GET ${ENDPOINT}`);
  496 |       const res = await get({ period: 'custom', to_date: '2026-05-31' });
  497 |       logger.pass('HTTP ' + res.status + ' received');
  498 |       logger.info('Asserting: response structure and values');
  499 |       expect(res.status, 'Missing from_date must return 400').toBe(400);
  500 |       allure.parameter('Missing from_date status', String(res.status));
  501 |       logger.pass('All assertions passed');
  502 |     });
  503 | 
  504 |     await logger.step('Step 4 â€” Inverted range (from > to)  -> 400 with clear message', async () => {
  505 |       logger.info(`GET ${ENDPOINT}`);
  506 |       const res = await get({ period: 'custom', from_date: '2026-12-31', to_date: '2026-01-01' });
  507 |       logger.pass('HTTP ' + res.status + ' received');
  508 |       logResponse('TC-DSUM-006', { period: 'custom', from_date: '2026-12-31', to_date: '2026-01-01' }, res);
  509 |       logger.info('Asserting: response structure and values');
  510 |       expect(res.status, 'Inverted date range must return 400').toBe(400);
  511 |       const msg = String(res.body.message ?? '');
  512 |       expect(msg.length, 'Error message must be non-empty for inverted range').toBeGreaterThan(0);
  513 |       allure.parameter('Inverted range status',  String(res.status));
  514 |       allure.parameter('Inverted range message', msg);
  515 |       logger.pass('All assertions passed');
  516 |     });
  517 | 
  518 |     await logger.step('Step 5 â€” Valid custom range returns 200 with matching date fields', async () => {
  519 |       logger.info(`GET ${ENDPOINT}`);
  520 |       const res = await get({ period: 'custom', from_date: '2026-01-01', to_date: '2026-05-31' });
  521 |       logger.pass('HTTP ' + res.status + ' received');
  522 |       logResponse('TC-DSUM-006', { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-31' }, res);
  523 |       logger.info('Asserting: response structure and values');
  524 |       expect(res.status, 'Valid custom range must return 200').toBe(200);
  525 |       const d = res.body?.data ?? res.body;
  526 |       expect(d.date_from, 'date_from must echo the requested from_date').toBe('2026-01-01');
  527 |       expect(d.date_to,   'date_to must echo the requested to_date').toBe('2026-05-31');
  528 |       allure.parameter('Custom range status',    String(res.status));
  529 |       allure.parameter('Returned date_from',     String(d.date_from));
  530 |       allure.parameter('Returned date_to',       String(d.date_to));
  531 |       logger.pass('All assertions passed');
  532 |     });
  533 |   });
  534 | 
  535 |   // â”€â”€ TC-DSUM-007 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  536 |   test('TC-DSUM-007 â€” BUG-SUM-04: single-day range (from=to) must be accepted (validator too strict)', async ({ logger }) => {
  537 |     backendLabels('TC-DSUM-007', 'BUG-SUM-04 â€” Single-Day Range Rejected');
  538 | 
  539 |     let res: supertest.Response;
  540 | 
  541 |     await logger.step('Step 1 â€” Send single-day range from_date=to_date=2026-05-15', async () => {
  542 |       logger.info(`GET ${ENDPOINT}`);
  543 |       res = await get({ period: 'custom', from_date: '2026-05-15', to_date: '2026-05-15' });
  544 |       logger.pass('HTTP ' + res.status + ' received');
  545 |       logResponse('TC-DSUM-007', { period: 'custom', from_date: '2026-05-15', to_date: '2026-05-15' }, res);
  546 |       allure.parameter('Single-day range status', String(res.status));
  547 |     });
  548 | 
  549 |     await logger.step('Step 2 â€” Flag BUG-SUM-04 if 400 is returned', async () => {
  550 |       if (res!.status === 400) {
  551 |         flagIssue('TC-DSUM-007', 'BUG-SUM-04',
  552 |           'Single-day custom range (from_date=to_date) returns 400 "from_date must be before to_date" â€” validator uses strict < instead of <=. Querying one specific day is a valid use case.',
  553 |           { from_date: '2026-05-15', to_date: '2026-05-15', status: res!.status, message: res!.body.message },
  554 |         );
  555 |       }
  556 |       logger.info('Asserting: Flag BUG-SUM-04 if 400 is returned');
  557 |       expect.soft(res!.status,
  558 |         '[BUG-SUM-04] Single-day range (from_date = to_date) must return 200 â€” equal dates are a valid range',
  559 |       ).toBe(200);
  560 |       logger.pass('All assertions passed');
  561 |     });
  562 | 
  563 |     await logger.step('Step 3 â€” Also test adjacent single-day range (from < to by 1 day) works fine', async () => {
  564 |       logger.info(`GET ${ENDPOINT}`);
  565 |       const res2 = await get({ period: 'custom', from_date: '2026-05-14', to_date: '2026-05-15' });
  566 |       logger.pass('HTTP ' + res2.status + ' received');
  567 |       logger.info('Asserting: response structure and values');
  568 |       expect(res2.status, 'Two-day range must return 200').toBe(200);
  569 |       allure.parameter('Two-day range status', String(res2.status));
  570 |       logger.pass('All assertions passed');
  571 |     });
  572 |   });
  573 | 
  574 |   // â”€â”€ TC-DSUM-008 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  575 |   test('TC-DSUM-008 â€” BUG-SUM-02/03: impossible dates and wrong date format crash with 500', async ({ logger }) => {
  576 |     backendLabels('TC-DSUM-008', 'BUG-SUM-02/03 â€” Date Format & Impossible Date Crashes');
  577 | 
  578 |     const crashCases: Array<{ label: string; params: Record<string, string>; bugId: string; reason: string }> = [
  579 |       {
```