# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-summary.spec.ts >> Smoke â€" Transaction Summary >> TC-SMK-002 â€" Summary: period=today returns 200 with expected shape
- Location: tests/API/smoke/smoke-summary.spec.ts:185:7

# Error details

```
Error: total_volume must be present

expect(received).toBeDefined()

Received: undefined
```

# Test source

```ts
  112 |   expect(refunds.currency,      'total_refunds.currency must be present').toBeTruthy();
  113 |   expect(refunds.currency,      'total_refunds.currency must be KES').toBe('KES');
  114 | 
  115 |   // â"€â"€ ISSUE-1: total_refunds is missing change_direction that total_volume has â"€
  116 |   if (!('change_direction' in refunds)) {
  117 |     flagIssue(tcId, 'ISSUE-1', 'total_refunds.change_direction field is missing â€" total_volume has it but total_refunds does not', refunds);
  118 |   }
  119 |   expect.soft(refunds, '[ISSUE-1] total_refunds should have change_direction field like total_volume does').toHaveProperty('change_direction');
  120 | }
  121 | 
  122 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  123 | 
  124 | test.describe('Smoke â€" Transaction Summary', () => {
  125 | 
  126 |   // â"€â"€ TC-SMK-001 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  127 |   test('TC-SMK-001 â€" Summary: period=week returns 200 with expected shape', async ({ logger }) => {
  128 |     smokeLabels('TC-SMK-001', 'Summary â€" Week');
  129 | 
  130 |     const params = { period: 'week' };
  131 |     let res: supertest.Response;
  132 | 
  133 |     await logger.step('Step 1 â€" Build request', async () => {
  134 |       allure.parameter('Base URL',  BASE_URL);
  135 |       allure.parameter('Endpoint',  ENDPOINT);
  136 |       allure.parameter('Method',    'GET');
  137 |       allure.parameter('period',    params.period);
  138 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  139 |     });
  140 | 
  141 |     await logger.step('Step 2 â€" Send GET /summary?period=week and log response', async () => {
  142 |       logger.info('GET /api/v1/transaction/summary?period=week');
  143 |       res = await get(params);
  144 |       logger.pass('HTTP ' + res.status + ' received');
  145 |       logResponse('TC-SMK-001', params, res);
  146 |       allure.parameter('HTTP Status',   String(res.status));
  147 |       allure.parameter('Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  148 |     });
  149 | 
  150 |     await logger.step('Step 3 â€" Validate HTTP 200 and top-level fields present', async () => {
  151 |       logger.info('Asserting: HTTP 200 and top-level fields present');
  152 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
  153 |       expect(res!.body,               'Body must be defined').toBeDefined();
  154 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
  155 |       expect(res!.body.success_rate,  'success_rate must be present').toBeDefined();
  156 |       expect(res!.body.total_refunds, 'total_refunds must be present').toBeDefined();
  157 |       allure.parameter('Fields present', Object.keys(res!.body).join(', '));
  158 |       logger.pass('All assertions passed');
  159 |     });
  160 | 
  161 |     await logger.step('Step 4 â€" Validate field types, ranges, and known-issues', async () => {
  162 |       logger.info('Asserting: field types, ranges, and known-issues');
  163 |       validateSummaryShape('TC-SMK-001', res!);
  164 |       logger.pass('All assertions passed');
  165 |     });
  166 | 
  167 |     await logger.step('Step 5 â€" Log key response values', async () => {
  168 |       const vol     = res!.body.total_volume   ?? {};
  169 |       const rate    = res!.body.success_rate   ?? {};
  170 |       const refunds = res!.body.total_refunds  ?? {};
  171 |       allure.parameter('total_volume.amount',           String(vol.amount));
  172 |       allure.parameter('total_volume.currency',         String(vol.currency));
  173 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  174 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  175 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  176 |       allure.parameter('success_rate.change_pct',       String(rate.change_pct ?? 'null âš  ISSUE-2'));
  177 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  178 |       allure.parameter('total_refunds.amount',          String(refunds.amount));
  179 |       allure.parameter('total_refunds.currency',        String(refunds.currency));
  180 |       allure.parameter('total_refunds.change_direction', String(refunds.change_direction ?? 'MISSING âš  ISSUE-1'));
  181 |     });
  182 |   });
  183 | 
  184 |   // â"€â"€ TC-SMK-002 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  185 |   test('TC-SMK-002 â€" Summary: period=today returns 200 with expected shape', async ({ logger }) => {
  186 |     smokeLabels('TC-SMK-002', 'Summary â€" Today');
  187 | 
  188 |     const params = { period: 'today' };
  189 |     let res: supertest.Response;
  190 | 
  191 |     await logger.step('Step 1 â€" Build request', async () => {
  192 |       allure.parameter('Base URL',  BASE_URL);
  193 |       allure.parameter('Endpoint',  ENDPOINT);
  194 |       allure.parameter('Method',    'GET');
  195 |       allure.parameter('period',    params.period);
  196 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  197 |     });
  198 | 
  199 |     await logger.step('Step 2 â€" Send GET /summary?period=today and log response', async () => {
  200 |       logger.info('GET /api/v1/transaction/summary?period=today');
  201 |       res = await get(params);
  202 |       logger.pass('HTTP ' + res.status + ' received');
  203 |       logResponse('TC-SMK-002', params, res);
  204 |       allure.parameter('HTTP Status',   String(res.status));
  205 |       allure.parameter('Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  206 |     });
  207 | 
  208 |     await logger.step('Step 3 â€" Validate HTTP 200 and top-level fields present', async () => {
  209 |       logger.info('Asserting: HTTP 200 and top-level fields present');
  210 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
  211 |       expect(res!.body,               'Body must be defined').toBeDefined();
> 212 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
      |                                                                       ^ Error: total_volume must be present
  213 |       expect(res!.body.success_rate,  'success_rate must be present').toBeDefined();
  214 |       expect(res!.body.total_refunds, 'total_refunds must be present').toBeDefined();
  215 |       allure.parameter('Fields present', Object.keys(res!.body).join(', '));
  216 |       logger.pass('All assertions passed');
  217 |     });
  218 | 
  219 |     await logger.step('Step 4 â€" Validate field types, ranges, and known-issues', async () => {
  220 |       logger.info('Asserting: field types, ranges, and known-issues');
  221 |       validateSummaryShape('TC-SMK-002', res!);
  222 |       logger.pass('All assertions passed');
  223 |     });
  224 | 
  225 |     await logger.step('Step 5 â€" Log key response values', async () => {
  226 |       const vol     = res!.body.total_volume   ?? {};
  227 |       const rate    = res!.body.success_rate   ?? {};
  228 |       const refunds = res!.body.total_refunds  ?? {};
  229 |       allure.parameter('total_volume.amount',           String(vol.amount));
  230 |       allure.parameter('total_volume.currency',         String(vol.currency));
  231 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  232 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  233 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  234 |       allure.parameter('success_rate.change_pct',       String(rate.change_pct ?? 'null âš  ISSUE-2'));
  235 |       allure.parameter('success_rate.change_direction', String(rate.change_direction));
  236 |       allure.parameter('total_refunds.amount',          String(refunds.amount));
  237 |       allure.parameter('total_refunds.currency',        String(refunds.currency));
  238 |       allure.parameter('total_refunds.change_direction', String(refunds.change_direction ?? 'MISSING âš  ISSUE-1'));
  239 |     });
  240 |   });
  241 | 
  242 |   // â"€â"€ TC-SMK-003 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  243 |   test('TC-SMK-003 â€" Summary: period=custom returns 200 with expected shape', async ({ logger }) => {
  244 |     smokeLabels('TC-SMK-003', 'Summary â€" Custom Range');
  245 | 
  246 |     const params = { period: 'custom', from_date: '2026-01-01', to_date: '2026-05-22' };
  247 |     let res: supertest.Response;
  248 | 
  249 |     await logger.step('Step 1 â€" Build request', async () => {
  250 |       allure.parameter('Base URL',   BASE_URL);
  251 |       allure.parameter('Endpoint',   ENDPOINT);
  252 |       allure.parameter('Method',     'GET');
  253 |       allure.parameter('period',     params.period);
  254 |       allure.parameter('from_date',  params.from_date);
  255 |       allure.parameter('to_date',    params.to_date);
  256 |       allure.parameter('Auth',       `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  257 |     });
  258 | 
  259 |     await logger.step('Step 2 â€" Send GET /summary?period=custom and log response', async () => {
  260 |       logger.info('GET /api/v1/transaction/summary?period=custom&from_date=2026-01-01&to_date=2026-05-22');
  261 |       res = await get(params);
  262 |       logger.pass('HTTP ' + res.status + ' received');
  263 |       logResponse('TC-SMK-003', params, res);
  264 |       allure.parameter('HTTP Status',   String(res.status));
  265 |       allure.parameter('Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  266 |     });
  267 | 
  268 |     await logger.step('Step 3 â€" Validate HTTP 200 and top-level fields present', async () => {
  269 |       logger.info('Asserting: HTTP 200 and top-level fields present');
  270 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
  271 |       expect(res!.body,               'Body must be defined').toBeDefined();
  272 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
  273 |       expect(res!.body.success_rate,  'success_rate must be present').toBeDefined();
  274 |       expect(res!.body.total_refunds, 'total_refunds must be present').toBeDefined();
  275 |       allure.parameter('Fields present', Object.keys(res!.body).join(', '));
  276 |       logger.pass('All assertions passed');
  277 |     });
  278 | 
  279 |     await logger.step('Step 4 â€" Validate field types, ranges, and known-issues', async () => {
  280 |       logger.info('Asserting: field types, ranges, and known-issues');
  281 |       validateSummaryShape('TC-SMK-003', res!);
  282 |       logger.pass('All assertions passed');
  283 |     });
  284 | 
  285 |     await logger.step('Step 5 â€" Validate custom range returns data for the entire date window', async () => {
  286 |       const vol = res!.body.total_volume ?? {};
  287 |       logger.info('Asserting: 5-month custom range has non-zero total_volume.amount');
  288 |       // 5-month range must have non-zero volume
  289 |       expect(vol.amount, 'Custom 5-month range must have total_volume.amount > 0').toBeGreaterThan(0);
  290 |       allure.parameter('5-month volume', `${vol.amount} ${vol.currency}`);
  291 |       logger.pass('All assertions passed');
  292 |     });
  293 | 
  294 |     await logger.step('Step 6 â€" Log key response values', async () => {
  295 |       const vol     = res!.body.total_volume   ?? {};
  296 |       const rate    = res!.body.success_rate   ?? {};
  297 |       const refunds = res!.body.total_refunds  ?? {};
  298 |       allure.parameter('total_volume.amount',           String(vol.amount));
  299 |       allure.parameter('total_volume.currency',         String(vol.currency));
  300 |       allure.parameter('total_volume.change_pct',       String(vol.change_pct));
  301 |       allure.parameter('total_volume.change_direction', String(vol.change_direction));
  302 |       allure.parameter('success_rate.rate_pct',         String(rate.rate_pct) + '%');
  303 |       allure.parameter('success_rate.change_pct',       String(rate.change_pct ?? 'null âš  ISSUE-2'));
  304 |       allure.parameter('total_refunds.amount',          String(refunds.amount));
  305 |       allure.parameter('total_refunds.change_direction', String(refunds.change_direction ?? 'MISSING âš  ISSUE-1'));
  306 |     });
  307 |   });
  308 | 
  309 | });
  310 | 
```