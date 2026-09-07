# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-007 — Transaction Detail: Fetch by transaction ID >> TC-TXN-007 — Detail for ID: 00bba274-1617-4568-aaf2-a2ed08b66a18
- Location: tests/API/backend-tests/backend-transaction-api.spec.ts:657:11

# Error details

```
Error: TC-TXN-007 [00bba274-1617-4568-aaf2-a2ed08b66a18] — expected HTTP 200, got 404

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 404
```

# Test source

```ts
  3   |  * @feature Transaction API — Full Coverage (Konza UAT)
  4   |  *
  5   |  * Validates all Transaction API endpoints exposed at:
  6   |  *   https://rms.uat.konza/transaction-api/api/v1/transaction
  7   |  *
  8   |  * Test Cases:
  9   |  *   TC-TXN-001 — Summary: Default period (last-7-days)
  10  |  *   TC-TXN-002 — Summary: Today
  11  |  *   TC-TXN-003 — Summary: Custom date range
  12  |  *   TC-TXN-004 — Transaction List: Default (today, sorted by date desc)
  13  |  *   TC-TXN-005 — Transaction List: With status + method filters (SUCCESS + MPESA)
  14  |  *   TC-TXN-006 — Transaction List: Search by keyword
  15  |  *   TC-TXN-007 — Transaction Detail: Fetch by transaction ID
  16  |  *   TC-TXN-008 — Filter Options: Retrieve available filter values
  17  |  *   TC-TXN-009 — Security: 401 on missing Authorization header
  18  |  *
  19  |  * Auth: Bearer token fetched dynamically via POST /api/v1/auth/login using KONZA_LOGIN_EMAIL/KONZA_LOGIN_PASSWORD (Konza UAT).
  20  |  */
  21  | 
  22  | /**
  23  |  * NOTE (Konza migration): mirrored from the AWS environment spec for parity testing.
  24  |  * Some fixture values (transaction IDs, search keywords, date ranges) were captured
  25  |  * against the AWS demo-fsit dataset and may need to be updated to match real data
  26  |  * available in the Konza UAT environment before this suite is executed.
  27  |  */
  28  | 
  29  | 
  30  | import { test, expect } from '../fixtures';
  31  | import { allure }        from 'allure-playwright';
  32  | import supertest          from 'supertest';
  33  | import { getKonzaToken }       from '../../utils/konzaTokenHelper';
  34  | 
  35  | // ── Constants ─────────────────────────────────────────────────────────────────
  36  | 
  37  | const BASE_URL = process.env.KONZA_TRANSACTION_API_BASE_URL
  38  |   ?? 'https://rms.uat.konza/transaction-api';
  39  | 
  40  | let BEARER_TOKEN = '';
  41  | 
  42  | test.beforeAll(async () => { BEARER_TOKEN = await getKonzaToken(); });
  43  | 
  44  | const API_DOCS_URL = 'https://rms.uat.konza/transaction-api/swagger-ui.html';
  45  | 
  46  | /** Real transaction IDs confirmed present in the environment */
  47  | const KNOWN_TXN_IDS = [
  48  |   '00bba274-1617-4568-aaf2-a2ed08b66a18',
  49  |   '2e8b4ea3-5413-4428-bc49-25de24680cdb',
  50  | ];
  51  | 
  52  | // ── Helpers ───────────────────────────────────────────────────────────────────
  53  | 
  54  | /**
  55  |  * Build an authenticated GET request via supertest.
  56  |  * Returns the supertest response (status + body available).
  57  |  */
  58  | async function apiGet(
  59  |   path: string,
  60  |   params: Record<string, string> = {},
  61  | ): Promise<supertest.Response> {
  62  |   const qs  = new URLSearchParams(params).toString();
  63  |   const url = qs ? `${path}?${qs}` : path;
  64  | 
  65  |   const res = await supertest(BASE_URL)
  66  |     .get(url)
  67  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  68  |     .set('Accept', 'application/json');
  69  | 
  70  |   console.log(`[GET ${url}] status: ${res.status} | body:`, JSON.stringify(res.body, null, 2));
  71  |   return res;
  72  | }
  73  | 
  74  | /** Attach full request + response as a formatted JSON attachment in Allure */
  75  | function attachRequestResponse(
  76  |   method: string,
  77  |   url: string,
  78  |   params: Record<string, string>,
  79  |   res: supertest.Response,
  80  | ) {
  81  |   const payload = {
  82  |     request: {
  83  |       method,
  84  |       url: `${BASE_URL}${url}`,
  85  |       params,
  86  |       headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' },
  87  |     },
  88  |     response: {
  89  |       status:  res.status,
  90  |       headers: res.headers,
  91  |       body:    res.body,
  92  |     },
  93  |   };
  94  |   allure.attachment(
  95  |     'Request & Response',
  96  |     JSON.stringify(payload, null, 2),
  97  |     'application/json',
  98  |   );
  99  | }
  100 | 
  101 | /** Assert the response is HTTP 200 and body is a non-null object */
  102 | function assertOk(res: supertest.Response, label: string) {
> 103 |   expect(res.status,       `${label} — expected HTTP 200, got ${res.status}`).toBe(200);
      |                                                                               ^ Error: TC-TXN-007 [00bba274-1617-4568-aaf2-a2ed08b66a18] — expected HTTP 200, got 404
  104 |   expect(res.body,         `${label} — body should be defined`).toBeDefined();
  105 |   expect(typeof res.body,  `${label} — body should be an object`).toBe('object');
  106 | }
  107 | 
  108 | // ── Common Allure metadata helper ─────────────────────────────────────────────
  109 | 
  110 | function setCommonLabels(tcId: string, story: string, severity: string = 'normal') {
  111 |   allure.parentSuite('RMS Transaction API (Konza)');
  112 |   allure.suite('API Regression');
  113 |   allure.subSuite('Transaction Endpoints');
  114 |   allure.epic('Transaction Management');
  115 |   allure.label('feature',  'Transaction API');
  116 |   allure.label('story',    story);
  117 |   allure.label('severity', severity);
  118 |   allure.label('testId',   tcId);
  119 |   allure.owner('Ashil Shaji');
  120 |   allure.tags('API', 'Regression', 'Transaction', 'Konza');
  121 |   allure.link('link', API_DOCS_URL, 'API Docs');
  122 | }
  123 | 
  124 | // ── Test Suite ────────────────────────────────────────────────────────────────
  125 | 
  126 | test.describe('Transaction API — Full Coverage', () => {
  127 | 
  128 |   // ===========================================================================
  129 |   // TC-TXN-001 — Summary: Default period (last-7-days)
  130 |   // ===========================================================================
  131 |   test('TC-TXN-001 — Summary: Default period (last-7-days)', async ({ logger }) => {
  132 | 
  133 |     setCommonLabels('TC-TXN-001', 'Summary Endpoint', 'critical');
  134 |     await allure.description(
  135 |       'Verifies the GET /summary?period=last-7-days endpoint.\n\n' +
  136 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  137 |       '**Param:** period=last-7-days\n' +
  138 |       '**Expected:** HTTP 200 with total_volume, success_rate, total_refunds fields.\n\n' +
  139 |       'This validates the weekly aggregation used on the RMS dashboard.\n\n' +
  140 |       '**Note (RMSK-646):** `period=week` is not a valid API value (rejected with ' +
  141 |       '400 "Invalid period value") — the accepted values are today|last-7-days|' +
  142 |       'last-30-days|last-90-days|custom.',
  143 |     );
  144 | 
  145 |     let res: supertest.Response;
  146 | 
  147 |     await logger.step('Step 1 — Build request parameters', async () => {
  148 |       allure.parameter('Base URL',    BASE_URL);
  149 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  150 |       allure.parameter('Method',      'GET');
  151 |       allure.parameter('period',      'last-7-days');
  152 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  153 |     });
  154 | 
  155 |     await logger.step('Step 2 — Send GET /summary?period=last-7-days', async () => {
  156 |       logger.info('GET /api/v1/transaction/summary?period=last-7-days');
  157 |       res = await apiGet('/api/v1/transaction/summary', { period: 'last-7-days' });
  158 |       logger.pass('HTTP ' + res.status + ' received');
  159 |       attachRequestResponse('GET', '/api/v1/transaction/summary', { period: 'last-7-days' }, res);
  160 |       allure.parameter('HTTP Status',  String(res.status));
  161 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  162 |     });
  163 | 
  164 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  165 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  166 |       assertOk(res!, 'TC-TXN-001');
  167 |       logger.pass('All assertions passed');
  168 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  169 |     });
  170 | 
  171 |     await logger.step('Step 4 — Validate response body structure', async () => {
  172 |       const body = res!.body;
  173 |       logger.info('Asserting: total_volume, success_rate, total_refunds fields present');
  174 |       expect(body,              'Body must be an object').toBeDefined();
  175 |       expect(body.total_volume, 'total_volume field must be present').toBeDefined();
  176 |       expect(body.success_rate, 'success_rate field must be present').toBeDefined();
  177 |       expect(body.total_refunds,'total_refunds field must be present').toBeDefined();
  178 |       logger.pass('All assertions passed');
  179 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  180 |     });
  181 | 
  182 |     await logger.step('Step 5 — Validate total_volume object', async () => {
  183 |       const vol = res!.body.total_volume;
  184 |       logger.info('Asserting: total_volume.amount is number, currency present, change_direction valid');
  185 |       expect(typeof vol.amount,   'total_volume.amount must be a number').toBe('number');
  186 |       expect(vol.currency,        'total_volume.currency must be present').toBeTruthy();
  187 |       expect(['up', 'down', 'flat'], 'change_direction must be up/down/flat')
  188 |         .toContain(vol.change_direction);
  189 |       logger.pass('All assertions passed');
  190 |       allure.parameter('total_volume.amount',           String(vol.amount));
  191 |       allure.parameter('total_volume.currency',         String(vol.currency));
  192 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  193 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  194 |     });
  195 | 
  196 |     await logger.step('Step 6 — Validate success_rate object', async () => {
  197 |       const rate = res!.body.success_rate;
  198 |       logger.info('Asserting: success_rate.rate_pct is number between 0 and 100');
  199 |       expect(typeof rate.rate_pct, 'success_rate.rate_pct must be a number').toBe('number');
  200 |       expect(rate.rate_pct,        'success_rate.rate_pct must be between 0 and 100')
  201 |         .toBeGreaterThanOrEqual(0);
  202 |       expect(rate.rate_pct).toBeLessThanOrEqual(100);
  203 |       logger.pass('All assertions passed');
```