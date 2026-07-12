# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/ingest.spec.ts >> Module 1 — Payment Gateway Integration (Ingress Layer) >> 2.1 HMAC and Edge Validation >> TC-INGEST-001 — Valid PesaFlow webhook with correct HMAC and fresh timestamp
- Location: tests/API/ingest.spec.ts:69:9

# Error details

```
Error: apiRequestContext.get: getaddrinfo ENOTFOUND rms-sit-edge
Call log:
  - → GET https://rms-sit-edge/metrics
    - user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.7778.96 Safari/537.36
    - accept: */*
    - accept-encoding: gzip,deflate,br

```

# Test source

```ts
  1   | import { test, expect } from './fixtures';
  2   | import type { APIRequestContext } from '@playwright/test';
  3   | import { allure } from 'allure-playwright';
  4   | import * as crypto from 'crypto';
  5   | import { randomUUID } from 'crypto';
  6   | 
  7   | // ---------------------------------------------------------------------------
  8   | // Environment / Constants
  9   | // ---------------------------------------------------------------------------
  10  | 
  11  | const WEBHOOK_BASE_URL  = process.env.WEBHOOK_BASE_URL  ?? 'https://rms-sit-edge';
  12  | const INTERNAL_API_URL  = process.env.INTERNAL_API_URL  ?? 'https://rms-sit-edge/internal';
  13  | const PESAFLOW_PSK      = process.env.PESAFLOW_PSK      ?? 'pesaflow-test-001';
  14  | const PESAFLOW_SERVICE_CODE = process.env.PESAFLOW_SERVICE_CODE ?? 'STANDARD';
  15  | const INVOICE_ID        = 'INV-TST-001-001';
  16  | const DOWNSTREAM_WAIT_MS = 10_000;
  17  | 
  18  | // ---------------------------------------------------------------------------
  19  | // Helpers
  20  | // ---------------------------------------------------------------------------
  21  | 
  22  | function buildPesaflowPayload(pspTransactionId: string, invoiceId: string) {
  23  |   return {
  24  |     psp_transaction_id: pspTransactionId,
  25  |     invoice_id:         invoiceId,
  26  |     amount:             1000,
  27  |     currency:           'KES',
  28  |     status:             'SUCCESS',
  29  |     psp:                'PESAFLOW',
  30  |     service_code:       PESAFLOW_SERVICE_CODE,
  31  |   };
  32  | }
  33  | 
  34  | /** HMAC-SHA256(body || timestamp, PSK) → lowercase hex */
  35  | function computeHmacSignature(body: string, timestamp: string, psk: string): string {
  36  |   return crypto.createHmac('sha256', psk).update(body + timestamp).digest('hex');
  37  | }
  38  | 
  39  | /** Query the Prometheus /metrics text endpoint and return current counter value. */
  40  | async function readPromCounter(
  41  |   request: APIRequestContext,
  42  |   metricName: string,
  43  |   labels: Record<string, string>,
  44  | ): Promise<number> {
> 45  |   const res  = await request.get(`${WEBHOOK_BASE_URL}/metrics`);
      |                              ^ Error: apiRequestContext.get: getaddrinfo ENOTFOUND rms-sit-edge
  46  |   expect(res.status(), 'Metrics endpoint should be reachable').toBe(200);
  47  |   const text = await res.text();
  48  | 
  49  |   const labelStr = Object.entries(labels)
  50  |     .map(([k, v]) => `${k}="${v}"`)
  51  |     .join(',');
  52  | 
  53  |   const line = text.split('\n').find(
  54  |     l => l.startsWith(metricName) && l.includes(labelStr),
  55  |   );
  56  | 
  57  |   if (!line) return 0;
  58  |   const parts = line.trim().split(/\s+/);
  59  |   return parseFloat(parts[parts.length - 1]) || 0;
  60  | }
  61  | 
  62  | // ---------------------------------------------------------------------------
  63  | // Test Suite
  64  | // ---------------------------------------------------------------------------
  65  | 
  66  | test.describe('Module 1 — Payment Gateway Integration (Ingress Layer)', () => {
  67  |   test.describe('2.1 HMAC and Edge Validation', () => {
  68  | 
  69  |     test(
  70  |       'TC-INGEST-001 — Valid PesaFlow webhook with correct HMAC and fresh timestamp',
  71  |       async ({ request, logger }) => {
  72  | 
  73  |         // ── Allure metadata ──────────────────────────────────────────────
  74  |         await allure.description(
  75  |           'Verifies that a valid PesaFlow webhook carrying a correct HMAC-SHA256 ' +
  76  |           'signature and a fresh ISO-8601 timestamp is accepted (HTTP 202) and that ' +
  77  |           'all downstream effects — Kafka message, ledger row, GridFS document, ' +
  78  |           'distributed trace, and Prometheus metric — are recorded correctly.',
  79  |         );
  80  |         await allure.label('feature',   'Payment Gateway Integration');
  81  |         await allure.label('story',     'HMAC and Edge Validation');
  82  |         await allure.label('severity',  'blocker');
  83  |         await allure.label('priority',  'P0');
  84  |         await allure.label('testId',    'TC-INGEST-001');
  85  |         await allure.label('type',      'Positive');
  86  |         await allure.label('trace',     'FR-1.1, FR-1.2, FR-1.4');
  87  |         await allure.label('risk',      'R-01, R-08, R-11');
  88  | 
  89  |         // ── Working variables ─────────────────────────────────────────────
  90  |         let pspTransactionId: string;
  91  |         let correlationId:    string;
  92  |         let timestamp:        string;
  93  |         let signature:        string;
  94  |         let bodyStr:          string;
  95  | 
  96  |         // ── Step 1: Generate payload ──────────────────────────────────────
  97  |         await logger.step(
  98  |           'Step 1: Generate payload from PesaFlow happy-path template',
  99  |           async () => {
  100 |             logger.info('Generating PesaFlow payload with randomized psp_transaction_id');
  101 |             pspTransactionId = randomUUID();
  102 |             const payload    = buildPesaflowPayload(pspTransactionId, INVOICE_ID);
  103 |             bodyStr          = JSON.stringify(payload);
  104 | 
  105 |             allure.parameter('psp_transaction_id', pspTransactionId);
  106 |             allure.parameter('invoice_id',         INVOICE_ID);
  107 |             allure.parameter('Payload',            bodyStr);
  108 |             logger.pass('Payload generated successfully');
  109 |           },
  110 |         );
  111 | 
  112 |         // ── Step 2: Compute HMAC-SHA256 signature ─────────────────────────
  113 |         await logger.step(
  114 |           'Step 2: Compute X-PSP-Signature = HMAC-SHA256(body || timestamp, PSK) and hex-encode',
  115 |           async () => {
  116 |             logger.info('Computing HMAC-SHA256 signature from body and current timestamp');
  117 |             timestamp = new Date().toISOString();          // UTC ISO 8601
  118 |             signature = computeHmacSignature(bodyStr, timestamp, PESAFLOW_PSK);
  119 | 
  120 |             allure.parameter('X-Request-Timestamp',   timestamp);
  121 |             allure.parameter('X-PSP-Signature (hex)', signature);
  122 |             logger.pass('HMAC-SHA256 signature computed successfully');
  123 |           },
  124 |         );
  125 | 
  126 |         // ── Step 3: Build correlation ID ──────────────────────────────────
  127 |         await logger.step(
  128 |           'Step 3: Set X-Correlation-ID = fresh UUIDv4',
  129 |           async () => {
  130 |             logger.info('Generating fresh UUIDv4 for X-Correlation-ID header');
  131 |             correlationId = randomUUID();
  132 |             allure.parameter('X-Correlation-ID', correlationId);
  133 |             logger.pass('Correlation ID generated successfully');
  134 |           },
  135 |         );
  136 | 
  137 |         // ── Step 4: Read baseline metric counter ──────────────────────────
  138 |         let metricBefore: number;
  139 |         await logger.step(
  140 |           'Step 4 (pre): Read baseline rms_ingestion_requests_total counter',
  141 |           async () => {
  142 |             logger.info('GET /metrics — reading baseline Prometheus counter before request');
  143 |             metricBefore = await readPromCounter(request, 'rms_ingestion_requests_total', {
  144 |               status_class: '2xx',
  145 |               psp:          'PESAFLOW',
```