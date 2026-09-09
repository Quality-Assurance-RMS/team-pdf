# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-analytics-dashboard-summary.spec.ts >> Backend (Konza) – Analytics Dashboard Summary >> TC-ASUM-001 – Happy Path: 200 + mcda_id + today object with all expected fields
- Location: tests/API/backend-tests/backend-analytics-dashboard-summary.spec.ts:172:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  95  | 
  96  | // ── Logger ──────────────────────────────────────────────────────────────────
  97  | 
  98  | function logResponse(tcId: string, res: supertest.Response, note = '') {
  99  |   const b = res.body as AnalyticsSummaryBody;
  100 |   const t = b?.today;
  101 | 
  102 |   console.log(`\n${'─'.repeat(70)}`);
  103 |   console.log(`[${tcId}]${note ? ' ' + note : ''} GET ${ANALYTICS_BASE}${ENDPOINT}`);
  104 |   console.log(`  ► HTTP Status             : ${res.status}`);
  105 |   console.log(`  ► Content-Type            : ${res.headers['content-type'] ?? 'N/A'}`);
  106 |   console.log(`  ► Has "data" wrapper       : ${'data' in (b ?? {})}  ← BUG-ASUM-07 if false`);
  107 |   if (b) {
  108 |     console.log(`  ► mcda_id                 : ${b.mcda_id}`);
  109 |   }
  110 |   if (t) {
  111 |     console.log(`  ► total_transactions      : ${t.total_transactions}`);
  112 |     console.log(`  ► successful_transactions : ${t.successful_transactions}`);
  113 |     console.log(`  ► failed_transactions     : ${t.failed_transactions}`);
  114 |     const unaccounted = t.total_transactions - (t.successful_transactions + t.failed_transactions);
  115 |     if (unaccounted !== 0) {
  116 |       console.warn(`  ⚠ BUG-ASUM-02: ${unaccounted} transactions unclassified (not success + failed)`);
  117 |     }
  118 |     console.log(`  ► success_rate_pct        : ${t.success_rate_pct}`);
  119 |     console.log(`  ► total_amount_kes        : ${t.total_amount_kes}`);
  120 |     if (t.total_transactions > 0 && t.total_amount_kes === 0) {
  121 |       console.warn(`  ⚠ BUG-ASUM-03: total_amount_kes=0 with ${t.total_transactions} transactions`);
  122 |     }
  123 |     console.log(`  ► settlement_total_kes    : ${t.settlement_total_kes}`);
  124 |     console.log(`  ► recon_matched           : ${t.recon_matched}`);
  125 |     console.log(`  ► recon_exceptions        : ${t.recon_exceptions}`);
  126 |     const reconCoverage = t.recon_matched + t.recon_exceptions;
  127 |     if (reconCoverage === 0 && t.total_transactions > 0) {
  128 |       console.warn(`  ⚠ BUG-ASUM-04: 0 recon records for ${t.total_transactions} transactions`);
  129 |     }
  130 |   }
  131 |   console.log(`  ► Cache-Control           : ${res.headers['cache-control'] ?? 'MISSING'}`);
  132 |   console.log(`  ► ACAO                    : ${res.headers['access-control-allow-origin'] ?? 'MISSING'}`);
  133 |   console.log(`  ► ACAM                    : ${res.headers['access-control-allow-methods'] ?? 'MISSING'}`);
  134 |   console.log(`${'─'.repeat(70)}\n`);
  135 | }
  136 | 
  137 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  138 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  139 |   console.warn(`⚠ ISSUE FLAGGED – ${msg}`);
  140 |   allure.attachment(`⚠ ${issueId}`, msg, 'text/plain');
  141 | }
  142 | 
  143 | function attachResponse(tcId: string, res: supertest.Response, extra: Record<string, unknown> = {}) {
  144 |   allure.attachment(
  145 |     `[${tcId}] Request & Response`,
  146 |     JSON.stringify({
  147 |       request:  { method: 'GET', url: `${ANALYTICS_BASE}${ENDPOINT}`, headers: { Authorization: 'Bearer ***redacted***' } },
  148 |       response: { status: res.status, headers: res.headers, body: res.body, ...extra },
  149 |     }, null, 2),
  150 |     'application/json',
  151 |   );
  152 | }
  153 | 
  154 | function backendLabels(tcId: string, title: string, severity: 'blocker' | 'critical' | 'normal' | 'minor' = 'critical') {
  155 |   allure.parentSuite('RMS Analytics API');
  156 |   allure.suite('Backend');
  157 |   allure.subSuite('Dashboard Summary Endpoint');
  158 |   allure.label('testId',   tcId);
  159 |   allure.label('severity', severity);
  160 |   allure.owner('Ashil Shaji');
  161 |   allure.tags('Backend', 'Konza', 'API', 'Analytics', 'DashboardSummary');
  162 |   allure.label('story', title);
  163 | }
  164 | 
  165 | // ══════════════════════════════════════════════════════════════════════════════
  166 | // Suite
  167 | // ══════════════════════════════════════════════════════════════════════════════
  168 | 
  169 | test.describe('Backend (Konza) – Analytics Dashboard Summary', () => {
  170 | 
  171 |   // ── TC-ASUM-001 ─────────────────────────────────────────────────────────────
  172 |   test('TC-ASUM-001 – Happy Path: 200 + mcda_id + today object with all expected fields', async ({ logger }) => {
  173 |     backendLabels('TC-ASUM-001', 'Happy Path – Analytics Dashboard Summary');
  174 |     let res: supertest.Response;
  175 | 
  176 |     await logger.step('Step 1 – Build request', async () => {
  177 |       allure.parameter('Base URL',  ANALYTICS_BASE);
  178 |       allure.parameter('Endpoint',  ENDPOINT);
  179 |       allure.parameter('Method',    'GET');
  180 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  181 |     });
  182 | 
  183 |     await logger.step('Step 2 – Send GET request and log response', async () => {
  184 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  185 |       res = await get();
  186 |       logger.pass('HTTP ' + res.status + ' received');
  187 |       logResponse('TC-ASUM-001', res);
  188 |       attachResponse('TC-ASUM-001', res);
  189 |       allure.parameter('HTTP Status',  String(res.status));
  190 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  191 |     });
  192 | 
  193 |     await logger.step('Step 3 – Validate HTTP 200 and Content-Type', async () => {
  194 |       logger.info('Asserting: HTTP 200, body defined, Content-Type application/json');
> 195 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                ^ Error: HTTP 200 expected
  196 |       expect(res!.body,   'Body must be defined').toBeDefined();
  197 |       expect(res!.headers['content-type'], 'Content-Type must be application/json').toContain('application/json');
  198 |       logger.pass('All assertions passed');
  199 |     });
  200 | 
  201 |     await logger.step('Step 4 – Validate top-level fields: mcda_id and today', async () => {
  202 |       logger.info('Asserting: mcda_id present, today present, types correct');
  203 |       expect(res!.body, 'Response must have "mcda_id" field').toHaveProperty('mcda_id');
  204 |       expect(res!.body, 'Response must have "today" field').toHaveProperty('today');
  205 |       expect(typeof res!.body.mcda_id, '"mcda_id" must be a string').toBe('string');
  206 |       expect(res!.body.today, '"today" must be an object').toBeInstanceOf(Object);
  207 |       allure.parameter('mcda_id', String(res!.body.mcda_id));
  208 |       logger.pass('All assertions passed');
  209 |     });
  210 | 
  211 |     await logger.step('Step 5 – Validate all 8 required fields in today object', async () => {
  212 |       const t = res!.body.today as TodayStats;
  213 |       const requiredFields = [
  214 |         'total_transactions', 'success_rate_pct', 'total_amount_kes',
  215 |         'successful_transactions', 'settlement_total_kes', 'failed_transactions',
  216 |         'recon_exceptions', 'recon_matched',
  217 |       ];
  218 |       logger.info('Asserting: all 8 required today.* fields present');
  219 |       for (const field of requiredFields) {
  220 |         expect(t, `today.${field} must be present`).toHaveProperty(field);
  221 |         allure.parameter(`today.${field}`, String((t as unknown as Record<string,unknown>)[field]));
  222 |       }
  223 |       logger.pass('All assertions passed');
  224 |     });
  225 |   });
  226 | 
  227 |   // ── TC-ASUM-002 ─────────────────────────────────────────────────────────────
  228 |   test('TC-ASUM-002 – BUG-ASUM-01 (BLOCKER): Complete auth bypass – endpoint is publicly accessible', async ({ logger }) => {
  229 |     backendLabels('TC-ASUM-002', 'BUG-ASUM-01 – Complete Authentication Bypass', 'blocker');
  230 | 
  231 |     const authBypassScenarios: Array<{ label: string; req: () => supertest.Test }> = [
  232 |       { label: 'No Authorization header',         req: () => getNoAuth() },
  233 |       { label: 'Invalid Bearer token',             req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer invalidtoken.bad.sig').set('Accept', 'application/json') },
  234 |       { label: 'Bearer empty string',              req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer ').set('Accept', 'application/json') },
  235 |       { label: 'Bearer null literal',              req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer null').set('Accept', 'application/json') },
  236 |       { label: 'Wrong scheme (Basic)',             req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Basic dXNlcjpwYXNz').set('Accept', 'application/json') },
  237 |       { label: 'Clearly expired token',            req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MDAwMDAwMDB9.abc123').set('Accept', 'application/json') },
  238 |     ];
  239 | 
  240 |     let bypassCount = 0;
  241 | 
  242 |     for (const scenario of authBypassScenarios) {
  243 |       await logger.step(`Auth bypass: "${scenario.label}"`, async () => {
  244 |         logger.info(`GET /api/v1/analytics/dashboard/summary (${scenario.label})`);
  245 |         const res = await scenario.req();
  246 |         logger.pass('HTTP ' + res.status + ' received');
  247 | 
  248 |         console.log(`[TC-ASUM-002] "${scenario.label}" → ${res.status}`);
  249 |         allure.parameter(scenario.label, String(res.status));
  250 | 
  251 |         if (res.status === 200) {
  252 |           bypassCount++;
  253 |           flagIssue('TC-ASUM-002', 'BUG-ASUM-01',
  254 |             `CRITICAL SECURITY: "${scenario.label}" returns 200 OK with live financial analytics data. ` +
  255 |             'The /rms-analytics/api/v1/analytics/dashboard/summary endpoint has NO authentication guard. ' +
  256 |             'Any unauthenticated internet request can read transaction counts and financial summaries. ' +
  257 |             'All other RMS endpoints (dashboard-api) correctly return 401 for this scenario.',
  258 |             {
  259 |               scenario:       scenario.label,
  260 |               actual_status:  res.status,
  261 |               expected_status: 401,
  262 |               data_exposed:   res.body,
  263 |               impact:         'FINANCIAL DATA PUBLICLY ACCESSIBLE without credentials',
  264 |               other_endpoints: 'dashboard-api/api/v1/dashboard/* all correctly return 401',
  265 |             },
  266 |           );
  267 |         }
  268 | 
  269 |         logger.info('Asserting: status must be 401');
  270 |         expect.soft(res.status,
  271 |           `[BUG-ASUM-01] "${scenario.label}" must return 401 Unauthorized, got ${res.status}`,
  272 |         ).toBe(401);
  273 |       });
  274 |     }
  275 | 
  276 |     await logger.step('Confirm total bypass count', async () => {
  277 |       allure.parameter('Auth bypass scenarios tested', String(authBypassScenarios.length));
  278 |       allure.parameter('Bypass count (all should be 0)', String(bypassCount));
  279 | 
  280 |       if (bypassCount === authBypassScenarios.length) {
  281 |         flagIssue('TC-ASUM-002', 'BUG-ASUM-01-SUMMARY',
  282 |           `ALL ${bypassCount} authentication bypass attempts returned 200 with real data. ` +
  283 |           'The endpoint has no authentication middleware at all – it is completely unprotected.',
  284 |           { bypass_count: bypassCount, total_tested: authBypassScenarios.length, endpoint: ENDPOINT },
  285 |         );
  286 |       }
  287 | 
  288 |       logger.info('Asserting: bypass count must be 0');
  289 |       expect.soft(bypassCount,
  290 |         `[BUG-ASUM-01] ${bypassCount} of ${authBypassScenarios.length} auth-bypass scenarios returned 200 – endpoint must require valid JWT`,
  291 |       ).toBe(0);
  292 |       logger.pass('All assertions passed');
  293 |     });
  294 |   });
  295 | 
```