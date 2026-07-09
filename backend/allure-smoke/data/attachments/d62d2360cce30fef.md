# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-filter-options.spec.ts >> Smoke â€" Filter Options >> TC-SMK-008 â€" Filter Options: returns 200 with all filter arrays validated
- Location: tests/API/smoke/smoke-filter-options.spec.ts:79:7

# Error details

```
Error: statuses key must be present

expect(received).toHaveProperty(path)

Expected path: "statuses"
Received path: []

Received value: {"data": {"categories": ["Land", "Health", "Business", "Communications", "Immigration", "Agriculture", "Energy", "Legal", "Standards & Compliance", "Revenue", …], "methods": ["MOBILE_MONEY", "CARD", "CARD_PAYMENT"], "statuses": ["Failed", "Pending", "Success"], "txnTypes": [{"label": "Payment", "value": 1}]}, "message": "success", "statusCode": 200, "success": true}
```

# Test source

```ts
  6   |  */
  7   | 
  8   | import { test, expect } from '../fixtures';
  9   | import { allure }        from 'allure-playwright';
  10  | import supertest          from 'supertest';
  11  | import { getToken }       from '../../utils/tokenHelper';
  12  | 
  13  | const BASE_URL     = process.env.TRANSACTION_API_BASE_URL ?? 'https://api.rms.dev.demo-fsit.com/transaction-api';
  14  | let BEARER_TOKEN = '';
  15  | const ENDPOINT   = '/api/v1/transaction/filter-options';
  16  | 
  17  | test.beforeAll(async () => { BEARER_TOKEN = await getToken(); });
  18  | 
  19  | const EXPECTED_STATUSES = ['SUCCESS', 'FAILED', 'PENDING'];
  20  | const EXPECTED_METHODS  = ['MPESA', 'CARD', 'BANK'];
  21  | 
  22  | // â"€â"€ HTTP helper â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  23  | 
  24  | function get() {
  25  |   return supertest(BASE_URL)
  26  |     .get(ENDPOINT)
  27  |     .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  28  |     .set('Accept', 'application/json');
  29  | }
  30  | 
  31  | // â"€â"€ Logger â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  32  | 
  33  | function logResponse(res: supertest.Response) {
  34  |   const fullUrl   = `${BASE_URL}${ENDPOINT}`;
  35  |   const statuses: string[] = res.body.statuses   ?? [];
  36  |   const methods:  string[] = res.body.methods    ?? [];
  37  |   const types:    unknown[] = res.body.txn_types  ?? [];
  38  |   const cats:     string[] = res.body.categories  ?? [];
  39  | 
  40  |   console.log(`\n${'â"€'.repeat(70)}`);
  41  |   console.log(`[TC-SMK-008] GET ${fullUrl}`);
  42  |   console.log(`  â–º Status        : ${res.status}`);
  43  |   console.log(`  â–º Content-Type  : ${res.headers['content-type'] ?? 'N/A'}`);
  44  |   console.log(`  â–º statuses      : [${statuses.join(', ')}]  (${statuses.length} items)`);
  45  |   console.log(`  â–º methods       : [${methods.join(', ')}]   (${methods.length} items)`);
  46  |   console.log(`  â–º txn_types     : ${JSON.stringify(types)}  (${types.length} items)`);
  47  |   console.log(`  â–º categories    : [${cats.join(', ')}]      (${cats.length} items)`);
  48  |   console.log(`  â–º Full Body:`);
  49  |   console.log(JSON.stringify(res.body, null, 4));
  50  |   console.log(`${'â"€'.repeat(70)}\n`);
  51  | 
  52  |   allure.attachment(
  53  |     '[TC-SMK-008] Request & Response',
  54  |     JSON.stringify({
  55  |       request:  { method: 'GET', url: fullUrl, headers: { Authorization: 'Bearer ***redacted***', Accept: 'application/json' } },
  56  |       response: { status: res.status, content_type: res.headers['content-type'] ?? 'N/A', statuses, methods, txn_types: types, categories: cats, full_body: res.body },
  57  |     }, null, 2),
  58  |     'application/json',
  59  |   );
  60  | }
  61  | 
  62  | // â"€â"€ Allure labels â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  63  | 
  64  | function smokeLabels(tcId: string, title: string) {
  65  |   allure.parentSuite('RMS Transaction API');
  66  |   allure.suite('Smoke');
  67  |   allure.subSuite('Filter Options Endpoint');
  68  |   allure.label('testId',   tcId);
  69  |   allure.label('severity', 'normal');
  70  |   allure.owner('Ashil Shaji');
  71  |   allure.tags('Smoke', 'API', 'FilterOptions');
  72  |   allure.label('story', title);
  73  | }
  74  | 
  75  | // â"€â"€ Suite â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€â"€
  76  | 
  77  | test.describe('Smoke â€" Filter Options', () => {
  78  | 
  79  |   test('TC-SMK-008 â€" Filter Options: returns 200 with all filter arrays validated', async ({ logger }) => {
  80  |     smokeLabels('TC-SMK-008', 'Filter Options');
  81  | 
  82  |     let res: supertest.Response;
  83  | 
  84  |     await logger.step('Step 1 â€" Build request', async () => {
  85  |       allure.parameter('Base URL',  BASE_URL);
  86  |       allure.parameter('Endpoint',  ENDPOINT);
  87  |       allure.parameter('Method',    'GET');
  88  |       allure.parameter('Auth',      `Bearer token present: ${BEARER_TOKEN ? 'YES' : 'NO'}`);
  89  |       logger.info('Asserting: request parameters are recorded');
  90  |       logger.pass('All assertions passed');
  91  |     });
  92  | 
  93  |     await logger.step('Step 2 â€" Send GET /filter-options and log response', async () => {
  94  |       logger.info('GET /api/v1/transaction/filter-options');
  95  |       res = await get();
  96  |       logger.pass('HTTP ' + res.status + ' received');
  97  |       logResponse(res);
  98  |       allure.parameter('HTTP Status',  String(res.status));
  99  |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  100 |     });
  101 | 
  102 |     await logger.step('Step 3 â€" Validate HTTP 200 and all top-level keys present', async () => {
  103 |       logger.info('Asserting: HTTP 200, body defined, and top-level keys present');
  104 |       expect(res!.status, 'HTTP 200 expected').toBe(200);
  105 |       expect(res!.body,   'Body must be defined').toBeDefined();
> 106 |       expect(res!.body, 'statuses key must be present').toHaveProperty('statuses');
      |                                                         ^ Error: statuses key must be present
  107 |       expect(res!.body, 'methods key must be present').toHaveProperty('methods');
  108 |       expect(res!.body, 'txn_types key must be present').toHaveProperty('txn_types');
  109 |       expect(res!.body, 'categories key must be present').toHaveProperty('categories');
  110 |       allure.parameter('Top-level keys', Object.keys(res!.body).join(', '));
  111 |       logger.pass('All assertions passed');
  112 |     });
  113 | 
  114 |     await logger.step('Step 4 â€" Validate statuses array: type, length, and all expected values', async () => {
  115 |       const statuses: string[] = res!.body.statuses ?? [];
  116 | 
  117 |       logger.info('Asserting: statuses is a non-empty array containing expected values with correct types');
  118 |       expect(Array.isArray(statuses), 'statuses must be an array').toBe(true);
  119 |       expect(statuses.length,         'statuses must not be empty').toBeGreaterThan(0);
  120 | 
  121 |       for (const expected of EXPECTED_STATUSES) {
  122 |         expect(statuses, `"${expected}" must be in statuses array`).toContain(expected);
  123 |       }
  124 | 
  125 |       // All statuses must be uppercase strings
  126 |       for (const s of statuses) {
  127 |         expect(typeof s, `status "${s}" must be a string`).toBe('string');
  128 |         expect(s, `status "${s}" must be uppercase`).toBe(s.toUpperCase());
  129 |       }
  130 | 
  131 |       allure.parameter('statuses count',  String(statuses.length));
  132 |       allure.parameter('statuses values', statuses.join(', '));
  133 |       allure.parameter('Expected statuses found', EXPECTED_STATUSES.join(', ') + ' â€" PASS');
  134 |       logger.pass('All assertions passed');
  135 |     });
  136 | 
  137 |     await logger.step('Step 5 â€" Validate methods array: type, length, and all expected values', async () => {
  138 |       const methods: string[] = res!.body.methods ?? [];
  139 | 
  140 |       logger.info('Asserting: methods is a non-empty array containing expected values with correct types');
  141 |       expect(Array.isArray(methods), 'methods must be an array').toBe(true);
  142 |       expect(methods.length,         'methods must not be empty').toBeGreaterThan(0);
  143 | 
  144 |       for (const expected of EXPECTED_METHODS) {
  145 |         expect(methods, `"${expected}" must be in methods array`).toContain(expected);
  146 |       }
  147 | 
  148 |       // All methods must be uppercase strings
  149 |       for (const m of methods) {
  150 |         expect(typeof m, `method "${m}" must be a string`).toBe('string');
  151 |         expect(m, `method "${m}" must be uppercase`).toBe(m.toUpperCase());
  152 |       }
  153 | 
  154 |       allure.parameter('methods count',  String(methods.length));
  155 |       allure.parameter('methods values', methods.join(', '));
  156 |       allure.parameter('Expected methods found', EXPECTED_METHODS.join(', ') + ' â€" PASS');
  157 |       logger.pass('All assertions passed');
  158 |     });
  159 | 
  160 |     await logger.step('Step 6 â€" Validate txn_types array: structure with value and label', async () => {
  161 |       const types = res!.body.txn_types as Array<{ value: number; label: string }>;
  162 | 
  163 |       logger.info('Asserting: txn_types is a non-empty array with valid value/label pairs including value=1 "Payment"');
  164 |       expect(Array.isArray(types), 'txn_types must be an array').toBe(true);
  165 |       expect(types.length,         'txn_types must not be empty').toBeGreaterThan(0);
  166 | 
  167 |       for (const [i, t] of types.entries()) {
  168 |         expect(typeof t.value, `txn_types[${i}].value must be a number`).toBe('number');
  169 |         expect(typeof t.label, `txn_types[${i}].label must be a string`).toBe('string');
  170 |         expect(t.label.length, `txn_types[${i}].label must not be empty`).toBeGreaterThan(0);
  171 |       }
  172 | 
  173 |       // Known entry: value=1, label="Payment"
  174 |       const paymentType = types.find(t => t.value === 1);
  175 |       expect(paymentType,         'txn_types must contain entry with value=1').toBeDefined();
  176 |       expect(paymentType?.label,  'txn_types[value=1].label must be "Payment"').toBe('Payment');
  177 | 
  178 |       allure.parameter('txn_types count',  String(types.length));
  179 |       allure.parameter('txn_types values', types.map(t => `${t.value}:${t.label}`).join(', '));
  180 |       logger.pass('All assertions passed');
  181 |     });
  182 | 
  183 |     await logger.step('Step 7 â€" Validate categories array: non-empty strings', async () => {
  184 |       const categories: string[] = res!.body.categories ?? [];
  185 | 
  186 |       logger.info('Asserting: categories is a non-empty string array containing known spot-check values');
  187 |       expect(Array.isArray(categories), 'categories must be an array').toBe(true);
  188 |       expect(categories.length,         'categories must not be empty').toBeGreaterThan(0);
  189 | 
  190 |       for (const [i, cat] of categories.entries()) {
  191 |         expect(typeof cat,    `categories[${i}] must be a string`).toBe('string');
  192 |         expect(cat.trim().length, `categories[${i}] must not be blank`).toBeGreaterThan(0);
  193 |       }
  194 | 
  195 |       // Spot-check some known categories from the environment
  196 |       const knownCategories = ['PAYMENT_SERVICES', 'Education', 'Health', 'Agriculture'];
  197 |       for (const cat of knownCategories) {
  198 |         expect(categories, `"${cat}" must be in categories`).toContain(cat);
  199 |       }
  200 | 
  201 |       allure.parameter('categories count',  String(categories.length));
  202 |       allure.parameter('categories values', categories.join(', '));
  203 |       allure.parameter('Spot-check categories', knownCategories.join(', ') + ' â€" PASS');
  204 |       logger.pass('All assertions passed');
  205 |     });
  206 |   });
```