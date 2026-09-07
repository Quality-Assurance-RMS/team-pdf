# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transactions/pg-webhook-transactions.spec.ts >> RMS — PG Webhook Transactions (supertest) >> TC1 + TC2 — Happy Path and Duplicate Dedup >> TC1 — Successful Transaction — Happy Path
- Location: tests/API/transactions/pg-webhook-transactions.spec.ts:384:9

# Error details

```
Error: Expected HTTP 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 401
```

# Test source

```ts
  326 |       amountDue:                5200.00,
  327 |       paymentType:              'FULL',
  328 |       invoiceId:                'IJKL9015',
  329 |       sessionId:                'f6a7b8c9-d0e1-2345-ef01-234567890abc',
  330 |     },
  331 |     gateway_response: {
  332 |       paymentReference:          'TXN-FAILED001',
  333 |       transactionStatus:         'FAILED',
  334 |       transactionConfirmationId: null,
  335 |       transactionTimestamp:      now.toISOString(),
  336 |       failureId:                 'ERR-001',
  337 |       failureCode:               'INSUFFICIENT_FUNDS',
  338 |       failureReason:             'Customer has insufficient funds',
  339 |       paymentMethod:             'MPESA',
  340 |       paymentMethodCategory:     'MOBILE_MONEY',
  341 |       paymentMethodDetails: {
  342 |         wallet: {
  343 |           mobileNumber:         '+254734567890',
  344 |           walletProvider:       'MPESA',
  345 |           walletUserName:       'Faith Njeri',
  346 |           walletTransactionRef: 'MPESA-FAILED001',
  347 |         },
  348 |       },
  349 |       grossAmount:         5252.00,
  350 |       amountPaid:          0,
  351 |       netAmount:           5200.00,
  352 |       transactionCurrency: 'KES',
  353 |       commission: {
  354 |         pg_fee:              0,
  355 |         platform_commission: 52.00,
  356 |         handling_charges:    null,
  357 |         taxes:               null,
  358 |         total:               52.00,
  359 |       },
  360 |       pgSettlementDate:   null,
  361 |       pgSettlementId:     null,
  362 |       settlementStatus:   'PENDING',
  363 |       refundEligible:     false,
  364 |       refundDeadlineDays: 0,
  365 |     },
  366 |   };
  367 | }
  368 | 
  369 | // ═════════════════════════════════════════════════════════════════════════════
  370 | // Test Suite
  371 | // ═════════════════════════════════════════════════════════════════════════════
  372 | 
  373 | test.describe('RMS — PG Webhook Transactions (supertest)', () => {
  374 |   test.setTimeout(60_000);
  375 | 
  376 |   // ──────────────────────────────────────────────────────────────────────────
  377 |   // TC1 + TC2 run serially: TC2 requires TC1's idempotencyKey to be in Redis
  378 |   // ──────────────────────────────────────────────────────────────────────────
  379 |   test.describe.serial('TC1 + TC2 — Happy Path and Duplicate Dedup', () => {
  380 | 
  381 |     // ────────────────────────────────────────────────────────────────────────
  382 |     // TC1 — Successful Transaction (Happy Path)
  383 |     // ────────────────────────────────────────────────────────────────────────
  384 |     test('TC1 — Successful Transaction — Happy Path', async ({ logger }) => {
  385 |       await allure.description(
  386 |         'Verifies the complete end-to-end flow for a successful MPESA transaction. ' +
  387 |         'Pre-condition: all three services running (:8080, :8081, :8082), Redis and PostgreSQL up.',
  388 |       );
  389 |       await allure.label('feature',  'PG Webhook');
  390 |       await allure.label('story',    'Happy Path');
  391 |       await allure.label('severity', 'blocker');
  392 |       await allure.label('priority', 'P0');
  393 |       await allure.label('testId',   'TC1');
  394 |       await allure.label('type',     'Positive');
  395 | 
  396 |       const now         = new Date();
  397 |       const basePayload = buildHappyPathPayload(now);
  398 |       let   signature   = '';
  399 | 
  400 |       // ── Step 1: Get HMAC signature ──────────────────────────────────────
  401 |       await logger.step('Step 1 — POST /dev/sign to obtain HMAC signature', async () => {
  402 |         logger.info('POST /pgwebhook/dev/sign');
  403 |         signature = await obtainSignature(basePayload);
  404 |         logger.pass('HTTP 200 received');
  405 | 
  406 |         // Persist for TC2
  407 |         tc1Signature = signature;
  408 |         tc1Payload   = { ...basePayload, response_signature: signature };
  409 | 
  410 |         allure.parameter('Obtained signature', signature);
  411 |         logger.info('Asserting: signature is truthy');
  412 |         expect(signature).toBeTruthy();
  413 |         logger.pass('All assertions passed');
  414 |       });
  415 | 
  416 |       // ── Step 2: Send webhook ────────────────────────────────────────────
  417 |       await logger.step('Step 2 — POST /transactions — expect HTTP 200 with status "received"', async () => {
  418 |         logger.info('POST /pgwebhook/transactions');
  419 |         const res = await postWebhook(tc1Payload, signature);
  420 |         logger.pass('HTTP ' + res.status + ' received');
  421 | 
  422 |         allure.parameter('HTTP status',    String(res.status));
  423 |         allure.parameter('Response body',  JSON.stringify(res.body));
  424 | 
  425 |         logger.info('Asserting: HTTP 200 and body.status is "received"');
> 426 |         expect(res.status, 'Expected HTTP 200').toBe(200);
      |                                                 ^ Error: Expected HTTP 200
  427 |         expect(res.body.status, 'Response body status should be "received"').toBe('received');
  428 |         logger.pass('All assertions passed');
  429 |       });
  430 | 
  431 |       // ── Step 3: Verify PostgreSQL ───────────────────────────────────────
  432 |       await logger.step('Step 3 — Verify PostgreSQL: row stored with correct recon_key, amounts, commission', async () => {
  433 |         const rows = await queryPgTransactions({ idempotency_key: TC1_IDEMPOTENCY_KEY });
  434 | 
  435 |         logger.info('Asserting: exactly 1 row in pg_transactions with correct fields');
  436 |         expect(rows.length, 'Exactly 1 row should be stored in pg_transactions').toBe(1);
  437 |         const row = rows[0];
  438 | 
  439 |         allure.parameter('invoice_number', String(row.invoice_number));
  440 |         allure.parameter('recon_key',      String(row.recon_key));
  441 |         allure.parameter('gross_amount',   String(row.gross_amount));
  442 |         allure.parameter('net_amount',     String(row.net_amount));
  443 | 
  444 |         expect(row.invoice_number,       'invoice_number').toBe('IJKL9012');
  445 |         expect(row.recon_key,            'recon_key').toBe('IJKL9012~KES~5200.00');
  446 |         expect(Number(row.gross_amount), 'gross_amount').toBe(5252.00);
  447 |         expect(Number(row.net_amount),   'net_amount').toBe(5200.00);
  448 |         expect(row.transaction_status,   'transaction_status').toBe('SUCCESS');
  449 |         expect(row.recon_status,         'recon_status').toBe('PENDING');
  450 |         expect(row.wallet_provider,      'wallet_provider').toBe('MPESA');
  451 | 
  452 |         const commission = row.commission as Record<string, unknown> | null;
  453 |         expect(Number(commission?.platform_commission), 'commission.platform_commission').toBe(52.00);
  454 |         expect(Number(commission?.total),               'commission.total').toBe(52.00);
  455 |         logger.pass('All assertions passed');
  456 |       });
  457 | 
  458 |       // ── Step 4: Verify MongoDB ──────────────────────────────────────────
  459 |       await logger.step('Step 4 — Verify MongoDB: 1 document in raw_pg_events with correct idempotency_key', async () => {
  460 |         const docs = await queryMongoEvents({ idempotency_key: TC1_IDEMPOTENCY_KEY });
  461 | 
  462 |         allure.parameter('MongoDB document count', String(docs.length));
  463 |         logger.info('Asserting: 1 document in raw_pg_events with correct idempotency_key');
  464 |         expect(docs.length, '1 document should exist in raw_pg_events').toBe(1);
  465 |         expect(String(docs[0].idempotency_key ?? docs[0].idempotencyKey), 'MongoDB idempotency_key').toBe(TC1_IDEMPOTENCY_KEY);
  466 |         logger.pass('All assertions passed');
  467 |       });
  468 |     });
  469 | 
  470 |     // ────────────────────────────────────────────────────────────────────────
  471 |     // TC2 — Exact Duplicate — Redis Dedup
  472 |     // ────────────────────────────────────────────────────────────────────────
  473 |     test('TC2 — Exact Duplicate — Redis Dedup', async ({ logger }) => {
  474 |       await allure.description(
  475 |         'Verifies that sending the same idempotencyKey twice is caught by Redis and ' +
  476 |         'rejected without inserting a duplicate row. ' +
  477 |         'Pre-condition: TC1 must have run first so the key is cached in Redis.',
  478 |       );
  479 |       await allure.label('feature',  'PG Webhook');
  480 |       await allure.label('story',    'Duplicate Dedup');
  481 |       await allure.label('severity', 'critical');
  482 |       await allure.label('priority', 'P1');
  483 |       await allure.label('testId',   'TC2');
  484 |       await allure.label('type',     'Negative / Dedup');
  485 | 
  486 |       // ── Step 1: Re-send exact TC1 payload and signature ─────────────────
  487 |       await logger.step('Step 1 — Re-send exact same payload and X-Response-Signature from TC1', async () => {
  488 |         logger.info('POST /pgwebhook/transactions (duplicate idempotency key)');
  489 |         const res = await postWebhook(tc1Payload, tc1Signature);
  490 |         logger.pass('HTTP ' + res.status + ' received');
  491 | 
  492 |         allure.parameter('HTTP status',    String(res.status));
  493 |         allure.parameter('Response body',  JSON.stringify(res.body));
  494 | 
  495 |         logger.info('Asserting: HTTP 200 and error_code is DUPLICATE_WEBHOOK');
  496 |         expect(res.status, 'Expected HTTP 200 (duplicate still returns 200)').toBe(200);
  497 |         expect(res.body.error_code, 'error_code should be DUPLICATE_WEBHOOK').toBe('DUPLICATE_WEBHOOK');
  498 |         logger.pass('All assertions passed');
  499 |       });
  500 | 
  501 |       // ── Step 2: Verify only 1 row in PostgreSQL ─────────────────────────
  502 |       await logger.step('Step 2 — Verify PostgreSQL: still only 1 row — Redis blocked duplicate before DB write', async () => {
  503 |         const rows = await queryPgTransactions({ idempotency_key: TC1_IDEMPOTENCY_KEY });
  504 | 
  505 |         allure.parameter('Row count',    String(rows.length));
  506 |         allure.parameter('is_duplicate', String(rows[0]?.is_duplicate));
  507 | 
  508 |         logger.info('Asserting: row count is still 1 and is_duplicate is false');
  509 |         expect(rows.length, 'Row count must still be 1').toBe(1);
  510 |         expect(rows[0].is_duplicate, 'Original row is_duplicate should remain false').toBe(false);
  511 |         logger.pass('All assertions passed');
  512 |       });
  513 | 
  514 |     }); // end serial describe
  515 | 
  516 |   }); // end serial describe
  517 | 
  518 |   // ──────────────────────────────────────────────────────────────────────────
  519 |   // TC3 — Invalid HMAC Signature — Security
  520 |   // ──────────────────────────────────────────────────────────────────────────
  521 |   test('TC3 — Invalid HMAC Signature — Security', async ({ logger }) => {
  522 |     await allure.description(
  523 |       'Verifies that a webhook carrying a zeroed-out (tampered) HMAC signature is ' +
  524 |       'rejected with HTTP 401 Unauthorized and nothing is written to PostgreSQL or MongoDB.',
  525 |     );
  526 |     await allure.label('feature',  'PG Webhook');
```