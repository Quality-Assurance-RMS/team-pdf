# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/invoice-webhook.spec.ts >> NUIS Invoice API — rms-invoice-webhook-listener (Staging) >> TC2 — Multiple line items on one invoice
- Location: tests/API/invoice-webhook.spec.ts:120:7

# Error details

```
Error: invoice POST should return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 401
```

# Test source

```ts
  1   | import { test, expect } from './fixtures';
  2   | import { allure } from 'allure-playwright';
  3   | import supertest from 'supertest';
  4   | import { signInvoicePayload, signInvoicePayloadAtTimestamp } from '../utils/invoiceWebhookHmac';
  5   | import {
  6   |   buildPaidInvoice, buildUnpaidInvoice, buildMultiItemInvoice,
  7   | } from '../utils/invoiceWebhookPayload';
  8   | import { verifyInvoiceInStagingDb, isStagingDbConfigured } from '../utils/invoiceDbVerification';
  9   | 
  10  | // ---------------------------------------------------------------------------
  11  | // NUIS Invoice API — rms-invoice-webhook-listener (Staging / AWS)
  12  | //
  13  | // Endpoint:   POST https://api.rms.dev.demo-fsit.com/invoice-webhook/api/v1/invoices
  14  | //             CONFIRMED LIVE: the PDF's raw text extraction dropped the
  15  | //             hyphen ("invoicewebhook") due to a line-wrap artifact — that
  16  | //             path actually returns 405 (hits the frontend's static route).
  17  | //             The hyphenated path returns 401 (missing/invalid signature),
  18  | //             confirming it's the real endpoint. Same hyphenated form as UAT.
  19  | // Auth:       HMAC — X-Webhook-Timestamp (unix secs) + X-Webhook-Signature
  20  | //             (sha256=<hex>, HMAC_SHA256(secret, "<timestamp>.<raw_body>"))
  21  | // Replay:     signature valid for 5 minutes from generation (either direction)
  22  | // 200 body:   {"status": "accepted"} — processing continues async server-side,
  23  | //             so DB verification waits briefly after a 200 before querying.
  24  | //
  25  | // STATUS: fully unblocked and verified live — HMAC secret, DB host
  26  | // (rms_transactional / staging schema), and endpoint path all confirmed.
  27  | // ---------------------------------------------------------------------------
  28  | 
  29  | const BASE_URL     = process.env.PG_WEBHOOK_BASE_URL ?? 'https://api.rms.dev.demo-fsit.com';
  30  | const INVOICE_PATH = '/invoice-webhook/api/v1/invoices';
  31  | const HMAC_SECRET  = process.env.INVOICE_WEBHOOK_HMAC_SECRET ?? '';
  32  | 
  33  | // No confirmed SLA for how long async processing takes before the invoice is
  34  | // queryable in the DB — this is an assumption, adjust once observed.
  35  | const DB_PROCESSING_WAIT_MS = 5000;
  36  | 
  37  | async function postInvoice(rawBody: string, headers: Record<string, string>) {
  38  |   let req = supertest(BASE_URL).post(INVOICE_PATH).set('Content-Type', 'application/json');
  39  |   for (const [key, value] of Object.entries(headers)) {
  40  |     req = req.set(key, value);
  41  |   }
  42  |   const res = await req.send(rawBody);
  43  |   console.log(`[invoice-webhook] status=${res.status} body=${JSON.stringify(res.body)}`);
  44  |   return res;
  45  | }
  46  | 
  47  | function expectAccepted(res: supertest.Response) {
> 48  |   expect(res.status, 'invoice POST should return 200').toBe(200);
      |                                                        ^ Error: invoice POST should return 200
  49  |   expect(res.body.status, 'response body should be {"status":"accepted"}').toBe('accepted');
  50  | }
  51  | 
  52  | function expect401WithCode(res: supertest.Response, expectedCode: string) {
  53  |   expect(res.status, `expected 401, got ${res.status} — body: ${JSON.stringify(res.body)}`).toBe(401);
  54  |   expect(res.body?.error?.code, `expected error.code=${expectedCode}`).toBe(expectedCode);
  55  | }
  56  | 
  57  | test.describe('NUIS Invoice API — rms-invoice-webhook-listener (Staging)', () => {
  58  | 
  59  |   test.beforeAll(() => {
  60  |     if (!HMAC_SECRET) {
  61  |       console.warn('[invoice-webhook] INVOICE_WEBHOOK_HMAC_SECRET is not set in .env — every request below will fail signature validation.');
  62  |     }
  63  |     if (!isStagingDbConfigured()) {
  64  |       console.warn('[invoice-webhook] Staging DB credentials (STAGING_DB_*) are not set in .env — DB verification steps will be skipped.');
  65  |     }
  66  |   });
  67  | 
  68  |   test('TC1 — Single line item, fully paid invoice — happy path', async ({ logger }) => {
  69  |     await allure.description(
  70  |       'Verifies the core happy path for the NUIS Invoice Webhook: a single-line-item, ' +
  71  |       'fully-paid invoice (matching the integration guide\'s worked example) is accepted ' +
  72  |       'and persisted correctly.\n\n' +
  73  |       '**Endpoint:** POST /invoice-webhook/api/v1/invoices\n' +
  74  |       '**Expected:** HTTP 200 with body {"status":"accepted"}, then a matching row in ' +
  75  |       'staging.pg_invoice_registry (status="Paid") and one row in staging.pg_invoice_items.',
  76  |     );
  77  |     await allure.label('feature', 'NUIS Invoice API');
  78  |     await allure.label('story', 'Happy Path');
  79  |     await allure.label('severity', 'blocker');
  80  | 
  81  |     const invoice = await logger.step('Step 1 — Build a single-item, fully-paid invoice payload', async () => {
  82  |       const inv = buildPaidInvoice();
  83  |       allure.parameter('invoice number', inv.number);
  84  |       allure.parameter('amount_due', inv.amount_due);
  85  |       allure.parameter('status', inv.status);
  86  |       return inv;
  87  |     });
  88  | 
  89  |     const rawBody = JSON.stringify(invoice);
  90  | 
  91  |     const { timestamp, signatureHeader } = await logger.step('Step 2 — Sign the payload (HMAC-SHA256 over "<timestamp>.<raw_body>")', async () => {
  92  |       const signed = signInvoicePayload(rawBody, HMAC_SECRET);
  93  |       allure.parameter('X-Webhook-Timestamp', signed.timestamp);
  94  |       allure.parameter('X-Webhook-Signature', signed.signatureHeader);
  95  |       return signed;
  96  |     });
  97  | 
  98  |     await logger.step(`Step 3 — POST invoice ${invoice.number} and validate the response`, async () => {
  99  |       const res = await postInvoice(rawBody, {
  100 |         'X-Webhook-Timestamp': timestamp,
  101 |         'X-Webhook-Signature': signatureHeader,
  102 |       });
  103 |       expectAccepted(res);
  104 |       logger.pass(`Invoice accepted: ${invoice.number}`);
  105 |     });
  106 | 
  107 |     if (isStagingDbConfigured()) {
  108 |       await logger.step(`Step 4 — Wait ${DB_PROCESSING_WAIT_MS}ms for async processing, then verify DB persistence`, async () => {
  109 |         await new Promise((r) => setTimeout(r, DB_PROCESSING_WAIT_MS));
  110 |         const { registry, items } = await verifyInvoiceInStagingDb(invoice.number);
  111 |         expect(registry, 'invoice_registry row must exist').toBeTruthy();
  112 |         expect(items.length, 'at least one invoice_items row must exist').toBeGreaterThan(0);
  113 |         logger.pass(`DB verified — registry status=${registry?.status}, ${items.length} line item row(s)`);
  114 |       });
  115 |     } else {
  116 |       logger.warn('Step 4 skipped — STAGING_DB_* not configured (non-blocking)');
  117 |     }
  118 |   });
  119 | 
  120 |   test('TC2 — Multiple line items on one invoice', async ({ logger }) => {
  121 |     await allure.description(
  122 |       'Verifies the "single invoice can consist of multiple line items" requirement: ' +
  123 |       'posts one invoice containing 3 distinct line items and confirms every item persists.\n\n' +
  124 |       '**Endpoint:** POST /invoice-webhook/api/v1/invoices\n' +
  125 |       '**Expected:** HTTP 200 with body {"status":"accepted"}, then exactly 3 rows in ' +
  126 |       'staging.pg_invoice_items for this invoice number.',
  127 |     );
  128 |     await allure.label('feature', 'NUIS Invoice API');
  129 |     await allure.label('story', 'Multi-Item Invoices');
  130 |     await allure.label('severity', 'critical');
  131 | 
  132 |     const invoice = await logger.step('Step 1 — Build a 3-line-item invoice payload', async () => {
  133 |       const inv = buildMultiItemInvoice();
  134 |       allure.parameter('invoice number', inv.number);
  135 |       allure.parameter('line item count', String(inv.invoice_items.length));
  136 |       return inv;
  137 |     });
  138 | 
  139 |     const rawBody = JSON.stringify(invoice);
  140 | 
  141 |     const { timestamp, signatureHeader } = await logger.step('Step 2 — Sign the payload', async () => {
  142 |       return signInvoicePayload(rawBody, HMAC_SECRET);
  143 |     });
  144 | 
  145 |     await logger.step(`Step 3 — POST invoice ${invoice.number} (${invoice.invoice_items.length} line items) and validate the response`, async () => {
  146 |       const res = await postInvoice(rawBody, {
  147 |         'X-Webhook-Timestamp': timestamp,
  148 |         'X-Webhook-Signature': signatureHeader,
```