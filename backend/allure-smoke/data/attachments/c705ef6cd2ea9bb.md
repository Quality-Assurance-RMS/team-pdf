# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-010 â€” BUG-SUM-05: successful% + failed% must sum to 100%
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:650:7

# Error details

```
Error: period=week  -> 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
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
  652 | 
  653 |     const periodsToCheck = ['week', 'year', 'last-3-month'] as const;
  654 | 
  655 |     for (const period of periodsToCheck) {
  656 |       await logger.step(`Step â€” period=${period}: validate rate sum`, async () => {
  657 |         logger.info(`GET ${ENDPOINT}`);
  658 |         const res = await get({ period });
  659 |         logger.pass('HTTP ' + res.status + ' received');
  660 |         logger.info('Asserting: Step â€” period=${period}: validate rate sum');
> 661 |         expect(res.status, `period=${period}  -> 200`).toBe(200);
      |                                                        ^ Error: period=week  -> 200
  662 |         logResponse('TC-DSUM-010', { period }, res);
  663 | 
  664 |         const d    = res.body?.data ?? res.body;
  665 |         const succ = d.successful_payments?.rate_pct as number ?? 0;
  666 |         const fail = d.failed_payments?.rate_pct    as number ?? 0;
  667 |         const sum  = succ + fail;
  668 |         const gap  = Math.abs(100 - sum);
  669 | 
  670 |         if (gap > 0.05) {
  671 |           flagIssue('TC-DSUM-010', 'BUG-SUM-05',
  672 |             `period=${period}: success(${succ}%) + failed(${fail}%) = ${sum.toFixed(2)}% â€” missing ${gap.toFixed(2)}% likely belongs to PENDING transactions which have no field in the response`,
  673 |             { period, successful: succ, failed: fail, sum, gap },
  674 |           );
  675 |         }
  676 |         expect.soft(sum,
  677 |           `[BUG-SUM-05] period=${period}: success(${succ}%) + failed(${fail}%) must equal 100%, gap=${gap.toFixed(2)}%`,
  678 |         ).toBeCloseTo(100, 1);
  679 | 
  680 |         allure.parameter(`${period} success%`,  String(succ) + '%');
  681 |         allure.parameter(`${period} failed%`,   String(fail) + '%');
  682 |         allure.parameter(`${period} sum%`,      String(sum.toFixed(2)) + (gap > 0.05 ? '% âš  BUG-SUM-05' : '% OK'));
  683 |         allure.parameter(`${period} gap`,       String(gap.toFixed(2)) + '%' + (gap > 0 ? ' (PENDING unaccounted)' : ''));
  684 |         logger.pass('All assertions passed');
  685 |       });
  686 |     }
  687 |   });
  688 | 
  689 |   // â”€â”€ TC-DSUM-011 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  690 |   test('TC-DSUM-011 â€” BUG-SUM-06: rate_pct must be null (not 0.0%) when transaction count is zero', async ({ logger }) => {
  691 |     smokeLabels('TC-DSUM-011', 'BUG-SUM-06 â€” Success Rate 0Ã·0 Should Be Null Not 0%');
  692 | 
  693 |     let res: supertest.Response;
  694 | 
  695 |     await logger.step('Step 1 â€” Get period=today (count=0 on a day with no transactions)', async () => {
  696 |       logger.info(`GET ${ENDPOINT}`);
  697 |       res = await get({ period: 'today' });
  698 |       logger.pass('HTTP ' + res.status + ' received');
  699 |       logResponse('TC-DSUM-011', { period: 'today' }, res);
  700 |       logger.info('Asserting: response structure and values');
  701 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  702 |       logger.pass('All assertions passed');
  703 |     });
  704 | 
  705 |     await logger.step('Step 2 â€” Check if transaction count is 0', async () => {
  706 |       const d   = res!.body?.data ?? res!.body;
  707 |       const cnt = d.total_transactions?.count as number;
  708 |       allure.parameter('total_transactions.count', String(cnt));
  709 |       if (cnt > 0) {
  710 |         console.log(`[TC-DSUM-011] period=today has ${cnt} transactions â€” skipping 0Ã·0 check (use a future empty date range instead)`);
  711 |       }
  712 |     });
  713 | 
  714 |     await logger.step('Step 3 â€” BUG-SUM-06: rate_pct should be null when count=0 (flag if 0.0)', async () => {
  715 |       const d    = res!.body?.data ?? res!.body;
  716 |       const cnt  = d.total_transactions?.count as number;
  717 |       const succ = d.successful_payments?.rate_pct;
  718 |       const fail = d.failed_payments?.rate_pct;
  719 | 
  720 |       if (cnt === 0) {
  721 |         if (succ === 0.0) {
  722 |           flagIssue('TC-DSUM-011', 'BUG-SUM-06',
  723 |             'successful_payments.rate_pct=0.0 when total count=0 â€” 0Ã·0 is mathematically indeterminate, should return null to avoid misleading users into thinking all transactions failed',
  724 |             { count: cnt, rate_pct: succ },
  725 |           );
  726 |         }
  727 |         if (fail === 0.0) {
  728 |           flagIssue('TC-DSUM-011', 'BUG-SUM-06',
  729 |             'failed_payments.rate_pct=0.0 when total count=0 â€” should be null when no transactions exist',
  730 |             { count: cnt, rate_pct: fail },
  731 |           );
  732 |         }
  733 |         logger.info('Asserting: BUG-SUM-06: rate_pct should be null when count=0');
  734 |         expect.soft(succ, '[BUG-SUM-06] successful_payments.rate_pct should be null when count=0').toBeNull();
  735 |         expect.soft(fail, '[BUG-SUM-06] failed_payments.rate_pct should be null when count=0').toBeNull();
  736 |       }
  737 | 
  738 |       allure.parameter('count',              String(cnt));
  739 |       allure.parameter('success rate_pct',   String(succ)   + (cnt === 0 && succ === 0.0 ? ' âš  BUG-SUM-06' : ''));
  740 |       allure.parameter('failed rate_pct',    String(fail)   + (cnt === 0 && fail === 0.0 ? ' âš  BUG-SUM-06' : ''));
  741 |       logger.pass('All assertions passed');
  742 |     });
  743 | 
  744 |     await logger.step('Step 4 â€” Also test with a future date range guaranteed to have 0 transactions', async () => {
  745 |       logger.info(`GET ${ENDPOINT}`);
  746 |       const futureRes = await get({ period: 'custom', from_date: '2030-01-01', to_date: '2030-01-02' });
  747 |       logger.pass('HTTP ' + futureRes.status + ' received');
  748 |       logger.info('Asserting: response structure and values');
  749 |       expect(futureRes.status, 'Future range  -> 200').toBe(200);
  750 |       const d    = futureRes.body?.data ?? futureRes.body;
  751 |       const cnt  = d.total_transactions?.count as number;
  752 |       const succ = d.successful_payments?.rate_pct;
  753 | 
  754 |       expect(cnt, 'Future range must have 0 transactions').toBe(0);
  755 | 
  756 |       if (succ === 0.0) {
  757 |         flagIssue('TC-DSUM-011', 'BUG-SUM-06',
  758 |           'successful_payments.rate_pct=0.0 on guaranteed-empty future date range â€” must be null when count=0',
  759 |           { period: 'custom 2030', count: cnt, rate_pct: succ },
  760 |         );
  761 |       }
```