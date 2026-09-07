# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-dashboard-revenue-trend.spec.ts >> Backend (Konza) â€” Dashboard Revenue Trend >> TC-DREVT-001 â€” Happy Path: 200 + data wrapper + all 4 arrays + summary
- Location: tests/API/backend-tests/backend-dashboard-revenue-trend.spec.ts:164:7

# Error details

```
Error: "totalAttempts" array must be present

expect(received).toHaveProperty(path)

Expected path: "totalAttempts"
Received path: []

Received value: {"failed": [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, …], "labels": ["Oct 2025", "Nov 2025", "Dec 2025", "Jan 2026", "Feb 2026", "Mar 2026", "Apr 2026", "May 2026", "Jun 2026", "Jul 2026", …], "successful": [500000, 0, 404, 23000, 150000, 200000, 200002, 1000002, 3017, 7547, …], "summary": [{"kesChangeDirection": "flat", "kesChangePercentage": 0, "kesValue": 59984146, "label": "Total Transactions", "transactionCountKes": 3017875, "transactionCountUsd": 737663, "usdChangeDirection": "flat", "usdChangePercentage": 0, "usdValue": 11788077}, {"kesChangeDirection": "flat", "kesChangePercentage": 0, "kesValue": 59984146, "label": "Successful", "transactionCountKes": 3017875, "transactionCountUsd": 737663, "usdChangeDirection": "flat", "usdChangePercentage": 0, "usdValue": 11788077}, {"kesChangeDirection": "flat", "kesChangePercentage": 0, "kesValue": 0, "label": "Failed", "transactionCountKes": 0, "transactionCountUsd": 0, "usdChangeDirection": "flat", "usdChangePercentage": 0, "usdValue": 0}], "totalTransactions": [500000, 0, 404, 23000, 150000, 200000, 200002, 1000002, 3017, 7547, …]}
```

# Test source

```ts
  107 |     const dupes = d.labels?.filter((l, i) => d.labels.indexOf(l) !== i) ?? [];
  108 |     if (dupes.length) console.warn(`  âš  BUG-REVT-04: Duplicate labels: [${dupes.join(', ')}]`);
  109 |     console.log(`  â–º totalAttempts : [${d.totalAttempts?.join(', ')}]`);
  110 |     console.log(`  â–º successful    : [${d.successful?.join(', ')}]`);
  111 |     console.log(`  â–º failed        : [${d.failed?.join(', ')}]`);
  112 |     d.summary?.forEach((s, i) => {
  113 |       console.log(`  â–º summary[${i}]    : label="${s.label}" value="${s.value}" points=${JSON.stringify(s.points)}(${typeof s.points}) isPostive=${s.isPostive} change="${s.change}"`);
  114 |       if ('isPostive' in s && !('isPositive' in s)) console.warn(`    âš  BUG-REVT-01: "isPostive" typo in summary[${i}]`);
  115 |       if (typeof s.points === 'string') console.warn(`    âš  BUG-REVT-02: points is string "${s.points}", should be number`);
  116 |       if (typeof s.value  === 'string') console.warn(`    âš  BUG-REVT-03: value is formatted string "${s.value}", raw number missing`);
  117 |       if (s.change === '+0%') console.warn(`    âš  BUG-REVT-05: change="+0%" may be a calculation bug`);
  118 |     });
  119 |   }
  120 |   console.log(`  â–º Cache-Control      : ${res.headers['cache-control'] ?? 'MISSING'}`);
  121 |   console.log(`  â–º X-Content-Type-Opt : ${res.headers['x-content-type-options'] ?? 'MISSING'}`);
  122 |   console.log(`  â–º X-Frame-Options    : ${res.headers['x-frame-options'] ?? 'MISSING'}`);
  123 |   console.log(`  â–º ACAO               : ${res.headers['access-control-allow-origin'] ?? 'MISSING'}`);
  124 |   console.log(`${'â”€'.repeat(70)}\n`);
  125 | }
  126 | 
  127 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  128 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  129 |   console.warn(`âš  ISSUE FLAGGED â€” ${msg}`);
  130 |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  131 | }
  132 | 
  133 | function attachResponse(tcId: string, res: supertest.Response, extra: Record<string, unknown> = {}) {
  134 |   allure.attachment(
  135 |     `[${tcId}] Request & Response`,
  136 |     JSON.stringify({
  137 |       request:  { method: 'GET', url: `${BASE_URL}${ENDPOINT}`, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  138 |       response: { status: res.status, content_type: res.headers['content-type'], headers: res.headers, body: res.body, ...extra },
  139 |     }, null, 2),
  140 |     'application/json',
  141 |   );
  142 | }
  143 | 
  144 | // â”€â”€ Allure labels â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  145 | 
  146 | function backendLabels(tcId: string, title: string, severity: 'blocker' | 'critical' | 'normal' | 'minor' = 'critical') {
  147 |   allure.parentSuite('RMS Dashboard API');
  148 |   allure.suite('Backend');
  149 |   allure.subSuite('Revenue Trend Endpoint');
  150 |   allure.label('testId',   tcId);
  151 |   allure.label('severity', severity);
  152 |   allure.owner('Ashil Shaji');
  153 |   allure.tags('Backend', 'Konza', 'API', 'RevenueTrend');
  154 |   allure.label('story', title);
  155 | }
  156 | 
  157 | // â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
  158 | // Suite
  159 | // â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
  160 | 
  161 | test.describe('Backend (Konza) â€” Dashboard Revenue Trend', () => {
  162 | 
  163 |   // â”€â”€ TC-DREVT-001 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  164 |   test('TC-DREVT-001 â€” Happy Path: 200 + data wrapper + all 4 arrays + summary', async ({ logger }) => {
  165 |     backendLabels('TC-DREVT-001', 'Happy Path â€” Revenue Trend');
  166 | 
  167 |     let res: supertest.Response;
  168 | 
  169 |     await logger.step('Step 1 â€” Build request', async () => {
  170 |       allure.parameter('Base URL',  BASE_URL);
  171 |       allure.parameter('Endpoint',  ENDPOINT);
  172 |       allure.parameter('Method',    'GET');
  173 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  174 |     });
  175 | 
  176 |     await logger.step('Step 2 â€” Send GET /revenue-trend and log response', async () => {
  177 |       logger.info(`GET ${ENDPOINT}`);
  178 |       res = await get();
  179 |       logger.pass('HTTP ' + res.status + ' received');
  180 |       logResponse('TC-DREVT-001', res);
  181 |       attachResponse('TC-DREVT-001', res);
  182 |       allure.parameter('HTTP Status',  String(res.status));
  183 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  184 |     });
  185 | 
  186 |     await logger.step('Step 3 â€” Validate HTTP 200 and Content-Type', async () => {
  187 |       logger.info('Asserting: Validate HTTP 200 and Content-Type');
  188 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  189 |       expect(res!.body,   'Body must be defined').toBeDefined();
  190 |       expect(res!.headers['content-type'], 'Content-Type must be application/json').toContain('application/json');
  191 |       logger.pass('All assertions passed');
  192 |     });
  193 | 
  194 |     await logger.step('Step 4 â€” Validate top-level data wrapper', async () => {
  195 |       logger.info('Asserting: Validate top-level data wrapper');
  196 |       expect(res!.body, 'Response must have "data" key').toHaveProperty('data');
  197 |       const d = res!.body.data as RevenueTrendData;
  198 |       expect(d, '"data" must be an object').toBeInstanceOf(Object);
  199 |       allure.parameter('data keys', Object.keys(d).join(', '));
  200 |       logger.pass('All assertions passed');
  201 |     });
  202 | 
  203 |     await logger.step('Step 5 â€” Validate 4 required array fields exist', async () => {
  204 |       const d = res!.body.data as RevenueTrendData;
  205 |       logger.info('Asserting: Validate 4 required array fields exist');
  206 |       expect(d, '"labels" array must be present').toHaveProperty('labels');
> 207 |       expect(d, '"totalAttempts" array must be present').toHaveProperty('totalAttempts');
      |                                                          ^ Error: "totalAttempts" array must be present
  208 |       expect(d, '"successful" array must be present').toHaveProperty('successful');
  209 |       expect(d, '"failed" array must be present').toHaveProperty('failed');
  210 | 
  211 |       expect(Array.isArray(d.labels),        '"labels" must be an array').toBe(true);
  212 |       expect(Array.isArray(d.totalAttempts), '"totalAttempts" must be an array').toBe(true);
  213 |       expect(Array.isArray(d.successful),    '"successful" must be an array').toBe(true);
  214 |       expect(Array.isArray(d.failed),        '"failed" must be an array').toBe(true);
  215 | 
  216 |       allure.parameter('labels count',        String(d.labels.length));
  217 |       allure.parameter('totalAttempts count', String(d.totalAttempts.length));
  218 |       allure.parameter('successful count',    String(d.successful.length));
  219 |       allure.parameter('failed count',        String(d.failed.length));
  220 |       logger.pass('All assertions passed');
  221 |     });
  222 | 
  223 |     await logger.step('Step 6 â€” Validate summary array structure', async () => {
  224 |       const d = res!.body.data as RevenueTrendData;
  225 |       logger.info('Asserting: Validate summary array structure');
  226 |       expect(d, '"summary" must be present').toHaveProperty('summary');
  227 |       expect(Array.isArray(d.summary), '"summary" must be an array').toBe(true);
  228 |       expect(d.summary.length, '"summary" must have exactly 3 items').toBe(3);
  229 | 
  230 |       const expectedLabels = ['Total Attempts', 'Successful', 'Failed'];
  231 |       d.summary.forEach((item, i) => {
  232 |         expect(item, `summary[${i}] must have "label"`).toHaveProperty('label');
  233 |         expect(item, `summary[${i}] must have "value"`).toHaveProperty('value');
  234 |         expect(item, `summary[${i}] must have "change"`).toHaveProperty('change');
  235 |         allure.parameter(`summary[${i}].label`, String(item.label));
  236 |       });
  237 | 
  238 |       const actualLabels = d.summary.map(s => s.label);
  239 |       for (const lbl of expectedLabels) {
  240 |         expect(actualLabels, `summary must include "${lbl}"`).toContain(lbl);
  241 |       }
  242 |       logger.pass('All assertions passed');
  243 |     });
  244 |   });
  245 | 
  246 |   // â”€â”€ TC-DREVT-002 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  247 |   test('TC-DREVT-002 â€” Auth Guard: missing token â†’ 401, wrong token â†’ 401', async ({ logger }) => {
  248 |     backendLabels('TC-DREVT-002', 'Auth Guard', 'blocker');
  249 | 
  250 |     let resNoAuth:    supertest.Response;
  251 |     let resWrongAuth: supertest.Response;
  252 | 
  253 |     await logger.step('Step 1 â€” No Authorization header', async () => {
  254 |       logger.info(`GET ${ENDPOINT}`);
  255 |       resNoAuth = await supertest(BASE_URL).get(ENDPOINT).set('Accept', 'application/json');
  256 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  257 |       console.log(`[TC-DREVT-002] No-auth: ${resNoAuth.status} â€” ${JSON.stringify(resNoAuth.body)}`);
  258 |       allure.parameter('No-auth status',  String(resNoAuth.status));
  259 |       allure.parameter('No-auth message', String(resNoAuth.body?.message ?? 'N/A'));
  260 |     });
  261 | 
  262 |     await logger.step('Step 2 â€” Invalid Bearer token', async () => {
  263 |       logger.info(`GET ${ENDPOINT}`);
  264 |       resWrongAuth = await supertest(BASE_URL)
  265 |         .get(ENDPOINT)
  266 |         .set('Authorization', 'Bearer invalidtoken.abc.xyz')
  267 |         .set('Accept', 'application/json');
  268 |       logger.pass('HTTP ' + resWrongAuth.status + ' received');
  269 |       console.log(`[TC-DREVT-002] Wrong-auth: ${resWrongAuth.status} â€” ${JSON.stringify(resWrongAuth.body)}`);
  270 |       allure.parameter('Wrong-auth status',  String(resWrongAuth.status));
  271 |       allure.parameter('Wrong-auth message', String(resWrongAuth.body?.message ?? 'N/A'));
  272 |     });
  273 | 
  274 |     await logger.step('Step 3 â€” Both must return 401', async () => {
  275 |       logger.info('Asserting: response structure and values');
  276 |       expect(resNoAuth!.status,    'Missing token must return 401').toBe(401);
  277 |       expect(resWrongAuth!.status, 'Invalid token must return 401').toBe(401);
  278 |       logger.pass('All assertions passed');
  279 |     });
  280 | 
  281 |     await logger.step('Step 4 â€” 401 error body must include status and message', async () => {
  282 |       logger.info('Asserting: response structure and values');
  283 |       expect(resNoAuth!.body.status,    'No-auth body.status must be 401').toBe(401);
  284 |       expect(resWrongAuth!.body.status, 'Wrong-auth body.status must be 401').toBe(401);
  285 |       expect(resNoAuth!.body.message,   'No-auth message must be "Missing Authorization token"').toBe('Missing Authorization token');
  286 |       expect(resWrongAuth!.body.message,'Wrong-auth message must be "Invalid or expired token"').toBe('Invalid or expired token');
  287 |       logger.pass('All assertions passed');
  288 |     });
  289 |   });
  290 | 
  291 |   // â”€â”€ TC-DREVT-003 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  292 |   test('TC-DREVT-003 â€” BUG-REVT-01: summary items must use "isPositive", not "isPostive"', async ({ logger }) => {
  293 |     backendLabels('TC-DREVT-003', 'BUG-REVT-01 â€” isPostive Typo in summary', 'critical');
  294 | 
  295 |     let res: supertest.Response;
  296 | 
  297 |     await logger.step('Step 1 â€” Fetch revenue-trend response', async () => {
  298 |       logger.info(`GET ${ENDPOINT}`);
  299 |       res = await get();
  300 |       logger.pass('HTTP ' + res.status + ' received');
  301 |       attachResponse('TC-DREVT-003', res, { bug: 'BUG-REVT-01' });
  302 |     });
  303 | 
  304 |     await logger.step('Step 2 â€” Validate HTTP 200', async () => {
  305 |       logger.info('Asserting: Validate HTTP 200');
  306 |       expect(res!.status).toBe(200);
  307 |       logger.pass('All assertions passed');
```