# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-010 – BUG-ASUM-09: Accept: text/html must not expose Spring Boot Whitelabel page
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:735:7

# Error details

```
Error: [BUG-ASUM-09] 406 error must NOT expose Whitelabel Error Page

expect(received).toBe(expected) // Object.is equality

Expected: false
Received: true
```

```
Error: [BUG-ASUM-09] 406 error body should be application/json

expect(received).toContain(expected) // indexOf

Expected substring: "application/json"
Received string:    "text/html;charset=UTF-8"
```

# Test source

```ts
  668 |       logger.info('Asserting: response has top-level "data" wrapper');
  669 |       expect.soft(hasDataWrapper,
  670 |         '[BUG-ASUM-07] Response must use a top-level "data" wrapper consistent with all other endpoints',
  671 |       ).toBe(true);
  672 |       logger.pass('All assertions passed');
  673 |     });
  674 |   });
  675 | 
  676 |   // ── TC-ASUM-009 ─────────────────────────────────────────────────────────────
  677 |   test('TC-ASUM-009 – BUG-ASUM-08: CORS Allow-Methods advertises POST/DELETE but server returns 405', async ({ logger }) => {
  678 |     smokeLabels('TC-ASUM-009', 'BUG-ASUM-08 – CORS Allow-Methods Inconsistent with Actual Routing', 'normal');
  679 |     let res: supertest.Response;
  680 | 
  681 |     await logger.step('Step 1 – Fetch response headers', async () => {
  682 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  683 |       res = await get();
  684 |       logger.pass('HTTP ' + res.status + ' received');
  685 |       attachResponse('TC-ASUM-009', res, { bug: 'BUG-ASUM-08' });
  686 |     });
  687 | 
  688 |     await logger.step('Step 2 – BUG-ASUM-08: CORS ACAM must match actual supported methods', async () => {
  689 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  690 |       allure.parameter('Access-Control-Allow-Methods (CORS)', acam ?? 'MISSING');
  691 | 
  692 |       // Server correctly returns 405 for POST/PUT/DELETE/PATCH
  693 |       // But CORS header says these are allowed – inconsistency
  694 |       const corsAllowsDestructive =
  695 |         acam?.toUpperCase().includes('POST')   ||
  696 |         acam?.toUpperCase().includes('DELETE') ||
  697 |         acam?.toUpperCase().includes('PUT')    ||
  698 |         acam?.toUpperCase().includes('PATCH');
  699 | 
  700 |       if (corsAllowsDestructive) {
  701 |         flagIssue('TC-ASUM-009', 'BUG-ASUM-08',
  702 |           `Access-Control-Allow-Methods: ${acam} – CORS advertises POST/PUT/DELETE/PATCH as allowed, ` +
  703 |           'but the server correctly returns 405 for all of them. ' +
  704 |           'The CORS configuration and the actual method routing are out of sync. ' +
  705 |           'ACAM should only list: GET, HEAD, OPTIONS.',
  706 |           {
  707 |             'access-control-allow-methods': acam,
  708 |             actual_supported: 'GET only (others return 405)',
  709 |             should_be: 'GET, HEAD, OPTIONS',
  710 |           },
  711 |         );
  712 |       }
  713 | 
  714 |       logger.info('Asserting: CORS ACAM must not advertise POST/DELETE/PUT/PATCH');
  715 |       expect.soft(corsAllowsDestructive,
  716 |         `[BUG-ASUM-08] CORS ACAM must not advertise POST/DELETE/PUT/PATCH when server returns 405 for them. Got: ${acam}`,
  717 |       ).toBe(false);
  718 |       logger.pass('All assertions passed');
  719 |     });
  720 | 
  721 |     await logger.step('Step 3 – Confirm POST returns 405 (method routing is correct)', async () => {
  722 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  723 |       const resPost = await getWithMethod('post');
  724 |       logger.pass('HTTP ' + resPost.status + ' received');
  725 |       logger.info('Asserting: POST returns 405 and Allow header contains GET');
  726 |       expect(resPost.status, 'POST must return 405 (correct behavior)').toBe(405);
  727 |       expect(resPost.headers['allow'], '405 response must include Allow: GET').toContain('GET');
  728 |       logger.pass('All assertions passed');
  729 |       allure.parameter('POST status (correct 405)', String(resPost.status));
  730 |       allure.parameter('Allow header in 405', String(resPost.headers['allow'] ?? 'MISSING'));
  731 |     });
  732 |   });
  733 | 
  734 |   // ── TC-ASUM-010 ─────────────────────────────────────────────────────────────
  735 |   test('TC-ASUM-010 – BUG-ASUM-09: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  736 |     smokeLabels('TC-ASUM-010', 'BUG-ASUM-09 – Framework Info Leak on 406', 'normal');
  737 | 
  738 |     await logger.step('Step 1 – Send GET with Accept: text/html', async () => {
  739 |       logger.info('GET /api/v1/analytics/dashboard/summary (Accept: text/html)');
  740 |       const res = await supertest(ANALYTICS_BASE)
  741 |         .get(ENDPOINT)
  742 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  743 |         .set('Accept', 'text/html');
  744 |       logger.pass('HTTP ' + res.status + ' received');
  745 | 
  746 |       console.log(`[TC-ASUM-010] text/html → ${res.status}, body excerpt: ${String(res.text).substring(0, 300)}`);
  747 |       allure.parameter('text/html status', String(res.status));
  748 | 
  749 |       logger.info('Asserting: status 406, no Whitelabel page');
  750 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
  751 | 
  752 |       const hasWhitelabel = String(res.text ?? '').includes('Whitelabel Error Page');
  753 |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  754 | 
  755 |       if (hasWhitelabel) {
  756 |         flagIssue('TC-ASUM-010', 'BUG-ASUM-09',
  757 |           'GET /rms-analytics/.../summary with Accept: text/html returns Spring Boot Whitelabel Error Page – ' +
  758 |           'leaks Spring Boot framework identity, server timezone (EAT), and internal request path',
  759 |           {
  760 |             status:       res.status,
  761 |             info_leaked:  ['Spring Boot', 'EAT timezone', '/api/v1/analytics/dashboard/summary path'],
  762 |             body_excerpt: String(res.text).substring(0, 400),
  763 |           },
  764 |         );
  765 |       }
  766 | 
  767 |       expect.soft(hasWhitelabel, '[BUG-ASUM-09] 406 error must NOT expose Whitelabel Error Page').toBe(false);
> 768 |       expect.soft(res.headers['content-type'], '[BUG-ASUM-09] 406 error body should be application/json').toContain('application/json');
      |                                                                                                           ^ Error: [BUG-ASUM-09] 406 error body should be application/json
  769 |       logger.pass('All assertions passed');
  770 |       allure.attachment('[TC-ASUM-010] 406 Body', String(res.text ?? ''), 'text/plain');
  771 |     });
  772 |   });
  773 | 
  774 |   // ── TC-ASUM-011 ─────────────────────────────────────────────────────────────
  775 |   test('TC-ASUM-011 – BUG-ASUM-10: Cache-Control and security headers must be present', async ({ logger }) => {
  776 |     smokeLabels('TC-ASUM-011', 'BUG-ASUM-10 – Missing Security Headers', 'normal');
  777 |     let res: supertest.Response;
  778 | 
  779 |     await logger.step('Step 1 – Fetch response headers', async () => {
  780 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  781 |       res = await get();
  782 |       logger.pass('HTTP ' + res.status + ' received');
  783 |       console.log(`[TC-ASUM-011] Headers:\n${JSON.stringify(res.headers, null, 2)}`);
  784 |       attachResponse('TC-ASUM-011', res, { bug: 'BUG-ASUM-10' });
  785 |     });
  786 | 
  787 |     await logger.step('Step 2 – Cache-Control must prevent caching of real-time analytics', async () => {
  788 |       const cc = res!.headers['cache-control'] as string | undefined;
  789 |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  790 |       if (!cc) {
  791 |         flagIssue('TC-ASUM-011', 'BUG-ASUM-10a', 'Cache-Control missing on real-time analytics endpoint',
  792 |           { expected: 'no-store, no-cache', actual: 'MISSING' });
  793 |       }
  794 |       logger.info('Asserting: Cache-Control header is present');
  795 |       expect.soft(cc, '[BUG-ASUM-10a] Cache-Control must be present').toBeDefined();
  796 |       logger.pass('All assertions passed');
  797 |     });
  798 | 
  799 |     await logger.step('Step 3 – X-Content-Type-Options: nosniff must be present', async () => {
  800 |       const h = res!.headers['x-content-type-options'] as string | undefined;
  801 |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  802 |       if (!h) flagIssue('TC-ASUM-011', 'BUG-ASUM-10b', 'X-Content-Type-Options missing', { expected: 'nosniff', actual: 'MISSING' });
  803 |       logger.info('Asserting: X-Content-Type-Options: nosniff present');
  804 |       expect.soft(h, '[BUG-ASUM-10b] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  805 |       logger.pass('All assertions passed');
  806 |     });
  807 | 
  808 |     await logger.step('Step 4 – X-Frame-Options must be present', async () => {
  809 |       const h = res!.headers['x-frame-options'] as string | undefined;
  810 |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  811 |       if (!h) flagIssue('TC-ASUM-011', 'BUG-ASUM-10c', 'X-Frame-Options missing', { expected: 'DENY', actual: 'MISSING' });
  812 |       logger.info('Asserting: X-Frame-Options header present');
  813 |       expect.soft(h, '[BUG-ASUM-10c] X-Frame-Options: DENY must be present').toBeDefined();
  814 |       logger.pass('All assertions passed');
  815 |     });
  816 | 
  817 |     await logger.step('Step 5 – Full security header audit', async () => {
  818 |       const audit = {
  819 |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  820 |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  821 |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  822 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  823 |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  824 |         'vary':                      res!.headers['vary']                      ?? 'MISSING',
  825 |       };
  826 |       allure.attachment('[TC-ASUM-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  827 |       console.log('[TC-ASUM-011] Security audit:', JSON.stringify(audit, null, 2));
  828 |     });
  829 |   });
  830 | 
  831 |   // ── TC-ASUM-012 ─────────────────────────────────────────────────────────────
  832 |   test('TC-ASUM-012 – BUG-ASUM-11: CORS wildcard origin on analytics financial endpoint', async ({ logger }) => {
  833 |     smokeLabels('TC-ASUM-012', 'BUG-ASUM-11 – Overly Permissive CORS', 'normal');
  834 |     let res: supertest.Response;
  835 | 
  836 |     await logger.step('Step 1 – Fetch response CORS headers', async () => {
  837 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  838 |       res = await get();
  839 |       logger.pass('HTTP ' + res.status + ' received');
  840 |       const acao = res.headers['access-control-allow-origin'] as string | undefined;
  841 |       allure.parameter('Access-Control-Allow-Origin', acao ?? 'MISSING');
  842 |       attachResponse('TC-ASUM-012', res, { bug: 'BUG-ASUM-11' });
  843 | 
  844 |       if (acao === '*') {
  845 |         flagIssue('TC-ASUM-012', 'BUG-ASUM-11',
  846 |           'Access-Control-Allow-Origin: * on an analytics endpoint that is ALSO unauthenticated (BUG-ASUM-01). ' +
  847 |           'Combined with the auth bypass, any webpage on the internet can read financial analytics data ' +
  848 |           'by making a simple GET request – no token, no CORS restriction, no protection at all.',
  849 |           {
  850 |             'access-control-allow-origin': acao,
  851 |             combined_with: 'BUG-ASUM-01 (auth bypass)',
  852 |             risk: 'CRITICAL – completely unprotected financial data API: no auth + no CORS restriction',
  853 |           },
  854 |         );
  855 |       }
  856 | 
  857 |       logger.info('Asserting: ACAO must not be wildcard *');
  858 |       expect.soft(acao, '[BUG-ASUM-11] ACAO must not be wildcard "*"').not.toBe('*');
  859 |       logger.pass('All assertions passed');
  860 |     });
  861 |   });
  862 | 
  863 |   // ── TC-ASUM-013 ─────────────────────────────────────────────────────────────
  864 |   test('TC-ASUM-013 – Field types: all today.* fields must be non-negative numbers', async ({ logger }) => {
  865 |     smokeLabels('TC-ASUM-013', 'Field Type Validation', 'normal');
  866 |     let res: supertest.Response;
  867 | 
  868 |     await logger.step('Step 1 – Fetch analytics summary', async () => {
```