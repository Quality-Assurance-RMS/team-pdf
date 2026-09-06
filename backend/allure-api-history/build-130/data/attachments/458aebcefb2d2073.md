# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/pg-webhook.spec.ts >> RMS — PG Webhook Integration >> TC3 — Invalid HMAC Signature — Security
- Location: tests/API/pg-webhook.spec.ts:311:7

# Error details

```
Error: Expected HTTP 401 Unauthorized

expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 405
```

# Test source

```ts
  308 |   // =========================================================================
  309 |   // TC3 — Invalid HMAC Signature
  310 |   // =========================================================================
  311 |   test(
  312 |     'TC3 — Invalid HMAC Signature — Security',
  313 |     async ({ request, logger }) => {
  314 |       await allure.description(
  315 |         'Verifies that a webhook carrying a zeroed-out HMAC signature is rejected with ' +
  316 |         'HTTP 401 Unauthorized and nothing is written to PostgreSQL or MongoDB.',
  317 |       );
  318 |       await allure.label('feature',  'PG Webhook');
  319 |       await allure.label('story',    'Security — HMAC Validation');
  320 |       await allure.label('severity', 'blocker');
  321 |       await allure.label('priority', 'P0');
  322 |       await allure.label('testId',   'TC3');
  323 |       await allure.label('type',     'Security / Negative');
  324 | 
  325 |       const ZEROED_SIGNATURE = '0'.repeat(64);
  326 | 
  327 |       const payload = {
  328 |         response_signature: ZEROED_SIGNATURE,
  329 |         eCitizen_platform_data: {
  330 |           paymentGatewayId:         'PG_001',
  331 |           apiVersion:               'v1',
  332 |           idempotencyKey:           'aaaabbbb-cccc-dddd-eeee-ffffaaaabbbb',
  333 |           requestTimestamp:         '2026-05-06T10:45:00.000Z',
  334 |           requestExpiryTimestamp:   '2026-05-06T23:59:00.000Z',
  335 |           payerId:                  'KE-NID-56789012',
  336 |           payerName:                'Faith Njeri',
  337 |           payerEmail:               'faith.njeri@gmail.com',
  338 |           payerPhone:               '+254734567890',
  339 |           payerIdType:              'NATIONAL_ID',
  340 |           payerIdNumber:            '56789012',
  341 |           payerNationality:         'KE',
  342 |           payerType:                'CITIZEN',
  343 |           mcdaCode:                 'MRCH-0234',
  344 |           mcdaName:                 'National Transport and Safety Authority',
  345 |           serviceCode:              'NTSA-003',
  346 |           serviceName:              'Vehicle Inspection',
  347 |           serviceCategory:          'Transport',
  348 |           clientReferenceNumber:    '1020260510',
  349 |           correlationUTR:           'IJKL9013',
  350 |           serviceAmount:            5200.00,
  351 |           amountDue:                5200.00,
  352 |           paymentType:              'FULL',
  353 |           invoiceId:                'IJKL9013',
  354 |           sessionId:                'aaaabbbb-cccc-dddd-eeee-ffffaaaabbbb',
  355 |         },
  356 |         gateway_response: {
  357 |           paymentReference:         'TXN-INVALID001',
  358 |           transactionStatus:        'SUCCESS',
  359 |           transactionConfirmationId:'CONF-INVALID001',
  360 |           transactionTimestamp:     '2026-05-06T10:45:02.000Z',
  361 |           failureId:                null,
  362 |           failureCode:              null,
  363 |           failureReason:            null,
  364 |           paymentMethod:            'MPESA',
  365 |           paymentMethodCategory:    'MOBILE_MONEY',
  366 |           paymentMethodDetails: {
  367 |             wallet: {
  368 |               mobileNumber:         '+254734567890',
  369 |               walletProvider:       'MPESA',
  370 |               walletUserName:       'Faith Njeri',
  371 |               walletTransactionRef: 'MPESA-INVALID001',
  372 |             },
  373 |           },
  374 |           grossAmount:              5252.00,
  375 |           amountPaid:               5252.00,
  376 |           netAmount:                5200.00,
  377 |           transactionCurrency:      'KES',
  378 |           commission: {
  379 |             pg_fee:               0,
  380 |             platform_commission:  52.00,
  381 |             handling_charges:     null,
  382 |             taxes:                null,
  383 |             total:                52.00,
  384 |           },
  385 |           pgSettlementDate:         '2026-05-08',
  386 |           pgSettlementId:           '26487111',
  387 |           settlementStatus:         'PENDING',
  388 |           refundEligible:           true,
  389 |           refundDeadlineDays:       30,
  390 |         },
  391 |       };
  392 | 
  393 |       // Step 1 — Send with zeroed-out signature
  394 |       await logger.step(
  395 |         'Step 1: POST to /transactions with zeroed-out X-Response-Signature',
  396 |         async () => {
  397 |           logger.info('POST /transactions — sending webhook with zeroed-out HMAC signature');
  398 |           const res = await request.post(WEBHOOK_URL, {
  399 |             headers: {
  400 |               'Content-Type':         'application/json',
  401 |               'X-Response-Signature': ZEROED_SIGNATURE,
  402 |             },
  403 |             data: payload,
  404 |           });
  405 |           logger.pass('HTTP ' + res.status() + ' received');
  406 |           allure.parameter('HTTP Status', String(res.status()));
  407 |           logger.info('Asserting: HTTP 401 Unauthorized');
> 408 |           expect(res.status(), 'Expected HTTP 401 Unauthorized').toBe(401);
      |                                                                  ^ Error: Expected HTTP 401 Unauthorized
  409 |           logger.pass('All assertions passed');
  410 |         },
  411 |       );
  412 | 
  413 |       // Step 2 — Verify nothing stored in PostgreSQL
  414 |       await logger.step(
  415 |         'Step 2: Verify 0 rows in PostgreSQL for TXN-INVALID001',
  416 |         async () => {
  417 |           logger.info('GET /internal/pg-transactions — verifying no row stored after 401');
  418 |           const rows = await queryPgTransactions(request, {
  419 |             payment_reference: 'TXN-INVALID001',
  420 |           });
  421 |           logger.pass('HTTP 200 received');
  422 |           logger.info('Asserting: row count is 0');
  423 |           expect(rows.length, 'Row count should be 0 — nothing stored after 401').toBe(0);
  424 |           allure.parameter('Row count', '0');
  425 |           logger.pass('All assertions passed');
  426 |         },
  427 |       );
  428 | 
  429 |       // Step 3 — Verify nothing stored in MongoDB
  430 |       await logger.step(
  431 |         'Step 3: Verify 0 documents in MongoDB raw_pg_events for this idempotency_key',
  432 |         async () => {
  433 |           logger.info('GET /internal/raw-pg-events — verifying no document stored after 401');
  434 |           const docs = await queryMongoEvents(request, {
  435 |             idempotency_key: 'aaaabbbb-cccc-dddd-eeee-ffffaaaabbbb',
  436 |           });
  437 |           logger.pass('HTTP 200 received');
  438 |           logger.info('Asserting: document count is 0');
  439 |           expect(docs.length, '0 documents in MongoDB — rejected before any write').toBe(0);
  440 |           allure.parameter('MongoDB document count', '0');
  441 |           logger.pass('All assertions passed');
  442 |         },
  443 |       );
  444 |     },
  445 |   );
  446 | 
  447 |   // =========================================================================
  448 |   // TC4 — Expired requestExpiryTimestamp
  449 |   // =========================================================================
  450 |   test(
  451 |     'TC4 — Expired requestExpiryTimestamp — Validation',
  452 |     async ({ request, logger }) => {
  453 |       await allure.description(
  454 |         'Verifies that a webhook whose requestExpiryTimestamp is in the past is rejected ' +
  455 |         'with HTTP 400 bad_request. The HMAC signature is valid; only the timestamp fails.',
  456 |       );
  457 |       await allure.label('feature',  'PG Webhook');
  458 |       await allure.label('story',    'Validation — Expired Timestamp');
  459 |       await allure.label('severity', 'critical');
  460 |       await allure.label('priority', 'P1');
  461 |       await allure.label('testId',   'TC4');
  462 |       await allure.label('type',     'Validation / Negative');
  463 | 
  464 |       const basePayload = {
  465 |         response_signature: '',
  466 |         eCitizen_platform_data: {
  467 |           paymentGatewayId:         'PG_001',
  468 |           apiVersion:               'v1',
  469 |           idempotencyKey:           'e5f6a7b8-c9d0-1234-def5-111122223333',
  470 |           requestTimestamp:         '2026-05-06T08:00:00.000Z',
  471 |           requestExpiryTimestamp:   '2026-05-06T08:15:00.000Z', // deliberately in the past
  472 |           payerId:                  'KE-NID-56789012',
  473 |           payerName:                'Faith Njeri',
  474 |           payerEmail:               'faith.njeri@gmail.com',
  475 |           payerPhone:               '+254734567890',
  476 |           payerIdType:              'NATIONAL_ID',
  477 |           payerIdNumber:            '56789012',
  478 |           payerNationality:         'KE',
  479 |           payerType:                'CITIZEN',
  480 |           mcdaCode:                 'MRCH-0234',
  481 |           mcdaName:                 'National Transport and Safety Authority',
  482 |           serviceCode:              'NTSA-003',
  483 |           serviceName:              'Vehicle Inspection',
  484 |           serviceCategory:          'Transport',
  485 |           clientReferenceNumber:    '1020260510',
  486 |           correlationUTR:           'IJKL9014',
  487 |           serviceAmount:            5200.00,
  488 |           amountDue:                5200.00,
  489 |           paymentType:              'FULL',
  490 |           invoiceId:                'IJKL9014',
  491 |           sessionId:                'e5f6a7b8-c9d0-1234-def5-111122223333',
  492 |         },
  493 |         gateway_response: {
  494 |           paymentReference:         'TXN-EXPIRED001',
  495 |           transactionStatus:        'SUCCESS',
  496 |           transactionConfirmationId:'CONF-EXPIRED001',
  497 |           transactionTimestamp:     '2026-05-06T08:00:02.000Z',
  498 |           failureId:                null,
  499 |           failureCode:              null,
  500 |           failureReason:            null,
  501 |           paymentMethod:            'MPESA',
  502 |           paymentMethodCategory:    'MOBILE_MONEY',
  503 |           paymentMethodDetails: {
  504 |             wallet: {
  505 |               mobileNumber:         '+254734567890',
  506 |               walletProvider:       'MPESA',
  507 |               walletUserName:       'Faith Njeri',
  508 |               walletTransactionRef: 'MPESA-EXPIRED001',
```