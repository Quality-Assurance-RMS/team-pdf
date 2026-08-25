# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-008 — Filter Options: Retrieve available filter values
- Location: tests/API/transaction-api.spec.ts:758:7

# Error details

```
Error: statuses must not be empty

expect(received).toBeGreaterThan(expected)

Expected: > 0
Received:   0
```

# Test source

```ts
  697 |           allure.parameter('payment_reference',          String(body.payment_reference));
  698 |           allure.parameter('invoice_id',                 String(body.invoice_id));
  699 |         });
  700 | 
  701 |         await logger.step('Step 5 — Validate payment fields', async () => {
  702 |           const body = res!.body;
  703 |           logger.info('Asserting: payment_method, gateway_status, gateway_name, amounts present and valid');
  704 |           expect(body.payment_method, 'payment_method must be present').toBeTruthy();
  705 |           expect(body.gateway_status, 'gateway_status must be present').toBeTruthy();
  706 |           expect(body.gateway_name,   'gateway_name must be present').toBeTruthy();
  707 |           expect(typeof body.pg_amount_charged,       'pg_amount_charged must be a number').toBe('number');
  708 |           expect(typeof body.net_settlement_amount,   'net_settlement_amount must be a number').toBe('number');
  709 |           expect(body.pg_amount_charged,  'pg_amount_charged must be positive').toBeGreaterThan(0);
  710 |           logger.pass('All assertions passed');
  711 |           allure.parameter('payment_method',          String(body.payment_method));
  712 |           allure.parameter('gateway_status',          String(body.gateway_status));
  713 |           allure.parameter('gateway_name',            String(body.gateway_name));
  714 |           allure.parameter('pg_amount_charged',       String(body.pg_amount_charged));
  715 |           allure.parameter('net_settlement_amount',   String(body.net_settlement_amount));
  716 |         });
  717 | 
  718 |         await logger.step('Step 6 — Validate service metadata fields', async () => {
  719 |           const body = res!.body;
  720 |           logger.info('Asserting: mcda_name, service_name, trnx_date present');
  721 |           expect(body.mcda_name,    'mcda_name must be present').toBeTruthy();
  722 |           expect(body.service_name, 'service_name must be present').toBeTruthy();
  723 |           expect(body.trnx_date,    'trnx_date must be present').toBeTruthy();
  724 |           logger.pass('All assertions passed');
  725 |           allure.parameter('mcda_name',    String(body.mcda_name));
  726 |           allure.parameter('service_name', String(body.service_name));
  727 |           allure.parameter('trnx_date',    String(body.trnx_date));
  728 |         });
  729 | 
  730 |         await logger.step('Step 7 — Validate payer information fields', async () => {
  731 |           const body = res!.body;
  732 |           logger.info('Asserting: payer_name, payer_phone, payer_email present');
  733 |           expect(body.payer_name,  'payer_name must be present').toBeTruthy();
  734 |           expect(body.payer_phone, 'payer_phone must be present').toBeTruthy();
  735 |           expect(body.payer_email, 'payer_email must be present').toBeTruthy();
  736 |           logger.pass('All assertions passed');
  737 |           allure.parameter('payer_name',  String(body.payer_name));
  738 |           allure.parameter('payer_phone', String(body.payer_phone));
  739 |           allure.parameter('payer_email', String(body.payer_email));
  740 |         });
  741 | 
  742 |         await logger.step('Step 8 — Validate trnx_date is a valid date string', async () => {
  743 |           const dateStr = res!.body.trnx_date as string;
  744 |           const dateRegex = /^\d{4}-\d{2}-\d{2}$/;
  745 |           logger.info('Asserting: trnx_date matches YYYY-MM-DD format');
  746 |           expect(dateRegex.test(dateStr), `trnx_date "${dateStr}" must match YYYY-MM-DD format`).toBe(true);
  747 |           logger.pass('All assertions passed');
  748 |           allure.parameter('trnx_date value',  dateStr);
  749 |           allure.parameter('trnx_date format', 'YYYY-MM-DD — PASS');
  750 |         });
  751 |       });
  752 |     }
  753 |   });
  754 | 
  755 |   // ===========================================================================
  756 |   // TC-TXN-008 — Filter Options
  757 |   // ===========================================================================
  758 |   test('TC-TXN-008 — Filter Options: Retrieve available filter values', async ({ logger }) => {
  759 | 
  760 |     setCommonLabels('TC-TXN-008', 'Filter Options', 'normal');
  761 |     await allure.description(
  762 |       'Verifies the GET /transaction/filter-options endpoint.\n\n' +
  763 |       '**Endpoint:** GET /api/v1/transaction/filter-options\n' +
  764 |       '**Expected:** HTTP 200 with arrays of statuses (SUCCESS, FAILED, PENDING), ' +
  765 |       'methods (MPESA, CARD, BANK), txn_types, and categories.',
  766 |     );
  767 | 
  768 |     let res: supertest.Response;
  769 | 
  770 |     await logger.step('Step 1 — Build request parameters', async () => {
  771 |       allure.parameter('Base URL',    BASE_URL);
  772 |       allure.parameter('Endpoint',    '/api/v1/transaction/filter-options');
  773 |       allure.parameter('Method',      'GET');
  774 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  775 |     });
  776 | 
  777 |     await logger.step('Step 2 — Send GET /transaction/filter-options', async () => {
  778 |       logger.info('GET /api/v1/transaction/filter-options');
  779 |       res = await apiGet('/api/v1/transaction/filter-options');
  780 |       logger.pass('HTTP ' + res.status + ' received');
  781 |       attachRequestResponse('GET', '/api/v1/transaction/filter-options', {}, res);
  782 |       allure.parameter('HTTP Status',  String(res.status));
  783 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  784 |     });
  785 | 
  786 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  787 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  788 |       assertOk(res!, 'TC-TXN-008');
  789 |       logger.pass('All assertions passed');
  790 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  791 |     });
  792 | 
  793 |     await logger.step('Step 4 — Validate statuses array is present and non-empty', async () => {
  794 |       const statuses: string[] = res!.body.statuses ?? [];
  795 |       logger.info('Asserting: statuses is a non-empty array');
  796 |       expect(Array.isArray(statuses), 'statuses must be an array').toBe(true);
> 797 |       expect(statuses.length,         'statuses must not be empty').toBeGreaterThan(0);
      |                                                                     ^ Error: statuses must not be empty
  798 |       logger.pass('All assertions passed');
  799 |       allure.parameter('statuses count',  String(statuses.length));
  800 |       allure.parameter('statuses values', statuses.join(', '));
  801 |     });
  802 | 
  803 |     await logger.step('Step 5 — Validate known statuses are present', async () => {
  804 |       const statuses: string[] = res!.body.statuses ?? [];
  805 |       const knownStatuses = ['SUCCESS', 'FAILED', 'PENDING'];
  806 |       logger.info('Asserting: SUCCESS, FAILED, PENDING all present in statuses array');
  807 |       for (const s of knownStatuses) {
  808 |         expect(statuses, `"${s}" must be in statuses array`).toContain(s);
  809 |       }
  810 |       logger.pass('All assertions passed');
  811 |       allure.parameter('Expected statuses', knownStatuses.join(', '));
  812 |       allure.parameter('Validation',        'All expected statuses found — PASS');
  813 |     });
  814 | 
  815 |     await logger.step('Step 6 — Validate methods array is present', async () => {
  816 |       const methods: string[] = res!.body.methods ?? [];
  817 |       logger.info('Asserting: methods is a non-empty array');
  818 |       expect(Array.isArray(methods), 'methods must be an array').toBe(true);
  819 |       expect(methods.length,         'methods must not be empty').toBeGreaterThan(0);
  820 |       logger.pass('All assertions passed');
  821 |       allure.parameter('methods count',  String(methods.length));
  822 |       allure.parameter('methods values', methods.join(', '));
  823 |     });
  824 | 
  825 |     await logger.step('Step 7 — Validate known payment methods are present', async () => {
  826 |       const methods: string[] = res!.body.methods ?? [];
  827 |       const knownMethods = ['MPESA', 'CARD', 'BANK'];
  828 |       logger.info('Asserting: MPESA, CARD, BANK all present in methods array');
  829 |       for (const m of knownMethods) {
  830 |         expect(methods, `"${m}" must be in methods array`).toContain(m);
  831 |       }
  832 |       logger.pass('All assertions passed');
  833 |       allure.parameter('Expected methods', knownMethods.join(', '));
  834 |       allure.parameter('Validation',       'All expected methods found — PASS');
  835 |     });
  836 | 
  837 |     await logger.step('Step 8 — Validate txn_types array is present', async () => {
  838 |       const types = res!.body.txn_types ?? [];
  839 |       logger.info('Asserting: txn_types is a non-empty array');
  840 |       expect(Array.isArray(types), 'txn_types must be an array').toBe(true);
  841 |       expect(types.length,         'txn_types must not be empty').toBeGreaterThan(0);
  842 |       logger.pass('All assertions passed');
  843 |       allure.parameter('txn_types count',  String(types.length));
  844 |       allure.parameter('txn_types values', JSON.stringify(types));
  845 |     });
  846 | 
  847 |     await logger.step('Step 9 — Validate categories array is present', async () => {
  848 |       const categories: string[] = res!.body.categories ?? [];
  849 |       logger.info('Asserting: categories is a non-empty array');
  850 |       expect(Array.isArray(categories), 'categories must be an array').toBe(true);
  851 |       expect(categories.length,         'categories must not be empty').toBeGreaterThan(0);
  852 |       logger.pass('All assertions passed');
  853 |       allure.parameter('categories count',  String(categories.length));
  854 |       allure.parameter('categories values', categories.join(', '));
  855 |     });
  856 |   });
  857 | 
  858 |   // ===========================================================================
  859 |   // TC-TXN-009 — Auth: 401 on missing / invalid token
  860 |   // ===========================================================================
  861 |   test('TC-TXN-009 — Security: Returns 401 when Authorization header is missing', async ({ logger }) => {
  862 | 
  863 |     setCommonLabels('TC-TXN-009', 'Authentication', 'critical');
  864 |     await allure.description(
  865 |       'Confirms the API enforces authentication and rejects unauthenticated requests.\n\n' +
  866 |       '**Endpoint:** GET /api/v1/transaction/summary?period=today\n' +
  867 |       '**Headers:** No Authorization header\n' +
  868 |       '**Expected:** HTTP 401 Unauthorized with an error message.\n\n' +
  869 |       'This validates that the API is properly secured and not publicly accessible.',
  870 |     );
  871 | 
  872 |     let res: supertest.Response;
  873 | 
  874 |     await logger.step('Step 1 — Build unauthenticated request', async () => {
  875 |       allure.parameter('Base URL',       BASE_URL);
  876 |       allure.parameter('Endpoint',       '/api/v1/transaction/summary?period=today');
  877 |       allure.parameter('Method',         'GET');
  878 |       allure.parameter('Authorization',  'NOT PROVIDED — intentionally omitted');
  879 |     });
  880 | 
  881 |     await logger.step('Step 2 — Send GET /summary without Authorization header', async () => {
  882 |       logger.info('GET /api/v1/transaction/summary?period=today (no auth)');
  883 |       res = await supertest(BASE_URL)
  884 |         .get('/api/v1/transaction/summary?period=today')
  885 |         .set('Accept', 'application/json');
  886 |       logger.pass('HTTP ' + res.status + ' received');
  887 | 
  888 |       console.log('[No-Auth GET /summary] status:', res.status, '| body:', JSON.stringify(res.body, null, 2));
  889 | 
  890 |       allure.attachment(
  891 |         'Unauthenticated Request & Response',
  892 |         JSON.stringify({
  893 |           request:  { method: 'GET', url: `${BASE_URL}/api/v1/transaction/summary?period=today`, headers: {} },
  894 |           response: { status: res.status, body: res.body },
  895 |         }, null, 2),
  896 |         'application/json',
  897 |       );
```