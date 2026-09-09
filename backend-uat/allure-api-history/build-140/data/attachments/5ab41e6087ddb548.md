# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-analytics-dashboard-summary.spec.ts >> Backend (Konza) – Analytics Dashboard Summary >> TC-ASUM-010 – BUG-ASUM-09: Accept: text/html must not expose Spring Boot Whitelabel page
- Location: tests/API/backend-tests/backend-analytics-dashboard-summary.spec.ts:743:7

# Error details

```
Error: Accept: text/html must return 406

expect(received).toBe(expected) // Object.is equality

Expected: 406
Received: 502
```

# Test source

```ts
  658 |       allure.parameter('Top-level keys',    topLevelKeys.join(', '));
  659 |       allure.parameter('Has "data" wrapper', String(hasDataWrapper));
  660 | 
  661 |       if (!hasDataWrapper) {
  662 |         flagIssue('TC-ASUM-008', 'BUG-ASUM-07',
  663 |           'This endpoint returns {mcda_id, today:{...}} without a top-level "data" wrapper. ' +
  664 |           'All other dashboard/analytics endpoints use {data:{...}} as their response envelope ' +
  665 |           '(e.g. /dashboard/today-payments, /dashboard/monthly-revenue, /dashboard/revenue-trend). ' +
  666 |           'Inconsistent response envelopes force clients to handle multiple response shapes – ' +
  667 |           'a shared API contract requires a uniform envelope.',
  668 |           {
  669 |             actual_structure:   topLevelKeys,
  670 |             expected_structure: '{ data: { mcda_id, today: {...} } }',
  671 |             other_endpoints:    '{ data: { ... } }',
  672 |           },
  673 |         );
  674 |       }
  675 | 
  676 |       logger.info('Asserting: response has top-level "data" wrapper');
  677 |       expect.soft(hasDataWrapper,
  678 |         '[BUG-ASUM-07] Response must use a top-level "data" wrapper consistent with all other endpoints',
  679 |       ).toBe(true);
  680 |       logger.pass('All assertions passed');
  681 |     });
  682 |   });
  683 | 
  684 |   // ── TC-ASUM-009 ─────────────────────────────────────────────────────────────
  685 |   test('TC-ASUM-009 – BUG-ASUM-08: CORS Allow-Methods advertises POST/DELETE but server returns 405', async ({ logger }) => {
  686 |     backendLabels('TC-ASUM-009', 'BUG-ASUM-08 – CORS Allow-Methods Inconsistent with Actual Routing', 'normal');
  687 |     let res: supertest.Response;
  688 | 
  689 |     await logger.step('Step 1 – Fetch response headers', async () => {
  690 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  691 |       res = await get();
  692 |       logger.pass('HTTP ' + res.status + ' received');
  693 |       attachResponse('TC-ASUM-009', res, { bug: 'BUG-ASUM-08' });
  694 |     });
  695 | 
  696 |     await logger.step('Step 2 – BUG-ASUM-08: CORS ACAM must match actual supported methods', async () => {
  697 |       const acam = res!.headers['access-control-allow-methods'] as string | undefined;
  698 |       allure.parameter('Access-Control-Allow-Methods (CORS)', acam ?? 'MISSING');
  699 | 
  700 |       // Server correctly returns 405 for POST/PUT/DELETE/PATCH
  701 |       // But CORS header says these are allowed – inconsistency
  702 |       const corsAllowsDestructive =
  703 |         acam?.toUpperCase().includes('POST')   ||
  704 |         acam?.toUpperCase().includes('DELETE') ||
  705 |         acam?.toUpperCase().includes('PUT')    ||
  706 |         acam?.toUpperCase().includes('PATCH');
  707 | 
  708 |       if (corsAllowsDestructive) {
  709 |         flagIssue('TC-ASUM-009', 'BUG-ASUM-08',
  710 |           `Access-Control-Allow-Methods: ${acam} – CORS advertises POST/PUT/DELETE/PATCH as allowed, ` +
  711 |           'but the server correctly returns 405 for all of them. ' +
  712 |           'The CORS configuration and the actual method routing are out of sync. ' +
  713 |           'ACAM should only list: GET, HEAD, OPTIONS.',
  714 |           {
  715 |             'access-control-allow-methods': acam,
  716 |             actual_supported: 'GET only (others return 405)',
  717 |             should_be: 'GET, HEAD, OPTIONS',
  718 |           },
  719 |         );
  720 |       }
  721 | 
  722 |       logger.info('Asserting: CORS ACAM must not advertise POST/DELETE/PUT/PATCH');
  723 |       expect.soft(corsAllowsDestructive,
  724 |         `[BUG-ASUM-08] CORS ACAM must not advertise POST/DELETE/PUT/PATCH when server returns 405 for them. Got: ${acam}`,
  725 |       ).toBe(false);
  726 |       logger.pass('All assertions passed');
  727 |     });
  728 | 
  729 |     await logger.step('Step 3 – Confirm POST returns 405 (method routing is correct)', async () => {
  730 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  731 |       const resPost = await getWithMethod('post');
  732 |       logger.pass('HTTP ' + resPost.status + ' received');
  733 |       logger.info('Asserting: POST returns 405 and Allow header contains GET');
  734 |       expect(resPost.status, 'POST must return 405 (correct behavior)').toBe(405);
  735 |       expect(resPost.headers['allow'], '405 response must include Allow: GET').toContain('GET');
  736 |       logger.pass('All assertions passed');
  737 |       allure.parameter('POST status (correct 405)', String(resPost.status));
  738 |       allure.parameter('Allow header in 405', String(resPost.headers['allow'] ?? 'MISSING'));
  739 |     });
  740 |   });
  741 | 
  742 |   // ── TC-ASUM-010 ─────────────────────────────────────────────────────────────
  743 |   test('TC-ASUM-010 – BUG-ASUM-09: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  744 |     backendLabels('TC-ASUM-010', 'BUG-ASUM-09 – Framework Info Leak on 406', 'normal');
  745 | 
  746 |     await logger.step('Step 1 – Send GET with Accept: text/html', async () => {
  747 |       logger.info('GET /api/v1/analytics/dashboard/summary (Accept: text/html)');
  748 |       const res = await supertest(ANALYTICS_BASE)
  749 |         .get(ENDPOINT)
  750 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  751 |         .set('Accept', 'text/html');
  752 |       logger.pass('HTTP ' + res.status + ' received');
  753 | 
  754 |       console.log(`[TC-ASUM-010] text/html → ${res.status}, body excerpt: ${String(res.text).substring(0, 300)}`);
  755 |       allure.parameter('text/html status', String(res.status));
  756 | 
  757 |       logger.info('Asserting: status 406, no Whitelabel page');
> 758 |       expect(res.status, 'Accept: text/html must return 406').toBe(406);
      |                                                               ^ Error: Accept: text/html must return 406
  759 | 
  760 |       const hasWhitelabel = String(res.text ?? '').includes('Whitelabel Error Page');
  761 |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  762 | 
  763 |       if (hasWhitelabel) {
  764 |         flagIssue('TC-ASUM-010', 'BUG-ASUM-09',
  765 |           'GET /rms-analytics/.../summary with Accept: text/html returns Spring Boot Whitelabel Error Page – ' +
  766 |           'leaks Spring Boot framework identity, server timezone (EAT), and internal request path',
  767 |           {
  768 |             status:       res.status,
  769 |             info_leaked:  ['Spring Boot', 'EAT timezone', '/api/v1/analytics/dashboard/summary path'],
  770 |             body_excerpt: String(res.text).substring(0, 400),
  771 |           },
  772 |         );
  773 |       }
  774 | 
  775 |       expect.soft(hasWhitelabel, '[BUG-ASUM-09] 406 error must NOT expose Whitelabel Error Page').toBe(false);
  776 |       expect.soft(res.headers['content-type'], '[BUG-ASUM-09] 406 error body should be application/json').toContain('application/json');
  777 |       logger.pass('All assertions passed');
  778 |       allure.attachment('[TC-ASUM-010] 406 Body', String(res.text ?? ''), 'text/plain');
  779 |     });
  780 |   });
  781 | 
  782 |   // ── TC-ASUM-011 ─────────────────────────────────────────────────────────────
  783 |   test('TC-ASUM-011 – BUG-ASUM-10: Cache-Control and security headers must be present', async ({ logger }) => {
  784 |     backendLabels('TC-ASUM-011', 'BUG-ASUM-10 – Missing Security Headers', 'normal');
  785 |     let res: supertest.Response;
  786 | 
  787 |     await logger.step('Step 1 – Fetch response headers', async () => {
  788 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  789 |       res = await get();
  790 |       logger.pass('HTTP ' + res.status + ' received');
  791 |       console.log(`[TC-ASUM-011] Headers:\n${JSON.stringify(res.headers, null, 2)}`);
  792 |       attachResponse('TC-ASUM-011', res, { bug: 'BUG-ASUM-10' });
  793 |     });
  794 | 
  795 |     await logger.step('Step 2 – Cache-Control must prevent caching of real-time analytics', async () => {
  796 |       const cc = res!.headers['cache-control'] as string | undefined;
  797 |       allure.parameter('Cache-Control', cc ?? 'MISSING');
  798 |       if (!cc) {
  799 |         flagIssue('TC-ASUM-011', 'BUG-ASUM-10a', 'Cache-Control missing on real-time analytics endpoint',
  800 |           { expected: 'no-store, no-cache', actual: 'MISSING' });
  801 |       }
  802 |       logger.info('Asserting: Cache-Control header is present');
  803 |       expect.soft(cc, '[BUG-ASUM-10a] Cache-Control must be present').toBeDefined();
  804 |       logger.pass('All assertions passed');
  805 |     });
  806 | 
  807 |     await logger.step('Step 3 – X-Content-Type-Options: nosniff must be present', async () => {
  808 |       const h = res!.headers['x-content-type-options'] as string | undefined;
  809 |       allure.parameter('X-Content-Type-Options', h ?? 'MISSING');
  810 |       if (!h) flagIssue('TC-ASUM-011', 'BUG-ASUM-10b', 'X-Content-Type-Options missing', { expected: 'nosniff', actual: 'MISSING' });
  811 |       logger.info('Asserting: X-Content-Type-Options: nosniff present');
  812 |       expect.soft(h, '[BUG-ASUM-10b] X-Content-Type-Options: nosniff must be present').toBe('nosniff');
  813 |       logger.pass('All assertions passed');
  814 |     });
  815 | 
  816 |     await logger.step('Step 4 – X-Frame-Options must be present', async () => {
  817 |       const h = res!.headers['x-frame-options'] as string | undefined;
  818 |       allure.parameter('X-Frame-Options', h ?? 'MISSING');
  819 |       if (!h) flagIssue('TC-ASUM-011', 'BUG-ASUM-10c', 'X-Frame-Options missing', { expected: 'DENY', actual: 'MISSING' });
  820 |       logger.info('Asserting: X-Frame-Options header present');
  821 |       expect.soft(h, '[BUG-ASUM-10c] X-Frame-Options: DENY must be present').toBeDefined();
  822 |       logger.pass('All assertions passed');
  823 |     });
  824 | 
  825 |     await logger.step('Step 5 – Full security header audit', async () => {
  826 |       const audit = {
  827 |         'cache-control':             res!.headers['cache-control']             ?? 'MISSING',
  828 |         'x-content-type-options':    res!.headers['x-content-type-options']    ?? 'MISSING',
  829 |         'x-frame-options':           res!.headers['x-frame-options']           ?? 'MISSING',
  830 |         'strict-transport-security': res!.headers['strict-transport-security'] ?? 'MISSING',
  831 |         'content-security-policy':   res!.headers['content-security-policy']   ?? 'MISSING',
  832 |         'vary':                      res!.headers['vary']                      ?? 'MISSING',
  833 |       };
  834 |       allure.attachment('[TC-ASUM-011] Security Header Audit', JSON.stringify(audit, null, 2), 'application/json');
  835 |       console.log('[TC-ASUM-011] Security audit:', JSON.stringify(audit, null, 2));
  836 |     });
  837 |   });
  838 | 
  839 |   // ── TC-ASUM-012 ─────────────────────────────────────────────────────────────
  840 |   test('TC-ASUM-012 – BUG-ASUM-11: CORS wildcard origin on analytics financial endpoint', async ({ logger }) => {
  841 |     backendLabels('TC-ASUM-012', 'BUG-ASUM-11 – Overly Permissive CORS', 'normal');
  842 |     let res: supertest.Response;
  843 | 
  844 |     await logger.step('Step 1 – Fetch response CORS headers', async () => {
  845 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  846 |       res = await get();
  847 |       logger.pass('HTTP ' + res.status + ' received');
  848 |       const acao = res.headers['access-control-allow-origin'] as string | undefined;
  849 |       allure.parameter('Access-Control-Allow-Origin', acao ?? 'MISSING');
  850 |       attachResponse('TC-ASUM-012', res, { bug: 'BUG-ASUM-11' });
  851 | 
  852 |       if (acao === '*') {
  853 |         flagIssue('TC-ASUM-012', 'BUG-ASUM-11',
  854 |           'Access-Control-Allow-Origin: * on an analytics endpoint that is ALSO unauthenticated (BUG-ASUM-01). ' +
  855 |           'Combined with the auth bypass, any webpage on the internet can read financial analytics data ' +
  856 |           'by making a simple GET request – no token, no CORS restriction, no protection at all.',
  857 |           {
  858 |             'access-control-allow-origin': acao,
```