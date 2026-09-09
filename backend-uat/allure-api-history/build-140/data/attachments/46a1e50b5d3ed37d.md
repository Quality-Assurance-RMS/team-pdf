# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-summary.spec.ts >> Backend (Konza) â€" Transaction Summary >> TC-BE-003 â€" Summary: period=custom returns 200 with expected shape
- Location: tests/API/backend-tests/backend-summary.spec.ts:251:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
  180 |       allure.parameter('total_volume.currency',         String(vol.currency));
  181 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  182 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  183 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  184 |       allure.parameter('success_rate.change_pct',       String(rate.change_pct ?? 'null âš  ISSUE-2'));
  185 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  186 |       allure.parameter('total_refunds.amount',          String(refunds.amount));
  187 |       allure.parameter('total_refunds.currency',        String(refunds.currency));
  188 |       allure.parameter('total_refunds.change_direction', String(refunds.change_direction ?? 'MISSING âš  ISSUE-1'));
  189 |     });
  190 |   });
  191 | 
  192 |   // â"€â"€ TC-BE-002 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  193 |   test('TC-BE-002 â€" Summary: period=today returns 200 with expected shape', async ({ logger }) => {
  194 |     backendLabels('TC-BE-002', 'Summary â€" Today');
  195 | 
  196 |     const params = { period: 'today' };
  197 |     let res: supertest.Response;
  198 | 
  199 |     await logger.step('Step 1 â€" Build request', async () => {
  200 |       allure.parameter('Base URL',  BASE_URL);
  201 |       allure.parameter('Endpoint',  ENDPOINT);
  202 |       allure.parameter('Method',    'GET');
  203 |       allure.parameter('period',    params.period);
  204 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  205 |     });
  206 | 
  207 |     await logger.step('Step 2 â€" Send GET /summary?period=today and log response', async () => {
  208 |       logger.info('GET /api/v1/transaction/summary?period=today');
  209 |       res = await get(params);
  210 |       logger.pass('HTTP ' + res.status + ' received');
  211 |       logResponse('TC-BE-002', params, res);
  212 |       allure.parameter('HTTP Status',   String(res.status));
  213 |       allure.parameter('Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  214 |     });
  215 | 
  216 |     await logger.step('Step 3 â€" Validate HTTP 200 and top-level fields present', async () => {
  217 |       logger.info('Asserting: HTTP 200 and top-level fields present');
  218 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
  219 |       expect(res!.body,               'Body must be defined').toBeDefined();
  220 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
  221 |       expect(res!.body.success_rate,  'success_rate must be present').toBeDefined();
  222 |       expect(res!.body.total_refunds, 'total_refunds must be present').toBeDefined();
  223 |       allure.parameter('Fields present', Object.keys(res!.body).join(', '));
  224 |       logger.pass('All assertions passed');
  225 |     });
  226 | 
  227 |     await logger.step('Step 4 â€" Validate field types, ranges, and known-issues', async () => {
  228 |       logger.info('Asserting: field types, ranges, and known-issues');
  229 |       validateSummaryShape('TC-BE-002', res!);
  230 |       logger.pass('All assertions passed');
  231 |     });
  232 | 
  233 |     await logger.step('Step 5 â€" Log key response values', async () => {
  234 |       const vol     = res!.body.total_volume   ?? {};
  235 |       const rate    = res!.body.success_rate   ?? {};
  236 |       const refunds = res!.body.total_refunds  ?? {};
  237 |       allure.parameter('total_volume.amount',           String(vol.amount));
  238 |       allure.parameter('total_volume.currency',         String(vol.currency));
  239 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  240 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  241 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  242 |       allure.parameter('success_rate.change_pct',       String(rate.change_pct ?? 'null âš  ISSUE-2'));
  243 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  244 |       allure.parameter('total_refunds.amount',          String(refunds.amount));
  245 |       allure.parameter('total_refunds.currency',        String(refunds.currency));
  246 |       allure.parameter('total_refunds.change_direction', String(refunds.change_direction ?? 'MISSING âš  ISSUE-1'));
  247 |     });
  248 |   });
  249 | 
  250 |   // â"€â"€ TC-BE-003 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  251 |   test('TC-BE-003 â€" Summary: period=custom returns 200 with expected shape', async ({ logger }) => {
  252 |     backendLabels('TC-BE-003', 'Summary â€" Custom Range');
  253 | 
  254 |     const params = { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-22' };
  255 |     let res: supertest.Response;
  256 | 
  257 |     await logger.step('Step 1 â€" Build request', async () => {
  258 |       allure.parameter('Base URL',   BASE_URL);
  259 |       allure.parameter('Endpoint',   ENDPOINT);
  260 |       allure.parameter('Method',     'GET');
  261 |       allure.parameter('period',     params.period);
  262 |       allure.parameter('from_date',  params.from_date);
  263 |       allure.parameter('to_date',    params.to_date);
  264 |       allure.parameter('Auth',       `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  265 |     });
  266 | 
  267 |     await logger.step('Step 2 â€" Send GET /summary?period=custom and log response', async () => {
  268 |       logger.info('GET /api/v1/transaction/summary?period=custom&from_date=2026-01-01&to_date=2026-05-22');
  269 |       res = await get(params);
  270 |       logger.pass('HTTP ' + res.status + ' received');
  271 |       logResponse('TC-BE-003', params, res);
  272 |       allure.parameter('HTTP Status',   String(res.status));
  273 |       allure.parameter('Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  274 |     });
  275 | 
  276 |     await logger.step('Step 3 â€" Validate HTTP 200 and top-level fields present', async () => {
  277 |       logger.info('Asserting: HTTP 200 and top-level fields present');
  278 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
  279 |       expect(res!.body,               'Body must be defined').toBeDefined();
> 280 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
      |                                                                       ^ Error: total_volume must be present
  281 |       expect(res!.body.success_rate,  'success_rate must be present').toBeDefined();
  282 |       expect(res!.body.total_refunds, 'total_refunds must be present').toBeDefined();
  283 |       allure.parameter('Fields present', Object.keys(res!.body).join(', '));
  284 |       logger.pass('All assertions passed');
  285 |     });
  286 | 
  287 |     await logger.step('Step 4 â€" Validate field types, ranges, and known-issues', async () => {
  288 |       logger.info('Asserting: field types, ranges, and known-issues');
  289 |       validateSummaryShape('TC-BE-003', res!);
  290 |       logger.pass('All assertions passed');
  291 |     });
  292 | 
  293 |     await logger.step('Step 5 â€" Validate custom range returns data for the entire date window', async () => {
  294 |       const vol = res!.body.total_volume ?? {};
  295 |       logger.info('Asserting: 5-month custom range has non-zero total_volume.amount');
  296 |       // 5-month range must have non-zero volume
  297 |       expect(vol.amount, 'Custom 5-month range must have total_volume.amount > 0').toBeGreaterThan(0);
  298 |       allure.parameter('5-month volume', `${vol.amount} ${vol.currency}`);
  299 |       logger.pass('All assertions passed');
  300 |     });
  301 | 
  302 |     await logger.step('Step 6 â€" Log key response values', async () => {
  303 |       const vol     = res!.body.total_volume   ?? {};
  304 |       const rate    = res!.body.success_rate   ?? {};
  305 |       const refunds = res!.body.total_refunds  ?? {};
  306 |       allure.parameter('total_volume.amount',           String(vol.amount));
  307 |       allure.parameter('total_volume.currency',         String(vol.currency));
  308 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  309 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  310 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  311 |       allure.parameter('success_rate.change_pct',       String(rate.change_pct ?? 'null âš  ISSUE-2'));
  312 |       allure.parameter('total_refunds.amount',          String(refunds.amount));
  313 |       allure.parameter('total_refunds.change_direction', String(refunds.change_direction ?? 'MISSING âš  ISSUE-1'));
  314 |     });
  315 |   });
  316 | 
  317 | });
  318 | 
```