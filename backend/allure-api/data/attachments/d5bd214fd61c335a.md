# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> SPR Fetcher Service >> TC-SF-005 — GET /api/v1/fetch/status: checkedAt is a recent ISO 8601 timestamp (< 60 s old)
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:948:7

# Error details

```
Error: checkedAt must not be in the future

expect(received).toBeGreaterThanOrEqual(expected)

Expected: >= 0
Received:    -10799.979
```

# Test source

```ts
  881  |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  882  |       logger.info('GET /api/v1/fetch/status');
  883  |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  884  |       logger.pass('HTTP ' + res.status + ' received');
  885  |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  886  |     });
  887  |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  888  |       logger.info('Asserting: HTTP status is 200');
  889  |       assertOk(res!, 'TC-SF-003');
  890  |       logger.pass('All assertions passed');
  891  |     });
  892  |     await logger.step('Step 3 — Assert service name', async () => {
  893  |       logger.info('Asserting: service field equals "spr-fetcher-service"');
  894  |       expect(res!.body.service, 'service must be "spr-fetcher-service"').toBe('spr-fetcher-service');
  895  |       allure.parameter('service', String(res!.body.service));
  896  |       logger.pass('All assertions passed');
  897  |     });
  898  |     await logger.step('Step 4 — Assert status is UP', async () => {
  899  |       logger.info('Asserting: status field equals "UP"');
  900  |       expect(res!.body.status, 'status must be "UP"').toBe('UP');
  901  |       allure.parameter('status', String(res!.body.status));
  902  |       logger.pass('All assertions passed');
  903  |     });
  904  |   });
  905  | 
  906  |   // ──────────────────────────────────────────────────────────────────────────
  907  |   // TC-SF-004 — apiEndpoint is a valid HTTPS URL
  908  |   // ──────────────────────────────────────────────────────────────────────────
  909  |   test('TC-SF-004 — GET /api/v1/fetch/status: apiEndpoint is a valid HTTPS URL', async ({ logger }) => {
  910  |     labelsSF('TC-SF-004', 'API Endpoint URL Validation', 'normal');
  911  |     await allure.description(
  912  |       '**What this test does:**\n' +
  913  |       'Parses the apiEndpoint field and verifies it is a well-formed HTTPS URL.\n\n' +
  914  |       '**Why it matters:** apiEndpoint is the upstream KCB API URL the spr-fetcher will call ' +
  915  |       'when the trigger fires. An invalid, HTTP-only, or malformed URL means all trigger ' +
  916  |       'calls will fail. HTTPS is required because the request includes a Bearer token — ' +
  917  |       'HTTP would expose that token in transit.',
  918  |     );
  919  |     let res: supertest.Response;
  920  |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  921  |       logger.info('GET /api/v1/fetch/status');
  922  |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  923  |       logger.pass('HTTP ' + res.status + ' received');
  924  |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  925  |     });
  926  |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  927  |       logger.info('Asserting: HTTP status is 200');
  928  |       assertOk(res!, 'TC-SF-004');
  929  |       logger.pass('All assertions passed');
  930  |     });
  931  |     await logger.step('Step 3 — Parse and validate apiEndpoint as HTTPS URL', async () => {
  932  |       logger.info('Asserting: apiEndpoint is a valid HTTPS URL');
  933  |       const endpoint = res!.body.apiEndpoint as string;
  934  |       expect(endpoint, 'apiEndpoint must be non-empty').toBeTruthy();
  935  |       let parsed: URL;
  936  |       try { parsed = new URL(endpoint); } catch { throw new Error(`apiEndpoint "${endpoint}" is not a valid URL`); }
  937  |       expect(parsed.protocol, 'apiEndpoint must use HTTPS').toBe('https:');
  938  |       allure.parameter('apiEndpoint', endpoint);
  939  |       allure.parameter('Protocol',    parsed.protocol);
  940  |       allure.parameter('Host',        parsed.host);
  941  |       logger.pass('All assertions passed');
  942  |     });
  943  |   });
  944  | 
  945  |   // ──────────────────────────────────────────────────────────────────────────
  946  |   // TC-SF-005 — checkedAt is recent (< 60s old)
  947  |   // ──────────────────────────────────────────────────────────────────────────
  948  |   test('TC-SF-005 — GET /api/v1/fetch/status: checkedAt is a recent ISO 8601 timestamp (< 60 s old)', async ({ logger }) => {
  949  |     labelsSF('TC-SF-005', 'checkedAt Timestamp Recency', 'normal');
  950  |     await allure.description(
  951  |       '**What this test does:**\n' +
  952  |       'Checks that checkedAt is:\n' +
  953  |       '1. A parseable ISO 8601 datetime\n' +
  954  |       '2. Not in the future\n' +
  955  |       '3. No more than 60 seconds old\n\n' +
  956  |       '**Why it matters:** If checkedAt is stale (older than 60 s), the status response is a ' +
  957  |       'cached snapshot from a previous check, not a live reading. Operators relying on this ' +
  958  |       'endpoint for real-time health decisions would be looking at outdated data.\n\n' +
  959  |       'A freshness window of 60 seconds is reasonable for a service that checks itself on ' +
  960  |       'every request or on a short poll interval.',
  961  |     );
  962  |     let res: supertest.Response;
  963  |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  964  |       logger.info('GET /api/v1/fetch/status');
  965  |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  966  |       logger.pass('HTTP ' + res.status + ' received');
  967  |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  968  |     });
  969  |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  970  |       logger.info('Asserting: HTTP status is 200');
  971  |       assertOk(res!, 'TC-SF-005');
  972  |       logger.pass('All assertions passed');
  973  |     });
  974  |     await logger.step('Step 3 — Validate checkedAt recency', async () => {
  975  |       logger.info('Asserting: checkedAt is parseable and within the last 60 seconds');
  976  |       const checkedAt = res!.body.checkedAt as string;
  977  |       expect(checkedAt, 'checkedAt must be non-empty').toBeTruthy();
  978  |       const parsed = new Date(checkedAt);
  979  |       expect(isNaN(parsed.getTime()), 'checkedAt must be parseable').toBe(false);
  980  |       const ageSec = (Date.now() - parsed.getTime()) / 1000;
> 981  |       expect(ageSec, 'checkedAt must not be in the future').toBeGreaterThanOrEqual(0);
       |                                                             ^ Error: checkedAt must not be in the future
  982  |       expect(ageSec, 'checkedAt must be within the last 60 seconds').toBeLessThan(60);
  983  |       allure.parameter('checkedAt',     checkedAt);
  984  |       allure.parameter('Age (seconds)', ageSec.toFixed(2));
  985  |       logger.pass('All assertions passed');
  986  |     });
  987  |   });
  988  | 
  989  |   // ──────────────────────────────────────────────────────────────────────────
  990  |   // TC-SF-006 — [BUG-SF-001] No auth → expect 401, actual 200 + sensitive data
  991  |   // ──────────────────────────────────────────────────────────────────────────
  992  |   test('[BUG-SF-001] TC-SF-006 — GET /api/v1/fetch/status: No auth returns 200 (sensitive data exposed without auth)', async ({ logger }) => {
  993  |     labelsSF('TC-SF-006', 'Security: Unauthenticated Status Access', 'critical');
  994  |     await allure.description(
  995  |       '**[KNOWN BUG — BUG-SF-001]**\n\n' +
  996  |       '**What this test does:**\n' +
  997  |       'Calls GET /spr-fetcher/api/v1/fetch/status without any Authorization header and ' +
  998  |       'verifies the server rejects the request with 401.\n\n' +
  999  |       '**Expected:** HTTP 401 Unauthorized\n' +
  1000 |       '**Actual:** HTTP 200 OK — returns the full status object with no auth required\n\n' +
  1001 |       '**Sensitive data exposed without authentication:**\n' +
  1002 |       '- accountNumber: the KCB bank account number used for all financial queries\n' +
  1003 |       '- apiEndpoint: the full URL of the internal KCB upstream API (internal topology)\n' +
  1004 |       '- tokenInjectionEnabled: reveals internal service configuration state\n' +
  1005 |       '- mockEnabled: reveals whether the system is in mock/test mode\n\n' +
  1006 |       '**Impact:** An external attacker with no credentials can enumerate the bank account ' +
  1007 |       'number, the upstream KCB API URL, and internal service flags. This information can ' +
  1008 |       'be used to craft targeted attacks against the KCB API or to understand the ' +
  1009 |       'system architecture for further exploitation.',
  1010 |     );
  1011 |     let res: supertest.Response;
  1012 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status WITHOUT Authorization header', async () => {
  1013 |       allure.parameter('Authorization', 'NOT PROVIDED — intentionally omitted');
  1014 |       logger.info('GET /api/v1/fetch/status');
  1015 |       res = await GET(SPR_BASE, '/api/v1/fetch/status');
  1016 |       logger.pass('HTTP ' + res.status + ' received');
  1017 |       attach('Request & Response (no auth)', 'GET', SPR_BASE, '/api/v1/fetch/status', res, false);
  1018 |       allure.parameter('HTTP Status',              String(res.status));
  1019 |       allure.parameter('accountNumber (exposed)',  String(res.body?.accountNumber ?? 'N/A'));
  1020 |       allure.parameter('apiEndpoint (exposed)',    String(res.body?.apiEndpoint   ?? 'N/A'));
  1021 |     });
  1022 |     await logger.step('Step 2 — Assert 401/403 (BUG: returns 200 with sensitive data)', async () => {
  1023 |       logger.info('Asserting: status is 401 or 403');
  1024 |       bugEvidence('BUG-SF-001',
  1025 |         '401 Unauthorized',
  1026 |         `HTTP ${res!.status} — accountNumber and apiEndpoint visible without auth`,
  1027 |         'External actors can enumerate bank account number and KCB API endpoint without credentials',
  1028 |         'Add JWT auth enforcement to GET /spr-fetcher/api/v1/fetch/status in APISIX',
  1029 |       );
  1030 |       expect([401, 403], `[BUG-SF-001] No-auth status must return 401/403, got ${res!.status}`).toContain(res!.status);
  1031 |       logger.pass('All assertions passed');
  1032 |     });
  1033 |   });
  1034 | 
  1035 |   // ──────────────────────────────────────────────────────────────────────────
  1036 |   // TC-SF-007 — [BUG-SF-002] POST /trigger → expect 200/202, actual failure
  1037 |   // ──────────────────────────────────────────────────────────────────────────
  1038 |   test('[BUG-SF-002] TC-SF-007 — POST /api/v1/fetch/trigger: Must return 200/202 (returns 504 or connection error)', async ({ logger }) => {
  1039 |     labelsSF('TC-SF-007', 'Trigger KCB Data Fetch — Core Functionality', 'critical');
  1040 |     await allure.description(
  1041 |       '**[KNOWN BUG — BUG-SF-002] — CORE FUNCTIONALITY BROKEN**\n\n' +
  1042 |       '**What this test does:**\n' +
  1043 |       'Posts to /spr-fetcher/api/v1/fetch/trigger with a valid Bearer token and verifies ' +
  1044 |       'the response is 200 OK or 202 Accepted with a trigger acknowledgement.\n\n' +
  1045 |       '**Expected:** HTTP 200 or 202\n' +
  1046 |       '**Actual:** HTTP 504 Gateway Timeout or a connection-level failure (no HTTP response at all)\n\n' +
  1047 |       '**Root Cause (confirmed):** The status endpoint reports tokenInjectionEnabled=false ' +
  1048 |       '(see BUG-SF-003). Without token injection, the spr-fetcher calls the KCB API without ' +
  1049 |       'attaching a valid OAuth token. The KCB API either:\n' +
  1050 |       '  a. Hangs on the unauthenticated request, causing the gateway to time out (504), or\n' +
  1051 |       '  b. Resets the TCP connection immediately, causing a connection-level error (status 0)\n\n' +
  1052 |       '**Impact (Critical):** The entire data ingestion pipeline for KCB financial data is ' +
  1053 |       'completely non-functional. No SPR (Statement Print) data can be fetched, which means ' +
  1054 |       'the RMS dashboard will show stale or empty financial data.',
  1055 |     );
  1056 |     let res: supertest.Response | null  = null;
  1057 |     let connErr: Error | null           = null;
  1058 |     await logger.step('Step 1 — Send POST /api/v1/fetch/trigger with valid auth', async () => {
  1059 |       allure.parameter('Endpoint', 'POST /spr-fetcher/api/v1/fetch/trigger');
  1060 |       allure.parameter('Auth',     'Valid Bearer token');
  1061 |       logger.info('POST /api/v1/fetch/trigger');
  1062 |       try {
  1063 |         res = await POST(SPR_BASE, '/api/v1/fetch/trigger', BEARER, {});
  1064 |         logger.pass('HTTP ' + res.status + ' received');
  1065 |         attach('Request & Response', 'POST', SPR_BASE, '/api/v1/fetch/trigger', res);
  1066 |         allure.parameter('HTTP Status', String(res.status));
  1067 |       } catch (err: unknown) {
  1068 |         connErr = err instanceof Error ? err : new Error(String(err));
  1069 |         allure.parameter('Connection Error', connErr.message);
  1070 |         console.error('[POST /fetch/trigger]', connErr.message);
  1071 |       }
  1072 |     });
  1073 |     await logger.step('Step 2 — Assert 200/202 (BUG: connection error or 504)', async () => {
  1074 |       logger.info('Asserting: status is 200 or 202');
  1075 |       bugEvidence('BUG-SF-002',
  1076 |         'HTTP 200 or 202 — trigger acknowledged',
  1077 |         res ? `HTTP ${res.status}` : `Connection error: ${connErr?.message}`,
  1078 |         'Core KCB data ingestion pipeline is completely non-functional',
  1079 |         'Enable tokenInjectionEnabled=true in spr-fetcher config (fixes BUG-SF-003) and re-test',
  1080 |       );
  1081 |       if (connErr) throw new Error(`[BUG-SF-002] POST /fetch/trigger failed at connection level: ${connErr.message}`);
```