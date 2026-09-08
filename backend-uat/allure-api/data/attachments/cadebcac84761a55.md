# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-001 â€” Happy path: all valid periods return HTTP 200
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:184:7

# Error details

```
Error: period=week must return HTTP 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  102 |   console.log(`  â–º Content-Type   : ${res.headers['content-type'] ?? 'N/A'}`);
  103 |   console.log(`  â–º period         : ${d.period ?? 'N/A'}`);
  104 |   console.log(`  â–º date_from      : ${d.date_from ?? 'N/A'}`);
  105 |   console.log(`  â–º date_to        : ${d.date_to ?? 'N/A'}`);
  106 | 
  107 |   if (d.total_revenue_kes) {
  108 |     const r = d.total_revenue_kes;
  109 |     console.log(`  â–º KES revenue    : ${r.amount} ${r.currency} | change_pct=${r.change_pct} dir=${r.change_direction}`);
  110 |   }
  111 |   if (d.total_revenue_usd) {
  112 |     const r = d.total_revenue_usd;
  113 |     console.log(`  â–º USD revenue    : ${r.amount} ${r.currency} | change_pct=${r.change_pct} dir=${r.change_direction}`);
  114 |   }
  115 |   if (d.total_transactions) {
  116 |     const t = d.total_transactions;
  117 |     console.log(`  â–º transactions   : count=${t.count} (kes=${t.count_kes} usd=${t.count_usd}) | change_pct=${t.change_pct} dir=${t.change_direction}`);
  118 |   }
  119 |   if (d.successful_payments) {
  120 |     const s = d.successful_payments;
  121 |     const f = d.failed_payments ?? {};
  122 |     const sum = (s.rate_pct ?? 0) + (f.rate_pct ?? 0);
  123 |     console.log(`  â–º success rate   : ${s.rate_pct}% change_pct=${s.change_pct} dir=${s.change_direction}`);
  124 |     console.log(`  â–º failed rate    : ${f.rate_pct}% change_pct=${f.change_pct} dir=${f.change_direction}`);
  125 |     console.log(`  â–º success+failed : ${sum.toFixed(1)}%${Math.abs(sum - 100) > 0.05 ? ' âš  BUG-SUM-05: NOT 100%' : ''}`);
  126 |   }
  127 |   console.log(`  â–º Full Body:`);
  128 |   console.log(JSON.stringify(res.body, null, 4));
  129 |   console.log(`${'â”€'.repeat(70)}\n`);
  130 | 
  131 |   allure.attachment(
  132 |     `[${tcId}] ${Object.keys(params).length ? JSON.stringify(params) : 'no params'} â€” Request & Response`,
  133 |     JSON.stringify({
  134 |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***', Accept: 'application/json' } },
  135 |       response: { status: res.status, content_type: res.headers['content-type'], body: res.body },
  136 |     }, null, 2),
  137 |     'application/json',
  138 |   );
  139 | }
  140 | 
  141 | function flagIssue(tcId: string, bugId: string, description: string, actual: unknown) {
  142 |   const msg = `[${tcId}] ${bugId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  143 |   console.warn(`âš  BUG FLAGGED â€” ${msg}`);
  144 |   allure.attachment(`âš  ${bugId}`, msg, 'text/plain');
  145 | }
  146 | 
  147 | // â”€â”€ Allure labels â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  148 | 
  149 | function backendLabels(tcId: string, title: string, severity: 'blocker' | 'critical' | 'normal' = 'critical') {
  150 |   allure.parentSuite('RMS Dashboard API');
  151 |   allure.suite('Backend');
  152 |   allure.subSuite('Summary Endpoint');
  153 |   allure.label('testId',   tcId);
  154 |   allure.label('severity', severity);
  155 |   allure.owner('Ashil Shaji');
  156 |   allure.tags('Backend', 'Konza', 'API', 'Dashboard', 'Summary');
  157 |   allure.label('story', title);
  158 | }
  159 | 
  160 | // â”€â”€ Shared: validate core response shape â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  161 | 
  162 | function validateShape(tcId: string, res: supertest.Response) {
  163 |   const d = res.body?.data ?? res.body;
  164 | 
  165 |   expect(d, 'period field must be present').toHaveProperty('period');
  166 |   expect(d, 'date_from field must be present').toHaveProperty('date_from');
  167 |   expect(d, 'date_to field must be present').toHaveProperty('date_to');
  168 |   expect(d, 'total_revenue_kes must be present').toHaveProperty('total_revenue_kes');
  169 |   expect(d, 'total_revenue_usd must be present').toHaveProperty('total_revenue_usd');
  170 |   expect(d, 'total_transactions must be present').toHaveProperty('total_transactions');
  171 |   expect(d, 'successful_payments must be present').toHaveProperty('successful_payments');
  172 |   expect(d, 'failed_payments must be present').toHaveProperty('failed_payments');
  173 | 
  174 |   allure.parameter('Fields present', Object.keys(d).join(', '));
  175 | }
  176 | 
  177 | // â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  178 | // SUITE
  179 | // â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  180 | 
  181 | test.describe('Backend (Konza) â€” Dashboard Summary', () => {
  182 | 
  183 |   // â”€â”€ TC-DSUM-001 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  184 |   test('TC-DSUM-001 â€” Happy path: all valid periods return HTTP 200', async ({ logger }) => {
  185 |     backendLabels('TC-DSUM-001', 'Happy Path â€” All Valid Periods');
  186 | 
  187 |     await logger.step('Step 1 â€” Build requests for all valid period values', async () => {
  188 |       allure.parameter('Base URL',  BASE_URL);
  189 |       allure.parameter('Endpoint',  ENDPOINT);
  190 |       allure.parameter('Periods',   VALID_PERIODS.join(', '));
  191 |       logger.info('Asserting: all valid period values are listed');
  192 |       logger.pass('All assertions passed');
  193 |     });
  194 | 
  195 |     for (const period of VALID_PERIODS) {
  196 |       await logger.step(`Step â€” period=${period}: HTTP 200 and body defined`, async () => {
  197 |         logger.info(`GET ${ENDPOINT}?period=${period}`);
  198 |         const res = await get({ period });
  199 |         logger.pass('HTTP ' + res.status + ' received');
  200 |         logResponse('TC-DSUM-001', { period }, res);
  201 |         logger.info('Asserting: HTTP 200 and body defined');
> 202 |         expect(res.status, `period=${period} must return HTTP 200`).toBe(200);
      |                                                                     ^ Error: period=week must return HTTP 200
  203 |         expect(res.body,   `period=${period} body must be defined`).toBeDefined();
  204 |         allure.parameter(`period=${period} status`, String(res.status));
  205 |         allure.parameter(`period=${period} date_from`, String(res.body?.data?.date_from ?? 'N/A'));
  206 |         allure.parameter(`period=${period} date_to`,   String(res.body?.data?.date_to   ?? 'N/A'));
  207 |         logger.pass('All assertions passed');
  208 |       });
  209 |     }
  210 |   });
  211 | 
  212 |   // â”€â”€ TC-DSUM-002 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  213 |   test('TC-DSUM-002 â€” Auth guard: no token  -> 401, invalid token  -> 401', async ({ logger }) => {
  214 |     backendLabels('TC-DSUM-002', 'Auth Guard â€” Unauthorized', 'blocker');
  215 | 
  216 |     let resNoAuth: supertest.Response;
  217 |     let resInvalid: supertest.Response;
  218 | 
  219 |     await logger.step('Step 1 â€” Send request with no Authorization header', async () => {
  220 |       logger.info(`GET ${ENDPOINT} (no Authorization header)`);
  221 |       resNoAuth = await getNoAuth();
  222 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  223 |       console.log(`\n[TC-DSUM-002] no-auth  -> ${resNoAuth.status}: ${JSON.stringify(resNoAuth.body)}`);
  224 |       allure.attachment('No-Auth Response', JSON.stringify({ status: resNoAuth.status, body: resNoAuth.body }, null, 2), 'application/json');
  225 |     });
  226 | 
  227 |     await logger.step('Step 2 â€” Validate no-auth returns 401 with error body', async () => {
  228 |       logger.info('Asserting: no-auth returns 401 with error body');
  229 |       expect(resNoAuth!.status, 'No-auth must return 401').toBe(401);
  230 |       expect(resNoAuth!.body.status ?? resNoAuth!.body.statusCode, '401 in error body').toBe(401);
  231 |       const msg = String(resNoAuth!.body.message ?? resNoAuth!.body.error ?? '');
  232 |       expect(msg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  233 |       allure.parameter('No-auth HTTP status', String(resNoAuth!.status));
  234 |       allure.parameter('No-auth error',       msg);
  235 |       logger.pass('All assertions passed');
  236 |     });
  237 | 
  238 |     await logger.step('Step 3 â€” Send request with invalid Bearer token', async () => {
  239 |       logger.info(`GET ${ENDPOINT} (invalid Bearer token)`);
  240 |       resInvalid = await getInvalidAuth();
  241 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  242 |       console.log(`\n[TC-DSUM-002] invalid-token  -> ${resInvalid.status}: ${JSON.stringify(resInvalid.body)}`);
  243 |       allure.attachment('Invalid-Auth Response', JSON.stringify({ status: resInvalid.status, body: resInvalid.body }, null, 2), 'application/json');
  244 |     });
  245 | 
  246 |     await logger.step('Step 4 â€” Validate invalid token returns 401', async () => {
  247 |       logger.info('Asserting: invalid token returns 401 with error message');
  248 |       expect(resInvalid!.status, 'Invalid token must return 401').toBe(401);
  249 |       const msg = String(resInvalid!.body.message ?? resInvalid!.body.error ?? '');
  250 |       expect(msg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  251 |       allure.parameter('Invalid-token HTTP status', String(resInvalid!.status));
  252 |       allure.parameter('Invalid-token error',       msg);
  253 |       logger.pass('All assertions passed');
  254 |     });
  255 |   });
  256 | 
  257 |   // â”€â”€ TC-DSUM-003 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  258 |   test('TC-DSUM-003 â€” Response structure: all five sections present with correct types', async ({ logger }) => {
  259 |     backendLabels('TC-DSUM-003', 'Response Structure â€” Field Types & Shape');
  260 | 
  261 |     let res: supertest.Response;
  262 | 
  263 |     await logger.step('Step 1 â€” Send GET with period=week', async () => {
  264 |       logger.info(`GET ${ENDPOINT}?period=week`);
  265 |       res = await get({ period: 'week' });
  266 |       logger.pass('HTTP ' + res.status + ' received');
  267 |       logResponse('TC-DSUM-003', { period: 'week' }, res);
  268 |     });
  269 | 
  270 |     await logger.step('Step 2 â€” Validate HTTP 200 and all five top-level sections', async () => {
  271 |       logger.info('Asserting: HTTP 200 and all five top-level sections present');
  272 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  273 |       validateShape('TC-DSUM-003', res!);
  274 |       logger.pass('All assertions passed');
  275 |     });
  276 | 
  277 |     await logger.step('Step 3 â€” Validate total_revenue_kes field types', async () => {
  278 |       const d   = res!.body?.data ?? res!.body;
  279 |       const kes = d.total_revenue_kes;
  280 |       logger.info('Asserting: total_revenue_kes field types and values');
  281 |       expect(typeof kes.amount,   'total_revenue_kes.amount must be a number').toBe('number');
  282 |       expect(kes.amount,          'total_revenue_kes.amount must be >= 0').toBeGreaterThanOrEqual(0);
  283 |       expect(kes.currency,        'total_revenue_kes.currency must be KES').toBe('KES');
  284 |       expect(typeof kes.change_pct,       'change_pct must be a number').toBe('number');
  285 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(kes.change_direction);
  286 |       allure.parameter('KES amount',    String(kes.amount));
  287 |       allure.parameter('KES change_pct', String(kes.change_pct));
  288 |       allure.parameter('KES direction',  kes.change_direction);
  289 |       logger.pass('All assertions passed');
  290 |     });
  291 | 
  292 |     await logger.step('Step 4 â€” Validate total_revenue_usd field types', async () => {
  293 |       const d   = res!.body?.data ?? res!.body;
  294 |       const usd = d.total_revenue_usd;
  295 |       logger.info('Asserting: total_revenue_usd field types and values');
  296 |       expect(typeof usd.amount,   'total_revenue_usd.amount must be a number').toBe('number');
  297 |       expect(usd.amount,          'total_revenue_usd.amount must be >= 0').toBeGreaterThanOrEqual(0);
  298 |       expect(usd.currency,        'total_revenue_usd.currency must be USD').toBe('USD');
  299 |       expect(typeof usd.change_pct,       'change_pct must be a number').toBe('number');
  300 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(usd.change_direction);
  301 |       allure.parameter('USD amount',    String(usd.amount));
  302 |       allure.parameter('USD change_pct', String(usd.change_pct));
```