# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-001 â€” Happy path: all valid periods return HTTP 200
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:176:7

# Error details

```
Error: period=week must return HTTP 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  94  |   console.log(`  â–º Content-Type   : ${res.headers['content-type'] ?? 'N/A'}`);
  95  |   console.log(`  â–º period         : ${d.period ?? 'N/A'}`);
  96  |   console.log(`  â–º date_from      : ${d.date_from ?? 'N/A'}`);
  97  |   console.log(`  â–º date_to        : ${d.date_to ?? 'N/A'}`);
  98  | 
  99  |   if (d.total_revenue_kes) {
  100 |     const r = d.total_revenue_kes;
  101 |     console.log(`  â–º KES revenue    : ${r.amount} ${r.currency} | change_pct=${r.change_pct} dir=${r.change_direction}`);
  102 |   }
  103 |   if (d.total_revenue_usd) {
  104 |     const r = d.total_revenue_usd;
  105 |     console.log(`  â–º USD revenue    : ${r.amount} ${r.currency} | change_pct=${r.change_pct} dir=${r.change_direction}`);
  106 |   }
  107 |   if (d.total_transactions) {
  108 |     const t = d.total_transactions;
  109 |     console.log(`  â–º transactions   : count=${t.count} (kes=${t.count_kes} usd=${t.count_usd}) | change_pct=${t.change_pct} dir=${t.change_direction}`);
  110 |   }
  111 |   if (d.successful_payments) {
  112 |     const s = d.successful_payments;
  113 |     const f = d.failed_payments ?? {};
  114 |     const sum = (s.rate_pct ?? 0) + (f.rate_pct ?? 0);
  115 |     console.log(`  â–º success rate   : ${s.rate_pct}% change_pct=${s.change_pct} dir=${s.change_direction}`);
  116 |     console.log(`  â–º failed rate    : ${f.rate_pct}% change_pct=${f.change_pct} dir=${f.change_direction}`);
  117 |     console.log(`  â–º success+failed : ${sum.toFixed(1)}%${Math.abs(sum - 100) > 0.05 ? ' âš  BUG-SUM-05: NOT 100%' : ''}`);
  118 |   }
  119 |   console.log(`  â–º Full Body:`);
  120 |   console.log(JSON.stringify(res.body, null, 4));
  121 |   console.log(`${'â”€'.repeat(70)}\n`);
  122 | 
  123 |   allure.attachment(
  124 |     `[${tcId}] ${Object.keys(params).length ? JSON.stringify(params) : 'no params'} â€” Request & Response`,
  125 |     JSON.stringify({
  126 |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***', Accept: 'application/json' } },
  127 |       response: { status: res.status, content_type: res.headers['content-type'], body: res.body },
  128 |     }, null, 2),
  129 |     'application/json',
  130 |   );
  131 | }
  132 | 
  133 | function flagIssue(tcId: string, bugId: string, description: string, actual: unknown) {
  134 |   const msg = `[${tcId}] ${bugId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  135 |   console.warn(`âš  BUG FLAGGED â€” ${msg}`);
  136 |   allure.attachment(`âš  ${bugId}`, msg, 'text/plain');
  137 | }
  138 | 
  139 | // â”€â”€ Allure labels â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  140 | 
  141 | function smokeLabels(tcId: string, title: string, severity: 'blocker' | 'critical' | 'normal' = 'critical') {
  142 |   allure.parentSuite('RMS Dashboard API');
  143 |   allure.suite('Smoke');
  144 |   allure.subSuite('Summary Endpoint');
  145 |   allure.label('testId',   tcId);
  146 |   allure.label('severity', severity);
  147 |   allure.owner('Ashil Shaji');
  148 |   allure.tags('Smoke', 'API', 'Dashboard', 'Summary');
  149 |   allure.label('story', title);
  150 | }
  151 | 
  152 | // â”€â”€ Shared: validate core response shape â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  153 | 
  154 | function validateShape(tcId: string, res: supertest.Response) {
  155 |   const d = res.body?.data ?? res.body;
  156 | 
  157 |   expect(d, 'period field must be present').toHaveProperty('period');
  158 |   expect(d, 'date_from field must be present').toHaveProperty('date_from');
  159 |   expect(d, 'date_to field must be present').toHaveProperty('date_to');
  160 |   expect(d, 'total_revenue_kes must be present').toHaveProperty('total_revenue_kes');
  161 |   expect(d, 'total_revenue_usd must be present').toHaveProperty('total_revenue_usd');
  162 |   expect(d, 'total_transactions must be present').toHaveProperty('total_transactions');
  163 |   expect(d, 'successful_payments must be present').toHaveProperty('successful_payments');
  164 |   expect(d, 'failed_payments must be present').toHaveProperty('failed_payments');
  165 | 
  166 |   allure.parameter('Fields present', Object.keys(d).join(', '));
  167 | }
  168 | 
  169 | // â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  170 | // SUITE
  171 | // â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  172 | 
  173 | test.describe('Smoke â€” Dashboard Summary', () => {
  174 | 
  175 |   // â”€â”€ TC-DSUM-001 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  176 |   test('TC-DSUM-001 â€” Happy path: all valid periods return HTTP 200', async ({ logger }) => {
  177 |     smokeLabels('TC-DSUM-001', 'Happy Path â€” All Valid Periods');
  178 | 
  179 |     await logger.step('Step 1 â€” Build requests for all valid period values', async () => {
  180 |       allure.parameter('Base URL',  BASE_URL);
  181 |       allure.parameter('Endpoint',  ENDPOINT);
  182 |       allure.parameter('Periods',   VALID_PERIODS.join(', '));
  183 |       logger.info('Asserting: all valid period values are listed');
  184 |       logger.pass('All assertions passed');
  185 |     });
  186 | 
  187 |     for (const period of VALID_PERIODS) {
  188 |       await logger.step(`Step â€” period=${period}: HTTP 200 and body defined`, async () => {
  189 |         logger.info(`GET ${ENDPOINT}?period=${period}`);
  190 |         const res = await get({ period });
  191 |         logger.pass('HTTP ' + res.status + ' received');
  192 |         logResponse('TC-DSUM-001', { period }, res);
  193 |         logger.info('Asserting: HTTP 200 and body defined');
> 194 |         expect(res.status, `period=${period} must return HTTP 200`).toBe(200);
      |                                                                     ^ Error: period=week must return HTTP 200
  195 |         expect(res.body,   `period=${period} body must be defined`).toBeDefined();
  196 |         allure.parameter(`period=${period} status`, String(res.status));
  197 |         allure.parameter(`period=${period} date_from`, String(res.body?.data?.date_from ?? 'N/A'));
  198 |         allure.parameter(`period=${period} date_to`,   String(res.body?.data?.date_to   ?? 'N/A'));
  199 |         logger.pass('All assertions passed');
  200 |       });
  201 |     }
  202 |   });
  203 | 
  204 |   // â”€â”€ TC-DSUM-002 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  205 |   test('TC-DSUM-002 â€” Auth guard: no token  -> 401, invalid token  -> 401', async ({ logger }) => {
  206 |     smokeLabels('TC-DSUM-002', 'Auth Guard â€” Unauthorized', 'blocker');
  207 | 
  208 |     let resNoAuth: supertest.Response;
  209 |     let resInvalid: supertest.Response;
  210 | 
  211 |     await logger.step('Step 1 â€” Send request with no Authorization header', async () => {
  212 |       logger.info(`GET ${ENDPOINT} (no Authorization header)`);
  213 |       resNoAuth = await getNoAuth();
  214 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  215 |       console.log(`\n[TC-DSUM-002] no-auth  -> ${resNoAuth.status}: ${JSON.stringify(resNoAuth.body)}`);
  216 |       allure.attachment('No-Auth Response', JSON.stringify({ status: resNoAuth.status, body: resNoAuth.body }, null, 2), 'application/json');
  217 |     });
  218 | 
  219 |     await logger.step('Step 2 â€” Validate no-auth returns 401 with error body', async () => {
  220 |       logger.info('Asserting: no-auth returns 401 with error body');
  221 |       expect(resNoAuth!.status, 'No-auth must return 401').toBe(401);
  222 |       expect(resNoAuth!.body.status ?? resNoAuth!.body.statusCode, '401 in error body').toBe(401);
  223 |       const msg = String(resNoAuth!.body.message ?? resNoAuth!.body.error ?? '');
  224 |       expect(msg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  225 |       allure.parameter('No-auth HTTP status', String(resNoAuth!.status));
  226 |       allure.parameter('No-auth error',       msg);
  227 |       logger.pass('All assertions passed');
  228 |     });
  229 | 
  230 |     await logger.step('Step 3 â€” Send request with invalid Bearer token', async () => {
  231 |       logger.info(`GET ${ENDPOINT} (invalid Bearer token)`);
  232 |       resInvalid = await getInvalidAuth();
  233 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  234 |       console.log(`\n[TC-DSUM-002] invalid-token  -> ${resInvalid.status}: ${JSON.stringify(resInvalid.body)}`);
  235 |       allure.attachment('Invalid-Auth Response', JSON.stringify({ status: resInvalid.status, body: resInvalid.body }, null, 2), 'application/json');
  236 |     });
  237 | 
  238 |     await logger.step('Step 4 â€” Validate invalid token returns 401', async () => {
  239 |       logger.info('Asserting: invalid token returns 401 with error message');
  240 |       expect(resInvalid!.status, 'Invalid token must return 401').toBe(401);
  241 |       const msg = String(resInvalid!.body.message ?? resInvalid!.body.error ?? '');
  242 |       expect(msg.length, 'Error message must be non-empty').toBeGreaterThan(0);
  243 |       allure.parameter('Invalid-token HTTP status', String(resInvalid!.status));
  244 |       allure.parameter('Invalid-token error',       msg);
  245 |       logger.pass('All assertions passed');
  246 |     });
  247 |   });
  248 | 
  249 |   // â”€â”€ TC-DSUM-003 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  250 |   test('TC-DSUM-003 â€” Response structure: all five sections present with correct types', async ({ logger }) => {
  251 |     smokeLabels('TC-DSUM-003', 'Response Structure â€” Field Types & Shape');
  252 | 
  253 |     let res: supertest.Response;
  254 | 
  255 |     await logger.step('Step 1 â€” Send GET with period=week', async () => {
  256 |       logger.info(`GET ${ENDPOINT}?period=week`);
  257 |       res = await get({ period: 'week' });
  258 |       logger.pass('HTTP ' + res.status + ' received');
  259 |       logResponse('TC-DSUM-003', { period: 'week' }, res);
  260 |     });
  261 | 
  262 |     await logger.step('Step 2 â€” Validate HTTP 200 and all five top-level sections', async () => {
  263 |       logger.info('Asserting: HTTP 200 and all five top-level sections present');
  264 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  265 |       validateShape('TC-DSUM-003', res!);
  266 |       logger.pass('All assertions passed');
  267 |     });
  268 | 
  269 |     await logger.step('Step 3 â€” Validate total_revenue_kes field types', async () => {
  270 |       const d   = res!.body?.data ?? res!.body;
  271 |       const kes = d.total_revenue_kes;
  272 |       logger.info('Asserting: total_revenue_kes field types and values');
  273 |       expect(typeof kes.amount,   'total_revenue_kes.amount must be a number').toBe('number');
  274 |       expect(kes.amount,          'total_revenue_kes.amount must be >= 0').toBeGreaterThanOrEqual(0);
  275 |       expect(kes.currency,        'total_revenue_kes.currency must be KES').toBe('KES');
  276 |       expect(typeof kes.change_pct,       'change_pct must be a number').toBe('number');
  277 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(kes.change_direction);
  278 |       allure.parameter('KES amount',    String(kes.amount));
  279 |       allure.parameter('KES change_pct', String(kes.change_pct));
  280 |       allure.parameter('KES direction',  kes.change_direction);
  281 |       logger.pass('All assertions passed');
  282 |     });
  283 | 
  284 |     await logger.step('Step 4 â€” Validate total_revenue_usd field types', async () => {
  285 |       const d   = res!.body?.data ?? res!.body;
  286 |       const usd = d.total_revenue_usd;
  287 |       logger.info('Asserting: total_revenue_usd field types and values');
  288 |       expect(typeof usd.amount,   'total_revenue_usd.amount must be a number').toBe('number');
  289 |       expect(usd.amount,          'total_revenue_usd.amount must be >= 0').toBeGreaterThanOrEqual(0);
  290 |       expect(usd.currency,        'total_revenue_usd.currency must be USD').toBe('USD');
  291 |       expect(typeof usd.change_pct,       'change_pct must be a number').toBe('number');
  292 |       expect(VALID_DIRECTIONS,    'change_direction must be up/down/flat').toContain(usd.change_direction);
  293 |       allure.parameter('USD amount',    String(usd.amount));
  294 |       allure.parameter('USD change_pct', String(usd.change_pct));
```