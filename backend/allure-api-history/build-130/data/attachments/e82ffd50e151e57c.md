# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> SPR Fetcher Service >> TC-SF-004 — GET /api/v1/fetch/status: apiEndpoint is a valid HTTPS URL
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:909:7

# Error details

```
Error: apiEndpoint must be non-empty

expect(received).toBeTruthy()

Received: undefined
```

# Test source

```ts
  834  |       'means the status response is incomplete and cannot be relied upon for monitoring:\n' +
  835  |       '- service: identifies which microservice responded\n' +
  836  |       '- status: UP/DOWN health indicator\n' +
  837  |       '- mockEnabled: whether the service is using live or mock data\n' +
  838  |       '- tokenInjectionEnabled: whether OAuth token is injected into KCB calls\n' +
  839  |       '- accountNumber: the KCB account being queried\n' +
  840  |       '- apiEndpoint: the upstream KCB API URL in use\n' +
  841  |       '- checkedAt: timestamp of the last status check',
  842  |     );
  843  |     let res: supertest.Response;
  844  |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  845  |       logger.info('GET /api/v1/fetch/status');
  846  |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  847  |       logger.pass('HTTP ' + res.status + ' received');
  848  |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  849  |     });
  850  |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  851  |       logger.info('Asserting: HTTP status is 200');
  852  |       assertOk(res!, 'TC-SF-002');
  853  |       logger.pass('All assertions passed');
  854  |     });
  855  |     await logger.step('Step 3 — Check each required field', async () => {
  856  |       logger.info('Asserting: all 7 required fields are present and non-null');
  857  |       const b = res!.body;
  858  |       const fields = ['service', 'status', 'mockEnabled', 'tokenInjectionEnabled', 'accountNumber', 'apiEndpoint', 'checkedAt'];
  859  |       for (const f of fields) expect(b[f] !== undefined && b[f] !== null, `"${f}" must be present`).toBe(true);
  860  |       fields.forEach(f => allure.parameter(f, String(b[f])));
  861  |       logger.pass('All assertions passed');
  862  |     });
  863  |   });
  864  | 
  865  |   // ──────────────────────────────────────────────────────────────────────────
  866  |   // TC-SF-003 — service = "spr-fetcher-service", status = "UP"
  867  |   // ──────────────────────────────────────────────────────────────────────────
  868  |   test('TC-SF-003 — GET /api/v1/fetch/status: service="spr-fetcher-service" and status="UP"', async ({ logger }) => {
  869  |     labelsSF('TC-SF-003', 'Service Identity and Operational Status', 'normal');
  870  |     await allure.description(
  871  |       '**What this test does:**\n' +
  872  |       'Checks two key identity fields in the status response:\n' +
  873  |       '- service must equal "spr-fetcher-service"\n' +
  874  |       '- status must equal "UP"\n\n' +
  875  |       '**Why it matters:**\n' +
  876  |       'The "service" field confirms the correct microservice responded (not a different service ' +
  877  |       'behind a misconfigured route). The "status" field confirms the service considers itself ' +
  878  |       'healthy. If status is not "UP", no trigger calls should be made.',
  879  |     );
  880  |     let res: supertest.Response;
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
> 934  |       expect(endpoint, 'apiEndpoint must be non-empty').toBeTruthy();
       |                                                         ^ Error: apiEndpoint must be non-empty
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
  981  |       expect(ageSec, 'checkedAt must not be in the future').toBeGreaterThanOrEqual(0);
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
```