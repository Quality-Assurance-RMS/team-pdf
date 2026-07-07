# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/transactions/pg-webhook-transactions.spec.ts >> RMS — PG Webhook Transactions (supertest) >> TC5 — Failed Transaction — INSUFFICIENT_FUNDS
- Location: tests/API/transactions/pg-webhook-transactions.spec.ts:639:7

# Error details

```
Error: Expected HTTP 200 — FAILED txns must be stored

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 401
```

# Test source

```ts
  579 |       'Pre-condition: Do not resend — generate a fresh request with a future expiry instead.',
  580 |     );
  581 |     await allure.label('feature',  'PG Webhook');
  582 |     await allure.label('story',    'Validation — Expired Timestamp');
  583 |     await allure.label('severity', 'critical');
  584 |     await allure.label('priority', 'P1');
  585 |     await allure.label('testId',   'TC4');
  586 |     await allure.label('type',     'Validation / Negative');
  587 | 
  588 |     const basePayload = buildExpiredTimestampPayload();
  589 |     let   signature   = '';
  590 | 
  591 |     // ── Step 1: Get signature for expired-timestamp payload ──────────────
  592 |     await logger.step('Step 1 — POST /dev/sign to obtain HMAC signature for expired-timestamp payload', async () => {
  593 |       logger.info('POST /pgwebhook/dev/sign (expired timestamp payload)');
  594 |       signature = await obtainSignature(basePayload);
  595 |       logger.pass('HTTP 200 received');
  596 | 
  597 |       allure.parameter('requestExpiryTimestamp', '2026-05-06T08:15:00.000Z (past)');
  598 |       allure.parameter('Obtained signature',     signature);
  599 |     });
  600 | 
  601 |     // ── Step 2: Send webhook, expect HTTP 400 ────────────────────────────
  602 |     await logger.step('Step 2 — POST /transactions — expect HTTP 400 with requestExpiryTimestamp error', async () => {
  603 |       const signedPayload = { ...basePayload, response_signature: signature };
  604 |       logger.info('POST /pgwebhook/transactions (expired timestamp)');
  605 |       const res = await postWebhook(signedPayload, signature);
  606 |       logger.pass('HTTP ' + res.status + ' received');
  607 | 
  608 |       allure.parameter('HTTP status',    String(res.status));
  609 |       allure.parameter('Response body',  JSON.stringify(res.body));
  610 | 
  611 |       logger.info('Asserting: HTTP 400 and error identifies requestExpiryTimestamp with "expired"');
  612 |       expect(res.status, 'Expected HTTP 400 Bad Request').toBe(400);
  613 | 
  614 |       const errorField   = res.body.field   ?? res.body.errors?.[0]?.field;
  615 |       const errorMessage = String(res.body.message ?? res.body.errors?.[0]?.message ?? '');
  616 | 
  617 |       allure.parameter('error field',   String(errorField));
  618 |       allure.parameter('error message', errorMessage);
  619 | 
  620 |       expect(errorField,                   'Error field should identify requestExpiryTimestamp').toBe('requestExpiryTimestamp');
  621 |       expect(errorMessage.toLowerCase(),   'Error message should mention "expired"').toContain('expired');
  622 |       logger.pass('All assertions passed');
  623 |     });
  624 | 
  625 |     // ── Step 3: Verify nothing stored in PostgreSQL ──────────────────────
  626 |     await logger.step('Step 3 — Verify PostgreSQL: 0 rows for TXN-EXPIRED001', async () => {
  627 |       const rows = await queryPgTransactions({ payment_reference: 'TXN-EXPIRED001' });
  628 | 
  629 |       allure.parameter('Row count', String(rows.length));
  630 |       logger.info('Asserting: 0 rows stored after 400');
  631 |       expect(rows.length, '0 rows — nothing stored after 400').toBe(0);
  632 |       logger.pass('All assertions passed');
  633 |     });
  634 |   });
  635 | 
  636 |   // ──────────────────────────────────────────────────────────────────────────
  637 |   // TC5 — Failed Transaction — INSUFFICIENT_FUNDS
  638 |   // ──────────────────────────────────────────────────────────────────────────
  639 |   test('TC5 — Failed Transaction — INSUFFICIENT_FUNDS', async ({ logger }) => {
  640 |     await allure.description(
  641 |       'Verifies that a FAILED transaction from the PG is stored correctly with ' +
  642 |       'failure details and recon_status PENDING. ' +
  643 |       'RMS stores all transactions regardless of gateway status — the reconciliation ' +
  644 |       'engine processes them separately.',
  645 |     );
  646 |     await allure.label('feature',  'PG Webhook');
  647 |     await allure.label('story',    'Failed Transaction Handling');
  648 |     await allure.label('severity', 'critical');
  649 |     await allure.label('priority', 'P1');
  650 |     await allure.label('testId',   'TC5');
  651 |     await allure.label('type',     'Negative / Storage');
  652 | 
  653 |     const now         = new Date();
  654 |     const basePayload = buildFailedTransactionPayload(now);
  655 |     let   signature   = '';
  656 | 
  657 |     // ── Step 1: Get signature for FAILED transaction payload ─────────────
  658 |     await logger.step('Step 1 — POST /dev/sign to obtain HMAC signature for FAILED transaction payload', async () => {
  659 |       logger.info('POST /pgwebhook/dev/sign (FAILED transaction payload)');
  660 |       signature = await obtainSignature(basePayload);
  661 |       logger.pass('HTTP 200 received');
  662 | 
  663 |       allure.parameter('transactionStatus', 'FAILED');
  664 |       allure.parameter('failureCode',       'INSUFFICIENT_FUNDS');
  665 |       allure.parameter('Obtained signature', signature);
  666 |     });
  667 | 
  668 |     // ── Step 2: Send webhook, expect HTTP 200 ────────────────────────────
  669 |     await logger.step('Step 2 — POST /transactions — expect HTTP 200 (FAILED transactions are accepted and stored)', async () => {
  670 |       const signedPayload = { ...basePayload, response_signature: signature };
  671 |       logger.info('POST /pgwebhook/transactions (FAILED transaction)');
  672 |       const res = await postWebhook(signedPayload, signature);
  673 |       logger.pass('HTTP ' + res.status + ' received');
  674 | 
  675 |       allure.parameter('HTTP status',    String(res.status));
  676 |       allure.parameter('Response body',  JSON.stringify(res.body));
  677 | 
  678 |       logger.info('Asserting: HTTP 200 and body.status is "received"');
> 679 |       expect(res.status, 'Expected HTTP 200 — FAILED txns must be stored').toBe(200);
      |                                                                            ^ Error: Expected HTTP 200 — FAILED txns must be stored
  680 |       expect(res.body.status, 'Response body status should be "received"').toBe('received');
  681 |       logger.pass('All assertions passed');
  682 |     });
  683 | 
  684 |     // ── Step 3: Verify PostgreSQL ─────────────────────────────────────────
  685 |     await logger.step('Step 3 — Verify PostgreSQL: row stored with FAILED status and INSUFFICIENT_FUNDS failure details', async () => {
  686 |       const rows = await queryPgTransactions({ idempotency_key: 'f6a7b8c9-d0e1-2345-ef01-234567890abc' });
  687 | 
  688 |       logger.info('Asserting: 1 row with FAILED status, INSUFFICIENT_FUNDS failure_code, PENDING recon_status');
  689 |       expect(rows.length, 'Exactly 1 row should be stored').toBe(1);
  690 |       const row = rows[0];
  691 | 
  692 |       allure.parameter('transaction_status', String(row.transaction_status));
  693 |       allure.parameter('failure_code',       String(row.failure_code));
  694 |       allure.parameter('failure_reason',     String(row.failure_reason));
  695 |       allure.parameter('recon_status',       String(row.recon_status));
  696 | 
  697 |       expect(row.transaction_status, 'transaction_status').toBe('FAILED');
  698 |       expect(row.failure_code,       'failure_code').toBe('INSUFFICIENT_FUNDS');
  699 |       expect(row.failure_reason,     'failure_reason').toBe('Customer has insufficient funds');
  700 |       expect(row.recon_status,       'recon_status').toBe('PENDING');
  701 |       expect(row.is_duplicate,       'is_duplicate').toBe(false);
  702 |       logger.pass('All assertions passed');
  703 |     });
  704 | 
  705 |     // ── Step 4: Verify MongoDB ────────────────────────────────────────────
  706 |     await logger.step('Step 4 — Verify MongoDB: 1 document in raw_pg_events with transaction_status FAILED', async () => {
  707 |       const docs = await queryMongoEvents({ idempotency_key: 'f6a7b8c9-d0e1-2345-ef01-234567890abc' });
  708 | 
  709 |       logger.info('Asserting: 1 document in MongoDB with transaction_status FAILED');
  710 |       expect(docs.length, '1 document should exist in MongoDB audit').toBe(1);
  711 |       const doc    = docs[0];
  712 |       const status = doc.transaction_status
  713 |         ?? (doc.gateway_response as Record<string, unknown> | null)?.transactionStatus;
  714 | 
  715 |       allure.parameter('MongoDB document count',        String(docs.length));
  716 |       allure.parameter('MongoDB transaction_status',    String(status ?? ''));
  717 | 
  718 |       expect(status, 'MongoDB document transaction_status should be FAILED').toBe('FAILED');
  719 |       logger.pass('All assertions passed');
  720 |     });
  721 |   });
  722 | 
  723 | });
  724 | 
```