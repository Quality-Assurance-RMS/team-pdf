# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-010 â€” BUG-SUM-05: successful% + failed% must sum to 100%
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:658:7

# Error details

```
Error: period=week  -> 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
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
  580 |         label:  'impossible Feb 30',
  581 |         params: { period: 'custom', from_date: '2026-02-30', to_date: '2026-03-01' },
  582 |         bugId:  'BUG-SUM-02',
  583 |         reason: 'Feb 30 does not exist in any year â€” must return 400 not 500',
  584 |       },
  585 |       {
  586 |         label:  'impossible Apr 31',
  587 |         params: { period: 'custom', from_date: '2026-04-31', to_date: '2026-05-01' },
  588 |         bugId:  'BUG-SUM-02',
  589 |         reason: 'April 31 does not exist â€” must return 400 not 500',
  590 |       },
  591 |       {
  592 |         label:  'DD-MM-YYYY format',
  593 |         params: { period: 'custom', from_date: '01-01-2026', to_date: '31-05-2026' },
  594 |         bugId:  'BUG-SUM-03',
  595 |         reason: 'DD-MM-YYYY date format must return 400 with format guidance, not 500',
  596 |       },
  597 |     ];
  598 | 
  599 |     for (const c of crashCases) {
  600 |       await logger.step(`Step â€” ${c.label}: must return 400 not 500`, async () => {
  601 |         logger.info(`GET ${ENDPOINT}`);
  602 |         const res = await get(c.params);
  603 |         logger.pass('HTTP ' + res.status + ' received');
  604 |         console.log(`\n[TC-DSUM-008] ${c.label}  -> ${res.status}: ${JSON.stringify(res.body)}`);
  605 | 
  606 |         if (res.status === 500) {
  607 |           flagIssue('TC-DSUM-008', c.bugId,
  608 |             `${c.label}: ${c.reason}`,
  609 |             { params: c.params, status: res.status },
  610 |           );
  611 |         }
  612 |         logger.info('Asserting: response structure and values');
  613 |         expect.soft(res.status,
  614 |           `[${c.bugId}] ${c.label} must return 400, not 500`,
  615 |         ).toBe(400);
  616 | 
  617 |         allure.parameter(`${c.label} status`, String(res.status) + (res.status === 500 ? ` âš  ${c.bugId}` : ''));
  618 |         logger.pass('All assertions passed');
  619 |       });
  620 |     }
  621 |   });
  622 | 
  623 |   // â”€â”€ TC-DSUM-009 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  624 |   test('TC-DSUM-009 â€” BUG-SUM-01: SQL injection in from_date must return 400 not 500 (security)', async ({ logger }) => {
  625 |     backendLabels('TC-DSUM-009', 'BUG-SUM-01 â€” SQL Injection Input Causes 500 (Security)', 'blocker');
  626 | 
  627 |     const injectionPayloads = [
  628 |       { label: 'SQL OR injection',    from: "'; DROP TABLE--",           to: '2026-05-31' },
  629 |       { label: 'SQL union injection', from: "2026-01-01' UNION SELECT--", to: '2026-05-31' },
  630 |       { label: 'Single quote',        from: "2026-01-01'",               to: '2026-05-31' },
  631 |     ];
  632 | 
  633 |     for (const p of injectionPayloads) {
  634 |       await logger.step(`Step â€” ${p.label}: must return 400 not 500`, async () => {
  635 |         logger.info(`GET ${ENDPOINT}`);
  636 |         const res = await get({ period: 'custom', from_date: p.from, to_date: p.to });
  637 |         logger.pass('HTTP ' + res.status + ' received');
  638 |         console.log(`\n[TC-DSUM-009] "${p.label}"  -> ${res.status}: ${JSON.stringify(res.body)}`);
  639 | 
  640 |         if (res.status === 500) {
  641 |           flagIssue('TC-DSUM-009', 'BUG-SUM-01',
  642 |             `SQL injection payload in from_date causes HTTP 500 â€” input is not rejected at validation layer before reaching the database. Payload: "${p.from}"`,
  643 |             { payload: p.from, status: res.status },
  644 |           );
  645 |         }
  646 |         logger.info('Asserting: response structure and values');
  647 |         expect.soft(res.status,
  648 |           `[BUG-SUM-01] SQL injection payload "${p.label}" must be rejected with 400, not crash with 500`,
  649 |         ).toBe(400);
  650 | 
  651 |         allure.parameter(`${p.label} status`, String(res.status) + (res.status === 500 ? ' âš  BUG-SUM-01 SECURITY' : ''));
  652 |         logger.pass('All assertions passed');
  653 |       });
  654 |     }
  655 |   });
  656 | 
  657 |   // â”€â”€ TC-DSUM-010 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  658 |   test('TC-DSUM-010 â€” BUG-SUM-05: successful% + failed% must sum to 100%', async ({ logger }) => {
  659 |     backendLabels('TC-DSUM-010', 'BUG-SUM-05 â€” Success + Failure Rates Must Sum to 100%');
  660 | 
  661 |     const periodsToCheck = ['week', 'year', 'last-3-month'] as const;
  662 | 
  663 |     for (const period of periodsToCheck) {
  664 |       await logger.step(`Step â€” period=${period}: validate rate sum`, async () => {
  665 |         logger.info(`GET ${ENDPOINT}`);
  666 |         const res = await get({ period });
  667 |         logger.pass('HTTP ' + res.status + ' received');
  668 |         logger.info('Asserting: Step â€” period=${period}: validate rate sum');
> 669 |         expect(res.status, `period=${period}  -> 200`).toBe(200);
      |                                                        ^ Error: period=week  -> 200
  670 |         logResponse('TC-DSUM-010', { period }, res);
  671 | 
  672 |         const d    = res.body?.data ?? res.body;
  673 |         const succ = d.successful_payments?.rate_pct as number ?? 0;
  674 |         const fail = d.failed_payments?.rate_pct    as number ?? 0;
  675 |         const sum  = succ + fail;
  676 |         const gap  = Math.abs(100 - sum);
  677 | 
  678 |         if (gap > 0.05) {
  679 |           flagIssue('TC-DSUM-010', 'BUG-SUM-05',
  680 |             `period=${period}: success(${succ}%) + failed(${fail}%) = ${sum.toFixed(2)}% â€” missing ${gap.toFixed(2)}% likely belongs to PENDING transactions which have no field in the response`,
  681 |             { period, successful: succ, failed: fail, sum, gap },
  682 |           );
  683 |         }
  684 |         expect.soft(sum,
  685 |           `[BUG-SUM-05] period=${period}: success(${succ}%) + failed(${fail}%) must equal 100%, gap=${gap.toFixed(2)}%`,
  686 |         ).toBeCloseTo(100, 1);
  687 | 
  688 |         allure.parameter(`${period} success%`,  String(succ) + '%');
  689 |         allure.parameter(`${period} failed%`,   String(fail) + '%');
  690 |         allure.parameter(`${period} sum%`,      String(sum.toFixed(2)) + (gap > 0.05 ? '% âš  BUG-SUM-05' : '% OK'));
  691 |         allure.parameter(`${period} gap`,       String(gap.toFixed(2)) + '%' + (gap > 0 ? ' (PENDING unaccounted)' : ''));
  692 |         logger.pass('All assertions passed');
  693 |       });
  694 |     }
  695 |   });
  696 | 
  697 |   // â”€â”€ TC-DSUM-011 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  698 |   test('TC-DSUM-011 â€” BUG-SUM-06: rate_pct must be null (not 0.0%) when transaction count is zero', async ({ logger }) => {
  699 |     backendLabels('TC-DSUM-011', 'BUG-SUM-06 â€” Success Rate 0Ã·0 Should Be Null Not 0%');
  700 | 
  701 |     let res: supertest.Response;
  702 | 
  703 |     await logger.step('Step 1 â€” Get period=today (count=0 on a day with no transactions)', async () => {
  704 |       logger.info(`GET ${ENDPOINT}`);
  705 |       res = await get({ period: 'today' });
  706 |       logger.pass('HTTP ' + res.status + ' received');
  707 |       logResponse('TC-DSUM-011', { period: 'today' }, res);
  708 |       logger.info('Asserting: response structure and values');
  709 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  710 |       logger.pass('All assertions passed');
  711 |     });
  712 | 
  713 |     await logger.step('Step 2 â€” Check if transaction count is 0', async () => {
  714 |       const d   = res!.body?.data ?? res!.body;
  715 |       const cnt = d.total_transactions?.count as number;
  716 |       allure.parameter('total_transactions.count', String(cnt));
  717 |       if (cnt > 0) {
  718 |         console.log(`[TC-DSUM-011] period=today has ${cnt} transactions â€” skipping 0Ã·0 check (use a future empty date range instead)`);
  719 |       }
  720 |     });
  721 | 
  722 |     await logger.step('Step 3 â€” BUG-SUM-06: rate_pct should be null when count=0 (flag if 0.0)', async () => {
  723 |       const d    = res!.body?.data ?? res!.body;
  724 |       const cnt  = d.total_transactions?.count as number;
  725 |       const succ = d.successful_payments?.rate_pct;
  726 |       const fail = d.failed_payments?.rate_pct;
  727 | 
  728 |       if (cnt === 0) {
  729 |         if (succ === 0.0) {
  730 |           flagIssue('TC-DSUM-011', 'BUG-SUM-06',
  731 |             'successful_payments.rate_pct=0.0 when total count=0 â€” 0Ã·0 is mathematically indeterminate, should return null to avoid misleading users into thinking all transactions failed',
  732 |             { count: cnt, rate_pct: succ },
  733 |           );
  734 |         }
  735 |         if (fail === 0.0) {
  736 |           flagIssue('TC-DSUM-011', 'BUG-SUM-06',
  737 |             'failed_payments.rate_pct=0.0 when total count=0 â€” should be null when no transactions exist',
  738 |             { count: cnt, rate_pct: fail },
  739 |           );
  740 |         }
  741 |         logger.info('Asserting: BUG-SUM-06: rate_pct should be null when count=0');
  742 |         expect.soft(succ, '[BUG-SUM-06] successful_payments.rate_pct should be null when count=0').toBeNull();
  743 |         expect.soft(fail, '[BUG-SUM-06] failed_payments.rate_pct should be null when count=0').toBeNull();
  744 |       }
  745 | 
  746 |       allure.parameter('count',              String(cnt));
  747 |       allure.parameter('success rate_pct',   String(succ)   + (cnt === 0 && succ === 0.0 ? ' âš  BUG-SUM-06' : ''));
  748 |       allure.parameter('failed rate_pct',    String(fail)   + (cnt === 0 && fail === 0.0 ? ' âš  BUG-SUM-06' : ''));
  749 |       logger.pass('All assertions passed');
  750 |     });
  751 | 
  752 |     await logger.step('Step 4 â€” Also test with a future date range guaranteed to have 0 transactions', async () => {
  753 |       logger.info(`GET ${ENDPOINT}`);
  754 |       const futureRes = await get({ period: 'custom', from_date: '2030-01-01', to_date: '2030-01-02' });
  755 |       logger.pass('HTTP ' + futureRes.status + ' received');
  756 |       logger.info('Asserting: response structure and values');
  757 |       expect(futureRes.status, 'Future range  -> 200').toBe(200);
  758 |       const d    = futureRes.body?.data ?? futureRes.body;
  759 |       const cnt  = d.total_transactions?.count as number;
  760 |       const succ = d.successful_payments?.rate_pct;
  761 | 
  762 |       expect(cnt, 'Future range must have 0 transactions').toBe(0);
  763 | 
  764 |       if (succ === 0.0) {
  765 |         flagIssue('TC-DSUM-011', 'BUG-SUM-06',
  766 |           'successful_payments.rate_pct=0.0 on guaranteed-empty future date range â€” must be null when count=0',
  767 |           { period: 'custom 2030', count: cnt, rate_pct: succ },
  768 |         );
  769 |       }
```