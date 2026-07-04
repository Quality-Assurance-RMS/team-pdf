# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-007 — Transaction Detail: Fetch by transaction ID >> TC-TXN-007 — Detail for ID: 00bba274-1617-4568-aaf2-a2ed08b66a18
- Location: tests/API/transaction-api.spec.ts:646:11

# Error details

```
Error: TC-TXN-007 [00bba274-1617-4568-aaf2-a2ed08b66a18] — expected HTTP 200, got 503

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 503
```

# Test source

```ts
  1   | /**
  2   |  * @file transaction-api.spec.ts
  3   |  * @feature Transaction API — Full Coverage
  4   |  *
  5   |  * Validates all Transaction API endpoints exposed at:
  6   |  *   https://api.rms.dev.demo-fsit.com/transaction-api/api/v1/transaction
  7   |  *
  8   |  * Test Cases:
  9   |  *   TC-TXN-001 — Summary: Default period (week)
  10  |  *   TC-TXN-002 — Summary: Today
  11  |  *   TC-TXN-003 — Summary: Custom date range
  12  |  *   TC-TXN-004 — Transaction List: Default (today, sorted by date desc)
  13  |  *   TC-TXN-005 — Transaction List: With status + method filters (SUCCESS + MPESA)
  14  |  *   TC-TXN-006 — Transaction List: Search by keyword
  15  |  *   TC-TXN-007 — Transaction Detail: Fetch by transaction ID
  16  |  *   TC-TXN-008 — Filter Options: Retrieve available filter values
  17  |  *   TC-TXN-009 — Security: 401 on missing Authorization header
  18  |  *
  19  |  * Auth: Bearer token fetched dynamically via POST /api/v1/auth/test/token using LOGIN_EMAIL.
  20  |  */
  21  | 
  22  | import { test, expect } from './fixtures';
  23  | import { allure }        from 'allure-playwright';
  24  | import supertest          from 'supertest';
  25  | import { getToken }       from '../utils/tokenHelper';
  26  | 
  27  | // ── Constants ─────────────────────────────────────────────────────────────────
  28  | 
  29  | const BASE_URL = process.env.TRANSACTION_API_BASE_URL
  30  |   ?? 'https://api.rms.dev.demo-fsit.com/transaction-api';
  31  | 
  32  | let BEARER_TOKEN = '';
  33  | 
  34  | test.beforeAll(async () => { BEARER_TOKEN = await getToken(); });
  35  | 
  36  | const API_DOCS_URL = 'https://api.rms.dev.demo-fsit.com/transaction-api/swagger-ui.html';
  37  | 
  38  | /** Real transaction IDs confirmed present in the environment */
  39  | const KNOWN_TXN_IDS = [
  40  |   '00bba274-1617-4568-aaf2-a2ed08b66a18',
  41  |   '2e8b4ea3-5413-4428-bc49-25de24680cdb',
  42  | ];
  43  | 
  44  | // ── Helpers ───────────────────────────────────────────────────────────────────
  45  | 
  46  | /**
  47  |  * Build an authenticated GET request via supertest.
  48  |  * Returns the supertest response (status + body available).
  49  |  */
  50  | async function apiGet(
  51  |   path: string,
  52  |   params: Record<string, string> = {},
  53  | ): Promise<supertest.Response> {
  54  |   const qs  = new URLSearchParams(params).toString();
  55  |   const url = qs ? `${path}?${qs}` : path;
  56  | 
  57  |   const res = await supertest(BASE_URL)
  58  |     .get(url)
  59  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  60  |     .set('Accept', 'application/json');
  61  | 
  62  |   console.log(`[GET ${url}] status: ${res.status} | body:`, JSON.stringify(res.body, null, 2));
  63  |   return res;
  64  | }
  65  | 
  66  | /** Attach full request + response as a formatted JSON attachment in Allure */
  67  | function attachRequestResponse(
  68  |   method: string,
  69  |   url: string,
  70  |   params: Record<string, string>,
  71  |   res: supertest.Response,
  72  | ) {
  73  |   const payload = {
  74  |     request: {
  75  |       method,
  76  |       url: `${BASE_URL}${url}`,
  77  |       params,
  78  |       headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' },
  79  |     },
  80  |     response: {
  81  |       status:  res.status,
  82  |       headers: res.headers,
  83  |       body:    res.body,
  84  |     },
  85  |   };
  86  |   allure.attachment(
  87  |     'Request & Response',
  88  |     JSON.stringify(payload, null, 2),
  89  |     'application/json',
  90  |   );
  91  | }
  92  | 
  93  | /** Assert the response is HTTP 200 and body is a non-null object */
  94  | function assertOk(res: supertest.Response, label: string) {
> 95  |   expect(res.status,       `${label} — expected HTTP 200, got ${res.status}`).toBe(200);
      |                                                                               ^ Error: TC-TXN-007 [00bba274-1617-4568-aaf2-a2ed08b66a18] — expected HTTP 200, got 503
  96  |   expect(res.body,         `${label} — body should be defined`).toBeDefined();
  97  |   expect(typeof res.body,  `${label} — body should be an object`).toBe('object');
  98  | }
  99  | 
  100 | // ── Common Allure metadata helper ─────────────────────────────────────────────
  101 | 
  102 | function setCommonLabels(tcId: string, story: string, severity: string = 'normal') {
  103 |   allure.parentSuite('RMS Transaction API');
  104 |   allure.suite('API Regression');
  105 |   allure.subSuite('Transaction Endpoints');
  106 |   allure.epic('Transaction Management');
  107 |   allure.label('feature',  'Transaction API');
  108 |   allure.label('story',    story);
  109 |   allure.label('severity', severity);
  110 |   allure.label('testId',   tcId);
  111 |   allure.owner('Ashil Shaji');
  112 |   allure.tags('API', 'Regression', 'Transaction');
  113 |   allure.link('link', API_DOCS_URL, 'API Docs');
  114 | }
  115 | 
  116 | // ── Test Suite ────────────────────────────────────────────────────────────────
  117 | 
  118 | test.describe('Transaction API — Full Coverage', () => {
  119 | 
  120 |   // ===========================================================================
  121 |   // TC-TXN-001 — Summary: Default period (week)
  122 |   // ===========================================================================
  123 |   test('TC-TXN-001 — Summary: Default period (week)', async ({ logger }) => {
  124 | 
  125 |     setCommonLabels('TC-TXN-001', 'Summary Endpoint', 'critical');
  126 |     await allure.description(
  127 |       'Verifies the GET /summary?period=week endpoint.\n\n' +
  128 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  129 |       '**Param:** period=week\n' +
  130 |       '**Expected:** HTTP 200 with total_volume, success_rate, total_refunds fields.\n\n' +
  131 |       'This validates the default weekly aggregation used on the RMS dashboard.',
  132 |     );
  133 | 
  134 |     let res: supertest.Response;
  135 | 
  136 |     await logger.step('Step 1 — Build request parameters', async () => {
  137 |       allure.parameter('Base URL',    BASE_URL);
  138 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  139 |       allure.parameter('Method',      'GET');
  140 |       allure.parameter('period',      'week');
  141 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  142 |     });
  143 | 
  144 |     await logger.step('Step 2 — Send GET /summary?period=week', async () => {
  145 |       logger.info('GET /api/v1/transaction/summary?period=week');
  146 |       res = await apiGet('/api/v1/transaction/summary', { period: 'week' });
  147 |       logger.pass('HTTP ' + res.status + ' received');
  148 |       attachRequestResponse('GET', '/api/v1/transaction/summary', { period: 'week' }, res);
  149 |       allure.parameter('HTTP Status',  String(res.status));
  150 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  151 |     });
  152 | 
  153 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  154 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  155 |       assertOk(res!, 'TC-TXN-001');
  156 |       logger.pass('All assertions passed');
  157 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  158 |     });
  159 | 
  160 |     await logger.step('Step 4 — Validate response body structure', async () => {
  161 |       const body = res!.body;
  162 |       logger.info('Asserting: total_volume, success_rate, total_refunds fields present');
  163 |       expect(body,              'Body must be an object').toBeDefined();
  164 |       expect(body.total_volume, 'total_volume field must be present').toBeDefined();
  165 |       expect(body.success_rate, 'success_rate field must be present').toBeDefined();
  166 |       expect(body.total_refunds,'total_refunds field must be present').toBeDefined();
  167 |       logger.pass('All assertions passed');
  168 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  169 |     });
  170 | 
  171 |     await logger.step('Step 5 — Validate total_volume object', async () => {
  172 |       const vol = res!.body.total_volume;
  173 |       logger.info('Asserting: total_volume.amount is number, currency present, change_direction valid');
  174 |       expect(typeof vol.amount,   'total_volume.amount must be a number').toBe('number');
  175 |       expect(vol.currency,        'total_volume.currency must be present').toBeTruthy();
  176 |       expect(['up', 'down', 'flat'], 'change_direction must be up/down/flat')
  177 |         .toContain(vol.change_direction);
  178 |       logger.pass('All assertions passed');
  179 |       allure.parameter('total_volume.amount',           String(vol.amount));
  180 |       allure.parameter('total_volume.currency',         String(vol.currency));
  181 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  182 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  183 |     });
  184 | 
  185 |     await logger.step('Step 6 — Validate success_rate object', async () => {
  186 |       const rate = res!.body.success_rate;
  187 |       logger.info('Asserting: success_rate.rate_pct is number between 0 and 100');
  188 |       expect(typeof rate.rate_pct, 'success_rate.rate_pct must be a number').toBe('number');
  189 |       expect(rate.rate_pct,        'success_rate.rate_pct must be between 0 and 100')
  190 |         .toBeGreaterThanOrEqual(0);
  191 |       expect(rate.rate_pct).toBeLessThanOrEqual(100);
  192 |       logger.pass('All assertions passed');
  193 |       allure.parameter('success_rate.rate_pct',       String(rate.rate_pct) + '%');
  194 |       allure.parameter('success_rate.change_pct',     String(rate.change_pct));
  195 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
```