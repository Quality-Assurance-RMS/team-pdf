# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-status-currency.spec.ts >> Backend (Konza) â€" Dashboard Status & Currency >> TC-DSH-001 â€" Happy path: HTTP 200 with all four data sections present
- Location: tests/API/backend-tests/backend-dashboard-status-currency.spec.ts:164:7

# Error details

```
Error: [DASH-ISSUE-5] Response should NOT have a "data" wrapper â€" other APIs return top-level objects

expect(received).toBeGreaterThan(expected)

Expected: > 1
Received:   1
```

```
Error: byMethod section must be present

expect(received).toHaveProperty(path)

Expected path: "byMethod"
Received path: []

Received value: {"currencyDistribution": [{"amount": 127804, "count": 12770, "label": "KES", "percentage": 100}], "peakMetrics": {"avgTPM": 124, "avgTpmChange": 100, "avgTpmDirection": "up", "maxTPM": 717, "maxTpmChange": -45, "maxTpmDirection": "down", "peakHourWindow": "12:00 PM – 12:59 PM", "peakHourWindowChange": 100, "peakHourWindowDirection": "up", "peakTransactionDate": "2026-09-07", "peakTransactionsPerDay": 9474, "peakTxnDayChange": 100, "peakTxnDayDirection": "up"}, "statusDistribution": [{"amountKes": 127804, "count": 12770, "label": "Successful", "percentage": 100}, {"amountKes": 0, "count": 0, "label": "Failed", "percentage": 0}, {"amountKes": 0, "count": 0, "label": "Pending", "percentage": 0}]}
```

# Test source

```ts
  114 |     console.log(`    peakMetrics.peakTransactionsPerDay : "${pm.peakTransactionsPerDay}" (string âš  DASH-ISSUE-2)`);
  115 |     console.log(`    peakMetrics.peakHourWindow         : "${pm.peakHourWindow}"`);
  116 |     console.log(`    peakMetrics.avgTPM                 : "${pm.avgTPM}" (unit embedded âš  DASH-ISSUE-3)`);
  117 |     console.log(`    peakMetrics.maxTPM                 : "${pm.maxTPM}" (unit embedded âš  DASH-ISSUE-3)`);
  118 |     console.log(`    peakMetrics.peakTxnDayChange       : "${pm.peakTxnDayChange}" (string % âš  DASH-ISSUE-4)`);
  119 |     console.log(`    peakMetrics.peakTxnDayUp           : ${pm.peakTxnDayUp}`);
  120 |     console.log(`    peakMetrics.avgTpmChange           : "${pm.avgTpmChange}" (string % âš  DASH-ISSUE-4)`);
  121 |     console.log(`    peakMetrics.avgTpmUp               : ${pm.avgTpmUp}`);
  122 |     console.log(`    peakMetrics.maxTpmChange           : "${pm.maxTpmChange}" (string % âš  DASH-ISSUE-4)`);
  123 |     console.log(`    peakMetrics.maxTpmUp               : ${pm.maxTpmUp}`);
  124 |   }
  125 | 
  126 |   console.log(`  â–º Full Body:`);
  127 |   console.log(JSON.stringify(res.body, null, 4));
  128 |   console.log(`${'â"€'.repeat(70)}\n`);
  129 | 
  130 |   allure.attachment(
  131 |     `[${tcId}] Request & Response`,
  132 |     JSON.stringify({
  133 |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  134 |       response: { status: res.status, content_type: res.headers['content-type'] ?? 'N/A', body: res.body },
  135 |     }, null, 2),
  136 |     'application/json',
  137 |   );
  138 | }
  139 | 
  140 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  141 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  142 |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  143 |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  144 | }
  145 | 
  146 | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  147 | 
  148 | function backendLabels(tcId: string, subSuite: string, title: string, severity: 'blocker' | 'critical' | 'normal' = 'critical') {
  149 |   allure.parentSuite('RMS Dashboard API');
  150 |   allure.suite('Backend');
  151 |   allure.subSuite(subSuite);
  152 |   allure.label('testId',   tcId);
  153 |   allure.label('severity', severity);
  154 |   allure.owner('Ashil Shaji');
  155 |   allure.tags('Backend', 'Konza', 'API', 'Dashboard', 'StatusCurrency');
  156 |   allure.label('story', title);
  157 | }
  158 | 
  159 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  160 | 
  161 | test.describe('Backend (Konza) â€" Dashboard Status & Currency', () => {
  162 | 
  163 |   // â"€â"€ TC-DSH-001 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  164 |   test('TC-DSH-001 â€" Happy path: HTTP 200 with all four data sections present', async ({ logger }) => {
  165 |     backendLabels('TC-DSH-001', 'Status Currency Endpoint', 'Happy Path â€" Full Response Shape');
  166 | 
  167 |     let res: supertest.Response;
  168 | 
  169 |     await logger.step('Step 1 â€" Build request', async () => {
  170 |       allure.parameter('Base URL',  BASE_URL);
  171 |       allure.parameter('Endpoint',  ENDPOINT);
  172 |       allure.parameter('Method',    'GET');
  173 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  174 |       logger.info('Asserting: request parameters are recorded');
  175 |       logger.pass('All assertions passed');
  176 |     });
  177 | 
  178 |     await logger.step('Step 2 â€" Send GET /status-currency and log response', async () => {
  179 |       logger.info('GET /api/v1/dashboard/status-currency');
  180 |       res = await get();
  181 |       logger.pass('HTTP ' + res.status + ' received');
  182 |       logResponse('TC-DSH-001', {}, res);
  183 |       allure.parameter('HTTP Status',  String(res.status));
  184 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  185 |     });
  186 | 
  187 |     await logger.step('Step 3 â€" Validate HTTP 200 and Content-Type', async () => {
  188 |       logger.info('Asserting: HTTP 200, body defined, Content-Type is application/json');
  189 |       expect(res!.status,  'HTTP 200 expected').toBe(200);
  190 |       expect(res!.body,    'Response body must be defined').toBeDefined();
  191 |       expect(res!.headers['content-type'], 'Content-Type must include application/json')
  192 |         .toMatch(/application\/json/);
  193 |       logger.pass('All assertions passed');
  194 |     });
  195 | 
  196 |     await logger.step('Step 4 â€" Validate top-level `data` wrapper and all four sections', async () => {
  197 |       // â"€â"€ DASH-ISSUE-5: other APIs don't use a `data` wrapper â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  198 |       const hasDataWrapper = 'data' in res!.body;
  199 |       if (hasDataWrapper) {
  200 |         flagIssue('TC-DSH-001', 'DASH-ISSUE-5',
  201 |           'Response is wrapped in a "data" key â€" other APIs in this project (transaction-api, summary) return top-level objects directly, causing client inconsistency',
  202 |           Object.keys(res!.body),
  203 |         );
  204 |       }
  205 | 
  206 |       logger.info('Asserting: all four sections (statusDistribution, currencyDistribution, byMethod, peakMetrics) are present');
  207 |       expect.soft(Object.keys(res!.body).length,
  208 |         '[DASH-ISSUE-5] Response should NOT have a "data" wrapper â€" other APIs return top-level objects',
  209 |       ).toBeGreaterThan(1);
  210 | 
  211 |       const d = res!.body?.data ?? res!.body;
  212 |       expect(d, 'statusDistribution section must be present').toHaveProperty('statusDistribution');
  213 |       expect(d, 'currencyDistribution section must be present').toHaveProperty('currencyDistribution');
> 214 |       expect(d, 'byMethod section must be present').toHaveProperty('byMethod');
      |                                                     ^ Error: byMethod section must be present
  215 |       expect(d, 'peakMetrics section must be present').toHaveProperty('peakMetrics');
  216 | 
  217 |       allure.parameter('data wrapper present',     String(hasDataWrapper) + (hasDataWrapper ? ' âš  DASH-ISSUE-5' : ''));
  218 |       allure.parameter('statusDistribution count', String((d.statusDistribution as unknown[])?.length ?? 0));
  219 |       allure.parameter('currencyDistribution count', String((d.currencyDistribution as unknown[])?.length ?? 0));
  220 |       allure.parameter('byMethod count',           String((d.byMethod as unknown[])?.length ?? 0));
  221 |       allure.parameter('peakMetrics present',      String(!!d.peakMetrics));
  222 |       logger.pass('All assertions passed');
  223 |     });
  224 |   });
  225 | 
  226 |   // â"€â"€ TC-DSH-002 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  227 |   test('TC-DSH-002 â€" Auth guard: no token and invalid token both return 401', async ({ logger }) => {
  228 |     backendLabels('TC-DSH-002', 'Auth Guard', 'Auth Guard â€" Unauthorized Access', 'blocker');
  229 | 
  230 |     let resNoAuth: supertest.Response;
  231 |     let resInvalidAuth: supertest.Response;
  232 | 
  233 |     await logger.step('Step 1 â€" Build requests', async () => {
  234 |       allure.parameter('Base URL',   BASE_URL);
  235 |       allure.parameter('Endpoint',   ENDPOINT);
  236 |       allure.parameter('Method',     'GET');
  237 |       allure.parameter('Scenario A', 'No Authorization header');
  238 |       allure.parameter('Scenario B', 'Invalid Bearer token');
  239 |       logger.info('Asserting: request parameters are recorded');
  240 |       logger.pass('All assertions passed');
  241 |     });
  242 | 
  243 |     await logger.step('Step 2 â€" Send GET with no Authorization header', async () => {
  244 |       logger.info('GET /api/v1/dashboard/status-currency (no Authorization header)');
  245 |       resNoAuth = await getNoAuth();
  246 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  247 |       console.log(`\n[TC-DSH-002] No-auth response: status=${resNoAuth.status}, body=${JSON.stringify(resNoAuth.body)}`);
  248 |       allure.attachment('[TC-DSH-002-A] No-Auth Response',
  249 |         JSON.stringify({ status: resNoAuth.status, body: resNoAuth.body }, null, 2),
  250 |         'application/json',
  251 |       );
  252 |     });
  253 | 
  254 |     await logger.step('Step 3 â€" Validate no-auth returns 401 with error body', async () => {
  255 |       logger.info('Asserting: no-auth returns 401 with a non-empty error message');
  256 |       expect(resNoAuth!.status, 'No-auth request must return 401').toBe(401);
  257 |       expect(resNoAuth!.body,   'Error body must be present').toBeDefined();
  258 |       expect(resNoAuth!.body.status ?? resNoAuth!.body.statusCode,
  259 |         'Error body must include status 401').toBe(401);
  260 |       const errMsg = String(resNoAuth!.body.message ?? resNoAuth!.body.error ?? '').toLowerCase();
  261 |       expect(errMsg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  262 |       allure.parameter('No-auth HTTP status', String(resNoAuth!.status));
  263 |       allure.parameter('No-auth error msg',   String(resNoAuth!.body.message ?? resNoAuth!.body.error));
  264 |       logger.pass('All assertions passed');
  265 |     });
  266 | 
  267 |     await logger.step('Step 4 â€" Send GET with invalid Bearer token', async () => {
  268 |       logger.info('GET /api/v1/dashboard/status-currency (invalid Bearer token)');
  269 |       resInvalidAuth = await getInvalidAuth();
  270 |       logger.pass('HTTP ' + resInvalidAuth.status + ' received');
  271 |       console.log(`\n[TC-DSH-002] Invalid-auth response: status=${resInvalidAuth.status}, body=${JSON.stringify(resInvalidAuth.body)}`);
  272 |       allure.attachment('[TC-DSH-002-B] Invalid-Auth Response',
  273 |         JSON.stringify({ status: resInvalidAuth.status, body: resInvalidAuth.body }, null, 2),
  274 |         'application/json',
  275 |       );
  276 |     });
  277 | 
  278 |     await logger.step('Step 5 â€" Validate invalid-token returns 401 with error body', async () => {
  279 |       logger.info('Asserting: invalid-token returns 401 with a non-empty error message');
  280 |       expect(resInvalidAuth!.status, 'Invalid-token request must return 401').toBe(401);
  281 |       expect(resInvalidAuth!.body,   'Error body must be present for invalid token').toBeDefined();
  282 |       const errMsg = String(resInvalidAuth!.body.message ?? resInvalidAuth!.body.error ?? '').toLowerCase();
  283 |       expect(errMsg.length, 'Error message must be non-empty for invalid token').toBeGreaterThan(0);
  284 |       allure.parameter('Invalid-token HTTP status', String(resInvalidAuth!.status));
  285 |       allure.parameter('Invalid-token error msg',   String(resInvalidAuth!.body.message ?? resInvalidAuth!.body.error));
  286 |       logger.pass('All assertions passed');
  287 |     });
  288 |   });
  289 | 
  290 |   // â"€â"€ TC-DSH-003 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  291 |   test('TC-DSH-003 â€" statusDistribution: structure, fields, and label validation', async ({ logger }) => {
  292 |     backendLabels('TC-DSH-003', 'Status Currency Endpoint', 'statusDistribution â€" Field Validation');
  293 | 
  294 |     let res: supertest.Response;
  295 | 
  296 |     await logger.step('Step 1 â€" Build request', async () => {
  297 |       allure.parameter('Base URL', BASE_URL);
  298 |       allure.parameter('Endpoint', ENDPOINT);
  299 |       allure.parameter('Method',   'GET');
  300 |       allure.parameter('Auth',     `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  301 |       logger.info('Asserting: request parameters are recorded');
  302 |       logger.pass('All assertions passed');
  303 |     });
  304 | 
  305 |     await logger.step('Step 2 â€" Send GET /status-currency and log response', async () => {
  306 |       logger.info('GET /api/v1/dashboard/status-currency');
  307 |       res = await get();
  308 |       logger.pass('HTTP ' + res.status + ' received');
  309 |       logResponse('TC-DSH-003', {}, res);
  310 |       allure.parameter('HTTP Status', String(res.status));
  311 |     });
  312 | 
  313 |     await logger.step('Step 3 â€" Validate HTTP 200 and statusDistribution is a non-empty array', async () => {
  314 |       logger.info('Asserting: HTTP 200 and statusDistribution is a non-empty array');
```