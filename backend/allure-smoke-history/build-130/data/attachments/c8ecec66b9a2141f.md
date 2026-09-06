# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-today-payments.spec.ts >> Smoke â€" Dashboard Today Payments >> TC-DTPAY-004 â€" BUG-TPAY-02: Monetary amounts must be numbers, not strings
- Location: tests/API/smoke/smoke-dashboard-today-payments.spec.ts:424:7

# Error details

```
Error: [BUG-TPAY-02] payments.successfullPaymentKES must be a number, got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] payments.successfullPaymentDoller must be a number, got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] payments.failerfullPaymentKES must be a number, got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] payments.failerfullPaymentDoller must be a number, got "undefined" ("undefined")

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] refunds.successfullPaymentKES must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] refunds.successfullPaymentDoller must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] refunds.failerfullPaymentKES must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] refunds.failerfullPaymentDoller must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] revenue.successfullPaymentKES must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] revenue.successfullPaymentDoller must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] revenue.failerfullPaymentKES must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: [BUG-TPAY-02] revenue.failerfullPaymentDoller must be a number, got "undefined"

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

```
Error: payments.postiveValue must be a string percentage label

expect(received).toBe(expected) // Object.is equality

Expected: "string"
Received: "undefined"
```

# Test source

```ts
  425 |     smokeLabels('TC-DTPAY-004', 'BUG-TPAY-02 â€" Amount Types as Strings', 'critical');
  426 | 
  427 |     let res: supertest.Response;
  428 | 
  429 |     await logger.step('Step 1 â€" Fetch today-payments response', async () => {
  430 |       logger.info('GET /api/v1/dashboard/today-payments');
  431 |       res = await get();
  432 |       logger.pass('HTTP ' + res.status + ' received');
  433 |       attachResponse('TC-DTPAY-004', res, { bug: 'BUG-TPAY-02' });
  434 |     });
  435 | 
  436 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  437 |       logger.info('Asserting: HTTP 200');
  438 |       expect(res!.status).toBe(200);
  439 |       logger.pass('All assertions passed');
  440 |     });
  441 | 
  442 |     await logger.step('Step 3 â€" All monetary fields in payments must be numbers', async () => {
  443 |       const pay = res!.body.data.payments as PaymentSection;
  444 |       const amountFields = [
  445 |         'successfullPaymentKES',
  446 |         'successfullPaymentDoller',
  447 |         'failerfullPaymentKES',
  448 |         'failerfullPaymentDoller',
  449 |       ] as const;
  450 | 
  451 |       logger.info('Asserting: all monetary fields in payments section are numbers, not strings');
  452 |       for (const field of amountFields) {
  453 |         const val = pay[field];
  454 |         const isString = typeof val === 'string';
  455 | 
  456 |         if (isString) {
  457 |           flagIssue('TC-DTPAY-004', 'BUG-TPAY-02',
  458 |             `payments.${field} is returned as a string "${val}" â€" monetary values must be numbers for arithmetic`,
  459 |             { field, actual_type: typeof val, actual_value: val, expected_type: 'number' },
  460 |           );
  461 |         }
  462 | 
  463 |         allure.parameter(`payments.${field} type`, typeof val);
  464 |         expect.soft(typeof val, `[BUG-TPAY-02] payments.${field} must be a number, got "${typeof val}" ("${val}")`).toBe('number');
  465 |       }
  466 |       logger.pass('All assertions passed');
  467 |     });
  468 | 
  469 |     await logger.step('Step 4 â€" All monetary fields in refunds must be numbers', async () => {
  470 |       const ref = res!.body.data.refunds as PaymentSection;
  471 |       const amountFields = [
  472 |         'successfullPaymentKES',
  473 |         'successfullPaymentDoller',
  474 |         'failerfullPaymentKES',
  475 |         'failerfullPaymentDoller',
  476 |       ] as const;
  477 | 
  478 |       logger.info('Asserting: all monetary fields in refunds section are numbers, not strings');
  479 |       for (const field of amountFields) {
  480 |         const val = ref[field];
  481 |         if (typeof val === 'string') {
  482 |           flagIssue('TC-DTPAY-004', 'BUG-TPAY-02',
  483 |             `refunds.${field} is a string â€" must be a number`,
  484 |             { field, actual_type: typeof val, actual_value: val },
  485 |           );
  486 |         }
  487 |         allure.parameter(`refunds.${field} type`, typeof val);
  488 |         expect.soft(typeof val, `[BUG-TPAY-02] refunds.${field} must be a number, got "${typeof val}"`).toBe('number');
  489 |       }
  490 |       logger.pass('All assertions passed');
  491 |     });
  492 | 
  493 |     await logger.step('Step 5 â€" All monetary fields in revenue must be numbers', async () => {
  494 |       const rev = res!.body.data.revenue as PaymentSection;
  495 |       const amountFields = [
  496 |         'successfullPaymentKES',
  497 |         'successfullPaymentDoller',
  498 |         'failerfullPaymentKES',
  499 |         'failerfullPaymentDoller',
  500 |       ] as const;
  501 | 
  502 |       logger.info('Asserting: all monetary fields in revenue section are numbers, not strings');
  503 |       for (const field of amountFields) {
  504 |         const val = rev[field];
  505 |         if (typeof val === 'string') {
  506 |           flagIssue('TC-DTPAY-004', 'BUG-TPAY-02',
  507 |             `revenue.${field} is a string â€" must be a number`,
  508 |             { field, actual_type: typeof val, actual_value: val },
  509 |           );
  510 |         }
  511 |         allure.parameter(`revenue.${field} type`, typeof val);
  512 |         expect.soft(typeof val, `[BUG-TPAY-02] revenue.${field} must be a number, got "${typeof val}"`).toBe('number');
  513 |       }
  514 |       logger.pass('All assertions passed');
  515 |     });
  516 | 
  517 |     await logger.step('Step 6 â€" Confirm postiveValue is a string (percentage label â€" acceptable)', async () => {
  518 |       // postiveValue (the percentage string like "-100.0%" or "0%") is expected to be a string
  519 |       // This step is informational â€" documenting that the string type IS acceptable for the label field
  520 |       const payPct = res!.body.data.payments.postiveValue;
  521 |       const refPct = res!.body.data.refunds.postiveValue;
  522 |       const revPct = res!.body.data.revenue.postiveValue;
  523 | 
  524 |       logger.info('Asserting: postiveValue is a string percentage label in all three sections');
> 525 |       expect(typeof payPct, 'payments.postiveValue must be a string percentage label').toBe('string');
      |                                                                                        ^ Error: payments.postiveValue must be a string percentage label
  526 |       expect(typeof refPct, 'refunds.postiveValue must be a string percentage label').toBe('string');
  527 |       expect(typeof revPct, 'revenue.postiveValue must be a string percentage label').toBe('string');
  528 | 
  529 |       allure.parameter('payments.postiveValue', String(payPct));
  530 |       allure.parameter('refunds.postiveValue',  String(refPct));
  531 |       allure.parameter('revenue.postiveValue',  String(revPct));
  532 |       logger.pass('All assertions passed');
  533 |     });
  534 |   });
  535 | 
  536 |   // â"€â"€ TC-DTPAY-005 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  537 |   test('TC-DTPAY-005 â€" BUG-TPAY-03/04: isPositive flag must agree with postiveValue percentage', async ({ logger }) => {
  538 |     smokeLabels('TC-DTPAY-005', 'BUG-TPAY-03/04 â€" isPositive vs postiveValue Logic', 'critical');
  539 | 
  540 |     let res: supertest.Response;
  541 | 
  542 |     await logger.step('Step 1 â€" Fetch today-payments response', async () => {
  543 |       logger.info('GET /api/v1/dashboard/today-payments');
  544 |       res = await get();
  545 |       logger.pass('HTTP ' + res.status + ' received');
  546 |       logResponse('TC-DTPAY-005', res, '(isPositive logic check)');
  547 |       attachResponse('TC-DTPAY-005', res, { bug: 'BUG-TPAY-03/BUG-TPAY-04' });
  548 |     });
  549 | 
  550 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  551 |       logger.info('Asserting: HTTP 200');
  552 |       expect(res!.status).toBe(200);
  553 |       logger.pass('All assertions passed');
  554 |     });
  555 | 
  556 |     await logger.step('Step 3 â€" BUG-TPAY-03: revenue.isPositive must not be true when postiveValue is negative', async () => {
  557 |       const rev = res!.body.data.revenue as PaymentSection;
  558 |       const pct  = rev.postiveValue as string;
  559 |       const flag = rev.isPositive;
  560 | 
  561 |       allure.parameter('revenue.isPositive',  String(flag));
  562 |       allure.parameter('revenue.postiveValue', String(pct));
  563 | 
  564 |       const isContradiction = flag === true && typeof pct === 'string' && pct.startsWith('-');
  565 |       if (isContradiction) {
  566 |         flagIssue('TC-DTPAY-005', 'BUG-TPAY-03',
  567 |           `revenue.isPositive=true but postiveValue="${pct}" â€" a negative % change cannot be "positive"`,
  568 |           { isPositive: flag, postiveValue: pct },
  569 |         );
  570 |       }
  571 | 
  572 |       allure.parameter('Contradiction detected', String(isContradiction));
  573 | 
  574 |       logger.info('Asserting: revenue.isPositive agrees with the sign of postiveValue');
  575 |       // If the percentage starts with '-', isPositive MUST be false
  576 |       if (typeof pct === 'string' && pct.startsWith('-')) {
  577 |         expect.soft(flag,
  578 |           `[BUG-TPAY-03] revenue.isPositive should be false when postiveValue="${pct}" (negative percentage)`,
  579 |         ).toBe(false);
  580 |       }
  581 | 
  582 |       // If the percentage is positive (no '-'), isPositive MUST be true
  583 |       if (typeof pct === 'string' && !pct.startsWith('-')) {
  584 |         expect(flag,
  585 |           `revenue.isPositive should be true when postiveValue="${pct}" (non-negative percentage)`,
  586 |         ).toBe(true);
  587 |       }
  588 |       logger.pass('All assertions passed');
  589 |     });
  590 | 
  591 |     await logger.step('Step 4 â€" BUG-TPAY-04: refunds.isPositive must be true or null when postiveValue is "0%"', async () => {
  592 |       const ref = res!.body.data.refunds as PaymentSection;
  593 |       const pct  = ref.postiveValue as string;
  594 |       const flag = ref.isPositive;
  595 | 
  596 |       allure.parameter('refunds.isPositive',  String(flag));
  597 |       allure.parameter('refunds.postiveValue', String(pct));
  598 | 
  599 |       const isZeroButFalse = flag === false && pct === '0%';
  600 |       if (isZeroButFalse) {
  601 |         flagIssue('TC-DTPAY-005', 'BUG-TPAY-04',
  602 |           'refunds.isPositive=false when postiveValue="0%" â€" zero change is neutral, not negative; isPositive should be true or null',
  603 |           { isPositive: flag, postiveValue: pct },
  604 |         );
  605 |       }
  606 | 
  607 |       allure.parameter('Zero-but-false contradiction', String(isZeroButFalse));
  608 | 
  609 |       logger.info('Asserting: refunds.isPositive is not false when postiveValue is "0%" (zero is neutral)');
  610 |       // 0% change is neutral â€" isPositive=false is wrong
  611 |       if (pct === '0%') {
  612 |         expect.soft(flag,
  613 |           '[BUG-TPAY-04] refunds.isPositive should be true (or null) when postiveValue="0%" â€" zero change is not negative',
  614 |         ).not.toBe(false);
  615 |       }
  616 |       logger.pass('All assertions passed');
  617 |     });
  618 | 
  619 |     await logger.step('Step 5 â€" payments.isPositive must agree with postiveValue (sanity check)', async () => {
  620 |       const pay = res!.body.data.payments as PaymentSection;
  621 |       const pct  = pay.postiveValue as string;
  622 |       const flag = pay.isPositive;
  623 | 
  624 |       allure.parameter('payments.isPositive',  String(flag));
  625 |       allure.parameter('payments.postiveValue', String(pct));
```