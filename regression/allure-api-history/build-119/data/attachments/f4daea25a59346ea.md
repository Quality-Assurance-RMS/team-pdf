# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> API Gateway & Security >> [BUG-GW-004] TC-GW-005 — Required security response headers must be present on all API routes
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:1546:7

# Error details

```
Error: [BUG-GW-004] X-Content-Type-Options must be "nosniff"

expect(received).toBe(expected) // Object.is equality

Expected: "nosniff"
Received: ""
```

# Test source

```ts
  1489 |         'Preflight denies https://evil.com (no wildcard in allow-origin)',
  1490 |         `access-control-allow-origin: ${origin} (wildcard — implicitly allows evil.com)`,
  1491 |         'Malicious websites can make cross-origin requests to all RMS APIs from any user\'s browser',
  1492 |         'Replace APISIX wildcard CORS with explicit allowlist: [https://rms.dev.demo-fsit.com]',
  1493 |       );
  1494 |       expect(origin, `[BUG-GW-002] Preflight must not allow arbitrary origins, got: "${origin}"`).not.toBe('*');
  1495 |       expect(origin, 'Preflight must not reflect https://evil.com').not.toBe('https://evil.com');
  1496 |       logger.pass('All assertions passed');
  1497 |     });
  1498 |   });
  1499 | 
  1500 |   // ──────────────────────────────────────────────────────────────────────────
  1501 |   // TC-GW-004 — [BUG-GW-003] Server header discloses APISIX version
  1502 |   // ──────────────────────────────────────────────────────────────────────────
  1503 |   test('[BUG-GW-003] TC-GW-004 — Server response header must not disclose APISIX version', async ({ logger }) => {
  1504 |     labelsGW('TC-GW-004', 'Security: Server Version Disclosure', 'medium');
  1505 |     await allure.description(
  1506 |       '**[KNOWN BUG — BUG-GW-003]**\n\n' +
  1507 |       '**What this test does:**\n' +
  1508 |       'Calls GET /spr-fetcher/api/v1/fetch/status and inspects the Server response header.\n\n' +
  1509 |       '**Expected:** Server header is absent or contains a generic value (e.g. "API Gateway")\n' +
  1510 |       '**Actual:** Server: APISIX/3.16.0\n\n' +
  1511 |       '**Why this is a security concern:**\n' +
  1512 |       'Advertising the specific gateway software and version gives attackers a starting point:\n' +
  1513 |       '1. They can look up CVEs specific to APISIX 3.16.0\n' +
  1514 |       '2. They can craft exploit attempts targeting known vulnerabilities in that version\n' +
  1515 |       '3. They can confirm which gateway is in use, helping map the infrastructure\n\n' +
  1516 |       '**This is an OWASP A05:2021 Security Misconfiguration finding.**\n\n' +
  1517 |       '**Fix:** In the APISIX configuration, set the "server_tokens" option to "false" or ' +
  1518 |       'configure a custom Server header value that does not disclose the product or version.',
  1519 |     );
  1520 |     let res: supertest.Response;
  1521 |     await logger.step('Step 1 — Send GET /spr-fetcher/api/v1/fetch/status and inspect Server header', async () => {
  1522 |       logger.info('GET /api/v1/fetch/status');
  1523 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  1524 |       logger.pass('HTTP ' + res.status + ' received');
  1525 |       attach('Response Headers', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  1526 |       allure.parameter('Server header', String(res.headers['server'] ?? 'NOT SET'));
  1527 |     });
  1528 |     await logger.step('Step 2 — Assert Server header does not disclose version (BUG: APISIX/3.16.0)', async () => {
  1529 |       logger.info('Asserting: Server header does not contain a version number');
  1530 |       const server = String(res!.headers['server'] ?? '');
  1531 |       bugEvidence('BUG-GW-003',
  1532 |         'Server header absent or generic (no version)',
  1533 |         `Server: ${server}`,
  1534 |         'OWASP A05: Version-specific CVE exploitation becomes trivial for attackers',
  1535 |         'Set server_tokens: false in APISIX nginx.conf or set a custom Server header',
  1536 |       );
  1537 |       const hasVersionNumber = /\d+\.\d+/.test(server);
  1538 |       expect(hasVersionNumber, `[BUG-GW-003] Server header must not disclose version number, got: "${server}"`).toBe(false);
  1539 |       logger.pass('All assertions passed');
  1540 |     });
  1541 |   });
  1542 | 
  1543 |   // ──────────────────────────────────────────────────────────────────────────
  1544 |   // TC-GW-005 — [BUG-GW-004] Missing security response headers
  1545 |   // ──────────────────────────────────────────────────────────────────────────
  1546 |   test('[BUG-GW-004] TC-GW-005 — Required security response headers must be present on all API routes', async ({ logger }) => {
  1547 |     labelsGW('TC-GW-005', 'Security: Missing HTTP Security Headers', 'high');
  1548 |     await allure.description(
  1549 |       '**[KNOWN BUG — BUG-GW-004]**\n\n' +
  1550 |       '**What this test does:**\n' +
  1551 |       'Checks that the following security headers are present on the spr-fetcher status response:\n\n' +
  1552 |       '| Header | Required Value | Purpose |\n' +
  1553 |       '|--------|---------------|---------|\n' +
  1554 |       '| X-Content-Type-Options | nosniff | Prevents MIME-type sniffing attacks |\n' +
  1555 |       '| X-Frame-Options | DENY or SAMEORIGIN | Prevents clickjacking |\n' +
  1556 |       '| Strict-Transport-Security | max-age >= 31536000 | Enforces HTTPS-only connections |\n' +
  1557 |       '| X-XSS-Protection | 0 (disabled — modern browsers use CSP) | Legacy XSS protection |\n\n' +
  1558 |       '**Actual:** None of these headers are present on any API response.\n\n' +
  1559 |       '**Impact:**\n' +
  1560 |       '- Without X-Content-Type-Options: browsers may interpret a JSON response as executable ' +
  1561 |       'script in some edge cases\n' +
  1562 |       '- Without Strict-Transport-Security: users on the same network can be downgraded to HTTP ' +
  1563 |       'via a man-in-the-middle attack, exposing Bearer tokens in transit\n' +
  1564 |       '- Without X-Frame-Options: API responses could be embedded in hidden iframes to perform ' +
  1565 |       'clickjacking on authenticated users\n\n' +
  1566 |       '**This is an OWASP A05:2021 Security Misconfiguration finding.**\n\n' +
  1567 |       '**Fix:** Add these headers via the APISIX response-rewrite plugin on all routes.',
  1568 |     );
  1569 |     let res: supertest.Response;
  1570 |     await logger.step('Step 1 — Send GET /spr-fetcher/api/v1/fetch/status', async () => {
  1571 |       logger.info('GET /api/v1/fetch/status');
  1572 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  1573 |       logger.pass('HTTP ' + res.status + ' received');
  1574 |       attach('Response Headers', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  1575 |       const h = res.headers;
  1576 |       allure.parameter('X-Content-Type-Options',   String(h['x-content-type-options']   ?? 'NOT PRESENT'));
  1577 |       allure.parameter('X-Frame-Options',           String(h['x-frame-options']           ?? 'NOT PRESENT'));
  1578 |       allure.parameter('Strict-Transport-Security', String(h['strict-transport-security'] ?? 'NOT PRESENT'));
  1579 |     });
  1580 |     await logger.step('Step 2 — Assert X-Content-Type-Options: nosniff (BUG: missing)', async () => {
  1581 |       logger.info('Asserting: X-Content-Type-Options is "nosniff"');
  1582 |       const val = String(res!.headers['x-content-type-options'] ?? '');
  1583 |       bugEvidence('BUG-GW-004',
  1584 |         'X-Content-Type-Options: nosniff',
  1585 |         val ? `X-Content-Type-Options: ${val}` : 'X-Content-Type-Options: NOT PRESENT',
  1586 |         'OWASP A05: Missing security headers enable MIME sniffing and clickjacking attacks',
  1587 |         'Add X-Content-Type-Options, X-Frame-Options, and HSTS via APISIX response-rewrite plugin',
  1588 |       );
> 1589 |       expect(val.toLowerCase(), '[BUG-GW-004] X-Content-Type-Options must be "nosniff"').toBe('nosniff');
       |                                                                                          ^ Error: [BUG-GW-004] X-Content-Type-Options must be "nosniff"
  1590 |       logger.pass('All assertions passed');
  1591 |     });
  1592 |     await logger.step('Step 3 — Assert Strict-Transport-Security is present (BUG: missing)', async () => {
  1593 |       logger.info('Asserting: Strict-Transport-Security header is present and includes max-age');
  1594 |       const hsts = String(res!.headers['strict-transport-security'] ?? '');
  1595 |       expect(hsts, '[BUG-GW-004] Strict-Transport-Security header must be present').toBeTruthy();
  1596 |       expect(hsts, 'HSTS must include max-age').toContain('max-age');
  1597 |       logger.pass('All assertions passed');
  1598 |     });
  1599 |     await logger.step('Step 4 — Assert X-Frame-Options is present (BUG: missing)', async () => {
  1600 |       logger.info('Asserting: X-Frame-Options is DENY or SAMEORIGIN');
  1601 |       const xfo = String(res!.headers['x-frame-options'] ?? '').toUpperCase();
  1602 |       expect(['DENY', 'SAMEORIGIN'], `[BUG-GW-004] X-Frame-Options must be DENY or SAMEORIGIN, got: "${xfo || 'NOT PRESENT'}"`).toContain(xfo);
  1603 |       logger.pass('All assertions passed');
  1604 |     });
  1605 |   });
  1606 | 
  1607 | });
  1608 | 
```