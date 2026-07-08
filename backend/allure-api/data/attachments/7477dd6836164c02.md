# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/oauth-token-spr-fetcher-api.spec.ts >> SPR Fetcher Service >> [BUG-SF-004] TC-SF-010 — mockEnabled must be false in a live/UAT environment (currently true)
- Location: tests/API/oauth-token-spr-fetcher-api.spec.ts:1180:7

# Error details

```
Error: [BUG-SF-004] mockEnabled must be false in live/UAT, got: undefined

expect(received).toBe(expected) // Object.is equality

Expected: false
Received: undefined
```

# Test source

```ts
  1121 |       if (connErr) throw new Error(`POST /fetch/trigger no-auth connection error (see BUG-SF-002): ${connErr.message}`);
  1122 |       expect([401, 403], `No-auth trigger must return 401/403, got ${res!.status}`).toContain(res!.status);
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
> 1221 |       expect(mock, `[BUG-SF-004] mockEnabled must be false in live/UAT, got: ${mock}`).toBe(false);
       |                                                                                        ^ Error: [BUG-SF-004] mockEnabled must be false in live/UAT, got: undefined
  1222 |       logger.pass('All assertions passed');
  1223 |     });
  1224 |   });
  1225 | 
  1226 |   // ──────────────────────────────────────────────────────────────────────────
  1227 |   // TC-SF-011 — [BUG-SF-005] POST /status → expect 405, actual 500
  1228 |   // ──────────────────────────────────────────────────────────────────────────
  1229 |   test('[BUG-SF-005] TC-SF-011 — POST /api/v1/fetch/status: Must return 405 Method Not Allowed (returns 500)', async ({ logger }) => {
  1230 |     labelsSF('TC-SF-011', 'HTTP Method Validation: POST on GET-only endpoint', 'medium');
  1231 |     await allure.description(
  1232 |       '**[KNOWN BUG — BUG-SF-005]**\n\n' +
  1233 |       '**What this test does:**\n' +
  1234 |       'Sends a POST request to /api/v1/fetch/status (a GET-only endpoint) and verifies ' +
  1235 |       'the server returns 405 Method Not Allowed.\n\n' +
  1236 |       '**Expected:** HTTP 405 Method Not Allowed\n' +
  1237 |       '**Actual:** HTTP 500 Internal Server Error\n\n' +
  1238 |       '**Root Cause:** The service lacks a global handler for unsupported HTTP methods. ' +
  1239 |       'Instead of returning 405, Spring throws an unhandled MethodNotAllowedException ' +
  1240 |       'which propagates to the default error handler and is returned as a generic 500.\n\n' +
  1241 |       '**Impact:** API clients and automated scanners receive a misleading 500 error. ' +
  1242 |       'The error response body does include a correlationId, which could aid debugging, ' +
  1243 |       'but the wrong status code means automated monitoring treats this as a server crash ' +
  1244 |       'rather than a normal invalid-method response.',
  1245 |     );
  1246 |     let res: supertest.Response;
  1247 |     await logger.step('Step 1 — Send POST /api/v1/fetch/status', async () => {
  1248 |       allure.parameter('Method used', 'POST (unsupported — endpoint is GET only)');
  1249 |       logger.info('POST /api/v1/fetch/status');
  1250 |       res = await POST(SPR_BASE, '/api/v1/fetch/status', BEARER, {});
  1251 |       logger.pass('HTTP ' + res.status + ' received');
  1252 |       attach('Request & Response', 'POST', SPR_BASE, '/api/v1/fetch/status', res);
  1253 |       allure.parameter('HTTP Status',  String(res.status));
  1254 |       allure.parameter('Error detail', String(res.body?.detail ?? 'N/A'));
  1255 |     });
  1256 |     await logger.step('Step 2 — Assert 405 (BUG: returns 500)', async () => {
  1257 |       logger.info('Asserting: HTTP status is 405');
  1258 |       bugEvidence('BUG-SF-005',
  1259 |         '405 Method Not Allowed with Allow: GET header',
  1260 |         `HTTP ${res!.status} Internal Server Error`,
  1261 |         'Incorrect status code misleads clients and monitoring tools',
  1262 |         'Add global MethodNotAllowedException handler in spr-fetcher returning 405 + Allow header',
  1263 |       );
  1264 |       expect(res!.status, `[BUG-SF-005] POST /status must return 405, got ${res!.status}`).toBe(405);
  1265 |       logger.pass('All assertions passed');
  1266 |     });
  1267 |   });
  1268 | 
  1269 |   // ──────────────────────────────────────────────────────────────────────────
  1270 |   // TC-SF-012 — [BUG-SF-006] GET /trigger → expect 405, actual 500
  1271 |   // ──────────────────────────────────────────────────────────────────────────
  1272 |   test('[BUG-SF-006] TC-SF-012 — GET /api/v1/fetch/trigger: Must return 405 Method Not Allowed (returns 500)', async ({ logger }) => {
  1273 |     labelsSF('TC-SF-012', 'HTTP Method Validation: GET on POST-only endpoint', 'medium');
  1274 |     await allure.description(
  1275 |       '**[KNOWN BUG — BUG-SF-006]**\n\n' +
  1276 |       '**What this test does:**\n' +
  1277 |       'Sends a GET request to /api/v1/fetch/trigger (a POST-only endpoint) and verifies ' +
  1278 |       'the server returns 405 Method Not Allowed.\n\n' +
  1279 |       '**Expected:** HTTP 405 Method Not Allowed with Allow: POST header\n' +
  1280 |       '**Actual:** HTTP 500 Internal Server Error\n\n' +
  1281 |       '**Root Cause:** Same as BUG-SF-005 — unhandled MethodNotAllowedException produces 500.\n\n' +
  1282 |       '**Common scenario where this occurs:** A developer calls GET /fetch/trigger expecting ' +
  1283 |       'to see the trigger status or last-run time. Instead they get a confusing 500 with ' +
  1284 |       'no indication that they used the wrong HTTP method.',
  1285 |     );
  1286 |     let res: supertest.Response;
  1287 |     await logger.step('Step 1 — Send GET /api/v1/fetch/trigger', async () => {
  1288 |       allure.parameter('Method used', 'GET (unsupported — endpoint is POST only)');
  1289 |       logger.info('GET /api/v1/fetch/trigger');
  1290 |       res = await GET(SPR_BASE, '/api/v1/fetch/trigger', BEARER);
  1291 |       logger.pass('HTTP ' + res.status + ' received');
  1292 |       attach('Request & Response', 'GET', SPR_BASE, '/api/v1/fetch/trigger', res);
  1293 |       allure.parameter('HTTP Status', String(res.status));
  1294 |     });
  1295 |     await logger.step('Step 2 — Assert 405 (BUG: returns 500)', async () => {
  1296 |       logger.info('Asserting: HTTP status is 405');
  1297 |       bugEvidence('BUG-SF-006',
  1298 |         '405 Method Not Allowed with Allow: POST header',
  1299 |         `HTTP ${res!.status} Internal Server Error`,
  1300 |         'Confusing error; clients cannot detect wrong-method vs real server error',
  1301 |         'Add global MethodNotAllowedException handler in spr-fetcher',
  1302 |       );
  1303 |       expect(res!.status, `[BUG-SF-006] GET /trigger must return 405, got ${res!.status}`).toBe(405);
  1304 |       logger.pass('All assertions passed');
  1305 |     });
  1306 |   });
  1307 | 
  1308 |   // ──────────────────────────────────────────────────────────────────────────
  1309 |   // TC-SF-013 — [BUG-SF-007] Accept: application/xml → expect 406, actual 500
  1310 |   // ──────────────────────────────────────────────────────────────────────────
  1311 |   test('[BUG-SF-007] TC-SF-013 — GET /api/v1/fetch/status with Accept: application/xml returns 500 (must be 406)', async ({ logger }) => {
  1312 |     labelsSF('TC-SF-013', 'Content Negotiation: Unsupported Accept type', 'low');
  1313 |     await allure.description(
  1314 |       '**[KNOWN BUG — BUG-SF-007]**\n\n' +
  1315 |       '**What this test does:**\n' +
  1316 |       'Sends GET /api/v1/fetch/status with "Accept: application/xml" to test content ' +
  1317 |       'negotiation. The service only produces application/json, so it should return 406.\n\n' +
  1318 |       '**Expected:** HTTP 406 Not Acceptable\n' +
  1319 |       '**Actual:** HTTP 500 Internal Server Error\n\n' +
  1320 |       '**Root Cause:** HttpMediaTypeNotAcceptableException is not handled globally, so it ' +
  1321 |       'surfaces as a generic 500 instead of a proper 406 response.\n\n' +
```