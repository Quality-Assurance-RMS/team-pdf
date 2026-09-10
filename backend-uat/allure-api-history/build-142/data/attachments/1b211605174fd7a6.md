# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/backend-tests/backend-deep-regression-api.spec.ts >> Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN) >> [BUG-DB-002] TC-DB-002 — period is case-sensitive (period=WEEK → 400, no documentation)
- Location: tests/API/backend-tests/backend-deep-regression-api.spec.ts:727:7

# Error details

```
Error: period=week must return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  648 |         allure.parameter(f, `${val} (${typeof val})`);
  649 |         expect(typeof val,        `today.${f} must be a number, not ${typeof val}`).toBe('number');
  650 |         expect(Number.isNaN(val), `today.${f} must not be NaN`).toBe(false);
  651 |         expect(Number.isFinite(val as number), `today.${f} must be finite`).toBe(true);
  652 |         expect(val as number, `today.${f} must be >= 0`).toBeGreaterThanOrEqual(0);
  653 |       }
  654 |       logger.pass('All assertions passed');
  655 |     });
  656 |   });
  657 | 
  658 |   // ── TC-ANA-007 ────────────────────────────────────────────────────────────
  659 |   test('TC-ANA-007 — Analytics HEAD method returns 200 with no body (correct behaviour)', async ({ logger }) => {
  660 |     labels('Analytics API', 'TC-ANA-007', 'HEAD Method Support', 'minor');
  661 |     await allure.description(
  662 |       'Verifies that HEAD requests to the analytics endpoint return 200 with no response body. ' +
  663 |       'HEAD support is required by HTTP/1.1 (RFC 7231) for any resource that supports GET.',
  664 |     );
  665 | 
  666 |     await logger.step('Step 1 — Send HEAD request', async () => {
  667 |       logger.info('HEAD /api/v1/analytics/dashboard/summary');
  668 |       const res = await supertest(ANA_BASE).head(ANA_EP).set('Accept', 'application/json');
  669 |       logger.pass('HTTP ' + res.status + ' received');
  670 |       const bodyText = JSON.stringify(res.body);
  671 |       logger.info('Asserting: HEAD returns 200 with empty body');
  672 |       expect(res.status, 'HEAD must return 200').toBe(200);
  673 |       expect(['{}','null',''].includes(bodyText), 'HEAD response body must be empty').toBe(true);
  674 |       allure.parameter('HEAD status', String(res.status));
  675 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  676 |       allure.parameter('Body empty', String(['{}','null',''].includes(bodyText)));
  677 |       logger.pass('All assertions passed');
  678 |     });
  679 |   });
  680 | 
  681 | });
  682 | 
  683 | // ═════════════════════════════════════════════════════════════════════════════
  684 | //  3. DASHBOARD API — EDGE CASES
  685 | //  Note: These tests require a valid DASHBOARD_API_TOKEN in .env
  686 | // ═════════════════════════════════════════════════════════════════════════════
  687 | 
  688 | test.describe('Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN)', () => {
  689 | 
  690 |   const SUMMARY   = '/api/v1/dashboard/summary';
  691 |   const MONTHLY   = '/api/v1/dashboard/monthly-revenue';
  692 |   const TREND     = '/api/v1/dashboard/revenue-trend';
  693 |   const STATUS_CY = '/api/v1/dashboard/status-currency';
  694 |   const TODAY_PAY = '/api/v1/dashboard/today-payments';
  695 | 
  696 |   // ── TC-DB-001 ─────────────────────────────────────────────────────────────
  697 |   test('[BUG-DB-001] TC-DB-001 — period="" (empty string) returns 200 with undefined default', async ({ logger }) => {
  698 |     labels('Dashboard API', 'TC-DB-001', 'Empty Period Param Returns 200', 'medium');
  699 |     await allure.description(
  700 |       '**[BUG-DB-001]**\n\n' +
  701 |       'Calling GET /dashboard/summary?period= (empty string) returns HTTP 200 instead of 400.\n\n' +
  702 |       '**Expected:** HTTP 400 Bad Request with `"message":"period must be one of: today, week, month, year, last-3-month, custom"`\n\n' +
  703 |       '**Actual:** HTTP 200 with some undefined default period applied silently.\n\n' +
  704 |       '**Impact:** Clients cannot detect they passed an invalid parameter. The response data ' +
  705 |       'is for an unspecified period — consuming it would be a silent correctness bug.',
  706 |     );
  707 | 
  708 |     await logger.step('Step 1 — GET /summary?period= (empty)', async () => {
  709 |       logger.info('GET /api/v1/dashboard/summary?period=');
  710 |       const res = await GET(DB_BASE, `${SUMMARY}?period=`, DB_TOKEN);
  711 |       logger.pass('HTTP ' + res.status + ' received');
  712 |       allure.parameter('HTTP Status', String(res.status));
  713 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 300));
  714 |       bugAttach('BUG-DB-001',
  715 |         '400 Bad Request — period must not be empty',
  716 |         `HTTP ${res.status} — empty period= silently accepted`,
  717 |         'Client does not know which time period the data covers; correctness risk',
  718 |         'Add input validation: reject empty string for period with 400 response',
  719 |       );
  720 |       logger.info('Asserting: Empty period returns 400');
  721 |       expect(res.status, '[BUG-DB-001] period="" must return 400, not 200').toBe(400);
  722 |       logger.pass('All assertions passed');
  723 |     });
  724 |   });
  725 | 
  726 |   // ── TC-DB-002 ─────────────────────────────────────────────────────────────
  727 |   test('[BUG-DB-002] TC-DB-002 — period is case-sensitive (period=WEEK → 400, no documentation)', async ({ logger }) => {
  728 |     labels('Dashboard API', 'TC-DB-002', 'Period Case Sensitivity Undocumented', 'medium');
  729 |     await allure.description(
  730 |       '**[BUG-DB-002]**\n\n' +
  731 |       'The `period` parameter is strictly case-sensitive:\n' +
  732 |       '- `period=week` → 200 OK\n' +
  733 |       '- `period=WEEK` → 400 Bad Request\n' +
  734 |       '- `period=Week` → 400 Bad Request\n\n' +
  735 |       '**Expected:** The API should either accept case-insensitive values and normalise them, ' +
  736 |       'OR document explicitly that values are case-sensitive.\n\n' +
  737 |       '**Additionally:** The 400 error message omits `today` and `last-3-month` from the valid values list, ' +
  738 |       'leaving developers guessing.\n\n' +
  739 |       '**Impact:** Developers who naturally capitalise query params or use uppercase constants will ' +
  740 |       'get unexpected 400 errors with no useful guidance.',
  741 |     );
  742 | 
  743 |     await logger.step('Step 1 — period=week (lowercase) must return 200', async () => {
  744 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  745 |       const res = await GET(DB_BASE, `${SUMMARY}?period=week`, DB_TOKEN);
  746 |       logger.pass('HTTP ' + res.status + ' received');
  747 |       logger.info('Asserting: period=week returns 200');
> 748 |       expect(res.status, 'period=week must return 200').toBe(200);
      |                                                         ^ Error: period=week must return 200
  749 |       allure.parameter('period=week status', String(res.status));
  750 |       logger.pass('All assertions passed');
  751 |     });
  752 | 
  753 |     await logger.step('Step 2 — period=WEEK (uppercase) must return 200 (BUG: returns 400)', async () => {
  754 |       logger.info('GET /api/v1/dashboard/summary?period=WEEK');
  755 |       const res = await GET(DB_BASE, `${SUMMARY}?period=WEEK`, DB_TOKEN);
  756 |       logger.pass('HTTP ' + res.status + ' received');
  757 |       allure.parameter('period=WEEK status', String(res.status));
  758 |       allure.parameter('Error message', String(res.body?.message ?? 'N/A'));
  759 |       bugAttach('BUG-DB-002',
  760 |         'period=WEEK → 200 OK (case-insensitive) or 400 with complete valid-values list',
  761 |         `period=WEEK → HTTP ${res.status} with message: "${res.body?.message}"`,
  762 |         'Developers using uppercase enum constants get 400 without clear guidance',
  763 |         'Normalise period to lowercase before validation; or document case-sensitivity with full enum list',
  764 |       );
  765 |       logger.info('Asserting: period=WEEK is accepted (case-insensitive)');
  766 |       expect(res.status, '[BUG-DB-002] period=WEEK should be accepted (case-insensitive) or error message should list all valid values').toBe(200);
  767 |       logger.pass('All assertions passed');
  768 |     });
  769 | 
  770 |     await logger.step('Step 3 — Check error message lists all valid periods', async () => {
  771 |       logger.info('GET /api/v1/dashboard/summary?period=invalid');
  772 |       const res = await GET(DB_BASE, `${SUMMARY}?period=invalid`, DB_TOKEN);
  773 |       logger.pass('HTTP ' + res.status + ' received');
  774 |       const msg = String(res.body?.message ?? '');
  775 |       allure.parameter('Invalid period error message', msg);
  776 |       const mentionsToday    = msg.toLowerCase().includes('today');
  777 |       const mentionsLast3    = msg.toLowerCase().includes('last-3-month') || msg.toLowerCase().includes('last3');
  778 |       allure.parameter('Message mentions "today"',        String(mentionsToday));
  779 |       allure.parameter('Message mentions "last-3-month"', String(mentionsLast3));
  780 |       logger.info('Asserting: Error message lists all valid period values including today and last-3-month');
  781 |       expect(mentionsToday, '[BUG-DB-002] Error message for invalid period must list "today" as a valid value').toBe(true);
  782 |       expect(mentionsLast3, '[BUG-DB-002] Error message must list "last-3-month" as a valid value').toBe(true);
  783 |       logger.pass('All assertions passed');
  784 |     });
  785 |   });
  786 | 
  787 |   // ── TC-DB-003 ─────────────────────────────────────────────────────────────
  788 |   test('[BUG-DB-003] TC-DB-003 — Impossible date (Feb-30) causes HTTP 500 instead of 400', async ({ logger }) => {
  789 |     labels('Dashboard API', 'TC-DB-003', 'Impossible Date Causes 500', 'high');
  790 |     await allure.description(
  791 |       '**[BUG-DB-003]**\n\n' +
  792 |       'Passing an impossible date like `from_date=2026-02-30` (February 30th does not exist) ' +
  793 |       'causes the server to return HTTP 500 Internal Server Error instead of 400 Bad Request.\n\n' +
  794 |       '**Expected:** HTTP 400 with `"message":"from_date is not a valid date (2026-02-30 does not exist)"`\n\n' +
  795 |       '**Root Cause:** The date is not validated before being passed to the database/ORM layer, ' +
  796 |       'which throws an unhandled exception when it tries to parse February 30th.\n\n' +
  797 |       '**Impact:** Exposes unhandled server exceptions to clients; 500 errors are monitored as ' +
  798 |       'outages in alerting systems — a single bad client request can trigger false alarms.',
  799 |     );
  800 | 
  801 |     const impossibleDates = [
  802 |       { from: '2026-02-30', to: '2026-03-01', label: 'Feb 30' },
  803 |       { from: '2026-04-31', to: '2026-05-01', label: 'Apr 31' },
  804 |       { from: '2026-13-01', to: '2026-13-15', label: 'Month 13' },
  805 |     ];
  806 | 
  807 |     for (const d of impossibleDates) {
  808 |       await logger.step(`Impossible date: ${d.label}`, async () => {
  809 |         logger.info(`GET /api/v1/dashboard/summary?period=custom&from_date=${d.from}&to_date=${d.to}`);
  810 |         const res = await GET(DB_BASE, `${SUMMARY}?period=custom&from_date=${d.from}&to_date=${d.to}`, DB_TOKEN);
  811 |         logger.pass('HTTP ' + res.status + ' received');
  812 |         allure.parameter(`${d.label} status`, String(res.status));
  813 |         if (res.status === 500) {
  814 |           bugAttach('BUG-DB-003',
  815 |             '400 Bad Request — date does not exist',
  816 |             `HTTP ${res.status} Internal Server Error for impossible date "${d.from}"`,
  817 |             'Unhandled date-parsing exception exposes 500 errors; triggers false outage alerts',
  818 |             'Add date existence validation before passing dates to the query layer; return 400',
  819 |           );
  820 |         }
  821 |         logger.info(`Asserting: Impossible date "${d.from}" returns 400`);
  822 |         expect(res.status, `[BUG-DB-003] Impossible date "${d.from}" must return 400, not 500`).toBe(400);
  823 |         logger.pass('All assertions passed');
  824 |       });
  825 |     }
  826 |   });
  827 | 
  828 |   // ── TC-DB-004 ─────────────────────────────────────────────────────────────
  829 |   test('[BUG-DB-004] TC-DB-004 — Wrong date format DD-MM-YYYY causes HTTP 500 not 400', async ({ logger }) => {
  830 |     labels('Dashboard API', 'TC-DB-004', 'Wrong Date Format Causes 500', 'high');
  831 |     await allure.description(
  832 |       '**[BUG-DB-004]**\n\n' +
  833 |       'Passing dates in DD-MM-YYYY format (common European format) instead of the required ' +
  834 |       'YYYY-MM-DD format causes HTTP 500 instead of 400 with format guidance.\n\n' +
  835 |       '**Expected:** HTTP 400 with `"message":"from_date must be in YYYY-MM-DD format"`\n\n' +
  836 |       '**Root Cause:** No format validation — the malformed date string is passed directly to ' +
  837 |       'the date parser which throws an unhandled exception.\n\n' +
  838 |       '**Impact:** Developers who pass dates in DD-MM-YYYY (which is technically a valid date ' +
  839 |       'string like 01-06-2026) get a confusing 500 with no format guidance.',
  840 |     );
  841 | 
  842 |     const wrongFormats = [
  843 |       { from: '09-06-2026', to: '09-06-2026', label: 'DD-MM-YYYY' },
  844 |       { from: '2026/06/09', to: '2026/06/09', label: 'YYYY/MM/DD (slashes)' },
  845 |       { from: 'June 9 2026', to: 'June 9 2026', label: 'Long form' },
  846 |     ];
  847 | 
  848 |     for (const d of wrongFormats) {
```