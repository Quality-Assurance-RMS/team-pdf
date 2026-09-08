# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-001 — Summary: Default period (last-7-days)
- Location: tests/API/backend-tests/backend-transaction-api.spec.ts:131:7

# Error details

```
Error: total_volume field must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
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
  103 |   expect(res.status,       `${label} — expected HTTP 200, got ${res.status}`).toBe(200);
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
> 175 |       expect(body.total_volume, 'total_volume field must be present').toBeDefined();
      |                                                                       ^ Error: total_volume field must be present
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
  204 |       allure.parameter('success_rate.rate_pct',       String(rate.rate_pct) + '%');
  205 |       allure.parameter('success_rate.change_pct',     String(rate.change_pct));
  206 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  207 |     });
  208 | 
  209 |     await logger.step('Step 7 — Validate total_refunds object', async () => {
  210 |       const refunds = res!.body.total_refunds;
  211 |       logger.info('Asserting: total_refunds.amount is number, currency present');
  212 |       expect(typeof refunds.amount,  'total_refunds.amount must be a number').toBe('number');
  213 |       expect(refunds.currency,       'total_refunds.currency must be present').toBeTruthy();
  214 |       logger.pass('All assertions passed');
  215 |       allure.parameter('total_refunds.amount',   String(refunds.amount));
  216 |       allure.parameter('total_refunds.currency', String(refunds.currency));
  217 |     });
  218 |   });
  219 | 
  220 |   // ===========================================================================
  221 |   // TC-TXN-002 — Summary: Today
  222 |   // ===========================================================================
  223 |   test('TC-TXN-002 — Summary: Today', async ({ logger }) => {
  224 | 
  225 |     setCommonLabels('TC-TXN-002', 'Summary Endpoint', 'critical');
  226 |     await allure.description(
  227 |       'Verifies the GET /summary?period=today endpoint.\n\n' +
  228 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  229 |       '**Param:** period=today\n' +
  230 |       '**Expected:** HTTP 200 with non-empty summary body.\n\n' +
  231 |       'Validates the daily summary aggregation used on the RMS dashboard.',
  232 |     );
  233 | 
  234 |     let res: supertest.Response;
  235 | 
  236 |     await logger.step('Step 1 — Build request parameters', async () => {
  237 |       allure.parameter('Base URL',    BASE_URL);
  238 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  239 |       allure.parameter('Method',      'GET');
  240 |       allure.parameter('period',      'today');
  241 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  242 |     });
  243 | 
  244 |     await logger.step('Step 2 — Send GET /summary?period=today', async () => {
  245 |       logger.info('GET /api/v1/transaction/summary?period=today');
  246 |       res = await apiGet('/api/v1/transaction/summary', { period: 'today' });
  247 |       logger.pass('HTTP ' + res.status + ' received');
  248 |       attachRequestResponse('GET', '/api/v1/transaction/summary', { period: 'today' }, res);
  249 |       allure.parameter('HTTP Status',  String(res.status));
  250 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  251 |     });
  252 | 
  253 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  254 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  255 |       assertOk(res!, 'TC-TXN-002');
  256 |       logger.pass('All assertions passed');
  257 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  258 |     });
  259 | 
  260 |     await logger.step('Step 4 — Validate response body is not empty', async () => {
  261 |       const body = res!.body;
  262 |       logger.info('Asserting: body has at least one field');
  263 |       expect(Object.keys(body).length, 'Summary body should not be empty').toBeGreaterThan(0);
  264 |       logger.pass('All assertions passed');
  265 |       allure.parameter('Field count',  String(Object.keys(body).length));
  266 |       allure.parameter('Fields found', Object.keys(body).join(', '));
  267 |     });
  268 | 
  269 |     await logger.step('Step 5 — Validate total_volume fields', async () => {
  270 |       const vol = res!.body.total_volume;
  271 |       logger.info('Asserting: total_volume present with number amount and truthy currency');
  272 |       expect(vol,              'total_volume must be present').toBeDefined();
  273 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  274 |       expect(vol.currency,     'total_volume.currency must be present').toBeTruthy();
  275 |       logger.pass('All assertions passed');
```