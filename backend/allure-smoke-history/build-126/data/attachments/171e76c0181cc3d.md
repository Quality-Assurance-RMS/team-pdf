# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-014 â€” Math Consistency: successful + failed must equal totalAttempts at every index
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:1126:7

# Error details

```
TypeError: Cannot read properties of undefined (reading '0')
```

# Test source

```ts
  1048 |   test('TC-DREVT-013 â€” Array Integrity: all arrays same length, no null/undefined entries', async ({ logger }) => {
  1049 |     smokeLabels('TC-DREVT-013', 'Array Integrity', 'normal');
  1050 | 
  1051 |     let res: supertest.Response;
  1052 | 
  1053 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  1054 |       logger.info(`GET ${ENDPOINT}`);
  1055 |       res = await get();
  1056 |       logger.pass('HTTP ' + res.status + ' received');
  1057 |       attachResponse('TC-DREVT-013', res);
  1058 |     });
  1059 | 
  1060 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  1061 |       logger.info('Asserting: Validate HTTP 200');
  1062 |       expect(res!.status).toBe(200);
  1063 |       logger.pass('All assertions passed');
  1064 |     });
  1065 | 
  1066 |     await logger.step('Step 3 â€” All 4 arrays must have identical length', async () => {
  1067 |       const d = res!.body.data as RevenueTrendData;
  1068 |       const len = d.labels.length;
  1069 | 
  1070 |       allure.parameter('labels.length',        String(d.labels.length));
  1071 |       allure.parameter('totalAttempts.length', String(d.totalAttempts.length));
  1072 |       allure.parameter('successful.length',    String(d.successful.length));
  1073 |       allure.parameter('failed.length',        String(d.failed.length));
  1074 | 
  1075 |       logger.info('Asserting: response structure and values');
  1076 |       expect(d.totalAttempts.length, 'totalAttempts must be same length as labels').toBe(len);
  1077 |       expect(d.successful.length,    'successful must be same length as labels').toBe(len);
  1078 |       expect(d.failed.length,        'failed must be same length as labels').toBe(len);
  1079 |       logger.pass('All assertions passed');
  1080 |     });
  1081 | 
  1082 |     await logger.step('Step 4 â€” No null/undefined/NaN entries in numeric arrays', async () => {
  1083 |       const d = res!.body.data as RevenueTrendData;
  1084 |       const arrays: [string, unknown[]][] = [
  1085 |         ['totalAttempts', d.totalAttempts],
  1086 |         ['successful',    d.successful],
  1087 |         ['failed',        d.failed],
  1088 |       ];
  1089 | 
  1090 |       for (const [name, arr] of arrays) {
  1091 |         arr.forEach((val, i) => {
  1092 |           logger.info('Asserting: response structure and values');
  1093 |           expect(val, `${name}[${i}] must not be null`).not.toBeNull();
  1094 |           expect(val, `${name}[${i}] must not be undefined`).toBeDefined();
  1095 |           expect(Number.isNaN(val), `${name}[${i}] must not be NaN`).toBe(false);
  1096 |           expect(typeof val, `${name}[${i}] must be a number`).toBe('number');
  1097 |           expect(val as number, `${name}[${i}] must be >= 0`).toBeGreaterThanOrEqual(0);
  1098 |         });
  1099 |       }
  1100 | 
  1101 |       d.labels.forEach((lbl, i) => {
  1102 |         expect(typeof lbl, `labels[${i}] must be a string`).toBe('string');
  1103 |         expect((lbl as string).length, `labels[${i}] must not be empty`).toBeGreaterThan(0);
  1104 |       });
  1105 |       logger.pass('All assertions passed');
  1106 |     });
  1107 | 
  1108 |     await logger.step('Step 5 â€” summary must have exactly 3 items with required fields', async () => {
  1109 |       const d = res!.body.data as RevenueTrendData;
  1110 |       logger.info('Asserting: response structure and values');
  1111 |       expect(d.summary.length, 'summary must have exactly 3 items').toBe(3);
  1112 | 
  1113 |       const requiredFields = ['value', 'label', 'change'];
  1114 |       for (const [i, item] of d.summary.entries()) {
  1115 |         for (const field of requiredFields) {
  1116 |           expect(item, `summary[${i}] must have "${field}"`).toHaveProperty(field);
  1117 |         }
  1118 |         expect(typeof item.label, `summary[${i}].label must be a string`).toBe('string');
  1119 |         expect((item.label as string).length, `summary[${i}].label must not be empty`).toBeGreaterThan(0);
  1120 |       }
  1121 |       logger.pass('All assertions passed');
  1122 |     });
  1123 |   });
  1124 | 
  1125 |   // â”€â”€ TC-DREVT-014 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  1126 |   test('TC-DREVT-014 â€” Math Consistency: successful + failed must equal totalAttempts at every index', async ({ logger }) => {
  1127 |     smokeLabels('TC-DREVT-014', 'Math Consistency â€” per-month totals', 'critical');
  1128 | 
  1129 |     let res: supertest.Response;
  1130 | 
  1131 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  1132 |       logger.info(`GET ${ENDPOINT}`);
  1133 |       res = await get();
  1134 |       logger.pass('HTTP ' + res.status + ' received');
  1135 |       attachResponse('TC-DREVT-014', res);
  1136 |     });
  1137 | 
  1138 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  1139 |       logger.info('Asserting: Validate HTTP 200');
  1140 |       expect(res!.status).toBe(200);
  1141 |       logger.pass('All assertions passed');
  1142 |     });
  1143 | 
  1144 |     await logger.step('Step 3 â€” Per-index: successful[i] + failed[i] must equal totalAttempts[i]', async () => {
  1145 |       const d = res!.body.data as RevenueTrendData;
  1146 | 
  1147 |       d.labels.forEach((label, i) => {
> 1148 |         const ta = d.totalAttempts[i];
       |                                   ^ TypeError: Cannot read properties of undefined (reading '0')
  1149 |         const s  = d.successful[i];
  1150 |         const f  = d.failed[i];
  1151 |         const sum = s + f;
  1152 | 
  1153 |         allure.parameter(`[${i}] ${label}: total`, String(ta));
  1154 |         allure.parameter(`[${i}] ${label}: success+failed`, `${s}+${f}=${sum}`);
  1155 | 
  1156 |         if (ta !== sum) {
  1157 |           flagIssue('TC-DREVT-014', 'BUG-REVT-MATH',
  1158 |             `Index ${i} (${label}): totalAttempts=${ta} but successful+failed=${s}+${f}=${sum} â€” mismatch of ${Math.abs(ta - sum)}`,
  1159 |             { index: i, label, totalAttempts: ta, successful: s, failed: f, sum, diff: ta - sum },
  1160 |           );
  1161 |         }
  1162 | 
  1163 |         logger.info('Asserting: response structure and values');
  1164 |         expect(sum, `[${i}] ${label}: successful(${s}) + failed(${f}) must equal totalAttempts(${ta})`).toBe(ta);
  1165 |       });
  1166 |       logger.pass('All assertions passed');
  1167 |     });
  1168 | 
  1169 |     await logger.step('Step 4 â€” Array sums must match summary.points values', async () => {
  1170 |       const d = res!.body.data as RevenueTrendData;
  1171 |       const sumTA = d.totalAttempts.reduce((a, b) => a + b, 0);
  1172 |       const sumS  = d.successful.reduce((a, b) => a + b, 0);
  1173 |       const sumF  = d.failed.reduce((a, b) => a + b, 0);
  1174 | 
  1175 |       allure.parameter('totalAttempts array sum', String(sumTA));
  1176 |       allure.parameter('successful array sum',    String(sumS));
  1177 |       allure.parameter('failed array sum',        String(sumF));
  1178 | 
  1179 |       const ptTA = Number(d.summary[0]?.points ?? NaN);
  1180 |       const ptS  = Number(d.summary[1]?.points ?? NaN);
  1181 |       const ptF  = Number(d.summary[2]?.points ?? NaN);
  1182 | 
  1183 |       allure.parameter('summary[0].points (Total Attempts)', String(ptTA));
  1184 |       allure.parameter('summary[1].points (Successful)',      String(ptS));
  1185 |       allure.parameter('summary[2].points (Failed)',          String(ptF));
  1186 | 
  1187 |       logger.info('Asserting: response structure and values');
  1188 |       expect(sumTA, `totalAttempts array sum (${sumTA}) must match summary[0].points (${ptTA})`).toBe(ptTA);
  1189 |       expect(sumS,  `successful array sum (${sumS}) must match summary[1].points (${ptS})`).toBe(ptS);
  1190 |       expect(sumF,  `failed array sum (${sumF}) must match summary[2].points (${ptF})`).toBe(ptF);
  1191 |       logger.pass('All assertions passed');
  1192 |     });
  1193 | 
  1194 |     await logger.step('Step 5 â€” KES cross-validation: Total âˆ’ Successful must equal Failed KES', async () => {
  1195 |       const d = res!.body.data as RevenueTrendData;
  1196 |       const parseKES = (s: unknown) => Number(String(s).replace('KES ', '').replace(/,/g, ''));
  1197 | 
  1198 |       const totKES  = parseKES(d.summary[0]?.value);
  1199 |       const sucKES  = parseKES(d.summary[1]?.value);
  1200 |       const failKES = parseKES(d.summary[2]?.value);
  1201 |       const diff    = totKES - sucKES;
  1202 | 
  1203 |       allure.parameter('Total KES',          totKES.toLocaleString());
  1204 |       allure.parameter('Successful KES',     sucKES.toLocaleString());
  1205 |       allure.parameter('Failed KES',         failKES.toLocaleString());
  1206 |       allure.parameter('Total âˆ’ Successful', diff.toLocaleString());
  1207 |       allure.parameter('Matches Failed KES', String(diff === failKES));
  1208 | 
  1209 |       if (!isNaN(totKES) && !isNaN(sucKES) && !isNaN(failKES)) {
  1210 |         logger.info('Asserting: KES cross-validation: Total âˆ’ Successful must e');
  1211 |         expect(diff, `Total KES (${totKES}) âˆ’ Successful KES (${sucKES}) must equal Failed KES (${failKES})`).toBe(failKES);
  1212 |       }
  1213 |       logger.pass('All assertions passed');
  1214 |     });
  1215 |   });
  1216 | 
  1217 |   // â”€â”€ TC-DREVT-015 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  1218 |   test('TC-DREVT-015 â€” Response-Time SLA: must respond within 2 000 ms', async ({ logger }) => {
  1219 |     smokeLabels('TC-DREVT-015', 'Response-Time SLA', 'minor');
  1220 | 
  1221 |     const SLA_MS = 2000;
  1222 |     let durationMs = 0;
  1223 |     let res: supertest.Response;
  1224 | 
  1225 |     await logger.step('Step 1 â€” Measure round-trip time', async () => {
  1226 |       const t0 = Date.now();
  1227 |       logger.info(`GET ${ENDPOINT}`);
  1228 |       res = await get();
  1229 |       logger.pass('HTTP ' + res.status + ' received');
  1230 |       durationMs = Date.now() - t0;
  1231 | 
  1232 |       console.log(`[TC-DREVT-015] Response time: ${durationMs}ms  (SLA: ${SLA_MS}ms)`);
  1233 |       allure.parameter('Response Time (ms)', String(durationMs));
  1234 |       allure.parameter('SLA Threshold (ms)', String(SLA_MS));
  1235 |       allure.parameter('SLA Pass',           String(durationMs < SLA_MS));
  1236 |     });
  1237 | 
  1238 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  1239 |       logger.info('Asserting: Validate HTTP 200');
  1240 |       expect(res!.status).toBe(200);
  1241 |       logger.pass('All assertions passed');
  1242 |     });
  1243 | 
  1244 |     await logger.step(`Step 3 â€” Response time must be under ${SLA_MS}ms`, async () => {
  1245 |       if (durationMs >= SLA_MS) {
  1246 |         flagIssue('TC-DREVT-015', 'PERF-REVT-01',
  1247 |           `Response time ${durationMs}ms exceeds ${SLA_MS}ms SLA for a dashboard chart endpoint`,
  1248 |           { actual_ms: durationMs, sla_ms: SLA_MS, endpoint: `GET ${ENDPOINT}` },
```