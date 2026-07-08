# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-status-currency.spec.ts >> Smoke â€" Dashboard Status & Currency >> TC-DSH-003 â€" statusDistribution: structure, fields, and label validation
- Location: tests/API/smoke/smoke-dashboard-status-currency.spec.ts:283:7

# Error details

```
Error: item[0] must have "amount" field

expect(received).toHaveProperty(path)

Expected path: "amount"
Received path: []

Received value: {"amountKes": 432179000, "count": 85580, "label": "Successful", "percentage": 100}
```

# Test source

```ts
  224 | 
  225 |     await logger.step('Step 1 â€" Build requests', async () => {
  226 |       allure.parameter('Base URL',   BASE_URL);
  227 |       allure.parameter('Endpoint',   ENDPOINT);
  228 |       allure.parameter('Method',     'GET');
  229 |       allure.parameter('Scenario A', 'No Authorization header');
  230 |       allure.parameter('Scenario B', 'Invalid Bearer token');
  231 |       logger.info('Asserting: request parameters are recorded');
  232 |       logger.pass('All assertions passed');
  233 |     });
  234 | 
  235 |     await logger.step('Step 2 â€" Send GET with no Authorization header', async () => {
  236 |       logger.info('GET /api/v1/dashboard/status-currency (no Authorization header)');
  237 |       resNoAuth = await getNoAuth();
  238 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  239 |       console.log(`\n[TC-DSH-002] No-auth response: status=${resNoAuth.status}, body=${JSON.stringify(resNoAuth.body)}`);
  240 |       allure.attachment('[TC-DSH-002-A] No-Auth Response',
  241 |         JSON.stringify({ status: resNoAuth.status, body: resNoAuth.body }, null, 2),
  242 |         'application/json',
  243 |       );
  244 |     });
  245 | 
  246 |     await logger.step('Step 3 â€" Validate no-auth returns 401 with error body', async () => {
  247 |       logger.info('Asserting: no-auth returns 401 with a non-empty error message');
  248 |       expect(resNoAuth!.status, 'No-auth request must return 401').toBe(401);
  249 |       expect(resNoAuth!.body,   'Error body must be present').toBeDefined();
  250 |       expect(resNoAuth!.body.status ?? resNoAuth!.body.statusCode,
  251 |         'Error body must include status 401').toBe(401);
  252 |       const errMsg = String(resNoAuth!.body.message ?? resNoAuth!.body.error ?? '').toLowerCase();
  253 |       expect(errMsg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  254 |       allure.parameter('No-auth HTTP status', String(resNoAuth!.status));
  255 |       allure.parameter('No-auth error msg',   String(resNoAuth!.body.message ?? resNoAuth!.body.error));
  256 |       logger.pass('All assertions passed');
  257 |     });
  258 | 
  259 |     await logger.step('Step 4 â€" Send GET with invalid Bearer token', async () => {
  260 |       logger.info('GET /api/v1/dashboard/status-currency (invalid Bearer token)');
  261 |       resInvalidAuth = await getInvalidAuth();
  262 |       logger.pass('HTTP ' + resInvalidAuth.status + ' received');
  263 |       console.log(`\n[TC-DSH-002] Invalid-auth response: status=${resInvalidAuth.status}, body=${JSON.stringify(resInvalidAuth.body)}`);
  264 |       allure.attachment('[TC-DSH-002-B] Invalid-Auth Response',
  265 |         JSON.stringify({ status: resInvalidAuth.status, body: resInvalidAuth.body }, null, 2),
  266 |         'application/json',
  267 |       );
  268 |     });
  269 | 
  270 |     await logger.step('Step 5 â€" Validate invalid-token returns 401 with error body', async () => {
  271 |       logger.info('Asserting: invalid-token returns 401 with a non-empty error message');
  272 |       expect(resInvalidAuth!.status, 'Invalid-token request must return 401').toBe(401);
  273 |       expect(resInvalidAuth!.body,   'Error body must be present for invalid token').toBeDefined();
  274 |       const errMsg = String(resInvalidAuth!.body.message ?? resInvalidAuth!.body.error ?? '').toLowerCase();
  275 |       expect(errMsg.length, 'Error message must be non-empty for invalid token').toBeGreaterThan(0);
  276 |       allure.parameter('Invalid-token HTTP status', String(resInvalidAuth!.status));
  277 |       allure.parameter('Invalid-token error msg',   String(resInvalidAuth!.body.message ?? resInvalidAuth!.body.error));
  278 |       logger.pass('All assertions passed');
  279 |     });
  280 |   });
  281 | 
  282 |   // â"€â"€ TC-DSH-003 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  283 |   test('TC-DSH-003 â€" statusDistribution: structure, fields, and label validation', async ({ logger }) => {
  284 |     smokeLabels('TC-DSH-003', 'Status Currency Endpoint', 'statusDistribution â€" Field Validation');
  285 | 
  286 |     let res: supertest.Response;
  287 | 
  288 |     await logger.step('Step 1 â€" Build request', async () => {
  289 |       allure.parameter('Base URL', BASE_URL);
  290 |       allure.parameter('Endpoint', ENDPOINT);
  291 |       allure.parameter('Method',   'GET');
  292 |       allure.parameter('Auth',     `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  293 |       logger.info('Asserting: request parameters are recorded');
  294 |       logger.pass('All assertions passed');
  295 |     });
  296 | 
  297 |     await logger.step('Step 2 â€" Send GET /status-currency and log response', async () => {
  298 |       logger.info('GET /api/v1/dashboard/status-currency');
  299 |       res = await get();
  300 |       logger.pass('HTTP ' + res.status + ' received');
  301 |       logResponse('TC-DSH-003', {}, res);
  302 |       allure.parameter('HTTP Status', String(res.status));
  303 |     });
  304 | 
  305 |     await logger.step('Step 3 â€" Validate HTTP 200 and statusDistribution is a non-empty array', async () => {
  306 |       logger.info('Asserting: HTTP 200 and statusDistribution is a non-empty array');
  307 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  308 |       const d    = res!.body?.data ?? res!.body;
  309 |       const dist = d.statusDistribution as Array<Record<string, unknown>>;
  310 |       expect(Array.isArray(dist), 'statusDistribution must be an array').toBe(true);
  311 |       expect(dist.length,         'statusDistribution must not be empty').toBeGreaterThan(0);
  312 |       allure.parameter('statusDistribution item count', String(dist.length));
  313 |       logger.pass('All assertions passed');
  314 |     });
  315 | 
  316 |     await logger.step('Step 4 â€" Validate each statusDistribution item has required fields', async () => {
  317 |       const d    = res!.body?.data ?? res!.body;
  318 |       const dist = d.statusDistribution as Array<Record<string, unknown>>;
  319 | 
  320 |       logger.info('Asserting: each statusDistribution item has label, percentage, amount, count, and color fields with correct types');
  321 |       for (const [i, item] of dist.entries()) {
  322 |         expect(item, `item[${i}] must have "label" field`).toHaveProperty('label');
  323 |         expect(item, `item[${i}] must have "percentage" field`).toHaveProperty('percentage');
> 324 |         expect(item, `item[${i}] must have "amount" field`).toHaveProperty('amount');
      |                                                             ^ Error: item[0] must have "amount" field
  325 |         expect(item, `item[${i}] must have "count" field`).toHaveProperty('count');
  326 |         expect(item, `item[${i}] must have "color" field`).toHaveProperty('color');
  327 | 
  328 |         expect(typeof item.label,      `item[${i}].label must be a string`).toBe('string');
  329 |         expect(typeof item.percentage, `item[${i}].percentage must be a number`).toBe('number');
  330 |         expect(item.percentage as number, `item[${i}].percentage must be >= 0`).toBeGreaterThanOrEqual(0);
  331 |         expect(item.percentage as number, `item[${i}].percentage must be <= 100`).toBeLessThanOrEqual(100);
  332 | 
  333 |         // â"€â"€ DASH-ISSUE-2: amount and count are strings, should be numbers â"€â"€â"€â"€â"€â"€â"€â"€â"€
  334 |         if (typeof item.amount === 'string') {
  335 |           flagIssue('TC-DSH-003', 'DASH-ISSUE-2',
  336 |             `statusDistribution[${i}].amount is a locale-formatted string â€" should be a number for client-side arithmetic`,
  337 |             item.amount,
  338 |           );
  339 |         }
  340 |         expect.soft(typeof item.amount,
  341 |           `[DASH-ISSUE-2] statusDistribution[${i}].amount should be a number, not a string`,
  342 |         ).toBe('number');
  343 | 
  344 |         if (typeof item.count === 'string') {
  345 |           flagIssue('TC-DSH-003', 'DASH-ISSUE-2',
  346 |             `statusDistribution[${i}].count is a string â€" should be a number`,
  347 |             item.count,
  348 |           );
  349 |         }
  350 |         expect.soft(typeof item.count,
  351 |           `[DASH-ISSUE-2] statusDistribution[${i}].count should be a number, not a string`,
  352 |         ).toBe('number');
  353 | 
  354 |         // â"€â"€ DASH-ISSUE-6: color hex codes are UI concerns â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  355 |         if (item.color !== null) {
  356 |           flagIssue('TC-DSH-003', 'DASH-ISSUE-6',
  357 |             `statusDistribution[${i}].color is a UI hex code â€" should not be returned by API`,
  358 |             item.color,
  359 |           );
  360 |         }
  361 |         expect.soft(item.color,
  362 |           `[DASH-ISSUE-6] statusDistribution[${i}].color UI hex code should not be in API response`,
  363 |         ).toBeNull();
  364 | 
  365 |         allure.parameter(`statusDistribution[${i}].label`,      String(item.label));
  366 |         allure.parameter(`statusDistribution[${i}].percentage`,  String(item.percentage) + '%');
  367 |         allure.parameter(`statusDistribution[${i}].amount`,      String(item.amount) + (typeof item.amount === 'string' ? ' âš  DASH-ISSUE-2' : ''));
  368 |         allure.parameter(`statusDistribution[${i}].count`,       String(item.count) + (typeof item.count === 'string' ? ' âš  DASH-ISSUE-2' : ''));
  369 |         allure.parameter(`statusDistribution[${i}].color`,       String(item.color) + ' âš  DASH-ISSUE-6');
  370 |       }
  371 |       logger.pass('All assertions passed');
  372 |     });
  373 | 
  374 |     await logger.step('Step 5 â€" Validate statusDistribution covers all expected statuses (flag if incomplete)', async () => {
  375 |       const d      = res!.body?.data ?? res!.body;
  376 |       const dist   = d.statusDistribution as Array<{ label: string }>;
  377 |       const labels = dist.map(item => item.label);
  378 | 
  379 |       // â"€â"€ DASH-ISSUE-7: Only "Successful" returned â€" FAILED/PENDING absent â"€â"€â"€â"€â"€â"€â"€â"€
  380 |       logger.info('Asserting: all valid status labels (Successful, Failed, Pending) are present and percentages sum to ~100');
  381 |       for (const expected of VALID_STATUS_LABELS) {
  382 |         if (!labels.includes(expected)) {
  383 |           flagIssue('TC-DSH-003', 'DASH-ISSUE-7',
  384 |             `statusDistribution does not contain a "${expected}" entry â€" all status categories should be present even with 0% share`,
  385 |             { expected, presentLabels: labels },
  386 |           );
  387 |         }
  388 |         expect.soft(labels,
  389 |           `[DASH-ISSUE-7] statusDistribution should contain a "${expected}" entry`,
  390 |         ).toContain(expected);
  391 |       }
  392 | 
  393 |       // Percentages within the returned entries should sum to 100 (Â± 0.5 for rounding)
  394 |       const totalPct = dist.reduce((sum, item) => sum + (item as unknown as { percentage: number }).percentage, 0);
  395 |       expect(totalPct, 'statusDistribution percentages must sum to ~100').toBeGreaterThanOrEqual(99);
  396 |       expect(totalPct, 'statusDistribution percentages must sum to ~100').toBeLessThanOrEqual(101);
  397 | 
  398 |       allure.parameter('statusDistribution labels',    labels.join(', '));
  399 |       allure.parameter('statusDistribution total %',   String(totalPct.toFixed(1)));
  400 |       allure.parameter('DASH-ISSUE-7 missing statuses',
  401 |         VALID_STATUS_LABELS.filter(l => !labels.includes(l)).join(', ') || 'none',
  402 |       );
  403 |       logger.pass('All assertions passed');
  404 |     });
  405 |   });
  406 | 
  407 |   // â"€â"€ TC-DSH-004 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  408 |   test('TC-DSH-004 â€" currencyDistribution: KES + USD entries, percentages sum to ~100 %', async ({ logger }) => {
  409 |     smokeLabels('TC-DSH-004', 'Status Currency Endpoint', 'currencyDistribution â€" Field & Math Validation');
  410 | 
  411 |     let res: supertest.Response;
  412 | 
  413 |     await logger.step('Step 1 â€" Build request', async () => {
  414 |       allure.parameter('Base URL', BASE_URL);
  415 |       allure.parameter('Endpoint', ENDPOINT);
  416 |       allure.parameter('Method',   'GET');
  417 |       logger.info('Asserting: request parameters are recorded');
  418 |       logger.pass('All assertions passed');
  419 |     });
  420 | 
  421 |     await logger.step('Step 2 â€" Send GET /status-currency', async () => {
  422 |       logger.info('GET /api/v1/dashboard/status-currency');
  423 |       res = await get();
  424 |       logger.pass('HTTP ' + res.status + ' received');
```