# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transaction-api.spec.ts >> Transaction API — Full Coverage >> TC-TXN-002 — Summary: Today
- Location: tests/API/transaction-api.spec.ts:212:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
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
  196 |     });
  197 | 
  198 |     await logger.step('Step 7 — Validate total_refunds object', async () => {
  199 |       const refunds = res!.body.total_refunds;
  200 |       logger.info('Asserting: total_refunds.amount is number, currency present');
  201 |       expect(typeof refunds.amount,  'total_refunds.amount must be a number').toBe('number');
  202 |       expect(refunds.currency,       'total_refunds.currency must be present').toBeTruthy();
  203 |       logger.pass('All assertions passed');
  204 |       allure.parameter('total_refunds.amount',   String(refunds.amount));
  205 |       allure.parameter('total_refunds.currency', String(refunds.currency));
  206 |     });
  207 |   });
  208 | 
  209 |   // ===========================================================================
  210 |   // TC-TXN-002 — Summary: Today
  211 |   // ===========================================================================
  212 |   test('TC-TXN-002 — Summary: Today', async ({ logger }) => {
  213 | 
  214 |     setCommonLabels('TC-TXN-002', 'Summary Endpoint', 'critical');
  215 |     await allure.description(
  216 |       'Verifies the GET /summary?period=today endpoint.\n\n' +
  217 |       '**Endpoint:** GET /api/v1/transaction/summary\n' +
  218 |       '**Param:** period=today\n' +
  219 |       '**Expected:** HTTP 200 with non-empty summary body.\n\n' +
  220 |       'Validates the daily summary aggregation used on the RMS dashboard.',
  221 |     );
  222 | 
  223 |     let res: supertest.Response;
  224 | 
  225 |     await logger.step('Step 1 — Build request parameters', async () => {
  226 |       allure.parameter('Base URL',    BASE_URL);
  227 |       allure.parameter('Endpoint',    '/api/v1/transaction/summary');
  228 |       allure.parameter('Method',      'GET');
  229 |       allure.parameter('period',      'today');
  230 |       allure.parameter('Auth',        'Bearer token present: ' + (BEARER_TOKEN ? 'YES' : 'NO'));
  231 |     });
  232 | 
  233 |     await logger.step('Step 2 — Send GET /summary?period=today', async () => {
  234 |       logger.info('GET /api/v1/transaction/summary?period=today');
  235 |       res = await apiGet('/api/v1/transaction/summary', { period: 'today' });
  236 |       logger.pass('HTTP ' + res.status + ' received');
  237 |       attachRequestResponse('GET', '/api/v1/transaction/summary', { period: 'today' }, res);
  238 |       allure.parameter('HTTP Status',  String(res.status));
  239 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  240 |     });
  241 | 
  242 |     await logger.step('Step 3 — Validate HTTP 200 status code', async () => {
  243 |       logger.info('Asserting: HTTP 200, body defined, body is object');
  244 |       assertOk(res!, 'TC-TXN-002');
  245 |       logger.pass('All assertions passed');
  246 |       allure.parameter('Status Assertion', 'PASS — HTTP 200 received');
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
> 261 |       expect(vol,              'total_volume must be present').toBeDefined();
      |                                                                ^ Error: total_volume must be present
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
  347 |       expect(vol,              'total_volume must be present').toBeDefined();
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
```