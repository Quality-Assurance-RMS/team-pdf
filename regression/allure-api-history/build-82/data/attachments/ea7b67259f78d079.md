# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-003 — Summary: Custom date range (2026-01-01 → 2026-05-22)
- Location: tests/API/transaction-api.spec.ts:296:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
  247 |     });
  248 | 
  249 |     await logger.step('Step 4 — Validate response body is not empty', async () => {
  250 |       const body = res!.body;
  251 |       logger.info('Asserting: body has at least one field');
  252 |       expect(Object.keys(body).length, 'Summary body should not be empty').toBeGreaterThan(0);
  253 |       logger.pass('All assertions passed');
  254 |       allure.parameter('Field count',  String(Object.keys(body).length));
  255 |       allure.parameter('Fields found', Object.keys(body).join(', '));
  256 |     });
  257 | 
  258 |     await logger.step('Step 5 — Validate total_volume fields', async () => {
  259 |       const vol = res!.body.total_volume;
  260 |       logger.info('Asserting: total_volume present with number amount and truthy currency');
  261 |       expect(vol,              'total_volume must be present').toBeDefined();
  262 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  263 |       expect(vol.currency,     'total_volume.currency must be present').toBeTruthy();
  264 |       logger.pass('All assertions passed');
  265 |       allure.parameter('total_volume.amount',           String(vol.amount));
  266 |       allure.parameter('total_volume.currency',         String(vol.currency));
  267 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  268 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  269 |     });
  270 | 
  271 |     await logger.step('Step 6 — Validate success_rate is a valid percentage', async () => {
  272 |       const rate = res!.body.success_rate;
  273 |       logger.info('Asserting: success_rate present, rate_pct between 0 and 100');
  274 |       expect(rate,                    'success_rate must be present').toBeDefined();
  275 |       expect(rate.rate_pct,           'rate_pct must be >= 0').toBeGreaterThanOrEqual(0);
  276 |       expect(rate.rate_pct,           'rate_pct must be <= 100').toBeLessThanOrEqual(100);
  277 |       logger.pass('All assertions passed');
  278 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  279 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  280 |     });
  281 | 
  282 |     await logger.step('Step 7 — Validate total_refunds is a valid amount', async () => {
  283 |       const refunds = res!.body.total_refunds;
  284 |       logger.info('Asserting: total_refunds present, amount is a number');
  285 |       expect(refunds,                'total_refunds must be present').toBeDefined();
  286 |       expect(typeof refunds.amount,  'total_refunds.amount must be a number').toBe('number');
  287 |       logger.pass('All assertions passed');
  288 |       allure.parameter('total_refunds.amount',   String(refunds.amount));
  289 |       allure.parameter('total_refunds.currency', String(refunds.currency));
  290 |     });
  291 |   });
  292 | 
  293 |   // ===========================================================================
  294 |   // TC-TXN-003 — Summary: Custom date range
  295 |   // ===========================================================================
  296 |   test('TC-TXN-003 — Summary: Custom date range (2026-01-01 → 2026-05-22)', async ({ logger }) => {
  297 | 
  298 |     setCommonLabels('TC-TXN-003', 'Summary Endpoint — Custom Range', 'normal');
  299 |     await allure.description(
  300 |       'Verifies the GET /summary endpoint with period=custom and explicit date range.\n\n' +
  301 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  302 |       '**Params:** period=custom, from_date=2026-01-01, to_date=2026-05-22\n' +
  303 |       '**Expected:** HTTP 200 with valid summary data for the specified date range.\n\n' +
  304 |       'Validates flexible date-range filtering used for custom reporting periods.',
  305 |     );
  306 | 
  307 |     const params = { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-22' };
  308 |     let res: supertest.Response;
  309 | 
  310 |     await logger.step('Step 1 — Build request parameters', async () => {
  311 |       allure.parameter('Base URL',    BASE_URL);
  312 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  313 |       allure.parameter('Method',      'GET');
  314 |       allure.parameter('period',      'custom');
  315 |       allure.parameter('from_date',   '2026-01-01');
  316 |       allure.parameter('to_date',     '2026-05-22');
  317 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  318 |     });
  319 | 
  320 |     await logger.step('Step 2 — Send GET /summary?period=custom&from_date=...&to_date=...', async () => {
  321 |       logger.info('GET /api/v1/transaction/summary?period=custom&from_date=2026-01-01&to_date=2026-05-22');
  322 |       res = await apiGet('/api/v1/transaction/summary', params);
  323 |       logger.pass('HTTP ' + res.status + ' received');
  324 |       attachRequestResponse('GET', '/api/v1/transaction/summary', params, res);
  325 |       allure.parameter('HTTP Status',  String(res.status));
  326 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  327 |     });
  328 | 
  329 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  330 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  331 |       assertOk(res!, 'TC-TXN-003');
  332 |       logger.pass('All assertions passed');
  333 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  334 |     });
  335 | 
  336 |     await logger.step('Step 4 — Validate response body has content', async () => {
  337 |       const body = res!.body;
  338 |       logger.info('Asserting: body has at least one field');
  339 |       expect(Object.keys(body).length, 'Custom range body should not be empty').toBeGreaterThan(0);
  340 |       logger.pass('All assertions passed');
  341 |       allure.parameter('Fields present', Object.keys(body).join(', '));
  342 |     });
  343 | 
  344 |     await logger.step('Step 5 — Validate total_volume for custom range', async () => {
  345 |       const vol = res!.body.total_volume;
  346 |       logger.info('Asserting: total_volume present, amount is a number >= 0');
> 347 |       expect(vol,              'total_volume must be present').toBeDefined();
      |                                                                ^ Error: total_volume must be present
  348 |       expect(typeof vol.amount,'total_volume.amount must be a number').toBe('number');
  349 |       expect(vol.amount,       'total_volume.amount must be >= 0 for a 5-month range').toBeGreaterThanOrEqual(0);
  350 |       logger.pass('All assertions passed');
  351 |       allure.parameter('total_volume.amount',           String(vol.amount));
  352 |       allure.parameter('total_volume.currency',         String(vol.currency));
  353 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  354 |     });
  355 | 
  356 |     await logger.step('Step 6 — Validate success_rate for the range', async () => {
  357 |       const rate = res!.body.success_rate;
  358 |       logger.info('Asserting: success_rate.rate_pct between 0 and 100');
  359 |       expect(rate.rate_pct, 'rate_pct must be between 0 and 100').toBeGreaterThanOrEqual(0);
  360 |       expect(rate.rate_pct).toBeLessThanOrEqual(100);
  361 |       logger.pass('All assertions passed');
  362 |       allure.parameter('success_rate.rate_pct',       String(rate.rate_pct) + '%');
  363 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  364 |     });
  365 |   });
  366 | 
  367 |   // ===========================================================================
  368 |   // TC-TXN-004 — Transaction List: Default today (sorted by date desc)
  369 |   // ===========================================================================
  370 |   test('TC-TXN-004 — Transaction List: Default today (sorted by trnx_date desc)', async ({ logger }) => {
  371 | 
  372 |     setCommonLabels('TC-TXN-004', 'Transaction List', 'critical');
  373 |     await allure.description(
  374 |       'Verifies the paginated transaction list for period=today sorted by trnx_date desc.\n\n' +
  375 |       '**Endpoint:** GET /api/v1/transaction\n' +
  376 |       '**Params:** page=0, size=20, period=today, sort_by=trnx_date, sort_dir=desc\n' +
  377 |       '**Expected:** HTTP 200 with a paginated response containing total_count, page, size, total_pages, and data array.',
  378 |     );
  379 | 
  380 |     const params = { page: '0', size: '20', period: 'today', sort_by: 'trnx_date', sort_dir: 'desc' };
  381 |     let res: supertest.Response;
  382 | 
  383 |     await logger.step('Step 1 — Build request parameters', async () => {
  384 |       allure.parameter('Base URL',    BASE_URL);
  385 |       allure.parameter('Endpoint',    '/api/v1/transaction');
  386 |       allure.parameter('Method',      'GET');
  387 |       allure.parameter('page',        '0');
  388 |       allure.parameter('size',        '20');
  389 |       allure.parameter('period',      'today');
  390 |       allure.parameter('sort_by',     'trnx_date');
  391 |       allure.parameter('sort_dir',    'desc');
  392 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  393 |     });
  394 | 
  395 |     await logger.step('Step 2 — Send GET /transaction?period=today', async () => {
  396 |       logger.info('GET /api/v1/transaction?page=0&size=20&period=today&sort_by=trnx_date&sort_dir=desc');
  397 |       res = await apiGet('/api/v1/transaction', params);
  398 |       logger.pass('HTTP ' + res.status + ' received');
  399 |       attachRequestResponse('GET', '/api/v1/transaction', params, res);
  400 |       allure.parameter('HTTP Status',  String(res.status));
  401 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  402 |     });
  403 | 
  404 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  405 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  406 |       assertOk(res!, 'TC-TXN-004');
  407 |       logger.pass('All assertions passed');
  408 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  409 |     });
  410 | 
  411 |     await logger.step('Step 4 — Validate pagination metadata fields', async () => {
  412 |       const body = res!.body;
  413 |       logger.info('Asserting: total_count, page, size, total_pages present and total_count is number');
  414 |       expect(body.total_count,  'total_count must be present').toBeDefined();
  415 |       expect(body.page,         'page must be present').toBeDefined();
  416 |       expect(body.size,         'size must be present').toBeDefined();
  417 |       expect(body.total_pages,  'total_pages must be present').toBeDefined();
  418 |       expect(typeof body.total_count, 'total_count must be a number').toBe('number');
  419 |       logger.pass('All assertions passed');
  420 |       allure.parameter('total_count',  String(body.total_count));
  421 |       allure.parameter('page',         String(body.page));
  422 |       allure.parameter('size',         String(body.size));
  423 |       allure.parameter('total_pages',  String(body.total_pages));
  424 |     });
  425 | 
  426 |     await logger.step('Step 5 — Validate data array is returned', async () => {
  427 |       const body = res!.body;
  428 |       logger.info('Asserting: data is an array with at least 1 record');
  429 |       expect(Array.isArray(body.data), 'data must be an array').toBe(true);
  430 |       expect(body.data.length,         'data array must have at least 1 record').toBeGreaterThan(0);
  431 |       logger.pass('All assertions passed');
  432 |       allure.parameter('Records in page', String(body.data.length));
  433 |     });
  434 | 
  435 |     await logger.step('Step 6 — Validate first record has required fields', async () => {
  436 |       const record = res!.body.data[0] as Record<string, unknown>;
  437 |       logger.info('Asserting: first record has all required fields');
  438 |       expect(record.payment_trnx_id,          'payment_trnx_id must be present').toBeTruthy();
  439 |       expect(record.payment_reference,        'payment_reference must be present').toBeTruthy();
  440 |       expect(record.payment_method,           'payment_method must be present').toBeTruthy();
  441 |       expect(record.amount,                   'amount must be present').toBeDefined();
  442 |       expect(record.currency,                 'currency must be present').toBeTruthy();
  443 |       expect(record.display_status,           'display_status must be present').toBeTruthy();
  444 |       expect(record.date_time,                'date_time must be present').toBeTruthy();
  445 |       logger.pass('All assertions passed');
  446 |       allure.parameter('First record — payment_trnx_id',   String(record.payment_trnx_id));
  447 |       allure.parameter('First record — payment_method',    String(record.payment_method));
```