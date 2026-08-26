# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-001 â€” Happy Path: 200 + data wrapper + all 4 arrays + summary
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:156:7

# Error details

```
Error: HTTP 200 expected

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 503
```

# Test source

```ts
  80  | function getWithMethod(method: 'post' | 'put' | 'delete' | 'patch') {
  81  |   return (supertest(BASE_URL) as unknown as Record<string, (p: string) => supertest.Test>)
  82  |     [method](ENDPOINT)
  83  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  84  |     .set('Accept', 'application/json');
  85  | }
  86  | 
  87  | // â”€â”€ Logger â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  88  | 
  89  | function logResponse(tcId: string, res: supertest.Response, label = '') {
  90  |   const body = res.body as RevenueTrendBody;
  91  |   const d    = body?.data;
  92  | 
  93  |   console.log(`\n${'â”€'.repeat(70)}`);
  94  |   console.log(`[${tcId}]${label ? ' ' + label : ''} GET ${BASE_URL}${ENDPOINT}`);
  95  |   console.log(`  â–º HTTP Status   : ${res.status}`);
  96  |   console.log(`  â–º Content-Type  : ${res.headers['content-type'] ?? 'N/A'}`);
  97  |   if (d) {
  98  |     console.log(`  â–º labels        : [${d.labels?.join(', ')}]  (${d.labels?.length} entries)`);
  99  |     const dupes = d.labels?.filter((l, i) => d.labels.indexOf(l) !== i) ?? [];
  100 |     if (dupes.length) console.warn(`  âš  BUG-REVT-04: Duplicate labels: [${dupes.join(', ')}]`);
  101 |     console.log(`  â–º totalAttempts : [${d.totalAttempts?.join(', ')}]`);
  102 |     console.log(`  â–º successful    : [${d.successful?.join(', ')}]`);
  103 |     console.log(`  â–º failed        : [${d.failed?.join(', ')}]`);
  104 |     d.summary?.forEach((s, i) => {
  105 |       console.log(`  â–º summary[${i}]    : label="${s.label}" value="${s.value}" points=${JSON.stringify(s.points)}(${typeof s.points}) isPostive=${s.isPostive} change="${s.change}"`);
  106 |       if ('isPostive' in s && !('isPositive' in s)) console.warn(`    âš  BUG-REVT-01: "isPostive" typo in summary[${i}]`);
  107 |       if (typeof s.points === 'string') console.warn(`    âš  BUG-REVT-02: points is string "${s.points}", should be number`);
  108 |       if (typeof s.value  === 'string') console.warn(`    âš  BUG-REVT-03: value is formatted string "${s.value}", raw number missing`);
  109 |       if (s.change === '+0%') console.warn(`    âš  BUG-REVT-05: change="+0%" may be a calculation bug`);
  110 |     });
  111 |   }
  112 |   console.log(`  â–º Cache-Control      : ${res.headers['cache-control'] ?? 'MISSING'}`);
  113 |   console.log(`  â–º X-Content-Type-Opt : ${res.headers['x-content-type-options'] ?? 'MISSING'}`);
  114 |   console.log(`  â–º X-Frame-Options    : ${res.headers['x-frame-options'] ?? 'MISSING'}`);
  115 |   console.log(`  â–º ACAO               : ${res.headers['access-control-allow-origin'] ?? 'MISSING'}`);
  116 |   console.log(`${'â”€'.repeat(70)}\n`);
  117 | }
  118 | 
  119 | function flagIssue(tcId: string, issueId: string, description: string, actual: unknown) {
  120 |   const msg = `[${tcId}] ${issueId}: ${description} | Actual: ${JSON.stringify(actual)}`;
  121 |   console.warn(`âš  ISSUE FLAGGED â€” ${msg}`);
  122 |   allure.attachment(`âš  ${issueId}`, msg, 'text/plain');
  123 | }
  124 | 
  125 | function attachResponse(tcId: string, res: supertest.Response, extra: Record<string, unknown> = {}) {
  126 |   allure.attachment(
  127 |     `[${tcId}] Request & Response`,
  128 |     JSON.stringify({
  129 |       request:  { method: 'GET', url: `${BASE_URL}${ENDPOINT}`, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  130 |       response: { status: res.status, content_type: res.headers['content-type'], headers: res.headers, body: res.body, ...extra },
  131 |     }, null, 2),
  132 |     'application/json',
  133 |   );
  134 | }
  135 | 
  136 | // â”€â”€ Allure labels â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  137 | 
  138 | function smokeLabels(tcId: string, title: string, severity: 'blocker' | 'critical' | 'normal' | 'minor' = 'critical') {
  139 |   allure.parentSuite('RMS Dashboard API');
  140 |   allure.suite('Smoke');
  141 |   allure.subSuite('Revenue Trend Endpoint');
  142 |   allure.label('testId',   tcId);
  143 |   allure.label('severity', severity);
  144 |   allure.owner('Ashil Shaji');
  145 |   allure.tags('Smoke', 'API', 'RevenueTrend');
  146 |   allure.label('story', title);
  147 | }
  148 | 
  149 | // â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
  150 | // Suite
  151 | // â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
  152 | 
  153 | test.describe('Smoke â€” Dashboard Revenue Trend', () => {
  154 | 
  155 |   // â”€â”€ TC-DREVT-001 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  156 |   test('TC-DREVT-001 â€” Happy Path: 200 + data wrapper + all 4 arrays + summary', async ({ logger }) => {
  157 |     smokeLabels('TC-DREVT-001', 'Happy Path â€” Revenue Trend');
  158 | 
  159 |     let res: supertest.Response;
  160 | 
  161 |     await logger.step('Step 1 â€” Build request', async () => {
  162 |       allure.parameter('Base URL',  BASE_URL);
  163 |       allure.parameter('Endpoint',  ENDPOINT);
  164 |       allure.parameter('Method',    'GET');
  165 |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  166 |     });
  167 | 
  168 |     await logger.step('Step 2 â€” Send GET /revenue-trend and log response', async () => {
  169 |       logger.info(`GET ${ENDPOINT}`);
  170 |       res = await get();
  171 |       logger.pass('HTTP ' + res.status + ' received');
  172 |       logResponse('TC-DREVT-001', res);
  173 |       attachResponse('TC-DREVT-001', res);
  174 |       allure.parameter('HTTP Status',  String(res.status));
  175 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  176 |     });
  177 | 
  178 |     await logger.step('Step 3 â€” Validate HTTP 200 and Content-Type', async () => {
  179 |       logger.info('Asserting: Validate HTTP 200 and Content-Type');
> 180 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
      |                                                ^ Error: HTTP 200 expected
  181 |       expect(res!.body,   'Body must be defined').toBeDefined();
  182 |       expect(res!.headers['content-type'], 'Content-Type must be application/json').toContain('application/json');
  183 |       logger.pass('All assertions passed');
  184 |     });
  185 | 
  186 |     await logger.step('Step 4 â€” Validate top-level data wrapper', async () => {
  187 |       logger.info('Asserting: Validate top-level data wrapper');
  188 |       expect(res!.body, 'Response must have "data" key').toHaveProperty('data');
  189 |       const d = res!.body.data as RevenueTrendData;
  190 |       expect(d, '"data" must be an object').toBeInstanceOf(Object);
  191 |       allure.parameter('data keys', Object.keys(d).join(', '));
  192 |       logger.pass('All assertions passed');
  193 |     });
  194 | 
  195 |     await logger.step('Step 5 â€” Validate 4 required array fields exist', async () => {
  196 |       const d = res!.body.data as RevenueTrendData;
  197 |       logger.info('Asserting: Validate 4 required array fields exist');
  198 |       expect(d, '"labels" array must be present').toHaveProperty('labels');
  199 |       expect(d, '"totalAttempts" array must be present').toHaveProperty('totalAttempts');
  200 |       expect(d, '"successful" array must be present').toHaveProperty('successful');
  201 |       expect(d, '"failed" array must be present').toHaveProperty('failed');
  202 | 
  203 |       expect(Array.isArray(d.labels),        '"labels" must be an array').toBe(true);
  204 |       expect(Array.isArray(d.totalAttempts), '"totalAttempts" must be an array').toBe(true);
  205 |       expect(Array.isArray(d.successful),    '"successful" must be an array').toBe(true);
  206 |       expect(Array.isArray(d.failed),        '"failed" must be an array').toBe(true);
  207 | 
  208 |       allure.parameter('labels count',        String(d.labels.length));
  209 |       allure.parameter('totalAttempts count', String(d.totalAttempts.length));
  210 |       allure.parameter('successful count',    String(d.successful.length));
  211 |       allure.parameter('failed count',        String(d.failed.length));
  212 |       logger.pass('All assertions passed');
  213 |     });
  214 | 
  215 |     await logger.step('Step 6 â€” Validate summary array structure', async () => {
  216 |       const d = res!.body.data as RevenueTrendData;
  217 |       logger.info('Asserting: Validate summary array structure');
  218 |       expect(d, '"summary" must be present').toHaveProperty('summary');
  219 |       expect(Array.isArray(d.summary), '"summary" must be an array').toBe(true);
  220 |       expect(d.summary.length, '"summary" must have exactly 3 items').toBe(3);
  221 | 
  222 |       const expectedLabels = ['Total Attempts', 'Successful', 'Failed'];
  223 |       d.summary.forEach((item, i) => {
  224 |         expect(item, `summary[${i}] must have "label"`).toHaveProperty('label');
  225 |         expect(item, `summary[${i}] must have "value"`).toHaveProperty('value');
  226 |         expect(item, `summary[${i}] must have "change"`).toHaveProperty('change');
  227 |         allure.parameter(`summary[${i}].label`, String(item.label));
  228 |       });
  229 | 
  230 |       const actualLabels = d.summary.map(s => s.label);
  231 |       for (const lbl of expectedLabels) {
  232 |         expect(actualLabels, `summary must include "${lbl}"`).toContain(lbl);
  233 |       }
  234 |       logger.pass('All assertions passed');
  235 |     });
  236 |   });
  237 | 
  238 |   // â”€â”€ TC-DREVT-002 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  239 |   test('TC-DREVT-002 â€” Auth Guard: missing token â†’ 401, wrong token â†’ 401', async ({ logger }) => {
  240 |     smokeLabels('TC-DREVT-002', 'Auth Guard', 'blocker');
  241 | 
  242 |     let resNoAuth:    supertest.Response;
  243 |     let resWrongAuth: supertest.Response;
  244 | 
  245 |     await logger.step('Step 1 â€” No Authorization header', async () => {
  246 |       logger.info(`GET ${ENDPOINT}`);
  247 |       resNoAuth = await supertest(BASE_URL).get(ENDPOINT).set('Accept', 'application/json');
  248 |       logger.pass('HTTP ' + resNoAuth.status + ' received');
  249 |       console.log(`[TC-DREVT-002] No-auth: ${resNoAuth.status} â€” ${JSON.stringify(resNoAuth.body)}`);
  250 |       allure.parameter('No-auth status',  String(resNoAuth.status));
  251 |       allure.parameter('No-auth message', String(resNoAuth.body?.message ?? 'N/A'));
  252 |     });
  253 | 
  254 |     await logger.step('Step 2 â€” Invalid Bearer token', async () => {
  255 |       logger.info(`GET ${ENDPOINT}`);
  256 |       resWrongAuth = await supertest(BASE_URL)
  257 |         .get(ENDPOINT)
  258 |         .set('Authorization', 'Bearer invalidtoken.abc.xyz')
  259 |         .set('Accept', 'application/json');
  260 |       logger.pass('HTTP ' + resWrongAuth.status + ' received');
  261 |       console.log(`[TC-DREVT-002] Wrong-auth: ${resWrongAuth.status} â€” ${JSON.stringify(resWrongAuth.body)}`);
  262 |       allure.parameter('Wrong-auth status',  String(resWrongAuth.status));
  263 |       allure.parameter('Wrong-auth message', String(resWrongAuth.body?.message ?? 'N/A'));
  264 |     });
  265 | 
  266 |     await logger.step('Step 3 â€” Both must return 401', async () => {
  267 |       logger.info('Asserting: response structure and values');
  268 |       expect(resNoAuth!.status,    'Missing token must return 401').toBe(401);
  269 |       expect(resWrongAuth!.status, 'Invalid token must return 401').toBe(401);
  270 |       logger.pass('All assertions passed');
  271 |     });
  272 | 
  273 |     await logger.step('Step 4 â€” 401 error body must include status and message', async () => {
  274 |       logger.info('Asserting: response structure and values');
  275 |       expect(resNoAuth!.body.status,    'No-auth body.status must be 401').toBe(401);
  276 |       expect(resWrongAuth!.body.status, 'Wrong-auth body.status must be 401').toBe(401);
  277 |       expect(resNoAuth!.body.message,   'No-auth message must be "Missing Authorization token"').toBe('Missing Authorization token');
  278 |       expect(resWrongAuth!.body.message,'Wrong-auth message must be "Invalid or expired token"').toBe('Invalid or expired token');
  279 |       logger.pass('All assertions passed');
  280 |     });
```