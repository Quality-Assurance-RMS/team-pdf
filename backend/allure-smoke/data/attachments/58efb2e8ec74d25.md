# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-005 â€” Input validation: invalid period, case sensitivity, error messages
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:357:7

# Error details

```
Error: Error message must be non-empty

expect(received).toBeTruthy()

Received: undefined
```

# Test source

```ts
  271 |       const kes = d.total_revenue_kes;
  272 |       logger.info('Asserting: total_revenue_kes field types and values');
  273 |       expect(typeof kes.amount,   'total_revenue_kes.amount must be a number').toBe('number');
  274 |       expect(kes.amount,          'total_revenue_kes.amount must be >= 0').toBeGreaterThanOrEqual(0);
  275 |       expect(kes.currency,        'total_revenue_kes.currency must be KES').toBe('KES');
  276 |       expect(typeof kes.change_pct,       'change_pct must be a number').toBe('number');
  277 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(kes.change_direction);
  278 |       allure.parameter('KES amount',    String(kes.amount));
  279 |       allure.parameter('KES change_pct', String(kes.change_pct));
  280 |       allure.parameter('KES direction',  kes.change_direction);
  281 |       logger.pass('All assertions passed');
  282 |     });
  283 | 
  284 |     await logger.step('Step 4 â€” Validate total_revenue_usd field types', async () => {
  285 |       const d   = res!.body?.data ?? res!.body;
  286 |       const usd = d.total_revenue_usd;
  287 |       logger.info('Asserting: total_revenue_usd field types and values');
  288 |       expect(typeof usd.amount,   'total_revenue_usd.amount must be a number').toBe('number');
  289 |       expect(usd.amount,          'total_revenue_usd.amount must be >= 0').toBeGreaterThanOrEqual(0);
  290 |       expect(usd.currency,        'total_revenue_usd.currency must be USD').toBe('USD');
  291 |       expect(typeof usd.change_pct,       'change_pct must be a number').toBe('number');
  292 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(usd.change_direction);
  293 |       allure.parameter('USD amount',    String(usd.amount));
  294 |       allure.parameter('USD change_pct', String(usd.change_pct));
  295 |       allure.parameter('USD direction',  usd.change_direction);
  296 |       logger.pass('All assertions passed');
  297 |     });
  298 | 
  299 |     await logger.step('Step 5 â€” Validate total_transactions field types', async () => {
  300 |       const d   = res!.body?.data ?? res!.body;
  301 |       const txn = d.total_transactions;
  302 |       logger.info('Asserting: total_transactions field types and count integrity');
  303 |       expect(typeof txn.count,     'count must be a number').toBe('number');
  304 |       expect(typeof txn.count_kes, 'count_kes must be a number').toBe('number');
  305 |       expect(typeof txn.count_usd, 'count_usd must be a number').toBe('number');
  306 |       expect(txn.count,            'count must be >= 0').toBeGreaterThanOrEqual(0);
  307 |       expect(txn.count_kes + txn.count_usd, 'count_kes + count_usd must equal count').toBe(txn.count);
  308 |       expect(VALID_DIRECTIONS).toContain(txn.change_direction);
  309 |       allure.parameter('txn count',     String(txn.count));
  310 |       allure.parameter('txn count_kes', String(txn.count_kes));
  311 |       allure.parameter('txn count_usd', String(txn.count_usd));
  312 |       logger.pass('All assertions passed');
  313 |     });
  314 | 
  315 |     await logger.step('Step 6 â€” Validate successful_payments and failed_payments field types', async () => {
  316 |       const d    = res!.body?.data ?? res!.body;
  317 |       const succ = d.successful_payments;
  318 |       const fail = d.failed_payments;
  319 |       logger.info('Asserting: successful_payments and failed_payments field types and rate ranges');
  320 |       for (const [label, obj] of [['successful_payments', succ], ['failed_payments', fail]] as [string, Record<string, unknown>][]) {
  321 |         expect(typeof obj.rate_pct,   `${label}.rate_pct must be a number`).toBe('number');
  322 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeGreaterThanOrEqual(0);
  323 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeLessThanOrEqual(100);
  324 |         expect(VALID_DIRECTIONS, `${label}.change_direction must be up/down/flat`).toContain(obj.change_direction);
  325 |         allure.parameter(`${label}.rate_pct`, String(obj.rate_pct) + '%');
  326 |       }
  327 |       logger.pass('All assertions passed');
  328 |     });
  329 |   });
  330 | 
  331 |   // â”€â”€ TC-DSUM-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  332 |   test('TC-DSUM-004 â€” Field types: amounts are numbers, rates are numbers', async ({ logger }) => {
  333 |     smokeLabels('TC-DSUM-004', 'Field Type Validation â€” All Periods', 'normal');
  334 | 
  335 |     await logger.step('Step 1 â€” Validate each period returns numeric fields (not strings)', async () => {
  336 |       for (const period of ['week', 'year', 'last-3-month'] as const) {
  337 |         logger.info(`GET ${ENDPOINT}?period=${period}`);
  338 |         const res = await get({ period });
  339 |         logger.pass('HTTP ' + res.status + ' received');
  340 |         logger.info('Asserting: numeric field types for all key fields');
  341 |         expect(res.status, `period=${period}  -> 200`).toBe(200);
  342 |         const d = res.body?.data ?? res.body;
  343 | 
  344 |         expect(typeof d.total_revenue_kes.amount, `[${period}] KES amount must be number not string`).toBe('number');
  345 |         expect(typeof d.total_revenue_usd.amount, `[${period}] USD amount must be number not string`).toBe('number');
  346 |         expect(typeof d.total_transactions.count, `[${period}] count must be number not string`).toBe('number');
  347 |         expect(typeof d.successful_payments.rate_pct, `[${period}] rate_pct must be number not string`).toBe('number');
  348 | 
  349 |         allure.parameter(`${period} KES amount type`, typeof d.total_revenue_kes.amount);
  350 |         allure.parameter(`${period} success rate`,    String(d.successful_payments.rate_pct) + '%');
  351 |       }
  352 |       logger.pass('All assertions passed');
  353 |     });
  354 |   });
  355 | 
  356 |   // â”€â”€ TC-DSUM-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  357 |   test('TC-DSUM-005 â€” Input validation: invalid period, case sensitivity, error messages', async ({ logger }) => {
  358 |     smokeLabels('TC-DSUM-005', 'Input Validation â€” Period Parameter');
  359 | 
  360 |     let resInvalid: supertest.Response;
  361 |     let resUpper: supertest.Response;
  362 |     let resMixed: supertest.Response;
  363 | 
  364 |     await logger.step('Step 1 â€” period=INVALID_XYZ must return 400', async () => {
  365 |       logger.info(`GET ${ENDPOINT}`);
  366 |       resInvalid = await get({ period: 'INVALID_XYZ' });
  367 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  368 |       logResponse('TC-DSUM-005', { period: 'INVALID_XYZ' }, resInvalid);
  369 |       logger.info('Asserting: response structure and values');
  370 |       expect(resInvalid.status, 'Invalid period must return 400').toBe(400);
> 371 |       expect(resInvalid.body.message ?? resInvalid.body.error, 'Error message must be non-empty').toBeTruthy();
      |                                                                                                   ^ Error: Error message must be non-empty
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
  471 |       expect(msg.toLowerCase(), 'Error must mention from_date and to_date').toMatch(/from_date|to_date/i);
```