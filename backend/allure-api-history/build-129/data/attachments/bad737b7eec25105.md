# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/deep-regression-api.spec.ts >> Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN) >> [BUG-DB-002] TC-DB-002 — period is case-sensitive (period=WEEK → 400, no documentation)
- Location: tests/API/deep-regression-api.spec.ts:719:7

# Error details

```
Error: period=week must return 200

expect(received).toBe(expected) // Object.is equality

Expected: 200
Received: 400
```

# Test source

```ts
  640 |         allure.parameter(f, `${val} (${typeof val})`);
  641 |         expect(typeof val,        `today.${f} must be a number, not ${typeof val}`).toBe('number');
  642 |         expect(Number.isNaN(val), `today.${f} must not be NaN`).toBe(false);
  643 |         expect(Number.isFinite(val as number), `today.${f} must be finite`).toBe(true);
  644 |         expect(val as number, `today.${f} must be >= 0`).toBeGreaterThanOrEqual(0);
  645 |       }
  646 |       logger.pass('All assertions passed');
  647 |     });
  648 |   });
  649 | 
  650 |   // ── TC-ANA-007 ────────────────────────────────────────────────────────────
  651 |   test('TC-ANA-007 — Analytics HEAD method returns 200 with no body (correct behaviour)', async ({ logger }) => {
  652 |     labels('Analytics API', 'TC-ANA-007', 'HEAD Method Support', 'minor');
  653 |     await allure.description(
  654 |       'Verifies that HEAD requests to the analytics endpoint return 200 with no response body. ' +
  655 |       'HEAD support is required by HTTP/1.1 (RFC 7231) for any resource that supports GET.',
  656 |     );
  657 | 
  658 |     await logger.step('Step 1 — Send HEAD request', async () => {
  659 |       logger.info('HEAD /api/v1/analytics/dashboard/summary');
  660 |       const res = await supertest(ANA_BASE).head(ANA_EP).set('Accept', 'application/json');
  661 |       logger.pass('HTTP ' + res.status + ' received');
  662 |       const bodyText = JSON.stringify(res.body);
  663 |       logger.info('Asserting: HEAD returns 200 with empty body');
  664 |       expect(res.status, 'HEAD must return 200').toBe(200);
  665 |       expect(['{}','null',''].includes(bodyText), 'HEAD response body must be empty').toBe(true);
  666 |       allure.parameter('HEAD status', String(res.status));
  667 |       allure.parameter('Content-Type', String(res.headers['content-type'] ?? 'N/A'));
  668 |       allure.parameter('Body empty', String(['{}','null',''].includes(bodyText)));
  669 |       logger.pass('All assertions passed');
  670 |     });
  671 |   });
  672 | 
  673 | });
  674 | 
  675 | // ═════════════════════════════════════════════════════════════════════════════
  676 | //  3. DASHBOARD API — EDGE CASES
  677 | //  Note: These tests require a valid DASHBOARD_API_TOKEN in .env
  678 | // ═════════════════════════════════════════════════════════════════════════════
  679 | 
  680 | test.describe('Dashboard API — Edge Cases (requires valid DASHBOARD_API_TOKEN)', () => {
  681 | 
  682 |   const SUMMARY   = '/api/v1/dashboard/summary';
  683 |   const MONTHLY   = '/api/v1/dashboard/monthly-revenue';
  684 |   const TREND     = '/api/v1/dashboard/revenue-trend';
  685 |   const STATUS_CY = '/api/v1/dashboard/status-currency';
  686 |   const TODAY_PAY = '/api/v1/dashboard/today-payments';
  687 | 
  688 |   // ── TC-DB-001 ─────────────────────────────────────────────────────────────
  689 |   test('[BUG-DB-001] TC-DB-001 — period="" (empty string) returns 200 with undefined default', async ({ logger }) => {
  690 |     labels('Dashboard API', 'TC-DB-001', 'Empty Period Param Returns 200', 'medium');
  691 |     await allure.description(
  692 |       '**[BUG-DB-001]**\n\n' +
  693 |       'Calling GET /dashboard/summary?period= (empty string) returns HTTP 200 instead of 400.\n\n' +
  694 |       '**Expected:** HTTP 400 Bad Request with `"message":"period must be one of: today, week, month, year, last-3-month, custom"`\n\n' +
  695 |       '**Actual:** HTTP 200 with some undefined default period applied silently.\n\n' +
  696 |       '**Impact:** Clients cannot detect they passed an invalid parameter. The response data ' +
  697 |       'is for an unspecified period — consuming it would be a silent correctness bug.',
  698 |     );
  699 | 
  700 |     await logger.step('Step 1 — GET /summary?period= (empty)', async () => {
  701 |       logger.info('GET /api/v1/dashboard/summary?period=');
  702 |       const res = await GET(DB_BASE, `${SUMMARY}?period=`, DB_TOKEN);
  703 |       logger.pass('HTTP ' + res.status + ' received');
  704 |       allure.parameter('HTTP Status', String(res.status));
  705 |       allure.parameter('Response body', JSON.stringify(res.body).slice(0, 300));
  706 |       bugAttach('BUG-DB-001',
  707 |         '400 Bad Request — period must not be empty',
  708 |         `HTTP ${res.status} — empty period= silently accepted`,
  709 |         'Client does not know which time period the data covers; correctness risk',
  710 |         'Add input validation: reject empty string for period with 400 response',
  711 |       );
  712 |       logger.info('Asserting: Empty period returns 400');
  713 |       expect(res.status, '[BUG-DB-001] period="" must return 400, not 200').toBe(400);
  714 |       logger.pass('All assertions passed');
  715 |     });
  716 |   });
  717 | 
  718 |   // ── TC-DB-002 ─────────────────────────────────────────────────────────────
  719 |   test('[BUG-DB-002] TC-DB-002 — period is case-sensitive (period=WEEK → 400, no documentation)', async ({ logger }) => {
  720 |     labels('Dashboard API', 'TC-DB-002', 'Period Case Sensitivity Undocumented', 'medium');
  721 |     await allure.description(
  722 |       '**[BUG-DB-002]**\n\n' +
  723 |       'The `period` parameter is strictly case-sensitive:\n' +
  724 |       '- `period=week` → 200 OK\n' +
  725 |       '- `period=WEEK` → 400 Bad Request\n' +
  726 |       '- `period=Week` → 400 Bad Request\n\n' +
  727 |       '**Expected:** The API should either accept case-insensitive values and normalise them, ' +
  728 |       'OR document explicitly that values are case-sensitive.\n\n' +
  729 |       '**Additionally:** The 400 error message omits `today` and `last-3-month` from the valid values list, ' +
  730 |       'leaving developers guessing.\n\n' +
  731 |       '**Impact:** Developers who naturally capitalise query params or use uppercase constants will ' +
  732 |       'get unexpected 400 errors with no useful guidance.',
  733 |     );
  734 | 
  735 |     await logger.step('Step 1 — period=week (lowercase) must return 200', async () => {
  736 |       logger.info('GET /api/v1/dashboard/summary?period=week');
  737 |       const res = await GET(DB_BASE, `${SUMMARY}?period=week`, DB_TOKEN);
  738 |       logger.pass('HTTP ' + res.status + ' received');
  739 |       logger.info('Asserting: period=week returns 200');
> 740 |       expect(res.status, 'period=week must return 200').toBe(200);
      |                                                         ^ Error: period=week must return 200
  741 |       allure.parameter('period=week status', String(res.status));
  742 |       logger.pass('All assertions passed');
  743 |     });
  744 | 
  745 |     await logger.step('Step 2 — period=WEEK (uppercase) must return 200 (BUG: returns 400)', async () => {
  746 |       logger.info('GET /api/v1/dashboard/summary?period=WEEK');
  747 |       const res = await GET(DB_BASE, `${SUMMARY}?period=WEEK`, DB_TOKEN);
  748 |       logger.pass('HTTP ' + res.status + ' received');
  749 |       allure.parameter('period=WEEK status', String(res.status));
  750 |       allure.parameter('Error message', String(res.body?.message ?? 'N/A'));
  751 |       bugAttach('BUG-DB-002',
  752 |         'period=WEEK → 200 OK (case-insensitive) or 400 with complete valid-values list',
  753 |         `period=WEEK → HTTP ${res.status} with message: "${res.body?.message}"`,
  754 |         'Developers using uppercase enum constants get 400 without clear guidance',
  755 |         'Normalise period to lowercase before validation; or document case-sensitivity with full enum list',
  756 |       );
  757 |       logger.info('Asserting: period=WEEK is accepted (case-insensitive)');
  758 |       expect(res.status, '[BUG-DB-002] period=WEEK should be accepted (case-insensitive) or error message should list all valid values').toBe(200);
  759 |       logger.pass('All assertions passed');
  760 |     });
  761 | 
  762 |     await logger.step('Step 3 — Check error message lists all valid periods', async () => {
  763 |       logger.info('GET /api/v1/dashboard/summary?period=invalid');
  764 |       const res = await GET(DB_BASE, `${SUMMARY}?period=invalid`, DB_TOKEN);
  765 |       logger.pass('HTTP ' + res.status + ' received');
  766 |       const msg = String(res.body?.message ?? '');
  767 |       allure.parameter('Invalid period error message', msg);
  768 |       const mentionsToday    = msg.toLowerCase().includes('today');
  769 |       const mentionsLast3    = msg.toLowerCase().includes('last-3-month') || msg.toLowerCase().includes('last3');
  770 |       allure.parameter('Message mentions "today"',        String(mentionsToday));
  771 |       allure.parameter('Message mentions "last-3-month"', String(mentionsLast3));
  772 |       logger.info('Asserting: Error message lists all valid period values including today and last-3-month');
  773 |       expect(mentionsToday, '[BUG-DB-002] Error message for invalid period must list "today" as a valid value').toBe(true);
  774 |       expect(mentionsLast3, '[BUG-DB-002] Error message must list "last-3-month" as a valid value').toBe(true);
  775 |       logger.pass('All assertions passed');
  776 |     });
  777 |   });
  778 | 
  779 |   // ── TC-DB-003 ─────────────────────────────────────────────────────────────
  780 |   test('[BUG-DB-003] TC-DB-003 — Impossible date (Feb-30) causes HTTP 500 instead of 400', async ({ logger }) => {
  781 |     labels('Dashboard API', 'TC-DB-003', 'Impossible Date Causes 500', 'high');
  782 |     await allure.description(
  783 |       '**[BUG-DB-003]**\n\n' +
  784 |       'Passing an impossible date like `from_date=2026-02-30` (February 30th does not exist) ' +
  785 |       'causes the server to return HTTP 500 Internal Server Error instead of 400 Bad Request.\n\n' +
  786 |       '**Expected:** HTTP 400 with `"message":"from_date is not a valid date (2026-02-30 does not exist)"`\n\n' +
  787 |       '**Root Cause:** The date is not validated before being passed to the database/ORM layer, ' +
  788 |       'which throws an unhandled exception when it tries to parse February 30th.\n\n' +
  789 |       '**Impact:** Exposes unhandled server exceptions to clients; 500 errors are monitored as ' +
  790 |       'outages in alerting systems — a single bad client request can trigger false alarms.',
  791 |     );
  792 | 
  793 |     const impossibleDates = [
  794 |       { from: '2026-02-30', to: '2026-03-01', label: 'Feb 30' },
  795 |       { from: '2026-04-31', to: '2026-05-01', label: 'Apr 31' },
  796 |       { from: '2026-13-01', to: '2026-13-15', label: 'Month 13' },
  797 |     ];
  798 | 
  799 |     for (const d of impossibleDates) {
  800 |       await logger.step(`Impossible date: ${d.label}`, async () => {
  801 |         logger.info(`GET /api/v1/dashboard/summary?period=custom&from_date=${d.from}&to_date=${d.to}`);
  802 |         const res = await GET(DB_BASE, `${SUMMARY}?period=custom&from_date=${d.from}&to_date=${d.to}`, DB_TOKEN);
  803 |         logger.pass('HTTP ' + res.status + ' received');
  804 |         allure.parameter(`${d.label} status`, String(res.status));
  805 |         if (res.status === 500) {
  806 |           bugAttach('BUG-DB-003',
  807 |             '400 Bad Request — date does not exist',
  808 |             `HTTP ${res.status} Internal Server Error for impossible date "${d.from}"`,
  809 |             'Unhandled date-parsing exception exposes 500 errors; triggers false outage alerts',
  810 |             'Add date existence validation before passing dates to the query layer; return 400',
  811 |           );
  812 |         }
  813 |         logger.info(`Asserting: Impossible date "${d.from}" returns 400`);
  814 |         expect(res.status, `[BUG-DB-003] Impossible date "${d.from}" must return 400, not 500`).toBe(400);
  815 |         logger.pass('All assertions passed');
  816 |       });
  817 |     }
  818 |   });
  819 | 
  820 |   // ── TC-DB-004 ─────────────────────────────────────────────────────────────
  821 |   test('[BUG-DB-004] TC-DB-004 — Wrong date format DD-MM-YYYY causes HTTP 500 not 400', async ({ logger }) => {
  822 |     labels('Dashboard API', 'TC-DB-004', 'Wrong Date Format Causes 500', 'high');
  823 |     await allure.description(
  824 |       '**[BUG-DB-004]**\n\n' +
  825 |       'Passing dates in DD-MM-YYYY format (common European format) instead of the required ' +
  826 |       'YYYY-MM-DD format causes HTTP 500 instead of 400 with format guidance.\n\n' +
  827 |       '**Expected:** HTTP 400 with `"message":"from_date must be in YYYY-MM-DD format"`\n\n' +
  828 |       '**Root Cause:** No format validation — the malformed date string is passed directly to ' +
  829 |       'the date parser which throws an unhandled exception.\n\n' +
  830 |       '**Impact:** Developers who pass dates in DD-MM-YYYY (which is technically a valid date ' +
  831 |       'string like 01-06-2026) get a confusing 500 with no format guidance.',
  832 |     );
  833 | 
  834 |     const wrongFormats = [
  835 |       { from: '09-06-2026', to: '09-06-2026', label: 'DD-MM-YYYY' },
  836 |       { from: '2026/06/09', to: '2026/06/09', label: 'YYYY/MM/DD (slashes)' },
  837 |       { from: 'June 9 2026', to: 'June 9 2026', label: 'Long form' },
  838 |     ];
  839 | 
  840 |     for (const d of wrongFormats) {
```