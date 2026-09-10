# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-transaction-list.spec.ts >> Backend (Konza) â€" Transaction List >> TC-BE-004 â€" List: period=today sorted by trnx_date desc returns paginated data
- Location: tests/API/backend-tests/backend-transaction-list.spec.ts:170:7

# Error details

```
Error: TC-BE-004 â€" HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  8   |  *
  9   |  * Known Issues (flagged with expect.soft):
  10  |  *   ISSUE-10 : List uses "ministry" â€" Detail uses "mcda_name" for same concept
  11  |  *   ISSUE-11 : List uses "department" â€" Detail uses "service_name" for same concept
  12  |  *   ISSUE-13 : display_status = "Success" (title case) vs gateway_status = "SUCCESS" (upper) in detail
  13  |  */
  14  | 
  15  | /**
  16  |  * NOTE (Konza migration): mirrored from the AWS environment spec for parity testing.
  17  |  * Some fixture values (transaction IDs, search keywords, date ranges) were captured
  18  |  * against the AWS demo-fsit dataset and may need to be updated to match real data
  19  |  * available in the Konza UAT environment before this suite is executed.
  20  |  */
  21  | 
  22  | 
  23  | import { test, expect } from '../fixtures';
  24  | import { allure }        from 'allure-playwright';
  25  | import supertest          from 'supertest';
  26  | import { getKonzaToken }       from '../../utils/konzaTokenHelper';
  27  | 
  28  | const BASE_URL     = process.env.KONZA_TRANSACTION_API_BASE_URL ?? 'https://rms.uat.konza/transaction-api';
  29  | let BEARER_TOKEN = '';
  30  | const ENDPOINT   = '/api/v1/transaction';
  31  | 
  32  | test.beforeAll(async () => { BEARER_TOKEN = await getKonzaToken(); });
  33  | 
  34  | const VALID_DISPLAY_STATUSES = ['Success', 'Failed', 'Pending'];
  35  | const VALID_PAYMENT_METHODS  = ['MPESA', 'CARD', 'BANK'];
  36  | 
  37  | // â"€â"€ HTTP helper â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  38  | 
  39  | function get(params: Record<string, string> = {}) {
  40  |   const qs  = new URLSearchParams(params).toString();
  41  |   const url = qs ? `${ENDPOINT}?${qs}` : ENDPOINT;
  42  |   return supertest(BASE_URL)
  43  |     .get(url)
  44  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  45  |     .set('Accept', 'application/json');
  46  | }
  47  | 
  48  | // â"€â"€ Logger â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  49  | 
  50  | function logResponse(tcId: string, params: Record<string, string>, res: supertest.Response) {
  51  |   const fullUrl = `${BASE_URL}${ENDPOINT}?${new URLSearchParams(params)}`;
  52  |   const body    = res.body as { total_count?: number; page?: number; size?: number; total_pages?: number; data?: unknown[] };
  53  | 
  54  |   console.log(`\n${'â"€'.repeat(70)}`);
  55  |   console.log(`[${tcId}] GET ${fullUrl}`);
  56  |   console.log(`  â–º Params         : ${JSON.stringify(params)}`);
  57  |   console.log(`  â–º Status         : ${res.status}`);
  58  |   console.log(`  â–º Content-Type   : ${res.headers['content-type'] ?? 'N/A'}`);
  59  |   console.log(`  â–º total_count    : ${body.total_count ?? 'N/A'}`);
  60  |   console.log(`  â–º page           : ${body.page        ?? 'N/A'}`);
  61  |   console.log(`  â–º size           : ${body.size        ?? 'N/A'}`);
  62  |   console.log(`  â–º total_pages    : ${body.total_pages ?? 'N/A'}`);
  63  |   console.log(`  â–º records in page: ${(body.data ?? []).length}`);
  64  |   if ((body.data ?? []).length > 0) {
  65  |     console.log(`  â–º First record   :`);
  66  |     console.log(JSON.stringify((body.data as unknown[])[0], null, 4));
  67  |   }
  68  |   console.log(`${'â"€'.repeat(70)}\n`);
  69  | 
  70  |   allure.attachment(
  71  |     `[${tcId}] Request & Response`,
  72  |     JSON.stringify({
  73  |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  74  |       response: {
  75  |         status: res.status, content_type: res.headers['content-type'] ?? 'N/A',
  76  |         total_count: body.total_count, page: body.page, size: body.size, total_pages: body.total_pages,
  77  |         records_in_page: (body.data ?? []).length,
  78  |         first_record: (body.data ?? [])[0] ?? null,
  79  |         full_body: res.body,
  80  |       },
  81  |     }, null, 2),
  82  |     'application/json',
  83  |   );
  84  | }
  85  | 
  86  | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  87  |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  88  |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  89  |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  90  | }
  91  | 
  92  | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  93  | 
  94  | function backendLabels(tcId: string, title: string) {
  95  |   allure.parentSuite('RMS Transaction API');
  96  |   allure.suite('Backend');
  97  |   allure.subSuite('Transaction List Endpoint');
  98  |   allure.label('testId',   tcId);
  99  |   allure.label('severity', 'critical');
  100 |   allure.owner('Ashil Shaji');
  101 |   allure.tags('Backend', 'Konza', 'API', 'TransactionList');
  102 |   allure.label('story', title);
  103 | }
  104 | 
  105 | // â"€â"€ Shared assertion â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  106 | 
  107 | function assertListShape(res: supertest.Response, label: string) {
> 108 |   expect(res.status,                   `${label} â€" HTTP 200 expected`).toBe(200);
      |                                                                          ^ Error: TC-BE-004 â€" HTTP 200 expected
  109 |   expect(res.body,                     `${label} â€" body must be defined`).toBeDefined();
  110 |   expect(typeof res.body.total_count,  `${label} â€" total_count must be a number`).toBe('number');
  111 |   expect(typeof res.body.page,         `${label} â€" page must be a number`).toBe('number');
  112 |   expect(typeof res.body.size,         `${label} â€" size must be a number`).toBe('number');
  113 |   expect(typeof res.body.total_pages,  `${label} â€" total_pages must be a number`).toBe('number');
  114 |   expect(Array.isArray(res.body.data), `${label} â€" data must be an array`).toBe(true);
  115 | }
  116 | 
  117 | function assertPaginationMath(res: supertest.Response, requestedPage: number, requestedSize: number, label: string) {
  118 |   const { total_count, page, size, total_pages } = res.body;
  119 |   const expectedTotalPages = Math.ceil(total_count / requestedSize);
  120 | 
  121 |   expect(page,       `${label} â€" page must equal requested page ${requestedPage}`).toBe(requestedPage);
  122 |   expect(size,       `${label} â€" size must equal requested size ${requestedSize}`).toBe(requestedSize);
  123 |   expect(total_pages, `${label} â€" total_pages must equal ceil(${total_count}/${requestedSize})=${expectedTotalPages}`).toBe(expectedTotalPages);
  124 |   expect(res.body.data.length, `${label} â€" records in page must be <= size`).toBeLessThanOrEqual(requestedSize);
  125 | }
  126 | 
  127 | function assertRecordShape(tcId: string, record: Record<string, unknown>, index: number) {
  128 |   const label = `${tcId} record[${index}]`;
  129 | 
  130 |   // Required identifier fields
  131 |   expect(record.payment_trnx_id,            `${label} â€" payment_trnx_id must be present`).toBeTruthy();
  132 |   expect(record.payment_reference,          `${label} â€" payment_reference must be present`).toBeTruthy();
  133 |   expect(record.transaction_confirmation_id, `${label} â€" transaction_confirmation_id must be present`).toBeTruthy();
  134 | 
  135 |   // Required payment fields
  136 |   expect(record.payment_method,  `${label} â€" payment_method must be present`).toBeTruthy();
  137 |   expect(VALID_PAYMENT_METHODS,  `${label} â€" payment_method must be MPESA/CARD/BANK`).toContain(record.payment_method);
  138 |   expect(record.display_status,  `${label} â€" display_status must be present`).toBeTruthy();
  139 |   expect(VALID_DISPLAY_STATUSES, `${label} â€" display_status must be Success/Failed/Pending`).toContain(record.display_status);
  140 | 
  141 |   // Amount
  142 |   expect(typeof record.amount, `${label} â€" amount must be a number`).toBe('number');
  143 |   expect(record.amount as number, `${label} â€" amount must be > 0`).toBeGreaterThan(0);
  144 |   expect(record.currency, `${label} â€" currency must be present`).toBeTruthy();
  145 |   expect(record.currency, `${label} â€" currency must be KES`).toBe('KES');
  146 | 
  147 |   // Timestamps
  148 |   expect(record.date_time, `${label} â€" date_time must be present`).toBeTruthy();
  149 | 
  150 |   // Allowed actions
  151 |   expect(Array.isArray(record.allowed_actions), `${label} â€" allowed_actions must be an array`).toBe(true);
  152 |   expect(record.allowed_actions as string[], `${label} â€" allowed_actions must contain VIEW`).toContain('VIEW');
  153 | 
  154 |   // ISSUE-10: list uses "ministry" while detail uses "mcda_name" for same concept
  155 |   expect.soft(record.ministry, `[ISSUE-10] ${label} â€" ministry field present in list (detail uses mcda_name)`).toBeTruthy();
  156 | 
  157 |   // ISSUE-11: list uses "department" while detail uses "service_name" for same concept
  158 |   expect.soft(record.department, `[ISSUE-11] ${label} â€" department field present in list (detail uses service_name)`).toBeTruthy();
  159 | 
  160 |   // ISSUE-13: display_status in list is "Success" (title case), gateway_status in detail is "SUCCESS"
  161 |   const statusUpper = String(record.display_status ?? '').toUpperCase();
  162 |   expect.soft(statusUpper, `[ISSUE-13] ${label} â€" display_status should match filter-options values (SUCCESS/FAILED/PENDING)`).toMatch(/^(SUCCESS|FAILED|PENDING)$/);
  163 | }
  164 | 
  165 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  166 | 
  167 | test.describe('Backend (Konza) â€" Transaction List', () => {
  168 | 
  169 |   // â"€â"€ TC-BE-004 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  170 |   test('TC-BE-004 â€" List: period=today sorted by trnx_date desc returns paginated data', async ({ logger }) => {
  171 |     backendLabels('TC-BE-004', 'Transaction List â€" Today');
  172 | 
  173 |     const params = { page: '0', size: '20', period: 'today', sort_by: 'trnx_date', sort_dir: 'desc' };
  174 |     let res: supertest.Response;
  175 | 
  176 |     await logger.step('Step 1 â€" Build request', async () => {
  177 |       allure.parameter('Base URL',  BASE_URL);
  178 |       allure.parameter('Endpoint',  ENDPOINT);
  179 |       allure.parameter('Method',    'GET');
  180 |       allure.parameter('period',    params.period);
  181 |       allure.parameter('sort_by',   params.sort_by);
  182 |       allure.parameter('sort_dir',  params.sort_dir);
  183 |       allure.parameter('page',      params.page);
  184 |       allure.parameter('size',      params.size);
  185 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  186 |     });
  187 | 
  188 |     await logger.step('Step 2 â€" Send GET /transaction?period=today and log response', async () => {
  189 |       logger.info('GET /api/v1/transaction?period=today&sort_by=trnx_date&sort_dir=desc');
  190 |       res = await get(params);
  191 |       logger.pass('HTTP ' + res.status + ' received');
  192 |       logResponse('TC-BE-004', params, res);
  193 |       allure.parameter('HTTP Status',  String(res.status));
  194 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  195 |     });
  196 | 
  197 |     await logger.step('Step 3 â€" Validate HTTP 200, pagination metadata, and data array', async () => {
  198 |       logger.info('Asserting: HTTP 200, pagination metadata present, and data array non-empty');
  199 |       assertListShape(res!, 'TC-BE-004');
  200 |       expect(res!.body.data.length, 'data array must contain at least one record').toBeGreaterThan(0);
  201 |       allure.parameter('total_count',     String(res!.body.total_count));
  202 |       allure.parameter('total_pages',     String(res!.body.total_pages));
  203 |       allure.parameter('Records in page', String(res!.body.data.length));
  204 |       logger.pass('All assertions passed');
  205 |     });
  206 | 
  207 |     await logger.step('Step 4 â€" Validate pagination math (total_pages = ceil(total_count / size))', async () => {
  208 |       logger.info('Asserting: pagination math total_pages = ceil(total_count / size)');
```