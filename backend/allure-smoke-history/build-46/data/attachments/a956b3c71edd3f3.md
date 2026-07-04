# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-monthly-revenue.spec.ts >> Smoke â€” Dashboard Monthly Revenue >> TC-DMREV-004 â€” BUG-MREV-01: year=0 must return 400, not silently default to current year
- Location: tests/API/smoke/smoke-dashboard-monthly-revenue.spec.ts:373:7

# Error details

```
Error: expect(received).toContain(expected) // indexOf

Matcher error: received value must not be null nor undefined

Received has value: undefined
```

# Test source

```ts
  316 |       const res = await get('year=2025');
  317 |       logger.pass('HTTP ' + res.status + ' received');
  318 |       logger.info('Asserting: response structure and values');
  319 |       expect(res.status, 'year=2025 must return 200').toBe(200);
  320 |       expect(res.body.data.year, 'data.year must echo back 2025').toBe(2025);
  321 |       allure.parameter('year=2025 status', String(res.status));
  322 |       allure.parameter('year=2025 total_payments', String(res.body.data?.summary?.total_payments));
  323 |       logger.pass('All assertions passed');
  324 |     });
  325 | 
  326 |     await logger.step('Step 3 â€” No year param defaults to current year', async () => {
  327 |       logger.info(`GET ${ENDPOINT}`);
  328 |       const res = await get();
  329 |       logger.pass('HTTP ' + res.status + ' received');
  330 |       logger.info('Asserting: response structure and values');
  331 |       expect(res.status).toBe(200);
  332 |       expect(res.body.data.year).toBe(new Date().getFullYear());
  333 |       allure.parameter('No-year param â€” returned year', String(res.body.data.year));
  334 |       logger.pass('All assertions passed');
  335 |     });
  336 | 
  337 |     await logger.step('Step 4 â€” year=invalid must return 400', async () => {
  338 |       logger.info(`GET ${ENDPOINT}`);
  339 |       const res = await get('year=invalid');
  340 |       logger.pass('HTTP ' + res.status + ' received');
  341 |       logger.info('Asserting: response structure and values');
  342 |       expect(res.status, 'year=invalid must return 400').toBe(400);
  343 |       expect(res.body.message, 'Error must mention invalid value').toContain('invalid');
  344 |       allure.parameter('year=invalid status', String(res.status));
  345 |       allure.parameter('year=invalid message', String(res.body?.message));
  346 |       logger.pass('All assertions passed');
  347 |     });
  348 | 
  349 |     await logger.step('Step 5 â€” year=2027 (future) must return 400', async () => {
  350 |       logger.info(`GET ${ENDPOINT}`);
  351 |       const res = await get('year=2027');
  352 |       logger.pass('HTTP ' + res.status + ' received');
  353 |       logger.info('Asserting: response structure and values');
  354 |       expect(res.status, 'Future year must return 400').toBe(400);
  355 |       expect(res.body.message, 'Error must mention future year').toContain('future');
  356 |       allure.parameter('year=2027 status', String(res.status));
  357 |       allure.parameter('year=2027 message', String(res.body?.message));
  358 |       logger.pass('All assertions passed');
  359 |     });
  360 | 
  361 |     await logger.step('Step 6 â€” year=2026.5 (decimal) must return 400', async () => {
  362 |       logger.info(`GET ${ENDPOINT}`);
  363 |       const res = await get('year=2026.5');
  364 |       logger.pass('HTTP ' + res.status + ' received');
  365 |       logger.info('Asserting: response structure and values');
  366 |       expect(res.status, 'Decimal year must return 400').toBe(400);
  367 |       allure.parameter('year=2026.5 status', String(res.status));
  368 |       logger.pass('All assertions passed');
  369 |     });
  370 |   });
  371 | 
  372 |   // â”€â”€ TC-DMREV-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  373 |   test('TC-DMREV-004 â€” BUG-MREV-01: year=0 must return 400, not silently default to current year', async ({ logger }) => {
  374 |     smokeLabels('TC-DMREV-004', 'BUG-MREV-01 â€” year=0 Bypasses Validation', 'critical');
  375 |     let res: supertest.Response;
  376 | 
  377 |     await logger.step('Step 1 â€” Send GET with year=0', async () => {
  378 |       logger.info(`GET ${ENDPOINT}`);
  379 |       res = await get('year=0');
  380 |       logger.pass('HTTP ' + res.status + ' received');
  381 |       console.log(`[TC-DMREV-004] year=0 â†’ ${res.status}  data.year=${res.body.data?.year}`);
  382 |       allure.parameter('year=0 status',     String(res.status));
  383 |       allure.parameter('year=0 data.year',  String(res.body.data?.year ?? 'N/A'));
  384 |       allure.parameter('year=0 total_payments', String(res.body.data?.summary?.total_payments ?? 'N/A'));
  385 |       attachResponse('TC-DMREV-004', res, { bug: 'BUG-MREV-01', query: 'year=0' });
  386 |     });
  387 | 
  388 |     await logger.step('Step 2 â€” BUG-MREV-01: year=0 must return 400', async () => {
  389 |       logger.info('Asserting: BUG-MREV-01 — year=0 must return 400');
  390 |       if (res!.status === 200) {
  391 |         flagIssue('TC-DMREV-004', 'BUG-MREV-01',
  392 |           'GET /monthly-revenue?year=0 returns 200 OK and silently defaults to the current year. ' +
  393 |           '0 is not a valid 4-digit year â€” the server\'s own validation message says “Must be a 4-digit year” ' +
  394 |           'for year=-1, but year=0 bypasses that check. Both should return 400 Bad Request.',
  395 |           {
  396 |             year_param:    0,
  397 |             actual_status: res!.status,
  398 |             actual_year_in_response: res!.body.data?.year,
  399 |             expected_status: 400,
  400 |             note: 'year=-1 correctly returns 400 but year=0 does not â€” inconsistent boundary check',
  401 |           },
  402 |         );
  403 |       }
  404 |       expect.soft(res!.status,
  405 |         '[BUG-MREV-01] year=0 must return 400 Bad Request (same as year=-1) â€” 0 is not a valid 4-digit year',
  406 |       ).toBe(400);
  407 |       logger.pass('All assertions passed');
  408 |     });
  409 | 
  410 |     await logger.step('Step 3 â€” Verify year=-1 correctly returns 400 (contrast)', async () => {
  411 |       logger.info(`GET ${ENDPOINT}`);
  412 |       const resNeg = await get('year=-1');
  413 |       logger.pass('HTTP ' + resNeg.status + ' received');
  414 |       logger.info('Asserting: response structure and values');
  415 |       expect(resNeg.status, 'year=-1 must return 400 (confirmed working)').toBe(400);
> 416 |       expect(resNeg.body.message, 'year=-1 error must mention 4-digit year').toContain('4-digit');
      |                                                                              ^ Error: expect(received).toContain(expected) // indexOf
  417 |       allure.parameter('year=-1 status',  String(resNeg.status));
  418 |       allure.parameter('year=-1 message', String(resNeg.body?.message));
  419 |       logger.pass('All assertions passed');
  420 |     });
  421 |   });
  422 | 
  423 |   // â”€â”€ TC-DMREV-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  424 |   test('TC-DMREV-005 â€” BUG-MREV-02: year= (empty string) silently defaults to current year', async ({ logger }) => {
  425 |     smokeLabels('TC-DMREV-005', 'BUG-MREV-02 â€” Empty year Param Not Rejected', 'normal');
  426 |     let res: supertest.Response;
  427 | 
  428 |     await logger.step('Step 1 â€” Send GET with year= (empty string)', async () => {
  429 |       logger.info(`GET ${ENDPOINT}`);
  430 |       res = await get('year=');
  431 |       logger.pass('HTTP ' + res.status + ' received');
  432 |       console.log(`[TC-DMREV-005] year= â†’ ${res.status}  data.year=${res.body.data?.year}`);
  433 |       allure.parameter('year= status',    String(res.status));
  434 |       allure.parameter('year= data.year', String(res.body.data?.year ?? 'N/A'));
  435 |       attachResponse('TC-DMREV-005', res, { bug: 'BUG-MREV-02', query: 'year=' });
  436 |     });
  437 | 
  438 |     await logger.step('Step 2 â€” BUG-MREV-02: empty year= must return 400', async () => {
  439 |       logger.info('Asserting: BUG-MREV-02 — empty year= must return 400');
  440 |       if (res!.status === 200) {
  441 |         flagIssue('TC-DMREV-005', 'BUG-MREV-02',
  442 |           'GET /monthly-revenue?year= (empty string) returns 200 and silently defaults to current year. ' +
  443 |           'An empty string is not a valid year value â€” the server must return 400 Bad Request ' +
  444 |           'or clearly document that an empty year means “use current year”.',
  445 |           {
  446 |             year_param:    '',
  447 |             actual_status: res!.status,
  448 |             actual_year:   res!.body.data?.year,
  449 |             expected_status: 400,
  450 |             note: 'Silent fallback to current year provides no feedback to the caller that their param was malformed',
  451 |           },
  452 |         );
  453 |       }
  454 |       expect.soft(res!.status,
  455 |         '[BUG-MREV-02] year= (empty) must return 400 or be documented as "use current year default"',
  456 |       ).toBe(400);
  457 |       logger.pass('All assertions passed');
  458 |     });
  459 |   });
  460 | 
  461 |   // â”€â”€ TC-DMREV-006 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  462 |   test('TC-DMREV-006 â€” BUG-MREV-03: year=1000 is accepted â€” no sensible lower-bound validation', async ({ logger }) => {
  463 |     smokeLabels('TC-DMREV-006', 'BUG-MREV-03 â€” Unreasonable Historical Year Accepted', 'minor');
  464 |     let res: supertest.Response;
  465 | 
  466 |     await logger.step('Step 1 â€” Send GET with year=1000', async () => {
  467 |       logger.info(`GET ${ENDPOINT}`);
  468 |       res = await get('year=1000');
  469 |       logger.pass('HTTP ' + res.status + ' received');
  470 |       console.log(`[TC-DMREV-006] year=1000 â†’ ${res.status}  data.year=${res.body.data?.year}`);
  471 |       allure.parameter('year=1000 status',    String(res.status));
  472 |       allure.parameter('year=1000 data.year', String(res.body.data?.year ?? 'N/A'));
  473 |       attachResponse('TC-DMREV-006', res, { bug: 'BUG-MREV-03', query: 'year=1000' });
  474 |     });
  475 | 
  476 |     await logger.step('Step 2 â€” BUG-MREV-03: year=1000 must return 400', async () => {
  477 |       logger.info('Asserting: BUG-MREV-03 — year=1000 must return 400');
  478 |       if (res!.status === 200) {
  479 |         flagIssue('TC-DMREV-006', 'BUG-MREV-03',
  480 |           'GET /monthly-revenue?year=1000 returns 200 OK with an empty dataset. ' +
  481 |           'Year 1000 predates electronic financial systems by approximately 1 024 years. ' +
  482 |           'A sensible minimum year (e.g. 2000 or system inception year) should be enforced. ' +
  483 |           'Accepting any 4-digit year wastes a database query returning meaningless data.',
  484 |           {
  485 |             year_param:    1000,
  486 |             actual_status: res!.status,
  487 |             actual_data:   { year: res!.body.data?.year, all_zeros: true },
  488 |             expected_status: 400,
  489 |             suggested_validation: 'year >= 2020 (or system start year)',
  490 |           },
  491 |         );
  492 |       }
  493 |       expect.soft(res!.status,
  494 |         '[BUG-MREV-03] year=1000 must return 400 â€” a sensible lower bound (e.g. â‰¥ 2000) should be enforced',
  495 |       ).toBe(400);
  496 |       logger.pass('All assertions passed');
  497 |     });
  498 |   });
  499 | 
  500 |   // â”€â”€ TC-DMREV-007 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  501 |   test('TC-DMREV-007 â€” BUG-MREV-04: CRITICAL â€” /revenue-trend KES total is inflated by USD amounts (currency mixing)', async ({ logger }) => {
  502 |     smokeLabels('TC-DMREV-007', 'BUG-MREV-04 â€” Cross-Endpoint Currency Mixing Bug', 'blocker');
  503 |     let res: supertest.Response;
  504 | 
  505 |     await logger.step('Step 1 â€” Fetch monthly-revenue for current year', async () => {
  506 |       logger.info(`GET ${ENDPOINT}`);
  507 |       res = await get();
  508 |       logger.pass('HTTP ' + res.status + ' received');
  509 |       expect(res.status).toBe(200);
  510 |       logResponse('TC-DMREV-007', res, '(cross-endpoint check)');
  511 |       attachResponse('TC-DMREV-007', res, { bug: 'BUG-MREV-04' });
  512 |       logger.pass('All assertions passed');
  513 |     });
  514 | 
  515 |     await logger.step('Step 2 â€” BUG-MREV-04: Confirm cross-endpoint data inconsistency', async () => {
  516 |       const s = res!.body.data.summary as Summary;
```