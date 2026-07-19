# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> SPR Fetcher Service >> TC-SF-002 — GET /api/v1/fetch/status: All 7 required fields are present and non-null
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:827:7

# Error details

```
Error: "mockEnabled" must be present

expect(received).toBe(expected) // Object.is equality

Expected: true
Received: false
```

# Test source

```ts
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
> 859 |       for (const f of fields) expect(b[f] !== undefined && b[f] !== null, `"${f}" must be present`).toBe(true);
      |                                                                                                     ^ Error: "mockEnabled" must be present
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
  875 |       '**Why it matters:**\n' +
  876 |       'The "service" field confirms the correct microservice responded (not a different service ' +
  877 |       'behind a misconfigured route). The "status" field confirms the service considers itself ' +
  878 |       'healthy. If status is not "UP", no trigger calls should be made.',
  879 |     );
  880 |     let res: supertest.Response;
  881 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  882 |       logger.info('GET /api/v1/fetch/status');
  883 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  884 |       logger.pass('HTTP ' + res.status + ' received');
  885 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  886 |     });
  887 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  888 |       logger.info('Asserting: HTTP status is 200');
  889 |       assertOk(res!, 'TC-SF-003');
  890 |       logger.pass('All assertions passed');
  891 |     });
  892 |     await logger.step('Step 3 — Assert service name', async () => {
  893 |       logger.info('Asserting: service field equals "spr-fetcher-service"');
  894 |       expect(res!.body.service, 'service must be "spr-fetcher-service"').toBe('spr-fetcher-service');
  895 |       allure.parameter('service', String(res!.body.service));
  896 |       logger.pass('All assertions passed');
  897 |     });
  898 |     await logger.step('Step 4 — Assert status is UP', async () => {
  899 |       logger.info('Asserting: status field equals "UP"');
  900 |       expect(res!.body.status, 'status must be "UP"').toBe('UP');
  901 |       allure.parameter('status', String(res!.body.status));
  902 |       logger.pass('All assertions passed');
  903 |     });
  904 |   });
  905 | 
  906 |   // ──────────────────────────────────────────────────────────────────────────
  907 |   // TC-SF-004 — apiEndpoint is a valid HTTPS URL
  908 |   // ──────────────────────────────────────────────────────────────────────────
  909 |   test('TC-SF-004 — GET /api/v1/fetch/status: apiEndpoint is a valid HTTPS URL', async ({ logger }) => {
  910 |     labelsSF('TC-SF-004', 'API Endpoint URL Validation', 'normal');
  911 |     await allure.description(
  912 |       '**What this test does:**\n' +
  913 |       'Parses the apiEndpoint field and verifies it is a well-formed HTTPS URL.\n\n' +
  914 |       '**Why it matters:** apiEndpoint is the upstream KCB API URL the spr-fetcher will call ' +
  915 |       'when the trigger fires. An invalid, HTTP-only, or malformed URL means all trigger ' +
  916 |       'calls will fail. HTTPS is required because the request includes a Bearer token — ' +
  917 |       'HTTP would expose that token in transit.',
  918 |     );
  919 |     let res: supertest.Response;
  920 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  921 |       logger.info('GET /api/v1/fetch/status');
  922 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  923 |       logger.pass('HTTP ' + res.status + ' received');
  924 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  925 |     });
  926 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  927 |       logger.info('Asserting: HTTP status is 200');
  928 |       assertOk(res!, 'TC-SF-004');
  929 |       logger.pass('All assertions passed');
  930 |     });
  931 |     await logger.step('Step 3 — Parse and validate apiEndpoint as HTTPS URL', async () => {
  932 |       logger.info('Asserting: apiEndpoint is a valid HTTPS URL');
  933 |       const endpoint = res!.body.apiEndpoint as string;
  934 |       expect(endpoint, 'apiEndpoint must be non-empty').toBeTruthy();
  935 |       let parsed: URL;
  936 |       try { parsed = new URL(endpoint); } catch { throw new Error(`apiEndpoint "${endpoint}" is not a valid URL`); }
  937 |       expect(parsed.protocol, 'apiEndpoint must use HTTPS').toBe('https:');
  938 |       allure.parameter('apiEndpoint', endpoint);
  939 |       allure.parameter('Protocol',    parsed.protocol);
  940 |       allure.parameter('Host',        parsed.host);
  941 |       logger.pass('All assertions passed');
  942 |     });
  943 |   });
  944 | 
  945 |   // ──────────────────────────────────────────────────────────────────────────
  946 |   // TC-SF-005 — checkedAt is recent (< 60s old)
  947 |   // ──────────────────────────────────────────────────────────────────────────
  948 |   test('TC-SF-005 — GET /api/v1/fetch/status: checkedAt is a recent ISO 8601 timestamp (< 60 s old)', async ({ logger }) => {
  949 |     labelsSF('TC-SF-005', 'checkedAt Timestamp Recency', 'normal');
  950 |     await allure.description(
  951 |       '**What this test does:**\n' +
  952 |       'Checks that checkedAt is:\n' +
  953 |       '1. A parseable ISO 8601 datetime\n' +
  954 |       '2. Not in the future\n' +
  955 |       '3. No more than 60 seconds old\n\n' +
  956 |       '**Why it matters:** If checkedAt is stale (older than 60 s), the status response is a ' +
  957 |       'cached snapshot from a previous check, not a live reading. Operators relying on this ' +
  958 |       'endpoint for real-time health decisions would be looking at outdated data.\n\n' +
  959 |       'A freshness window of 60 seconds is reasonable for a service that checks itself on ' +
```