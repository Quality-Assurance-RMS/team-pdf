# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Transaction API — Edge Cases (requires valid TRANSACTION_API_TOKEN) >> TC-TXN-007 — Filter options: statuses, methods, txn_types, categories are all non-empty arrays
- Location: tests/API/deep-regression-api.spec.ts:1307:7

# Error details

```
Error: statuses must not be empty

expect(received).toBeGreaterThan(expected)

Expected: > 0
Received:   0
```

# Test source

```ts
  1227 |       'The `success_rate.change_pct` field in the transaction summary is always `null` regardless ' +
  1228 |       'of the period requested. This means the dashboard cannot show whether the success rate ' +
  1229 |       'is improving or declining compared to the previous period.\n\n' +
  1230 |       '**Expected:** `change_pct` is a number (positive = improved, negative = declined)\n\n' +
  1231 |       '**Contrast:** `total_volume.change_pct` and `total_volume.change_direction` ARE populated.\n\n' +
  1232 |       '**Root Cause:** The success rate change calculation is not implemented — it likely ' +
  1233 |       'requires comparing the current period\'s rate against the prior period\'s rate, ' +
  1234 |       'which requires two queries.\n\n' +
  1235 |       '**Impact:** The "Success Rate" card on the RMS dashboard never shows a trend arrow, ' +
  1236 |       'leaving operators unable to detect declining payment success rates.',
  1237 |     );
  1238 | 
  1239 |     const periods = ['week', 'today', 'month'];
  1240 |     for (const period of periods) {
  1241 |       await logger.step(`period=${period} — success_rate.change_pct must be a number`, async () => {
  1242 |         logger.info(`GET /api/v1/transaction/summary?period=${period}`);
  1243 |         const res = await GET(TXN_BASE, `${SUMMARY}?period=${period}`, TXN_TOKEN);
  1244 |         logger.pass('HTTP ' + res.status + ' received');
  1245 |         if (res.status !== 200) { test.skip(); return; }
  1246 |         const changeP = res.body?.success_rate?.change_pct;
  1247 |         allure.parameter(`${period} success_rate.change_pct`, String(changeP));
  1248 |         allure.parameter(`${period} change_pct is null`, String(changeP === null));
  1249 |         bugAttach('BUG-TXN-005',
  1250 |           'success_rate.change_pct is a number reflecting period-over-period delta',
  1251 |           `success_rate.change_pct = ${changeP} (always null)`,
  1252 |           'Success rate trend is unavailable; dashboard cannot show improving/declining patterns',
  1253 |           'Implement period-over-period success rate comparison; populate change_pct field',
  1254 |         );
  1255 |         logger.info(`Asserting: success_rate.change_pct is not null and is a number for period=${period}`);
  1256 |         expect(changeP, `[BUG-TXN-005] success_rate.change_pct must not be null for period=${period}`).not.toBeNull();
  1257 |         expect(typeof changeP, 'success_rate.change_pct must be a number').toBe('number');
  1258 |         logger.pass('All assertions passed');
  1259 |       });
  1260 |     }
  1261 |   });
  1262 | 
  1263 |   // ── TC-TXN-006 ────────────────────────────────────────────────────────────
  1264 |   test('[BUG-TXN-006] TC-TXN-006 — total_refunds missing change_direction field', async ({ logger }) => {
  1265 |     labels('Transaction API', 'TC-TXN-006', 'total_refunds Missing change_direction', 'medium');
  1266 |     await allure.description(
  1267 |       '**[BUG-TXN-006]**\n\n' +
  1268 |       'The `total_volume` object has `change_pct` AND `change_direction` fields.\n' +
  1269 |       'The `total_refunds` object has `amount` and `currency` but is missing `change_direction`.\n\n' +
  1270 |       '**Field comparison:**\n' +
  1271 |       '- `total_volume`: `{amount, currency, change_pct, change_direction}` ✓\n' +
  1272 |       '- `total_refunds`: `{amount, currency}` — `change_pct` and `change_direction` MISSING\n\n' +
  1273 |       '**Expected:** `total_refunds` should also include `change_pct` and `change_direction` ' +
  1274 |       'to show whether refunds are increasing or decreasing.\n\n' +
  1275 |       '**Impact:** The "Total Refunds" card on the dashboard cannot show a trend direction. ' +
  1276 |       'An increase in refunds is a key business signal that would otherwise go undetected.',
  1277 |     );
  1278 | 
  1279 |     await logger.step('Step 1 — Fetch summary and check total_refunds fields', async () => {
  1280 |       logger.info('GET /api/v1/transaction/summary?period=week');
  1281 |       const res = await GET(TXN_BASE, `${SUMMARY}?period=week`, TXN_TOKEN);
  1282 |       logger.pass('HTTP ' + res.status + ' received');
  1283 |       if (res.status !== 200) { test.skip(); return; }
  1284 | 
  1285 |       const refunds = res.body?.total_refunds as Record<string, unknown> ?? {};
  1286 |       const volume  = res.body?.total_volume  as Record<string, unknown> ?? {};
  1287 | 
  1288 |       allure.parameter('total_volume fields',  Object.keys(volume).join(', '));
  1289 |       allure.parameter('total_refunds fields', Object.keys(refunds).join(', '));
  1290 |       allure.parameter('total_refunds has change_direction', String('change_direction' in refunds));
  1291 |       allure.parameter('total_refunds has change_pct',      String('change_pct' in refunds));
  1292 | 
  1293 |       bugAttach('BUG-TXN-006',
  1294 |         'total_refunds includes {amount, currency, change_pct, change_direction}',
  1295 |         `total_refunds only has: {${Object.keys(refunds).join(', ')}}`,
  1296 |         'Refunds trend direction unavailable; operators cannot detect rising refund rates',
  1297 |         'Add change_pct and change_direction to the total_refunds response object',
  1298 |       );
  1299 |       logger.info('Asserting: total_refunds includes change_direction and change_pct fields');
  1300 |       expect('change_direction' in refunds, '[BUG-TXN-006] total_refunds must include change_direction field').toBe(true);
  1301 |       expect('change_pct' in refunds,       '[BUG-TXN-006] total_refunds must include change_pct field').toBe(true);
  1302 |       logger.pass('All assertions passed');
  1303 |     });
  1304 |   });
  1305 | 
  1306 |   // ── TC-TXN-007 ────────────────────────────────────────────────────────────
  1307 |   test('TC-TXN-007 — Filter options: statuses, methods, txn_types, categories are all non-empty arrays', async ({ logger }) => {
  1308 |     labels('Transaction API', 'TC-TXN-007', 'Filter Options Complete Validation', 'normal');
  1309 |     await allure.description(
  1310 |       'Deep validation of GET /api/v1/transaction/filter-options — verifies all four arrays ' +
  1311 |       'are present, non-empty, and contain the expected known values.',
  1312 |     );
  1313 | 
  1314 |     await logger.step('Step 1 — Fetch filter options', async () => {
  1315 |       logger.info('GET /api/v1/transaction/filter-options');
  1316 |       const res = await GET(TXN_BASE, FILTER_OPT, TXN_TOKEN);
  1317 |       logger.pass('HTTP ' + res.status + ' received');
  1318 |       if (res.status !== 200) { test.skip(); return; }
  1319 | 
  1320 |       const body = res.body as Record<string, unknown[]>;
  1321 |       const sections = ['statuses','methods','txn_types','categories'] as const;
  1322 | 
  1323 |       logger.info('Asserting: All filter option sections are present, arrays, and non-empty');
  1324 |       for (const section of sections) {
  1325 |         const arr = body[section] ?? [];
  1326 |         expect(Array.isArray(arr), `${section} must be an array`).toBe(true);
> 1327 |         expect((arr as unknown[]).length, `${section} must not be empty`).toBeGreaterThan(0);
       |                                                                           ^ Error: statuses must not be empty
  1328 |         allure.parameter(section, JSON.stringify(arr).slice(0, 120));
  1329 |       }
  1330 |       logger.pass('All assertions passed');
  1331 |     });
  1332 | 
  1333 |     await logger.step('Step 2 — Known statuses must include SUCCESS, FAILED, PENDING', async () => {
  1334 |       logger.info('GET /api/v1/transaction/filter-options');
  1335 |       const res = await GET(TXN_BASE, FILTER_OPT, TXN_TOKEN);
  1336 |       logger.pass('HTTP ' + res.status + ' received');
  1337 |       if (res.status !== 200) return;
  1338 |       const statuses = (res.body?.statuses ?? []) as string[];
  1339 |       logger.info('Asserting: Statuses include SUCCESS, FAILED, PENDING');
  1340 |       ['SUCCESS','FAILED','PENDING'].forEach(s =>
  1341 |         expect(statuses, `"${s}" must be in statuses array`).toContain(s));
  1342 |       logger.pass('All assertions passed');
  1343 |     });
  1344 | 
  1345 |     await logger.step('Step 3 — Known methods must include MPESA, CARD, BANK', async () => {
  1346 |       logger.info('GET /api/v1/transaction/filter-options');
  1347 |       const res = await GET(TXN_BASE, FILTER_OPT, TXN_TOKEN);
  1348 |       logger.pass('HTTP ' + res.status + ' received');
  1349 |       if (res.status !== 200) return;
  1350 |       const methods = (res.body?.methods ?? []) as string[];
  1351 |       logger.info('Asserting: Methods include MPESA, CARD, BANK');
  1352 |       ['MPESA','CARD','BANK'].forEach(m =>
  1353 |         expect(methods, `"${m}" must be in methods array`).toContain(m));
  1354 |       logger.pass('All assertions passed');
  1355 |     });
  1356 |   });
  1357 | 
  1358 |   // ── TC-TXN-008 ────────────────────────────────────────────────────────────
  1359 |   test('TC-TXN-008 — Transaction list: display_status casing must be consistent across all records', async ({ logger }) => {
  1360 |     labels('Transaction API', 'TC-TXN-008', 'display_status Case Inconsistency', 'medium');
  1361 |     await allure.description(
  1362 |       '`display_status` in the list uses title-case ("Success", "Failed") while ' +
  1363 |       '`gateway_status` in the detail endpoint uses ALL-CAPS ("SUCCESS", "FAILED").\n\n' +
  1364 |       'This test confirms the inconsistency exists across the list endpoint and validates that ' +
  1365 |       'all `display_status` values follow a single consistent casing convention.',
  1366 |     );
  1367 | 
  1368 |     await logger.step('Step 1 — Fetch list and check display_status casing', async () => {
  1369 |       logger.info('GET /api/v1/transaction?period=month&page=0&size=50');
  1370 |       const res = await GET(TXN_BASE, `${LIST}?period=month&page=0&size=50`, TXN_TOKEN);
  1371 |       logger.pass('HTTP ' + res.status + ' received');
  1372 |       if (res.status !== 200) { test.skip(); return; }
  1373 | 
  1374 |       const records = (res.body?.data ?? []) as Array<{display_status: string}>;
  1375 |       const allUppercase  = records.every(r => r.display_status === r.display_status.toUpperCase());
  1376 |       const allTitleCase  = records.every(r => /^[A-Z][a-z]+/.test(r.display_status));
  1377 |       const uniqueStatuses = [...new Set(records.map(r => r.display_status))];
  1378 | 
  1379 |       allure.parameter('Unique display_status values', uniqueStatuses.join(', '));
  1380 |       allure.parameter('All uppercase',  String(allUppercase));
  1381 |       allure.parameter('All title-case', String(allTitleCase));
  1382 |       allure.parameter('Total records',  String(records.length));
  1383 | 
  1384 |       logger.info('Asserting: display_status values follow a consistent casing convention');
  1385 |       expect(!allUppercase && !allTitleCase,
  1386 |         `[ISSUE] display_status values are inconsistently cased: [${uniqueStatuses.join(', ')}]`).toBe(false);
  1387 | 
  1388 |       allure.attachment('Status values observed', JSON.stringify(uniqueStatuses), 'application/json');
  1389 |       logger.pass('All assertions passed');
  1390 |     });
  1391 |   });
  1392 | 
  1393 | });
  1394 | 
```