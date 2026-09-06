# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> OAuth Token Service >> [BUG-OT-005] TC-OT-013 — PUT /api/v1/token: Must return 405 Method Not Allowed (returns 500)
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:662:7

# Error details

```
Error: [BUG-OT-005] PUT /token must return 405, got 502

expect(received).toBe(expected) // Object.is equality

Expected: 405
Received: 502
```

# Test source

```ts
  594 |       '3. May cause cascading failures if the KCB auth server is slow or unavailable',
  595 |     );
  596 |     let res: supertest.Response;
  597 |     await logger.step('Step 1 — Send DELETE /api/v1/token/cache WITHOUT Authorization header', async () => {
  598 |       allure.parameter('Authorization', 'NOT PROVIDED — intentionally omitted');
  599 |       logger.info('DELETE /api/v1/token/cache');
  600 |       res = await DELETE(OAUTH_BASE, '/api/v1/token/cache');
  601 |       logger.pass('HTTP ' + res.status + ' received');
  602 |       attach('Request & Response (no auth)', 'DELETE', OAUTH_BASE, '/api/v1/token/cache', res, false);
  603 |       allure.parameter('HTTP Status', String(res.status));
  604 |     });
  605 |     await logger.step('Step 2 — Assert 401/403 (BUG: returns 204)', async () => {
  606 |       logger.info('Asserting: status is 401 or 403');
  607 |       bugEvidence('BUG-OT-003',
  608 |         '401 Unauthorized',
  609 |         `HTTP ${res!.status} — cache evicted without authentication`,
  610 |         'DoS risk: attacker can evict cache repeatedly, triggering KCB rate-limits',
  611 |         'Add JWT auth enforcement to DELETE /oauth-token/api/v1/token/cache in APISIX',
  612 |       );
  613 |       expect([401, 403], `[BUG-OT-003] No-auth DELETE /cache must return 401/403, got ${res!.status}`).toContain(res!.status);
  614 |       logger.pass('All assertions passed');
  615 |     });
  616 |   });
  617 | 
  618 |   // ──────────────────────────────────────────────────────────────────────────
  619 |   // TC-OT-012 — [BUG-OT-004] GET /actuator/health → expect 200, actual 504
  620 |   // ──────────────────────────────────────────────────────────────────────────
  621 |   test('[BUG-OT-004] TC-OT-012 — GET /actuator/health: Must return 200 UP (returns 504)', async ({ logger }) => {
  622 |     labelsOT('TC-OT-012', 'Health Check Endpoint', 'high');
  623 |     await allure.description(
  624 |       '**[KNOWN BUG — BUG-OT-004]**\n\n' +
  625 |       '**What this test does:**\n' +
  626 |       'Calls GET /oauth-token/actuator/health and verifies it returns 200 with body {"status":"UP"}.\n\n' +
  627 |       '**Expected:** HTTP 200 with {"status":"UP"}\n' +
  628 |       '**Actual:** HTTP 504 Gateway Timeout\n\n' +
  629 |       '**Root Cause:** The APISIX gateway has no route configured for /oauth-token/actuator/*. ' +
  630 |       'The request never reaches the Spring Boot actuator endpoint.\n\n' +
  631 |       '**Impact:** The following are all broken:\n' +
  632 |       '1. Kubernetes liveness and readiness probes (causing unnecessary pod restarts or ' +
  633 |       '   marking the pod as not-ready)\n' +
  634 |       '2. Uptime monitoring dashboards\n' +
  635 |       '3. CI/CD pipeline health checks that gate deployments',
  636 |     );
  637 |     let res: supertest.Response;
  638 |     await logger.step('Step 1 — Send GET /actuator/health', async () => {
  639 |       logger.info('GET /actuator/health');
  640 |       res = await GET(OAUTH_BASE, '/actuator/health', BEARER);
  641 |       logger.pass('HTTP ' + res.status + ' received');
  642 |       attach('Request & Response', 'GET', OAUTH_BASE, '/actuator/health', res);
  643 |       allure.parameter('HTTP Status', String(res.status));
  644 |     });
  645 |     await logger.step('Step 2 — Assert 200 with status=UP (BUG: returns 504)', async () => {
  646 |       logger.info('Asserting: HTTP 200 and body status is "UP"');
  647 |       bugEvidence('BUG-OT-004',
  648 |         'HTTP 200 {"status":"UP"}',
  649 |         `HTTP ${res!.status}`,
  650 |         'K8s probes, monitoring, and CI health gates are all broken',
  651 |         'Add APISIX route: GET /oauth-token/actuator/health → upstream actuator port',
  652 |       );
  653 |       expect(res!.status, `[BUG-OT-004] Health endpoint returned ${res!.status}, expected 200`).toBe(200);
  654 |       expect(res!.body.status, 'Health body status must be "UP"').toBe('UP');
  655 |       logger.pass('All assertions passed');
  656 |     });
  657 |   });
  658 | 
  659 |   // ──────────────────────────────────────────────────────────────────────────
  660 |   // TC-OT-013 — [BUG-OT-005] PUT /token → expect 405, actual 500
  661 |   // ──────────────────────────────────────────────────────────────────────────
  662 |   test('[BUG-OT-005] TC-OT-013 — PUT /api/v1/token: Must return 405 Method Not Allowed (returns 500)', async ({ logger }) => {
  663 |     labelsOT('TC-OT-013', 'HTTP Method Validation: PUT on GET-only endpoint', 'medium');
  664 |     await allure.description(
  665 |       '**[KNOWN BUG — BUG-OT-005]**\n\n' +
  666 |       '**What this test does:**\n' +
  667 |       'Sends a PUT request to /api/v1/token (which is a GET-only endpoint) and verifies the ' +
  668 |       'server responds with 405 Method Not Allowed.\n\n' +
  669 |       '**Expected:** HTTP 405 Method Not Allowed with an Allow header listing the valid methods\n' +
  670 |       '**Actual:** HTTP 500 Internal Server Error\n\n' +
  671 |       '**Root Cause:** The service throws an unhandled exception instead of returning a proper ' +
  672 |       '405 response when it receives an unsupported HTTP method.\n\n' +
  673 |       '**Impact:** API clients that probe method availability or send the wrong method receive a ' +
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
> 694 |       expect(res!.status, `[BUG-OT-005] PUT /token must return 405, got ${res!.status}`).toBe(405);
      |                                                                                          ^ Error: [BUG-OT-005] PUT /token must return 405, got 502
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
  774 |       expect(res!.status, `[BUG-OT-007] Unsupported Accept type must return 406, got ${res!.status}`).toBe(406);
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
```