# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-004 â€” BUG-REVT-02/03: points must be a number; value must expose raw numeric amount
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:333:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 503
```

# Test source

```ts
  247 |       resNoAuth = await supertest(BASE_URL).get(ENDPOINT).set('Accept', 'application/json');
  248 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  249 |       console.log(`[TC-DREVT-002] No-auth: ${resNoAuth.status} â€” ${JSON.stringify(resNoAuth.body)}`);
  250 |       allure.parameter('No-auth status',  String(resNoAuth.status));
  251 |       allure.parameter('No-auth message', String(resNoAuth.body?.message ?? 'N/A'));
  252 |     });
  253 | 
  254 |     await logger.step('Step 2 â€” Invalid Bearer token', async () => {
  255 |       logger.info(`GET ${ENDPOINT}`);
  256 |       resWrongAuth = await supertest(BASE_URL)
  257 |         .get(ENDPOINT)
  258 |         .set('Authorization', 'Bearer invalidtoken.abc.xyz')
  259 |         .set('Accept', 'application/json');
  260 |       logger.pass('HTTP ' + resWrongAuth.status + ' received');
  261 |       console.log(`[TC-DREVT-002] Wrong-auth: ${resWrongAuth.status} â€” ${JSON.stringify(resWrongAuth.body)}`);
  262 |       allure.parameter('Wrong-auth status',  String(resWrongAuth.status));
  263 |       allure.parameter('Wrong-auth message', String(resWrongAuth.body?.message ?? 'N/A'));
  264 |     });
  265 | 
  266 |     await logger.step('Step 3 â€” Both must return 401', async () => {
  267 |       logger.info('Asserting: response structure and values');
  268 |       expect(resNoAuth!.status,    'Missing token must return 401').toBe(401);
  269 |       expect(resWrongAuth!.status, 'Invalid token must return 401').toBe(401);
  270 |       logger.pass('All assertions passed');
  271 |     });
  272 | 
  273 |     await logger.step('Step 4 â€” 401 error body must include status and message', async () => {
  274 |       logger.info('Asserting: response structure and values');
  275 |       expect(resNoAuth!.body.status,    'No-auth body.status must be 401').toBe(401);
  276 |       expect(resWrongAuth!.body.status, 'Wrong-auth body.status must be 401').toBe(401);
  277 |       expect(resNoAuth!.body.message,   'No-auth message must be "Missing Authorization token"').toBe('Missing Authorization token');
  278 |       expect(resWrongAuth!.body.message,'Wrong-auth message must be "Invalid or expired token"').toBe('Invalid or expired token');
  279 |       logger.pass('All assertions passed');
  280 |     });
  281 |   });
  282 | 
  283 |   // â”€â”€ TC-DREVT-003 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  284 |   test('TC-DREVT-003 â€” BUG-REVT-01: summary items must use "isPositive", not "isPostive"', async ({ logger }) => {
  285 |     smokeLabels('TC-DREVT-003', 'BUG-REVT-01 â€” isPostive Typo in summary', 'critical');
  286 | 
  287 |     let res: supertest.Response;
  288 | 
  289 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  290 |       logger.info(`GET ${ENDPOINT}`);
  291 |       res = await get();
  292 |       logger.pass('HTTP ' + res.status + ' received');
  293 |       attachResponse('TC-DREVT-003', res, { bug: 'BUG-REVT-01' });
  294 |     });
  295 | 
  296 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  297 |       logger.info('Asserting: Validate HTTP 200');
  298 |       expect(res!.status).toBe(200);
  299 |       logger.pass('All assertions passed');
  300 |     });
  301 | 
  302 |     await logger.step('Step 3 â€” Each summary item must NOT have "isPostive" (typo)', async () => {
  303 |       const summary = (res!.body.data as RevenueTrendData).summary;
  304 | 
  305 |       summary.forEach((item, i) => {
  306 |         const hasTypo    = 'isPostive'  in item;
  307 |         const hasCorrect = 'isPositive' in item;
  308 | 
  309 |         allure.parameter(`summary[${i}] "${item.label}" isPostive (typo)`,    String(hasTypo));
  310 |         allure.parameter(`summary[${i}] "${item.label}" isPositive (correct)`, String(hasCorrect));
  311 | 
  312 |         if (hasTypo) {
  313 |           flagIssue('TC-DREVT-003', 'BUG-REVT-01',
  314 |             `summary[${i}] "${item.label}" has field "isPostive" â€” correct name is "isPositive" (missing 'i'). This typo exists in all 3 summary items.`,
  315 |             { index: i, label: item.label, actual_field: 'isPostive', expected_field: 'isPositive', value: (item as unknown as Record<string,unknown>)['isPostive'] },
  316 |           );
  317 |         }
  318 | 
  319 |         logger.info('Asserting: response structure and values');
  320 |         expect.soft(hasCorrect,
  321 |           `[BUG-REVT-01] summary[${i}] "${item.label}" must use "isPositive" (correct) not "isPostive" (typo)`,
  322 |         ).toBe(true);
  323 | 
  324 |         expect.soft(hasTypo,
  325 |           `[BUG-REVT-01] summary[${i}] "${item.label}" must not have misspelled field "isPostive"`,
  326 |         ).toBe(false);
  327 |       });
  328 |       logger.pass('All assertions passed');
  329 |     });
  330 |   });
  331 | 
  332 |   // â”€â”€ TC-DREVT-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  333 |   test('TC-DREVT-004 â€” BUG-REVT-02/03: points must be a number; value must expose raw numeric amount', async ({ logger }) => {
  334 |     smokeLabels('TC-DREVT-004', 'BUG-REVT-02/03 â€” Data Types (points & value as Strings)', 'critical');
  335 | 
  336 |     let res: supertest.Response;
  337 | 
  338 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  339 |       logger.info(`GET ${ENDPOINT}`);
  340 |       res = await get();
  341 |       logger.pass('HTTP ' + res.status + ' received');
  342 |       attachResponse('TC-DREVT-004', res, { bug: 'BUG-REVT-02/BUG-REVT-03' });
  343 |     });
  344 | 
  345 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  346 |       logger.info('Asserting: Validate HTTP 200');
> 347 |       expect(res!.status).toBe(200);
      |                           ^ Error: expect(received).toBe(expected) // Object.is equality
  348 |       logger.pass('All assertions passed');
  349 |     });
  350 | 
  351 |     await logger.step('Step 3 â€” BUG-REVT-02: summary[*].points must be a number, not a string', async () => {
  352 |       const summary = (res!.body.data as RevenueTrendData).summary;
  353 | 
  354 |       summary.forEach((item, i) => {
  355 |         const pts = item.points;
  356 |         allure.parameter(`summary[${i}] "${item.label}" points type`, typeof pts);
  357 |         allure.parameter(`summary[${i}] "${item.label}" points value`, String(pts));
  358 | 
  359 |         if (typeof pts === 'string') {
  360 |           flagIssue('TC-DREVT-004', 'BUG-REVT-02',
  361 |             `summary[${i}] "${item.label}" .points="${pts}" is a string â€” transaction counts must be numbers for client-side arithmetic`,
  362 |             { index: i, label: item.label, actual_type: 'string', actual_value: pts, expected_type: 'number' },
  363 |           );
  364 |         }
  365 | 
  366 |         logger.info('Asserting: BUG-REVT-02: summary[*].points must be a number,');
  367 |         expect.soft(typeof pts,
  368 |           `[BUG-REVT-02] summary[${i}] "${item.label}" .points must be type "number", got "${typeof pts}" ("${pts}")`,
  369 |         ).toBe('number');
  370 | 
  371 |         // When fixed: points must be non-negative integer
  372 |         if (typeof pts === 'number') {
  373 |           expect(pts, `summary[${i}].points must be >= 0`).toBeGreaterThanOrEqual(0);
  374 |           expect(Number.isInteger(pts), `summary[${i}].points must be an integer`).toBe(true);
  375 |         }
  376 |       });
  377 |       logger.pass('All assertions passed');
  378 |     });
  379 | 
  380 |     await logger.step('Step 4 â€” BUG-REVT-03: summary[*].value must include a raw numeric amount', async () => {
  381 |       const summary = (res!.body.data as RevenueTrendData).summary;
  382 | 
  383 |       summary.forEach((item, i) => {
  384 |         const val = item.value;
  385 |         allure.parameter(`summary[${i}] "${item.label}" value type`, typeof val);
  386 |         allure.parameter(`summary[${i}] "${item.label}" value`,      String(val));
  387 | 
  388 |         // Actual: "KES 18,633,694" â€” a pre-formatted display string
  389 |         // Expected: either a number (18633694) or an object {amount: 18633694, currency: "KES", formatted: "KES 18,633,694"}
  390 |         const isFormattedString = typeof val === 'string' && (val as string).startsWith('KES ');
  391 | 
  392 |         if (isFormattedString) {
  393 |           flagIssue('TC-DREVT-004', 'BUG-REVT-03',
  394 |             `summary[${i}] "${item.label}" .value="${val}" is a pre-formatted display string â€” ` +
  395 |             'APIs must return raw numbers; display formatting belongs in the UI layer. ' +
  396 |             'Client cannot perform math on "KES 18,633,694" without stripping "KES " and commas.',
  397 |             { index: i, label: item.label, actual: val, expected: 'number or {amount, currency, formatted}' },
  398 |           );
  399 |         }
  400 | 
  401 |         // The value field should be a number OR an object with at least an `amount` field
  402 |         const isAcceptable = typeof val === 'number' || (typeof val === 'object' && val !== null && 'amount' in (val as object));
  403 |         logger.info('Asserting: BUG-REVT-03: summary[*].value must include a raw');
  404 |         expect.soft(isAcceptable,
  405 |           `[BUG-REVT-03] summary[${i}] "${item.label}" .value must be a number (or contain one) â€” got formatted string "${val}"`,
  406 |         ).toBe(true);
  407 |       });
  408 |       logger.pass('All assertions passed');
  409 |     });
  410 | 
  411 |     await logger.step('Step 5 â€” Validate array data types (numbers, not strings)', async () => {
  412 |       const d = res!.body.data as RevenueTrendData;
  413 |       const arrays: [keyof RevenueTrendData, unknown[]][] = [
  414 |         ['totalAttempts', d.totalAttempts],
  415 |         ['successful',    d.successful],
  416 |         ['failed',        d.failed],
  417 |       ];
  418 |       for (const [name, arr] of arrays) {
  419 |         arr.forEach((val, i) => {
  420 |           logger.info('Asserting: Validate array data types (numbers, not strings)');
  421 |           expect(typeof val, `data.${name}[${i}] must be a number`).toBe('number');
  422 |           expect(val as number, `data.${name}[${i}] must be >= 0`).toBeGreaterThanOrEqual(0);
  423 |         });
  424 |         allure.parameter(`${String(name)} types OK`, 'true');
  425 |       }
  426 |       logger.pass('All assertions passed');
  427 |     });
  428 |   });
  429 | 
  430 |   // â”€â”€ TC-DREVT-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  431 |   test('TC-DREVT-005 â€” BUG-REVT-04: labels must not contain duplicates without year disambiguation', async ({ logger }) => {
  432 |     smokeLabels('TC-DREVT-005', 'BUG-REVT-04 â€” Duplicate Month Labels', 'critical');
  433 | 
  434 |     let res: supertest.Response;
  435 | 
  436 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  437 |       logger.info(`GET ${ENDPOINT}`);
  438 |       res = await get();
  439 |       logger.pass('HTTP ' + res.status + ' received');
  440 |       attachResponse('TC-DREVT-005', res, { bug: 'BUG-REVT-04' });
  441 |     });
  442 | 
  443 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  444 |       logger.info('Asserting: Validate HTTP 200');
  445 |       expect(res!.status).toBe(200);
  446 |       logger.pass('All assertions passed');
  447 |     });
```