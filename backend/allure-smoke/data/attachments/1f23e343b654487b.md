# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-007 â€” BUG-SUM-04: single-day range (from=to) must be accepted (validator too strict)
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:528:7

# Error details

```
Error: [BUG-SUM-04] Single-day range (from_date = to_date) must return 200 â€” equal dates are a valid range

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
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
> 551 |       ).toBe(200);
      |         ^ Error: [BUG-SUM-04] Single-day range (from_date = to_date) must return 200 â€” equal dates are a valid range
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
  572 |         label:  'impossible Feb 30',
  573 |         params: { period: 'custom', from_date: '2026-02-30', to_date: '2026-03-01' },
  574 |         bugId:  'BUG-SUM-02',
  575 |         reason: 'Feb 30 does not exist in any year â€” must return 400 not 500',
  576 |       },
  577 |       {
  578 |         label:  'impossible Apr 31',
  579 |         params: { period: 'custom', from_date: '2026-04-31', to_date: '2026-05-01' },
  580 |         bugId:  'BUG-SUM-02',
  581 |         reason: 'April 31 does not exist â€” must return 400 not 500',
  582 |       },
  583 |       {
  584 |         label:  'DD-MM-YYYY format',
  585 |         params: { period: 'custom', from_date: '01-01-2026', to_date: '31-05-2026' },
  586 |         bugId:  'BUG-SUM-03',
  587 |         reason: 'DD-MM-YYYY date format must return 400 with format guidance, not 500',
  588 |       },
  589 |     ];
  590 | 
  591 |     for (const c of crashCases) {
  592 |       await logger.step(`Step â€” ${c.label}: must return 400 not 500`, async () => {
  593 |         logger.info(`GET ${ENDPOINT}`);
  594 |         const res = await get(c.params);
  595 |         logger.pass('HTTP ' + res.status + ' received');
  596 |         console.log(`\n[TC-DSUM-008] ${c.label}  -> ${res.status}: ${JSON.stringify(res.body)}`);
  597 | 
  598 |         if (res.status === 500) {
  599 |           flagIssue('TC-DSUM-008', c.bugId,
  600 |             `${c.label}: ${c.reason}`,
  601 |             { params: c.params, status: res.status },
  602 |           );
  603 |         }
  604 |         logger.info('Asserting: response structure and values');
  605 |         expect.soft(res.status,
  606 |           `[${c.bugId}] ${c.label} must return 400, not 500`,
  607 |         ).toBe(400);
  608 | 
  609 |         allure.parameter(`${c.label} status`, String(res.status) + (res.status === 500 ? ` âš  ${c.bugId}` : ''));
  610 |         logger.pass('All assertions passed');
  611 |       });
  612 |     }
  613 |   });
  614 | 
  615 |   // â”€â”€ TC-DSUM-009 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  616 |   test('TC-DSUM-009 â€” BUG-SUM-01: SQL injection in from_date must return 400 not 500 (security)', async ({ logger }) => {
  617 |     smokeLabels('TC-DSUM-009', 'BUG-SUM-01 â€” SQL Injection Input Causes 500 (Security)', 'blocker');
  618 | 
  619 |     const injectionPayloads = [
  620 |       { label: 'SQL OR injection',    from: "'; DROP TABLE--",           to: '2026-05-31' },
  621 |       { label: 'SQL union injection', from: "2026-01-01' UNION SELECT--", to: '2026-05-31' },
  622 |       { label: 'Single quote',        from: "2026-01-01'",               to: '2026-05-31' },
  623 |     ];
  624 | 
  625 |     for (const p of injectionPayloads) {
  626 |       await logger.step(`Step â€” ${p.label}: must return 400 not 500`, async () => {
  627 |         logger.info(`GET ${ENDPOINT}`);
  628 |         const res = await get({ period: 'custom', from_date: p.from, to_date: p.to });
  629 |         logger.pass('HTTP ' + res.status + ' received');
  630 |         console.log(`\n[TC-DSUM-009] "${p.label}"  -> ${res.status}: ${JSON.stringify(res.body)}`);
  631 | 
  632 |         if (res.status === 500) {
  633 |           flagIssue('TC-DSUM-009', 'BUG-SUM-01',
  634 |             `SQL injection payload in from_date causes HTTP 500 â€” input is not rejected at validation layer before reaching the database. Payload: "${p.from}"`,
  635 |             { payload: p.from, status: res.status },
  636 |           );
  637 |         }
  638 |         logger.info('Asserting: response structure and values');
  639 |         expect.soft(res.status,
  640 |           `[BUG-SUM-01] SQL injection payload "${p.label}" must be rejected with 400, not crash with 500`,
  641 |         ).toBe(400);
  642 | 
  643 |         allure.parameter(`${p.label} status`, String(res.status) + (res.status === 500 ? ' âš  BUG-SUM-01 SECURITY' : ''));
  644 |         logger.pass('All assertions passed');
  645 |       });
  646 |     }
  647 |   });
  648 | 
  649 |   // â”€â”€ TC-DSUM-010 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  650 |   test('TC-DSUM-010 â€” BUG-SUM-05: successful% + failed% must sum to 100%', async ({ logger }) => {
  651 |     smokeLabels('TC-DSUM-010', 'BUG-SUM-05 â€” Success + Failure Rates Must Sum to 100%');
```