# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/invoice-webhook.spec.ts >> NUIS Invoice API — rms-invoice-webhook-listener (Staging) >> [Validation] TC10 — Missing "number" field returns 400
- Location: tests/API/invoice-webhook.spec.ts:404:7

# Error details

```
Error: missing "number" field should return 400, not 401

expect(received).toBe(expected) // Object.is equality

Expected: 400
Received: 401
```

# Test source

```ts
  333 | 
  334 |     const rawBody = JSON.stringify(invoice);
  335 | 
  336 |     const { timestamp } = await logger.step('Step 2 — Sign the payload (signature will be withheld)', async () => {
  337 |       return signInvoicePayload(rawBody, HMAC_SECRET);
  338 |     });
  339 | 
  340 |     await logger.step('Step 3 — POST with only the timestamp header and confirm 401 MISSING_SIGNATURE_HEADER', async () => {
  341 |       const res = await postInvoice(rawBody, { 'X-Webhook-Timestamp': timestamp });
  342 |       expect401WithCode(res, 'MISSING_SIGNATURE_HEADER');
  343 |       logger.pass('Missing signature header correctly rejected with MISSING_SIGNATURE_HEADER');
  344 |     });
  345 |   });
  346 | 
  347 |   test('[Security] TC8 — Missing X-Webhook-Timestamp header returns 401 MISSING_TIMESTAMP_HEADER', async ({ logger }) => {
  348 |     await allure.description(
  349 |       'Sends a correctly-signed request but omits the X-Webhook-Timestamp header entirely.\n\n' +
  350 |       '**Expected:** HTTP 401 with body {"error":{"code":"MISSING_TIMESTAMP_HEADER", ...}}.',
  351 |     );
  352 |     await allure.label('feature', 'NUIS Invoice API');
  353 |     await allure.label('story', 'Security');
  354 |     await allure.label('severity', 'critical');
  355 | 
  356 |     const invoice = await logger.step('Step 1 — Build a valid invoice payload', async () => {
  357 |       return buildPaidInvoice();
  358 |     });
  359 | 
  360 |     const rawBody = JSON.stringify(invoice);
  361 | 
  362 |     const { signatureHeader } = await logger.step('Step 2 — Sign the payload (timestamp header will be withheld)', async () => {
  363 |       return signInvoicePayload(rawBody, HMAC_SECRET);
  364 |     });
  365 | 
  366 |     await logger.step('Step 3 — POST with only the signature header and confirm 401 MISSING_TIMESTAMP_HEADER', async () => {
  367 |       const res = await postInvoice(rawBody, { 'X-Webhook-Signature': signatureHeader });
  368 |       expect401WithCode(res, 'MISSING_TIMESTAMP_HEADER');
  369 |       logger.pass('Missing timestamp header correctly rejected with MISSING_TIMESTAMP_HEADER');
  370 |     });
  371 |   });
  372 | 
  373 |   test('[Security] TC9 — Non-integer timestamp header returns 401 INVALID_TIMESTAMP_HEADER', async ({ logger }) => {
  374 |     await allure.description(
  375 |       'Sends a request where the X-Webhook-Timestamp header is present but not a valid ' +
  376 |       'integer (e.g. a malformed client sending garbage or a date string instead of ' +
  377 |       'unix seconds).\n\n' +
  378 |       '**Expected:** HTTP 401 with body {"error":{"code":"INVALID_TIMESTAMP_HEADER", ...}}.',
  379 |     );
  380 |     await allure.label('feature', 'NUIS Invoice API');
  381 |     await allure.label('story', 'Security');
  382 |     await allure.label('severity', 'normal');
  383 | 
  384 |     const invoice = await logger.step('Step 1 — Build a valid invoice payload', async () => {
  385 |       return buildPaidInvoice();
  386 |     });
  387 | 
  388 |     const rawBody = JSON.stringify(invoice);
  389 | 
  390 |     const { signatureHeader } = await logger.step('Step 2 — Sign the payload normally (timestamp header sent separately, will be corrupted)', async () => {
  391 |       return signInvoicePayload(rawBody, HMAC_SECRET);
  392 |     });
  393 | 
  394 |     await logger.step('Step 3 — POST with a non-integer timestamp header and confirm 401 INVALID_TIMESTAMP_HEADER', async () => {
  395 |       const res = await postInvoice(rawBody, {
  396 |         'X-Webhook-Timestamp': 'not-a-timestamp',
  397 |         'X-Webhook-Signature': signatureHeader,
  398 |       });
  399 |       expect401WithCode(res, 'INVALID_TIMESTAMP_HEADER');
  400 |       logger.pass('Non-integer timestamp header correctly rejected with INVALID_TIMESTAMP_HEADER');
  401 |     });
  402 |   });
  403 | 
  404 |   test('[Validation] TC10 — Missing "number" field returns 400', async ({ logger }) => {
  405 |     await allure.description(
  406 |       'Per the documented response codes table: "400 Bad Request — Malformed JSON or ' +
  407 |       'missing Number". Sends a validly-signed payload with the required "number" field ' +
  408 |       'deleted, and confirms the API rejects it at the validation layer rather than the ' +
  409 |       'signature layer.\n\n' +
  410 |       '**Expected:** HTTP 400 (not 401 — this must fail JSON/schema validation, not auth).',
  411 |     );
  412 |     await allure.label('feature', 'NUIS Invoice API');
  413 |     await allure.label('story', 'Validation');
  414 |     await allure.label('severity', 'normal');
  415 | 
  416 |     const invoice = await logger.step('Step 1 — Build a valid invoice payload, then remove the "number" field', async () => {
  417 |       const inv: Record<string, unknown> = { ...buildPaidInvoice() };
  418 |       delete inv.number;
  419 |       return inv;
  420 |     });
  421 | 
  422 |     const rawBody = JSON.stringify(invoice);
  423 | 
  424 |     const { timestamp, signatureHeader } = await logger.step('Step 2 — Sign the malformed payload (signature itself will still be valid)', async () => {
  425 |       return signInvoicePayload(rawBody, HMAC_SECRET);
  426 |     });
  427 | 
  428 |     await logger.step('Step 3 — POST the payload missing "number" and confirm 400', async () => {
  429 |       const res = await postInvoice(rawBody, {
  430 |         'X-Webhook-Timestamp': timestamp,
  431 |         'X-Webhook-Signature': signatureHeader,
  432 |       });
> 433 |       expect(res.status, 'missing "number" field should return 400, not 401').toBe(400);
      |                                                                               ^ Error: missing "number" field should return 400, not 401
  434 |       logger.pass(`Missing "number" field correctly rejected with HTTP ${res.status}`);
  435 |     });
  436 |   });
  437 | 
  438 | });
  439 | 
```