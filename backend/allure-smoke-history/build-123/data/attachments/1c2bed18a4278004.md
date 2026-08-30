# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-transaction-detail.spec.ts >> Smoke â€" Transaction Detail >> TC-SMK-007 â€" Detail: 00bba274-1617-4568-aaf2-a2ed08b66a18 returns 200 with core fields
- Location: tests/API/smoke/smoke-transaction-detail.spec.ts:122:9

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 404
```

# Test source

```ts
  46  |   const b       = res.body as Record<string, unknown>;
  47  | 
  48  |   console.log(`\n${'â"€'.repeat(70)}`);
  49  |   console.log(`[${tcId}] GET ${fullUrl}`);
  50  |   console.log(`  â–º Status                     : ${res.status}`);
  51  |   console.log(`  â–º Content-Type               : ${res.headers['content-type'] ?? 'N/A'}`);
  52  |   console.log(`  â–º payment_trnx_id            : ${b.payment_trnx_id}`);
  53  |   console.log(`  â–º transaction_confirmation_id: ${b.transaction_confirmation_id}`);
  54  |   if (b.payment_trnx_id === b.transaction_confirmation_id) {
  55  |     console.warn(`  âš  ISSUE-3: payment_trnx_id === transaction_confirmation_id (${b.payment_trnx_id})`);
  56  |   }
  57  |   console.log(`  â–º payment_reference          : ${b.payment_reference}`);
  58  |   console.log(`  â–º invoice_id                 : ${b.invoice_id}`);
  59  |   console.log(`  â–º payment_method             : ${b.payment_method}`);
  60  |   console.log(`  â–º gateway_status             : ${b.gateway_status}`);
  61  |   console.log(`  â–º gateway_name               : ${b.gateway_name}`);
  62  |   console.log(`  â–º pg_amount_charged          : ${b.pg_amount_charged}`);
  63  |   console.log(`  â–º net_settlement_amount      : ${b.net_settlement_amount}`);
  64  |   console.log(`  â–º trnx_date                  : ${b.trnx_date}  (list returns "date_time" field â€" ISSUE-4)`);
  65  |   console.log(`  â–º wallet_provider            : ${JSON.stringify(b.wallet_provider)}  (list returns null â€" ISSUE-6)`);
  66  |   console.log(`  â–º timeline                   : ${JSON.stringify(b.timeline)}`);
  67  |   console.log(`  â–º spr_match                  : ${JSON.stringify(b.spr_match)}`);
  68  |   console.log(`  â–º mcda_name                  : ${b.mcda_name}  (list uses "ministry" â€" ISSUE-10)`);
  69  |   console.log(`  â–º service_name               : ${b.service_name}  (list uses "department" â€" ISSUE-11)`);
  70  |   console.log(`  â–º payer_name                 : ${b.payer_name}`);
  71  |   console.log(`  â–º payer_phone                : ${b.payer_phone}`);
  72  |   console.log(`  â–º payer_email                : ${b.payer_email}`);
  73  |   console.log(`  â–º Full Body:`);
  74  |   console.log(JSON.stringify(res.body, null, 4));
  75  |   console.log(`${'â"€'.repeat(70)}\n`);
  76  | 
  77  |   allure.attachment(
  78  |     `[${tcId}] Request & Response â€" ${txnId}`,
  79  |     JSON.stringify({
  80  |       request: { method: 'GET', url: fullUrl, transaction_uuid: txnId, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  81  |       response: {
  82  |         status: res.status, content_type: res.headers['content-type'] ?? 'N/A',
  83  |         payment_trnx_id: b.payment_trnx_id, transaction_confirmation_id: b.transaction_confirmation_id,
  84  |         payment_reference: b.payment_reference, invoice_id: b.invoice_id,
  85  |         payment_method: b.payment_method, gateway_status: b.gateway_status, gateway_name: b.gateway_name,
  86  |         pg_amount_charged: b.pg_amount_charged, net_settlement_amount: b.net_settlement_amount,
  87  |         trnx_date: b.trnx_date, mcda_name: b.mcda_name, service_name: b.service_name,
  88  |         payer_name: b.payer_name, payer_phone: b.payer_phone, payer_email: b.payer_email,
  89  |         wallet_provider: b.wallet_provider, timeline: b.timeline, spr_match: b.spr_match,
  90  |         full_body: res.body,
  91  |       },
  92  |     }, null, 2),
  93  |     'application/json',
  94  |   );
  95  | }
  96  | 
  97  | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  98  |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  99  |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  100 |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  101 | }
  102 | 
  103 | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  104 | 
  105 | function smokeLabels(tcId: string, title: string) {
  106 |   allure.parentSuite('RMS Transaction API');
  107 |   allure.suite('Smoke');
  108 |   allure.subSuite('Transaction Detail Endpoint');
  109 |   allure.label('testId',   tcId);
  110 |   allure.label('severity', 'critical');
  111 |   allure.owner('Ashil Shaji');
  112 |   allure.tags('Smoke', 'API', 'TransactionDetail');
  113 |   allure.label('story', title);
  114 | }
  115 | 
  116 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  117 | 
  118 | test.describe('Smoke â€" Transaction Detail', () => {
  119 | 
  120 |   for (const txnId of KNOWN_TXN_IDS) {
  121 | 
  122 |     test(`TC-SMK-007 â€" Detail: ${txnId} returns 200 with core fields`, async ({ logger }) => {
  123 |       smokeLabels('TC-SMK-007', 'Transaction Detail');
  124 | 
  125 |       let res: supertest.Response;
  126 | 
  127 |       await logger.step('Step 1 â€" Build request', async () => {
  128 |         allure.parameter('Base URL',         BASE_URL);
  129 |         allure.parameter('Endpoint',         `/api/v1/transaction/${txnId}`);
  130 |         allure.parameter('Method',           'GET');
  131 |         allure.parameter('Transaction UUID', txnId);
  132 |         allure.parameter('Auth',             `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  133 |       });
  134 | 
  135 |       await logger.step(`Step 2 â€" Send GET /transaction/${txnId} and log response`, async () => {
  136 |         logger.info(`GET /api/v1/transaction/${txnId}`);
  137 |         res = await get(txnId);
  138 |         logger.pass('HTTP ' + res.status + ' received');
  139 |         logResponse('TC-SMK-007', txnId, res);
  140 |         allure.parameter('HTTP Status',  String(res.status));
  141 |         allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  142 |       });
  143 | 
  144 |       await logger.step('Step 3 â€" Validate HTTP 200 and core identifier fields', async () => {
  145 |         logger.info('Asserting: HTTP 200 and core identifier fields present');
> 146 |         expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                  ^ Error: HTTP 200 expected
  147 |         expect(res!.body,   'Body must be defined').toBeDefined();
  148 |         expect(res!.body.payment_trnx_id,            'payment_trnx_id must be present').toBeTruthy();
  149 |         expect(res!.body.transaction_confirmation_id, 'transaction_confirmation_id must be present').toBeTruthy();
  150 |         expect(res!.body.payment_reference,           'payment_reference must be present').toBeTruthy();
  151 |         expect(res!.body.invoice_id,                  'invoice_id must be present').toBeTruthy();
  152 | 
  153 |         // â"€â"€ ISSUE-3: payment_trnx_id and transaction_confirmation_id have the same value â"€â"€
  154 |         if (res!.body.payment_trnx_id === res!.body.transaction_confirmation_id) {
  155 |           flagIssue('TC-SMK-007', 'ISSUE-3',
  156 |             'payment_trnx_id === transaction_confirmation_id â€" these should be distinct identifiers',
  157 |             { payment_trnx_id: res!.body.payment_trnx_id, transaction_confirmation_id: res!.body.transaction_confirmation_id },
  158 |           );
  159 |         }
  160 |         expect.soft(
  161 |           res!.body.payment_trnx_id,
  162 |           '[ISSUE-3] payment_trnx_id should NOT equal transaction_confirmation_id',
  163 |         ).not.toBe(res!.body.transaction_confirmation_id);
  164 | 
  165 |         allure.parameter('payment_trnx_id',            String(res!.body.payment_trnx_id));
  166 |         allure.parameter('transaction_confirmation_id', String(res!.body.transaction_confirmation_id));
  167 |         allure.parameter('payment_reference',          String(res!.body.payment_reference));
  168 |         allure.parameter('invoice_id',                 String(res!.body.invoice_id));
  169 |         logger.pass('All assertions passed');
  170 |       });
  171 | 
  172 |       await logger.step('Step 4 â€" Validate core payment fields and status', async () => {
  173 |         logger.info('Asserting: payment_method, gateway_status, gateway_name present and status valid');
  174 |         expect(res!.body.payment_method, 'payment_method must be present').toBeTruthy();
  175 |         expect(res!.body.gateway_status, 'gateway_status must be present').toBeTruthy();
  176 |         expect(res!.body.gateway_name,   'gateway_name must be present').toBeTruthy();
  177 | 
  178 |         // gateway_status must be one of the known statuses (uppercase)
  179 |         expect(['SUCCESS', 'FAILED', 'PENDING'],
  180 |           `gateway_status "${res!.body.gateway_status}" must be SUCCESS/FAILED/PENDING`,
  181 |         ).toContain(res!.body.gateway_status);
  182 | 
  183 |         allure.parameter('payment_method', String(res!.body.payment_method));
  184 |         allure.parameter('gateway_status', String(res!.body.gateway_status));
  185 |         allure.parameter('gateway_name',   String(res!.body.gateway_name));
  186 |         logger.pass('All assertions passed');
  187 |       });
  188 | 
  189 |       await logger.step('Step 5 â€" Validate financial amounts', async () => {
  190 |         logger.info('Asserting: pg_amount_charged and net_settlement_amount types and ranges');
  191 |         const pgAmt  = res!.body.pg_amount_charged      as number;
  192 |         const netAmt = res!.body.net_settlement_amount  as number;
  193 | 
  194 |         expect(typeof pgAmt,  'pg_amount_charged must be a number').toBe('number');
  195 |         expect(typeof netAmt, 'net_settlement_amount must be a number').toBe('number');
  196 |         expect(pgAmt,         'pg_amount_charged must be > 0').toBeGreaterThan(0);
  197 |         expect(netAmt,        'net_settlement_amount must be >= 0').toBeGreaterThanOrEqual(0);
  198 |         expect(netAmt,        'net_settlement_amount must be <= pg_amount_charged (fees applied)').toBeLessThanOrEqual(pgAmt);
  199 | 
  200 |         allure.parameter('pg_amount_charged',      String(pgAmt));
  201 |         allure.parameter('net_settlement_amount',  String(netAmt));
  202 |         allure.parameter('fee_deducted',           String((pgAmt - netAmt).toFixed(2)));
  203 |         logger.pass('All assertions passed');
  204 |       });
  205 | 
  206 |       await logger.step('Step 6 â€" Validate trnx_date format and flag missing date_time field', async () => {
  207 |         logger.info('Asserting: trnx_date present and matches YYYY-MM-DD format');
  208 |         const trnxDate = res!.body.trnx_date as string;
  209 |         expect(trnxDate, 'trnx_date must be present').toBeTruthy();
  210 |         expect(DATE_REGEX.test(trnxDate), `trnx_date "${trnxDate}" must match YYYY-MM-DD`).toBe(true);
  211 | 
  212 |         // â"€â"€ ISSUE-4: detail has trnx_date (date only) but list has date_time (with time component)
  213 |         const hasDateTime = 'date_time' in res!.body;
  214 |         if (!hasDateTime) {
  215 |           flagIssue('TC-SMK-007', 'ISSUE-4',
  216 |             'date_time field missing in detail response â€" list returns date_time but detail only has trnx_date (ISSUE-5: different formats too)',
  217 |             { trnx_date: trnxDate, date_time: 'MISSING' },
  218 |           );
  219 |         }
  220 |         expect.soft(res!.body, '[ISSUE-4] detail should include date_time field like the list endpoint does').toHaveProperty('date_time');
  221 | 
  222 |         allure.parameter('trnx_date',       trnxDate);
  223 |         allure.parameter('date_time field', hasDateTime ? 'present' : 'MISSING âš  ISSUE-4/5');
  224 |         logger.pass('All assertions passed');
  225 |       });
  226 | 
  227 |       await logger.step('Step 7 â€" Validate payer information formats', async () => {
  228 |         logger.info('Asserting: payer_name, payer_phone, payer_email present and valid formats');
  229 |         const payer_email = String(res!.body.payer_email ?? '');
  230 |         const payer_phone = String(res!.body.payer_phone ?? '');
  231 |         const payer_name  = String(res!.body.payer_name  ?? '');
  232 | 
  233 |         expect(payer_name,  'payer_name must be present').toBeTruthy();
  234 |         expect(payer_phone, 'payer_phone must be present').toBeTruthy();
  235 |         expect(payer_email, 'payer_email must be present').toBeTruthy();
  236 | 
  237 |         expect(EMAIL_REGEX.test(payer_email),
  238 |           `payer_email "${payer_email}" must be a valid email address`).toBe(true);
  239 |         expect(INTL_PHONE_REGEX.test(payer_phone),
  240 |           `payer_phone "${payer_phone}" must be international format (+digits)`).toBe(true);
  241 | 
  242 |         allure.parameter('payer_name',  payer_name);
  243 |         allure.parameter('payer_phone', payer_phone);
  244 |         allure.parameter('payer_email', payer_email);
  245 |         logger.pass('All assertions passed');
  246 |       });
```