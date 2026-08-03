# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: API/smoke/smoke-dashboard-revenue-trend.spec.ts >> Smoke â€” Dashboard Revenue Trend >> TC-DREVT-008 â€” BUG-REVT-07: all query parameters (?period=, ?startDate=, etc.) silently ignored
- Location: tests/API/smoke/smoke-dashboard-revenue-trend.spec.ts:654:7

# Error details

```
Error: [BUG-REVT-07a] Invalid ?period= value must return 400 Bad Request, not 200

expect(received).toBe(expected) // Object.is equality

Expected: 400
Received: 200
```

```
Error: [BUG-REVT-07b] ?period=weekly must return different data from the default (monthly) view

expect(received).toBe(expected) // Object.is equality

Expected: false
Received: true
```

```
Error: [BUG-REVT-07c] ?startDate/endDate must filter the returned data range

expect(received).toBe(expected) // Object.is equality

Expected: false
Received: true
```

# Test source

```ts
  646 |           ).toBe(true);
  647 |         }
  648 |       }
  649 |       logger.pass('All assertions passed');
  650 |     });
  651 |   });
  652 | 
  653 |   // â”€â”€ TC-DREVT-008 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  654 |   test('TC-DREVT-008 â€” BUG-REVT-07: all query parameters (?period=, ?startDate=, etc.) silently ignored', async ({ logger }) => {
  655 |     smokeLabels('TC-DREVT-008', 'BUG-REVT-07 â€” Query Params Silently Ignored', 'normal');
  656 | 
  657 |     let resBase:    supertest.Response;
  658 |     let resWeekly:  supertest.Response;
  659 |     let resInvalid: supertest.Response;
  660 |     let resDate:    supertest.Response;
  661 | 
  662 |     await logger.step('Step 1 â€” Fetch baseline (no params)', async () => {
  663 |       logger.info(`GET ${ENDPOINT}`);
  664 |       resBase   = await get();
  665 |       logger.pass('HTTP ' + resBase.status + ' received');
  666 |       expect(resBase.status).toBe(200);
  667 |       logger.pass('All assertions passed');
  668 |     });
  669 | 
  670 |     await logger.step('Step 2 â€” Fetch with period=weekly', async () => {
  671 |       logger.info(`GET ${ENDPOINT}`);
  672 |       resWeekly = await get('period=weekly');
  673 |       logger.pass('HTTP ' + resWeekly.status + ' received');
  674 |       allure.parameter('?period=weekly status', String(resWeekly.status));
  675 |     });
  676 | 
  677 |     await logger.step('Step 3 â€” Fetch with period=invalid_value', async () => {
  678 |       logger.info(`GET ${ENDPOINT}`);
  679 |       resInvalid = await get('period=invalid_value');
  680 |       logger.pass('HTTP ' + resInvalid.status + ' received');
  681 |       allure.parameter('?period=invalid_value status', String(resInvalid.status));
  682 |     });
  683 | 
  684 |     await logger.step('Step 4 â€” Fetch with startDate/endDate', async () => {
  685 |       logger.info(`GET ${ENDPOINT}`);
  686 |       resDate = await get('startDate=2026-01-01&endDate=2026-06-01');
  687 |       logger.pass('HTTP ' + resDate.status + ' received');
  688 |       allure.parameter('?startDate/endDate status', String(resDate.status));
  689 |     });
  690 | 
  691 |     await logger.step('Step 5 â€” BUG-REVT-07a: ?period=invalid_value must return 400, not 200', async () => {
  692 |       if (resInvalid!.status === 200) {
  693 |         flagIssue('TC-DREVT-008', 'BUG-REVT-07a',
  694 |           '?period=invalid_value returns 200 OK â€” invalid parameter values must return 400 Bad Request. ' +
  695 |           'Silently accepting any value gives clients no feedback that their request was malformed.',
  696 |           { actual_status: 200, query: 'period=invalid_value', expected_status: 400 },
  697 |         );
  698 |       }
  699 |       logger.info('Asserting: BUG-REVT-07a: ?period=invalid_value must return 4');
  700 |       expect.soft(resInvalid!.status,
  701 |         '[BUG-REVT-07a] Invalid ?period= value must return 400 Bad Request, not 200',
  702 |       ).toBe(400);
  703 |       logger.pass('All assertions passed');
  704 |     });
  705 | 
  706 |     await logger.step('Step 6 â€” BUG-REVT-07b: ?period=weekly must return different data than no-period (monthly)', async () => {
  707 |       // If period param is honoured, weekly data will have different labels and data arrays
  708 |       const baseBody    = JSON.stringify(resBase!.body);
  709 |       const weeklyBody  = JSON.stringify(resWeekly!.body);
  710 |       const identical   = baseBody === weeklyBody;
  711 | 
  712 |       allure.parameter('baseline === ?period=weekly', String(identical));
  713 | 
  714 |       if (identical && resWeekly!.status === 200) {
  715 |         flagIssue('TC-DREVT-008', 'BUG-REVT-07b',
  716 |           '?period=weekly returns IDENTICAL response to no-param call â€” the period filter is silently ignored. ' +
  717 |           'A weekly trend should show daily/weekly bucket labels (e.g. "Week 1", "Mon 26 May") not monthly "Jun, Julâ€¦".',
  718 |           { actual: 'identical responses', expected: 'different label granularity for weekly vs monthly periods' },
  719 |         );
  720 |       }
  721 | 
  722 |       logger.info('Asserting: BUG-REVT-07b: ?period=weekly must return differen');
  723 |       expect.soft(identical,
  724 |         '[BUG-REVT-07b] ?period=weekly must return different data from the default (monthly) view',
  725 |       ).toBe(false);
  726 |       logger.pass('All assertions passed');
  727 |     });
  728 | 
  729 |     await logger.step('Step 7 â€” BUG-REVT-07c: ?startDate/endDate must filter data range', async () => {
  730 |       const baseBody = JSON.stringify(resBase!.body);
  731 |       const dateBody = JSON.stringify(resDate!.body);
  732 |       const identical = baseBody === dateBody;
  733 | 
  734 |       allure.parameter('baseline === ?startDate/endDate', String(identical));
  735 | 
  736 |       if (identical && resDate!.status === 200) {
  737 |         flagIssue('TC-DREVT-008', 'BUG-REVT-07c',
  738 |           '?startDate=2026-01-01&endDate=2026-06-01 returns IDENTICAL response to no-param call â€” date range filter is silently ignored.',
  739 |           { actual: 'identical responses', expected: 'data filtered to Janâ€“Jun 2026 range only' },
  740 |         );
  741 |       }
  742 | 
  743 |       logger.info('Asserting: BUG-REVT-07c: ?startDate/endDate must filter data');
  744 |       expect.soft(identical,
  745 |         '[BUG-REVT-07c] ?startDate/endDate must filter the returned data range',
> 746 |       ).toBe(false);
      |         ^ Error: [BUG-REVT-07c] ?startDate/endDate must filter the returned data range
  747 |       logger.pass('All assertions passed');
  748 |     });
  749 | 
  750 |     attachResponse('TC-DREVT-008 (base)',    resBase!,    { query: '(none)',                         bug: 'BUG-REVT-07' });
  751 |     attachResponse('TC-DREVT-008 (weekly)',  resWeekly!,  { query: 'period=weekly',                  bug: 'BUG-REVT-07' });
  752 |     attachResponse('TC-DREVT-008 (invalid)', resInvalid!, { query: 'period=invalid_value',           bug: 'BUG-REVT-07' });
  753 |     attachResponse('TC-DREVT-008 (dates)',   resDate!,    { query: 'startDate=2026-01-01&endDate=2026-06-01', bug: 'BUG-REVT-07' });
  754 |   });
  755 | 
  756 |   // â”€â”€ TC-DREVT-009 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  757 |   test('TC-DREVT-009 â€” BUG-REVT-08: Wrong HTTP methods must return 405, not 500', async ({ logger }) => {
  758 |     smokeLabels('TC-DREVT-009', 'BUG-REVT-08 â€” HTTP Method Enforcement', 'normal');
  759 | 
  760 |     const wrongMethods = ['post', 'put', 'delete', 'patch'] as const;
  761 | 
  762 |     for (const method of wrongMethods) {
  763 |       await logger.step(`${method.toUpperCase()} must return 405`, async () => {
  764 |         logger.info(`GET ${ENDPOINT}`);
  765 |         const res = await getWithMethod(method);
  766 |         logger.pass('HTTP ' + res.status + ' received');
  767 | 
  768 |         console.log(`[TC-DREVT-009] ${method.toUpperCase()} â†’ ${res.status}  body: ${JSON.stringify(res.body)}`);
  769 |         allure.parameter(`${method.toUpperCase()} status`, String(res.status));
  770 |         allure.parameter(`${method.toUpperCase()} error`,  String(res.body?.error ?? 'N/A'));
  771 | 
  772 |         if (res.status === 500) {
  773 |           flagIssue('TC-DREVT-009', 'BUG-REVT-08',
  774 |             `${method.toUpperCase()} ${ENDPOINT} returns 500 Internal Server Error â€” must return 405 Method Not Allowed`,
  775 |             { method: method.toUpperCase(), actual_status: res.status, expected_status: 405, body: res.body },
  776 |           );
  777 |         }
  778 | 
  779 |         logger.info('Asserting: response structure and values');
  780 |         expect.soft(res.status,
  781 |           `[BUG-REVT-08] ${method.toUpperCase()} must return 405 Method Not Allowed, got ${res.status}`,
  782 |         ).toBe(405);
  783 | 
  784 |         if (res.status === 405) {
  785 |           expect(res.headers['allow'], '405 response must include Allow header').toBeTruthy();
  786 |           expect(res.headers['allow'], 'Allow header must list GET').toContain('GET');
  787 |         }
  788 |         logger.pass('All assertions passed');
  789 |       });
  790 |     }
  791 |   });
  792 | 
  793 |   // â”€â”€ TC-DREVT-010 â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
  794 |   test('TC-DREVT-010 â€” BUG-REVT-09: Accept: text/html must not expose Spring Boot Whitelabel page', async ({ logger }) => {
  795 |     smokeLabels('TC-DREVT-010', 'BUG-REVT-09 â€” Framework Info Leak on 406', 'normal');
  796 | 
  797 |     let resHtml: supertest.Response;
  798 |     let resXml:  supertest.Response;
  799 | 
  800 |     await logger.step('Step 1 â€” Send GET with Accept: text/html', async () => {
  801 |       logger.info(`GET ${ENDPOINT}`);
  802 |       resHtml = await supertest(BASE_URL)
  803 |         .get(ENDPOINT)
  804 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  805 |         .set('Accept', 'text/html');
  806 |       logger.pass('HTTP ' + resHtml.status + ' received');
  807 | 
  808 |       console.log(`[TC-DREVT-010] Accept: text/html â†’ ${resHtml.status}`);
  809 |       console.log(`  Content-Type : ${resHtml.headers['content-type']}`);
  810 |       console.log(`  Body excerpt : ${String(resHtml.text).substring(0, 400)}`);
  811 |       allure.parameter('text/html status',  String(resHtml.status));
  812 |       allure.parameter('Response Content-Type', String(resHtml.headers['content-type'] ?? 'N/A'));
  813 |     });
  814 | 
  815 |     await logger.step('Step 2 â€” Send GET with Accept: application/xml', async () => {
  816 |       logger.info(`GET ${ENDPOINT}`);
  817 |       resXml = await supertest(BASE_URL)
  818 |         .get(ENDPOINT)
  819 |         .set('Authorization', `Bearer ${BEARER_TOKEN}`)
  820 |         .set('Accept', 'application/xml');
  821 |       logger.pass('HTTP ' + resXml.status + ' received');
  822 |       allure.parameter('application/xml status', String(resXml.status));
  823 |     });
  824 | 
  825 |     await logger.step('Step 3 â€” Both must return 406 Not Acceptable', async () => {
  826 |       logger.info('Asserting: response structure and values');
  827 |       expect(resHtml!.status, 'Accept: text/html must return 406').toBe(406);
  828 |       expect(resXml!.status,  'Accept: application/xml must return 406').toBe(406);
  829 |       logger.pass('All assertions passed');
  830 |     });
  831 | 
  832 |     await logger.step('Step 4 â€” BUG-REVT-09: 406 error body must NOT expose Whitelabel page', async () => {
  833 |       const htmlBody      = String(resHtml!.text ?? '');
  834 |       const hasWhitelabel = htmlBody.includes('Whitelabel Error Page');
  835 | 
  836 |       allure.parameter('Whitelabel exposed', String(hasWhitelabel));
  837 | 
  838 |       if (hasWhitelabel) {
  839 |         flagIssue('TC-DREVT-010', 'BUG-REVT-09',
  840 |           'GET /revenue-trend with Accept: text/html returns Spring Boot Whitelabel Error Page â€” ' +
  841 |           'exposes framework identity, server timezone (EAT), and internal path in public response',
  842 |           {
  843 |             status:       resHtml!.status,
  844 |             content_type: resHtml!.headers['content-type'],
  845 |             info_leaked:  ['Spring Boot framework', '/api/v1/dashboard/revenue-trend path', 'Server timezone (EAT)', 'Spring error page structure'],
  846 |             body_excerpt: htmlBody.substring(0, 400),
```