# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-transaction-list.spec.ts >> Smoke â€" Transaction List >> TC-SMK-004 â€" List: period=today sorted by trnx_date desc returns paginated data
- Location: tests/API/smoke/smoke-transaction-list.spec.ts:162:7

# Error details

```
Error: TC-SMK-004 â€" HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  1   | /**
  2   |  * @file smoke-transaction-list.spec.ts
  3   |  * @feature Transaction API â€" Transaction List Endpoint Smoke Tests
  4   |  *
  5   |  * TC-SMK-004  List: period=today, sorted desc             â†' HTTP 200 + paginated data
  6   |  * TC-SMK-005  List: period=month, status=SUCCESS, method=MPESA â†' HTTP 200 + all records match filters
  7   |  * TC-SMK-006  List: period=week, keyword search q=CONF-MAY26   â†' HTTP 200 + data
  8   |  *
  9   |  * Known Issues (flagged with expect.soft):
  10  |  *   ISSUE-10 : List uses "ministry" â€" Detail uses "mcda_name" for same concept
  11  |  *   ISSUE-11 : List uses "department" â€" Detail uses "service_name" for same concept
  12  |  *   ISSUE-13 : display_status = "Success" (title case) vs gateway_status = "SUCCESS" (upper) in detail
  13  |  */
  14  | 
  15  | import { test, expect } from '../fixtures';
  16  | import { allure }        from 'allure-playwright';
  17  | import supertest          from 'supertest';
  18  | import { getToken }       from '../../utils/tokenHelper';
  19  | 
  20  | const BASE_URL     = process.env.TRANSACTION_API_BASE_URL ?? 'https://api.rms.dev.demo-fsit.com/transaction-api';
  21  | let BEARER_TOKEN = '';
  22  | const ENDPOINT   = '/api/v1/transaction';
  23  | 
  24  | test.beforeAll(async () => { BEARER_TOKEN = await getToken(); });
  25  | 
  26  | const VALID_DISPLAY_STATUSES = ['Success', 'Failed', 'Pending'];
  27  | const VALID_PAYMENT_METHODS  = ['MPESA', 'CARD', 'BANK'];
  28  | 
  29  | // â"€â"€ HTTP helper â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  30  | 
  31  | function get(params: Record<string, string> = {}) {
  32  |   const qs  = new URLSearchParams(params).toString();
  33  |   const url = qs ? `${ENDPOINT}?${qs}` : ENDPOINT;
  34  |   return supertest(BASE_URL)
  35  |     .get(url)
  36  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  37  |     .set('Accept', 'application/json');
  38  | }
  39  | 
  40  | // â"€â"€ Logger â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  41  | 
  42  | function logResponse(tcId: string, params: Record<string, string>, res: supertest.Response) {
  43  |   const fullUrl = `${BASE_URL}${ENDPOINT}?${new URLSearchParams(params)}`;
  44  |   const body    = res.body as { total_count?: number; page?: number; size?: number; total_pages?: number; data?: unknown[] };
  45  | 
  46  |   console.log(`\n${'â"€'.repeat(70)}`);
  47  |   console.log(`[${tcId}] GET ${fullUrl}`);
  48  |   console.log(`  â–º Params         : ${JSON.stringify(params)}`);
  49  |   console.log(`  â–º Status         : ${res.status}`);
  50  |   console.log(`  â–º Content-Type   : ${res.headers['content-type'] ?? 'N/A'}`);
  51  |   console.log(`  â–º total_count    : ${body.total_count ?? 'N/A'}`);
  52  |   console.log(`  â–º page           : ${body.page        ?? 'N/A'}`);
  53  |   console.log(`  â–º size           : ${body.size        ?? 'N/A'}`);
  54  |   console.log(`  â–º total_pages    : ${body.total_pages ?? 'N/A'}`);
  55  |   console.log(`  â–º records in page: ${(body.data ?? []).length}`);
  56  |   if ((body.data ?? []).length > 0) {
  57  |     console.log(`  â–º First record   :`);
  58  |     console.log(JSON.stringify((body.data as unknown[])[0], null, 4));
  59  |   }
  60  |   console.log(`${'â"€'.repeat(70)}\n`);
  61  | 
  62  |   allure.attachment(
  63  |     `[${tcId}] Request & Response`,
  64  |     JSON.stringify({
  65  |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  66  |       response: {
  67  |         status: res.status, content_type: res.headers['content-type'] ?? 'N/A',
  68  |         total_count: body.total_count, page: body.page, size: body.size, total_pages: body.total_pages,
  69  |         records_in_page: (body.data ?? []).length,
  70  |         first_record: (body.data ?? [])[0] ?? null,
  71  |         full_body: res.body,
  72  |       },
  73  |     }, null, 2),
  74  |     'application/json',
  75  |   );
  76  | }
  77  | 
  78  | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  79  |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  80  |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  81  |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  82  | }
  83  | 
  84  | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  85  | 
  86  | function smokeLabels(tcId: string, title: string) {
  87  |   allure.parentSuite('RMS Transaction API');
  88  |   allure.suite('Smoke');
  89  |   allure.subSuite('Transaction List Endpoint');
  90  |   allure.label('testId',   tcId);
  91  |   allure.label('severity', 'critical');
  92  |   allure.owner('Ashil Shaji');
  93  |   allure.tags('Smoke', 'API', 'TransactionList');
  94  |   allure.label('story', title);
  95  | }
  96  | 
  97  | // â"€â"€ Shared assertion â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  98  | 
  99  | function assertListShape(res: supertest.Response, label: string) {
> 100 |   expect(res.status,                   `${label} â€" HTTP 200 expected`).toBe(200);
      |                                                                          ^ Error: TC-SMK-004 â€" HTTP 200 expected
  101 |   expect(res.body,                     `${label} â€" body must be defined`).toBeDefined();
  102 |   expect(typeof res.body.total_count,  `${label} â€" total_count must be a number`).toBe('number');
  103 |   expect(typeof res.body.page,         `${label} â€" page must be a number`).toBe('number');
  104 |   expect(typeof res.body.size,         `${label} â€" size must be a number`).toBe('number');
  105 |   expect(typeof res.body.total_pages,  `${label} â€" total_pages must be a number`).toBe('number');
  106 |   expect(Array.isArray(res.body.data), `${label} â€" data must be an array`).toBe(true);
  107 | }
  108 | 
  109 | function assertPaginationMath(res: supertest.Response, requestedPage: number, requestedSize: number, label: string) {
  110 |   const { total_count, page, size, total_pages } = res.body;
  111 |   const expectedTotalPages = Math.ceil(total_count / requestedSize);
  112 | 
  113 |   expect(page,       `${label} â€" page must equal requested page ${requestedPage}`).toBe(requestedPage);
  114 |   expect(size,       `${label} â€" size must equal requested size ${requestedSize}`).toBe(requestedSize);
  115 |   expect(total_pages, `${label} â€" total_pages must equal ceil(${total_count}/${requestedSize})=${expectedTotalPages}`).toBe(expectedTotalPages);
  116 |   expect(res.body.data.length, `${label} â€" records in page must be <= size`).toBeLessThanOrEqual(requestedSize);
  117 | }
  118 | 
  119 | function assertRecordShape(tcId: string, record: Record<string, unknown>, index: number) {
  120 |   const label = `${tcId} record[${index}]`;
  121 | 
  122 |   // Required identifier fields
  123 |   expect(record.payment_trnx_id,            `${label} â€" payment_trnx_id must be present`).toBeTruthy();
  124 |   expect(record.payment_reference,          `${label} â€" payment_reference must be present`).toBeTruthy();
  125 |   expect(record.transaction_confirmation_id, `${label} â€" transaction_confirmation_id must be present`).toBeTruthy();
  126 | 
  127 |   // Required payment fields
  128 |   expect(record.payment_method,  `${label} â€" payment_method must be present`).toBeTruthy();
  129 |   expect(VALID_PAYMENT_METHODS,  `${label} â€" payment_method must be MPESA/CARD/BANK`).toContain(record.payment_method);
  130 |   expect(record.display_status,  `${label} â€" display_status must be present`).toBeTruthy();
  131 |   expect(VALID_DISPLAY_STATUSES, `${label} â€" display_status must be Success/Failed/Pending`).toContain(record.display_status);
  132 | 
  133 |   // Amount
  134 |   expect(typeof record.amount, `${label} â€" amount must be a number`).toBe('number');
  135 |   expect(record.amount as number, `${label} â€" amount must be > 0`).toBeGreaterThan(0);
  136 |   expect(record.currency, `${label} â€" currency must be present`).toBeTruthy();
  137 |   expect(record.currency, `${label} â€" currency must be KES`).toBe('KES');
  138 | 
  139 |   // Timestamps
  140 |   expect(record.date_time, `${label} â€" date_time must be present`).toBeTruthy();
  141 | 
  142 |   // Allowed actions
  143 |   expect(Array.isArray(record.allowed_actions), `${label} â€" allowed_actions must be an array`).toBe(true);
  144 |   expect(record.allowed_actions as string[], `${label} â€" allowed_actions must contain VIEW`).toContain('VIEW');
  145 | 
  146 |   // ISSUE-10: list uses "ministry" while detail uses "mcda_name" for same concept
  147 |   expect.soft(record.ministry, `[ISSUE-10] ${label} â€" ministry field present in list (detail uses mcda_name)`).toBeTruthy();
  148 | 
  149 |   // ISSUE-11: list uses "department" while detail uses "service_name" for same concept
  150 |   expect.soft(record.department, `[ISSUE-11] ${label} â€" department field present in list (detail uses service_name)`).toBeTruthy();
  151 | 
  152 |   // ISSUE-13: display_status in list is "Success" (title case), gateway_status in detail is "SUCCESS"
  153 |   const statusUpper = String(record.display_status ?? '').toUpperCase();
  154 |   expect.soft(statusUpper, `[ISSUE-13] ${label} â€" display_status should match filter-options values (SUCCESS/FAILED/PENDING)`).toMatch(/^(SUCCESS|FAILED|PENDING)$/);
  155 | }
  156 | 
  157 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  158 | 
  159 | test.describe('Smoke â€" Transaction List', () => {
  160 | 
  161 |   // â"€â"€ TC-SMK-004 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  162 |   test('TC-SMK-004 â€" List: period=today sorted by trnx_date desc returns paginated data', async ({ logger }) => {
  163 |     smokeLabels('TC-SMK-004', 'Transaction List â€" Today');
  164 | 
  165 |     const params = { page: '0', size: '20', period: 'today', sort_by: 'trnx_date', sort_dir: 'desc' };
  166 |     let res: supertest.Response;
  167 | 
  168 |     await logger.step('Step 1 â€" Build request', async () => {
  169 |       allure.parameter('Base URL',  BASE_URL);
  170 |       allure.parameter('Endpoint',  ENDPOINT);
  171 |       allure.parameter('Method',    'GET');
  172 |       allure.parameter('period',    params.period);
  173 |       allure.parameter('sort_by',   params.sort_by);
  174 |       allure.parameter('sort_dir',  params.sort_dir);
  175 |       allure.parameter('page',      params.page);
  176 |       allure.parameter('size',      params.size);
  177 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  178 |     });
  179 | 
  180 |     await logger.step('Step 2 â€" Send GET /transaction?period=today and log response', async () => {
  181 |       logger.info('GET /api/v1/transaction?period=today&sort_by=trnx_date&sort_dir=desc');
  182 |       res = await get(params);
  183 |       logger.pass('HTTP ' + res.status + ' received');
  184 |       logResponse('TC-SMK-004', params, res);
  185 |       allure.parameter('HTTP Status',  String(res.status));
  186 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  187 |     });
  188 | 
  189 |     await logger.step('Step 3 â€" Validate HTTP 200, pagination metadata, and data array', async () => {
  190 |       logger.info('Asserting: HTTP 200, pagination metadata present, and data array non-empty');
  191 |       assertListShape(res!, 'TC-SMK-004');
  192 |       expect(res!.body.data.length, 'data array must contain at least one record').toBeGreaterThan(0);
  193 |       allure.parameter('total_count',     String(res!.body.total_count));
  194 |       allure.parameter('total_pages',     String(res!.body.total_pages));
  195 |       allure.parameter('Records in page', String(res!.body.data.length));
  196 |       logger.pass('All assertions passed');
  197 |     });
  198 | 
  199 |     await logger.step('Step 4 â€" Validate pagination math (total_pages = ceil(total_count / size))', async () => {
  200 |       logger.info('Asserting: pagination math total_pages = ceil(total_count / size)');
```