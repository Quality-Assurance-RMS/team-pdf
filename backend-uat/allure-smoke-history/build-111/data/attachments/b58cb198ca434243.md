# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-summary.spec.ts >> Backend (Konza) â€" Transaction Summary >> TC-BE-001 â€" Summary: period=week returns 200 with expected shape
- Location: tests/API/backend-tests/backend-summary.spec.ts:135:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  60  |   allure.attachment(
  61  |     `[${tcId}] Request & Response`,
  62  |     JSON.stringify({
  63  |       request:  { method: 'GET', url: fullUrl, params, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  64  |       response: { status: res.status, content_type: res.headers['content-type'] ?? 'N/A', body: res.body },
  65  |     }, null, 2),
  66  |     'application/json',
  67  |   );
  68  | }
  69  | 
  70  | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  71  |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  72  |   console.warn(`âš  ISSUE FLAGGED â€" ${msg}`);
  73  |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  74  | }
  75  | 
  76  | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  77  | 
  78  | function backendLabels(tcId: string, title: string) {
  79  |   allure.parentSuite('RMS Transaction API');
  80  |   allure.suite('Backend');
  81  |   allure.subSuite('Summary Endpoint');
  82  |   allure.label('testId',   tcId);
  83  |   allure.label('severity', 'critical');
  84  |   allure.owner('Ashil Shaji');
  85  |   allure.tags('Backend', 'Konza', 'API', 'Summary');
  86  |   allure.label('story', title);
  87  | }
  88  | 
  89  | // â"€â"€ Shared validators â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  90  | 
  91  | function validateSummaryShape(tcId: string, res: supertest.Response) {
  92  |   const body    = res.body;
  93  |   const vol     = body.total_volume  ?? {};
  94  |   const rate    = body.success_rate  ?? {};
  95  |   const refunds = body.total_refunds ?? {};
  96  | 
  97  |   // â"€â"€ total_volume â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  98  |   expect(typeof vol.amount,   'total_volume.amount must be a number').toBe('number');
  99  |   expect(vol.amount,          'total_volume.amount must be >= 0').toBeGreaterThanOrEqual(0);
  100 |   expect(vol.currency,        'total_volume.currency must be present').toBeTruthy();
  101 |   expect(vol.currency,        'total_volume.currency must be KES').toBe('KES');
  102 |   expect(typeof vol.change_pct, 'total_volume.change_pct must be a number').toBe('number');
  103 |   expect(VALID_CHANGE_DIRECTIONS, 'total_volume.change_direction must be up/down/flat').toContain(vol.change_direction);
  104 | 
  105 |   // â"€â"€ success_rate â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  106 |   expect(typeof rate.rate_pct, 'success_rate.rate_pct must be a number').toBe('number');
  107 |   expect(rate.rate_pct,        'success_rate.rate_pct must be >= 0').toBeGreaterThanOrEqual(0);
  108 |   expect(rate.rate_pct,        'success_rate.rate_pct must be <= 100').toBeLessThanOrEqual(100);
  109 |   expect(VALID_CHANGE_DIRECTIONS, 'success_rate.change_direction must be up/down/flat').toContain(rate.change_direction);
  110 | 
  111 |   // â"€â"€ ISSUE-2: success_rate.change_pct is always null â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  112 |   if (rate.change_pct === null) {
  113 |     flagIssue(tcId, 'ISSUE-2', 'success_rate.change_pct is null â€" change tracking not calculated for success rate', rate.change_pct);
  114 |   }
  115 |   expect.soft(rate.change_pct, '[ISSUE-2] success_rate.change_pct should be a number, not null').not.toBeNull();
  116 | 
  117 |   // â"€â"€ total_refunds â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  118 |   expect(typeof refunds.amount, 'total_refunds.amount must be a number').toBe('number');
  119 |   expect(refunds.amount,        'total_refunds.amount must be >= 0').toBeGreaterThanOrEqual(0);
  120 |   expect(refunds.currency,      'total_refunds.currency must be present').toBeTruthy();
  121 |   expect(refunds.currency,      'total_refunds.currency must be KES').toBe('KES');
  122 | 
  123 |   // â"€â"€ ISSUE-1: total_refunds is missing change_direction that total_volume has â"€
  124 |   if (!('change_direction' in refunds)) {
  125 |     flagIssue(tcId, 'ISSUE-1', 'total_refunds.change_direction field is missing â€" total_volume has it but total_refunds does not', refunds);
  126 |   }
  127 |   expect.soft(refunds, '[ISSUE-1] total_refunds should have change_direction field like total_volume does').toHaveProperty('change_direction');
  128 | }
  129 | 
  130 | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  131 | 
  132 | test.describe('Backend (Konza) â€" Transaction Summary', () => {
  133 | 
  134 |   // â"€â"€ TC-BE-001 â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  135 |   test('TC-BE-001 â€" Summary: period=week returns 200 with expected shape', async ({ logger }) => {
  136 |     backendLabels('TC-BE-001', 'Summary â€" Week');
  137 | 
  138 |     const params = { period: 'week' };
  139 |     let res: supertest.Response;
  140 | 
  141 |     await logger.step('Step 1 â€" Build request', async () => {
  142 |       allure.parameter('Base URL',  BASE_URL);
  143 |       allure.parameter('Endpoint',  ENDPOINT);
  144 |       allure.parameter('Method',    'GET');
  145 |       allure.parameter('period',    params.period);
  146 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  147 |     });
  148 | 
  149 |     await logger.step('Step 2 â€" Send GET /summary?period=week and log response', async () => {
  150 |       logger.info('GET /api/v1/transaction/summary?period=week');
  151 |       res = await get(params);
  152 |       logger.pass('HTTP ' + res.status + ' received');
  153 |       logResponse('TC-BE-001', params, res);
  154 |       allure.parameter('HTTP Status',   String(res.status));
  155 |       allure.parameter('Content-Type',  String(res.headers['content-type'] ?? 'N/A'));
  156 |     });
  157 | 
  158 |     await logger.step('Step 3 â€" Validate HTTP 200 and top-level fields present', async () => {
  159 |       logger.info('Asserting: HTTP 200 and top-level fields present');
> 160 |       expect(res!.status,             'HTTP 200 expected').toBe(200);
      |                                                            ^ Error: HTTP 200 expected
  161 |       expect(res!.body,               'Body must be defined').toBeDefined();
  162 |       expect(res!.body.total_volume,  'total_volume must be present').toBeDefined();
  163 |       expect(res!.body.success_rate,  'success_rate must be present').toBeDefined();
  164 |       expect(res!.body.total_refunds, 'total_refunds must be present').toBeDefined();
  165 |       allure.parameter('Fields present', Object.keys(res!.body).join(', '));
  166 |       logger.pass('All assertions passed');
  167 |     });
  168 | 
  169 |     await logger.step('Step 4 â€" Validate field types, ranges, and known-issues', async () => {
  170 |       logger.info('Asserting: field types, ranges, and known-issues');
  171 |       validateSummaryShape('TC-BE-001', res!);
  172 |       logger.pass('All assertions passed');
  173 |     });
  174 | 
  175 |     await logger.step('Step 5 â€" Log key response values', async () => {
  176 |       const vol     = res!.body.total_volume   ?? {};
  177 |       const rate    = res!.body.success_rate   ?? {};
  178 |       const refunds = res!.body.total_refunds  ?? {};
  179 |       allure.parameter('total_volume.amount',           String(vol.amount));
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
```