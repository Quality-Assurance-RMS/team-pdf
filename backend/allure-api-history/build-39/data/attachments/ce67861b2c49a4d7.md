# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/pg-webhook.spec.ts >> RMS — PG Webhook Integration >> TC4 — Expired requestExpiryTimestamp — Validation
- Location: tests/API/pg-webhook.spec.ts:450:7

# Error details

```
Error: /dev/sign should return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 405
```

# Test source

```ts
  1   | import { test, expect } from './fixtures';
  2   | import { allure } from 'allure-playwright';
  3   | 
  4   | // ---------------------------------------------------------------------------
  5   | // Environment / Constants
  6   | // ---------------------------------------------------------------------------
  7   | 
  8   | const BASE_URL          = process.env.PG_WEBHOOK_BASE_URL  ?? 'http://localhost:8080';
  9   | const SIGN_URL          = `${BASE_URL}/dev/sign`;
  10  | const WEBHOOK_URL       = `${BASE_URL}/transactions`;
  11  | const INTERNAL_API_URL  = process.env.PG_INTERNAL_API_URL  ?? `${BASE_URL}/internal`;
  12  | 
  13  | // ---------------------------------------------------------------------------
  14  | // TC1 → TC2 shared state (serial describe guarantees ordering)
  15  | // ---------------------------------------------------------------------------
  16  | 
  17  | let tc1SharedPayload: Record<string, unknown> = {};
  18  | let tc1SharedSignature = '';
  19  | 
  20  | // ---------------------------------------------------------------------------
  21  | // Helpers
  22  | // ---------------------------------------------------------------------------
  23  | 
  24  | async function obtainSignature(
  25  |   request: import('@playwright/test').APIRequestContext,
  26  |   payload: object,
  27  | ): Promise<string> {
  28  |   const res = await request.post(SIGN_URL, {
  29  |     headers: { 'Content-Type': 'application/json' },
  30  |     data: payload,
  31  |   });
> 32  |   expect(res.status(), '/dev/sign should return 200').toBe(200);
      |                                                       ^ Error: /dev/sign should return 200
  33  |   const body = await res.json();
  34  |   return body.signature as string;
  35  | }
  36  | 
  37  | async function queryPgTransactions(
  38  |   request: import('@playwright/test').APIRequestContext,
  39  |   params: Record<string, string>,
  40  | ): Promise<Record<string, unknown>[]> {
  41  |   const res = await request.get(`${INTERNAL_API_URL}/pg-transactions`, { params });
  42  |   expect(res.status(), 'Internal PG query should return 200').toBe(200);
  43  |   const data = await res.json();
  44  |   return Array.isArray(data) ? data : (data.rows ?? data.results ?? []);
  45  | }
  46  | 
  47  | async function queryMongoEvents(
  48  |   request: import('@playwright/test').APIRequestContext,
  49  |   params: Record<string, string>,
  50  | ): Promise<Record<string, unknown>[]> {
  51  |   const res = await request.get(`${INTERNAL_API_URL}/raw-pg-events`, { params });
  52  |   expect(res.status(), 'Internal MongoDB query should return 200').toBe(200);
  53  |   const data = await res.json();
  54  |   return Array.isArray(data) ? data : (data.documents ?? []);
  55  | }
  56  | 
  57  | // ---------------------------------------------------------------------------
  58  | // Test Suite
  59  | // ---------------------------------------------------------------------------
  60  | 
  61  | test.describe('RMS — PG Webhook Integration', () => {
  62  | 
  63  |   // =========================================================================
  64  |   // TC1 + TC2 run serially: TC2 requires TC1's idempotencyKey to be in Redis
  65  |   // =========================================================================
  66  |   test.describe.serial('TC1 + TC2 — Happy Path and Duplicate Dedup', () => {
  67  | 
  68  |     // -----------------------------------------------------------------------
  69  |     // TC1 — Successful Transaction (Happy Path)
  70  |     // -----------------------------------------------------------------------
  71  |     test(
  72  |       'TC1 — Successful Transaction — Happy Path',
  73  |       async ({ request, logger }) => {
  74  |         await allure.description(
  75  |           'Verifies the complete end-to-end flow for a successful MPESA transaction. ' +
  76  |           'Expects HTTP 200 with status "received", a PostgreSQL row with the correct ' +
  77  |           'recon_key, commission, and wallet_provider, and a MongoDB document in raw_pg_events.',
  78  |         );
  79  |         await allure.label('feature',  'PG Webhook');
  80  |         await allure.label('story',    'Happy Path');
  81  |         await allure.label('severity', 'blocker');
  82  |         await allure.label('priority', 'P0');
  83  |         await allure.label('testId',   'TC1');
  84  |         await allure.label('type',     'Positive');
  85  | 
  86  |         const now    = new Date();
  87  |         const expiry = new Date(now.getTime() + 24 * 60 * 60 * 1000); // +24 h
  88  | 
  89  |         const basePayload: Record<string, unknown> = {
  90  |           response_signature: '',
  91  |           eCitizen_platform_data: {
  92  |             paymentGatewayId:         'PG_001',
  93  |             apiVersion:               'v1',
  94  |             idempotencyKey:           'd4e5f6a7-b8c9-0123-cdef-4567890123ef',
  95  |             requestTimestamp:         now.toISOString(),
  96  |             requestExpiryTimestamp:   expiry.toISOString(),
  97  |             payerId:                  'KE-NID-56789012',
  98  |             payerName:                'Faith Njeri',
  99  |             payerEmail:               'faith.njeri@gmail.com',
  100 |             payerPhone:               '+254734567890',
  101 |             payerIdType:              'NATIONAL_ID',
  102 |             payerIdNumber:            '56789012',
  103 |             payerNationality:         'KE',
  104 |             payerType:                'CITIZEN',
  105 |             mcdaCode:                 'MRCH-0234',
  106 |             mcdaName:                 'National Transport and Safety Authority',
  107 |             serviceCode:              'NTSA-003',
  108 |             serviceName:              'Vehicle Inspection',
  109 |             serviceCategory:          'Transport',
  110 |             clientReferenceNumber:    '1020260510',
  111 |             correlationUTR:           'IJKL9012',
  112 |             serviceAmount:            5200.00,
  113 |             amountDue:                5200.00,
  114 |             paymentType:              'FULL',
  115 |             invoiceId:                'IJKL9012',
  116 |             sessionId:                'e5f6a7b8-c9d0-1234-def5-6789012345ab',
  117 |           },
  118 |           gateway_response: {
  119 |             paymentReference:         'TXN-EF78GH90',
  120 |             transactionStatus:        'SUCCESS',
  121 |             transactionConfirmationId:'CONF-GH90IJ12',
  122 |             transactionTimestamp:     now.toISOString(),
  123 |             failureId:                null,
  124 |             failureCode:              null,
  125 |             failureReason:            null,
  126 |             paymentMethod:            'MPESA',
  127 |             paymentMethodCategory:    'MOBILE_MONEY',
  128 |             paymentMethodDetails: {
  129 |               wallet: {
  130 |                 mobileNumber:         '+254734567890',
  131 |                 walletProvider:       'MPESA',
  132 |                 walletUserName:       'Faith Njeri',
```