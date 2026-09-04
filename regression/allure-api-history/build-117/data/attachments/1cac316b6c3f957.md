# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> API Gateway & Security >> [BUG-GW-001] TC-GW-002 — CORS allow-origin must not be wildcard "*" on spr-fetcher routes
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:1407:7

# Error details

```
Error: [BUG-GW-001] CORS allow-origin must not be "*", got: "*"

expect(received).not.toBe(expected) // Object.is equality

Expected: not "*"
```

# Test source

```ts
  1344 |       logger.pass('All assertions passed');
  1345 |     });
  1346 |   });
  1347 | 
  1348 | });
  1349 | 
  1350 | // ═════════════════════════════════════════════════════════════════════════════
  1351 | //  GATEWAY / SECURITY TESTS
  1352 | // ═════════════════════════════════════════════════════════════════════════════
  1353 | 
  1354 | test.describe('API Gateway & Security', () => {
  1355 | 
  1356 |   // ──────────────────────────────────────────────────────────────────────────
  1357 |   // TC-GW-001 — [BUG-GW-001] Wildcard CORS on oauth-token routes
  1358 |   // ──────────────────────────────────────────────────────────────────────────
  1359 |   test('[BUG-GW-001] TC-GW-001 — CORS allow-origin must not be wildcard "*" on oauth-token routes', async ({ logger }) => {
  1360 |     labelsGW('TC-GW-001', 'Security: Wildcard CORS on oauth-token', 'high');
  1361 |     await allure.description(
  1362 |       '**[KNOWN BUG — BUG-GW-001]**\n\n' +
  1363 |       '**What this test does:**\n' +
  1364 |       'Calls GET /oauth-token/api/v1/token and inspects the CORS response headers.\n\n' +
  1365 |       '**Expected CORS headers:**\n' +
  1366 |       '- access-control-allow-origin: https://rms.dev.demo-fsit.com (explicit trusted origin)\n' +
  1367 |       '- access-control-allow-headers: Content-Type, Authorization (explicit list)\n\n' +
  1368 |       '**Actual CORS headers:**\n' +
  1369 |       '- access-control-allow-origin: * (wildcard — any origin)\n' +
  1370 |       '- access-control-allow-headers: * (wildcard — any header)\n' +
  1371 |       '- access-control-allow-methods: GET,POST,PUT,DELETE,PATCH,OPTIONS\n\n' +
  1372 |       '**Why this is dangerous:**\n' +
  1373 |       'Wildcard CORS means any website on the internet can make cross-origin requests to the ' +
  1374 |       'oauth-token service from a user\'s browser. Combined with BUG-OT-002 (no auth ' +
  1375 |       'enforcement), a malicious page can silently obtain the KCB API token on behalf of ' +
  1376 |       'any user who visits it, even if that user has no RMS account.\n\n' +
  1377 |       '**Fix:** Replace the APISIX global CORS plugin wildcard with an explicit allow-list ' +
  1378 |       'containing only the RMS frontend origin.',
  1379 |     );
  1380 |     let res: supertest.Response;
  1381 |     await logger.step('Step 1 — Send GET /oauth-token/api/v1/token and capture CORS headers', async () => {
  1382 |       logger.info('GET /api/v1/token');
  1383 |       res = await GET(OAUTH_BASE, '/api/v1/token', BEARER);
  1384 |       logger.pass('HTTP ' + res.status + ' received');
  1385 |       attach('Response Headers', 'GET', OAUTH_BASE, '/api/v1/token', res);
  1386 |       allure.parameter('access-control-allow-origin',  String(res.headers['access-control-allow-origin']  ?? 'NOT SET'));
  1387 |       allure.parameter('access-control-allow-headers', String(res.headers['access-control-allow-headers'] ?? 'NOT SET'));
  1388 |       allure.parameter('access-control-allow-methods', String(res.headers['access-control-allow-methods'] ?? 'NOT SET'));
  1389 |     });
  1390 |     await logger.step('Step 2 — Assert CORS origin is NOT wildcard (BUG: "*")', async () => {
  1391 |       logger.info('Asserting: access-control-allow-origin is not "*"');
  1392 |       const origin = String(res!.headers['access-control-allow-origin'] ?? '');
  1393 |       bugEvidence('BUG-GW-001',
  1394 |         'access-control-allow-origin: https://rms.dev.demo-fsit.com',
  1395 |         `access-control-allow-origin: ${origin}`,
  1396 |         'Any website can retrieve the KCB API token from a user\'s browser via CORS',
  1397 |         'Configure APISIX CORS plugin with explicit allow-origin matching only the RMS frontend domain',
  1398 |       );
  1399 |       expect(origin, `[BUG-GW-001] CORS allow-origin must not be "*", got: "${origin}"`).not.toBe('*');
  1400 |       logger.pass('All assertions passed');
  1401 |     });
  1402 |   });
  1403 | 
  1404 |   // ──────────────────────────────────────────────────────────────────────────
  1405 |   // TC-GW-002 — [BUG-GW-001] Wildcard CORS on spr-fetcher routes
  1406 |   // ──────────────────────────────────────────────────────────────────────────
  1407 |   test('[BUG-GW-001] TC-GW-002 — CORS allow-origin must not be wildcard "*" on spr-fetcher routes', async ({ logger }) => {
  1408 |     labelsGW('TC-GW-002', 'Security: Wildcard CORS on spr-fetcher', 'high');
  1409 |     await allure.description(
  1410 |       '**[KNOWN BUG — BUG-GW-001]**\n\n' +
  1411 |       '**What this test does:**\n' +
  1412 |       'Calls GET /spr-fetcher/api/v1/fetch/status and inspects CORS response headers.\n\n' +
  1413 |       '**Expected:** access-control-allow-origin is set to a specific trusted origin\n' +
  1414 |       '**Actual:** access-control-allow-origin: * (wildcard)\n\n' +
  1415 |       '**Why this is dangerous on spr-fetcher specifically:**\n' +
  1416 |       'Combined with BUG-SF-001 (no auth enforcement on the status endpoint), the wildcard ' +
  1417 |       'CORS means any website can read the SPR Fetcher status — including the accountNumber ' +
  1418 |       'and apiEndpoint — directly from a user\'s browser without the user knowing.\n\n' +
  1419 |       'This creates a trivial CSRF-style data exfiltration path:\n' +
  1420 |       '  1. User visits a malicious website\n' +
  1421 |       '  2. Page scripts call GET /spr-fetcher/api/v1/fetch/status (no auth needed)\n' +
  1422 |       '  3. CORS allows the response to be read by the malicious page\n' +
  1423 |       '  4. accountNumber and apiEndpoint are silently exfiltrated',
  1424 |     );
  1425 |     let res: supertest.Response;
  1426 |     await logger.step('Step 1 — Send GET /spr-fetcher/api/v1/fetch/status and capture CORS headers', async () => {
  1427 |       logger.info('GET /api/v1/fetch/status');
  1428 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  1429 |       logger.pass('HTTP ' + res.status + ' received');
  1430 |       attach('Response Headers', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  1431 |       allure.parameter('access-control-allow-origin',  String(res.headers['access-control-allow-origin']  ?? 'NOT SET'));
  1432 |       allure.parameter('access-control-allow-headers', String(res.headers['access-control-allow-headers'] ?? 'NOT SET'));
  1433 |       allure.parameter('access-control-allow-methods', String(res.headers['access-control-allow-methods'] ?? 'NOT SET'));
  1434 |     });
  1435 |     await logger.step('Step 2 — Assert CORS origin is NOT wildcard (BUG: "*")', async () => {
  1436 |       logger.info('Asserting: access-control-allow-origin is not "*"');
  1437 |       const origin = String(res!.headers['access-control-allow-origin'] ?? '');
  1438 |       bugEvidence('BUG-GW-001',
  1439 |         'access-control-allow-origin: https://rms.dev.demo-fsit.com',
  1440 |         `access-control-allow-origin: ${origin}`,
  1441 |         'Any website can silently read accountNumber and apiEndpoint via CORS + no-auth status endpoint',
  1442 |         'Restrict APISIX CORS plugin to the specific RMS frontend origin',
  1443 |       );
> 1444 |       expect(origin, `[BUG-GW-001] CORS allow-origin must not be "*", got: "${origin}"`).not.toBe('*');
       |                                                                                              ^ Error: [BUG-GW-001] CORS allow-origin must not be "*", got: "*"
  1445 |       logger.pass('All assertions passed');
  1446 |     });
  1447 |   });
  1448 | 
  1449 |   // ──────────────────────────────────────────────────────────────────────────
  1450 |   // TC-GW-003 — [BUG-GW-002] OPTIONS preflight allows any origin
  1451 |   // ──────────────────────────────────────────────────────────────────────────
  1452 |   test('[BUG-GW-002] TC-GW-003 — OPTIONS preflight must not allow arbitrary origins (e.g. https://evil.com)', async ({ logger }) => {
  1453 |     labelsGW('TC-GW-003', 'Security: CORS Preflight Accepts Any Origin', 'high');
  1454 |     await allure.description(
  1455 |       '**[KNOWN BUG — BUG-GW-002]**\n\n' +
  1456 |       '**What this test does:**\n' +
  1457 |       'Sends an OPTIONS (CORS preflight) request to /spr-fetcher/api/v1/fetch/status with ' +
  1458 |       '"Origin: https://evil.com" to test whether the gateway accepts arbitrary origins.\n\n' +
  1459 |       '**Expected:** The server should NOT reflect "https://evil.com" in the ' +
  1460 |       'access-control-allow-origin header. It should either return the trusted origin or ' +
  1461 |       'omit the header entirely.\n\n' +
  1462 |       '**Actual:** HTTP 200 with access-control-allow-origin: * — meaning "https://evil.com" ' +
  1463 |       'is implicitly allowed (wildcard accepts all origins)\n\n' +
  1464 |       '**Why this matters:**\n' +
  1465 |       'CORS preflights are the browser\'s way of asking "is this origin allowed to make this ' +
  1466 |       'cross-origin request?" By answering "*", the gateway tells the browser to allow ALL ' +
  1467 |       'origins, including malicious ones. This negates CORS as a security mechanism entirely.\n\n' +
  1468 |       '**Fix:** Replace the wildcard CORS plugin with an explicit origin allowlist in APISIX. ' +
  1469 |       'The OPTIONS response should only reflect the requesting origin if it is in the allowlist.',
  1470 |     );
  1471 |     let res: supertest.Response;
  1472 |     await logger.step('Step 1 — Send OPTIONS preflight with Origin: https://evil.com', async () => {
  1473 |       allure.parameter('Origin header sent', 'https://evil.com (malicious origin — intentional)');
  1474 |       logger.info('OPTIONS /api/v1/fetch/status');
  1475 |       res = await supertest(SPR_BASE)
  1476 |         .options('/api/v1/fetch/status')
  1477 |         .set('Origin', 'https://evil.com')
  1478 |         .set('Access-Control-Request-Method', 'GET')
  1479 |         .set('Access-Control-Request-Headers', 'Authorization');
  1480 |       logger.pass('HTTP ' + res.status + ' received');
  1481 |       attach('OPTIONS Preflight Response', 'OPTIONS', SPR_BASE, '/api/v1/fetch/status', res);
  1482 |       allure.parameter('HTTP Status',                 String(res.status));
  1483 |       allure.parameter('access-control-allow-origin', String(res.headers['access-control-allow-origin'] ?? 'NOT SET'));
  1484 |     });
  1485 |     await logger.step('Step 2 — Assert evil.com is NOT allowed (BUG: wildcard allows it)', async () => {
  1486 |       logger.info('Asserting: access-control-allow-origin is not "*" and not "https://evil.com"');
  1487 |       const origin = String(res!.headers['access-control-allow-origin'] ?? '');
  1488 |       bugEvidence('BUG-GW-002',
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
```