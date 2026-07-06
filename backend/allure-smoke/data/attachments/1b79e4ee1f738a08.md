# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-monthly-revenue.spec.ts >> Smoke â€” Dashboard Monthly Revenue >> TC-DMREV-011 â€” BUG-MREV-08: Cache-Control and security headers must be present
- Location: tests/API/smoke/smoke-dashboard-monthly-revenue.spec.ts:700:7

# Error details

```
Error: [BUG-MREV-08] Cache-Control must be present

expect(received).toBeDefined()

Received: undefined
```

```
Error: X-Content-Type-Options: nosniff must be present

expect(received).toBe(expected) // Object.is equality

Expected: "nosniff"
Received: undefined
```

```
Error: X-Frame-Options: DENY must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
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
  714 |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  715 | 
  716 |       if (!cc) {
  717 |         flagIssue('TC-DMREV-011', 'BUG-MREV-08',
  718 |           'Cache-Control header is absent on /monthly-revenue â€” revenue figures update as transactions are processed. ' +
  719 |           'Without Cache-Control: no-store, proxy servers and browser caches may serve stale financial data.',
  720 |           { expected: 'Cache-Control: no-store, no-cache', actual: 'MISSING' },
  721 |         );
  722 |       }
  723 |       logger.info('Asserting: BUG-MREV-08: Cache-Control must prohibit caching');
  724 |       expect.soft(cc, '[BUG-MREV-08] Cache-Control must be present').toBeDefined();
  725 |       if (cc) {
  726 |         const preventsCache = cc.includes('no-store') || cc.includes('no-cache') || cc.includes('private');
  727 |         expect.soft(preventsCache, `[BUG-MREV-08] Cache-Control="${cc}" must prevent caching`).toBe(true);
  728 |       }
  729 |       logger.pass('All assertions passed');
  730 |     });
  731 | 
  732 |     await logger.step('Step 3 â€” X-Content-Type-Options must be nosniff', async () => {
  733 |       const h = res!.headers['x-content-type-options'] as string | undefined;
  734 |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  735 |       if (!h) {
  736 |         flagIssue('TC-DMREV-011', 'BUG-MREV-08b', 'X-Content-Type-Options: nosniff is missing', { expected: 'nosniff', actual: 'MISSING' });
  737 |       }
  738 |       logger.info('Asserting: response structure and values');
  739 |       expect.soft(h, 'X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  740 |       logger.pass('All assertions passed');
  741 |     });
  742 | 
  743 |     await logger.step('Step 4 â€” X-Frame-Options must be present', async () => {
  744 |       const h = res!.headers['x-frame-options'] as string | undefined;
  745 |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  746 |       if (!h) {
  747 |         flagIssue('TC-DMREV-011', 'BUG-MREV-08c', 'X-Frame-Options is missing', { expected: 'DENY', actual: 'MISSING' });
  748 |       }
  749 |       logger.info('Asserting: response structure and values');
> 750 |       expect.soft(h, 'X-Frame-Options: DENY must be present').toBeDefined();
      |                                                               ^ Error: X-Frame-Options: DENY must be present
  751 |       logger.pass('All assertions passed');
  752 |     });
  753 | 
  754 |     await logger.step('Step 5 â€” Full security header audit', async () => {
  755 |       const audit = {
  756 |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  757 |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  758 |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  759 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  760 |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  761 |       };
  762 |       allure.attachment('[TC-DMREV-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  763 |     });
  764 |   });
  765 | 
  766 |   // â”€â”€ TC-DMREV-012 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  767 |   test('TC-DMREV-012 â€” BUG-MREV-09: CORS Access-Control-Allow-Origin: * is overly permissive', async ({ logger }) => {
  768 |     smokeLabels('TC-DMREV-012', 'BUG-MREV-09 â€” Overly Permissive CORS', 'normal');
  769 |     let res: supertest.Response;
  770 | 
  771 |     await logger.step('Step 1 â€” Fetch response and check CORS headers', async () => {
  772 |       logger.info(`GET ${ENDPOINT}`);
  773 |       res = await get();
  774 |       logger.pass('HTTP ' + res.status + ' received');
  775 |       const acao = res.headers['access-control-allow-origin'] as string | undefined;
  776 |       const acam = res.headers['access-control-allow-methods'] as string | undefined;
  777 |       allure.parameter('Access-Control-Allow-Origin',  acao ?? 'MISSING');
  778 |       allure.parameter('Access-Control-Allow-Methods', acam ?? 'MISSING');
  779 |       attachResponse('TC-DMREV-012', res, { bug: 'BUG-MREV-09' });
  780 |     });
  781 | 
  782 |     await logger.step('Step 2 â€” ACAO must not be wildcard on a financial endpoint', async () => {
  783 |       const acao = res!.headers['access-control-allow-origin'] as string | undefined;
  784 | 
  785 |       if (acao === '*') {
  786 |         flagIssue('TC-DMREV-012', 'BUG-MREV-09a',
  787 |           'Access-Control-Allow-Origin: * allows any website to initiate authenticated requests ' +
  788 |           'to /monthly-revenue and read back financial data. Must be restricted to the RMS web app origin.',
  789 |           { actual: 'ACAO: *', risk: 'Cross-site data read of financial revenue breakdowns', expected: 'specific trusted domain' },
  790 |         );
  791 |       }
  792 |       logger.info('Asserting: response structure and values');
  793 |       expect.soft(acao, '[BUG-MREV-09a] ACAO must not be wildcard "*"').not.toBe('*');
  794 |       logger.pass('All assertions passed');
  795 |     });
  796 | 
  797 |     await logger.step('Step 3 â€” Destructive methods must not be CORS-allowed on GET-only endpoint', async () => {
  798 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  799 |       const bad  = ['DELETE','POST','PUT','PATCH'].filter(m => acam?.toUpperCase().includes(m));
  800 | 
  801 |       if (bad.length) {
  802 |         flagIssue('TC-DMREV-012', 'BUG-MREV-09b',
  803 |           `CORS allows ${bad.join(', ')} from any origin on a read-only endpoint`,
  804 |           { 'access-control-allow-methods': acam },
  805 |         );
  806 |       }
  807 |       logger.info('Asserting: response structure and values');
  808 |       expect.soft(bad.length, `[BUG-MREV-09b] CORS must not allow destructive methods: ${bad.join(', ')}`).toBe(0);
  809 |       logger.pass('All assertions passed');
  810 |     });
  811 |   });
  812 | 
  813 |   // â”€â”€ TC-DMREV-013 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  814 |   test('TC-DMREV-013 â€” Math: per-month gross âˆ’ net = commission for every month', async ({ logger }) => {
  815 |     smokeLabels('TC-DMREV-013', 'Per-Month Math: gross âˆ’ net = commission', 'critical');
  816 |     let res: supertest.Response;
  817 | 
  818 |     await logger.step('Step 1 â€” Fetch monthly-revenue', async () => {
  819 |       logger.info(`GET ${ENDPOINT}`);
  820 |       res = await get();
  821 |       logger.pass('HTTP ' + res.status + ' received');
  822 |       expect(res.status).toBe(200);
  823 |       attachResponse('TC-DMREV-013', res);
  824 |       logger.pass('All assertions passed');
  825 |     });
  826 | 
  827 |     await logger.step('Step 2 â€” Validate KES: gross_kes âˆ’ net_kes = commission_kes for each month', async () => {
  828 |       const months = (res!.body.data as MonthlyRevenueData).months;
  829 | 
  830 |       for (const m of months.filter(m => m.gross_kes > 0)) {
  831 |         const diffKes = round4(m.gross_kes - m.net_kes);
  832 |         const commKes = round4(m.commission_kes);
  833 |         const ok      = diffKes === commKes;
  834 | 
  835 |         allure.parameter(
  836 |           `[${m.month_label}] KES diff vs commission`,
  837 |           `gross(${m.gross_kes}) - net(${m.net_kes}) = ${diffKes} vs commission(${commKes}) â†’ ${ok ? 'OK' : 'MISMATCH'}`,
  838 |         );
  839 | 
  840 |         if (!ok) {
  841 |           flagIssue('TC-DMREV-013', 'BUG-MREV-MATH-KES',
  842 |             `${m.month_label}: gross_kes(${m.gross_kes}) âˆ’ net_kes(${m.net_kes}) = ${diffKes} â‰  commission_kes(${commKes})`,
  843 |             { month: m.month_label, gross: m.gross_kes, net: m.net_kes, computed_diff: diffKes, commission: commKes },
  844 |           );
  845 |         }
  846 | 
  847 |         logger.info('Asserting: Validate KES: gross_kes âˆ’ net_kes = commission_');
  848 |         expect(diffKes, `${m.month_label}: gross_kes âˆ’ net_kes must equal commission_kes`).toBe(commKes);
  849 |       }
  850 |       logger.pass('All assertions passed');
```