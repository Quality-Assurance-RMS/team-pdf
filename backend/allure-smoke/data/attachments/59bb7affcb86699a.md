# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-012 â€” BUG-SUM-07: change_pct must be null when no prior period data exists
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:771:7

# Error details

```
Error: [BUG-SUM-07] period=year KES change_pct should be null when no prior year exists â€” 0.0 misleads users into thinking growth is flat

expect(received).toBeNull()

Received: undefined
```

```
Error: [BUG-SUM-07] total_revenue_kes.change_pct should be null when no data in range

expect(received).toBeNull()

Received: undefined
```

```
Error: [BUG-SUM-07] total_revenue_usd.change_pct should be null when no data in range

expect(received).toBeNull()

Received: undefined
```

```
Error: [BUG-SUM-07] total_transactions.change_pct should be null when no data in range

expect(received).toBeNull()

Received: undefined
```

# Test source

```ts
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
  762 |       expect.soft(succ, '[BUG-SUM-06] rate_pct must be null when count=0 (future empty range)').toBeNull();
  763 | 
  764 |       allure.parameter('future count',       String(cnt));
  765 |       allure.parameter('future success%',    String(succ) + (succ === 0.0 ? ' âš  BUG-SUM-06' : ''));
  766 |       logger.pass('All assertions passed');
  767 |     });
  768 |   });
  769 | 
  770 |   // â”€â”€ TC-DSUM-012 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  771 |   test('TC-DSUM-012 â€” BUG-SUM-07: change_pct must be null when no prior period data exists', async ({ logger }) => {
  772 |     smokeLabels('TC-DSUM-012', 'BUG-SUM-07 â€” change_pct=0.0/flat When No Comparison Period');
  773 | 
  774 |     await logger.step('Step 1 â€” period=year: change_pct should be null (no 2025 data to compare)', async () => {
  775 |       logger.info(`GET ${ENDPOINT}`);
  776 |       const res = await get({ period: 'year' });
  777 |       logger.pass('HTTP ' + res.status + ' received');
  778 |       logger.info('Asserting: response structure and values');
  779 |       expect(res.status, ' -> 200').toBe(200);
  780 |       logResponse('TC-DSUM-012', { period: 'year' }, res);
  781 | 
  782 |       const d         = res.body?.data ?? res.body;
  783 |       const kesChange = d.total_revenue_kes?.change_pct;
  784 |       const txnChange = d.total_transactions?.change_pct;
  785 | 
  786 |       if (kesChange === 0.0) {
  787 |         flagIssue('TC-DSUM-012', 'BUG-SUM-07',
  788 |           'period=year: total_revenue_kes.change_pct=0.0 â€” implies year-over-year is flat, but there is likely no prior year (2025) data to compare against. Should be null to indicate "no comparison baseline".',
  789 |           { period: 'year', change_pct: kesChange },
  790 |         );
  791 |       }
  792 |       expect.soft(kesChange,
  793 |         '[BUG-SUM-07] period=year KES change_pct should be null when no prior year exists â€” 0.0 misleads users into thinking growth is flat',
  794 |       ).toBeNull();
  795 | 
  796 |       allure.parameter('year KES change_pct',  String(kesChange) + (kesChange === 0.0 ? ' âš  BUG-SUM-07' : ''));
  797 |       allure.parameter('year TXN change_pct',  String(txnChange) + (txnChange === 0.0 ? ' âš  BUG-SUM-07' : ''));
  798 |       logger.pass('All assertions passed');
  799 |     });
  800 | 
  801 |     await logger.step('Step 2 â€” custom future range: change_pct must be null (no data in range)', async () => {
  802 |       logger.info(`GET ${ENDPOINT}`);
  803 |       const res = await get({ period: 'custom', from_date: '2030-01-01', to_date: '2030-12-31' });
  804 |       logger.pass('HTTP ' + res.status + ' received');
  805 |       logger.info('Asserting: response structure and values');
  806 |       expect(res.status, ' -> 200').toBe(200);
  807 |       const d = res.body?.data ?? res.body;
  808 | 
  809 |       for (const [field, val] of [
  810 |         ['total_revenue_kes.change_pct', d.total_revenue_kes?.change_pct],
  811 |         ['total_revenue_usd.change_pct', d.total_revenue_usd?.change_pct],
  812 |         ['total_transactions.change_pct', d.total_transactions?.change_pct],
  813 |       ] as [string, unknown][]) {
  814 |         if (val === 0.0) {
  815 |           flagIssue('TC-DSUM-012', 'BUG-SUM-07',
  816 |             `Future empty range: ${field}=0.0 â€” no data exists for 2030, so change_pct cannot be computed. Should return null.`,
  817 |             { field, value: val },
  818 |           );
  819 |         }
> 820 |         expect.soft(val, `[BUG-SUM-07] ${field} should be null when no data in range`).toBeNull();
      |                                                                                        ^ Error: [BUG-SUM-07] total_transactions.change_pct should be null when no data in range
  821 |         allure.parameter(field, String(val) + (val === 0.0 ? ' âš  BUG-SUM-07' : ''));
  822 |       }
  823 |       logger.pass('All assertions passed');
  824 |     });
  825 |   });
  826 | 
  827 |   // â”€â”€ TC-DSUM-013 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  828 |   test('TC-DSUM-013 â€” BUG-SUM-08: wrong HTTP methods return 500 instead of 405', async ({ logger }) => {
  829 |     smokeLabels('TC-DSUM-013', 'BUG-SUM-08 â€” Wrong HTTP Methods Return 500 Not 405');
  830 | 
  831 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  832 | 
  833 |     for (const verb of wrongMethods) {
  834 |       await logger.step(`Step â€” ${verb.toUpperCase()} must return 405 Method Not Allowed`, async () => {
  835 |         logger.info(`GET ${ENDPOINT}`);
  836 |         const res = await method(verb, { period: 'week' });
  837 |         logger.pass('HTTP ' + res.status + ' received');
  838 |         console.log(`\n[TC-DSUM-013] ${verb.toUpperCase()}  -> ${res.status}`);
  839 | 
  840 |         if (res.status === 500) {
  841 |           flagIssue('TC-DSUM-013', 'BUG-SUM-08',
  842 |             `${verb.toUpperCase()} /dashboard/summary returns 500 â€” must return 405 Method Not Allowed with Allow: GET, HEAD, OPTIONS header`,
  843 |             { method: verb.toUpperCase(), status: res.status },
  844 |           );
  845 |         }
  846 |         logger.info('Asserting: response structure and values');
  847 |         expect.soft(res.status,
  848 |           `[BUG-SUM-08] ${verb.toUpperCase()} must return 405 not 500`,
  849 |         ).toBe(405);
  850 | 
  851 |         allure.parameter(`${verb.toUpperCase()} status`, String(res.status) + (res.status === 500 ? ' âš  BUG-SUM-08' : ''));
  852 |         logger.pass('All assertions passed');
  853 |       });
  854 |     }
  855 | 
  856 |     await logger.step('Step â€” HEAD and OPTIONS must return 200 (valid methods)', async () => {
  857 |       logger.info(`GET ${ENDPOINT}`);
  858 |       const headRes    = await method('head');
  859 |       logger.pass('HTTP ' + headRes.status + ' received');
  860 |       logger.info(`GET ${ENDPOINT}`);
  861 |       const optionRes  = await method('options');
  862 |       logger.pass('HTTP ' + optionRes.status + ' received');
  863 |       logger.info('Asserting: response structure and values');
  864 |       expect(headRes.status,   'HEAD must return 200').toBe(200);
  865 |       expect(optionRes.status, 'OPTIONS must return 200').toBe(200);
  866 |       allure.parameter('HEAD status',    String(headRes.status));
  867 |       allure.parameter('OPTIONS status', String(optionRes.status));
  868 |       logger.pass('All assertions passed');
  869 |     });
  870 |   });
  871 | 
  872 |   // â”€â”€ TC-DSUM-014 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  873 |   test('TC-DSUM-014 â€” BUG-SUM-09/10/11: empty period, case sensitivity, incomplete error message', async ({ logger }) => {
  874 |     smokeLabels('TC-DSUM-014', 'BUG-SUM-09/10/11 â€” Period Param Edge Cases', 'normal');
  875 | 
  876 |     await logger.step('Step 1 â€” BUG-SUM-09: empty period= must return 400', async () => {
  877 |       logger.info(`GET ${ENDPOINT}`);
  878 |       const res = await get({ period: '' });
  879 |       logger.pass('HTTP ' + res.status + ' received');
  880 |       if (res.status === 200) {
  881 |         flagIssue('TC-DSUM-014', 'BUG-SUM-09',
  882 |           'period= (empty string) returns 200 â€” behaviour is undefined. Must either return 400 or document what default period is applied.',
  883 |           { status: res.status },
  884 |         );
  885 |       }
  886 |       logger.info('Asserting: BUG-SUM-09: empty period= must return 400');
  887 |       expect.soft(res.status, '[BUG-SUM-09] period= must return 400').toBe(400);
  888 |       allure.parameter('Empty period status', String(res.status) + (res.status === 200 ? ' âš  BUG-SUM-09' : ''));
  889 |       logger.pass('All assertions passed');
  890 |     });
  891 | 
  892 |     await logger.step('Step 2 â€” BUG-SUM-10: period=WEEK uppercase must return 200 (case-insensitive)', async () => {
  893 |       logger.info(`GET ${ENDPOINT}`);
  894 |       const res = await get({ period: 'WEEK' });
  895 |       logger.pass('HTTP ' + res.status + ' received');
  896 |       if (res.status === 400) {
  897 |         flagIssue('TC-DSUM-014', 'BUG-SUM-10',
  898 |           'period=WEEK (uppercase) returns 400 â€” period values should not be case-sensitive',
  899 |           { status: res.status, message: res.body.message },
  900 |         );
  901 |       }
  902 |       logger.info('Asserting: BUG-SUM-10: period=WEEK uppercase must return 200');
  903 |       expect.soft(res.status, '[BUG-SUM-10] period=WEEK should return 200').toBe(200);
  904 |       allure.parameter('WEEK uppercase status', String(res.status) + (res.status === 400 ? ' âš  BUG-SUM-10' : ''));
  905 |       logger.pass('All assertions passed');
  906 |     });
  907 | 
  908 |     await logger.step('Step 3 â€” BUG-SUM-11: invalid period error must include today and last-3-month', async () => {
  909 |       logger.info(`GET ${ENDPOINT}`);
  910 |       const res = await get({ period: 'bad_value' });
  911 |       logger.pass('HTTP ' + res.status + ' received');
  912 |       logger.info('Asserting: BUG-SUM-11: invalid period error must include tod');
  913 |       expect(res.status, 'Invalid period  -> 400').toBe(400);
  914 |       const msg = String(res.body.message ?? '').toLowerCase();
  915 | 
  916 |       const shouldList = ['today', 'week', 'month', 'last-3-month', 'year', 'custom'];
  917 |       const missing    = shouldList.filter(v => !msg.includes(v));
  918 | 
  919 |       if (missing.length) {
  920 |         flagIssue('TC-DSUM-014', 'BUG-SUM-11',
```