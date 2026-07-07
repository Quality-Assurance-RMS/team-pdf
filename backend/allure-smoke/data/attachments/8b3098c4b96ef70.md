# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-summary.spec.ts >> Smoke â€" Transaction Summary >> TC-SMK-001 â€" Summary: period=week returns 200 with expected shape
- Location: tests/API/smoke/smoke-summary.spec.ts:127:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  52  |   allure.attachment(
  53  |     `[${tcId}] Request & Response`,
  54  |     JSON.stringify({
  55  |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  56  |       response: { status: res.status, content_type: res.headers['content-type'] ?? 'N/A', body: res.body },
  57  |     }, null, 2),
  58  |     'application/json',
  59  |   );
  60  | }
  61  | 
  62  | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  63  |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  64  |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  65  |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  66  | }
  67  | 
  68  | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  69  | 
  70  | function smokeLabels(tcId: string, title: string) {
  71  |   allure.parentSuite('RMS Transaction API');
  72  |   allure.suite('Smoke');
  73  |   allure.subSuite('Summary Endpoint');
  74  |   allure.label('testId',   tcId);
  75  |   allure.label('severity', 'critical');
  76  |   allure.owner('Ashil Shaji');
  77  |   allure.tags('Smoke', 'API', 'Summary');
  78  |   allure.label('story', title);
  79  | }
  80  | 
  81  | // â"€â"€ Shared validators â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  82  | 
  83  | function validateSummaryShape(tcId: string, res: supertest.Response) {
  84  |   const body    = res.body;
  85  |   const vol     = body.total_volume  ?? {};
  86  |   const rate    = body.success_rate  ?? {};
  87  |   const refunds = body.total_refunds ?? {};
  88  | 
  89  |   // â"€â"€ total_volume â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  90  |   expect(typeof vol.amount,   'total_volume.amount must be a number').toBe('number');
  91  |   expect(vol.amount,          'total_volume.amount must be >= 0').toBeGreaterThanOrEqual(0);
  92  |   expect(vol.currency,        'total_volume.currency must be present').toBeTruthy();
  93  |   expect(vol.currency,        'total_volume.currency must be KES').toBe('KES');
  94  |   expect(typeof vol.change_pct, 'total_volume.change_pct must be a number').toBe('number');
  95  |   expect(VALID_CHANGE_DIRECTIONS, 'total_volume.change_direction must be up/down/flat').toContain(vol.change_direction);
  96  | 
  97  |   // â"€â"€ success_rate â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  98  |   expect(typeof rate.rate_pct, 'success_rate.rate_pct must be a number').toBe('number');
  99  |   expect(rate.rate_pct,        'success_rate.rate_pct must be >= 0').toBeGreaterThanOrEqual(0);
  100 |   expect(rate.rate_pct,        'success_rate.rate_pct must be <= 100').toBeLessThanOrEqual(100);
  101 |   expect(VALID_CHANGE_DIRECTIONS, 'success_rate.change_direction must be up/down/flat').toContain(rate.change_direction);
  102 | 
  103 |   // â"€â"€ ISSUE-2: success_rate.change_pct is always null â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  104 |   if (rate.change_pct === null) {
  105 |     flagIssue(tcId, 'ISSUE-2', 'success_rate.change_pct is null â€" change tracking not calculated for success rate', rate.change_pct);
  106 |   }
  107 |   expect.soft(rate.change_pct, '[ISSUE-2] success_rate.change_pct should be a number, not null').not.toBeNull();
  108 | 
  109 |   // â"€â"€ total_refunds â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  110 |   expect(typeof refunds.amount, 'total_refunds.amount must be a number').toBe('number');
  111 |   expect(refunds.amount,        'total_refunds.amount must be >= 0').toBeGreaterThanOrEqual(0);
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
> 152 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
      |                                                            ^ Error: HTTP 200 expected
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
  212 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
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
```