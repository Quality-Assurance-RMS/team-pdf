# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: database-testing/dashboard-kpi-vs-db.spec.ts >> Database Testing — Dashboard KPI reconciliation (Last 30 Days) >> TC1 — No of Transactions matches the database total for the selected period
- Location: tests/database-testing/dashboard-kpi-vs-db.spec.ts:51:9

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.innerText: Test timeout of 120000ms exceeded.
Call log:
  - waiting for getByText('No of Transactions', { exact: true }).locator('..').locator('..').locator('..').locator('..')

```

# Page snapshot

```yaml
- generic [ref=e2]:
  - generic [ref=e3]:
    - complementary [ref=e4]:
      - img "eCitizen" [ref=e8] [cursor=pointer]
      - navigation [ref=e9]:
        - link "Dashboard" [ref=e11] [cursor=pointer]:
          - /url: /dashboard
          - img [ref=e12]
          - generic [ref=e14]: Dashboard
        - link "Transactions" [ref=e16] [cursor=pointer]:
          - /url: /transaction
          - img [ref=e17]
          - generic [ref=e19]: Transactions
        - generic [ref=e21]:
          - link "Reconciliation" [ref=e24] [cursor=pointer]:
            - /url: /reconciliation
            - img [ref=e25]
            - generic [ref=e27]: Reconciliation
          - button "Expand Reconciliation" [ref=e28]:
            - img [ref=e29]
        - link "Settlements" [ref=e32] [cursor=pointer]:
          - /url: /settlements
          - img [ref=e33]
          - generic [ref=e35]: Settlements
        - link "Payouts" [ref=e37] [cursor=pointer]:
          - /url: /payouts
          - img [ref=e38]
          - generic [ref=e40]: Payouts
        - link "SLA" [ref=e42] [cursor=pointer]:
          - /url: /service-level-agreement/list
          - img [ref=e43]
          - generic [ref=e45]: SLA
        - link "Reports" [ref=e47] [cursor=pointer]:
          - /url: /reports
          - img [ref=e48]
          - generic [ref=e50]: Reports
        - generic [ref=e52]:
          - link "Administration" [ref=e55] [cursor=pointer]:
            - /url: /psp-files
            - img [ref=e56]
            - generic [ref=e58]: Administration
          - button "Expand Administration" [ref=e59]:
            - img [ref=e60]
      - generic [ref=e63]:
        - generic [ref=e64]:
          - generic: Awaiting Settlement
        - paragraph [ref=e66]: Awaiting Settlement
        - generic [ref=e67]:
          - button [ref=e68]:
            - img [ref=e69]
          - generic [ref=e71]:
            - button [ref=e72]:
              - img [ref=e73]
            - generic: Refresh status
      - button "Logout" [ref=e76] [cursor=pointer]:
        - img [ref=e77]
        - generic [ref=e79]: Logout
      - button "Collapse sidebar" [ref=e80]:
        - img [ref=e81]
    - generic [ref=e83]:
      - banner [ref=e84]:
        - generic [ref=e85]:
          - button "Notifications" [ref=e86]:
            - img [ref=e87]
          - generic [ref=e93]: A
          - generic [ref=e95]:
            - generic [ref=e96]: Ashil
            - generic [ref=e97]:
              - generic [ref=e98]: SUPER_ADMIN
              - generic [ref=e99]: PLATFORM
      - main [ref=e101]:
        - generic [ref=e102]:
          - generic [ref=e103]:
            - generic [ref=e104]:
              - generic [ref=e106]:
                - heading "Hi, Ashil" [level=2] [ref=e107]
                - paragraph [ref=e108]: Here's what's happening with eCitizen Last 30 Days.
              - combobox [ref=e110]:
                - option "Today"
                - option "Last 7 Days"
                - option "Last 30 Days" [selected]
                - option "Last 90 Days"
                - option "Custom Range"
            - generic [ref=e113]:
              - generic [ref=e114]:
                - generic [ref=e115]:
                  - generic [ref=e116]:
                    - img [ref=e118]
                    - generic [ref=e120]:
                      - paragraph [ref=e121]: Revenue generated
                      - paragraph [ref=e122]: Revenue across all MCDA'S
                  - button "View Details →" [ref=e123] [cursor=pointer]
                - generic [ref=e125]:
                  - paragraph [ref=e126]: Total Revenue (KES) & (USD)
                  - generic [ref=e127]:
                    - generic [ref=e128]:
                      - generic [ref=e129]:
                        - img [ref=e130]
                        - generic [ref=e138]: KES 21.2M
                      - generic: KES 21,176,733.00
                    - generic [ref=e139]:
                      - generic [ref=e140]: +95.4%
                      - img [ref=e141]
                  - generic [ref=e143]:
                    - generic [ref=e144]:
                      - generic [ref=e145]:
                        - img [ref=e146]
                        - generic [ref=e156]: USD 10.9M
                      - generic: USD 10,944,475.00
                    - generic [ref=e157]:
                      - generic [ref=e158]: +100%
                      - img [ref=e159]
              - generic [ref=e161]:
                - generic [ref=e162]:
                  - generic [ref=e163]:
                    - img [ref=e165]
                    - generic [ref=e168]:
                      - paragraph [ref=e169]: No. of Transactions
                      - paragraph [ref=e170]: 1,474,208 transactions
                  - button "View Transactions →" [ref=e171] [cursor=pointer]
                - generic [ref=e173]:
                  - paragraph [ref=e174]: Total Transactions (KES) & (USD)
                  - generic [ref=e175]:
                    - generic [ref=e176]:
                      - img [ref=e177]
                      - generic [ref=e185]: 744,617
                    - generic [ref=e186]:
                      - generic [ref=e187]: +100%
                      - img [ref=e188]
                  - generic [ref=e190]:
                    - generic [ref=e191]:
                      - img [ref=e192]
                      - generic [ref=e202]: 729,591
                    - generic [ref=e203]:
                      - generic [ref=e204]: +100%
                      - img [ref=e205]
              - generic [ref=e207]:
                - generic [ref=e208]:
                  - generic [ref=e209]:
                    - img [ref=e211]
                    - generic [ref=e215]:
                      - paragraph [ref=e216]: Total Transactions
                      - paragraph [ref=e217]: 1,474,208 Transactions
                  - button "View Details →" [ref=e218] [cursor=pointer]
                - generic [ref=e220]:
                  - generic [ref=e221]:
                    - generic [ref=e222]:
                      - generic [ref=e223]: Successful Transactions
                      - generic [ref=e224]: 0.0 —
                    - paragraph [ref=e225]: 100%
                    - paragraph [ref=e226]: 1.5M
                  - generic [ref=e228]:
                    - generic [ref=e229]:
                      - generic [ref=e230]: Failed Transactions
                      - generic [ref=e231]: 0.0 —
                    - paragraph [ref=e232]: 0%
                    - paragraph [ref=e233]: "0"
            - generic [ref=e235]:
              - heading "Gateway Payments" [level=2] [ref=e238]
              - generic [ref=e239]:
                - generic [ref=e240]:
                  - generic [ref=e241]:
                    - paragraph [ref=e242]: "1474208"
                    - paragraph [ref=e243]: Transactions (last 30 Days)
                  - generic [ref=e244]:
                    - paragraph [ref=e245]: 100%
                    - paragraph [ref=e246]: Success Rate
                  - generic [ref=e247]:
                    - paragraph [ref=e248]: 100%
                    - paragraph [ref=e249]: Availability
                  - generic [ref=e250]:
                    - paragraph [ref=e251]: 86 /sec
                    - paragraph [ref=e252]: P95 Latency
                  - generic [ref=e253]:
                    - paragraph [ref=e254]: 0%
                    - paragraph [ref=e255]: Timeout Rate
                - generic [ref=e256]:
                  - generic [ref=e258]:
                    - generic [ref=e259]:
                      - paragraph [ref=e261]: Transactions by Payment Method
                      - img [ref=e266]:
                        - generic [ref=e276]:
                          - generic [ref=e277]: Card
                          - generic [ref=e278]: Mobile money
                          - generic [ref=e279]: Card payment
                    - generic [ref=e280]:
                      - generic [ref=e281]:
                        - paragraph [ref=e282]: Success vs Failure by Method
                        - generic [ref=e284]:
                          - generic [ref=e285]: Success
                          - generic [ref=e287]: Failure
                      - img [ref=e292]:
                        - generic [ref=e303]:
                          - generic [ref=e304]: Card
                          - generic [ref=e305]: Mobile money
                          - generic [ref=e306]: Card payment
                  - generic [ref=e308]:
                    - paragraph [ref=e309]: Share of Payment Methods
                    - generic [ref=e311]:
                      - generic [ref=e312]:
                        - paragraph [ref=e313]: Card
                        - paragraph [ref=e314]: 0.00%
                      - generic [ref=e315]:
                        - paragraph [ref=e316]: Mobile money
                        - paragraph [ref=e317]: 19.66%
                      - generic [ref=e318]:
                        - paragraph [ref=e319]: Card payment
                        - paragraph [ref=e320]: 80.34%
          - generic [ref=e322]:
            - generic [ref=e323]:
              - generic [ref=e324]:
                - paragraph [ref=e325]: Monthly Revenue for All Services
                - generic [ref=e326]:
                  - img [ref=e328]
                  - generic: Showing last 12 months · auto-updates daily
              - generic [ref=e331]:
                - button "KES" [ref=e332]
                - button "USD" [ref=e333]
            - generic [ref=e334]:
              - generic [ref=e335]:
                - generic [ref=e336]:
                  - generic [ref=e338]:
                    - generic [ref=e339]: KES 60.0M
                    - generic: KES 59,984,146.31
                  - generic [ref=e342]: Gross Amount
                - generic [ref=e343]:
                  - generic [ref=e345]:
                    - generic [ref=e346]: KES 54.0M
                    - generic: KES 53,964,994.07
                  - generic [ref=e349]: Net Amount
                - generic [ref=e350]:
                  - generic [ref=e352]:
                    - generic [ref=e353]: KES 6.0M
                    - generic: KES 6,019,152.24
                  - generic [ref=e356]: Access Fee
                - generic [ref=e357]:
                  - paragraph [ref=e358]: 100%
                  - generic [ref=e361]: Transaction Success Ratio
              - generic [ref=e362]:
                - generic [ref=e363]:
                  - generic [ref=e366]: Gross KES
                  - generic [ref=e369]: Net KES
                  - generic [ref=e372]: Access Fee
                - generic [ref=e373]:
                  - img [ref=e376]:
                    - generic [ref=e411]:
                      - generic [ref=e412]: Oct
                      - generic [ref=e413]: Nov
                      - generic [ref=e414]: Dec
                      - generic [ref=e415]: Jan
                      - generic [ref=e416]: Feb
                      - generic [ref=e417]: Mar
                      - generic [ref=e418]: Apr
                      - generic [ref=e419]: May
                      - generic [ref=e420]: Jun
                      - generic [ref=e421]: Jul
                      - generic [ref=e422]: Aug
                      - generic [ref=e423]: Sep
                    - generic [ref=e424]:
                      - generic [ref=e425]: "0"
                      - generic [ref=e426]: 10.0M
                      - generic [ref=e427]: 20.0M
                      - generic [ref=e428]: 30.0M
                  - generic [ref=e430]:
                    - img [ref=e432]
                    - generic: "Access Fee: 799,969.64"
                  - generic [ref=e436]:
                    - img [ref=e438]
                    - generic: "Gross KES: 2,123,894.37"
                  - generic [ref=e442]:
                    - img [ref=e444]
                    - generic: "Net KES: 2,102,655.39"
                  - generic [ref=e448]:
                    - img [ref=e450]
                    - generic: "Access Fee: 21,238.98"
                  - generic [ref=e454]:
                    - img [ref=e456]
                    - generic: "Gross KES: 388,099.60"
                  - generic [ref=e460]:
                    - img [ref=e462]
                    - generic: "Net KES: 330,360.12"
                  - generic [ref=e466]:
                    - img [ref=e468]
                    - generic: "Access Fee: 57,739.48"
                  - generic [ref=e472]:
                    - img [ref=e474]
                    - generic: "Gross KES: 1,500,501.22"
                  - generic [ref=e478]:
                    - img [ref=e480]
                    - generic: "Net KES: 1,260,426.74"
                  - generic [ref=e484]:
                    - img [ref=e486]
                    - generic: "Access Fee: 240,074.48"
                  - generic [ref=e490]:
                    - img [ref=e492]
                    - generic: "Gross KES: 1,998,946.98"
                  - generic [ref=e496]:
                    - img [ref=e498]
                    - generic: "Net KES: 1,679,031.22"
                  - generic [ref=e502]:
                    - img [ref=e504]
                    - generic: "Access Fee: 319,915.76"
                  - generic [ref=e508]:
                    - img [ref=e510]
                    - generic: "Gross KES: 1,998,369.88"
                  - generic [ref=e514]:
                    - img [ref=e516]
                    - generic: "Net KES: 1,678,669.66"
                  - generic [ref=e520]:
                    - img [ref=e522]
                    - generic: "Access Fee: 319,700.22"
                  - generic [ref=e526]:
                    - img [ref=e528]
                    - generic: "Access Fee: 2,398,134.05"
                  - generic [ref=e532]:
                    - img [ref=e534]
                    - generic: "Gross KES: 35,168.49"
                  - generic [ref=e538]:
                    - img [ref=e540]
                    - generic: "Net KES: 30,064.23"
                  - generic [ref=e544]:
                    - img [ref=e546]
                    - generic: "Access Fee: 5,104.26"
                  - generic [ref=e550]:
                    - img [ref=e552]
                    - generic: "Access Fee: 1,233.00"
                  - generic [ref=e556]:
                    - img [ref=e558]
                    - generic: "Access Fee: 1,855,966.73"
                  - generic [ref=e562]:
                    - img [ref=e564]
                    - generic: "Gross KES: 7,623.63"
                  - generic [ref=e568]:
                    - img [ref=e570]
                    - generic: "Net KES: 7,547.99"
                  - generic [ref=e574]:
                    - img [ref=e576]
                    - generic: "Access Fee: 75.64"
  - region "Notifications alt+T"
```

# Test source

```ts
  1   | import { test, expect, Page, Locator } from '@playwright/test';
  2   | import { DashboardPage } from '../pages/DashboardPage';
  3   | import { getDailySummaryTotals, mapDateFilterLabelToDays } from './dashboardDbHelpers';
  4   | import { parseAbbreviatedNumber, parseLastPercent, withinTolerancePct } from '../utils/numberParsing';
  5   | 
  6   | // ---------------------------------------------------------------------------
  7   | // Dashboard KPI cards vs. database — Konza UAT
  8   | //
  9   | // Confirms the "No of Transactions", "Total Transactions" (Successful /
  10  | // Failed %), and "Revenue generated" (KES & USD) KPI cards on the RMS
  11  | // dashboard actually reconcile with the source-of-truth data in
  12  | // rms_analytics.daily_summary (ClickHouse), not just that the cards render.
  13  | //
  14  | // Runs once per date filter in DATE_FILTERS_TO_CHECK below. The date
  15  | // filter is a native <select> (see DashboardPage.selectDateFilter) —
  16  | // selectOption() drives it directly, and its current selection must be
  17  | // read via inputValue() (the option's value, e.g. "last-7-days"), not
  18  | // innerText() (which on a native <select> returns every option's text
  19  | // concatenated, not just the selected one).
  20  | //
  21  | // The card containers below were located by walking the live DOM (there is
  22  | // no data-testid or "card"/"widget" class on these elements, despite
  23  | // DashboardPage.ts assuming one) — each KPI heading sits inside its own
  24  | // self-contained container exactly 4 ancestors up, and each Successful/
  25  | // Failed Transactions sub-metric is self-contained 2 ancestors up from its
  26  | // own label. Re-verify these depths with a live DOM dump if this UI is
  27  | // restructured; they are not guaranteed to stay stable.
  28  | //
  29  | // The "Revenue generated" figures are rendered abbreviated (e.g. "KES
  30  | // 1.1M"), which is inherently imprecise, so that comparison uses a wider
  31  | // tolerance than the transaction counts and percentages, which are exact.
  32  | //
  33  | // Requires the WireGuard tunnel to be active — same requirement as
  34  | // tests/DB/*.spec.ts and the Postgres-backed reconciliation tests.
  35  | // ---------------------------------------------------------------------------
  36  | 
  37  | const DATE_FILTERS_TO_CHECK = ['Last 7 Days', 'Last 30 Days'];
  38  | 
  39  | /** The KPI card heading and its metric values render 4 ancestors up as one self-contained block. */
  40  | function cardContainer(page: Page, exactLabel: string): Locator {
  41  |   return page.getByText(exactLabel, { exact: true }).locator('..').locator('..').locator('..').locator('..');
  42  | }
  43  | 
  44  | /** The Successful/Failed Transactions sub-metric renders 2 ancestors up from its own label as one self-contained block. */
  45  | function metricContainer(page: Page, exactLabel: string): Locator {
  46  |   return page.getByText(exactLabel, { exact: true }).locator('..').locator('..');
  47  | }
  48  | 
  49  | for (const filterLabel of DATE_FILTERS_TO_CHECK) {
  50  |   test.describe(`Database Testing — Dashboard KPI reconciliation (${filterLabel})`, () => {
  51  |     test('TC1 — No of Transactions matches the database total for the selected period', async ({ page }) => {
  52  |       const dashboardPage = new DashboardPage(page);
  53  |       await dashboardPage.goto();
  54  |       await dashboardPage.assertPageLoaded();
  55  |       await dashboardPage.selectDateFilter(filterLabel);
  56  | 
  57  |       const days = mapDateFilterLabelToDays(await dashboardPage.dateFilterDropdown.inputValue());
  58  | 
> 59  |       const cardText = await cardContainer(page, 'No of Transactions').innerText();
      |                                                                        ^ Error: locator.innerText: Test timeout of 120000ms exceeded.
  60  |       const uiTransactionCount = parseAbbreviatedNumber(cardText.match(/([\d,]+(?:\.\d+)?[KMB]?)\s*transactions/i)?.[1] ?? '');
  61  | 
  62  |       const dbTotals = await getDailySummaryTotals(days);
  63  | 
  64  |       console.log(`>> TC1 [${filterLabel}, days=${days}] UI transaction count: ${uiTransactionCount} | DB total_transactions: ${dbTotals.totalTxns}`);
  65  | 
  66  |       expect(
  67  |         withinTolerancePct(uiTransactionCount, dbTotals.totalTxns, 2),
  68  |         `UI showed ${uiTransactionCount} transactions, database sum for the last ${days} day(s) is ${dbTotals.totalTxns}`,
  69  |       ).toBe(true);
  70  |     });
  71  | 
  72  |     test('TC2 — Successful and Failed Transactions percentages match the database', async ({ page }) => {
  73  |       const dashboardPage = new DashboardPage(page);
  74  |       await dashboardPage.goto();
  75  |       await dashboardPage.assertPageLoaded();
  76  |       await dashboardPage.selectDateFilter(filterLabel);
  77  | 
  78  |       const days = mapDateFilterLabelToDays(await dashboardPage.dateFilterDropdown.inputValue());
  79  | 
  80  |       const successfulText = await metricContainer(page, 'Successful Transactions').innerText();
  81  |       const failedText = await metricContainer(page, 'Failed Transactions').innerText();
  82  |       // Each block renders a period-over-period trend percentage before the
  83  |       // actual rate (e.g. "Successful Transactions -100% 0% 0"), so the rate
  84  |       // being reported is the LAST percentage in the block, not the first.
  85  |       const uiSuccessPct = parseLastPercent(successfulText);
  86  |       const uiFailedPct = parseLastPercent(failedText);
  87  | 
  88  |       const dbTotals = await getDailySummaryTotals(days);
  89  |       const dbSuccessPct = dbTotals.totalTxns === 0 ? 0 : (dbTotals.successfulTxns / dbTotals.totalTxns) * 100;
  90  |       const dbFailedPct = dbTotals.totalTxns === 0 ? 0 : (dbTotals.failedTxns / dbTotals.totalTxns) * 100;
  91  | 
  92  |       console.log(
  93  |         `>> TC2 [${filterLabel}, days=${days}] UI success%: ${uiSuccessPct} | DB success%: ${dbSuccessPct.toFixed(2)} ` +
  94  |         `(successTxns=${dbTotals.successfulTxns}/${dbTotals.totalTxns}) || UI failed%: ${uiFailedPct} | DB failed%: ${dbFailedPct.toFixed(2)} ` +
  95  |         `(failedTxns=${dbTotals.failedTxns}/${dbTotals.totalTxns})`,
  96  |       );
  97  | 
  98  |       expect(
  99  |         withinTolerancePct(uiSuccessPct, dbSuccessPct, 1),
  100 |         `UI Successful Transactions ${uiSuccessPct}%, database computes ${dbSuccessPct.toFixed(2)}%`,
  101 |       ).toBe(true);
  102 |       expect(
  103 |         withinTolerancePct(uiFailedPct, dbFailedPct, 1),
  104 |         `UI Failed Transactions ${uiFailedPct}%, database computes ${dbFailedPct.toFixed(2)}%`,
  105 |       ).toBe(true);
  106 |     });
  107 | 
  108 |     test('TC3 — Revenue generated (KES and USD) is consistent with the database gross amount', async ({ page }) => {
  109 |       const dashboardPage = new DashboardPage(page);
  110 |       await dashboardPage.goto();
  111 |       await dashboardPage.assertPageLoaded();
  112 |       await dashboardPage.selectDateFilter(filterLabel);
  113 | 
  114 |       const days = mapDateFilterLabelToDays(await dashboardPage.dateFilterDropdown.inputValue());
  115 | 
  116 |       const cardText = await cardContainer(page, 'Revenue generated').innerText();
  117 | 
  118 |       const kesMatch = cardText.match(/KES\s*([\d,.]+[KMB]?)/i);
  119 |       const usdMatch = cardText.match(/USD\s*([\d,.]+[KMB]?)/i);
  120 |       expect(kesMatch, `Could not find a KES figure in the Revenue generated card: "${cardText}"`).toBeTruthy();
  121 |       expect(usdMatch, `Could not find a USD figure in the Revenue generated card: "${cardText}"`).toBeTruthy();
  122 | 
  123 |       const uiKesRevenue = parseAbbreviatedNumber(kesMatch![1]);
  124 |       const uiUsdRevenue = parseAbbreviatedNumber(usdMatch![1]);
  125 | 
  126 |       const dbTotals = await getDailySummaryTotals(days);
  127 |       const dbKesRevenue = dbTotals.grossAmountByCurrency['KES'] ?? 0;
  128 |       const dbUsdRevenue = dbTotals.grossAmountByCurrency['USD'] ?? 0;
  129 | 
  130 |       console.log(
  131 |         `>> TC3 [${filterLabel}, days=${days}] UI KES revenue: ${uiKesRevenue} | DB KES gross_amount: ${dbKesRevenue} || ` +
  132 |         `UI USD revenue: ${uiUsdRevenue} | DB USD gross_amount: ${dbUsdRevenue}`,
  133 |       );
  134 | 
  135 |       // Wider tolerance than the counts/percentages above: the UI abbreviates
  136 |       // this figure to one decimal place of K/M/B, which is inherently lossy.
  137 |       expect(
  138 |         withinTolerancePct(uiKesRevenue, dbKesRevenue, 10),
  139 |         `UI showed KES ${uiKesRevenue}, database sum for the last ${days} day(s) is KES ${dbKesRevenue}`,
  140 |       ).toBe(true);
  141 |       expect(
  142 |         withinTolerancePct(uiUsdRevenue, dbUsdRevenue, 10),
  143 |         `UI showed USD ${uiUsdRevenue}, database sum for the last ${days} day(s) is USD ${dbUsdRevenue}`,
  144 |       ).toBe(true);
  145 |     });
  146 |   });
  147 | }
  148 | 
```