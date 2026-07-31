# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Cross-Service Consistency >> [BUG-XS-003] TC-XS-003 — Unknown paths return 401 not 404 on dashboard and transaction APIs
- Location: tests/API/deep-regression-api.spec.ts:236:7

# Error details

```
Error: [BUG-XS-003] Unknown dashboard path must return 404, not 401

expect(received).toBe(expected) // Object.is equality

Expected: 404
Received: 401
```

# Test source

```ts
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
  247 |       'approach is to route first, then authenticate (known paths exist but require auth → 401; ' +
  248 |       'unknown paths → 404 regardless of auth state).\n\n' +
  249 |       '**Impact:** Developer experience is degraded; documentation gaps are hidden behind 401.',
  250 |     );
  251 | 
  252 |     await logger.step('Step 1 — GET unknown path on dashboard (no auth)', async () => {
  253 |       logger.info('GET /api/v1/dashboard/this-does-not-exist');
  254 |       const res = await GET(DB_BASE, '/api/v1/dashboard/this-does-not-exist');
  255 |       logger.pass('HTTP ' + res.status + ' received');
  256 |       allure.parameter('Dashboard unknown path status', String(res.status));
  257 |       bugAttach('BUG-XS-003',
  258 |         '404 Not Found for unknown path',
  259 |         `HTTP ${res.status} — returns 401 for unknown paths`,
  260 |         'Cannot distinguish wrong path from missing auth; developer experience degraded',
  261 |         'Route before authenticating; return 404 for genuinely unknown routes',
  262 |       );
  263 |       logger.info('Asserting: Unknown dashboard path returns 404');
> 264 |       expect(res.status, '[BUG-XS-003] Unknown dashboard path must return 404, not 401').toBe(404);
      |                                                                                          ^ Error: [BUG-XS-003] Unknown dashboard path must return 404, not 401
  265 |       logger.pass('All assertions passed');
  266 |     });
  267 | 
  268 |     await logger.step('Step 2 — GET unknown path on transaction (no auth)', async () => {
  269 |       logger.info('GET /api/v1/transaction/this-does-not-exist');
  270 |       const res = await GET(TXN_BASE, '/api/v1/transaction/this-does-not-exist');
  271 |       logger.pass('HTTP ' + res.status + ' received');
  272 |       allure.parameter('Transaction unknown path status', String(res.status));
  273 |       logger.info('Asserting: Unknown transaction path returns 404');
  274 |       expect(res.status, '[BUG-XS-003] Unknown transaction path must return 404, not 401').toBe(404);
  275 |       logger.pass('All assertions passed');
  276 |     });
  277 | 
  278 |     await logger.step('Step 3 — GET unknown path on analytics (no auth) — should correctly return 404', async () => {
  279 |       logger.info('GET /api/v1/analytics/this-does-not-exist');
  280 |       const res = await GET(ANA_BASE, '/api/v1/analytics/this-does-not-exist');
  281 |       logger.pass('HTTP ' + res.status + ' received');
  282 |       allure.parameter('Analytics unknown path status', String(res.status));
  283 |       allure.parameter('Analytics correctly returns 404', String(res.status === 404));
  284 |       logger.info('Asserting: Analytics correctly returns 404 for unknown path');
  285 |       expect(res.status, 'Analytics correctly returns 404 for unknown path (correct behaviour)').toBe(404);
  286 |       logger.pass('All assertions passed');
  287 |     });
  288 |   });
  289 | 
  290 |   // ── TC-XS-004 ─────────────────────────────────────────────────────────────
  291 |   test('[BUG-XS-004] TC-XS-004 — CORS wildcard * is present even on 401 error responses', async ({ logger }) => {
  292 |     labels('Cross-Service', 'TC-XS-004', 'CORS Wildcard on Error Responses', 'high');
  293 |     await allure.description(
  294 |       '**[BUG-XS-004]**\n\n' +
  295 |       'The Access-Control-Allow-Origin: * header is returned on ALL responses — including 401 ' +
  296 |       'Unauthorized error responses. This means the error body is readable cross-origin by any website.\n\n' +
  297 |       '**What this enables:**\n' +
  298 |       'A malicious page can make a cross-origin request with no token, read the 401 body ' +
  299 |       '(`"Missing Authorization token"`), and use the distinct error messages (BUG-XS-002) ' +
  300 |       'to enumerate token validity from the user\'s browser — all without any credentials.\n\n' +
  301 |       '**Expected:** CORS headers should only be present on successful responses to allowed origins.\n' +
  302 |       'Error responses should not have CORS headers, or should restrict them to the trusted origin.',
  303 |     );
  304 | 
  305 |     const endpoints = [
  306 |       { base: DB_BASE,  path: '/api/v1/dashboard/summary?period=week',           label: 'Dashboard 401' },
  307 |       { base: TXN_BASE, path: '/api/v1/transaction/summary?period=week',          label: 'Transaction 401' },
  308 |       { base: ANA_BASE, path: '/api/v1/analytics/dashboard/summary',              label: 'Analytics 200 (no auth needed)' },
  309 |     ];
  310 | 
  311 |     for (const ep of endpoints) {
  312 |       await logger.step(`Check CORS on ${ep.label}`, async () => {
  313 |         logger.info('GET ' + ep.path);
  314 |         const res = await GET(ep.base, ep.path);
  315 |         logger.pass('HTTP ' + res.status + ' received');
  316 |         const acao = String(res.headers['access-control-allow-origin'] ?? '');
  317 |         allure.parameter(`${ep.label} ACAO`, acao || 'NOT SET');
  318 |         allure.parameter(`${ep.label} status`, String(res.status));
  319 |         bugAttach('BUG-XS-004',
  320 |           'No CORS headers on error responses (or restrict to trusted origin)',
  321 |           `HTTP ${res.status} with ACAO: ${acao}`,
  322 |           'Cross-origin error body is readable by malicious pages; enables enumeration via CORS',
  323 |           'Remove CORS headers from error responses or restrict ACAO to specific RMS frontend origin',
  324 |         );
  325 |         logger.info('Asserting: CORS Access-Control-Allow-Origin is not wildcard on error response');
  326 |         expect(acao, `[BUG-XS-004] ${ep.label} — ACAO must not be wildcard "*" on error response`)
  327 |           .not.toBe('*');
  328 |         logger.pass('All assertions passed');
  329 |       });
  330 |     }
  331 |   });
  332 | 
  333 | });
  334 | 
  335 | // ═════════════════════════════════════════════════════════════════════════════
  336 | //  2. ANALYTICS API — DEEP TESTS
  337 | // ═════════════════════════════════════════════════════════════════════════════
  338 | 
  339 | test.describe('Analytics API — Deep Tests', () => {
  340 | 
  341 |   const ANA_EP = '/api/v1/analytics/dashboard/summary';
  342 | 
  343 |   // ── TC-ANA-001 ────────────────────────────────────────────────────────────
  344 |   test('[BUG-ANA-001] TC-ANA-001 — Spring Boot Whitelabel page leaks EAT server timezone on every 406', async ({ logger }) => {
  345 |     labels('Analytics API', 'TC-ANA-001', 'Framework Info Leak via 406 Error', 'medium');
  346 |     await allure.description(
  347 |       '**[BUG-ANA-001]**\n\n' +
  348 |       'When GET /rms-analytics/api/v1/analytics/dashboard/summary is called with `Accept: text/html`, ' +
  349 |       'the server returns 406 Not Acceptable but the body is a Spring Boot Whitelabel Error Page:\n\n' +
  350 |       '```html\n' +
  351 |       '<html><body><h1>Whitelabel Error Page</h1>...\n' +
  352 |       '<div id=\'created\'>Tue Jun 09 15:24:09 EAT 2026</div>...\n' +
  353 |       '```\n\n' +
  354 |       '**Information leaked:**\n' +
  355 |       '1. Spring Boot framework (confirms Java stack)\n' +
  356 |       '2. Server timezone: **EAT** (East Africa Time, UTC+3) — reveals the server\'s geographic region\n' +
  357 |       '3. Exact server timestamp at time of request\n' +
  358 |       '4. Internal URL path: `/api/v1/analytics/dashboard/summary`\n\n' +
  359 |       '**Expected:** 406 with a JSON error body (`{"status":406,"error":"Not Acceptable","message":"..."}`)\n\n' +
  360 |       '**Fix:** Register a global `HttpMediaTypeNotAcceptableException` handler that returns JSON.',
  361 |     );
  362 | 
  363 |     await logger.step('Step 1 — Send GET with Accept: text/html', async () => {
  364 |       logger.info('GET /api/v1/analytics/dashboard/summary');
```