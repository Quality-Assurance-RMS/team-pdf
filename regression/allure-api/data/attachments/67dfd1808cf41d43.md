# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> OAuth Token Service >> [BUG-OT-007] TC-OT-015 — GET /api/v1/token with Accept: text/html returns 500 (must be 406)
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:742:7

# Error details

```
Error: [BUG-OT-007] Unsupported Accept type must return 406, got 500

expect(received).toBe(expected) // Object.is equality

Expected: 406
Received: 500
```

# Test source

```ts
  674 |       'confusing 500 error instead of a clear 405 with the Allow header. This also indicates ' +
  675 |       'unhandled exception paths that could mask real server errors.',
  676 |     );
  677 |     let res: supertest.Response;
  678 |     await logger.step('Step 1 — Send PUT /api/v1/token', async () => {
  679 |       allure.parameter('Method used', 'PUT (unsupported — should be GET only)');
  680 |       logger.info('PUT /api/v1/token');
  681 |       res = await PUT(OAUTH_BASE, '/api/v1/token', BEARER, {});
  682 |       logger.pass('HTTP ' + res.status + ' received');
  683 |       attach('Request & Response', 'PUT', OAUTH_BASE, '/api/v1/token', res);
  684 |       allure.parameter('HTTP Status', String(res.status));
  685 |     });
  686 |     await logger.step('Step 2 — Assert 405 (BUG: returns 500)', async () => {
  687 |       logger.info('Asserting: HTTP status is 405');
  688 |       bugEvidence('BUG-OT-005',
  689 |         '405 Method Not Allowed with Allow: GET header',
  690 |         `HTTP ${res!.status} Internal Server Error`,
  691 |         'Misleading error code; unhandled method exceptions expose server instability',
  692 |         'Add Spring MVC @RequestMapping(method=GET) or handle MethodNotAllowedException globally',
  693 |       );
  694 |       expect(res!.status, `[BUG-OT-005] PUT /token must return 405, got ${res!.status}`).toBe(405);
  695 |       logger.pass('All assertions passed');
  696 |     });
  697 |   });
  698 | 
  699 |   // ──────────────────────────────────────────────────────────────────────────
  700 |   // TC-OT-014 — [BUG-OT-006] DELETE /token → expect 405, actual 500
  701 |   // ──────────────────────────────────────────────────────────────────────────
  702 |   test('[BUG-OT-006] TC-OT-014 — DELETE /api/v1/token: Must return 405 Method Not Allowed (returns 500)', async ({ logger }) => {
  703 |     labelsOT('TC-OT-014', 'HTTP Method Validation: DELETE on GET-only endpoint', 'medium');
  704 |     await allure.description(
  705 |       '**[KNOWN BUG — BUG-OT-006]**\n\n' +
  706 |       '**What this test does:**\n' +
  707 |       'Sends a DELETE request to /api/v1/token (a GET-only endpoint — the DELETE endpoint ' +
  708 |       'is /api/v1/token/cache) and verifies the server returns 405 Method Not Allowed.\n\n' +
  709 |       '**Expected:** HTTP 405 Method Not Allowed\n' +
  710 |       '**Actual:** HTTP 500 Internal Server Error\n\n' +
  711 |       '**Root Cause:** Same as BUG-OT-005 — unsupported methods trigger an unhandled exception ' +
  712 |       'in the service instead of a standard 405 response.\n\n' +
  713 |       '**Additional Risk:** A client who mistypes the path and sends DELETE /api/v1/token ' +
  714 |       'instead of DELETE /api/v1/token/cache will receive a confusing 500 instead of a ' +
  715 |       'helpful 405 that would guide them to the correct endpoint.',
  716 |     );
  717 |     let res: supertest.Response;
  718 |     await logger.step('Step 1 — Send DELETE /api/v1/token (not /token/cache)', async () => {
  719 |       allure.parameter('Method used', 'DELETE on /api/v1/token (correct DELETE path is /api/v1/token/cache)');
  720 |       logger.info('DELETE /api/v1/token');
  721 |       res = await DELETE(OAUTH_BASE, '/api/v1/token', BEARER);
  722 |       logger.pass('HTTP ' + res.status + ' received');
  723 |       attach('Request & Response', 'DELETE', OAUTH_BASE, '/api/v1/token', res);
  724 |       allure.parameter('HTTP Status', String(res.status));
  725 |     });
  726 |     await logger.step('Step 2 — Assert 405 (BUG: returns 500)', async () => {
  727 |       logger.info('Asserting: HTTP status is 405');
  728 |       bugEvidence('BUG-OT-006',
  729 |         '405 Method Not Allowed',
  730 |         `HTTP ${res!.status} Internal Server Error`,
  731 |         'Confusing error; client cannot distinguish wrong-method from wrong-path without a proper 405',
  732 |         'Add global MethodNotAllowedException handler returning 405 + Allow header',
  733 |       );
  734 |       expect(res!.status, `[BUG-OT-006] DELETE /token must return 405, got ${res!.status}`).toBe(405);
  735 |       logger.pass('All assertions passed');
  736 |     });
  737 |   });
  738 | 
  739 |   // ──────────────────────────────────────────────────────────────────────────
  740 |   // TC-OT-015 — [BUG-OT-007] Accept: text/html → expect 406, actual 500
  741 |   // ──────────────────────────────────────────────────────────────────────────
  742 |   test('[BUG-OT-007] TC-OT-015 — GET /api/v1/token with Accept: text/html returns 500 (must be 406)', async ({ logger }) => {
  743 |     labelsOT('TC-OT-015', 'Content Negotiation: Unsupported Accept type', 'low');
  744 |     await allure.description(
  745 |       '**[KNOWN BUG — BUG-OT-007]**\n\n' +
  746 |       '**What this test does:**\n' +
  747 |       'Sends GET /api/v1/token with "Accept: text/html" to test content negotiation. ' +
  748 |       'The service only produces application/json, so it should return 406 Not Acceptable.\n\n' +
  749 |       '**Expected:** HTTP 406 Not Acceptable\n' +
  750 |       '**Actual:** HTTP 500 Internal Server Error\n\n' +
  751 |       '**Root Cause:** The service does not handle the HttpMediaTypeNotAcceptableException ' +
  752 |       'thrown by Spring when it cannot satisfy the Accept header. The exception propagates ' +
  753 |       'to the global error handler and is returned as a generic 500.\n\n' +
  754 |       '**Impact:** API clients that probe content types receive a misleading 500 error. ' +
  755 |       'Automated API scanners may flag this as a server crash rather than a normal negotiation failure.',
  756 |     );
  757 |     let res: supertest.Response;
  758 |     await logger.step('Step 1 — Send GET /api/v1/token with Accept: text/html', async () => {
  759 |       allure.parameter('Accept header', 'text/html (unsupported — service produces application/json only)');
  760 |       logger.info('GET /api/v1/token');
  761 |       res = await GET(OAUTH_BASE, '/api/v1/token', BEARER, { Accept: 'text/html' });
  762 |       logger.pass('HTTP ' + res.status + ' received');
  763 |       attach('Request & Response', 'GET', OAUTH_BASE, '/api/v1/token', res);
  764 |       allure.parameter('HTTP Status', String(res.status));
  765 |     });
  766 |     await logger.step('Step 2 — Assert 406 (BUG: returns 500)', async () => {
  767 |       logger.info('Asserting: HTTP status is 406');
  768 |       bugEvidence('BUG-OT-007',
  769 |         '406 Not Acceptable',
  770 |         `HTTP ${res!.status} Internal Server Error`,
  771 |         'Misleading error; clients cannot detect content-type mismatch vs real server errors',
  772 |         'Handle HttpMediaTypeNotAcceptableException globally and return 406',
  773 |       );
> 774 |       expect(res!.status, `[BUG-OT-007] Unsupported Accept type must return 406, got ${res!.status}`).toBe(406);
      |                                                                                                       ^ Error: [BUG-OT-007] Unsupported Accept type must return 406, got 500
  775 |       logger.pass('All assertions passed');
  776 |     });
  777 |   });
  778 | 
  779 | });
  780 | 
  781 | // ═════════════════════════════════════════════════════════════════════════════
  782 | //  SPR FETCHER SERVICE
  783 | // ═════════════════════════════════════════════════════════════════════════════
  784 | 
  785 | test.describe('SPR Fetcher Service', () => {
  786 | 
  787 |   // ──────────────────────────────────────────────────────────────────────────
  788 |   // TC-SF-001 — Happy path: 200 with status object
  789 |   // ──────────────────────────────────────────────────────────────────────────
  790 |   test('TC-SF-001 — GET /api/v1/fetch/status: Returns 200 with service status object', async ({ logger }) => {
  791 |     labelsSF('TC-SF-001', 'Service Status', 'critical');
  792 |     await allure.description(
  793 |       '**What this test does:**\n' +
  794 |       'Calls GET /spr-fetcher/api/v1/fetch/status with a valid Bearer token and verifies ' +
  795 |       'the response is HTTP 200 with a non-empty JSON object.\n\n' +
  796 |       '**Why it matters:** This is the primary health/status endpoint for the SPR Fetcher. ' +
  797 |       'If it returns an error, operators cannot determine whether the service is running, ' +
  798 |       'what configuration it is using, or whether it can successfully reach the KCB API.',
  799 |     );
  800 |     let res: supertest.Response;
  801 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status with auth', async () => {
  802 |       allure.parameter('Endpoint', 'GET /spr-fetcher/api/v1/fetch/status');
  803 |       allure.parameter('Auth',     'Valid Bearer token');
  804 |       logger.info('GET /api/v1/fetch/status');
  805 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  806 |       logger.pass('HTTP ' + res.status + ' received');
  807 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  808 |       allure.parameter('HTTP Status',  String(res.status));
  809 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  810 |     });
  811 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  812 |       logger.info('Asserting: HTTP status is 200');
  813 |       assertOk(res!, 'TC-SF-001');
  814 |       logger.pass('All assertions passed');
  815 |     });
  816 |     await logger.step('Step 3 — Assert body has at least one field', async () => {
  817 |       logger.info('Asserting: response body is non-empty');
  818 |       expect(Object.keys(res!.body).length, 'Body must not be empty').toBeGreaterThan(0);
  819 |       allure.parameter('Fields', Object.keys(res!.body).join(', '));
  820 |       logger.pass('All assertions passed');
  821 |     });
  822 |   });
  823 | 
  824 |   // ──────────────────────────────────────────────────────────────────────────
  825 |   // TC-SF-002 — All 7 required fields present
  826 |   // ──────────────────────────────────────────────────────────────────────────
  827 |   test('TC-SF-002 — GET /api/v1/fetch/status: All 7 required fields are present and non-null', async ({ logger }) => {
  828 |     labelsSF('TC-SF-002', 'Status Response Fields Validation', 'normal');
  829 |     await allure.description(
  830 |       '**What this test does:**\n' +
  831 |       'Verifies that the /fetch/status response contains exactly these 7 required fields:\n' +
  832 |       'service, status, mockEnabled, tokenInjectionEnabled, accountNumber, apiEndpoint, checkedAt\n\n' +
  833 |       '**Why it matters:** Each field serves a specific operational purpose. A missing field ' +
  834 |       'means the status response is incomplete and cannot be relied upon for monitoring:\n' +
  835 |       '- service: identifies which microservice responded\n' +
  836 |       '- status: UP/DOWN health indicator\n' +
  837 |       '- mockEnabled: whether the service is using live or mock data\n' +
  838 |       '- tokenInjectionEnabled: whether OAuth token is injected into KCB calls\n' +
  839 |       '- accountNumber: the KCB account being queried\n' +
  840 |       '- apiEndpoint: the upstream KCB API URL in use\n' +
  841 |       '- checkedAt: timestamp of the last status check',
  842 |     );
  843 |     let res: supertest.Response;
  844 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  845 |       logger.info('GET /api/v1/fetch/status');
  846 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  847 |       logger.pass('HTTP ' + res.status + ' received');
  848 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  849 |     });
  850 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  851 |       logger.info('Asserting: HTTP status is 200');
  852 |       assertOk(res!, 'TC-SF-002');
  853 |       logger.pass('All assertions passed');
  854 |     });
  855 |     await logger.step('Step 3 — Check each required field', async () => {
  856 |       logger.info('Asserting: all 7 required fields are present and non-null');
  857 |       const b = res!.body;
  858 |       const fields = ['service', 'status', 'mockEnabled', 'tokenInjectionEnabled', 'accountNumber', 'apiEndpoint', 'checkedAt'];
  859 |       for (const f of fields) expect(b[f] !== undefined && b[f] !== null, `"${f}" must be present`).toBe(true);
  860 |       fields.forEach(f => allure.parameter(f, String(b[f])));
  861 |       logger.pass('All assertions passed');
  862 |     });
  863 |   });
  864 | 
  865 |   // ──────────────────────────────────────────────────────────────────────────
  866 |   // TC-SF-003 — service = "spr-fetcher-service", status = "UP"
  867 |   // ──────────────────────────────────────────────────────────────────────────
  868 |   test('TC-SF-003 — GET /api/v1/fetch/status: service="spr-fetcher-service" and status="UP"', async ({ logger }) => {
  869 |     labelsSF('TC-SF-003', 'Service Identity and Operational Status', 'normal');
  870 |     await allure.description(
  871 |       '**What this test does:**\n' +
  872 |       'Checks two key identity fields in the status response:\n' +
  873 |       '- service must equal "spr-fetcher-service"\n' +
  874 |       '- status must equal "UP"\n\n' +
```