# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> OAuth Token Service >> [BUG-OT-002] TC-OT-010 — GET /api/v1/token: Invalid Bearer token returns 200 (auth NOT enforced)
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:537:7

# Error details

```
Error: [BUG-OT-002] Invalid token must return 401/403, got 200

expect(received).toContain(expected) // indexOf

Expected value: 200
Received array: [401, 403]
```

# Test source

```ts
  472 |     });
  473 |     await logger.step('Step 3 — GET /token → capture Token B', async () => {
  474 |       logger.info('GET /api/v1/token');
  475 |       const r = await GET(OAUTH_BASE, '/api/v1/token', BEARER);
  476 |       logger.pass('HTTP ' + r.status + ' received');
  477 |       assertOk(r, 'TC-OT-008 Token B');
  478 |       tokenB    = String(r.body.access_token ?? '');
  479 |       issuedAtB = String(r.body.issued_at    ?? '');
  480 |       allure.parameter('Token B issued_at', issuedAtB);
  481 |       logger.pass('All assertions passed');
  482 |     });
  483 |     await logger.step('Step 4 — Assert Token B is fresh (TB >= TA)', async () => {
  484 |       logger.info('Asserting: Token B issued_at >= Token A issued_at');
  485 |       const dateA = new Date(issuedAtA).getTime();
  486 |       const dateB = new Date(issuedAtB).getTime();
  487 |       expect(dateB, 'Token B issued_at must be >= Token A issued_at after eviction').toBeGreaterThanOrEqual(dateA);
  488 |       allure.parameter('Token renewed', tokenA !== tokenB ? 'YES — new token issued' : 'SAME token (still cached)');
  489 |       logger.pass('All assertions passed');
  490 |     });
  491 |   });
  492 | 
  493 |   // ──────────────────────────────────────────────────────────────────────────
  494 |   // TC-OT-009 — [BUG-OT-001] No auth → expect 401, actual 504
  495 |   // ──────────────────────────────────────────────────────────────────────────
  496 |   test('[BUG-OT-001] TC-OT-009 — GET /api/v1/token: No Authorization header must return 401 (returns 504)', async ({ logger }) => {
  497 |     labelsOT('TC-OT-009', 'Security: Missing Authorization Header', 'critical');
  498 |     await allure.description(
  499 |       '**[KNOWN BUG — BUG-OT-001]**\n\n' +
  500 |       '**What this test does:**\n' +
  501 |       'Calls GET /api/v1/token without any Authorization header and verifies the server ' +
  502 |       'rejects the request with 401 Unauthorized.\n\n' +
  503 |       '**Expected:** HTTP 401 Unauthorized (fail-fast at the gateway)\n' +
  504 |       '**Actual:** HTTP 504 Gateway Timeout\n\n' +
  505 |       '**Root Cause:** The APISIX gateway does not enforce authentication on this route. ' +
  506 |       'The unauthenticated request is forwarded to the upstream oauth-token service, which ' +
  507 |       'then attempts to contact the KCB authorization server but times out.\n\n' +
  508 |       '**Impact:** Every unauthenticated call ties up an upstream connection and waits ' +
  509 |       'for the full gateway timeout period (60+ seconds), wasting resources and potentially ' +
  510 |       'masking the real error from the caller.',
  511 |     );
  512 |     let res: supertest.Response;
  513 |     await logger.step('Step 1 — Send GET /api/v1/token WITHOUT Authorization header', async () => {
  514 |       allure.parameter('Authorization', 'NOT PROVIDED — intentionally omitted');
  515 |       logger.info('GET /api/v1/token');
  516 |       res = await GET(OAUTH_BASE, '/api/v1/token');
  517 |       logger.pass('HTTP ' + res.status + ' received');
  518 |       attach('Request & Response (no auth)', 'GET', OAUTH_BASE, '/api/v1/token', res, false);
  519 |       allure.parameter('HTTP Status', String(res.status));
  520 |     });
  521 |     await logger.step('Step 2 — Assert 401/403 (BUG: returns 504)', async () => {
  522 |       logger.info('Asserting: status is 401 or 403');
  523 |       bugEvidence('BUG-OT-001',
  524 |         '401 Unauthorized (fail-fast at gateway)',
  525 |         `HTTP ${res!.status} Gateway Timeout`,
  526 |         'Unauthenticated requests consume upstream connections and take 60+ s to fail',
  527 |         'Add JWT auth plugin to APISIX route: /oauth-token/api/v1/token',
  528 |       );
  529 |       expect([401, 403], `[BUG-OT-001] No-auth GET /token must return 401/403, got ${res!.status}`).toContain(res!.status);
  530 |       logger.pass('All assertions passed');
  531 |     });
  532 |   });
  533 | 
  534 |   // ──────────────────────────────────────────────────────────────────────────
  535 |   // TC-OT-010 — [BUG-OT-002] Invalid token → expect 401, actual 200
  536 |   // ──────────────────────────────────────────────────────────────────────────
  537 |   test('[BUG-OT-002] TC-OT-010 — GET /api/v1/token: Invalid Bearer token returns 200 (auth NOT enforced)', async ({ logger }) => {
  538 |     labelsOT('TC-OT-010', 'Security: Invalid Bearer Token Accepted', 'critical');
  539 |     await allure.description(
  540 |       '**[KNOWN BUG — BUG-OT-002] — CRITICAL SECURITY VULNERABILITY**\n\n' +
  541 |       '**What this test does:**\n' +
  542 |       'Sends an intentionally malformed Bearer token ("invalid.token.here") to GET /api/v1/token ' +
  543 |       'and verifies the server rejects it with 401.\n\n' +
  544 |       '**Expected:** HTTP 401 Unauthorized\n' +
  545 |       '**Actual:** HTTP 200 OK — returns a valid KCB API access_token!\n\n' +
  546 |       '**Root Cause:** The oauth-token service does NOT verify the incoming RMS JWT. It accepts ' +
  547 |       'any non-empty string as a Bearer token and returns the KCB API token regardless.\n\n' +
  548 |       '**Impact (Critical):** The KCB OAuth token is obtainable by any caller who sends any ' +
  549 |       'string in the Authorization header. This bypasses the entire authentication layer ' +
  550 |       'and exposes a sensitive third-party token to unauthorized parties.\n\n' +
  551 |       '**Fix:** Validate the incoming RMS Bearer token (signature + expiry + issuer claims) ' +
  552 |       'against the RMS auth public key before returning the KCB token.',
  553 |     );
  554 |     let res: supertest.Response;
  555 |     await logger.step('Step 1 — Send GET /api/v1/token with deliberately malformed Bearer token', async () => {
  556 |       allure.parameter('Authorization', 'Bearer invalid.token.here (malformed — intentional)');
  557 |       logger.info('GET /api/v1/token');
  558 |       res = await GET(OAUTH_BASE, '/api/v1/token', 'invalid.token.here');
  559 |       logger.pass('HTTP ' + res.status + ' received');
  560 |       attach('Request & Response', 'GET', OAUTH_BASE, '/api/v1/token', res);
  561 |       allure.parameter('HTTP Status',         String(res.status));
  562 |       allure.parameter('access_token present', String(!!res.body?.access_token));
  563 |     });
  564 |     await logger.step('Step 2 — Assert 401/403 (BUG: returns 200 + valid KCB token)', async () => {
  565 |       logger.info('Asserting: status is 401 or 403 for invalid token');
  566 |       bugEvidence('BUG-OT-002',
  567 |         '401 Unauthorized',
  568 |         `HTTP ${res!.status} — returns valid access_token for any Bearer string`,
  569 |         'CRITICAL: KCB API token exposed without credential validation',
  570 |         'Validate RMS JWT (signature, expiry, issuer) before returning KCB token',
  571 |       );
> 572 |       expect([401, 403], `[BUG-OT-002] Invalid token must return 401/403, got ${res!.status}`).toContain(res!.status);
      |                                                                                                ^ Error: [BUG-OT-002] Invalid token must return 401/403, got 200
  573 |       logger.pass('All assertions passed');
  574 |     });
  575 |   });
  576 | 
  577 |   // ──────────────────────────────────────────────────────────────────────────
  578 |   // TC-OT-011 — [BUG-OT-003] DELETE /cache no auth → expect 401, actual 204
  579 |   // ──────────────────────────────────────────────────────────────────────────
  580 |   test('[BUG-OT-003] TC-OT-011 — DELETE /token/cache: No auth returns 204 (unauthenticated eviction allowed)', async ({ logger }) => {
  581 |     labelsOT('TC-OT-011', 'Security: Unauthenticated Cache Eviction', 'high');
  582 |     await allure.description(
  583 |       '**[KNOWN BUG — BUG-OT-003]**\n\n' +
  584 |       '**What this test does:**\n' +
  585 |       'Calls DELETE /api/v1/token/cache without any Authorization header and verifies the ' +
  586 |       'server rejects the request with 401.\n\n' +
  587 |       '**Expected:** HTTP 401 Unauthorized\n' +
  588 |       '**Actual:** HTTP 204 No Content — the cache is evicted without any authentication!\n\n' +
  589 |       '**Impact (DoS Risk):** Any unauthenticated attacker can repeatedly call this endpoint ' +
  590 |       'to evict the cached KCB token. Each eviction forces the service to re-authenticate ' +
  591 |       'with the KCB authorization server, which:\n' +
  592 |       '1. Adds latency to every downstream token request\n' +
  593 |       '2. Can trigger KCB rate-limiting if called rapidly\n' +
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
```