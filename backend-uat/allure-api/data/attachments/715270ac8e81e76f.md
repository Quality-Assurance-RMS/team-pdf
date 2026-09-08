# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-revenue-trend.spec.ts >> Backend (Konza) â€” Dashboard Revenue Trend >> TC-DREVT-004 â€” BUG-REVT-02/03: points must be a number; value must expose raw numeric amount
- Location: tests/API/backend-tests/backend-dashboard-revenue-trend.spec.ts:341:7

# Error details

```
Error: [BUG-REVT-02] summary[0] "Total Transactions" .points must be type "number", got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-REVT-02] summary[1] "Successful" .points must be type "number", got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-REVT-02] summary[2] "Failed" .points must be type "number", got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-REVT-03] summary[0] "Total Transactions" .value must be a number (or contain one) â€” got formatted string "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

```
Error: [BUG-REVT-03] summary[1] "Successful" .value must be a number (or contain one) â€” got formatted string "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

```
Error: [BUG-REVT-03] summary[2] "Failed" .value must be a number (or contain one) â€” got formatted string "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

```
TypeError: Cannot read properties of undefined (reading 'forEach')
```

# Test source

```ts
  327 |         logger.info('Asserting: response structure and values');
  328 |         expect.soft(hasCorrect,
  329 |           `[BUG-REVT-01] summary[${i}] "${item.label}" must use "isPositive" (correct) not "isPostive" (typo)`,
  330 |         ).toBe(true);
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
> 427 |         arr.forEach((val, i) => {
      |             ^ TypeError: Cannot read properties of undefined (reading 'forEach')
  428 |           logger.info('Asserting: Validate array data types (numbers, not strings)');
  429 |           expect(typeof val, `data.${name}[${i}] must be a number`).toBe('number');
  430 |           expect(val as number, `data.${name}[${i}] must be >= 0`).toBeGreaterThanOrEqual(0);
  431 |         });
  432 |         allure.parameter(`${String(name)} types OK`, 'true');
  433 |       }
  434 |       logger.pass('All assertions passed');
  435 |     });
  436 |   });
  437 | 
  438 |   // â”€â”€ TC-DREVT-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  439 |   test('TC-DREVT-005 â€” BUG-REVT-04: labels must not contain duplicates without year disambiguation', async ({ logger }) => {
  440 |     backendLabels('TC-DREVT-005', 'BUG-REVT-04 â€” Duplicate Month Labels', 'critical');
  441 | 
  442 |     let res: supertest.Response;
  443 | 
  444 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  445 |       logger.info(`GET ${ENDPOINT}`);
  446 |       res = await get();
  447 |       logger.pass('HTTP ' + res.status + ' received');
  448 |       attachResponse('TC-DREVT-005', res, { bug: 'BUG-REVT-04' });
  449 |     });
  450 | 
  451 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  452 |       logger.info('Asserting: Validate HTTP 200');
  453 |       expect(res!.status).toBe(200);
  454 |       logger.pass('All assertions passed');
  455 |     });
  456 | 
  457 |     await logger.step('Step 3 â€” BUG-REVT-04: labels array must not have duplicate month names', async () => {
  458 |       const labels = (res!.body.data as RevenueTrendData).labels;
  459 | 
  460 |       allure.parameter('labels', labels.join(', '));
  461 |       allure.parameter('labels count', String(labels.length));
  462 | 
  463 |       const duplicates = labels.filter((l, i) => labels.indexOf(l) !== i);
  464 |       allure.parameter('Duplicate labels', duplicates.length > 0 ? duplicates.join(', ') : 'none');
  465 | 
  466 |       if (duplicates.length > 0) {
  467 |         const dupeDetails = duplicates.map(d => ({
  468 |           label: d,
  469 |           first_index:  labels.indexOf(d),
  470 |           second_index: labels.lastIndexOf(d),
  471 |           interpretation: `index ${labels.indexOf(d)} = earlier year, index ${labels.lastIndexOf(d)} = later year â€” both show "${d}" with no year`,
  472 |         }));
  473 | 
  474 |         flagIssue('TC-DREVT-005', 'BUG-REVT-04',
  475 |           `labels array contains ${duplicates.length} duplicate(s): [${duplicates.join(', ')}]. ` +
  476 |           'For a 13-month trend window spanning two calendar years, each month abbreviation must include the year ' +
  477 |           '(e.g. "Jun 2025" vs "Jun 2026") so charts and screen readers can distinguish them.',
  478 |           { labels, duplicates: dupeDetails, expected_format: '"Jun 2025", "Jul 2025", ..., "May 2026", "Jun 2026"' },
  479 |         );
  480 |       }
  481 | 
  482 |       logger.info('Asserting: BUG-REVT-04: labels array must not have duplicate');
  483 |       expect.soft(duplicates.length,
  484 |         `[BUG-REVT-04] labels must not contain duplicate month names without year. Duplicates found: [${duplicates.join(', ')}]`,
  485 |       ).toBe(0);
  486 |       logger.pass('All assertions passed');
  487 |     });
  488 | 
  489 |     await logger.step('Step 4 â€” Labels must contain only valid month abbreviations', async () => {
  490 |       const labels = (res!.body.data as RevenueTrendData).labels;
  491 |       const VALID_MONTHS = new Set(['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec']);
  492 | 
  493 |       for (const label of labels) {
  494 |         // Accept "Mon" or "Mon YYYY" format
  495 |         const monthPart = label.split(' ')[0];
  496 |         logger.info('Asserting: response structure and values');
  497 |         expect(VALID_MONTHS.has(monthPart),
  498 |           `Label "${label}" must be a valid month abbreviation (Janâ€“Dec)`,
  499 |         ).toBe(true);
  500 |       }
  501 |       logger.pass('All assertions passed');
  502 |     });
  503 | 
  504 |     await logger.step('Step 5 â€” Validate array length is within reasonable range (12â€“13 months)', async () => {
  505 |       const labels = (res!.body.data as RevenueTrendData).labels;
  506 |       logger.info('Asserting: Validate array length is within reasonable range');
  507 |       expect(labels.length, 'labels must have 12 or 13 entries for a monthly trend').toBeGreaterThanOrEqual(12);
  508 |       expect(labels.length, 'labels must not exceed 13 entries for a monthly trend').toBeLessThanOrEqual(13);
  509 |       logger.pass('All assertions passed');
  510 |     });
  511 |   });
  512 | 
  513 |   // â”€â”€ TC-DREVT-006 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  514 |   test('TC-DREVT-006 â€” BUG-REVT-05: change field must reflect real period-over-period delta, not always "+0%"', async ({ logger }) => {
  515 |     backendLabels('TC-DREVT-006', 'BUG-REVT-05 â€” change Always "+0%"', 'critical');
  516 | 
  517 |     let res: supertest.Response;
  518 | 
  519 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  520 |       logger.info(`GET ${ENDPOINT}`);
  521 |       res = await get();
  522 |       logger.pass('HTTP ' + res.status + ' received');
  523 |       logResponse('TC-DREVT-006', res, '(change field check)');
  524 |       attachResponse('TC-DREVT-006', res, { bug: 'BUG-REVT-05' });
  525 |     });
  526 | 
  527 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
```