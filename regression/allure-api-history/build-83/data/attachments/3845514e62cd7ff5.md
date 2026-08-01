# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-008 — Filter Options: Retrieve available filter values
- Location: tests/API/transaction-api.spec.ts:755:7

# Error details

```
Error: statuses must not be empty

expect(received).toBeGreaterThan(expected)

Expected: > 0
Received:   0
```

# Test source

```ts
  694 |           allure.parameter('payment_reference',          String(body.payment_reference));
  695 |           allure.parameter('invoice_id',                 String(body.invoice_id));
  696 |         });
  697 | 
  698 |         await logger.step('Step 5 — Validate payment fields', async () => {
  699 |           const body = res!.body;
  700 |           logger.info('Asserting: payment_method, gateway_status, gateway_name, amounts present and valid');
  701 |           expect(body.payment_method, 'payment_method must be present').toBeTruthy();
  702 |           expect(body.gateway_status, 'gateway_status must be present').toBeTruthy();
  703 |           expect(body.gateway_name,   'gateway_name must be present').toBeTruthy();
  704 |           expect(typeof body.pg_amount_charged,       'pg_amount_charged must be a number').toBe('number');
  705 |           expect(typeof body.net_settlement_amount,   'net_settlement_amount must be a number').toBe('number');
  706 |           expect(body.pg_amount_charged,  'pg_amount_charged must be positive').toBeGreaterThan(0);
  707 |           logger.pass('All assertions passed');
  708 |           allure.parameter('payment_method',          String(body.payment_method));
  709 |           allure.parameter('gateway_status',          String(body.gateway_status));
  710 |           allure.parameter('gateway_name',            String(body.gateway_name));
  711 |           allure.parameter('pg_amount_charged',       String(body.pg_amount_charged));
  712 |           allure.parameter('net_settlement_amount',   String(body.net_settlement_amount));
  713 |         });
  714 | 
  715 |         await logger.step('Step 6 — Validate service metadata fields', async () => {
  716 |           const body = res!.body;
  717 |           logger.info('Asserting: mcda_name, service_name, trnx_date present');
  718 |           expect(body.mcda_name,    'mcda_name must be present').toBeTruthy();
  719 |           expect(body.service_name, 'service_name must be present').toBeTruthy();
  720 |           expect(body.trnx_date,    'trnx_date must be present').toBeTruthy();
  721 |           logger.pass('All assertions passed');
  722 |           allure.parameter('mcda_name',    String(body.mcda_name));
  723 |           allure.parameter('service_name', String(body.service_name));
  724 |           allure.parameter('trnx_date',    String(body.trnx_date));
  725 |         });
  726 | 
  727 |         await logger.step('Step 7 — Validate payer information fields', async () => {
  728 |           const body = res!.body;
  729 |           logger.info('Asserting: payer_name, payer_phone, payer_email present');
  730 |           expect(body.payer_name,  'payer_name must be present').toBeTruthy();
  731 |           expect(body.payer_phone, 'payer_phone must be present').toBeTruthy();
  732 |           expect(body.payer_email, 'payer_email must be present').toBeTruthy();
  733 |           logger.pass('All assertions passed');
  734 |           allure.parameter('payer_name',  String(body.payer_name));
  735 |           allure.parameter('payer_phone', String(body.payer_phone));
  736 |           allure.parameter('payer_email', String(body.payer_email));
  737 |         });
  738 | 
  739 |         await logger.step('Step 8 — Validate trnx_date is a valid date string', async () => {
  740 |           const dateStr = res!.body.trnx_date as string;
  741 |           const dateRegex = /^\d{4}-\d{2}-\d{2}$/;
  742 |           logger.info('Asserting: trnx_date matches YYYY-MM-DD format');
  743 |           expect(dateRegex.test(dateStr), `trnx_date "${dateStr}" must match YYYY-MM-DD format`).toBe(true);
  744 |           logger.pass('All assertions passed');
  745 |           allure.parameter('trnx_date value',  dateStr);
  746 |           allure.parameter('trnx_date format', 'YYYY-MM-DD — PASS');
  747 |         });
  748 |       });
  749 |     }
  750 |   });
  751 | 
  752 |   // ===========================================================================
  753 |   // TC-TXN-008 — Filter Options
  754 |   // ===========================================================================
  755 |   test('TC-TXN-008 — Filter Options: Retrieve available filter values', async ({ logger }) => {
  756 | 
  757 |     setCommonLabels('TC-TXN-008', 'Filter Options', 'normal');
  758 |     await allure.description(
  759 |       'Verifies the GET /transaction/filter-options endpoint.\n\n' +
  760 |       '**Endpoint:** GET /api/v1/transaction/filter-options\n' +
  761 |       '**Expected:** HTTP 200 with arrays of statuses (SUCCESS, FAILED, PENDING), ' +
  762 |       'methods (MPESA, CARD, BANK), txn_types, and categories.',
  763 |     );
  764 | 
  765 |     let res: supertest.Response;
  766 | 
  767 |     await logger.step('Step 1 — Build request parameters', async () => {
  768 |       allure.parameter('Base URL',    BASE_URL);
  769 |       allure.parameter('Endpoint',    '/api/v1/transaction/filter-options');
  770 |       allure.parameter('Method',      'GET');
  771 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  772 |     });
  773 | 
  774 |     await logger.step('Step 2 — Send GET /transaction/filter-options', async () => {
  775 |       logger.info('GET /api/v1/transaction/filter-options');
  776 |       res = await apiGet('/api/v1/transaction/filter-options');
  777 |       logger.pass('HTTP ' + res.status + ' received');
  778 |       attachRequestResponse('GET', '/api/v1/transaction/filter-options', {}, res);
  779 |       allure.parameter('HTTP Status',  String(res.status));
  780 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  781 |     });
  782 | 
  783 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  784 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  785 |       assertOk(res!, 'TC-TXN-008');
  786 |       logger.pass('All assertions passed');
  787 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  788 |     });
  789 | 
  790 |     await logger.step('Step 4 — Validate statuses array is present and non-empty', async () => {
  791 |       const statuses: string[] = res!.body.statuses ?? [];
  792 |       logger.info('Asserting: statuses is a non-empty array');
  793 |       expect(Array.isArray(statuses), 'statuses must be an array').toBe(true);
> 794 |       expect(statuses.length,         'statuses must not be empty').toBeGreaterThan(0);
      |                                                                     ^ Error: statuses must not be empty
  795 |       logger.pass('All assertions passed');
  796 |       allure.parameter('statuses count',  String(statuses.length));
  797 |       allure.parameter('statuses values', statuses.join(', '));
  798 |     });
  799 | 
  800 |     await logger.step('Step 5 — Validate known statuses are present', async () => {
  801 |       const statuses: string[] = res!.body.statuses ?? [];
  802 |       const knownStatuses = ['SUCCESS', 'FAILED', 'PENDING'];
  803 |       logger.info('Asserting: SUCCESS, FAILED, PENDING all present in statuses array');
  804 |       for (const s of knownStatuses) {
  805 |         expect(statuses, `"${s}" must be in statuses array`).toContain(s);
  806 |       }
  807 |       logger.pass('All assertions passed');
  808 |       allure.parameter('Expected statuses', knownStatuses.join(', '));
  809 |       allure.parameter('Validation',        'All expected statuses found — PASS');
  810 |     });
  811 | 
  812 |     await logger.step('Step 6 — Validate methods array is present', async () => {
  813 |       const methods: string[] = res!.body.methods ?? [];
  814 |       logger.info('Asserting: methods is a non-empty array');
  815 |       expect(Array.isArray(methods), 'methods must be an array').toBe(true);
  816 |       expect(methods.length,         'methods must not be empty').toBeGreaterThan(0);
  817 |       logger.pass('All assertions passed');
  818 |       allure.parameter('methods count',  String(methods.length));
  819 |       allure.parameter('methods values', methods.join(', '));
  820 |     });
  821 | 
  822 |     await logger.step('Step 7 — Validate known payment methods are present', async () => {
  823 |       const methods: string[] = res!.body.methods ?? [];
  824 |       const knownMethods = ['MPESA', 'CARD', 'BANK'];
  825 |       logger.info('Asserting: MPESA, CARD, BANK all present in methods array');
  826 |       for (const m of knownMethods) {
  827 |         expect(methods, `"${m}" must be in methods array`).toContain(m);
  828 |       }
  829 |       logger.pass('All assertions passed');
  830 |       allure.parameter('Expected methods', knownMethods.join(', '));
  831 |       allure.parameter('Validation',       'All expected methods found — PASS');
  832 |     });
  833 | 
  834 |     await logger.step('Step 8 — Validate txn_types array is present', async () => {
  835 |       const types = res!.body.txn_types ?? [];
  836 |       logger.info('Asserting: txn_types is a non-empty array');
  837 |       expect(Array.isArray(types), 'txn_types must be an array').toBe(true);
  838 |       expect(types.length,         'txn_types must not be empty').toBeGreaterThan(0);
  839 |       logger.pass('All assertions passed');
  840 |       allure.parameter('txn_types count',  String(types.length));
  841 |       allure.parameter('txn_types values', JSON.stringify(types));
  842 |     });
  843 | 
  844 |     await logger.step('Step 9 — Validate categories array is present', async () => {
  845 |       const categories: string[] = res!.body.categories ?? [];
  846 |       logger.info('Asserting: categories is a non-empty array');
  847 |       expect(Array.isArray(categories), 'categories must be an array').toBe(true);
  848 |       expect(categories.length,         'categories must not be empty').toBeGreaterThan(0);
  849 |       logger.pass('All assertions passed');
  850 |       allure.parameter('categories count',  String(categories.length));
  851 |       allure.parameter('categories values', categories.join(', '));
  852 |     });
  853 |   });
  854 | 
  855 |   // ===========================================================================
  856 |   // TC-TXN-009 — Auth: 401 on missing / invalid token
  857 |   // ===========================================================================
  858 |   test('TC-TXN-009 — Security: Returns 401 when Authorization header is missing', async ({ logger }) => {
  859 | 
  860 |     setCommonLabels('TC-TXN-009', 'Authentication', 'critical');
  861 |     await allure.description(
  862 |       'Confirms the API enforces authentication and rejects unauthenticated requests.\n\n' +
  863 |       '**Endpoint:** GET /api/v1/transaction/summary?period=today\n' +
  864 |       '**Headers:** No Authorization header\n' +
  865 |       '**Expected:** HTTP 401 Unauthorized with an error message.\n\n' +
  866 |       'This validates that the API is properly secured and not publicly accessible.',
  867 |     );
  868 | 
  869 |     let res: supertest.Response;
  870 | 
  871 |     await logger.step('Step 1 — Build unauthenticated request', async () => {
  872 |       allure.parameter('Base URL',       BASE_URL);
  873 |       allure.parameter('Endpoint',       '/api/v1/transaction/summary?period=today');
  874 |       allure.parameter('Method',         'GET');
  875 |       allure.parameter('Authorization',  'NOT PROVIDED — intentionally omitted');
  876 |     });
  877 | 
  878 |     await logger.step('Step 2 — Send GET /summary without Authorization header', async () => {
  879 |       logger.info('GET /api/v1/transaction/summary?period=today (no auth)');
  880 |       res = await supertest(BASE_URL)
  881 |         .get('/api/v1/transaction/summary?period=today')
  882 |         .set('Accept', 'application/json');
  883 |       logger.pass('HTTP ' + res.status + ' received');
  884 | 
  885 |       console.log('[No-Auth GET /summary] status:', res.status, '| body:', JSON.stringify(res.body, null, 2));
  886 | 
  887 |       allure.attachment(
  888 |         'Unauthenticated Request & Response',
  889 |         JSON.stringify({
  890 |           request:  { method: 'GET', url: `${BASE_URL}/api/v1/transaction/summary?period=today`, headers: {} },
  891 |           response: { status: res.status, body: res.body },
  892 |         }, null, 2),
  893 |         'application/json',
  894 |       );
```