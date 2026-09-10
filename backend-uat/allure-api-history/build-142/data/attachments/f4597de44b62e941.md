# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-filter-options.spec.ts >> Backend (Konza) â€" Filter Options >> TC-BE-008 â€" Filter Options: returns 200 with all filter arrays validated
- Location: tests/API/backend-tests/backend-filter-options.spec.ts:87:7

# Error details

```
Error: statuses key must be present

expect(received).toHaveProperty(path)

Expected path: "statuses"
Received path: []

Received value: {"data": {"methods": ["MOBILE_MONEY", "MOBILE_PAYMENT", "CARD_PAYMENT", "CARD"], "statuses": ["Success"]}, "message": "success", "statusCode": 200, "success": true}
```

# Test source

```ts
  14  | 
  15  | 
  16  | import { test, expect } from '../fixtures';
  17  | import { allure }        from 'allure-playwright';
  18  | import supertest          from 'supertest';
  19  | import { getKonzaToken }       from '../../utils/konzaTokenHelper';
  20  | 
  21  | const BASE_URL     = process.env.KONZA_TRANSACTION_API_BASE_URL ?? 'https://rms.uat.konza/transaction-api';
  22  | let BEARER_TOKEN = '';
  23  | const ENDPOINT   = '/api/v1/transaction/filter-options';
  24  | 
  25  | test.beforeAll(async () => { BEARER_TOKEN = await getKonzaToken(); });
  26  | 
  27  | const EXPECTED_STATUSES = ['SUCCESS', 'FAILED', 'PENDING'];
  28  | const EXPECTED_METHODS  = ['MPESA', 'CARD', 'BANK'];
  29  | 
  30  | // â"€â"€ HTTP helper â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  31  | 
  32  | function get() {
  33  |   return supertest(BASE_URL)
  34  |     .get(ENDPOINT)
  35  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  36  |     .set('Accept', 'application/json');
  37  | }
  38  | 
  39  | // â"€â"€ Logger â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  40  | 
  41  | function logResponse(res: supertest.Response) {
  42  |   const fullUrl   = `${BASE_URL}${ENDPOINT}`;
  43  |   const statuses: string[] = res.body.statuses   ?? [];
  44  |   const methods:  string[] = res.body.methods    ?? [];
  45  |   const types:    unknown[] = res.body.txn_types  ?? [];
  46  |   const cats:     string[] = res.body.categories  ?? [];
  47  | 
  48  |   console.log(`\n${'â"€'.repeat(70)}`);
  49  |   console.log(`[TC-BE-008] GET ${fullUrl}`);
  50  |   console.log(`  â–º Status        : ${res.status}`);
  51  |   console.log(`  â–º Content-Type  : ${res.headers['content-type'] ?? 'N/A'}`);
  52  |   console.log(`  â–º statuses      : [${statuses.join(', ')}]  (${statuses.length} items)`);
  53  |   console.log(`  â–º methods       : [${methods.join(', ')}]   (${methods.length} items)`);
  54  |   console.log(`  â–º txn_types     : ${JSON.stringify(types)}  (${types.length} items)`);
  55  |   console.log(`  â–º categories    : [${cats.join(', ')}]      (${cats.length} items)`);
  56  |   console.log(`  â–º Full Body:`);
  57  |   console.log(JSON.stringify(res.body, null, 4));
  58  |   console.log(`${'â"€'.repeat(70)}\n`);
  59  | 
  60  |   allure.attachment(
  61  |     '[TC-BE-008] Request & Response',
  62  |     JSON.stringify({
  63  |       request:  { method: 'GET', url: fullUrl, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  64  |       response: { status: res.status, content_type: res.headers['content-type'] ?? 'N/A', statuses, methods, txn_types: types, categories: cats, full_body: res.body },
  65  |     }, null, 2),
  66  |     'application/json',
  67  |   );
  68  | }
  69  | 
  70  | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  71  | 
  72  | function backendLabels(tcId: string, title: string) {
  73  |   allure.parentSuite('RMS Transaction API');
  74  |   allure.suite('Backend');
  75  |   allure.subSuite('Filter Options Endpoint');
  76  |   allure.label('testId',   tcId);
  77  |   allure.label('severity', 'normal');
  78  |   allure.owner('Ashil Shaji');
  79  |   allure.tags('Backend', 'Konza', 'API', 'FilterOptions');
  80  |   allure.label('story', title);
  81  | }
  82  | 
  83  | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  84  | 
  85  | test.describe('Backend (Konza) â€" Filter Options', () => {
  86  | 
  87  |   test('TC-BE-008 â€" Filter Options: returns 200 with all filter arrays validated', async ({ logger }) => {
  88  |     backendLabels('TC-BE-008', 'Filter Options');
  89  | 
  90  |     let res: supertest.Response;
  91  | 
  92  |     await logger.step('Step 1 â€" Build request', async () => {
  93  |       allure.parameter('Base URL',  BASE_URL);
  94  |       allure.parameter('Endpoint',  ENDPOINT);
  95  |       allure.parameter('Method',    'GET');
  96  |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  97  |       logger.info('Asserting: request parameters are recorded');
  98  |       logger.pass('All assertions passed');
  99  |     });
  100 | 
  101 |     await logger.step('Step 2 â€" Send GET /filter-options and log response', async () => {
  102 |       logger.info('GET /api/v1/transaction/filter-options');
  103 |       res = await get();
  104 |       logger.pass('HTTP ' + res.status + ' received');
  105 |       logResponse(res);
  106 |       allure.parameter('HTTP Status',  String(res.status));
  107 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  108 |     });
  109 | 
  110 |     await logger.step('Step 3 â€" Validate HTTP 200 and all top-level keys present', async () => {
  111 |       logger.info('Asserting: HTTP 200, body defined, and top-level keys present');
  112 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  113 |       expect(res!.body,   'Body must be defined').toBeDefined();
> 114 |       expect(res!.body, 'statuses key must be present').toHaveProperty('statuses');
      |                                                         ^ Error: statuses key must be present
  115 |       expect(res!.body, 'methods key must be present').toHaveProperty('methods');
  116 |       expect(res!.body, 'txn_types key must be present').toHaveProperty('txn_types');
  117 |       expect(res!.body, 'categories key must be present').toHaveProperty('categories');
  118 |       allure.parameter('Top-level keys', Object.keys(res!.body).join(', '));
  119 |       logger.pass('All assertions passed');
  120 |     });
  121 | 
  122 |     await logger.step('Step 4 â€" Validate statuses array: type, length, and all expected values', async () => {
  123 |       const statuses: string[] = res!.body.statuses ?? [];
  124 | 
  125 |       logger.info('Asserting: statuses is a non-empty array containing expected values with correct types');
  126 |       expect(Array.isArray(statuses), 'statuses must be an array').toBe(true);
  127 |       expect(statuses.length,         'statuses must not be empty').toBeGreaterThan(0);
  128 | 
  129 |       for (const expected of EXPECTED_STATUSES) {
  130 |         expect(statuses, `"${expected}" must be in statuses array`).toContain(expected);
  131 |       }
  132 | 
  133 |       // All statuses must be uppercase strings
  134 |       for (const s of statuses) {
  135 |         expect(typeof s, `status "${s}" must be a string`).toBe('string');
  136 |         expect(s, `status "${s}" must be uppercase`).toBe(s.toUpperCase());
  137 |       }
  138 | 
  139 |       allure.parameter('statuses count',  String(statuses.length));
  140 |       allure.parameter('statuses values', statuses.join(', '));
  141 |       allure.parameter('Expected statuses found', EXPECTED_STATUSES.join(', ') + ' â€" PASS');
  142 |       logger.pass('All assertions passed');
  143 |     });
  144 | 
  145 |     await logger.step('Step 5 â€" Validate methods array: type, length, and all expected values', async () => {
  146 |       const methods: string[] = res!.body.methods ?? [];
  147 | 
  148 |       logger.info('Asserting: methods is a non-empty array containing expected values with correct types');
  149 |       expect(Array.isArray(methods), 'methods must be an array').toBe(true);
  150 |       expect(methods.length,         'methods must not be empty').toBeGreaterThan(0);
  151 | 
  152 |       for (const expected of EXPECTED_METHODS) {
  153 |         expect(methods, `"${expected}" must be in methods array`).toContain(expected);
  154 |       }
  155 | 
  156 |       // All methods must be uppercase strings
  157 |       for (const m of methods) {
  158 |         expect(typeof m, `method "${m}" must be a string`).toBe('string');
  159 |         expect(m, `method "${m}" must be uppercase`).toBe(m.toUpperCase());
  160 |       }
  161 | 
  162 |       allure.parameter('methods count',  String(methods.length));
  163 |       allure.parameter('methods values', methods.join(', '));
  164 |       allure.parameter('Expected methods found', EXPECTED_METHODS.join(', ') + ' â€" PASS');
  165 |       logger.pass('All assertions passed');
  166 |     });
  167 | 
  168 |     await logger.step('Step 6 â€" Validate txn_types array: structure with value and label', async () => {
  169 |       const types = res!.body.txn_types as Array<{ value: number; label: string }>;
  170 | 
  171 |       logger.info('Asserting: txn_types is a non-empty array with valid value/label pairs including value=1 "Payment"');
  172 |       expect(Array.isArray(types), 'txn_types must be an array').toBe(true);
  173 |       expect(types.length,         'txn_types must not be empty').toBeGreaterThan(0);
  174 | 
  175 |       for (const [i, t] of types.entries()) {
  176 |         expect(typeof t.value, `txn_types[${i}].value must be a number`).toBe('number');
  177 |         expect(typeof t.label, `txn_types[${i}].label must be a string`).toBe('string');
  178 |         expect(t.label.length, `txn_types[${i}].label must not be empty`).toBeGreaterThan(0);
  179 |       }
  180 | 
  181 |       // Known entry: value=1, label="Payment"
  182 |       const paymentType = types.find(t => t.value === 1);
  183 |       expect(paymentType,         'txn_types must contain entry with value=1').toBeDefined();
  184 |       expect(paymentType?.label,  'txn_types[value=1].label must be "Payment"').toBe('Payment');
  185 | 
  186 |       allure.parameter('txn_types count',  String(types.length));
  187 |       allure.parameter('txn_types values', types.map(t => `${t.value}:${t.label}`).join(', '));
  188 |       logger.pass('All assertions passed');
  189 |     });
  190 | 
  191 |     await logger.step('Step 7 â€" Validate categories array: non-empty strings', async () => {
  192 |       const categories: string[] = res!.body.categories ?? [];
  193 | 
  194 |       logger.info('Asserting: categories is a non-empty string array containing known spot-check values');
  195 |       expect(Array.isArray(categories), 'categories must be an array').toBe(true);
  196 |       expect(categories.length,         'categories must not be empty').toBeGreaterThan(0);
  197 | 
  198 |       for (const [i, cat] of categories.entries()) {
  199 |         expect(typeof cat,    `categories[${i}] must be a string`).toBe('string');
  200 |         expect(cat.trim().length, `categories[${i}] must not be blank`).toBeGreaterThan(0);
  201 |       }
  202 | 
  203 |       // Spot-check some known categories from the environment
  204 |       const knownCategories = ['PAYMENT_SERVICES', 'Education', 'Health', 'Agriculture'];
  205 |       for (const cat of knownCategories) {
  206 |         expect(categories, `"${cat}" must be in categories`).toContain(cat);
  207 |       }
  208 | 
  209 |       allure.parameter('categories count',  String(categories.length));
  210 |       allure.parameter('categories values', categories.join(', '));
  211 |       allure.parameter('Spot-check categories', knownCategories.join(', ') + ' â€" PASS');
  212 |       logger.pass('All assertions passed');
  213 |     });
  214 |   });
```