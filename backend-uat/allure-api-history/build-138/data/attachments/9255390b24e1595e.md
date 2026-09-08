# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-monthly-revenue.spec.ts >> Backend (Konza) â€” Dashboard Monthly Revenue >> TC-DMREV-011 â€” BUG-MREV-08: Cache-Control and security headers must be present
- Location: tests/API/backend-tests/backend-dashboard-monthly-revenue.spec.ts:708:7

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
  658 |         if (res.status === 405) {
  659 |           expect(res.headers['allow'], '405 response must include Allow header listing GET').toContain('GET');
  660 |         }
  661 |         logger.pass('All assertions passed');
  662 |       });
  663 |     }
  664 |   });
  665 | 
  666 |   // â”€â”€ TC-DMREV-010 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  667 |   test('TC-DMREV-010 â€” BUG-MREV-07: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  668 |     backendLabels('TC-DMREV-010', 'BUG-MREV-07 â€” Framework Info Leak on 406', 'normal');
  669 | 
  670 |     await logger.step('Step 1 â€” Send GET with Accept: text/html', async () => {
  671 |       logger.info(`GET ${ENDPOINT}`);
  672 |       const res = await supertest(BASE_URL).get(ENDPOINT)
  673 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  674 |         .set('Accept', 'text/html');
  675 |       logger.pass('HTTP ' + res.status + ' received');
  676 | 
  677 |       console.log(`[TC-DMREV-010] Accept: text/html â†’ ${res.status}`);
  678 |       console.log(`  Excerpt: ${String(res.text).substring(0, 300)}`);
  679 |       allure.parameter('text/html status',       String(res.status));
  680 |       allure.parameter('Response Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  681 | 
  682 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
  683 | 
  684 |       const hasWhitelabel = String(res.text ?? '').includes('Whitelabel Error Page');
  685 |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  686 | 
  687 |       if (hasWhitelabel) {
  688 |         flagIssue('TC-DMREV-010', 'BUG-MREV-07',
  689 |           'GET /monthly-revenue with Accept: text/html returns Spring Boot Whitelabel Error Page â€” ' +
  690 |           'exposes Spring Boot framework, server timezone (EAT), and internal path',
  691 |           {
  692 |             status:       res.status,
  693 |             info_leaked:  ['Spring Boot framework', 'Internal path: /api/v1/dashboard/monthly-revenue', 'Server timezone (EAT)'],
  694 |             body_excerpt: String(res.text).substring(0, 400),
  695 |           },
  696 |         );
  697 |       }
  698 | 
  699 |       expect.soft(hasWhitelabel, '[BUG-MREV-07] 406 error must NOT expose Spring Boot Whitelabel Error Page').toBe(false);
  700 |       expect.soft(res.headers['content-type'], '[BUG-MREV-07] 406 error body should be application/json').toContain('application/json');
  701 | 
  702 |       allure.attachment('[TC-DMREV-010] 406 Response Body', String(res.text ?? ''), 'text/plain');
  703 |       logger.pass('All assertions passed');
  704 |     });
  705 |   });
  706 | 
  707 |   // â”€â”€ TC-DMREV-011 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  708 |   test('TC-DMREV-011 â€” BUG-MREV-08: Cache-Control and security headers must be present', async ({ logger }) => {
  709 |     backendLabels('TC-DMREV-011', 'BUG-MREV-08 â€” Missing Cache-Control & Security Headers', 'normal');
  710 |     let res: supertest.Response;
  711 | 
  712 |     await logger.step('Step 1 â€” Fetch response and inspect headers', async () => {
  713 |       logger.info(`GET ${ENDPOINT}`);
  714 |       res = await get();
  715 |       logger.pass('HTTP ' + res.status + ' received');
  716 |       console.log(`[TC-DMREV-011] Response headers:\n${JSON.stringify(res.headers, null, 2)}`);
  717 |       attachResponse('TC-DMREV-011', res, { bug: 'BUG-MREV-08' });
  718 |     });
  719 | 
  720 |     await logger.step('Step 2 â€” BUG-MREV-08: Cache-Control must prohibit caching of financial data', async () => {
  721 |       const cc = res!.headers['cache-control'] as string | undefined;
  722 |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  723 | 
  724 |       if (!cc) {
  725 |         flagIssue('TC-DMREV-011', 'BUG-MREV-08',
  726 |           'Cache-Control header is absent on /monthly-revenue â€” revenue figures update as transactions are processed. ' +
  727 |           'Without Cache-Control: no-store, proxy servers and browser caches may serve stale financial data.',
  728 |           { expected: 'Cache-Control: no-store, no-cache', actual: 'MISSING' },
  729 |         );
  730 |       }
  731 |       logger.info('Asserting: BUG-MREV-08: Cache-Control must prohibit caching');
  732 |       expect.soft(cc, '[BUG-MREV-08] Cache-Control must be present').toBeDefined();
  733 |       if (cc) {
  734 |         const preventsCache = cc.includes('no-store') || cc.includes('no-cache') || cc.includes('private');
  735 |         expect.soft(preventsCache, `[BUG-MREV-08] Cache-Control="${cc}" must prevent caching`).toBe(true);
  736 |       }
  737 |       logger.pass('All assertions passed');
  738 |     });
  739 | 
  740 |     await logger.step('Step 3 â€” X-Content-Type-Options must be nosniff', async () => {
  741 |       const h = res!.headers['x-content-type-options'] as string | undefined;
  742 |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  743 |       if (!h) {
  744 |         flagIssue('TC-DMREV-011', 'BUG-MREV-08b', 'X-Content-Type-Options: nosniff is missing', { expected: 'nosniff', actual: 'MISSING' });
  745 |       }
  746 |       logger.info('Asserting: response structure and values');
  747 |       expect.soft(h, 'X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  748 |       logger.pass('All assertions passed');
  749 |     });
  750 | 
  751 |     await logger.step('Step 4 â€” X-Frame-Options must be present', async () => {
  752 |       const h = res!.headers['x-frame-options'] as string | undefined;
  753 |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  754 |       if (!h) {
  755 |         flagIssue('TC-DMREV-011', 'BUG-MREV-08c', 'X-Frame-Options is missing', { expected: 'DENY', actual: 'MISSING' });
  756 |       }
  757 |       logger.info('Asserting: response structure and values');
> 758 |       expect.soft(h, 'X-Frame-Options: DENY must be present').toBeDefined();
      |                                                               ^ Error: X-Frame-Options: DENY must be present
  759 |       logger.pass('All assertions passed');
  760 |     });
  761 | 
  762 |     await logger.step('Step 5 â€” Full security header audit', async () => {
  763 |       const audit = {
  764 |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  765 |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  766 |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  767 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  768 |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  769 |       };
  770 |       allure.attachment('[TC-DMREV-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  771 |     });
  772 |   });
  773 | 
  774 |   // â”€â”€ TC-DMREV-012 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  775 |   test('TC-DMREV-012 â€” BUG-MREV-09: CORS Access-Control-Allow-Origin: * is overly permissive', async ({ logger }) => {
  776 |     backendLabels('TC-DMREV-012', 'BUG-MREV-09 â€” Overly Permissive CORS', 'normal');
  777 |     let res: supertest.Response;
  778 | 
  779 |     await logger.step('Step 1 â€” Fetch response and check CORS headers', async () => {
  780 |       logger.info(`GET ${ENDPOINT}`);
  781 |       res = await get();
  782 |       logger.pass('HTTP ' + res.status + ' received');
  783 |       const acao = res.headers['access-control-allow-origin'] as string | undefined;
  784 |       const acam = res.headers['access-control-allow-methods'] as string | undefined;
  785 |       allure.parameter('Access-Control-Allow-Origin',  acao ?? 'MISSING');
  786 |       allure.parameter('Access-Control-Allow-Methods', acam ?? 'MISSING');
  787 |       attachResponse('TC-DMREV-012', res, { bug: 'BUG-MREV-09' });
  788 |     });
  789 | 
  790 |     await logger.step('Step 2 â€” ACAO must not be wildcard on a financial endpoint', async () => {
  791 |       const acao = res!.headers['access-control-allow-origin'] as string | undefined;
  792 | 
  793 |       if (acao === '*') {
  794 |         flagIssue('TC-DMREV-012', 'BUG-MREV-09a',
  795 |           'Access-Control-Allow-Origin: * allows any website to initiate authenticated requests ' +
  796 |           'to /monthly-revenue and read back financial data. Must be restricted to the RMS web app origin.',
  797 |           { actual: 'ACAO: *', risk: 'Cross-site data read of financial revenue breakdowns', expected: 'specific trusted domain' },
  798 |         );
  799 |       }
  800 |       logger.info('Asserting: response structure and values');
  801 |       expect.soft(acao, '[BUG-MREV-09a] ACAO must not be wildcard "*"').not.toBe('*');
  802 |       logger.pass('All assertions passed');
  803 |     });
  804 | 
  805 |     await logger.step('Step 3 â€” Destructive methods must not be CORS-allowed on GET-only endpoint', async () => {
  806 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  807 |       const bad  = ['DELETE','POST','PUT','PATCH'].filter(m => acam?.toUpperCase().includes(m));
  808 | 
  809 |       if (bad.length) {
  810 |         flagIssue('TC-DMREV-012', 'BUG-MREV-09b',
  811 |           `CORS allows ${bad.join(', ')} from any origin on a read-only endpoint`,
  812 |           { 'access-control-allow-methods': acam },
  813 |         );
  814 |       }
  815 |       logger.info('Asserting: response structure and values');
  816 |       expect.soft(bad.length, `[BUG-MREV-09b] CORS must not allow destructive methods: ${bad.join(', ')}`).toBe(0);
  817 |       logger.pass('All assertions passed');
  818 |     });
  819 |   });
  820 | 
  821 |   // â”€â”€ TC-DMREV-013 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  822 |   test('TC-DMREV-013 â€” Math: per-month gross âˆ’ net = commission for every month', async ({ logger }) => {
  823 |     backendLabels('TC-DMREV-013', 'Per-Month Math: gross âˆ’ net = commission', 'critical');
  824 |     let res: supertest.Response;
  825 | 
  826 |     await logger.step('Step 1 â€” Fetch monthly-revenue', async () => {
  827 |       logger.info(`GET ${ENDPOINT}`);
  828 |       res = await get();
  829 |       logger.pass('HTTP ' + res.status + ' received');
  830 |       expect(res.status).toBe(200);
  831 |       attachResponse('TC-DMREV-013', res);
  832 |       logger.pass('All assertions passed');
  833 |     });
  834 | 
  835 |     await logger.step('Step 2 â€” Validate KES: gross_kes âˆ’ net_kes = commission_kes for each month', async () => {
  836 |       const months = (res!.body.data as MonthlyRevenueData).months;
  837 | 
  838 |       for (const m of months.filter(m => m.gross_kes > 0)) {
  839 |         const diffKes = round4(m.gross_kes - m.net_kes);
  840 |         const commKes = round4(m.commission_kes);
  841 |         const ok      = diffKes === commKes;
  842 | 
  843 |         allure.parameter(
  844 |           `[${m.month_label}] KES diff vs commission`,
  845 |           `gross(${m.gross_kes}) - net(${m.net_kes}) = ${diffKes} vs commission(${commKes}) â†’ ${ok ? 'OK' : 'MISMATCH'}`,
  846 |         );
  847 | 
  848 |         if (!ok) {
  849 |           flagIssue('TC-DMREV-013', 'BUG-MREV-MATH-KES',
  850 |             `${m.month_label}: gross_kes(${m.gross_kes}) âˆ’ net_kes(${m.net_kes}) = ${diffKes} â‰  commission_kes(${commKes})`,
  851 |             { month: m.month_label, gross: m.gross_kes, net: m.net_kes, computed_diff: diffKes, commission: commKes },
  852 |           );
  853 |         }
  854 | 
  855 |         logger.info('Asserting: Validate KES: gross_kes âˆ’ net_kes = commission_');
  856 |         expect(diffKes, `${m.month_label}: gross_kes âˆ’ net_kes must equal commission_kes`).toBe(commKes);
  857 |       }
  858 |       logger.pass('All assertions passed');
```