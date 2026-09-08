# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-revenue-trend.spec.ts >> Backend (Konza) â€” Dashboard Revenue Trend >> TC-DREVT-003 â€” BUG-REVT-01: summary items must use "isPositive", not "isPostive"
- Location: tests/API/backend-tests/backend-dashboard-revenue-trend.spec.ts:292:7

# Error details

```
Error: [BUG-REVT-01] summary[0] "Total Transactions" must use "isPositive" (correct) not "isPostive" (typo)

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

```
Error: [BUG-REVT-01] summary[1] "Successful" must use "isPositive" (correct) not "isPostive" (typo)

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

```
Error: [BUG-REVT-01] summary[2] "Failed" must use "isPositive" (correct) not "isPostive" (typo)

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

# Test source

```ts
  230 |       const expectedLabels = ['Total Attempts', 'Successful', 'Failed'];
  231 |       d.summary.forEach((item, i) => {
  232 |         expect(item, `summary[${i}] must have "label"`).toHaveProperty('label');
  233 |         expect(item, `summary[${i}] must have "value"`).toHaveProperty('value');
  234 |         expect(item, `summary[${i}] must have "change"`).toHaveProperty('change');
  235 |         allure.parameter(`summary[${i}].label`, String(item.label));
  236 |       });
  237 | 
  238 |       const actualLabels = d.summary.map(s => s.label);
  239 |       for (const lbl of expectedLabels) {
  240 |         expect(actualLabels, `summary must include "${lbl}"`).toContain(lbl);
  241 |       }
  242 |       logger.pass('All assertions passed');
  243 |     });
  244 |   });
  245 | 
  246 |   // â”€â”€ TC-DREVT-002 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  247 |   test('TC-DREVT-002 â€” Auth Guard: missing token â†’ 401, wrong token â†’ 401', async ({ logger }) => {
  248 |     backendLabels('TC-DREVT-002', 'Auth Guard', 'blocker');
  249 | 
  250 |     let resNoAuth:    supertest.Response;
  251 |     let resWrongAuth: supertest.Response;
  252 | 
  253 |     await logger.step('Step 1 â€” No Authorization header', async () => {
  254 |       logger.info(`GET ${ENDPOINT}`);
  255 |       resNoAuth = await supertest(BASE_URL).get(ENDPOINT).set('Accept', 'application/json');
  256 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  257 |       console.log(`[TC-DREVT-002] No-auth: ${resNoAuth.status} â€” ${JSON.stringify(resNoAuth.body)}`);
  258 |       allure.parameter('No-auth status',  String(resNoAuth.status));
  259 |       allure.parameter('No-auth message', String(resNoAuth.body?.message ?? 'N/A'));
  260 |     });
  261 | 
  262 |     await logger.step('Step 2 â€” Invalid Bearer token', async () => {
  263 |       logger.info(`GET ${ENDPOINT}`);
  264 |       resWrongAuth = await supertest(BASE_URL)
  265 |         .get(ENDPOINT)
  266 |         .set('Authorization', 'Bearer invalidtoken.abc.xyz')
  267 |         .set('Accept', 'application/json');
  268 |       logger.pass('HTTP ' + resWrongAuth.status + ' received');
  269 |       console.log(`[TC-DREVT-002] Wrong-auth: ${resWrongAuth.status} â€” ${JSON.stringify(resWrongAuth.body)}`);
  270 |       allure.parameter('Wrong-auth status',  String(resWrongAuth.status));
  271 |       allure.parameter('Wrong-auth message', String(resWrongAuth.body?.message ?? 'N/A'));
  272 |     });
  273 | 
  274 |     await logger.step('Step 3 â€” Both must return 401', async () => {
  275 |       logger.info('Asserting: response structure and values');
  276 |       expect(resNoAuth!.status,    'Missing token must return 401').toBe(401);
  277 |       expect(resWrongAuth!.status, 'Invalid token must return 401').toBe(401);
  278 |       logger.pass('All assertions passed');
  279 |     });
  280 | 
  281 |     await logger.step('Step 4 â€” 401 error body must include status and message', async () => {
  282 |       logger.info('Asserting: response structure and values');
  283 |       expect(resNoAuth!.body.status,    'No-auth body.status must be 401').toBe(401);
  284 |       expect(resWrongAuth!.body.status, 'Wrong-auth body.status must be 401').toBe(401);
  285 |       expect(resNoAuth!.body.message,   'No-auth message must be "Missing Authorization token"').toBe('Missing Authorization token');
  286 |       expect(resWrongAuth!.body.message,'Wrong-auth message must be "Invalid or expired token"').toBe('Invalid or expired token');
  287 |       logger.pass('All assertions passed');
  288 |     });
  289 |   });
  290 | 
  291 |   // â”€â”€ TC-DREVT-003 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  292 |   test('TC-DREVT-003 â€” BUG-REVT-01: summary items must use "isPositive", not "isPostive"', async ({ logger }) => {
  293 |     backendLabels('TC-DREVT-003', 'BUG-REVT-01 â€” isPostive Typo in summary', 'critical');
  294 | 
  295 |     let res: supertest.Response;
  296 | 
  297 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  298 |       logger.info(`GET ${ENDPOINT}`);
  299 |       res = await get();
  300 |       logger.pass('HTTP ' + res.status + ' received');
  301 |       attachResponse('TC-DREVT-003', res, { bug: 'BUG-REVT-01' });
  302 |     });
  303 | 
  304 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  305 |       logger.info('Asserting: Validate HTTP 200');
  306 |       expect(res!.status).toBe(200);
  307 |       logger.pass('All assertions passed');
  308 |     });
  309 | 
  310 |     await logger.step('Step 3 â€” Each summary item must NOT have "isPostive" (typo)', async () => {
  311 |       const summary = (res!.body.data as RevenueTrendData).summary;
  312 | 
  313 |       summary.forEach((item, i) => {
  314 |         const hasTypo    = 'isPostive'  in item;
  315 |         const hasCorrect = 'isPositive' in item;
  316 | 
  317 |         allure.parameter(`summary[${i}] "${item.label}" isPostive (typo)`,    String(hasTypo));
  318 |         allure.parameter(`summary[${i}] "${item.label}" isPositive (correct)`, String(hasCorrect));
  319 | 
  320 |         if (hasTypo) {
  321 |           flagIssue('TC-DREVT-003', 'BUG-REVT-01',
  322 |             `summary[${i}] "${item.label}" has field "isPostive" â€” correct name is "isPositive" (missing 'i'). This typo exists in all 3 summary items.`,
  323 |             { index: i, label: item.label, actual_field: 'isPostive', expected_field: 'isPositive', value: (item as unknown as Record<string,unknown>)['isPostive'] },
  324 |           );
  325 |         }
  326 | 
  327 |         logger.info('Asserting: response structure and values');
  328 |         expect.soft(hasCorrect,
  329 |           `[BUG-REVT-01] summary[${i}] "${item.label}" must use "isPositive" (correct) not "isPostive" (typo)`,
> 330 |         ).toBe(true);
      |           ^ Error: [BUG-REVT-01] summary[2] "Failed" must use "isPositive" (correct) not "isPostive" (typo)
  331 | 
  332 |         expect.soft(hasTypo,
  333 |           `[BUG-REVT-01] summary[${i}] "${item.label}" must not have misspelled field "isPostive"`,
  334 |         ).toBe(false);
  335 |       });
  336 |       logger.pass('All assertions passed');
  337 |     });
  338 |   });
  339 | 
  340 |   // â”€â”€ TC-DREVT-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  341 |   test('TC-DREVT-004 â€” BUG-REVT-02/03: points must be a number; value must expose raw numeric amount', async ({ logger }) => {
  342 |     backendLabels('TC-DREVT-004', 'BUG-REVT-02/03 â€” Data Types (points & value as Strings)', 'critical');
  343 | 
  344 |     let res: supertest.Response;
  345 | 
  346 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  347 |       logger.info(`GET ${ENDPOINT}`);
  348 |       res = await get();
  349 |       logger.pass('HTTP ' + res.status + ' received');
  350 |       attachResponse('TC-DREVT-004', res, { bug: 'BUG-REVT-02/BUG-REVT-03' });
  351 |     });
  352 | 
  353 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  354 |       logger.info('Asserting: Validate HTTP 200');
  355 |       expect(res!.status).toBe(200);
  356 |       logger.pass('All assertions passed');
  357 |     });
  358 | 
  359 |     await logger.step('Step 3 â€” BUG-REVT-02: summary[*].points must be a number, not a string', async () => {
  360 |       const summary = (res!.body.data as RevenueTrendData).summary;
  361 | 
  362 |       summary.forEach((item, i) => {
  363 |         const pts = item.points;
  364 |         allure.parameter(`summary[${i}] "${item.label}" points type`, typeof pts);
  365 |         allure.parameter(`summary[${i}] "${item.label}" points value`, String(pts));
  366 | 
  367 |         if (typeof pts === 'string') {
  368 |           flagIssue('TC-DREVT-004', 'BUG-REVT-02',
  369 |             `summary[${i}] "${item.label}" .points="${pts}" is a string â€” transaction counts must be numbers for client-side arithmetic`,
  370 |             { index: i, label: item.label, actual_type: 'string', actual_value: pts, expected_type: 'number' },
  371 |           );
  372 |         }
  373 | 
  374 |         logger.info('Asserting: BUG-REVT-02: summary[*].points must be a number,');
  375 |         expect.soft(typeof pts,
  376 |           `[BUG-REVT-02] summary[${i}] "${item.label}" .points must be type "number", got "${typeof pts}" ("${pts}")`,
  377 |         ).toBe('number');
  378 | 
  379 |         // When fixed: points must be non-negative integer
  380 |         if (typeof pts === 'number') {
  381 |           expect(pts, `summary[${i}].points must be >= 0`).toBeGreaterThanOrEqual(0);
  382 |           expect(Number.isInteger(pts), `summary[${i}].points must be an integer`).toBe(true);
  383 |         }
  384 |       });
  385 |       logger.pass('All assertions passed');
  386 |     });
  387 | 
  388 |     await logger.step('Step 4 â€” BUG-REVT-03: summary[*].value must include a raw numeric amount', async () => {
  389 |       const summary = (res!.body.data as RevenueTrendData).summary;
  390 | 
  391 |       summary.forEach((item, i) => {
  392 |         const val = item.value;
  393 |         allure.parameter(`summary[${i}] "${item.label}" value type`, typeof val);
  394 |         allure.parameter(`summary[${i}] "${item.label}" value`,      String(val));
  395 | 
  396 |         // Actual: "KES 18,633,694" â€” a pre-formatted display string
  397 |         // Expected: either a number (18633694) or an object {amount: 18633694, currency: "KES", formatted: "KES 18,633,694"}
  398 |         const isFormattedString = typeof val === 'string' && (val as string).startsWith('KES ');
  399 | 
  400 |         if (isFormattedString) {
  401 |           flagIssue('TC-DREVT-004', 'BUG-REVT-03',
  402 |             `summary[${i}] "${item.label}" .value="${val}" is a pre-formatted display string â€” ` +
  403 |             'APIs must return raw numbers; display formatting belongs in the UI layer. ' +
  404 |             'Client cannot perform math on "KES 18,633,694" without stripping "KES " and commas.',
  405 |             { index: i, label: item.label, actual: val, expected: 'number or {amount, currency, formatted}' },
  406 |           );
  407 |         }
  408 | 
  409 |         // The value field should be a number OR an object with at least an `amount` field
  410 |         const isAcceptable = typeof val === 'number' || (typeof val === 'object' && val !== null && 'amount' in (val as object));
  411 |         logger.info('Asserting: BUG-REVT-03: summary[*].value must include a raw');
  412 |         expect.soft(isAcceptable,
  413 |           `[BUG-REVT-03] summary[${i}] "${item.label}" .value must be a number (or contain one) â€” got formatted string "${val}"`,
  414 |         ).toBe(true);
  415 |       });
  416 |       logger.pass('All assertions passed');
  417 |     });
  418 | 
  419 |     await logger.step('Step 5 â€” Validate array data types (numbers, not strings)', async () => {
  420 |       const d = res!.body.data as RevenueTrendData;
  421 |       const arrays: [keyof RevenueTrendData, unknown[]][] = [
  422 |         ['totalAttempts', d.totalAttempts],
  423 |         ['successful',    d.successful],
  424 |         ['failed',        d.failed],
  425 |       ];
  426 |       for (const [name, arr] of arrays) {
  427 |         arr.forEach((val, i) => {
  428 |           logger.info('Asserting: Validate array data types (numbers, not strings)');
  429 |           expect(typeof val, `data.${name}[${i}] must be a number`).toBe('number');
  430 |           expect(val as number, `data.${name}[${i}] must be >= 0`).toBeGreaterThanOrEqual(0);
```