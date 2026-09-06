# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-002 — Summary: Today
- Location: tests/API/transaction-api.spec.ts:215:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
  164 |       const body = res!.body;
  165 |       logger.info('Asserting: total_volume, success_rate, total_refunds fields present');
  166 |       expect(body,              'Body must be an object').toBeDefined();
  167 |       expect(body.total_volume, 'total_volume field must be present').toBeDefined();
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
> 264 |       expect(vol,              'total_volume must be present').toBeDefined();
      |                                                                ^ Error: total_volume must be present
  265 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  266 |       expect(vol.currency,     'total_volume.currency must be present').toBeTruthy();
  267 |       logger.pass('All assertions passed');
  268 |       allure.parameter('total_volume.amount',           String(vol.amount));
  269 |       allure.parameter('total_volume.currency',         String(vol.currency));
  270 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  271 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  272 |     });
  273 | 
  274 |     await logger.step('Step 6 — Validate success_rate is a valid percentage', async () => {
  275 |       const rate = res!.body.success_rate;
  276 |       logger.info('Asserting: success_rate present, rate_pct between 0 and 100');
  277 |       expect(rate,                    'success_rate must be present').toBeDefined();
  278 |       expect(rate.rate_pct,           'rate_pct must be >= 0').toBeGreaterThanOrEqual(0);
  279 |       expect(rate.rate_pct,           'rate_pct must be <= 100').toBeLessThanOrEqual(100);
  280 |       logger.pass('All assertions passed');
  281 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  282 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  283 |     });
  284 | 
  285 |     await logger.step('Step 7 — Validate total_refunds is a valid amount', async () => {
  286 |       const refunds = res!.body.total_refunds;
  287 |       logger.info('Asserting: total_refunds present, amount is a number');
  288 |       expect(refunds,                'total_refunds must be present').toBeDefined();
  289 |       expect(typeof refunds.amount,  'total_refunds.amount must be a number').toBe('number');
  290 |       logger.pass('All assertions passed');
  291 |       allure.parameter('total_refunds.amount',   String(refunds.amount));
  292 |       allure.parameter('total_refunds.currency', String(refunds.currency));
  293 |     });
  294 |   });
  295 | 
  296 |   // ===========================================================================
  297 |   // TC-TXN-003 — Summary: Custom date range
  298 |   // ===========================================================================
  299 |   test('TC-TXN-003 — Summary: Custom date range (2026-01-01 → 2026-05-22)', async ({ logger }) => {
  300 | 
  301 |     setCommonLabels('TC-TXN-003', 'Summary Endpoint — Custom Range', 'normal');
  302 |     await allure.description(
  303 |       'Verifies the GET /summary endpoint with period=custom and explicit date range.\n\n' +
  304 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  305 |       '**Params:** period=custom, from_date=2026-01-01, to_date=2026-05-22\n' +
  306 |       '**Expected:** HTTP 200 with valid summary data for the specified date range.\n\n' +
  307 |       'Validates flexible date-range filtering used for custom reporting periods.',
  308 |     );
  309 | 
  310 |     const params = { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-22' };
  311 |     let res: supertest.Response;
  312 | 
  313 |     await logger.step('Step 1 — Build request parameters', async () => {
  314 |       allure.parameter('Base URL',    BASE_URL);
  315 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  316 |       allure.parameter('Method',      'GET');
  317 |       allure.parameter('period',      'custom');
  318 |       allure.parameter('from_date',   '2026-01-01');
  319 |       allure.parameter('to_date',     '2026-05-22');
  320 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  321 |     });
  322 | 
  323 |     await logger.step('Step 2 — Send GET /summary?period=custom&from_date=...&to_date=...', async () => {
  324 |       logger.info('GET /api/v1/transaction/summary?period=custom&from_date=2026-01-01&to_date=2026-05-22');
  325 |       res = await apiGet('/api/v1/transaction/summary', params);
  326 |       logger.pass('HTTP ' + res.status + ' received');
  327 |       attachRequestResponse('GET', '/api/v1/transaction/summary', params, res);
  328 |       allure.parameter('HTTP Status',  String(res.status));
  329 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  330 |     });
  331 | 
  332 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  333 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  334 |       assertOk(res!, 'TC-TXN-003');
  335 |       logger.pass('All assertions passed');
  336 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  337 |     });
  338 | 
  339 |     await logger.step('Step 4 — Validate response body has content', async () => {
  340 |       const body = res!.body;
  341 |       logger.info('Asserting: body has at least one field');
  342 |       expect(Object.keys(body).length, 'Custom range body should not be empty').toBeGreaterThan(0);
  343 |       logger.pass('All assertions passed');
  344 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  345 |     });
  346 | 
  347 |     await logger.step('Step 5 — Validate total_volume for custom range', async () => {
  348 |       const vol = res!.body.total_volume;
  349 |       logger.info('Asserting: total_volume present, amount is a number >= 0');
  350 |       expect(vol,              'total_volume must be present').toBeDefined();
  351 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  352 |       expect(vol.amount,       'total_volume.amount must be >= 0 for a 5-month range').toBeGreaterThanOrEqual(0);
  353 |       logger.pass('All assertions passed');
  354 |       allure.parameter('total_volume.amount',           String(vol.amount));
  355 |       allure.parameter('total_volume.currency',         String(vol.currency));
  356 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  357 |     });
  358 | 
  359 |     await logger.step('Step 6 — Validate success_rate for the range', async () => {
  360 |       const rate = res!.body.success_rate;
  361 |       logger.info('Asserting: success_rate.rate_pct between 0 and 100');
  362 |       expect(rate.rate_pct, 'rate_pct must be between 0 and 100').toBeGreaterThanOrEqual(0);
  363 |       expect(rate.rate_pct).toBeLessThanOrEqual(100);
  364 |       logger.pass('All assertions passed');
```