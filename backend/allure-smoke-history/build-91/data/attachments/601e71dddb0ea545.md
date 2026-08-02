# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-analytics-dashboard-summary.spec.ts >> Smoke – Analytics Dashboard Summary >> TC-ASUM-001 – Happy Path: 200 + mcda_id + today object with all expected fields
- Location: tests/API/smoke/smoke-analytics-dashboard-summary.spec.ts:164:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 502
```

# Test source

```ts
  87  | 
  88  | // ── Logger ──────────────────────────────────────────────────────────────────
  89  | 
  90  | function logResponse(tcId: string, res: supertest.Response, note = '') {
  91  |   const b = res.body as AnalyticsSummaryBody;
  92  |   const t = b?.today;
  93  | 
  94  |   console.log(`\n${'─'.repeat(70)}`);
  95  |   console.log(`[${tcId}]${note ? ' ' + note : ''} GET ${ANALYTICS_BASE}${ENDPOINT}`);
  96  |   console.log(`  ► HTTP Status             : ${res.status}`);
  97  |   console.log(`  ► Content-Type            : ${res.headers['content-type'] ?? 'N/A'}`);
  98  |   console.log(`  ► Has "data" wrapper       : ${'data' in (b ?? {})}  ← BUG-ASUM-07 if false`);
  99  |   if (b) {
  100 |     console.log(`  ► mcda_id                 : ${b.mcda_id}`);
  101 |   }
  102 |   if (t) {
  103 |     console.log(`  ► total_transactions      : ${t.total_transactions}`);
  104 |     console.log(`  ► successful_transactions : ${t.successful_transactions}`);
  105 |     console.log(`  ► failed_transactions     : ${t.failed_transactions}`);
  106 |     const unaccounted = t.total_transactions - (t.successful_transactions + t.failed_transactions);
  107 |     if (unaccounted !== 0) {
  108 |       console.warn(`  ⚠ BUG-ASUM-02: ${unaccounted} transactions unclassified (not success + failed)`);
  109 |     }
  110 |     console.log(`  ► success_rate_pct        : ${t.success_rate_pct}`);
  111 |     console.log(`  ► total_amount_kes        : ${t.total_amount_kes}`);
  112 |     if (t.total_transactions > 0 && t.total_amount_kes === 0) {
  113 |       console.warn(`  ⚠ BUG-ASUM-03: total_amount_kes=0 with ${t.total_transactions} transactions`);
  114 |     }
  115 |     console.log(`  ► settlement_total_kes    : ${t.settlement_total_kes}`);
  116 |     console.log(`  ► recon_matched           : ${t.recon_matched}`);
  117 |     console.log(`  ► recon_exceptions        : ${t.recon_exceptions}`);
  118 |     const reconCoverage = t.recon_matched + t.recon_exceptions;
  119 |     if (reconCoverage === 0 && t.total_transactions > 0) {
  120 |       console.warn(`  ⚠ BUG-ASUM-04: 0 recon records for ${t.total_transactions} transactions`);
  121 |     }
  122 |   }
  123 |   console.log(`  ► Cache-Control           : ${res.headers['cache-control'] ?? 'MISSING'}`);
  124 |   console.log(`  ► ACAO                    : ${res.headers['access-control-allow-origin'] ?? 'MISSING'}`);
  125 |   console.log(`  ► ACAM                    : ${res.headers['access-control-allow-methods'] ?? 'MISSING'}`);
  126 |   console.log(`${'─'.repeat(70)}\n`);
  127 | }
  128 | 
  129 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  130 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  131 |   console.warn(`⚠ ISSUE FLAGGED – ${msg}`);
  132 |   allure.attachment(`⚠ ${issueId}`, msg, 'text/plain');
  133 | }
  134 | 
  135 | function attachResponse(tcId: string, res: supertest.Response, extra: Record<string, unknown> = {}) {
  136 |   allure.attachment(
  137 |     `[${tcId}] Request & Response`,
  138 |     JSON.stringify({
  139 |       request:  { method: 'GET', url: `${ANALYTICS_BASE}${ENDPOINT}`, headers: { Authorization: 'Bearer ***redacted***' } },
  140 |       response: { status: res.status, headers: res.headers, body: res.body, ...extra },
  141 |     }, null, 2),
  142 |     'application/json',
  143 |   );
  144 | }
  145 | 
  146 | function smokeLabels(tcId: string, title: string, severity: 'blocker' | 'critical' | 'normal' | 'minor' = 'critical') {
  147 |   allure.parentSuite('RMS Analytics API');
  148 |   allure.suite('Smoke');
  149 |   allure.subSuite('Dashboard Summary Endpoint');
  150 |   allure.label('testId',   tcId);
  151 |   allure.label('severity', severity);
  152 |   allure.owner('Ashil Shaji');
  153 |   allure.tags('Smoke', 'API', 'Analytics', 'DashboardSummary');
  154 |   allure.label('story', title);
  155 | }
  156 | 
  157 | // ══════════════════════════════════════════════════════════════════════════════
  158 | // Suite
  159 | // ══════════════════════════════════════════════════════════════════════════════
  160 | 
  161 | test.describe('Smoke – Analytics Dashboard Summary', () => {
  162 | 
  163 |   // ── TC-ASUM-001 ─────────────────────────────────────────────────────────────
  164 |   test('TC-ASUM-001 – Happy Path: 200 + mcda_id + today object with all expected fields', async ({ logger }) => {
  165 |     smokeLabels('TC-ASUM-001', 'Happy Path – Analytics Dashboard Summary');
  166 |     let res: supertest.Response;
  167 | 
  168 |     await logger.step('Step 1 – Build request', async () => {
  169 |       allure.parameter('Base URL',  ANALYTICS_BASE);
  170 |       allure.parameter('Endpoint',  ENDPOINT);
  171 |       allure.parameter('Method',    'GET');
  172 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  173 |     });
  174 | 
  175 |     await logger.step('Step 2 – Send GET request and log response', async () => {
  176 |       logger.info('GET /api/v1/analytics/dashboard/summary');
  177 |       res = await get();
  178 |       logger.pass('HTTP ' + res.status + ' received');
  179 |       logResponse('TC-ASUM-001', res);
  180 |       attachResponse('TC-ASUM-001', res);
  181 |       allure.parameter('HTTP Status',  String(res.status));
  182 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  183 |     });
  184 | 
  185 |     await logger.step('Step 3 – Validate HTTP 200 and Content-Type', async () => {
  186 |       logger.info('Asserting: HTTP 200, body defined, Content-Type application/json');
> 187 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                ^ Error: HTTP 200 expected
  188 |       expect(res!.body,   'Body must be defined').toBeDefined();
  189 |       expect(res!.headers['content-type'], 'Content-Type must be application/json').toContain('application/json');
  190 |       logger.pass('All assertions passed');
  191 |     });
  192 | 
  193 |     await logger.step('Step 4 – Validate top-level fields: mcda_id and today', async () => {
  194 |       logger.info('Asserting: mcda_id present, today present, types correct');
  195 |       expect(res!.body, 'Response must have "mcda_id" field').toHaveProperty('mcda_id');
  196 |       expect(res!.body, 'Response must have "today" field').toHaveProperty('today');
  197 |       expect(typeof res!.body.mcda_id, '"mcda_id" must be a string').toBe('string');
  198 |       expect(res!.body.today, '"today" must be an object').toBeInstanceOf(Object);
  199 |       allure.parameter('mcda_id', String(res!.body.mcda_id));
  200 |       logger.pass('All assertions passed');
  201 |     });
  202 | 
  203 |     await logger.step('Step 5 – Validate all 8 required fields in today object', async () => {
  204 |       const t = res!.body.today as TodayStats;
  205 |       const requiredFields = [
  206 |         'total_transactions', 'success_rate_pct', 'total_amount_kes',
  207 |         'successful_transactions', 'settlement_total_kes', 'failed_transactions',
  208 |         'recon_exceptions', 'recon_matched',
  209 |       ];
  210 |       logger.info('Asserting: all 8 required today.* fields present');
  211 |       for (const field of requiredFields) {
  212 |         expect(t, `today.${field} must be present`).toHaveProperty(field);
  213 |         allure.parameter(`today.${field}`, String((t as unknown as Record<string,unknown>)[field]));
  214 |       }
  215 |       logger.pass('All assertions passed');
  216 |     });
  217 |   });
  218 | 
  219 |   // ── TC-ASUM-002 ─────────────────────────────────────────────────────────────
  220 |   test('TC-ASUM-002 – BUG-ASUM-01 (BLOCKER): Complete auth bypass – endpoint is publicly accessible', async ({ logger }) => {
  221 |     smokeLabels('TC-ASUM-002', 'BUG-ASUM-01 – Complete Authentication Bypass', 'blocker');
  222 | 
  223 |     const authBypassScenarios: Array<{ label: string; req: () => supertest.Test }> = [
  224 |       { label: 'No Authorization header',         req: () => getNoAuth() },
  225 |       { label: 'Invalid Bearer token',             req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer invalidtoken.bad.sig').set('Accept', 'application/json') },
  226 |       { label: 'Bearer empty string',              req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer ').set('Accept', 'application/json') },
  227 |       { label: 'Bearer null literal',              req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer null').set('Accept', 'application/json') },
  228 |       { label: 'Wrong scheme (Basic)',             req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Basic dXNlcjpwYXNz').set('Accept', 'application/json') },
  229 |       { label: 'Clearly expired token',            req: () => supertest(ANALYTICS_BASE).get(ENDPOINT).set('Authorization', 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MDAwMDAwMDB9.abc123').set('Accept', 'application/json') },
  230 |     ];
  231 | 
  232 |     let bypassCount = 0;
  233 | 
  234 |     for (const scenario of authBypassScenarios) {
  235 |       await logger.step(`Auth bypass: "${scenario.label}"`, async () => {
  236 |         logger.info(`GET /api/v1/analytics/dashboard/summary (${scenario.label})`);
  237 |         const res = await scenario.req();
  238 |         logger.pass('HTTP ' + res.status + ' received');
  239 | 
  240 |         console.log(`[TC-ASUM-002] "${scenario.label}" → ${res.status}`);
  241 |         allure.parameter(scenario.label, String(res.status));
  242 | 
  243 |         if (res.status === 200) {
  244 |           bypassCount++;
  245 |           flagIssue('TC-ASUM-002', 'BUG-ASUM-01',
  246 |             `CRITICAL SECURITY: "${scenario.label}" returns 200 OK with live financial analytics data. ` +
  247 |             'The /rms-analytics/api/v1/analytics/dashboard/summary endpoint has NO authentication guard. ' +
  248 |             'Any unauthenticated internet request can read transaction counts and financial summaries. ' +
  249 |             'All other RMS endpoints (dashboard-api) correctly return 401 for this scenario.',
  250 |             {
  251 |               scenario:       scenario.label,
  252 |               actual_status:  res.status,
  253 |               expected_status: 401,
  254 |               data_exposed:   res.body,
  255 |               impact:         'FINANCIAL DATA PUBLICLY ACCESSIBLE without credentials',
  256 |               other_endpoints: 'dashboard-api/api/v1/dashboard/* all correctly return 401',
  257 |             },
  258 |           );
  259 |         }
  260 | 
  261 |         logger.info('Asserting: status must be 401');
  262 |         expect.soft(res.status,
  263 |           `[BUG-ASUM-01] "${scenario.label}" must return 401 Unauthorized, got ${res.status}`,
  264 |         ).toBe(401);
  265 |       });
  266 |     }
  267 | 
  268 |     await logger.step('Confirm total bypass count', async () => {
  269 |       allure.parameter('Auth bypass scenarios tested', String(authBypassScenarios.length));
  270 |       allure.parameter('Bypass count (all should be 0)', String(bypassCount));
  271 | 
  272 |       if (bypassCount === authBypassScenarios.length) {
  273 |         flagIssue('TC-ASUM-002', 'BUG-ASUM-01-SUMMARY',
  274 |           `ALL ${bypassCount} authentication bypass attempts returned 200 with real data. ` +
  275 |           'The endpoint has no authentication middleware at all – it is completely unprotected.',
  276 |           { bypass_count: bypassCount, total_tested: authBypassScenarios.length, endpoint: ENDPOINT },
  277 |         );
  278 |       }
  279 | 
  280 |       logger.info('Asserting: bypass count must be 0');
  281 |       expect.soft(bypassCount,
  282 |         `[BUG-ASUM-01] ${bypassCount} of ${authBypassScenarios.length} auth-bypass scenarios returned 200 – endpoint must require valid JWT`,
  283 |       ).toBe(0);
  284 |       logger.pass('All assertions passed');
  285 |     });
  286 |   });
  287 | 
```