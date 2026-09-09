# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-012 â€” BUG-SUM-07: change_pct must be null when no prior period data exists
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:779:7

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
  770 |       expect.soft(succ, '[BUG-SUM-06] rate_pct must be null when count=0 (future empty range)').toBeNull();
  771 | 
  772 |       allure.parameter('future count',       String(cnt));
  773 |       allure.parameter('future success%',    String(succ) + (succ === 0.0 ? ' âš  BUG-SUM-06' : ''));
  774 |       logger.pass('All assertions passed');
  775 |     });
  776 |   });
  777 | 
  778 |   // â”€â”€ TC-DSUM-012 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  779 |   test('TC-DSUM-012 â€” BUG-SUM-07: change_pct must be null when no prior period data exists', async ({ logger }) => {
  780 |     backendLabels('TC-DSUM-012', 'BUG-SUM-07 â€” change_pct=0.0/flat When No Comparison Period');
  781 | 
  782 |     await logger.step('Step 1 â€” period=year: change_pct should be null (no 2025 data to compare)', async () => {
  783 |       logger.info(`GET ${ENDPOINT}`);
  784 |       const res = await get({ period: 'year' });
  785 |       logger.pass('HTTP ' + res.status + ' received');
  786 |       logger.info('Asserting: response structure and values');
  787 |       expect(res.status, ' -> 200').toBe(200);
  788 |       logResponse('TC-DSUM-012', { period: 'year' }, res);
  789 | 
  790 |       const d         = res.body?.data ?? res.body;
  791 |       const kesChange = d.total_revenue_kes?.change_pct;
  792 |       const txnChange = d.total_transactions?.change_pct;
  793 | 
  794 |       if (kesChange === 0.0) {
  795 |         flagIssue('TC-DSUM-012', 'BUG-SUM-07',
  796 |           'period=year: total_revenue_kes.change_pct=0.0 â€” implies year-over-year is flat, but there is likely no prior year (2025) data to compare against. Should be null to indicate "no comparison baseline".',
  797 |           { period: 'year', change_pct: kesChange },
  798 |         );
  799 |       }
  800 |       expect.soft(kesChange,
  801 |         '[BUG-SUM-07] period=year KES change_pct should be null when no prior year exists â€” 0.0 misleads users into thinking growth is flat',
  802 |       ).toBeNull();
  803 | 
  804 |       allure.parameter('year KES change_pct',  String(kesChange) + (kesChange === 0.0 ? ' âš  BUG-SUM-07' : ''));
  805 |       allure.parameter('year TXN change_pct',  String(txnChange) + (txnChange === 0.0 ? ' âš  BUG-SUM-07' : ''));
  806 |       logger.pass('All assertions passed');
  807 |     });
  808 | 
  809 |     await logger.step('Step 2 â€” custom future range: change_pct must be null (no data in range)', async () => {
  810 |       logger.info(`GET ${ENDPOINT}`);
  811 |       const res = await get({ period: 'custom', from_date: '2030-01-01', to_date: '2030-12-31' });
  812 |       logger.pass('HTTP ' + res.status + ' received');
  813 |       logger.info('Asserting: response structure and values');
  814 |       expect(res.status, ' -> 200').toBe(200);
  815 |       const d = res.body?.data ?? res.body;
  816 | 
  817 |       for (const [field, val] of [
  818 |         ['total_revenue_kes.change_pct', d.total_revenue_kes?.change_pct],
  819 |         ['total_revenue_usd.change_pct', d.total_revenue_usd?.change_pct],
  820 |         ['total_transactions.change_pct', d.total_transactions?.change_pct],
  821 |       ] as [string, unknown][]) {
  822 |         if (val === 0.0) {
  823 |           flagIssue('TC-DSUM-012', 'BUG-SUM-07',
  824 |             `Future empty range: ${field}=0.0 â€” no data exists for 2030, so change_pct cannot be computed. Should return null.`,
  825 |             { field, value: val },
  826 |           );
  827 |         }
> 828 |         expect.soft(val, `[BUG-SUM-07] ${field} should be null when no data in range`).toBeNull();
      |                                                                                        ^ Error: [BUG-SUM-07] total_transactions.change_pct should be null when no data in range
  829 |         allure.parameter(field, String(val) + (val === 0.0 ? ' âš  BUG-SUM-07' : ''));
  830 |       }
  831 |       logger.pass('All assertions passed');
  832 |     });
  833 |   });
  834 | 
  835 |   // â”€â”€ TC-DSUM-013 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  836 |   test('TC-DSUM-013 â€” BUG-SUM-08: wrong HTTP methods return 500 instead of 405', async ({ logger }) => {
  837 |     backendLabels('TC-DSUM-013', 'BUG-SUM-08 â€” Wrong HTTP Methods Return 500 Not 405');
  838 | 
  839 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  840 | 
  841 |     for (const verb of wrongMethods) {
  842 |       await logger.step(`Step â€” ${verb.toUpperCase()} must return 405 Method Not Allowed`, async () => {
  843 |         logger.info(`GET ${ENDPOINT}`);
  844 |         const res = await method(verb, { period: 'week' });
  845 |         logger.pass('HTTP ' + res.status + ' received');
  846 |         console.log(`\n[TC-DSUM-013] ${verb.toUpperCase()}  -> ${res.status}`);
  847 | 
  848 |         if (res.status === 500) {
  849 |           flagIssue('TC-DSUM-013', 'BUG-SUM-08',
  850 |             `${verb.toUpperCase()} /dashboard/summary returns 500 â€” must return 405 Method Not Allowed with Allow: GET, HEAD, OPTIONS header`,
  851 |             { method: verb.toUpperCase(), status: res.status },
  852 |           );
  853 |         }
  854 |         logger.info('Asserting: response structure and values');
  855 |         expect.soft(res.status,
  856 |           `[BUG-SUM-08] ${verb.toUpperCase()} must return 405 not 500`,
  857 |         ).toBe(405);
  858 | 
  859 |         allure.parameter(`${verb.toUpperCase()} status`, String(res.status) + (res.status === 500 ? ' âš  BUG-SUM-08' : ''));
  860 |         logger.pass('All assertions passed');
  861 |       });
  862 |     }
  863 | 
  864 |     await logger.step('Step â€” HEAD and OPTIONS must return 200 (valid methods)', async () => {
  865 |       logger.info(`GET ${ENDPOINT}`);
  866 |       const headRes    = await method('head');
  867 |       logger.pass('HTTP ' + headRes.status + ' received');
  868 |       logger.info(`GET ${ENDPOINT}`);
  869 |       const optionRes  = await method('options');
  870 |       logger.pass('HTTP ' + optionRes.status + ' received');
  871 |       logger.info('Asserting: response structure and values');
  872 |       expect(headRes.status,   'HEAD must return 200').toBe(200);
  873 |       expect(optionRes.status, 'OPTIONS must return 200').toBe(200);
  874 |       allure.parameter('HEAD status',    String(headRes.status));
  875 |       allure.parameter('OPTIONS status', String(optionRes.status));
  876 |       logger.pass('All assertions passed');
  877 |     });
  878 |   });
  879 | 
  880 |   // â”€â”€ TC-DSUM-014 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  881 |   test('TC-DSUM-014 â€” BUG-SUM-09/10/11: empty period, case sensitivity, incomplete error message', async ({ logger }) => {
  882 |     backendLabels('TC-DSUM-014', 'BUG-SUM-09/10/11 â€” Period Param Edge Cases', 'normal');
  883 | 
  884 |     await logger.step('Step 1 â€” BUG-SUM-09: empty period= must return 400', async () => {
  885 |       logger.info(`GET ${ENDPOINT}`);
  886 |       const res = await get({ period: '' });
  887 |       logger.pass('HTTP ' + res.status + ' received');
  888 |       if (res.status === 200) {
  889 |         flagIssue('TC-DSUM-014', 'BUG-SUM-09',
  890 |           'period= (empty string) returns 200 â€” behaviour is undefined. Must either return 400 or document what default period is applied.',
  891 |           { status: res.status },
  892 |         );
  893 |       }
  894 |       logger.info('Asserting: BUG-SUM-09: empty period= must return 400');
  895 |       expect.soft(res.status, '[BUG-SUM-09] period= must return 400').toBe(400);
  896 |       allure.parameter('Empty period status', String(res.status) + (res.status === 200 ? ' âš  BUG-SUM-09' : ''));
  897 |       logger.pass('All assertions passed');
  898 |     });
  899 | 
  900 |     await logger.step('Step 2 â€” BUG-SUM-10: period=WEEK uppercase must return 200 (case-insensitive)', async () => {
  901 |       logger.info(`GET ${ENDPOINT}`);
  902 |       const res = await get({ period: 'WEEK' });
  903 |       logger.pass('HTTP ' + res.status + ' received');
  904 |       if (res.status === 400) {
  905 |         flagIssue('TC-DSUM-014', 'BUG-SUM-10',
  906 |           'period=WEEK (uppercase) returns 400 â€” period values should not be case-sensitive',
  907 |           { status: res.status, message: res.body.message },
  908 |         );
  909 |       }
  910 |       logger.info('Asserting: BUG-SUM-10: period=WEEK uppercase must return 200');
  911 |       expect.soft(res.status, '[BUG-SUM-10] period=WEEK should return 200').toBe(200);
  912 |       allure.parameter('WEEK uppercase status', String(res.status) + (res.status === 400 ? ' âš  BUG-SUM-10' : ''));
  913 |       logger.pass('All assertions passed');
  914 |     });
  915 | 
  916 |     await logger.step('Step 3 â€” BUG-SUM-11: invalid period error must include today and last-3-month', async () => {
  917 |       logger.info(`GET ${ENDPOINT}`);
  918 |       const res = await get({ period: 'bad_value' });
  919 |       logger.pass('HTTP ' + res.status + ' received');
  920 |       logger.info('Asserting: BUG-SUM-11: invalid period error must include tod');
  921 |       expect(res.status, 'Invalid period  -> 400').toBe(400);
  922 |       const msg = String(res.body.message ?? '').toLowerCase();
  923 | 
  924 |       const shouldList = ['today', 'week', 'month', 'last-3-month', 'year', 'custom'];
  925 |       const missing    = shouldList.filter(v => !msg.includes(v));
  926 | 
  927 |       if (missing.length) {
  928 |         flagIssue('TC-DSUM-014', 'BUG-SUM-11',
```