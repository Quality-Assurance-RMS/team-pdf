# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-status-currency.spec.ts >> Smoke â€" Dashboard Status & Currency >> TC-DSH-001 â€" Happy path: HTTP 200 with all four data sections present
- Location: tests/API/smoke/smoke-dashboard-status-currency.spec.ts:156:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 503
```

# Test source

```ts
  81  |   console.log(`  â–º peakMetrics present             : ${!!d.peakMetrics}`);
  82  | 
  83  |   if (d.statusDistribution) {
  84  |     for (const s of d.statusDistribution) {
  85  |       console.log(`    statusDistribution: label=${s.label}, percentage=${s.percentage}, amount="${s.amount}" (string âš  DASH-ISSUE-2), count="${s.count}" (string âš  DASH-ISSUE-2), color="${s.color}" âš  DASH-ISSUE-6`);
  86  |     }
  87  |     if (d.statusDistribution.length === 1) {
  88  |       console.warn(`  âš  DASH-ISSUE-7: statusDistribution only contains "${d.statusDistribution[0].label}" â€" FAILED/PENDING entries absent`);
  89  |     }
  90  |   }
  91  | 
  92  |   if (d.currencyDistribution) {
  93  |     for (const c of d.currencyDistribution) {
  94  |       console.log(`    currencyDistribution: label=${c.label}, percentage=${c.percentage}%, amount="${c.amount}" (string âš  DASH-ISSUE-2), count="${c.count}" (string âš  DASH-ISSUE-2)`);
  95  |     }
  96  |   }
  97  | 
  98  |   if (d.byMethod) {
  99  |     for (const m of d.byMethod) {
  100 |       console.log(`    byMethod: method=${m.method}, successKES="${m.successKES}" (string âš  DASH-ISSUE-2), successCount="${m.successCount}", failKES="${m.failKES}", failCount="${m.failCount}"`);
  101 |     }
  102 |   }
  103 | 
  104 |   if (d.peakMetrics) {
  105 |     const pm = d.peakMetrics;
  106 |     console.log(`    peakMetrics.peakTransactionsPerDay : "${pm.peakTransactionsPerDay}" (string âš  DASH-ISSUE-2)`);
  107 |     console.log(`    peakMetrics.peakHourWindow         : "${pm.peakHourWindow}"`);
  108 |     console.log(`    peakMetrics.avgTPM                 : "${pm.avgTPM}" (unit embedded âš  DASH-ISSUE-3)`);
  109 |     console.log(`    peakMetrics.maxTPM                 : "${pm.maxTPM}" (unit embedded âš  DASH-ISSUE-3)`);
  110 |     console.log(`    peakMetrics.peakTxnDayChange       : "${pm.peakTxnDayChange}" (string % âš  DASH-ISSUE-4)`);
  111 |     console.log(`    peakMetrics.peakTxnDayUp           : ${pm.peakTxnDayUp}`);
  112 |     console.log(`    peakMetrics.avgTpmChange           : "${pm.avgTpmChange}" (string % âš  DASH-ISSUE-4)`);
  113 |     console.log(`    peakMetrics.avgTpmUp               : ${pm.avgTpmUp}`);
  114 |     console.log(`    peakMetrics.maxTpmChange           : "${pm.maxTpmChange}" (string % âš  DASH-ISSUE-4)`);
  115 |     console.log(`    peakMetrics.maxTpmUp               : ${pm.maxTpmUp}`);
  116 |   }
  117 | 
  118 |   console.log(`  â–º Full Body:`);
  119 |   console.log(JSON.stringify(res.body, null, 4));
  120 |   console.log(`${'â"€'.repeat(70)}\n`);
  121 | 
  122 |   allure.attachment(
  123 |     `[${tcId}] Request & Response`,
  124 |     JSON.stringify({
  125 |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  126 |       response: { status: res.status, content_type: res.headers['content-type'] ?? 'N/A', body: res.body },
  127 |     }, null, 2),
  128 |     'application/json',
  129 |   );
  130 | }
  131 | 
  132 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  133 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  134 |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  135 |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  136 | }
  137 | 
  138 | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  139 | 
  140 | function smokeLabels(tcId: string, subSuite: string, title: string, severity: 'blocker' | 'critical' | 'normal' = 'critical') {
  141 |   allure.parentSuite('RMS Dashboard API');
  142 |   allure.suite('Smoke');
  143 |   allure.subSuite(subSuite);
  144 |   allure.label('testId',   tcId);
  145 |   allure.label('severity', severity);
  146 |   allure.owner('Ashil Shaji');
  147 |   allure.tags('Smoke', 'API', 'Dashboard', 'StatusCurrency');
  148 |   allure.label('story', title);
  149 | }
  150 | 
  151 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  152 | 
  153 | test.describe('Smoke â€" Dashboard Status & Currency', () => {
  154 | 
  155 |   // â"€â"€ TC-DSH-001 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  156 |   test('TC-DSH-001 â€" Happy path: HTTP 200 with all four data sections present', async ({ logger }) => {
  157 |     smokeLabels('TC-DSH-001', 'Status Currency Endpoint', 'Happy Path â€" Full Response Shape');
  158 | 
  159 |     let res: supertest.Response;
  160 | 
  161 |     await logger.step('Step 1 â€" Build request', async () => {
  162 |       allure.parameter('Base URL',  BASE_URL);
  163 |       allure.parameter('Endpoint',  ENDPOINT);
  164 |       allure.parameter('Method',    'GET');
  165 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  166 |       logger.info('Asserting: request parameters are recorded');
  167 |       logger.pass('All assertions passed');
  168 |     });
  169 | 
  170 |     await logger.step('Step 2 â€" Send GET /status-currency and log response', async () => {
  171 |       logger.info('GET /api/v1/dashboard/status-currency');
  172 |       res = await get();
  173 |       logger.pass('HTTP ' + res.status + ' received');
  174 |       logResponse('TC-DSH-001', {}, res);
  175 |       allure.parameter('HTTP Status',  String(res.status));
  176 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  177 |     });
  178 | 
  179 |     await logger.step('Step 3 â€" Validate HTTP 200 and Content-Type', async () => {
  180 |       logger.info('Asserting: HTTP 200, body defined, Content-Type is application/json');
> 181 |       expect(res!.status,  'HTTP 200 expected').toBe(200);
      |                                                 ^ Error: HTTP 200 expected
  182 |       expect(res!.body,    'Response body must be defined').toBeDefined();
  183 |       expect(res!.headers['content-type'], 'Content-Type must include application/json')
  184 |         .toMatch(/application\/json/);
  185 |       logger.pass('All assertions passed');
  186 |     });
  187 | 
  188 |     await logger.step('Step 4 â€" Validate top-level `data` wrapper and all four sections', async () => {
  189 |       // â"€â"€ DASH-ISSUE-5: other APIs don't use a `data` wrapper â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  190 |       const hasDataWrapper = 'data' in res!.body;
  191 |       if (hasDataWrapper) {
  192 |         flagIssue('TC-DSH-001', 'DASH-ISSUE-5',
  193 |           'Response is wrapped in a "data" key â€" other APIs in this project (transaction-api, summary) return top-level objects directly, causing client inconsistency',
  194 |           Object.keys(res!.body),
  195 |         );
  196 |       }
  197 | 
  198 |       logger.info('Asserting: all four sections (statusDistribution, currencyDistribution, byMethod, peakMetrics) are present');
  199 |       expect.soft(Object.keys(res!.body).length,
  200 |         '[DASH-ISSUE-5] Response should NOT have a "data" wrapper â€" other APIs return top-level objects',
  201 |       ).toBeGreaterThan(1);
  202 | 
  203 |       const d = res!.body?.data ?? res!.body;
  204 |       expect(d, 'statusDistribution section must be present').toHaveProperty('statusDistribution');
  205 |       expect(d, 'currencyDistribution section must be present').toHaveProperty('currencyDistribution');
  206 |       expect(d, 'byMethod section must be present').toHaveProperty('byMethod');
  207 |       expect(d, 'peakMetrics section must be present').toHaveProperty('peakMetrics');
  208 | 
  209 |       allure.parameter('data wrapper present',     String(hasDataWrapper) + (hasDataWrapper ? ' âš  DASH-ISSUE-5' : ''));
  210 |       allure.parameter('statusDistribution count', String((d.statusDistribution as unknown[])?.length ?? 0));
  211 |       allure.parameter('currencyDistribution count', String((d.currencyDistribution as unknown[])?.length ?? 0));
  212 |       allure.parameter('byMethod count',           String((d.byMethod as unknown[])?.length ?? 0));
  213 |       allure.parameter('peakMetrics present',      String(!!d.peakMetrics));
  214 |       logger.pass('All assertions passed');
  215 |     });
  216 |   });
  217 | 
  218 |   // â"€â"€ TC-DSH-002 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  219 |   test('TC-DSH-002 â€" Auth guard: no token and invalid token both return 401', async ({ logger }) => {
  220 |     smokeLabels('TC-DSH-002', 'Auth Guard', 'Auth Guard â€" Unauthorized Access', 'blocker');
  221 | 
  222 |     let resNoAuth: supertest.Response;
  223 |     let resInvalidAuth: supertest.Response;
  224 | 
  225 |     await logger.step('Step 1 â€" Build requests', async () => {
  226 |       allure.parameter('Base URL',   BASE_URL);
  227 |       allure.parameter('Endpoint',   ENDPOINT);
  228 |       allure.parameter('Method',     'GET');
  229 |       allure.parameter('Scenario A', 'No Authorization header');
  230 |       allure.parameter('Scenario B', 'Invalid Bearer token');
  231 |       logger.info('Asserting: request parameters are recorded');
  232 |       logger.pass('All assertions passed');
  233 |     });
  234 | 
  235 |     await logger.step('Step 2 â€" Send GET with no Authorization header', async () => {
  236 |       logger.info('GET /api/v1/dashboard/status-currency (no Authorization header)');
  237 |       resNoAuth = await getNoAuth();
  238 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  239 |       console.log(`\n[TC-DSH-002] No-auth response: status=${resNoAuth.status}, body=${JSON.stringify(resNoAuth.body)}`);
  240 |       allure.attachment('[TC-DSH-002-A] No-Auth Response',
  241 |         JSON.stringify({ status: resNoAuth.status, body: resNoAuth.body }, null, 2),
  242 |         'application/json',
  243 |       );
  244 |     });
  245 | 
  246 |     await logger.step('Step 3 â€" Validate no-auth returns 401 with error body', async () => {
  247 |       logger.info('Asserting: no-auth returns 401 with a non-empty error message');
  248 |       expect(resNoAuth!.status, 'No-auth request must return 401').toBe(401);
  249 |       expect(resNoAuth!.body,   'Error body must be present').toBeDefined();
  250 |       expect(resNoAuth!.body.status ?? resNoAuth!.body.statusCode,
  251 |         'Error body must include status 401').toBe(401);
  252 |       const errMsg = String(resNoAuth!.body.message ?? resNoAuth!.body.error ?? '').toLowerCase();
  253 |       expect(errMsg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  254 |       allure.parameter('No-auth HTTP status', String(resNoAuth!.status));
  255 |       allure.parameter('No-auth error msg',   String(resNoAuth!.body.message ?? resNoAuth!.body.error));
  256 |       logger.pass('All assertions passed');
  257 |     });
  258 | 
  259 |     await logger.step('Step 4 â€" Send GET with invalid Bearer token', async () => {
  260 |       logger.info('GET /api/v1/dashboard/status-currency (invalid Bearer token)');
  261 |       resInvalidAuth = await getInvalidAuth();
  262 |       logger.pass('HTTP ' + resInvalidAuth.status + ' received');
  263 |       console.log(`\n[TC-DSH-002] Invalid-auth response: status=${resInvalidAuth.status}, body=${JSON.stringify(resInvalidAuth.body)}`);
  264 |       allure.attachment('[TC-DSH-002-B] Invalid-Auth Response',
  265 |         JSON.stringify({ status: resInvalidAuth.status, body: resInvalidAuth.body }, null, 2),
  266 |         'application/json',
  267 |       );
  268 |     });
  269 | 
  270 |     await logger.step('Step 5 â€" Validate invalid-token returns 401 with error body', async () => {
  271 |       logger.info('Asserting: invalid-token returns 401 with a non-empty error message');
  272 |       expect(resInvalidAuth!.status, 'Invalid-token request must return 401').toBe(401);
  273 |       expect(resInvalidAuth!.body,   'Error body must be present for invalid token').toBeDefined();
  274 |       const errMsg = String(resInvalidAuth!.body.message ?? resInvalidAuth!.body.error ?? '').toLowerCase();
  275 |       expect(errMsg.length, 'Error message must be non-empty for invalid token').toBeGreaterThan(0);
  276 |       allure.parameter('Invalid-token HTTP status', String(resInvalidAuth!.status));
  277 |       allure.parameter('Invalid-token error msg',   String(resInvalidAuth!.body.message ?? resInvalidAuth!.body.error));
  278 |       logger.pass('All assertions passed');
  279 |     });
  280 |   });
  281 | 
```