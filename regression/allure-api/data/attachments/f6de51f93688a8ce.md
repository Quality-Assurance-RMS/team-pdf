# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Cross-Service Consistency >> [BUG-XS-001] TC-XS-001 — Error response body schema differs across all three services
- Location: tests/API/deep-regression-api.spec.ts:98:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 401
Received: 502
```

# Test source

```ts
  46  | import supertest          from 'supertest';
  47  | import { getToken }       from '../utils/tokenHelper';
  48  | 
  49  | // ── Base URLs ─────────────────────────────────────────────────────────────────
  50  | 
  51  | const ANA_BASE = 'https://api.rms.dev.demo-fsit.com/rms-analytics';
  52  | const DB_BASE  = process.env.DASHBOARD_API_BASE_URL   ?? 'https://api.rms.dev.demo-fsit.com/dashboard-api';
  53  | const TXN_BASE = process.env.TRANSACTION_API_BASE_URL ?? 'https://api.rms.dev.demo-fsit.com/transaction-api';
  54  | 
  55  | let DB_TOKEN  = '';
  56  | let TXN_TOKEN = '';
  57  | 
  58  | test.beforeAll(async () => { DB_TOKEN = TXN_TOKEN = await getToken(); });
  59  | 
  60  | // ── HTTP helpers ──────────────────────────────────────────────────────────────
  61  | 
  62  | async function GET(base: string, path: string, token?: string, extra: Record<string,string> = {}) {
  63  |   const req = supertest(base).get(path).set('Accept', 'application/json');
  64  |   if (token !== undefined) req.set('Authorization', `Bearer ${token}`);
  65  |   Object.entries(extra).forEach(([k, v]) => req.set(k, v));
  66  |   const res = await req;
  67  |   console.log(`GET ${base}${path} → ${res.status}`);
  68  |   return res;
  69  | }
  70  | 
  71  | async function METHOD(base: string, path: string, method: 'post'|'put'|'delete'|'patch', token?: string) {
  72  |   const req = (supertest(base) as unknown as Record<string,(p:string)=>supertest.Test>)[method](path)
  73  |     .set('Accept','application/json').set('Content-Type','application/json');
  74  |   if (token !== undefined) req.set('Authorization', `Bearer ${token}`);
  75  |   const res = await req;
  76  |   console.log(`${method.toUpperCase()} ${base}${path} → ${res.status}`);
  77  |   return res;
  78  | }
  79  | 
  80  | function bugAttach(id: string, expected: string, actual: string, impact: string, fix: string) {
  81  |   allure.attachment('Bug Evidence', JSON.stringify({bug_id:id,expected,actual,impact,fix},null,2),'application/json');
  82  | }
  83  | 
  84  | function labels(suite: string, id: string, story: string, sev = 'normal') {
  85  |   allure.parentSuite('RMS Deep Regression'); allure.suite(suite);
  86  |   allure.label('testId', id); allure.label('severity', sev);
  87  |   allure.label('story', story); allure.owner('Ashil Shaji');
  88  |   allure.tags('API', 'Regression', 'DeepTest');
  89  | }
  90  | 
  91  | // ═════════════════════════════════════════════════════════════════════════════
  92  | //  1. CROSS-SERVICE CONSISTENCY
  93  | // ═════════════════════════════════════════════════════════════════════════════
  94  | 
  95  | test.describe('Cross-Service Consistency', () => {
  96  | 
  97  |   // ── TC-XS-001 ─────────────────────────────────────────────────────────────
  98  |   test('[BUG-XS-001] TC-XS-001 — Error response body schema differs across all three services', async ({ logger }) => {
  99  |     labels('Cross-Service', 'TC-XS-001', 'Error Schema Inconsistency', 'high');
  100 |     await allure.description(
  101 |       '**[BUG-XS-001]**\n\n' +
  102 |       'Each service returns a different JSON shape for error responses, making it impossible ' +
  103 |       'to write a single error-handling routine on the client.\n\n' +
  104 |       '| Service | Error Fields |\n' +
  105 |       '|---------|-------------|\n' +
  106 |       '| Analytics (405) | `{timestamp, status, error, path}` — no `message` field |\n' +
  107 |       '| Dashboard (401) | `{status, error, message}` — no `timestamp` or `path` |\n' +
  108 |       '| Transaction (401) | `{status, error, message}` — same as Dashboard |\n\n' +
  109 |       '**Expected:** Uniform error schema: `{status, error, message, timestamp, path}` across all services.\n\n' +
  110 |       '**Impact:** Frontend error-handling code must branch per-service. New services will likely ' +
  111 |       'add a 4th format. Clients cannot reliably extract a user-facing error message.',
  112 |     );
  113 | 
  114 |     await logger.step('Step 1 — Collect Analytics 405 error body', async () => {
  115 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  116 |       const res = await METHOD(ANA_BASE, '/api/v1/analytics/dashboard/summary', 'post');
  117 |       logger.pass('HTTP ' + res.status + ' received');
  118 |       allure.parameter('Analytics 405 fields', Object.keys(res.body).join(', '));
  119 |       allure.parameter('Analytics has timestamp', String('timestamp' in res.body));
  120 |       allure.parameter('Analytics has message',   String('message'   in res.body));
  121 |       allure.parameter('Analytics has path',      String('path'      in res.body));
  122 |       logger.info('Asserting: Analytics returns 405');
  123 |       expect(res.status).toBe(405);
  124 |       logger.pass('All assertions passed');
  125 |     });
  126 | 
  127 |     await logger.step('Step 2 — Collect Dashboard 401 error body', async () => {
  128 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  129 |       const res = await GET(DB_BASE, '/api/v1/dashboard/summary?period=week');
  130 |       logger.pass('HTTP ' + res.status + ' received');
  131 |       allure.parameter('Dashboard 401 fields', Object.keys(res.body).join(', '));
  132 |       allure.parameter('Dashboard has timestamp', String('timestamp' in res.body));
  133 |       allure.parameter('Dashboard has message',   String('message'   in res.body));
  134 |       allure.parameter('Dashboard has path',      String('path'      in res.body));
  135 |       logger.info('Asserting: Dashboard returns 401');
  136 |       expect(res.status).toBe(401);
  137 |       logger.pass('All assertions passed');
  138 |     });
  139 | 
  140 |     await logger.step('Step 3 — Collect Transaction 401 error body', async () => {
  141 |       logger.info('GET /api/v1/transaction/summary?period=week');
  142 |       const res = await GET(TXN_BASE, '/api/v1/transaction/summary?period=week');
  143 |       logger.pass('HTTP ' + res.status + ' received');
  144 |       allure.parameter('Transaction 401 fields', Object.keys(res.body).join(', '));
  145 |       logger.info('Asserting: Transaction returns 401');
> 146 |       expect(res.status).toBe(401);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
  147 |       logger.pass('All assertions passed');
  148 |     });
  149 | 
  150 |     await logger.step('Step 4 — Assert schema is consistent (BUG: it is not)', async () => {
  151 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  152 |       const anaRes = await METHOD(ANA_BASE, '/api/v1/analytics/dashboard/summary', 'post');
  153 |       logger.pass('HTTP ' + anaRes.status + ' received');
  154 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  155 |       const dbRes  = await GET(DB_BASE,  '/api/v1/dashboard/summary?period=week');
  156 |       logger.pass('HTTP ' + dbRes.status + ' received');
  157 |       logger.info('GET /api/v1/transaction/summary?period=week');
  158 |       const txnRes = await GET(TXN_BASE, '/api/v1/transaction/summary?period=week');
  159 |       logger.pass('HTTP ' + txnRes.status + ' received');
  160 | 
  161 |       const anaHasMsg = 'message' in anaRes.body;
  162 |       const dbHasMsg  = 'message' in dbRes.body;
  163 |       const anaHasTs  = 'timestamp' in anaRes.body;
  164 |       const dbHasTs   = 'timestamp' in dbRes.body;
  165 | 
  166 |       bugAttach('BUG-XS-001',
  167 |         'All services return {status, error, message, timestamp, path}',
  168 |         `Analytics: {${Object.keys(anaRes.body).join(',')}}, Dashboard: {${Object.keys(dbRes.body).join(',')}}`,
  169 |         'Client error handling must branch per-service; 4th format risk as more services are added',
  170 |         'Define a shared error response DTO in a common library and use it across all services',
  171 |       );
  172 | 
  173 |       logger.info('Asserting: Error schema is consistent across all services');
  174 |       expect(anaHasMsg, '[BUG-XS-001] Analytics error body must include "message" field').toBe(true);
  175 |       expect(dbHasTs,   '[BUG-XS-001] Dashboard error body must include "timestamp" field').toBe(true);
  176 |       expect(anaHasTs === dbHasTs, '[BUG-XS-001] All services must share the same error schema').toBe(true);
  177 |       allure.parameter('Schema consistent across services', String(anaHasMsg && dbHasMsg && anaHasTs === dbHasTs));
  178 |       logger.pass('All assertions passed');
  179 |     });
  180 |   });
  181 | 
  182 |   // ── TC-XS-002 ─────────────────────────────────────────────────────────────
  183 |   test('[BUG-XS-002] TC-XS-002 — Dashboard 401 leaks whether token was present (token-presence enumeration)', async ({ logger }) => {
  184 |     labels('Cross-Service', 'TC-XS-002', 'Auth Error Message Enumeration Risk', 'medium');
  185 |     await allure.description(
  186 |       '**[BUG-XS-002]**\n\n' +
  187 |       'The dashboard API returns different 401 error messages depending on whether an ' +
  188 |       'Authorization header was provided, allowing an attacker to enumerate token validity:\n\n' +
  189 |       '- No Authorization header → `"Missing Authorization token"`\n' +
  190 |       '- Invalid/expired token   → `"Invalid or expired token"`\n\n' +
  191 |       '**Expected:** A single generic message for all 401 cases, e.g. `"Authentication required"`.\n\n' +
  192 |       '**Impact:** An attacker learns whether their forged token was accepted at the auth layer ' +
  193 |       'or simply absent, helping them refine token-guessing attacks. It also confirms that the ' +
  194 |       'service separately handles "no token" and "bad token" — useful for bypassing auth probes.',
  195 |     );
  196 |     let noAuthMsg  = '';
  197 |     let badAuthMsg = '';
  198 | 
  199 |     await logger.step('Step 1 — GET dashboard summary with NO auth', async () => {
  200 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  201 |       const res = await GET(DB_BASE, '/api/v1/dashboard/summary?period=week');
  202 |       logger.pass('HTTP ' + res.status + ' received');
  203 |       logger.info('Asserting: Response is 401 without auth');
  204 |       expect(res.status).toBe(401);
  205 |       noAuthMsg = String(res.body?.message ?? '');
  206 |       allure.parameter('No-auth message', noAuthMsg);
  207 |       logger.pass('All assertions passed');
  208 |     });
  209 | 
  210 |     await logger.step('Step 2 — GET dashboard summary with INVALID token', async () => {
  211 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  212 |       const res = await GET(DB_BASE, '/api/v1/dashboard/summary?period=week', 'bad.token.here');
  213 |       logger.pass('HTTP ' + res.status + ' received');
  214 |       logger.info('Asserting: Response is 401 with invalid token');
  215 |       expect(res.status).toBe(401);
  216 |       badAuthMsg = String(res.body?.message ?? '');
  217 |       allure.parameter('Invalid-token message', badAuthMsg);
  218 |       logger.pass('All assertions passed');
  219 |     });
  220 | 
  221 |     await logger.step('Step 3 — Assert both return the same generic message (BUG: they differ)', async () => {
  222 |       bugAttach('BUG-XS-002',
  223 |         'Same generic 401 message regardless of token presence',
  224 |         `No-auth: "${noAuthMsg}" vs Invalid: "${badAuthMsg}"`,
  225 |         'Attackers can enumerate token presence via distinct 401 messages',
  226 |         'Return a single generic message "Authentication required" for all 401 scenarios',
  227 |       );
  228 |       logger.info('Asserting: Both 401 messages are identical');
  229 |       expect(noAuthMsg, '[BUG-XS-002] 401 message must be identical whether token is absent or invalid')
  230 |         .toBe(badAuthMsg);
  231 |       logger.pass('All assertions passed');
  232 |     });
  233 |   });
  234 | 
  235 |   // ── TC-XS-003 ─────────────────────────────────────────────────────────────
  236 |   test('[BUG-XS-003] TC-XS-003 — Unknown paths return 401 not 404 on dashboard and transaction APIs', async ({ logger }) => {
  237 |     labels('Cross-Service', 'TC-XS-003', 'Unknown Path Returns 401 Not 404', 'medium');
  238 |     await allure.description(
  239 |       '**[BUG-XS-003]**\n\n' +
  240 |       'When an unauthenticated request hits an unknown path, both dashboard and transaction ' +
  241 |       'APIs return 401 instead of 404. This is because the auth middleware runs before routing.\n\n' +
  242 |       '**The problem:** A developer who typos an endpoint path cannot distinguish:\n' +
  243 |       '  - "This path does not exist" (404)\n' +
  244 |       '  - "This path exists but you need auth" (401)\n\n' +
  245 |       '**Contrast:** The analytics API correctly returns 404 for unknown paths (no auth middleware).\n\n' +
  246 |       '**Expected:** Unknown paths should return 404, even to unauthenticated callers. The standard ' +
```