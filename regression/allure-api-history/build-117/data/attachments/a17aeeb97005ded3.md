# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-003 — Summary: Custom date range (2026-01-01 → 2026-05-22)
- Location: tests/API/transaction-api.spec.ts:299:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
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
> 350 |       expect(vol,              'total_volume must be present').toBeDefined();
      |                                                                ^ Error: total_volume must be present
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
  365 |       allure.parameter('success_rate.rate_pct',       String(rate.rate_pct) + '%');
  366 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  367 |     });
  368 |   });
  369 | 
  370 |   // ===========================================================================
  371 |   // TC-TXN-004 — Transaction List: Default today (sorted by date desc)
  372 |   // ===========================================================================
  373 |   test('TC-TXN-004 — Transaction List: Default today (sorted by trnx_date desc)', async ({ logger }) => {
  374 | 
  375 |     setCommonLabels('TC-TXN-004', 'Transaction List', 'critical');
  376 |     await allure.description(
  377 |       'Verifies the paginated transaction list for period=today sorted by trnx_date desc.\n\n' +
  378 |       '**Endpoint:** GET /api/v1/transaction\n' +
  379 |       '**Params:** page=0, size=20, period=today, sort_by=trnx_date, sort_dir=desc\n' +
  380 |       '**Expected:** HTTP 200 with a paginated response containing total_count, page, size, total_pages, and data array.',
  381 |     );
  382 | 
  383 |     const params = { page: '0', size: '20', period: 'today', sort_by: 'trnx_date', sort_dir: 'desc' };
  384 |     let res: supertest.Response;
  385 | 
  386 |     await logger.step('Step 1 — Build request parameters', async () => {
  387 |       allure.parameter('Base URL',    BASE_URL);
  388 |       allure.parameter('Endpoint',    '/api/v1/transaction');
  389 |       allure.parameter('Method',      'GET');
  390 |       allure.parameter('page',        '0');
  391 |       allure.parameter('size',        '20');
  392 |       allure.parameter('period',      'today');
  393 |       allure.parameter('sort_by',     'trnx_date');
  394 |       allure.parameter('sort_dir',    'desc');
  395 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  396 |     });
  397 | 
  398 |     await logger.step('Step 2 — Send GET /transaction?period=today', async () => {
  399 |       logger.info('GET /api/v1/transaction?page=0&size=20&period=today&sort_by=trnx_date&sort_dir=desc');
  400 |       res = await apiGet('/api/v1/transaction', params);
  401 |       logger.pass('HTTP ' + res.status + ' received');
  402 |       attachRequestResponse('GET', '/api/v1/transaction', params, res);
  403 |       allure.parameter('HTTP Status',  String(res.status));
  404 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  405 |     });
  406 | 
  407 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  408 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  409 |       assertOk(res!, 'TC-TXN-004');
  410 |       logger.pass('All assertions passed');
  411 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
  412 |     });
  413 | 
  414 |     await logger.step('Step 4 — Validate pagination metadata fields', async () => {
  415 |       const body = res!.body;
  416 |       logger.info('Asserting: total_count, page, size, total_pages present and total_count is number');
  417 |       expect(body.total_count,  'total_count must be present').toBeDefined();
  418 |       expect(body.page,         'page must be present').toBeDefined();
  419 |       expect(body.size,         'size must be present').toBeDefined();
  420 |       expect(body.total_pages,  'total_pages must be present').toBeDefined();
  421 |       expect(typeof body.total_count, 'total_count must be a number').toBe('number');
  422 |       logger.pass('All assertions passed');
  423 |       allure.parameter('total_count',  String(body.total_count));
  424 |       allure.parameter('page',         String(body.page));
  425 |       allure.parameter('size',         String(body.size));
  426 |       allure.parameter('total_pages',  String(body.total_pages));
  427 |     });
  428 | 
  429 |     await logger.step('Step 5 — Validate data array is returned', async () => {
  430 |       const body = res!.body;
  431 |       logger.info('Asserting: data is an array with at least 1 record');
  432 |       expect(Array.isArray(body.data), 'data must be an array').toBe(true);
  433 |       expect(body.data.length,         'data array must have at least 1 record').toBeGreaterThan(0);
  434 |       logger.pass('All assertions passed');
  435 |       allure.parameter('Records in page', String(body.data.length));
  436 |     });
  437 | 
  438 |     await logger.step('Step 6 — Validate first record has required fields', async () => {
  439 |       const record = res!.body.data[0] as Record<string, unknown>;
  440 |       logger.info('Asserting: first record has all required fields');
  441 |       expect(record.payment_trnx_id,          'payment_trnx_id must be present').toBeTruthy();
  442 |       expect(record.payment_reference,        'payment_reference must be present').toBeTruthy();
  443 |       expect(record.payment_method,           'payment_method must be present').toBeTruthy();
  444 |       expect(record.amount,                   'amount must be present').toBeDefined();
  445 |       expect(record.currency,                 'currency must be present').toBeTruthy();
  446 |       expect(record.display_status,           'display_status must be present').toBeTruthy();
  447 |       expect(record.date_time,                'date_time must be present').toBeTruthy();
  448 |       logger.pass('All assertions passed');
  449 |       allure.parameter('First record — payment_trnx_id',   String(record.payment_trnx_id));
  450 |       allure.parameter('First record — payment_method',    String(record.payment_method));
```