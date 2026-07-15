# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-summary.spec.ts >> Smoke â€” Dashboard Summary >> TC-DSUM-003 â€” Response structure: all five sections present with correct types
- Location: tests/API/smoke/smoke-dashboard-summary.spec.ts:250:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
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
  194 |         expect(res.status, `period=${period} must return HTTP 200`).toBe(200);
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
> 264 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                ^ Error: HTTP 200 expected
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
  295 |       allure.parameter('USD direction',  usd.change_direction);
  296 |       logger.pass('All assertions passed');
  297 |     });
  298 | 
  299 |     await logger.step('Step 5 â€” Validate total_transactions field types', async () => {
  300 |       const d   = res!.body?.data ?? res!.body;
  301 |       const txn = d.total_transactions;
  302 |       logger.info('Asserting: total_transactions field types and count integrity');
  303 |       expect(typeof txn.count,     'count must be a number').toBe('number');
  304 |       expect(typeof txn.count_kes, 'count_kes must be a number').toBe('number');
  305 |       expect(typeof txn.count_usd, 'count_usd must be a number').toBe('number');
  306 |       expect(txn.count,            'count must be >= 0').toBeGreaterThanOrEqual(0);
  307 |       expect(txn.count_kes + txn.count_usd, 'count_kes + count_usd must equal count').toBe(txn.count);
  308 |       expect(VALID_DIRECTIONS).toContain(txn.change_direction);
  309 |       allure.parameter('txn count',     String(txn.count));
  310 |       allure.parameter('txn count_kes', String(txn.count_kes));
  311 |       allure.parameter('txn count_usd', String(txn.count_usd));
  312 |       logger.pass('All assertions passed');
  313 |     });
  314 | 
  315 |     await logger.step('Step 6 â€” Validate successful_payments and failed_payments field types', async () => {
  316 |       const d    = res!.body?.data ?? res!.body;
  317 |       const succ = d.successful_payments;
  318 |       const fail = d.failed_payments;
  319 |       logger.info('Asserting: successful_payments and failed_payments field types and rate ranges');
  320 |       for (const [label, obj] of [['successful_payments', succ], ['failed_payments', fail]] as [string, Record<string, unknown>][]) {
  321 |         expect(typeof obj.rate_pct,   `${label}.rate_pct must be a number`).toBe('number');
  322 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeGreaterThanOrEqual(0);
  323 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeLessThanOrEqual(100);
  324 |         expect(VALID_DIRECTIONS, `${label}.change_direction must be up/down/flat`).toContain(obj.change_direction);
  325 |         allure.parameter(`${label}.rate_pct`, String(obj.rate_pct) + '%');
  326 |       }
  327 |       logger.pass('All assertions passed');
  328 |     });
  329 |   });
  330 | 
  331 |   // â”€â”€ TC-DSUM-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  332 |   test('TC-DSUM-004 â€” Field types: amounts are numbers, rates are numbers', async ({ logger }) => {
  333 |     smokeLabels('TC-DSUM-004', 'Field Type Validation â€” All Periods', 'normal');
  334 | 
  335 |     await logger.step('Step 1 â€” Validate each period returns numeric fields (not strings)', async () => {
  336 |       for (const period of ['week', 'year', 'last-3-month'] as const) {
  337 |         logger.info(`GET ${ENDPOINT}?period=${period}`);
  338 |         const res = await get({ period });
  339 |         logger.pass('HTTP ' + res.status + ' received');
  340 |         logger.info('Asserting: numeric field types for all key fields');
  341 |         expect(res.status, `period=${period}  -> 200`).toBe(200);
  342 |         const d = res.body?.data ?? res.body;
  343 | 
  344 |         expect(typeof d.total_revenue_kes.amount, `[${period}] KES amount must be number not string`).toBe('number');
  345 |         expect(typeof d.total_revenue_usd.amount, `[${period}] USD amount must be number not string`).toBe('number');
  346 |         expect(typeof d.total_transactions.count, `[${period}] count must be number not string`).toBe('number');
  347 |         expect(typeof d.successful_payments.rate_pct, `[${period}] rate_pct must be number not string`).toBe('number');
  348 | 
  349 |         allure.parameter(`${period} KES amount type`, typeof d.total_revenue_kes.amount);
  350 |         allure.parameter(`${period} success rate`,    String(d.successful_payments.rate_pct) + '%');
  351 |       }
  352 |       logger.pass('All assertions passed');
  353 |     });
  354 |   });
  355 | 
  356 |   // â”€â”€ TC-DSUM-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  357 |   test('TC-DSUM-005 â€” Input validation: invalid period, case sensitivity, error messages', async ({ logger }) => {
  358 |     smokeLabels('TC-DSUM-005', 'Input Validation â€” Period Parameter');
  359 | 
  360 |     let resInvalid: supertest.Response;
  361 |     let resUpper: supertest.Response;
  362 |     let resMixed: supertest.Response;
  363 | 
  364 |     await logger.step('Step 1 â€” period=INVALID_XYZ must return 400', async () => {
```