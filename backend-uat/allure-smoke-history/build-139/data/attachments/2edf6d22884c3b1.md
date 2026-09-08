# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-transaction-detail.spec.ts >> Backend (Konza) â€" Transaction Detail >> TC-BE-007 â€" Detail: 00bba274-1617-4568-aaf2-a2ed08b66a18 returns 200 with core fields
- Location: tests/API/backend-tests/backend-transaction-detail.spec.ts:130:9

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 404
```

# Test source

```ts
  54  |   const b       = res.body as Record<string, unknown>;
  55  | 
  56  |   console.log(`\n${'â"€'.repeat(70)}`);
  57  |   console.log(`[${tcId}] GET ${fullUrl}`);
  58  |   console.log(`  â–º Status                     : ${res.status}`);
  59  |   console.log(`  â–º Content-Type               : ${res.headers['content-type'] ?? 'N/A'}`);
  60  |   console.log(`  â–º payment_trnx_id            : ${b.payment_trnx_id}`);
  61  |   console.log(`  â–º transaction_confirmation_id: ${b.transaction_confirmation_id}`);
  62  |   if (b.payment_trnx_id === b.transaction_confirmation_id) {
  63  |     console.warn(`  âš  ISSUE-3: payment_trnx_id === transaction_confirmation_id (${b.payment_trnx_id})`);
  64  |   }
  65  |   console.log(`  â–º payment_reference          : ${b.payment_reference}`);
  66  |   console.log(`  â–º invoice_id                 : ${b.invoice_id}`);
  67  |   console.log(`  â–º payment_method             : ${b.payment_method}`);
  68  |   console.log(`  â–º gateway_status             : ${b.gateway_status}`);
  69  |   console.log(`  â–º gateway_name               : ${b.gateway_name}`);
  70  |   console.log(`  â–º pg_amount_charged          : ${b.pg_amount_charged}`);
  71  |   console.log(`  â–º net_settlement_amount      : ${b.net_settlement_amount}`);
  72  |   console.log(`  â–º trnx_date                  : ${b.trnx_date}  (list returns "date_time" field â€" ISSUE-4)`);
  73  |   console.log(`  â–º wallet_provider            : ${JSON.stringify(b.wallet_provider)}  (list returns null â€" ISSUE-6)`);
  74  |   console.log(`  â–º timeline                   : ${JSON.stringify(b.timeline)}`);
  75  |   console.log(`  â–º spr_match                  : ${JSON.stringify(b.spr_match)}`);
  76  |   console.log(`  â–º mcda_name                  : ${b.mcda_name}  (list uses "ministry" â€" ISSUE-10)`);
  77  |   console.log(`  â–º service_name               : ${b.service_name}  (list uses "department" â€" ISSUE-11)`);
  78  |   console.log(`  â–º payer_name                 : ${b.payer_name}`);
  79  |   console.log(`  â–º payer_phone                : ${b.payer_phone}`);
  80  |   console.log(`  â–º payer_email                : ${b.payer_email}`);
  81  |   console.log(`  â–º Full Body:`);
  82  |   console.log(JSON.stringify(res.body, null, 4));
  83  |   console.log(`${'â"€'.repeat(70)}\n`);
  84  | 
  85  |   allure.attachment(
  86  |     `[${tcId}] Request & Response â€" ${txnId}`,
  87  |     JSON.stringify({
  88  |       request: { method: 'GET', url: fullUrl, transaction_uuid: txnId, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  89  |       response: {
  90  |         status: res.status, content_type: res.headers['content-type'] ?? 'N/A',
  91  |         payment_trnx_id: b.payment_trnx_id, transaction_confirmation_id: b.transaction_confirmation_id,
  92  |         payment_reference: b.payment_reference, invoice_id: b.invoice_id,
  93  |         payment_method: b.payment_method, gateway_status: b.gateway_status, gateway_name: b.gateway_name,
  94  |         pg_amount_charged: b.pg_amount_charged, net_settlement_amount: b.net_settlement_amount,
  95  |         trnx_date: b.trnx_date, mcda_name: b.mcda_name, service_name: b.service_name,
  96  |         payer_name: b.payer_name, payer_phone: b.payer_phone, payer_email: b.payer_email,
  97  |         wallet_provider: b.wallet_provider, timeline: b.timeline, spr_match: b.spr_match,
  98  |         full_body: res.body,
  99  |       },
  100 |     }, null, 2),
  101 |     'application/json',
  102 |   );
  103 | }
  104 | 
  105 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  106 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  107 |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  108 |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  109 | }
  110 | 
  111 | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  112 | 
  113 | function backendLabels(tcId: string, title: string) {
  114 |   allure.parentSuite('RMS Transaction API');
  115 |   allure.suite('Backend');
  116 |   allure.subSuite('Transaction Detail Endpoint');
  117 |   allure.label('testId',   tcId);
  118 |   allure.label('severity', 'critical');
  119 |   allure.owner('Ashil Shaji');
  120 |   allure.tags('Backend', 'Konza', 'API', 'TransactionDetail');
  121 |   allure.label('story', title);
  122 | }
  123 | 
  124 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  125 | 
  126 | test.describe('Backend (Konza) â€" Transaction Detail', () => {
  127 | 
  128 |   for (const txnId of KNOWN_TXN_IDS) {
  129 | 
  130 |     test(`TC-BE-007 â€" Detail: ${txnId} returns 200 with core fields`, async ({ logger }) => {
  131 |       backendLabels('TC-BE-007', 'Transaction Detail');
  132 | 
  133 |       let res: supertest.Response;
  134 | 
  135 |       await logger.step('Step 1 â€" Build request', async () => {
  136 |         allure.parameter('Base URL',         BASE_URL);
  137 |         allure.parameter('Endpoint',         `/api/v1/transaction/${txnId}`);
  138 |         allure.parameter('Method',           'GET');
  139 |         allure.parameter('Transaction UUID', txnId);
  140 |         allure.parameter('Auth',             `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  141 |       });
  142 | 
  143 |       await logger.step(`Step 2 â€" Send GET /transaction/${txnId} and log response`, async () => {
  144 |         logger.info(`GET /api/v1/transaction/${txnId}`);
  145 |         res = await get(txnId);
  146 |         logger.pass('HTTP ' + res.status + ' received');
  147 |         logResponse('TC-BE-007', txnId, res);
  148 |         allure.parameter('HTTP Status',  String(res.status));
  149 |         allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  150 |       });
  151 | 
  152 |       await logger.step('Step 3 â€" Validate HTTP 200 and core identifier fields', async () => {
  153 |         logger.info('Asserting: HTTP 200 and core identifier fields present');
> 154 |         expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                  ^ Error: HTTP 200 expected
  155 |         expect(res!.body,   'Body must be defined').toBeDefined();
  156 |         expect(res!.body.payment_trnx_id,            'payment_trnx_id must be present').toBeTruthy();
  157 |         expect(res!.body.transaction_confirmation_id, 'transaction_confirmation_id must be present').toBeTruthy();
  158 |         expect(res!.body.payment_reference,           'payment_reference must be present').toBeTruthy();
  159 |         expect(res!.body.invoice_id,                  'invoice_id must be present').toBeTruthy();
  160 | 
  161 |         // â"€â"€ ISSUE-3: payment_trnx_id and transaction_confirmation_id have the same value â"€â"€
  162 |         if (res!.body.payment_trnx_id === res!.body.transaction_confirmation_id) {
  163 |           flagIssue('TC-BE-007', 'ISSUE-3',
  164 |             'payment_trnx_id === transaction_confirmation_id â€" these should be distinct identifiers',
  165 |             { payment_trnx_id: res!.body.payment_trnx_id, transaction_confirmation_id: res!.body.transaction_confirmation_id },
  166 |           );
  167 |         }
  168 |         expect.soft(
  169 |           res!.body.payment_trnx_id,
  170 |           '[ISSUE-3] payment_trnx_id should NOT equal transaction_confirmation_id',
  171 |         ).not.toBe(res!.body.transaction_confirmation_id);
  172 | 
  173 |         allure.parameter('payment_trnx_id',            String(res!.body.payment_trnx_id));
  174 |         allure.parameter('transaction_confirmation_id', String(res!.body.transaction_confirmation_id));
  175 |         allure.parameter('payment_reference',          String(res!.body.payment_reference));
  176 |         allure.parameter('invoice_id',                 String(res!.body.invoice_id));
  177 |         logger.pass('All assertions passed');
  178 |       });
  179 | 
  180 |       await logger.step('Step 4 â€" Validate core payment fields and status', async () => {
  181 |         logger.info('Asserting: payment_method, gateway_status, gateway_name present and status valid');
  182 |         expect(res!.body.payment_method, 'payment_method must be present').toBeTruthy();
  183 |         expect(res!.body.gateway_status, 'gateway_status must be present').toBeTruthy();
  184 |         expect(res!.body.gateway_name,   'gateway_name must be present').toBeTruthy();
  185 | 
  186 |         // gateway_status must be one of the known statuses (uppercase)
  187 |         expect(['SUCCESS', 'FAILED', 'PENDING'],
  188 |           `gateway_status "${res!.body.gateway_status}" must be SUCCESS/FAILED/PENDING`,
  189 |         ).toContain(res!.body.gateway_status);
  190 | 
  191 |         allure.parameter('payment_method', String(res!.body.payment_method));
  192 |         allure.parameter('gateway_status', String(res!.body.gateway_status));
  193 |         allure.parameter('gateway_name',   String(res!.body.gateway_name));
  194 |         logger.pass('All assertions passed');
  195 |       });
  196 | 
  197 |       await logger.step('Step 5 â€" Validate financial amounts', async () => {
  198 |         logger.info('Asserting: pg_amount_charged and net_settlement_amount types and ranges');
  199 |         const pgAmt  = res!.body.pg_amount_charged      as number;
  200 |         const netAmt = res!.body.net_settlement_amount  as number;
  201 | 
  202 |         expect(typeof pgAmt,  'pg_amount_charged must be a number').toBe('number');
  203 |         expect(typeof netAmt, 'net_settlement_amount must be a number').toBe('number');
  204 |         expect(pgAmt,         'pg_amount_charged must be > 0').toBeGreaterThan(0);
  205 |         expect(netAmt,        'net_settlement_amount must be >= 0').toBeGreaterThanOrEqual(0);
  206 |         expect(netAmt,        'net_settlement_amount must be <= pg_amount_charged (fees applied)').toBeLessThanOrEqual(pgAmt);
  207 | 
  208 |         allure.parameter('pg_amount_charged',      String(pgAmt));
  209 |         allure.parameter('net_settlement_amount',  String(netAmt));
  210 |         allure.parameter('fee_deducted',           String((pgAmt - netAmt).toFixed(2)));
  211 |         logger.pass('All assertions passed');
  212 |       });
  213 | 
  214 |       await logger.step('Step 6 â€" Validate trnx_date format and flag missing date_time field', async () => {
  215 |         logger.info('Asserting: trnx_date present and matches YYYY-MM-DD format');
  216 |         const trnxDate = res!.body.trnx_date as string;
  217 |         expect(trnxDate, 'trnx_date must be present').toBeTruthy();
  218 |         expect(DATE_REGEX.test(trnxDate), `trnx_date "${trnxDate}" must match YYYY-MM-DD`).toBe(true);
  219 | 
  220 |         // â"€â"€ ISSUE-4: detail has trnx_date (date only) but list has date_time (with time component)
  221 |         const hasDateTime = 'date_time' in res!.body;
  222 |         if (!hasDateTime) {
  223 |           flagIssue('TC-BE-007', 'ISSUE-4',
  224 |             'date_time field missing in detail response â€" list returns date_time but detail only has trnx_date (ISSUE-5: different formats too)',
  225 |             { trnx_date: trnxDate, date_time: 'MISSING' },
  226 |           );
  227 |         }
  228 |         expect.soft(res!.body, '[ISSUE-4] detail should include date_time field like the list endpoint does').toHaveProperty('date_time');
  229 | 
  230 |         allure.parameter('trnx_date',       trnxDate);
  231 |         allure.parameter('date_time field', hasDateTime ? 'present' : 'MISSING âš  ISSUE-4/5');
  232 |         logger.pass('All assertions passed');
  233 |       });
  234 | 
  235 |       await logger.step('Step 7 â€" Validate payer information formats', async () => {
  236 |         logger.info('Asserting: payer_name, payer_phone, payer_email present and valid formats');
  237 |         const payer_email = String(res!.body.payer_email ?? '');
  238 |         const payer_phone = String(res!.body.payer_phone ?? '');
  239 |         const payer_name  = String(res!.body.payer_name  ?? '');
  240 | 
  241 |         expect(payer_name,  'payer_name must be present').toBeTruthy();
  242 |         expect(payer_phone, 'payer_phone must be present').toBeTruthy();
  243 |         expect(payer_email, 'payer_email must be present').toBeTruthy();
  244 | 
  245 |         expect(EMAIL_REGEX.test(payer_email),
  246 |           `payer_email "${payer_email}" must be a valid email address`).toBe(true);
  247 |         expect(INTL_PHONE_REGEX.test(payer_phone),
  248 |           `payer_phone "${payer_phone}" must be international format (+digits)`).toBe(true);
  249 | 
  250 |         allure.parameter('payer_name',  payer_name);
  251 |         allure.parameter('payer_phone', payer_phone);
  252 |         allure.parameter('payer_email', payer_email);
  253 |         logger.pass('All assertions passed');
  254 |       });
```