# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-summary.spec.ts >> Backend (Konza) â€” Dashboard Summary >> TC-DSUM-003 â€” Response structure: all five sections present with correct types
- Location: tests/API/backend-tests/backend-dashboard-summary.spec.ts:258:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
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
  202 |         expect(res.status, `period=${period} must return HTTP 200`).toBe(200);
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
> 272 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                ^ Error: HTTP 200 expected
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
  303 |       allure.parameter('USD direction',  usd.change_direction);
  304 |       logger.pass('All assertions passed');
  305 |     });
  306 | 
  307 |     await logger.step('Step 5 â€” Validate total_transactions field types', async () => {
  308 |       const d   = res!.body?.data ?? res!.body;
  309 |       const txn = d.total_transactions;
  310 |       logger.info('Asserting: total_transactions field types and count integrity');
  311 |       expect(typeof txn.count,     'count must be a number').toBe('number');
  312 |       expect(typeof txn.count_kes, 'count_kes must be a number').toBe('number');
  313 |       expect(typeof txn.count_usd, 'count_usd must be a number').toBe('number');
  314 |       expect(txn.count,            'count must be >= 0').toBeGreaterThanOrEqual(0);
  315 |       expect(txn.count_kes + txn.count_usd, 'count_kes + count_usd must equal count').toBe(txn.count);
  316 |       expect(VALID_DIRECTIONS).toContain(txn.change_direction);
  317 |       allure.parameter('txn count',     String(txn.count));
  318 |       allure.parameter('txn count_kes', String(txn.count_kes));
  319 |       allure.parameter('txn count_usd', String(txn.count_usd));
  320 |       logger.pass('All assertions passed');
  321 |     });
  322 | 
  323 |     await logger.step('Step 6 â€” Validate successful_payments and failed_payments field types', async () => {
  324 |       const d    = res!.body?.data ?? res!.body;
  325 |       const succ = d.successful_payments;
  326 |       const fail = d.failed_payments;
  327 |       logger.info('Asserting: successful_payments and failed_payments field types and rate ranges');
  328 |       for (const [label, obj] of [['successful_payments', succ], ['failed_payments', fail]] as [string, Record<string, unknown>][]) {
  329 |         expect(typeof obj.rate_pct,   `${label}.rate_pct must be a number`).toBe('number');
  330 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeGreaterThanOrEqual(0);
  331 |         expect(obj.rate_pct as number, `${label}.rate_pct must be 0â€”100`).toBeLessThanOrEqual(100);
  332 |         expect(VALID_DIRECTIONS, `${label}.change_direction must be up/down/flat`).toContain(obj.change_direction);
  333 |         allure.parameter(`${label}.rate_pct`, String(obj.rate_pct) + '%');
  334 |       }
  335 |       logger.pass('All assertions passed');
  336 |     });
  337 |   });
  338 | 
  339 |   // â”€â”€ TC-DSUM-004 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  340 |   test('TC-DSUM-004 â€” Field types: amounts are numbers, rates are numbers', async ({ logger }) => {
  341 |     backendLabels('TC-DSUM-004', 'Field Type Validation â€” All Periods', 'normal');
  342 | 
  343 |     await logger.step('Step 1 â€” Validate each period returns numeric fields (not strings)', async () => {
  344 |       for (const period of ['week', 'year', 'last-3-month'] as const) {
  345 |         logger.info(`GET ${ENDPOINT}?period=${period}`);
  346 |         const res = await get({ period });
  347 |         logger.pass('HTTP ' + res.status + ' received');
  348 |         logger.info('Asserting: numeric field types for all key fields');
  349 |         expect(res.status, `period=${period}  -> 200`).toBe(200);
  350 |         const d = res.body?.data ?? res.body;
  351 | 
  352 |         expect(typeof d.total_revenue_kes.amount, `[${period}] KES amount must be number not string`).toBe('number');
  353 |         expect(typeof d.total_revenue_usd.amount, `[${period}] USD amount must be number not string`).toBe('number');
  354 |         expect(typeof d.total_transactions.count, `[${period}] count must be number not string`).toBe('number');
  355 |         expect(typeof d.successful_payments.rate_pct, `[${period}] rate_pct must be number not string`).toBe('number');
  356 | 
  357 |         allure.parameter(`${period} KES amount type`, typeof d.total_revenue_kes.amount);
  358 |         allure.parameter(`${period} success rate`,    String(d.successful_payments.rate_pct) + '%');
  359 |       }
  360 |       logger.pass('All assertions passed');
  361 |     });
  362 |   });
  363 | 
  364 |   // â”€â”€ TC-DSUM-005 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  365 |   test('TC-DSUM-005 â€” Input validation: invalid period, case sensitivity, error messages', async ({ logger }) => {
  366 |     backendLabels('TC-DSUM-005', 'Input Validation â€” Period Parameter');
  367 | 
  368 |     let resInvalid: supertest.Response;
  369 |     let resUpper: supertest.Response;
  370 |     let resMixed: supertest.Response;
  371 | 
  372 |     await logger.step('Step 1 â€” period=INVALID_XYZ must return 400', async () => {
```