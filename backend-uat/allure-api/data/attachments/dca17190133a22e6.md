# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-today-payments.spec.ts >> Backend (Konza) â€" Dashboard Today Payments >> TC-DTPAY-004 â€" BUG-TPAY-02: Monetary amounts must be numbers, not strings
- Location: tests/API/backend-tests/backend-dashboard-today-payments.spec.ts:432:7

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
  433 |     backendLabels('TC-DTPAY-004', 'BUG-TPAY-02 â€" Amount Types as Strings', 'critical');
  434 | 
  435 |     let res: supertest.Response;
  436 | 
  437 |     await logger.step('Step 1 â€" Fetch today-payments response', async () => {
  438 |       logger.info('GET /api/v1/dashboard/today-payments');
  439 |       res = await get();
  440 |       logger.pass('HTTP ' + res.status + ' received');
  441 |       attachResponse('TC-DTPAY-004', res, { bug: 'BUG-TPAY-02' });
  442 |     });
  443 | 
  444 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  445 |       logger.info('Asserting: HTTP 200');
  446 |       expect(res!.status).toBe(200);
  447 |       logger.pass('All assertions passed');
  448 |     });
  449 | 
  450 |     await logger.step('Step 3 â€" All monetary fields in payments must be numbers', async () => {
  451 |       const pay = res!.body.data.payments as PaymentSection;
  452 |       const amountFields = [
  453 |         'successfullPaymentKES',
  454 |         'successfullPaymentDoller',
  455 |         'failerfullPaymentKES',
  456 |         'failerfullPaymentDoller',
  457 |       ] as const;
  458 | 
  459 |       logger.info('Asserting: all monetary fields in payments section are numbers, not strings');
  460 |       for (const field of amountFields) {
  461 |         const val = pay[field];
  462 |         const isString = typeof val === 'string';
  463 | 
  464 |         if (isString) {
  465 |           flagIssue('TC-DTPAY-004', 'BUG-TPAY-02',
  466 |             `payments.${field} is returned as a string "${val}" â€" monetary values must be numbers for arithmetic`,
  467 |             { field, actual_type: typeof val, actual_value: val, expected_type: 'number' },
  468 |           );
  469 |         }
  470 | 
  471 |         allure.parameter(`payments.${field} type`, typeof val);
  472 |         expect.soft(typeof val, `[BUG-TPAY-02] payments.${field} must be a number, got "${typeof val}" ("${val}")`).toBe('number');
  473 |       }
  474 |       logger.pass('All assertions passed');
  475 |     });
  476 | 
  477 |     await logger.step('Step 4 â€" All monetary fields in refunds must be numbers', async () => {
  478 |       const ref = res!.body.data.refunds as PaymentSection;
  479 |       const amountFields = [
  480 |         'successfullPaymentKES',
  481 |         'successfullPaymentDoller',
  482 |         'failerfullPaymentKES',
  483 |         'failerfullPaymentDoller',
  484 |       ] as const;
  485 | 
  486 |       logger.info('Asserting: all monetary fields in refunds section are numbers, not strings');
  487 |       for (const field of amountFields) {
  488 |         const val = ref[field];
  489 |         if (typeof val === 'string') {
  490 |           flagIssue('TC-DTPAY-004', 'BUG-TPAY-02',
  491 |             `refunds.${field} is a string â€" must be a number`,
  492 |             { field, actual_type: typeof val, actual_value: val },
  493 |           );
  494 |         }
  495 |         allure.parameter(`refunds.${field} type`, typeof val);
  496 |         expect.soft(typeof val, `[BUG-TPAY-02] refunds.${field} must be a number, got "${typeof val}"`).toBe('number');
  497 |       }
  498 |       logger.pass('All assertions passed');
  499 |     });
  500 | 
  501 |     await logger.step('Step 5 â€" All monetary fields in revenue must be numbers', async () => {
  502 |       const rev = res!.body.data.revenue as PaymentSection;
  503 |       const amountFields = [
  504 |         'successfullPaymentKES',
  505 |         'successfullPaymentDoller',
  506 |         'failerfullPaymentKES',
  507 |         'failerfullPaymentDoller',
  508 |       ] as const;
  509 | 
  510 |       logger.info('Asserting: all monetary fields in revenue section are numbers, not strings');
  511 |       for (const field of amountFields) {
  512 |         const val = rev[field];
  513 |         if (typeof val === 'string') {
  514 |           flagIssue('TC-DTPAY-004', 'BUG-TPAY-02',
  515 |             `revenue.${field} is a string â€" must be a number`,
  516 |             { field, actual_type: typeof val, actual_value: val },
  517 |           );
  518 |         }
  519 |         allure.parameter(`revenue.${field} type`, typeof val);
  520 |         expect.soft(typeof val, `[BUG-TPAY-02] revenue.${field} must be a number, got "${typeof val}"`).toBe('number');
  521 |       }
  522 |       logger.pass('All assertions passed');
  523 |     });
  524 | 
  525 |     await logger.step('Step 6 â€" Confirm postiveValue is a string (percentage label â€" acceptable)', async () => {
  526 |       // postiveValue (the percentage string like "-100.0%" or "0%") is expected to be a string
  527 |       // This step is informational â€" documenting that the string type IS acceptable for the label field
  528 |       const payPct = res!.body.data.payments.postiveValue;
  529 |       const refPct = res!.body.data.refunds.postiveValue;
  530 |       const revPct = res!.body.data.revenue.postiveValue;
  531 | 
  532 |       logger.info('Asserting: postiveValue is a string percentage label in all three sections');
> 533 |       expect(typeof payPct, 'payments.postiveValue must be a string percentage label').toBe('string');
      |                                                                                        ^ Error: payments.postiveValue must be a string percentage label
  534 |       expect(typeof refPct, 'refunds.postiveValue must be a string percentage label').toBe('string');
  535 |       expect(typeof revPct, 'revenue.postiveValue must be a string percentage label').toBe('string');
  536 | 
  537 |       allure.parameter('payments.postiveValue', String(payPct));
  538 |       allure.parameter('refunds.postiveValue',  String(refPct));
  539 |       allure.parameter('revenue.postiveValue',  String(revPct));
  540 |       logger.pass('All assertions passed');
  541 |     });
  542 |   });
  543 | 
  544 |   // â"€â"€ TC-DTPAY-005 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  545 |   test('TC-DTPAY-005 â€" BUG-TPAY-03/04: isPositive flag must agree with postiveValue percentage', async ({ logger }) => {
  546 |     backendLabels('TC-DTPAY-005', 'BUG-TPAY-03/04 â€" isPositive vs postiveValue Logic', 'critical');
  547 | 
  548 |     let res: supertest.Response;
  549 | 
  550 |     await logger.step('Step 1 â€" Fetch today-payments response', async () => {
  551 |       logger.info('GET /api/v1/dashboard/today-payments');
  552 |       res = await get();
  553 |       logger.pass('HTTP ' + res.status + ' received');
  554 |       logResponse('TC-DTPAY-005', res, '(isPositive logic check)');
  555 |       attachResponse('TC-DTPAY-005', res, { bug: 'BUG-TPAY-03/BUG-TPAY-04' });
  556 |     });
  557 | 
  558 |     await logger.step('Step 2 â€" Validate HTTP 200', async () => {
  559 |       logger.info('Asserting: HTTP 200');
  560 |       expect(res!.status).toBe(200);
  561 |       logger.pass('All assertions passed');
  562 |     });
  563 | 
  564 |     await logger.step('Step 3 â€" BUG-TPAY-03: revenue.isPositive must not be true when postiveValue is negative', async () => {
  565 |       const rev = res!.body.data.revenue as PaymentSection;
  566 |       const pct  = rev.postiveValue as string;
  567 |       const flag = rev.isPositive;
  568 | 
  569 |       allure.parameter('revenue.isPositive',  String(flag));
  570 |       allure.parameter('revenue.postiveValue', String(pct));
  571 | 
  572 |       const isContradiction = flag === true && typeof pct === 'string' && pct.startsWith('-');
  573 |       if (isContradiction) {
  574 |         flagIssue('TC-DTPAY-005', 'BUG-TPAY-03',
  575 |           `revenue.isPositive=true but postiveValue="${pct}" â€" a negative % change cannot be "positive"`,
  576 |           { isPositive: flag, postiveValue: pct },
  577 |         );
  578 |       }
  579 | 
  580 |       allure.parameter('Contradiction detected', String(isContradiction));
  581 | 
  582 |       logger.info('Asserting: revenue.isPositive agrees with the sign of postiveValue');
  583 |       // If the percentage starts with '-', isPositive MUST be false
  584 |       if (typeof pct === 'string' && pct.startsWith('-')) {
  585 |         expect.soft(flag,
  586 |           `[BUG-TPAY-03] revenue.isPositive should be false when postiveValue="${pct}" (negative percentage)`,
  587 |         ).toBe(false);
  588 |       }
  589 | 
  590 |       // If the percentage is positive (no '-'), isPositive MUST be true
  591 |       if (typeof pct === 'string' && !pct.startsWith('-')) {
  592 |         expect(flag,
  593 |           `revenue.isPositive should be true when postiveValue="${pct}" (non-negative percentage)`,
  594 |         ).toBe(true);
  595 |       }
  596 |       logger.pass('All assertions passed');
  597 |     });
  598 | 
  599 |     await logger.step('Step 4 â€" BUG-TPAY-04: refunds.isPositive must be true or null when postiveValue is "0%"', async () => {
  600 |       const ref = res!.body.data.refunds as PaymentSection;
  601 |       const pct  = ref.postiveValue as string;
  602 |       const flag = ref.isPositive;
  603 | 
  604 |       allure.parameter('refunds.isPositive',  String(flag));
  605 |       allure.parameter('refunds.postiveValue', String(pct));
  606 | 
  607 |       const isZeroButFalse = flag === false && pct === '0%';
  608 |       if (isZeroButFalse) {
  609 |         flagIssue('TC-DTPAY-005', 'BUG-TPAY-04',
  610 |           'refunds.isPositive=false when postiveValue="0%" â€" zero change is neutral, not negative; isPositive should be true or null',
  611 |           { isPositive: flag, postiveValue: pct },
  612 |         );
  613 |       }
  614 | 
  615 |       allure.parameter('Zero-but-false contradiction', String(isZeroButFalse));
  616 | 
  617 |       logger.info('Asserting: refunds.isPositive is not false when postiveValue is "0%" (zero is neutral)');
  618 |       // 0% change is neutral â€" isPositive=false is wrong
  619 |       if (pct === '0%') {
  620 |         expect.soft(flag,
  621 |           '[BUG-TPAY-04] refunds.isPositive should be true (or null) when postiveValue="0%" â€" zero change is not negative',
  622 |         ).not.toBe(false);
  623 |       }
  624 |       logger.pass('All assertions passed');
  625 |     });
  626 | 
  627 |     await logger.step('Step 5 â€" payments.isPositive must agree with postiveValue (sanity check)', async () => {
  628 |       const pay = res!.body.data.payments as PaymentSection;
  629 |       const pct  = pay.postiveValue as string;
  630 |       const flag = pay.isPositive;
  631 | 
  632 |       allure.parameter('payments.isPositive',  String(flag));
  633 |       allure.parameter('payments.postiveValue', String(pct));
```