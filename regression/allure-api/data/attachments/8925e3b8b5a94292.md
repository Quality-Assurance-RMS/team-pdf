# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> SPR Fetcher Service >> TC-SF-008 — POST /api/v1/fetch/trigger: No auth must return 401
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:1090:7

# Error details

```
Error: No-auth trigger must return 401/403, got 500

expect(received).toContain(expected) // indexOf

Expected value: 500
Received array: [401, 403]
```

# Test source

```ts
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
  1082 |       expect([200, 202], `[BUG-SF-002] Trigger must return 200/202, got ${res!.status}`).toContain(res!.status);
  1083 |       logger.pass('All assertions passed');
  1084 |     });
  1085 |   });
  1086 | 
  1087 |   // ──────────────────────────────────────────────────────────────────────────
  1088 |   // TC-SF-008 — POST /trigger no auth → expect 401
  1089 |   // ──────────────────────────────────────────────────────────────────────────
  1090 |   test('TC-SF-008 — POST /api/v1/fetch/trigger: No auth must return 401', async ({ logger }) => {
  1091 |     labelsSF('TC-SF-008', 'Security: Unauthenticated Trigger', 'high');
  1092 |     await allure.description(
  1093 |       '**What this test does:**\n' +
  1094 |       'Posts to /spr-fetcher/api/v1/fetch/trigger without any Authorization header and ' +
  1095 |       'verifies the server responds with 401 or 403.\n\n' +
  1096 |       '**Why it matters:** The trigger endpoint initiates a data fetch from the KCB API. ' +
  1097 |       'If it does not require authentication, any external actor could initiate an unlimited ' +
  1098 |       'number of fetch operations, consuming KCB API quota and causing excessive load on ' +
  1099 |       'both the spr-fetcher service and the KCB API.\n\n' +
  1100 |       '**Note:** Due to BUG-SF-002, the endpoint may also return a connection-level error ' +
  1101 |       'for unauthenticated requests. If that occurs, the test reports the connection error ' +
  1102 |       'and fails, as the auth enforcement cannot be verified.',
  1103 |     );
  1104 |     let res: supertest.Response | null = null;
  1105 |     let connErr: Error | null          = null;
  1106 |     await logger.step('Step 1 — Send POST /api/v1/fetch/trigger WITHOUT Authorization header', async () => {
  1107 |       allure.parameter('Authorization', 'NOT PROVIDED — intentionally omitted');
  1108 |       logger.info('POST /api/v1/fetch/trigger');
  1109 |       try {
  1110 |         res = await POST(SPR_BASE, '/api/v1/fetch/trigger', undefined, {});
  1111 |         logger.pass('HTTP ' + res.status + ' received');
  1112 |         attach('Request & Response (no auth)', 'POST', SPR_BASE, '/api/v1/fetch/trigger', res, false);
  1113 |         allure.parameter('HTTP Status', String(res.status));
  1114 |       } catch (err: unknown) {
  1115 |         connErr = err instanceof Error ? err : new Error(String(err));
  1116 |         allure.parameter('Connection Error', connErr.message);
  1117 |       }
  1118 |     });
  1119 |     await logger.step('Step 2 — Assert 401/403', async () => {
  1120 |       logger.info('Asserting: status is 401 or 403');
  1121 |       if (connErr) throw new Error(`POST /fetch/trigger no-auth connection error (see BUG-SF-002): ${connErr.message}`);
> 1122 |       expect([401, 403], `No-auth trigger must return 401/403, got ${res!.status}`).toContain(res!.status);
       |                                                                                     ^ Error: No-auth trigger must return 401/403, got 500
  1123 |       allure.parameter('Security Assertion', `PASS — HTTP ${res!.status}`);
  1124 |       logger.pass('All assertions passed');
  1125 |     });
  1126 |   });
  1127 | 
  1128 |   // ──────────────────────────────────────────────────────────────────────────
  1129 |   // TC-SF-009 — [BUG-SF-003] tokenInjectionEnabled must be true
  1130 |   // ──────────────────────────────────────────────────────────────────────────
  1131 |   test('[BUG-SF-003] TC-SF-009 — tokenInjectionEnabled must be true (currently false — root cause of trigger failure)', async ({ logger }) => {
  1132 |     labelsSF('TC-SF-009', 'Configuration: OAuth Token Injection', 'high');
  1133 |     await allure.description(
  1134 |       '**[KNOWN BUG — BUG-SF-003]**\n\n' +
  1135 |       '**What this test does:**\n' +
  1136 |       'Reads the tokenInjectionEnabled field from the status response and asserts it is true.\n\n' +
  1137 |       '**Expected:** tokenInjectionEnabled = true\n' +
  1138 |       '**Actual:** tokenInjectionEnabled = false\n\n' +
  1139 |       '**What tokenInjectionEnabled=false means:**\n' +
  1140 |       'The spr-fetcher is configured to call the KCB API WITHOUT first obtaining an OAuth ' +
  1141 |       'token from the oauth-token-service. This means every KCB API call is unauthenticated.\n\n' +
  1142 |       '**Why this is the root cause of BUG-SF-002:**\n' +
  1143 |       'The KCB API at uat.buni.kcbgroup.com requires a valid Bearer token. Without one, ' +
  1144 |       'the request is either rejected immediately or left hanging, causing the 504/connection ' +
  1145 |       'error seen in TC-SF-007.\n\n' +
  1146 |       '**Fix:**\n' +
  1147 |       'Set tokenInjectionEnabled=true in spr-fetcher\'s application.yml and configure the ' +
  1148 |       'oauth-token-service URL so the spr-fetcher can retrieve a KCB token before each fetch.',
  1149 |     );
  1150 |     let res: supertest.Response;
  1151 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  1152 |       logger.info('GET /api/v1/fetch/status');
  1153 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  1154 |       logger.pass('HTTP ' + res.status + ' received');
  1155 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  1156 |     });
  1157 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  1158 |       logger.info('Asserting: HTTP status is 200');
  1159 |       assertOk(res!, 'TC-SF-009');
  1160 |       logger.pass('All assertions passed');
  1161 |     });
  1162 |     await logger.step('Step 3 — Assert tokenInjectionEnabled = true (BUG: false)', async () => {
  1163 |       logger.info('Asserting: tokenInjectionEnabled is true');
  1164 |       const flag = res!.body.tokenInjectionEnabled as boolean;
  1165 |       allure.parameter('tokenInjectionEnabled', String(flag));
  1166 |       bugEvidence('BUG-SF-003',
  1167 |         'tokenInjectionEnabled: true',
  1168 |         `tokenInjectionEnabled: ${flag}`,
  1169 |         'Root cause of BUG-SF-002: spr-fetcher calls KCB without OAuth token, causing auth failure/timeout',
  1170 |         'Set tokenInjectionEnabled=true in spr-fetcher application.yml; configure oauth-token-service URL',
  1171 |       );
  1172 |       expect(flag, `[BUG-SF-003] tokenInjectionEnabled must be true, got: ${flag}`).toBe(true);
  1173 |       logger.pass('All assertions passed');
  1174 |     });
  1175 |   });
  1176 | 
  1177 |   // ──────────────────────────────────────────────────────────────────────────
  1178 |   // TC-SF-010 — [BUG-SF-004] mockEnabled must be false in live/UAT
  1179 |   // ──────────────────────────────────────────────────────────────────────────
  1180 |   test('[BUG-SF-004] TC-SF-010 — mockEnabled must be false in a live/UAT environment (currently true)', async ({ logger }) => {
  1181 |     labelsSF('TC-SF-010', 'Configuration: Mock Mode Enabled in Live Environment', 'high');
  1182 |     await allure.description(
  1183 |       '**[KNOWN BUG — BUG-SF-004]**\n\n' +
  1184 |       '**What this test does:**\n' +
  1185 |       'Reads the mockEnabled field from the status response and asserts it is false.\n\n' +
  1186 |       '**Expected:** mockEnabled = false (service uses real KCB API data)\n' +
  1187 |       '**Actual:** mockEnabled = true (service returns mock/synthetic data)\n\n' +
  1188 |       '**What mockEnabled=true means:**\n' +
  1189 |       'The spr-fetcher is NOT calling the real KCB API. Instead it is returning hardcoded ' +
  1190 |       'or pre-generated mock responses. All financial data processed by the RMS in this ' +
  1191 |       'environment is fake — it does not reflect actual transaction history.\n\n' +
  1192 |       '**Impact:**\n' +
  1193 |       '- In UAT: testers are validating the system against fake data, not real bank data\n' +
  1194 |       '- In production (if deployed): real financial decisions would be made on synthetic data\n' +
  1195 |       '- Dashboards and reports show numbers that have no relation to actual KCB transactions\n\n' +
  1196 |       '**Note:** mockEnabled=true may have been intentional during initial development but ' +
  1197 |       'must be disabled before UAT sign-off or production deployment.',
  1198 |     );
  1199 |     let res: supertest.Response;
  1200 |     await logger.step('Step 1 — Send GET /api/v1/fetch/status', async () => {
  1201 |       logger.info('GET /api/v1/fetch/status');
  1202 |       res = await GET(SPR_BASE, '/api/v1/fetch/status', BEARER);
  1203 |       logger.pass('HTTP ' + res.status + ' received');
  1204 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/status', res);
  1205 |     });
  1206 |     await logger.step('Step 2 — Assert HTTP 200', async () => {
  1207 |       logger.info('Asserting: HTTP status is 200');
  1208 |       assertOk(res!, 'TC-SF-010');
  1209 |       logger.pass('All assertions passed');
  1210 |     });
  1211 |     await logger.step('Step 3 — Assert mockEnabled = false (BUG: currently true)', async () => {
  1212 |       logger.info('Asserting: mockEnabled is false');
  1213 |       const mock = res!.body.mockEnabled as boolean;
  1214 |       allure.parameter('mockEnabled', String(mock));
  1215 |       bugEvidence('BUG-SF-004',
  1216 |         'mockEnabled: false (service uses real KCB API)',
  1217 |         `mockEnabled: ${mock}`,
  1218 |         'All data in this environment is synthetic — UAT validation is testing mock responses, not real bank data',
  1219 |         'Set mockEnabled=false in spr-fetcher application.yml for UAT and production environments',
  1220 |       );
  1221 |       expect(mock, `[BUG-SF-004] mockEnabled must be false in live/UAT, got: ${mock}`).toBe(false);
  1222 |       logger.pass('All assertions passed');
```