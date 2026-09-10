# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Transaction API — Edge Cases (requires valid TRANSACTION_API_TOKEN) >> TC-TXN-007 — Filter options: statuses, methods, txn_types, categories are all non-empty arrays
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:1315:7

# Error details

```
Error: statuses must not be empty

expect(received).toBeGreaterThan(expected)

Expected: > 0
Received:   0
```

# Test source

```ts
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
  1295 | 
  1296 |       allure.parameter('total_volume fields',  Object.keys(volume).join(', '));
  1297 |       allure.parameter('total_refunds fields', Object.keys(refunds).join(', '));
  1298 |       allure.parameter('total_refunds has change_direction', String('change_direction' in refunds));
  1299 |       allure.parameter('total_refunds has change_pct',      String('change_pct' in refunds));
  1300 | 
  1301 |       bugAttach('BUG-TXN-006',
  1302 |         'total_refunds includes {amount, currency, change_pct, change_direction}',
  1303 |         `total_refunds only has: {${Object.keys(refunds).join(', ')}}`,
  1304 |         'Refunds trend direction unavailable; operators cannot detect rising refund rates',
  1305 |         'Add change_pct and change_direction to the total_refunds response object',
  1306 |       );
  1307 |       logger.info('Asserting: total_refunds includes change_direction and change_pct fields');
  1308 |       expect('change_direction' in refunds, '[BUG-TXN-006] total_refunds must include change_direction field').toBe(true);
  1309 |       expect('change_pct' in refunds,       '[BUG-TXN-006] total_refunds must include change_pct field').toBe(true);
  1310 |       logger.pass('All assertions passed');
  1311 |     });
  1312 |   });
  1313 | 
  1314 |   // ── TC-TXN-007 ────────────────────────────────────────────────────────────
  1315 |   test('TC-TXN-007 — Filter options: statuses, methods, txn_types, categories are all non-empty arrays', async ({ logger }) => {
  1316 |     labels('Transaction API', 'TC-TXN-007', 'Filter Options Complete Validation', 'normal');
  1317 |     await allure.description(
  1318 |       'Deep validation of GET /api/v1/transaction/filter-options — verifies all four arrays ' +
  1319 |       'are present, non-empty, and contain the expected known values.',
  1320 |     );
  1321 | 
  1322 |     await logger.step('Step 1 — Fetch filter options', async () => {
  1323 |       logger.info('GET /api/v1/transaction/filter-options');
  1324 |       const res = await GET(TXN_BASE, FILTER_OPT, TXN_TOKEN);
  1325 |       logger.pass('HTTP ' + res.status + ' received');
  1326 |       if (res.status !== 200) { test.skip(); return; }
  1327 | 
  1328 |       const body = res.body as Record<string, unknown[]>;
  1329 |       const sections = ['statuses','methods','txn_types','categories'] as const;
  1330 | 
  1331 |       logger.info('Asserting: All filter option sections are present, arrays, and non-empty');
  1332 |       for (const section of sections) {
  1333 |         const arr = body[section] ?? [];
  1334 |         expect(Array.isArray(arr), `${section} must be an array`).toBe(true);
> 1335 |         expect((arr as unknown[]).length, `${section} must not be empty`).toBeGreaterThan(0);
       |                                                                           ^ Error: statuses must not be empty
  1336 |         allure.parameter(section, JSON.stringify(arr).slice(0, 120));
  1337 |       }
  1338 |       logger.pass('All assertions passed');
  1339 |     });
  1340 | 
  1341 |     await logger.step('Step 2 — Known statuses must include SUCCESS, FAILED, PENDING', async () => {
  1342 |       logger.info('GET /api/v1/transaction/filter-options');
  1343 |       const res = await GET(TXN_BASE, FILTER_OPT, TXN_TOKEN);
  1344 |       logger.pass('HTTP ' + res.status + ' received');
  1345 |       if (res.status !== 200) return;
  1346 |       const statuses = (res.body?.statuses ?? []) as string[];
  1347 |       logger.info('Asserting: Statuses include SUCCESS, FAILED, PENDING');
  1348 |       ['SUCCESS','FAILED','PENDING'].forEach(s =>
  1349 |         expect(statuses, `"${s}" must be in statuses array`).toContain(s));
  1350 |       logger.pass('All assertions passed');
  1351 |     });
  1352 | 
  1353 |     await logger.step('Step 3 — Known methods must include MPESA, CARD, BANK', async () => {
  1354 |       logger.info('GET /api/v1/transaction/filter-options');
  1355 |       const res = await GET(TXN_BASE, FILTER_OPT, TXN_TOKEN);
  1356 |       logger.pass('HTTP ' + res.status + ' received');
  1357 |       if (res.status !== 200) return;
  1358 |       const methods = (res.body?.methods ?? []) as string[];
  1359 |       logger.info('Asserting: Methods include MPESA, CARD, BANK');
  1360 |       ['MPESA','CARD','BANK'].forEach(m =>
  1361 |         expect(methods, `"${m}" must be in methods array`).toContain(m));
  1362 |       logger.pass('All assertions passed');
  1363 |     });
  1364 |   });
  1365 | 
  1366 |   // ── TC-TXN-008 ────────────────────────────────────────────────────────────
  1367 |   test('TC-TXN-008 — Transaction list: display_status casing must be consistent across all records', async ({ logger }) => {
  1368 |     labels('Transaction API', 'TC-TXN-008', 'display_status Case Inconsistency', 'medium');
  1369 |     await allure.description(
  1370 |       '`display_status` in the list uses title-case ("Success", "Failed") while ' +
  1371 |       '`gateway_status` in the detail endpoint uses ALL-CAPS ("SUCCESS", "FAILED").\n\n' +
  1372 |       'This test confirms the inconsistency exists across the list endpoint and validates that ' +
  1373 |       'all `display_status` values follow a single consistent casing convention.',
  1374 |     );
  1375 | 
  1376 |     await logger.step('Step 1 — Fetch list and check display_status casing', async () => {
  1377 |       logger.info('GET /api/v1/transaction?period=month&page=0&size=50');
  1378 |       const res = await GET(TXN_BASE, `${LIST}?period=month&page=0&size=50`, TXN_TOKEN);
  1379 |       logger.pass('HTTP ' + res.status + ' received');
  1380 |       if (res.status !== 200) { test.skip(); return; }
  1381 | 
  1382 |       const records = (res.body?.data ?? []) as Array<{display_status: string}>;
  1383 |       const allUppercase  = records.every(r => r.display_status === r.display_status.toUpperCase());
  1384 |       const allTitleCase  = records.every(r => /^[A-Z][a-z]+/.test(r.display_status));
  1385 |       const uniqueStatuses = [...new Set(records.map(r => r.display_status))];
  1386 | 
  1387 |       allure.parameter('Unique display_status values', uniqueStatuses.join(', '));
  1388 |       allure.parameter('All uppercase',  String(allUppercase));
  1389 |       allure.parameter('All title-case', String(allTitleCase));
  1390 |       allure.parameter('Total records',  String(records.length));
  1391 | 
  1392 |       logger.info('Asserting: display_status values follow a consistent casing convention');
  1393 |       expect(!allUppercase && !allTitleCase,
  1394 |         `[ISSUE] display_status values are inconsistently cased: [${uniqueStatuses.join(', ')}]`).toBe(false);
  1395 | 
  1396 |       allure.attachment('Status values observed', JSON.stringify(uniqueStatuses), 'application/json');
  1397 |       logger.pass('All assertions passed');
  1398 |     });
  1399 |   });
  1400 | 
  1401 | });
  1402 | 
```