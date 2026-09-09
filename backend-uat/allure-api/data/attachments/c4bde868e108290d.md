# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-002 — Summary: Today
- Location: tests/API/backend-tests/backend-transaction-api.spec.ts:223:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
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
> 272 |       expect(vol,              'total_volume must be present').toBeDefined();
      |                                                                ^ Error: total_volume must be present
  273 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  274 |       expect(vol.currency,     'total_volume.currency must be present').toBeTruthy();
  275 |       logger.pass('All assertions passed');
  276 |       allure.parameter('total_volume.amount',           String(vol.amount));
  277 |       allure.parameter('total_volume.currency',         String(vol.currency));
  278 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  279 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  280 |     });
  281 | 
  282 |     await logger.step('Step 6 — Validate success_rate is a valid percentage', async () => {
  283 |       const rate = res!.body.success_rate;
  284 |       logger.info('Asserting: success_rate present, rate_pct between 0 and 100');
  285 |       expect(rate,                    'success_rate must be present').toBeDefined();
  286 |       expect(rate.rate_pct,           'rate_pct must be >= 0').toBeGreaterThanOrEqual(0);
  287 |       expect(rate.rate_pct,           'rate_pct must be <= 100').toBeLessThanOrEqual(100);
  288 |       logger.pass('All assertions passed');
  289 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  290 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  291 |     });
  292 | 
  293 |     await logger.step('Step 7 — Validate total_refunds is a valid amount', async () => {
  294 |       const refunds = res!.body.total_refunds;
  295 |       logger.info('Asserting: total_refunds present, amount is a number');
  296 |       expect(refunds,                'total_refunds must be present').toBeDefined();
  297 |       expect(typeof refunds.amount,  'total_refunds.amount must be a number').toBe('number');
  298 |       logger.pass('All assertions passed');
  299 |       allure.parameter('total_refunds.amount',   String(refunds.amount));
  300 |       allure.parameter('total_refunds.currency', String(refunds.currency));
  301 |     });
  302 |   });
  303 | 
  304 |   // ===========================================================================
  305 |   // TC-TXN-003 — Summary: Custom date range
  306 |   // ===========================================================================
  307 |   test('TC-TXN-003 — Summary: Custom date range (2026-01-01 → 2026-05-22)', async ({ logger }) => {
  308 | 
  309 |     setCommonLabels('TC-TXN-003', 'Summary Endpoint — Custom Range', 'normal');
  310 |     await allure.description(
  311 |       'Verifies the GET /summary endpoint with period=custom and explicit date range.\n\n' +
  312 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  313 |       '**Params:** period=custom, from_date=2026-01-01, to_date=2026-05-22\n' +
  314 |       '**Expected:** HTTP 200 with valid summary data for the specified date range.\n\n' +
  315 |       'Validates flexible date-range filtering used for custom reporting periods.',
  316 |     );
  317 | 
  318 |     const params = { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-22' };
  319 |     let res: supertest.Response;
  320 | 
  321 |     await logger.step('Step 1 — Build request parameters', async () => {
  322 |       allure.parameter('Base URL',    BASE_URL);
  323 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  324 |       allure.parameter('Method',      'GET');
  325 |       allure.parameter('period',      'custom');
  326 |       allure.parameter('from_date',   '2026-01-01');
  327 |       allure.parameter('to_date',     '2026-05-22');
  328 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  329 |     });
  330 | 
  331 |     await logger.step('Step 2 — Send GET /summary?period=custom&from_date=...&to_date=...', async () => {
  332 |       logger.info('GET /api/v1/transaction/summary?period=custom&from_date=2026-01-01&to_date=2026-05-22');
  333 |       res = await apiGet('/api/v1/transaction/summary', params);
  334 |       logger.pass('HTTP ' + res.status + ' received');
  335 |       attachRequestResponse('GET', '/api/v1/transaction/summary', params, res);
  336 |       allure.parameter('HTTP Status',  String(res.status));
  337 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  338 |     });
  339 | 
  340 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  341 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  342 |       assertOk(res!, 'TC-TXN-003');
  343 |       logger.pass('All assertions passed');
  344 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  345 |     });
  346 | 
  347 |     await logger.step('Step 4 — Validate response body has content', async () => {
  348 |       const body = res!.body;
  349 |       logger.info('Asserting: body has at least one field');
  350 |       expect(Object.keys(body).length, 'Custom range body should not be empty').toBeGreaterThan(0);
  351 |       logger.pass('All assertions passed');
  352 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  353 |     });
  354 | 
  355 |     await logger.step('Step 5 — Validate total_volume for custom range', async () => {
  356 |       const vol = res!.body.total_volume;
  357 |       logger.info('Asserting: total_volume present, amount is a number >= 0');
  358 |       expect(vol,              'total_volume must be present').toBeDefined();
  359 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  360 |       expect(vol.amount,       'total_volume.amount must be >= 0 for a 5-month range').toBeGreaterThanOrEqual(0);
  361 |       logger.pass('All assertions passed');
  362 |       allure.parameter('total_volume.amount',           String(vol.amount));
  363 |       allure.parameter('total_volume.currency',         String(vol.currency));
  364 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  365 |     });
  366 | 
  367 |     await logger.step('Step 6 — Validate success_rate for the range', async () => {
  368 |       const rate = res!.body.success_rate;
  369 |       logger.info('Asserting: success_rate.rate_pct between 0 and 100');
  370 |       expect(rate.rate_pct, 'rate_pct must be between 0 and 100').toBeGreaterThanOrEqual(0);
  371 |       expect(rate.rate_pct).toBeLessThanOrEqual(100);
  372 |       logger.pass('All assertions passed');
```