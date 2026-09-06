# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-001 — Summary: Default period (last-7-days)
- Location: tests/API/transaction-api.spec.ts:123:7

# Error details

```
Error: total_volume field must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
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
  95  |   expect(res.status,       `${label} — expected HTTP 200, got ${res.status}`).toBe(200);
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
  121 |   // TC-TXN-001 — Summary: Default period (last-7-days)
  122 |   // ===========================================================================
  123 |   test('TC-TXN-001 — Summary: Default period (last-7-days)', async ({ logger }) => {
  124 | 
  125 |     setCommonLabels('TC-TXN-001', 'Summary Endpoint', 'critical');
  126 |     await allure.description(
  127 |       'Verifies the GET /summary?period=last-7-days endpoint.\n\n' +
  128 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  129 |       '**Param:** period=last-7-days\n' +
  130 |       '**Expected:** HTTP 200 with total_volume, success_rate, total_refunds fields.\n\n' +
  131 |       'This validates the weekly aggregation used on the RMS dashboard.\n\n' +
  132 |       '**Note (RMSK-646):** `period=week` is not a valid API value (rejected with ' +
  133 |       '400 "Invalid period value") — the accepted values are today|last-7-days|' +
  134 |       'last-30-days|last-90-days|custom.',
  135 |     );
  136 | 
  137 |     let res: supertest.Response;
  138 | 
  139 |     await logger.step('Step 1 — Build request parameters', async () => {
  140 |       allure.parameter('Base URL',    BASE_URL);
  141 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  142 |       allure.parameter('Method',      'GET');
  143 |       allure.parameter('period',      'last-7-days');
  144 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  145 |     });
  146 | 
  147 |     await logger.step('Step 2 — Send GET /summary?period=last-7-days', async () => {
  148 |       logger.info('GET /api/v1/transaction/summary?period=last-7-days');
  149 |       res = await apiGet('/api/v1/transaction/summary', { period: 'last-7-days' });
  150 |       logger.pass('HTTP ' + res.status + ' received');
  151 |       attachRequestResponse('GET', '/api/v1/transaction/summary', { period: 'last-7-days' }, res);
  152 |       allure.parameter('HTTP Status',  String(res.status));
  153 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  154 |     });
  155 | 
  156 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  157 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  158 |       assertOk(res!, 'TC-TXN-001');
  159 |       logger.pass('All assertions passed');
  160 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  161 |     });
  162 | 
  163 |     await logger.step('Step 4 — Validate response body structure', async () => {
  164 |       const body = res!.body;
  165 |       logger.info('Asserting: total_volume, success_rate, total_refunds fields present');
  166 |       expect(body,              'Body must be an object').toBeDefined();
> 167 |       expect(body.total_volume, 'total_volume field must be present').toBeDefined();
      |                                                                       ^ Error: total_volume field must be present
  168 |       expect(body.success_rate, 'success_rate field must be present').toBeDefined();
  169 |       expect(body.total_refunds,'total_refunds field must be present').toBeDefined();
  170 |       logger.pass('All assertions passed');
  171 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  172 |     });
  173 | 
  174 |     await logger.step('Step 5 — Validate total_volume object', async () => {
  175 |       const vol = res!.body.total_volume;
  176 |       logger.info('Asserting: total_volume.amount is number, currency present, change_direction valid');
  177 |       expect(typeof vol.amount,   'total_volume.amount must be a number').toBe('number');
  178 |       expect(vol.currency,        'total_volume.currency must be present').toBeTruthy();
  179 |       expect(['up', 'down', 'flat'], 'change_direction must be up/down/flat')
  180 |         .toContain(vol.change_direction);
  181 |       logger.pass('All assertions passed');
  182 |       allure.parameter('total_volume.amount',           String(vol.amount));
  183 |       allure.parameter('total_volume.currency',         String(vol.currency));
  184 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  185 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  186 |     });
  187 | 
  188 |     await logger.step('Step 6 — Validate success_rate object', async () => {
  189 |       const rate = res!.body.success_rate;
  190 |       logger.info('Asserting: success_rate.rate_pct is number between 0 and 100');
  191 |       expect(typeof rate.rate_pct, 'success_rate.rate_pct must be a number').toBe('number');
  192 |       expect(rate.rate_pct,        'success_rate.rate_pct must be between 0 and 100')
  193 |         .toBeGreaterThanOrEqual(0);
  194 |       expect(rate.rate_pct).toBeLessThanOrEqual(100);
  195 |       logger.pass('All assertions passed');
  196 |       allure.parameter('success_rate.rate_pct',       String(rate.rate_pct) + '%');
  197 |       allure.parameter('success_rate.change_pct',     String(rate.change_pct));
  198 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  199 |     });
  200 | 
  201 |     await logger.step('Step 7 — Validate total_refunds object', async () => {
  202 |       const refunds = res!.body.total_refunds;
  203 |       logger.info('Asserting: total_refunds.amount is number, currency present');
  204 |       expect(typeof refunds.amount,  'total_refunds.amount must be a number').toBe('number');
  205 |       expect(refunds.currency,       'total_refunds.currency must be present').toBeTruthy();
  206 |       logger.pass('All assertions passed');
  207 |       allure.parameter('total_refunds.amount',   String(refunds.amount));
  208 |       allure.parameter('total_refunds.currency', String(refunds.currency));
  209 |     });
  210 |   });
  211 | 
  212 |   // ===========================================================================
  213 |   // TC-TXN-002 — Summary: Today
  214 |   // ===========================================================================
  215 |   test('TC-TXN-002 — Summary: Today', async ({ logger }) => {
  216 | 
  217 |     setCommonLabels('TC-TXN-002', 'Summary Endpoint', 'critical');
  218 |     await allure.description(
  219 |       'Verifies the GET /summary?period=today endpoint.\n\n' +
  220 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  221 |       '**Param:** period=today\n' +
  222 |       '**Expected:** HTTP 200 with non-empty summary body.\n\n' +
  223 |       'Validates the daily summary aggregation used on the RMS dashboard.',
  224 |     );
  225 | 
  226 |     let res: supertest.Response;
  227 | 
  228 |     await logger.step('Step 1 — Build request parameters', async () => {
  229 |       allure.parameter('Base URL',    BASE_URL);
  230 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  231 |       allure.parameter('Method',      'GET');
  232 |       allure.parameter('period',      'today');
  233 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  234 |     });
  235 | 
  236 |     await logger.step('Step 2 — Send GET /summary?period=today', async () => {
  237 |       logger.info('GET /api/v1/transaction/summary?period=today');
  238 |       res = await apiGet('/api/v1/transaction/summary', { period: 'today' });
  239 |       logger.pass('HTTP ' + res.status + ' received');
  240 |       attachRequestResponse('GET', '/api/v1/transaction/summary', { period: 'today' }, res);
  241 |       allure.parameter('HTTP Status',  String(res.status));
  242 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  243 |     });
  244 | 
  245 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  246 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  247 |       assertOk(res!, 'TC-TXN-002');
  248 |       logger.pass('All assertions passed');
  249 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  250 |     });
  251 | 
  252 |     await logger.step('Step 4 — Validate response body is not empty', async () => {
  253 |       const body = res!.body;
  254 |       logger.info('Asserting: body has at least one field');
  255 |       expect(Object.keys(body).length, 'Summary body should not be empty').toBeGreaterThan(0);
  256 |       logger.pass('All assertions passed');
  257 |       allure.parameter('Field count',  String(Object.keys(body).length));
  258 |       allure.parameter('Fields found', Object.keys(body).join(', '));
  259 |     });
  260 | 
  261 |     await logger.step('Step 5 — Validate total_volume fields', async () => {
  262 |       const vol = res!.body.total_volume;
  263 |       logger.info('Asserting: total_volume present with number amount and truthy currency');
  264 |       expect(vol,              'total_volume must be present').toBeDefined();
  265 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  266 |       expect(vol.currency,     'total_volume.currency must be present').toBeTruthy();
  267 |       logger.pass('All assertions passed');
```