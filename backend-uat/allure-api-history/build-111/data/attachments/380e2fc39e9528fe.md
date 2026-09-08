# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Transaction API — Edge Cases (requires valid TRANSACTION_API_TOKEN) >> [BUG-TXN-003] TC-TXN-003 — Non-existent UUID in transaction detail must return 404 (not 500)
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:1155:7

# Error details

```
Error: Malformed UUID path must return 400/422, got 404

expect(received).toContain(expected) // indexOf

Expected value: 404
Received array: [400, 422]
```

# Test source

```ts
  1094 |     labels('Transaction API', 'TC-TXN-001', 'Negative Page Number Not Rejected', 'medium');
  1095 |     await allure.description(
  1096 |       '**[BUG-TXN-001]**\n\n' +
  1097 |       'Passing `page=-1` to the transaction list endpoint should return 400 Bad Request. ' +
  1098 |       'A negative page index is not meaningful and indicates a client error.\n\n' +
  1099 |       '**Expected:** HTTP 400 with `"message":"page must be >= 0"`\n\n' +
  1100 |       '**Impact:** If the server treats page=-1 as page=0 (common ORM behaviour), results are ' +
  1101 |       'silently incorrect with no indication to the client. If it errors at the DB layer, ' +
  1102 |       'it produces a confusing 500.',
  1103 |     );
  1104 | 
  1105 |     await logger.step('Step 1 — GET list with page=-1', async () => {
  1106 |       logger.info('GET /api/v1/transaction?period=today&page=-1&size=10');
  1107 |       const res = await GET(TXN_BASE, `${LIST}?period=today&page=-1&size=10`, TXN_TOKEN);
  1108 |       logger.pass('HTTP ' + res.status + ' received');
  1109 |       allure.parameter('HTTP Status', String(res.status));
  1110 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 200));
  1111 |       bugAttach('BUG-TXN-001',
  1112 |         '400 Bad Request — page must be >= 0',
  1113 |         `HTTP ${res.status} for page=-1`,
  1114 |         'Negative page silently returns page 0 or causes 500; client cannot detect invalid input',
  1115 |         'Add @Min(0) or equivalent validation on page parameter; return 400 for negative values',
  1116 |       );
  1117 |       logger.info('Asserting: page=-1 returns 400 or 422');
  1118 |       expect([400, 422], `[BUG-TXN-001] page=-1 must return 400, got ${res.status}`).toContain(res.status);
  1119 |       logger.pass('All assertions passed');
  1120 |     });
  1121 |   });
  1122 | 
  1123 |   // ── TC-TXN-002 ────────────────────────────────────────────────────────────
  1124 |   test('[BUG-TXN-002] TC-TXN-002 — Transaction list: size=0 returns 200 with empty data (should be 400)', async ({ logger }) => {
  1125 |     labels('Transaction API', 'TC-TXN-002', 'Zero Page Size Accepted', 'medium');
  1126 |     await allure.description(
  1127 |       '**[BUG-TXN-002]**\n\n' +
  1128 |       'Passing `size=0` to the transaction list returns HTTP 200 with an empty `data` array. ' +
  1129 |       'A page size of zero is meaningless — it returns no records and provides no useful information.\n\n' +
  1130 |       '**Expected:** HTTP 400 with `"message":"size must be between 1 and 100"`\n\n' +
  1131 |       '**Impact:** Clients that compute size dynamically might accidentally send size=0 ' +
  1132 |       '(e.g. when viewport calculation rounds down) and receive a silently empty response ' +
  1133 |       'with no error, causing them to show an empty state incorrectly.',
  1134 |     );
  1135 | 
  1136 |     await logger.step('Step 1 — GET list with size=0', async () => {
  1137 |       logger.info('GET /api/v1/transaction?period=today&page=0&size=0');
  1138 |       const res = await GET(TXN_BASE, `${LIST}?period=today&page=0&size=0`, TXN_TOKEN);
  1139 |       logger.pass('HTTP ' + res.status + ' received');
  1140 |       allure.parameter('HTTP Status', String(res.status));
  1141 |       allure.parameter('data.length', String((res.body?.data ?? []).length));
  1142 |       bugAttach('BUG-TXN-002',
  1143 |         '400 Bad Request — size must be >= 1',
  1144 |         `HTTP ${res.status} with empty data array`,
  1145 |         'Zero-size response is useless; client cannot distinguish "no data" from "bad request"',
  1146 |         'Add @Min(1) validation on size parameter; return 400 for size=0',
  1147 |       );
  1148 |       logger.info('Asserting: size=0 returns 400');
  1149 |       expect(res.status, `[BUG-TXN-002] size=0 must return 400, got ${res.status}`).toBe(400);
  1150 |       logger.pass('All assertions passed');
  1151 |     });
  1152 |   });
  1153 | 
  1154 |   // ── TC-TXN-003 ────────────────────────────────────────────────────────────
  1155 |   test('[BUG-TXN-003] TC-TXN-003 — Non-existent UUID in transaction detail must return 404 (not 500)', async ({ logger }) => {
  1156 |     labels('Transaction API', 'TC-TXN-003', 'Non-Existent UUID Returns Non-404', 'high');
  1157 |     await allure.description(
  1158 |       '**[BUG-TXN-003]**\n\n' +
  1159 |       'Requesting a transaction detail for a UUID that does not exist in the database ' +
  1160 |       'should return 404 Not Found, not 500 Internal Server Error.\n\n' +
  1161 |       '**Test UUID:** `00000000-0000-0000-0000-000000000000` (nil UUID — guaranteed not to exist)\n\n' +
  1162 |       '**Expected:** HTTP 404 with `"message":"Transaction not found"`\n\n' +
  1163 |       '**Root Cause if 500:** The service does not check for null return from the repository ' +
  1164 |       'and calls a method on a null object, causing NullPointerException.\n\n' +
  1165 |       '**Impact:** Clients cannot distinguish "ID does not exist" from "server crashed". ' +
  1166 |       'Monitoring tools will alert on 500s caused by normal client requests.',
  1167 |     );
  1168 | 
  1169 |     await logger.step('Step 1 — GET detail for nil UUID', async () => {
  1170 |       const nilUUID = '00000000-0000-0000-0000-000000000000';
  1171 |       logger.info(`GET /api/v1/transaction/${nilUUID}`);
  1172 |       const res = await GET(TXN_BASE, `${LIST}/${nilUUID}`, TXN_TOKEN);
  1173 |       logger.pass('HTTP ' + res.status + ' received');
  1174 |       allure.parameter('UUID', nilUUID);
  1175 |       allure.parameter('HTTP Status', String(res.status));
  1176 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 200));
  1177 |       bugAttach('BUG-TXN-003',
  1178 |         '404 Not Found — transaction does not exist',
  1179 |         `HTTP ${res.status} for non-existent UUID`,
  1180 |         'Cannot distinguish "not found" from server crash; 500s trigger false outage alerts',
  1181 |         'Add null-check after repository findById; throw ResourceNotFoundException (maps to 404)',
  1182 |       );
  1183 |       logger.info('Asserting: Non-existent UUID returns 404');
  1184 |       expect(res.status, `[BUG-TXN-003] Non-existent UUID must return 404, got ${res.status}`).toBe(404);
  1185 |       logger.pass('All assertions passed');
  1186 |     });
  1187 | 
  1188 |     await logger.step('Step 2 — GET detail for malformed UUID must return 400', async () => {
  1189 |       logger.info('GET /api/v1/transaction/not-a-valid-uuid');
  1190 |       const res = await GET(TXN_BASE, `${LIST}/not-a-valid-uuid`, TXN_TOKEN);
  1191 |       logger.pass('HTTP ' + res.status + ' received');
  1192 |       allure.parameter('Malformed UUID status', String(res.status));
  1193 |       logger.info('Asserting: Malformed UUID returns 400 or 422');
> 1194 |       expect([400, 422], `Malformed UUID path must return 400/422, got ${res.status}`).toContain(res.status);
       |                                                                                        ^ Error: Malformed UUID path must return 400/422, got 404
  1195 |       logger.pass('All assertions passed');
  1196 |     });
  1197 |   });
  1198 | 
  1199 |   // ── TC-TXN-004 ────────────────────────────────────────────────────────────
  1200 |   test('[BUG-TXN-004] TC-TXN-004 — period=custom without from_date/to_date returns 200 (should be 400)', async ({ logger }) => {
  1201 |     labels('Transaction API', 'TC-TXN-004', 'Custom Period Without Dates Returns 200', 'medium');
  1202 |     await allure.description(
  1203 |       '**[BUG-TXN-004]**\n\n' +
  1204 |       'Calling the transaction summary with `period=custom` but omitting `from_date` and ' +
  1205 |       '`to_date` should return 400 Bad Request because the custom period is meaningless ' +
  1206 |       'without a date range.\n\n' +
  1207 |       '**Expected:** HTTP 400 with `"message":"from_date and to_date are required when period=custom"`\n\n' +
  1208 |       '**Impact:** The API silently applies an undefined date range, returning data for an ' +
  1209 |       'unknown period. Clients have no way to know which time window the response covers.',
  1210 |     );
  1211 | 
  1212 |     await logger.step('Step 1 — GET summary with period=custom and no dates', async () => {
  1213 |       logger.info('GET /api/v1/transaction/summary?period=custom');
  1214 |       const res = await GET(TXN_BASE, `${SUMMARY}?period=custom`, TXN_TOKEN);
  1215 |       logger.pass('HTTP ' + res.status + ' received');
  1216 |       allure.parameter('HTTP Status', String(res.status));
  1217 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 200));
  1218 |       bugAttach('BUG-TXN-004',
  1219 |         '400 Bad Request — from_date and to_date required for period=custom',
  1220 |         `HTTP ${res.status} for period=custom without dates`,
  1221 |         'Response covers unknown date range; client cannot know what period the data represents',
  1222 |         'Validate that from_date and to_date are present when period=custom; return 400 otherwise',
  1223 |       );
  1224 |       logger.info('Asserting: period=custom without dates returns 400');
  1225 |       expect(res.status, `[BUG-TXN-004] period=custom without dates must return 400, got ${res.status}`).toBe(400);
  1226 |       logger.pass('All assertions passed');
  1227 |     });
  1228 |   });
  1229 | 
  1230 |   // ── TC-TXN-005 ────────────────────────────────────────────────────────────
  1231 |   test('[BUG-TXN-005] TC-TXN-005 — success_rate.change_pct is always null (no period-over-period tracking)', async ({ logger }) => {
  1232 |     labels('Transaction API', 'TC-TXN-005', 'Success Rate Change Always Null', 'high');
  1233 |     await allure.description(
  1234 |       '**[BUG-TXN-005]**\n\n' +
  1235 |       'The `success_rate.change_pct` field in the transaction summary is always `null` regardless ' +
  1236 |       'of the period requested. This means the dashboard cannot show whether the success rate ' +
  1237 |       'is improving or declining compared to the previous period.\n\n' +
  1238 |       '**Expected:** `change_pct` is a number (positive = improved, negative = declined)\n\n' +
  1239 |       '**Contrast:** `total_volume.change_pct` and `total_volume.change_direction` ARE populated.\n\n' +
  1240 |       '**Root Cause:** The success rate change calculation is not implemented — it likely ' +
  1241 |       'requires comparing the current period\'s rate against the prior period\'s rate, ' +
  1242 |       'which requires two queries.\n\n' +
  1243 |       '**Impact:** The "Success Rate" card on the RMS dashboard never shows a trend arrow, ' +
  1244 |       'leaving operators unable to detect declining payment success rates.',
  1245 |     );
  1246 | 
  1247 |     const periods = ['week', 'today', 'month'];
  1248 |     for (const period of periods) {
  1249 |       await logger.step(`period=${period} — success_rate.change_pct must be a number`, async () => {
  1250 |         logger.info(`GET /api/v1/transaction/summary?period=${period}`);
  1251 |         const res = await GET(TXN_BASE, `${SUMMARY}?period=${period}`, TXN_TOKEN);
  1252 |         logger.pass('HTTP ' + res.status + ' received');
  1253 |         if (res.status !== 200) { test.skip(); return; }
  1254 |         const changeP = res.body?.success_rate?.change_pct;
  1255 |         allure.parameter(`${period} success_rate.change_pct`, String(changeP));
  1256 |         allure.parameter(`${period} change_pct is null`, String(changeP === null));
  1257 |         bugAttach('BUG-TXN-005',
  1258 |           'success_rate.change_pct is a number reflecting period-over-period delta',
  1259 |           `success_rate.change_pct = ${changeP} (always null)`,
  1260 |           'Success rate trend is unavailable; dashboard cannot show improving/declining patterns',
  1261 |           'Implement period-over-period success rate comparison; populate change_pct field',
  1262 |         );
  1263 |         logger.info(`Asserting: success_rate.change_pct is not null and is a number for period=${period}`);
  1264 |         expect(changeP, `[BUG-TXN-005] success_rate.change_pct must not be null for period=${period}`).not.toBeNull();
  1265 |         expect(typeof changeP, 'success_rate.change_pct must be a number').toBe('number');
  1266 |         logger.pass('All assertions passed');
  1267 |       });
  1268 |     }
  1269 |   });
  1270 | 
  1271 |   // ── TC-TXN-006 ────────────────────────────────────────────────────────────
  1272 |   test('[BUG-TXN-006] TC-TXN-006 — total_refunds missing change_direction field', async ({ logger }) => {
  1273 |     labels('Transaction API', 'TC-TXN-006', 'total_refunds Missing change_direction', 'medium');
  1274 |     await allure.description(
  1275 |       '**[BUG-TXN-006]**\n\n' +
  1276 |       'The `total_volume` object has `change_pct` AND `change_direction` fields.\n' +
  1277 |       'The `total_refunds` object has `amount` and `currency` but is missing `change_direction`.\n\n' +
  1278 |       '**Field comparison:**\n' +
  1279 |       '- `total_volume`: `{amount, currency, change_pct, change_direction}` ✓\n' +
  1280 |       '- `total_refunds`: `{amount, currency}` — `change_pct` and `change_direction` MISSING\n\n' +
  1281 |       '**Expected:** `total_refunds` should also include `change_pct` and `change_direction` ' +
  1282 |       'to show whether refunds are increasing or decreasing.\n\n' +
  1283 |       '**Impact:** The "Total Refunds" card on the dashboard cannot show a trend direction. ' +
  1284 |       'An increase in refunds is a key business signal that would otherwise go undetected.',
  1285 |     );
  1286 | 
  1287 |     await logger.step('Step 1 — Fetch summary and check total_refunds fields', async () => {
  1288 |       logger.info('GET /api/v1/transaction/summary?period=week');
  1289 |       const res = await GET(TXN_BASE, `${SUMMARY}?period=week`, TXN_TOKEN);
  1290 |       logger.pass('HTTP ' + res.status + ' received');
  1291 |       if (res.status !== 200) { test.skip(); return; }
  1292 | 
  1293 |       const refunds = res.body?.total_refunds as Record<string, unknown> ?? {};
  1294 |       const volume  = res.body?.total_volume  as Record<string, unknown> ?? {};
```