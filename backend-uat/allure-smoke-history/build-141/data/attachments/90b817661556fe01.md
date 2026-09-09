# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-005 â€” Input validation: invalid period, case sensitivity, error messages
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:365:7

# Error details

```
Error: Error message must be non-empty

expect(received).toBeTruthy()

Received: undefined
```

# Test source

```ts
  279 |       const kes = d.total_revenue_kes;
  280 |       logger.info('Asserting: total_revenue_kes field types and values');
  281 |       expect(typeof kes.amount,   'total_revenue_kes.amount must be a number').toBe('number');
  282 |       expect(kes.amount,          'total_revenue_kes.amount must be >= 0').toBeGreaterThanOrEqual(0);
  283 |       expect(kes.currency,        'total_revenue_kes.currency must be KES').toBe('KES');
  284 |       expect(typeof kes.change_pct,       'change_pct must be a number').toBe('number');
  285 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(kes.change_direction);
  286 |       allure.parameter('KES amount',    String(kes.amount));
  287 |       allure.parameter('KES change_pct', String(kes.change_pct));
  288 |       allure.parameter('KES direction',  kes.change_direction);
  289 |       logger.pass('All assertions passed');
  290 |     });
  291 | 
  292 |     await logger.step('Step 4 â€” Validate total_revenue_usd field types', async () => {
  293 |       const d   = res!.body?.data ?? res!.body;
  294 |       const usd = d.total_revenue_usd;
  295 |       logger.info('Asserting: total_revenue_usd field types and values');
  296 |       expect(typeof usd.amount,   'total_revenue_usd.amount must be a number').toBe('number');
  297 |       expect(usd.amount,          'total_revenue_usd.amount must be >= 0').toBeGreaterThanOrEqual(0);
  298 |       expect(usd.currency,        'total_revenue_usd.currency must be USD').toBe('USD');
  299 |       expect(typeof usd.change_pct,       'change_pct must be a number').toBe('number');
  300 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(usd.change_direction);
  301 |       allure.parameter('USD amount',    String(usd.amount));
  302 |       allure.parameter('USD change_pct', String(usd.change_pct));
  303 |       allure.parameter('USD direction',  usd.change_direction);
  304 |       logger.pass('All assertions passed');
  305 |     });
  306 | 
  307 |     await logger.step('Step 5 â€” Validate total_transactions field types', async () => {
  308 |       const d   = res!.body?.data ?? res!.body;
  309 |       const txn = d.total_transactions;
  310 |       logger.info('Asserting: total_transactions field types and count integrity');
  311 |       expect(typeof txn.count,     'count must be a number').toBe('number');
  312 |       expect(typeof txn.count_kes, 'count_kes must be a number').toBe('number');
  313 |       expect(typeof txn.count_usd, 'count_usd must be a number').toBe('number');
  314 |       expect(txn.count,            'count must be >= 0').toBeGreaterThanOrEqual(0);
  315 |       expect(txn.count_kes + txn.count_usd, 'count_kes + count_usd must equal count').toBe(txn.count);
  316 |       expect(VALID_DIRECTIONS).toContain(txn.change_direction);
  317 |       allure.parameter('txn count',     String(txn.count));
  318 |       allure.parameter('txn count_kes', String(txn.count_kes));
  319 |       allure.parameter('txn count_usd', String(txn.count_usd));
  320 |       logger.pass('All assertions passed');
  321 |     });
  322 | 
  323 |     await logger.step('Step 6 â€” Validate successful_payments and failed_payments field types', async () => {
  324 |       const d    = res!.body?.data ?? res!.body;
  325 |       const succ = d.successful_payments;
  326 |       const fail = d.failed_payments;
  327 |       logger.info('Asserting: successful_payments and failed_payments field types and rate ranges');
  328 |       for (const [label, obj] of [['successful_payments', succ], ['failed_payments', fail]] as [string, Record<string, unknown>][]) {
  329 |         expect(typeof obj.rate_pct,   `${label}.rate_pct must be a number`).toBe('number');
  330 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeGreaterThanOrEqual(0);
  331 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeLessThanOrEqual(100);
  332 |         expect(VALID_DIRECTIONS, `${label}.change_direction must be up/down/flat`).toContain(obj.change_direction);
  333 |         allure.parameter(`${label}.rate_pct`, String(obj.rate_pct) + '%');
  334 |       }
  335 |       logger.pass('All assertions passed');
  336 |     });
  337 |   });
  338 | 
  339 |   // â”€â”€ TC-DSUM-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  340 |   test('TC-DSUM-004 â€” Field types: amounts are numbers, rates are numbers', async ({ logger }) => {
  341 |     backendLabels('TC-DSUM-004', 'Field Type Validation â€” All Periods', 'normal');
  342 | 
  343 |     await logger.step('Step 1 â€” Validate each period returns numeric fields (not strings)', async () => {
  344 |       for (const period of ['week', 'year', 'last-3-month'] as const) {
  345 |         logger.info(`GET ${ENDPOINT}?period=${period}`);
  346 |         const res = await get({ period });
  347 |         logger.pass('HTTP ' + res.status + ' received');
  348 |         logger.info('Asserting: numeric field types for all key fields');
  349 |         expect(res.status, `period=${period}  -> 200`).toBe(200);
  350 |         const d = res.body?.data ?? res.body;
  351 | 
  352 |         expect(typeof d.total_revenue_kes.amount, `[${period}] KES amount must be number not string`).toBe('number');
  353 |         expect(typeof d.total_revenue_usd.amount, `[${period}] USD amount must be number not string`).toBe('number');
  354 |         expect(typeof d.total_transactions.count, `[${period}] count must be number not string`).toBe('number');
  355 |         expect(typeof d.successful_payments.rate_pct, `[${period}] rate_pct must be number not string`).toBe('number');
  356 | 
  357 |         allure.parameter(`${period} KES amount type`, typeof d.total_revenue_kes.amount);
  358 |         allure.parameter(`${period} success rate`,    String(d.successful_payments.rate_pct) + '%');
  359 |       }
  360 |       logger.pass('All assertions passed');
  361 |     });
  362 |   });
  363 | 
  364 |   // â”€â”€ TC-DSUM-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  365 |   test('TC-DSUM-005 â€” Input validation: invalid period, case sensitivity, error messages', async ({ logger }) => {
  366 |     backendLabels('TC-DSUM-005', 'Input Validation â€” Period Parameter');
  367 | 
  368 |     let resInvalid: supertest.Response;
  369 |     let resUpper: supertest.Response;
  370 |     let resMixed: supertest.Response;
  371 | 
  372 |     await logger.step('Step 1 â€” period=INVALID_XYZ must return 400', async () => {
  373 |       logger.info(`GET ${ENDPOINT}`);
  374 |       resInvalid = await get({ period: 'INVALID_XYZ' });
  375 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  376 |       logResponse('TC-DSUM-005', { period: 'INVALID_XYZ' }, resInvalid);
  377 |       logger.info('Asserting: response structure and values');
  378 |       expect(resInvalid.status, 'Invalid period must return 400').toBe(400);
> 379 |       expect(resInvalid.body.message ?? resInvalid.body.error, 'Error message must be non-empty').toBeTruthy();
      |                                                                                                   ^ Error: Error message must be non-empty
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
  479 |       expect(msg.toLowerCase(), 'Error must mention from_date and to_date').toMatch(/from_date|to_date/i);
```