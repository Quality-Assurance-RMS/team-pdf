# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-monthly-revenue.spec.ts >> Smoke â€” Dashboard Monthly Revenue >> TC-DMREV-008 â€” BUG-MREV-05: conversion_ratio_pct must be null when no data, not 0.0
- Location: tests/API/smoke/smoke-dashboard-monthly-revenue.spec.ts:571:7

# Error details

```
Error: [BUG-MREV-05] conversion_ratio_pct must be null (not 0.0) when total_payments=0 â€” there were no transactions, not 0% successful ones

expect(received).toBeNull()

Received: undefined
```

```
Error: conversion_ratio_pct must be a number

expect(received).toBe(expected) // Object.is equality

Expected: "number"
Received: "undefined"
```

# Test source

```ts
  513 |     });
  514 | 
  515 |     await logger.step('Step 2 â€” BUG-MREV-04: Confirm cross-endpoint data inconsistency', async () => {
  516 |       const s = res!.body.data.summary as Summary;
  517 | 
  518 |       const monthlyGrossKes = s.total_gross_kes;
  519 |       const monthlyGrossUsd = s.total_gross_usd;
  520 |       const revTrendKes     = REVENUE_TREND_TOTAL_KES; // 18,633,694
  521 | 
  522 |       const sumKesAndUsd = monthlyGrossKes + monthlyGrossUsd;
  523 |       const diffFromTrend = Math.abs(revTrendKes - monthlyGrossKes);
  524 |       const diffFromUsd   = Math.abs(diffFromTrend - monthlyGrossUsd);
  525 |       const isCurrencyMix = diffFromUsd < 1; // within 1 unit (floating point tolerance)
  526 | 
  527 |       allure.parameter('/monthly-revenue total_gross_kes', monthlyGrossKes.toLocaleString());
  528 |       allure.parameter('/monthly-revenue total_gross_usd', monthlyGrossUsd.toLocaleString());
  529 |       allure.parameter('/revenue-trend KES total (reported)', revTrendKes.toLocaleString());
  530 |       allure.parameter('KES + USD (if mixed 1:1)', sumKesAndUsd.toFixed(2));
  531 |       allure.parameter('Trend minus Monthly KES diff', diffFromTrend.toFixed(2));
  532 |       allure.parameter('Diff â‰ˆ gross_usd amount?', String(isCurrencyMix));
  533 |       allure.parameter('CURRENCY MIXING CONFIRMED', String(isCurrencyMix));
  534 | 
  535 |       if (isCurrencyMix) {
  536 |         flagIssue('TC-DMREV-007', 'BUG-MREV-04',
  537 |           'CRITICAL CROSS-ENDPOINT BUG: The /revenue-trend endpoint reports its KES total as ' +
  538 |           `${revTrendKes.toLocaleString()} KES, while /monthly-revenue reports total_gross_kes=${monthlyGrossKes.toLocaleString()} KES. ` +
  539 |           `The difference is ${diffFromTrend.toFixed(2)}, which equals total_gross_usd (${monthlyGrossUsd.toLocaleString()} USD) to within 1 unit. ` +
  540 |           'This proves /revenue-trend computes its KES total by adding KES + USD amounts treating 1 USD = 1 KES. ' +
  541 |           `The correct KES total is ${monthlyGrossKes.toLocaleString()} KES. ` +
  542 |           'Reported figure is inflated by the full USD amount (222,987 KES worth of USD is counted twice at wrong rate).',
  543 |           {
  544 |             revenue_trend_reported_kes: revTrendKes,
  545 |             monthly_revenue_gross_kes:  monthlyGrossKes,
  546 |             monthly_revenue_gross_usd:  monthlyGrossUsd,
  547 |             kes_plus_usd_at_1to1:       sumKesAndUsd,
  548 |             difference_from_trend:      diffFromTrend,
  549 |             currency_mixing_confirmed:  isCurrencyMix,
  550 |             impact: 'Revenue figures shown in /revenue-trend chart are overstated by ~KES 222,987 (the USD total)',
  551 |             root_cause: 'Likely: SUM(gross_amount) across all currencies without currency conversion â€” USD amounts added to KES as-is',
  552 |           },
  553 |         );
  554 |       }
  555 | 
  556 |       // The two endpoints should agree on the KES total when data is current
  557 |       // We use expect.soft because the fix is in /revenue-trend, not here
  558 |       logger.info('Asserting: BUG-MREV-04: Confirm cross-endpoint data inconsis');
  559 |       expect.soft(isCurrencyMix,
  560 |         `[BUG-MREV-04] /revenue-trend KES total (${revTrendKes.toLocaleString()}) = monthly_gross_kes(${monthlyGrossKes.toLocaleString()}) + monthly_gross_usd(${monthlyGrossUsd.toLocaleString()}) â€” USD treated as KES`,
  561 |       ).toBe(false);
  562 | 
  563 |       // Both totals must be positive when there is data
  564 |       expect(monthlyGrossKes, 'total_gross_kes must be > 0 when year has transactions').toBeGreaterThan(0);
  565 |       expect(monthlyGrossUsd, 'total_gross_usd must be > 0 when year has transactions').toBeGreaterThan(0);
  566 |       logger.pass('All assertions passed');
  567 |     });
  568 |   });
  569 | 
  570 |   // â”€â”€ TC-DMREV-008 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  571 |   test('TC-DMREV-008 â€” BUG-MREV-05: conversion_ratio_pct must be null when no data, not 0.0', async ({ logger }) => {
  572 |     smokeLabels('TC-DMREV-008', 'BUG-MREV-05 â€” conversion_ratio_pct=0.0 for Zero-Data Years', 'minor');
  573 | 
  574 |     await logger.step('Step 1 â€” Fetch year=2025 (known zero-data year)', async () => {
  575 |       logger.info(`GET ${ENDPOINT}`);
  576 |       const res = await get('year=2025');
  577 |       logger.pass('HTTP ' + res.status + ' received');
  578 |       expect(res.status).toBe(200);
  579 | 
  580 |       const s = res.body.data.summary as Summary;
  581 |       allure.parameter('year=2025 total_payments', String(s.total_payments));
  582 |       allure.parameter('year=2025 conversion_ratio_pct', String(s.conversion_ratio_pct));
  583 |       attachResponse('TC-DMREV-008', res, { bug: 'BUG-MREV-05', query: 'year=2025' });
  584 | 
  585 |       if (s.total_payments === 0 && s.conversion_ratio_pct === 0) {
  586 |         flagIssue('TC-DMREV-008', 'BUG-MREV-05',
  587 |           'conversion_ratio_pct=0.0 when total_payments=0 â€” this is division by zero (0 successful / 0 total). ' +
  588 |           'A ratio of 0.0% is misleading because it implies 0% of transactions succeeded, not that there were no transactions. ' +
  589 |           'When there is no data, conversion_ratio_pct should be null (or omitted) to signal "not applicable".',
  590 |           {
  591 |             year: 2025,
  592 |             total_payments: s.total_payments,
  593 |             conversion_ratio_pct: s.conversion_ratio_pct,
  594 |             expected: null,
  595 |             issue: 'Division by zero returns 0.0 instead of null â€” "0% conversion" misrepresents "no data"',
  596 |           },
  597 |         );
  598 |       }
  599 | 
  600 |       expect.soft(s.conversion_ratio_pct,
  601 |         '[BUG-MREV-05] conversion_ratio_pct must be null (not 0.0) when total_payments=0 â€” there were no transactions, not 0% successful ones',
  602 |       ).toBeNull();
  603 |       logger.pass('All assertions passed');
  604 |     });
  605 | 
  606 |     await logger.step('Step 2 â€” Verify current year has a valid conversion ratio > 0', async () => {
  607 |       logger.info(`GET ${ENDPOINT}`);
  608 |       const res = await get();
  609 |       logger.pass('HTTP ' + res.status + ' received');
  610 |       logger.info('Asserting: response structure and values');
  611 |       expect(res.status).toBe(200);
  612 |       const s = res.body.data.summary as Summary;
> 613 |       expect(typeof s.conversion_ratio_pct, 'conversion_ratio_pct must be a number').toBe('number');
      |                                                                                      ^ Error: conversion_ratio_pct must be a number
  614 |       expect(s.conversion_ratio_pct, 'conversion_ratio_pct must be > 0 when transactions exist').toBeGreaterThan(0);
  615 |       expect(s.conversion_ratio_pct, 'conversion_ratio_pct must be <= 100').toBeLessThanOrEqual(100);
  616 |       allure.parameter('Current year conversion_ratio_pct', String(s.conversion_ratio_pct));
  617 |       logger.pass('All assertions passed');
  618 |     });
  619 |   });
  620 | 
  621 |   // â”€â”€ TC-DMREV-009 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  622 |   test('TC-DMREV-009 â€” BUG-MREV-06: Wrong HTTP methods must return 405, not 500', async ({ logger }) => {
  623 |     smokeLabels('TC-DMREV-009', 'BUG-MREV-06 â€” HTTP Method Enforcement', 'normal');
  624 | 
  625 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  626 | 
  627 |     for (const method of wrongMethods) {
  628 |       await logger.step(`${method.toUpperCase()} must return 405`, async () => {
  629 |         logger.info(`GET ${ENDPOINT}`);
  630 |         const res = await getWithMethod(method);
  631 |         logger.pass('HTTP ' + res.status + ' received');
  632 | 
  633 |         console.log(`[TC-DMREV-009] ${method.toUpperCase()} â†’ ${res.status}  body: ${JSON.stringify(res.body)}`);
  634 |         allure.parameter(`${method.toUpperCase()} status`, String(res.status));
  635 |         allure.parameter(`${method.toUpperCase()} error`,  String(res.body?.error ?? 'N/A'));
  636 | 
  637 |         if (res.status === 500) {
  638 |           flagIssue('TC-DMREV-009', 'BUG-MREV-06',
  639 |             `${method.toUpperCase()} ${ENDPOINT} returns 500 Internal Server Error â€” must return 405 Method Not Allowed. ` +
  640 |             'This bug is present on every dashboard endpoint tested (status-currency, summary, today-payments, revenue-trend, monthly-revenue).',
  641 |             { method: method.toUpperCase(), actual_status: res.status, expected_status: 405, body: res.body },
  642 |           );
  643 |         }
  644 | 
  645 |         logger.info('Asserting: response structure and values');
  646 |         expect.soft(res.status,
  647 |           `[BUG-MREV-06] ${method.toUpperCase()} must return 405 Method Not Allowed, got ${res.status}`,
  648 |         ).toBe(405);
  649 | 
  650 |         if (res.status === 405) {
  651 |           expect(res.headers['allow'], '405 response must include Allow header listing GET').toContain('GET');
  652 |         }
  653 |         logger.pass('All assertions passed');
  654 |       });
  655 |     }
  656 |   });
  657 | 
  658 |   // â”€â”€ TC-DMREV-010 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  659 |   test('TC-DMREV-010 â€” BUG-MREV-07: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  660 |     smokeLabels('TC-DMREV-010', 'BUG-MREV-07 â€” Framework Info Leak on 406', 'normal');
  661 | 
  662 |     await logger.step('Step 1 â€” Send GET with Accept: text/html', async () => {
  663 |       logger.info(`GET ${ENDPOINT}`);
  664 |       const res = await supertest(BASE_URL).get(ENDPOINT)
  665 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  666 |         .set('Accept', 'text/html');
  667 |       logger.pass('HTTP ' + res.status + ' received');
  668 | 
  669 |       console.log(`[TC-DMREV-010] Accept: text/html â†’ ${res.status}`);
  670 |       console.log(`  Excerpt: ${String(res.text).substring(0, 300)}`);
  671 |       allure.parameter('text/html status',       String(res.status));
  672 |       allure.parameter('Response Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  673 | 
  674 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
  675 | 
  676 |       const hasWhitelabel = String(res.text ?? '').includes('Whitelabel Error Page');
  677 |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  678 | 
  679 |       if (hasWhitelabel) {
  680 |         flagIssue('TC-DMREV-010', 'BUG-MREV-07',
  681 |           'GET /monthly-revenue with Accept: text/html returns Spring Boot Whitelabel Error Page â€” ' +
  682 |           'exposes Spring Boot framework, server timezone (EAT), and internal path',
  683 |           {
  684 |             status:       res.status,
  685 |             info_leaked:  ['Spring Boot framework', 'Internal path: /api/v1/dashboard/monthly-revenue', 'Server timezone (EAT)'],
  686 |             body_excerpt: String(res.text).substring(0, 400),
  687 |           },
  688 |         );
  689 |       }
  690 | 
  691 |       expect.soft(hasWhitelabel, '[BUG-MREV-07] 406 error must NOT expose Spring Boot Whitelabel Error Page').toBe(false);
  692 |       expect.soft(res.headers['content-type'], '[BUG-MREV-07] 406 error body should be application/json').toContain('application/json');
  693 | 
  694 |       allure.attachment('[TC-DMREV-010] 406 Response Body', String(res.text ?? ''), 'text/plain');
  695 |       logger.pass('All assertions passed');
  696 |     });
  697 |   });
  698 | 
  699 |   // â”€â”€ TC-DMREV-011 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  700 |   test('TC-DMREV-011 â€” BUG-MREV-08: Cache-Control and security headers must be present', async ({ logger }) => {
  701 |     smokeLabels('TC-DMREV-011', 'BUG-MREV-08 â€” Missing Cache-Control & Security Headers', 'normal');
  702 |     let res: supertest.Response;
  703 | 
  704 |     await logger.step('Step 1 â€” Fetch response and inspect headers', async () => {
  705 |       logger.info(`GET ${ENDPOINT}`);
  706 |       res = await get();
  707 |       logger.pass('HTTP ' + res.status + ' received');
  708 |       console.log(`[TC-DMREV-011] Response headers:\n${JSON.stringify(res.headers, null, 2)}`);
  709 |       attachResponse('TC-DMREV-011', res, { bug: 'BUG-MREV-08' });
  710 |     });
  711 | 
  712 |     await logger.step('Step 2 â€” BUG-MREV-08: Cache-Control must prohibit caching of financial data', async () => {
  713 |       const cc = res!.headers['cache-control'] as string | undefined;
```