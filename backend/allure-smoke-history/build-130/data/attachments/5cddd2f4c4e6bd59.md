# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-006 â€” Date validation: inverted range  -> 400, missing dates  -> 400
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:461:7

# Error details

```
Error: Error must mention from_date and to_date

expect(received).toMatch(expected)

Expected pattern: /from_date|to_date/i
Received string:  ""
```

# Test source

```ts
  371 |       expect(resInvalid.body.message ?? resInvalid.body.error, 'Error message must be non-empty').toBeTruthy();
  372 |       allure.parameter('invalid period status', String(resInvalid.status));
  373 |       logger.pass('All assertions passed');
  374 |     });
  375 | 
  376 |     await logger.step('Step 2 â€” BUG-SUM-11: error message must list ALL valid period values', async () => {
  377 |       const errorMsg = String(resInvalid!.body.message ?? '').toLowerCase();
  378 |       const missingValues: string[] = [];
  379 | 
  380 |       for (const v of ['today', 'week', 'month', 'last-3-month', 'year', 'custom']) {
  381 |         if (!errorMsg.includes(v)) missingValues.push(v);
  382 |       }
  383 | 
  384 |       if (missingValues.length > 0) {
  385 |         flagIssue('TC-DSUM-005', 'BUG-SUM-11',
  386 |           `Invalid period error message omits valid values: [${missingValues.join(', ')}]. Actual message: "${resInvalid!.body.message}"`,
  387 |           { message: resInvalid!.body.message, missingValues },
  388 |         );
  389 |       }
  390 |       logger.info('Asserting: BUG-SUM-11: error message must list ALL valid per');
  391 |       expect.soft(missingValues.length,
  392 |         `[BUG-SUM-11] Error message must list all valid periods â€” missing: [${missingValues.join(', ')}]`,
  393 |       ).toBe(0);
  394 | 
  395 |       allure.parameter('Error message',         String(resInvalid!.body.message));
  396 |       allure.parameter('Missing from message',  missingValues.join(', ') || 'none');
  397 |       logger.pass('All assertions passed');
  398 |     });
  399 | 
  400 |     await logger.step('Step 3 â€” BUG-SUM-10: period=WEEK (uppercase) should be accepted (case-insensitive)', async () => {
  401 |       logger.info(`GET ${ENDPOINT}`);
  402 |       resUpper = await get({ period: 'WEEK' });
  403 |       logger.pass('HTTP ' + resUpper.status + ' received');
  404 |       logResponse('TC-DSUM-005', { period: 'WEEK' }, resUpper);
  405 | 
  406 |       if (resUpper.status === 400) {
  407 |         flagIssue('TC-DSUM-005', 'BUG-SUM-10',
  408 |           'period=WEEK (uppercase) returns 400 â€” API is case-sensitive with no documentation. Should accept case-insensitively.',
  409 |           { status: resUpper.status },
  410 |         );
  411 |       }
  412 |       logger.info('Asserting: BUG-SUM-10: period=WEEK (uppercase) should be acc');
  413 |       expect.soft(resUpper.status,
  414 |         '[BUG-SUM-10] period=WEEK should return 200 â€” period values should be case-insensitive',
  415 |       ).toBe(200);
  416 | 
  417 |       allure.parameter('WEEK (uppercase) status', String(resUpper.status) + (resUpper.status === 400 ? ' âš  BUG-SUM-10' : ''));
  418 |       logger.pass('All assertions passed');
  419 |     });
  420 | 
  421 |     await logger.step('Step 4 â€” BUG-SUM-10: period=Week (mixed case) should be accepted', async () => {
  422 |       logger.info(`GET ${ENDPOINT}`);
  423 |       resMixed = await get({ period: 'Week' });
  424 |       logger.pass('HTTP ' + resMixed.status + ' received');
  425 |       if (resMixed.status === 400) {
  426 |         flagIssue('TC-DSUM-005', 'BUG-SUM-10',
  427 |           'period=Week (mixed case) returns 400 â€” case-sensitivity bug',
  428 |           { status: resMixed.status },
  429 |         );
  430 |       }
  431 |       logger.info('Asserting: BUG-SUM-10: period=Week (mixed case) should be ac');
  432 |       expect.soft(resMixed.status,
  433 |         '[BUG-SUM-10] period=Week (mixed case) should return 200',
  434 |       ).toBe(200);
  435 |       allure.parameter('Week (mixed case) status', String(resMixed.status) + (resMixed.status === 400 ? ' âš  BUG-SUM-10' : ''));
  436 |       logger.pass('All assertions passed');
  437 |     });
  438 | 
  439 |     await logger.step('Step 5 â€” BUG-SUM-09: period= (empty string) should return 400 or documented default', async () => {
  440 |       logger.info(`GET ${ENDPOINT}`);
  441 |       const resEmpty = await get({ period: '' });
  442 |       logger.pass('HTTP ' + resEmpty.status + ' received');
  443 |       logResponse('TC-DSUM-005', { period: '' }, resEmpty);
  444 | 
  445 |       if (resEmpty.status === 200) {
  446 |         flagIssue('TC-DSUM-005', 'BUG-SUM-09',
  447 |           'period= (empty string) returns 200 â€” undefined default behaviour. Should return 400 or document the default.',
  448 |           { status: resEmpty.status },
  449 |         );
  450 |       }
  451 |       logger.info('Asserting: BUG-SUM-09: period= (empty string) should return');
  452 |       expect.soft(resEmpty.status,
  453 |         '[BUG-SUM-09] period= empty string should return 400, not silently use an undocumented default',
  454 |       ).toBe(400);
  455 |       allure.parameter('Empty period status', String(resEmpty.status) + (resEmpty.status === 200 ? ' âš  BUG-SUM-09' : ''));
  456 |       logger.pass('All assertions passed');
  457 |     });
  458 |   });
  459 | 
  460 |   // â”€â”€ TC-DSUM-006 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  461 |   test('TC-DSUM-006 â€” Date validation: inverted range  -> 400, missing dates  -> 400', async ({ logger }) => {
  462 |     smokeLabels('TC-DSUM-006', 'Date Validation â€” Required Fields and Range Order');
  463 | 
  464 |     await logger.step('Step 1 â€” period=custom missing both dates  -> 400', async () => {
  465 |       logger.info(`GET ${ENDPOINT}`);
  466 |       const res = await get({ period: 'custom' });
  467 |       logger.pass('HTTP ' + res.status + ' received');
  468 |       logger.info('Asserting: response structure and values');
  469 |       expect(res.status, 'Missing dates must return 400').toBe(400);
  470 |       const msg = String(res.body.message ?? '');
> 471 |       expect(msg.toLowerCase(), 'Error must mention from_date and to_date').toMatch(/from_date|to_date/i);
      |                                                                             ^ Error: Error must mention from_date and to_date
  472 |       allure.parameter('Missing both dates status', String(res.status));
  473 |       logger.pass('All assertions passed');
  474 |     });
  475 | 
  476 |     await logger.step('Step 2 â€” period=custom from_date only  -> 400', async () => {
  477 |       logger.info(`GET ${ENDPOINT}`);
  478 |       const res = await get({ period: 'custom', from_date: '2026-01-01' });
  479 |       logger.pass('HTTP ' + res.status + ' received');
  480 |       logger.info('Asserting: response structure and values');
  481 |       expect(res.status, 'Missing to_date must return 400').toBe(400);
  482 |       allure.parameter('Missing to_date status', String(res.status));
  483 |       logger.pass('All assertions passed');
  484 |     });
  485 | 
  486 |     await logger.step('Step 3 â€” period=custom to_date only  -> 400', async () => {
  487 |       logger.info(`GET ${ENDPOINT}`);
  488 |       const res = await get({ period: 'custom', to_date: '2026-05-31' });
  489 |       logger.pass('HTTP ' + res.status + ' received');
  490 |       logger.info('Asserting: response structure and values');
  491 |       expect(res.status, 'Missing from_date must return 400').toBe(400);
  492 |       allure.parameter('Missing from_date status', String(res.status));
  493 |       logger.pass('All assertions passed');
  494 |     });
  495 | 
  496 |     await logger.step('Step 4 â€” Inverted range (from > to)  -> 400 with clear message', async () => {
  497 |       logger.info(`GET ${ENDPOINT}`);
  498 |       const res = await get({ period: 'custom', from_date: '2026-12-31', to_date: '2026-01-01' });
  499 |       logger.pass('HTTP ' + res.status + ' received');
  500 |       logResponse('TC-DSUM-006', { period: 'custom', from_date: '2026-12-31', to_date: '2026-01-01' }, res);
  501 |       logger.info('Asserting: response structure and values');
  502 |       expect(res.status, 'Inverted date range must return 400').toBe(400);
  503 |       const msg = String(res.body.message ?? '');
  504 |       expect(msg.length, 'Error message must be non-empty for inverted range').toBeGreaterThan(0);
  505 |       allure.parameter('Inverted range status',  String(res.status));
  506 |       allure.parameter('Inverted range message', msg);
  507 |       logger.pass('All assertions passed');
  508 |     });
  509 | 
  510 |     await logger.step('Step 5 â€” Valid custom range returns 200 with matching date fields', async () => {
  511 |       logger.info(`GET ${ENDPOINT}`);
  512 |       const res = await get({ period: 'custom', from_date: '2026-01-01', to_date: '2026-05-31' });
  513 |       logger.pass('HTTP ' + res.status + ' received');
  514 |       logResponse('TC-DSUM-006', { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-31' }, res);
  515 |       logger.info('Asserting: response structure and values');
  516 |       expect(res.status, 'Valid custom range must return 200').toBe(200);
  517 |       const d = res.body?.data ?? res.body;
  518 |       expect(d.date_from, 'date_from must echo the requested from_date').toBe('2026-01-01');
  519 |       expect(d.date_to,   'date_to must echo the requested to_date').toBe('2026-05-31');
  520 |       allure.parameter('Custom range status',    String(res.status));
  521 |       allure.parameter('Returned date_from',     String(d.date_from));
  522 |       allure.parameter('Returned date_to',       String(d.date_to));
  523 |       logger.pass('All assertions passed');
  524 |     });
  525 |   });
  526 | 
  527 |   // â”€â”€ TC-DSUM-007 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  528 |   test('TC-DSUM-007 â€” BUG-SUM-04: single-day range (from=to) must be accepted (validator too strict)', async ({ logger }) => {
  529 |     smokeLabels('TC-DSUM-007', 'BUG-SUM-04 â€” Single-Day Range Rejected');
  530 | 
  531 |     let res: supertest.Response;
  532 | 
  533 |     await logger.step('Step 1 â€” Send single-day range from_date=to_date=2026-05-15', async () => {
  534 |       logger.info(`GET ${ENDPOINT}`);
  535 |       res = await get({ period: 'custom', from_date: '2026-05-15', to_date: '2026-05-15' });
  536 |       logger.pass('HTTP ' + res.status + ' received');
  537 |       logResponse('TC-DSUM-007', { period: 'custom', from_date: '2026-05-15', to_date: '2026-05-15' }, res);
  538 |       allure.parameter('Single-day range status', String(res.status));
  539 |     });
  540 | 
  541 |     await logger.step('Step 2 â€” Flag BUG-SUM-04 if 400 is returned', async () => {
  542 |       if (res!.status === 400) {
  543 |         flagIssue('TC-DSUM-007', 'BUG-SUM-04',
  544 |           'Single-day custom range (from_date=to_date) returns 400 "from_date must be before to_date" â€” validator uses strict < instead of <=. Querying one specific day is a valid use case.',
  545 |           { from_date: '2026-05-15', to_date: '2026-05-15', status: res!.status, message: res!.body.message },
  546 |         );
  547 |       }
  548 |       logger.info('Asserting: Flag BUG-SUM-04 if 400 is returned');
  549 |       expect.soft(res!.status,
  550 |         '[BUG-SUM-04] Single-day range (from_date = to_date) must return 200 â€” equal dates are a valid range',
  551 |       ).toBe(200);
  552 |       logger.pass('All assertions passed');
  553 |     });
  554 | 
  555 |     await logger.step('Step 3 â€” Also test adjacent single-day range (from < to by 1 day) works fine', async () => {
  556 |       logger.info(`GET ${ENDPOINT}`);
  557 |       const res2 = await get({ period: 'custom', from_date: '2026-05-14', to_date: '2026-05-15' });
  558 |       logger.pass('HTTP ' + res2.status + ' received');
  559 |       logger.info('Asserting: response structure and values');
  560 |       expect(res2.status, 'Two-day range must return 200').toBe(200);
  561 |       allure.parameter('Two-day range status', String(res2.status));
  562 |       logger.pass('All assertions passed');
  563 |     });
  564 |   });
  565 | 
  566 |   // â”€â”€ TC-DSUM-008 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  567 |   test('TC-DSUM-008 â€” BUG-SUM-02/03: impossible dates and wrong date format crash with 500', async ({ logger }) => {
  568 |     smokeLabels('TC-DSUM-008', 'BUG-SUM-02/03 â€” Date Format & Impossible Date Crashes');
  569 | 
  570 |     const crashCases: Array<{ label: string; params: Record<string, string>; bugId: string; reason: string }> = [
  571 |       {
```