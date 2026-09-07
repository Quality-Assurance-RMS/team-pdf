# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Cross-Service Consistency >> [BUG-XS-001] TC-XS-001 — Error response body schema differs across all three services
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:106:7

# Error details

```
Error: expect(received).toBe(expected) // Object.is equality

Expected: 405
Received: 502
```

# Test source

```ts
  31  |  *  BUG-TXN-001  Transaction list page=-1 does not return 400
  32  |  *  BUG-TXN-002  Transaction list size=0 returns 200 with empty data (should be 400)
  33  |  *  BUG-TXN-003  Transaction detail for non-existent UUID returns non-404
  34  |  *  BUG-TXN-004  Custom period without from_date/to_date returns 200 not 400
  35  |  *  BUG-TXN-005  success_rate.change_pct is always null (no change tracking)
  36  |  *  BUG-TXN-006  total_refunds missing change_direction field
  37  |  *
  38  |  * Token note:
  39  |  *   Dashboard and Transaction tests require a valid RMS Bearer token.
  40  |  *   Update DASHBOARD_API_TOKEN and TRANSACTION_API_TOKEN in .env before running.
  41  |  *   Analytics tests need NO token (auth bypass confirmed — BUG-ASUM-01).
  42  |  */
  43  | 
  44  | /**
  45  |  * NOTE (Konza migration): mirrored from the AWS environment spec for parity testing.
  46  |  * Some fixture values (transaction IDs, search keywords, date ranges) were captured
  47  |  * against the AWS demo-fsit dataset and may need to be updated to match real data
  48  |  * available in the Konza UAT environment before this suite is executed.
  49  |  */
  50  | 
  51  | 
  52  | import { test, expect } from '../fixtures';
  53  | import { allure }        from 'allure-playwright';
  54  | import supertest          from 'supertest';
  55  | import { getKonzaToken }       from '../../utils/konzaTokenHelper';
  56  | 
  57  | // ── Base URLs ─────────────────────────────────────────────────────────────────
  58  | 
  59  | const ANA_BASE = process.env.KONZA_ANALYTICS_API_BASE_URL ?? 'https://rms.uat.konza/rms-analytics';
  60  | const DB_BASE  = process.env.KONZA_DASHBOARD_API_BASE_URL   ?? 'https://rms.uat.konza/dashboard-api';
  61  | const TXN_BASE = process.env.KONZA_TRANSACTION_API_BASE_URL ?? 'https://rms.uat.konza/transaction-api';
  62  | 
  63  | let DB_TOKEN  = '';
  64  | let TXN_TOKEN = '';
  65  | 
  66  | test.beforeAll(async () => { DB_TOKEN = TXN_TOKEN = await getKonzaToken(); });
  67  | 
  68  | // ── HTTP helpers ──────────────────────────────────────────────────────────────
  69  | 
  70  | async function GET(base: string, path: string, token?: string, extra: Record<string,string> = {}) {
  71  |   const req = supertest(base).get(path).set('Accept', 'application/json');
  72  |   if (token !== undefined) req.set('Authorization', `Bearer ${token}`);
  73  |   Object.entries(extra).forEach(([k, v]) => req.set(k, v));
  74  |   const res = await req;
  75  |   console.log(`GET ${base}${path} → ${res.status}`);
  76  |   return res;
  77  | }
  78  | 
  79  | async function METHOD(base: string, path: string, method: 'post'|'put'|'delete'|'patch', token?: string) {
  80  |   const req = (supertest(base) as unknown as Record<string,(p:string)=>supertest.Test>)[method](path)
  81  |     .set('Accept','application/json').set('Content-Type','application/json');
  82  |   if (token !== undefined) req.set('Authorization', `Bearer ${token}`);
  83  |   const res = await req;
  84  |   console.log(`${method.toUpperCase()} ${base}${path} → ${res.status}`);
  85  |   return res;
  86  | }
  87  | 
  88  | function bugAttach(id: string, expected: string, actual: string, impact: string, fix: string) {
  89  |   allure.attachment('Bug Evidence', JSON.stringify({bug_id:id,expected,actual,impact,fix},null,2),'application/json');
  90  | }
  91  | 
  92  | function labels(suite: string, id: string, story: string, sev = 'normal') {
  93  |   allure.parentSuite('RMS Deep Regression (Konza)'); allure.suite(suite);
  94  |   allure.label('testId', id); allure.label('severity', sev);
  95  |   allure.label('story', story); allure.owner('Ashil Shaji');
  96  |   allure.tags('API', 'Regression', 'DeepTest', 'Konza');
  97  | }
  98  | 
  99  | // ═════════════════════════════════════════════════════════════════════════════
  100 | //  1. CROSS-SERVICE CONSISTENCY
  101 | // ═════════════════════════════════════════════════════════════════════════════
  102 | 
  103 | test.describe('Cross-Service Consistency', () => {
  104 | 
  105 |   // ── TC-XS-001 ─────────────────────────────────────────────────────────────
  106 |   test('[BUG-XS-001] TC-XS-001 — Error response body schema differs across all three services', async ({ logger }) => {
  107 |     labels('Cross-Service', 'TC-XS-001', 'Error Schema Inconsistency', 'high');
  108 |     await allure.description(
  109 |       '**[BUG-XS-001]**\n\n' +
  110 |       'Each service returns a different JSON shape for error responses, making it impossible ' +
  111 |       'to write a single error-handling routine on the client.\n\n' +
  112 |       '| Service | Error Fields |\n' +
  113 |       '|---------|-------------|\n' +
  114 |       '| Analytics (405) | `{timestamp, status, error, path}` — no `message` field |\n' +
  115 |       '| Dashboard (401) | `{status, error, message}` — no `timestamp` or `path` |\n' +
  116 |       '| Transaction (401) | `{status, error, message}` — same as Dashboard |\n\n' +
  117 |       '**Expected:** Uniform error schema: `{status, error, message, timestamp, path}` across all services.\n\n' +
  118 |       '**Impact:** Frontend error-handling code must branch per-service. New services will likely ' +
  119 |       'add a 4th format. Clients cannot reliably extract a user-facing error message.',
  120 |     );
  121 | 
  122 |     await logger.step('Step 1 — Collect Analytics 405 error body', async () => {
  123 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  124 |       const res = await METHOD(ANA_BASE, '/api/v1/analytics/dashboard/summary', 'post');
  125 |       logger.pass('HTTP ' + res.status + ' received');
  126 |       allure.parameter('Analytics 405 fields', Object.keys(res.body).join(', '));
  127 |       allure.parameter('Analytics has timestamp', String('timestamp' in res.body));
  128 |       allure.parameter('Analytics has message',   String('message'   in res.body));
  129 |       allure.parameter('Analytics has path',      String('path'      in res.body));
  130 |       logger.info('Asserting: Analytics returns 405');
> 131 |       expect(res.status).toBe(405);
      |                          ^ Error: expect(received).toBe(expected) // Object.is equality
  132 |       logger.pass('All assertions passed');
  133 |     });
  134 | 
  135 |     await logger.step('Step 2 — Collect Dashboard 401 error body', async () => {
  136 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  137 |       const res = await GET(DB_BASE, '/api/v1/dashboard/summary?period=week');
  138 |       logger.pass('HTTP ' + res.status + ' received');
  139 |       allure.parameter('Dashboard 401 fields', Object.keys(res.body).join(', '));
  140 |       allure.parameter('Dashboard has timestamp', String('timestamp' in res.body));
  141 |       allure.parameter('Dashboard has message',   String('message'   in res.body));
  142 |       allure.parameter('Dashboard has path',      String('path'      in res.body));
  143 |       logger.info('Asserting: Dashboard returns 401');
  144 |       expect(res.status).toBe(401);
  145 |       logger.pass('All assertions passed');
  146 |     });
  147 | 
  148 |     await logger.step('Step 3 — Collect Transaction 401 error body', async () => {
  149 |       logger.info('GET /api/v1/transaction/summary?period=week');
  150 |       const res = await GET(TXN_BASE, '/api/v1/transaction/summary?period=week');
  151 |       logger.pass('HTTP ' + res.status + ' received');
  152 |       allure.parameter('Transaction 401 fields', Object.keys(res.body).join(', '));
  153 |       logger.info('Asserting: Transaction returns 401');
  154 |       expect(res.status).toBe(401);
  155 |       logger.pass('All assertions passed');
  156 |     });
  157 | 
  158 |     await logger.step('Step 4 — Assert schema is consistent (BUG: it is not)', async () => {
  159 |       logger.info('POST /api/v1/analytics/dashboard/summary');
  160 |       const anaRes = await METHOD(ANA_BASE, '/api/v1/analytics/dashboard/summary', 'post');
  161 |       logger.pass('HTTP ' + anaRes.status + ' received');
  162 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  163 |       const dbRes  = await GET(DB_BASE,  '/api/v1/dashboard/summary?period=week');
  164 |       logger.pass('HTTP ' + dbRes.status + ' received');
  165 |       logger.info('GET /api/v1/transaction/summary?period=week');
  166 |       const txnRes = await GET(TXN_BASE, '/api/v1/transaction/summary?period=week');
  167 |       logger.pass('HTTP ' + txnRes.status + ' received');
  168 | 
  169 |       const anaHasMsg = 'message' in anaRes.body;
  170 |       const dbHasMsg  = 'message' in dbRes.body;
  171 |       const anaHasTs  = 'timestamp' in anaRes.body;
  172 |       const dbHasTs   = 'timestamp' in dbRes.body;
  173 | 
  174 |       bugAttach('BUG-XS-001',
  175 |         'All services return {status, error, message, timestamp, path}',
  176 |         `Analytics: {${Object.keys(anaRes.body).join(',')}}, Dashboard: {${Object.keys(dbRes.body).join(',')}}`,
  177 |         'Client error handling must branch per-service; 4th format risk as more services are added',
  178 |         'Define a shared error response DTO in a common library and use it across all services',
  179 |       );
  180 | 
  181 |       logger.info('Asserting: Error schema is consistent across all services');
  182 |       expect(anaHasMsg, '[BUG-XS-001] Analytics error body must include "message" field').toBe(true);
  183 |       expect(dbHasTs,   '[BUG-XS-001] Dashboard error body must include "timestamp" field').toBe(true);
  184 |       expect(anaHasTs === dbHasTs, '[BUG-XS-001] All services must share the same error schema').toBe(true);
  185 |       allure.parameter('Schema consistent across services', String(anaHasMsg && dbHasMsg && anaHasTs === dbHasTs));
  186 |       logger.pass('All assertions passed');
  187 |     });
  188 |   });
  189 | 
  190 |   // ── TC-XS-002 ─────────────────────────────────────────────────────────────
  191 |   test('[BUG-XS-002] TC-XS-002 — Dashboard 401 leaks whether token was present (token-presence enumeration)', async ({ logger }) => {
  192 |     labels('Cross-Service', 'TC-XS-002', 'Auth Error Message Enumeration Risk', 'medium');
  193 |     await allure.description(
  194 |       '**[BUG-XS-002]**\n\n' +
  195 |       'The dashboard API returns different 401 error messages depending on whether an ' +
  196 |       'Authorization header was provided, allowing an attacker to enumerate token validity:\n\n' +
  197 |       '- No Authorization header → `"Missing Authorization token"`\n' +
  198 |       '- Invalid/expired token   → `"Invalid or expired token"`\n\n' +
  199 |       '**Expected:** A single generic message for all 401 cases, e.g. `"Authentication required"`.\n\n' +
  200 |       '**Impact:** An attacker learns whether their forged token was accepted at the auth layer ' +
  201 |       'or simply absent, helping them refine token-guessing attacks. It also confirms that the ' +
  202 |       'service separately handles "no token" and "bad token" — useful for bypassing auth probes.',
  203 |     );
  204 |     let noAuthMsg  = '';
  205 |     let badAuthMsg = '';
  206 | 
  207 |     await logger.step('Step 1 — GET dashboard summary with NO auth', async () => {
  208 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  209 |       const res = await GET(DB_BASE, '/api/v1/dashboard/summary?period=week');
  210 |       logger.pass('HTTP ' + res.status + ' received');
  211 |       logger.info('Asserting: Response is 401 without auth');
  212 |       expect(res.status).toBe(401);
  213 |       noAuthMsg = String(res.body?.message ?? '');
  214 |       allure.parameter('No-auth message', noAuthMsg);
  215 |       logger.pass('All assertions passed');
  216 |     });
  217 | 
  218 |     await logger.step('Step 2 — GET dashboard summary with INVALID token', async () => {
  219 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  220 |       const res = await GET(DB_BASE, '/api/v1/dashboard/summary?period=week', 'bad.token.here');
  221 |       logger.pass('HTTP ' + res.status + ' received');
  222 |       logger.info('Asserting: Response is 401 with invalid token');
  223 |       expect(res.status).toBe(401);
  224 |       badAuthMsg = String(res.body?.message ?? '');
  225 |       allure.parameter('Invalid-token message', badAuthMsg);
  226 |       logger.pass('All assertions passed');
  227 |     });
  228 | 
  229 |     await logger.step('Step 3 — Assert both return the same generic message (BUG: they differ)', async () => {
  230 |       bugAttach('BUG-XS-002',
  231 |         'Same generic 401 message regardless of token presence',
```