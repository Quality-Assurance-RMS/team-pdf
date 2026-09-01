# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: UI/Dashboard/dash-kpi-tc2-kpi-cards-numeric-values.spec.ts >> Dashboard — KPI Cards >> TC2 — KPI cards display non-empty numeric values
- Location: tests/UI/Dashboard/dash-kpi-tc2-kpi-cards-numeric-values.spec.ts:36:7

# Error details

```
Test timeout of 120000ms exceeded.
```

```
Error: locator.innerText: Target page, context or browser has been closed
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
        - link "Invoice" [ref=e21] [cursor=pointer]:
          - /url: /invoice
          - img [ref=e22]
          - generic [ref=e24]: Invoice
        - generic [ref=e26]:
          - link "Reconciliation" [ref=e29] [cursor=pointer]:
            - /url: /reconciliation
            - img [ref=e30]
            - generic [ref=e32]: Reconciliation
          - button "Expand Reconciliation" [ref=e33]:
            - img [ref=e34]
        - link "Settlements" [ref=e37] [cursor=pointer]:
          - /url: /settlements
          - img [ref=e38]
          - generic [ref=e40]: Settlements
        - link "Payouts" [ref=e42] [cursor=pointer]:
          - /url: /payouts
          - img [ref=e43]
          - generic [ref=e45]: Payouts
        - link "SLA" [ref=e47] [cursor=pointer]:
          - /url: /service-level-agreement/list
          - img [ref=e48]
          - generic [ref=e50]: SLA
        - link "Reports" [ref=e52] [cursor=pointer]:
          - /url: /reports
          - img [ref=e53]
          - generic [ref=e55]: Reports
        - generic [ref=e57]:
          - link "Administration" [ref=e60] [cursor=pointer]:
            - /url: /psp-files
            - img [ref=e61]
            - generic [ref=e63]: Administration
          - button "Expand Administration" [ref=e64]:
            - img [ref=e65]
      - generic [ref=e68]:
        - generic [ref=e69]:
          - generic: Awaiting Settlement
        - paragraph [ref=e71]: Awaiting Settlement
        - generic [ref=e72]:
          - button [ref=e73]:
            - img [ref=e74]
          - generic [ref=e76]:
            - button [ref=e77]:
              - img [ref=e78]
            - generic: Refresh status
      - button "Logout" [ref=e81] [cursor=pointer]:
        - img [ref=e82]
        - generic [ref=e84]: Logout
      - button "Collapse sidebar" [ref=e85]:
        - img [ref=e86]
    - generic [ref=e88]:
      - banner [ref=e89]:
        - generic [ref=e90]:
          - generic [ref=e93]: A
          - generic [ref=e95]:
            - generic [ref=e96]: Ashil
            - generic [ref=e97]:
              - generic [ref=e98]: ADMIN
              - generic [ref=e99]: PLATFORM
      - main [ref=e101]:
        - generic [ref=e102]:
          - generic [ref=e103]:
            - generic [ref=e104]:
              - generic [ref=e106]:
                - heading "Hi, Ashil" [level=2] [ref=e108]
                - paragraph [ref=e109]: Here's what's happening with eCitizen Last 7 Days.
              - combobox [ref=e111]:
                - option "Today"
                - option "Last 7 Days" [selected]
                - option "Last 30 Days"
                - option "Last 90 Days"
                - option "Custom Range"
            - generic [ref=e114]:
              - generic [ref=e115]:
                - generic [ref=e116]:
                  - generic [ref=e117]:
                    - img [ref=e119]
                    - generic [ref=e121]:
                      - paragraph [ref=e122]: Revenue generated
                      - paragraph [ref=e123]: Revenue across all MCDA'S
                  - button "View Details →" [ref=e124] [cursor=pointer]
                - generic [ref=e126]:
                  - paragraph [ref=e127]: Total Revenue (KES) & (USD)
                  - generic [ref=e128]:
                    - generic [ref=e129]:
                      - generic [ref=e130]:
                        - img [ref=e131]
                        - generic [ref=e139]: KES 290.2K
                      - generic: KES 290,183.14
                    - generic [ref=e140]:
                      - generic [ref=e141]: +44.3%
                      - img [ref=e142]
                  - generic [ref=e144]:
                    - generic [ref=e145]:
                      - generic [ref=e146]:
                        - img [ref=e147]
                        - generic [ref=e157]: USD 0
                      - generic: USD 0.00
                    - generic [ref=e158]:
                      - generic [ref=e159]: "-100%"
                      - img [ref=e160]
              - generic [ref=e162]:
                - generic [ref=e163]:
                  - generic [ref=e164]:
                    - img [ref=e166]
                    - generic [ref=e169]:
                      - paragraph [ref=e170]: No. of Transactions
                      - paragraph [ref=e171]: 509 transactions
                  - button "View Transactions →" [ref=e172] [cursor=pointer]
                - generic [ref=e174]:
                  - paragraph [ref=e175]: Total Transactions (KES) & (USD)
                  - generic [ref=e176]:
                    - generic [ref=e177]:
                      - img [ref=e178]
                      - generic [ref=e186]: "509"
                    - generic [ref=e187]:
                      - generic [ref=e188]: +100%
                      - img [ref=e189]
                  - generic [ref=e191]:
                    - generic [ref=e192]:
                      - img [ref=e193]
                      - generic [ref=e203]: "0"
                    - generic [ref=e204]:
                      - generic [ref=e205]: "-100%"
                      - img [ref=e206]
              - generic [ref=e208]:
                - generic [ref=e209]:
                  - generic [ref=e210]:
                    - img [ref=e212]
                    - generic [ref=e216]:
                      - paragraph [ref=e217]: Total Transactions
                      - paragraph [ref=e218]: 509 Transactions
                  - button "View Details →" [ref=e219] [cursor=pointer]
                - generic [ref=e221]:
                  - generic [ref=e222]:
                    - generic [ref=e223]:
                      - generic [ref=e224]: Successful Transactions
                      - generic [ref=e225]: 0.0 —
                    - paragraph [ref=e226]: 100%
                    - paragraph [ref=e227]: "509"
                  - generic [ref=e229]:
                    - generic [ref=e230]:
                      - generic [ref=e231]: Failed Transactions
                      - generic [ref=e232]: 0.0 —
                    - paragraph [ref=e233]: 0%
                    - paragraph [ref=e234]: "0"
            - generic [ref=e236]:
              - heading "Gateway Payments" [level=2] [ref=e239]
              - generic [ref=e240]:
                - generic [ref=e241]:
                  - generic [ref=e242]:
                    - paragraph [ref=e243]: "509"
                    - paragraph [ref=e244]: Transactions (last 7 Days)
                  - generic [ref=e245]:
                    - paragraph [ref=e246]: 100%
                    - paragraph [ref=e247]: Success Rate
                  - generic [ref=e248]:
                    - paragraph [ref=e249]: 100%
                    - paragraph [ref=e250]: Availability
                  - generic [ref=e251]:
                    - paragraph [ref=e252]: 0 /sec
                    - paragraph [ref=e253]: P95 Latency
                  - generic [ref=e254]:
                    - paragraph [ref=e255]: 0%
                    - paragraph [ref=e256]: Timeout Rate
                - generic [ref=e257]:
                  - generic [ref=e259]:
                    - generic [ref=e260]:
                      - paragraph [ref=e262]: Transactions by Payment Method
                      - img [ref=e267]:
                        - generic [ref=e277]: Mobile money
                    - generic [ref=e278]:
                      - generic [ref=e279]:
                        - paragraph [ref=e280]: Success vs Failure by Method
                        - generic [ref=e282]:
                          - generic [ref=e283]: Success
                          - generic [ref=e285]: Failure
                      - img [ref=e290]:
                        - generic [ref=e300]: Mobile money
                  - generic [ref=e302]:
                    - paragraph [ref=e303]: Share of Payment Methods
                    - generic [ref=e306]:
                      - paragraph [ref=e307]: Mobile money
                      - paragraph [ref=e308]: 100.00%
          - generic [ref=e310]:
            - generic [ref=e311]:
              - generic [ref=e312]:
                - paragraph [ref=e313]: Monthly Revenue for All Services
                - generic [ref=e314]:
                  - img [ref=e316]
                  - generic: Showing last 12 months · auto-updates daily
              - generic [ref=e319]:
                - button "KES" [ref=e320]
                - button "USD" [ref=e321]
            - generic [ref=e322]:
              - generic [ref=e323]:
                - generic [ref=e324]:
                  - generic [ref=e326]:
                    - generic [ref=e327]: KES 941.7M
                    - generic: KES 941,691,659.22
                  - generic [ref=e330]: Gross Amount
                - generic [ref=e331]:
                  - generic [ref=e333]:
                    - generic [ref=e334]: KES 929.1M
                    - generic: KES 929,096,007.12
                  - generic [ref=e337]: Net Amount
                - generic [ref=e338]:
                  - generic [ref=e340]:
                    - generic [ref=e341]: KES 12.6M
                    - generic: KES 12,595,635.10
                  - generic [ref=e344]: Access Fee
                - generic [ref=e345]:
                  - paragraph [ref=e346]: 100%
                  - generic [ref=e349]: Transaction Success Ratio
              - generic [ref=e350]:
                - generic [ref=e351]:
                  - generic [ref=e354]: Gross KES
                  - generic [ref=e357]: Net KES
                  - generic [ref=e360]: Access Fee
                - generic [ref=e361]:
                  - img [ref=e364]:
                    - generic [ref=e383]:
                      - generic [ref=e384]: Dec
                      - generic [ref=e385]: Jan
                      - generic [ref=e386]: Feb
                      - generic [ref=e387]: Mar
                      - generic [ref=e388]: Apr
                      - generic [ref=e389]: May
                      - generic [ref=e390]: Jun
                      - generic [ref=e391]: Jul
                      - generic [ref=e392]: Aug
                      - generic [ref=e393]: Sep
                      - generic [ref=e394]: Oct
                      - generic [ref=e395]: Nov
                    - generic [ref=e396]:
                      - generic [ref=e397]: "0"
                      - generic "250.0M" [ref=e398]: 250.…
                      - generic "500.0M" [ref=e399]: 500.…
                      - generic "750.0M" [ref=e400]: 750.…
                      - generic "1000.0M" [ref=e401]: 100…
                      - generic "1250.0M" [ref=e402]: 1250…
                  - generic [ref=e404]:
                    - img [ref=e406]
                    - generic: "Access Fee: 12,055,500.95"
                  - generic [ref=e410]:
                    - img [ref=e412]
                    - generic: "Gross KES: 4,777,300.00"
                  - generic [ref=e416]:
                    - img [ref=e418]
                    - generic: "Net KES: 4,777,200.00"
                  - generic [ref=e422]:
                    - img [ref=e424]
                    - generic: "Access Fee: 100.00"
                  - generic [ref=e428]:
                    - img [ref=e430]
                    - generic: "Gross KES: 660,496.50"
                  - generic [ref=e434]:
                    - img [ref=e436]
                    - generic: "Net KES: 617,299.00"
                  - generic [ref=e440]:
                    - img [ref=e442]
                    - generic: "Access Fee: 43,197.50"
                  - generic [ref=e446]:
                    - img [ref=e448]
                    - generic: "Gross KES: 75,759,791.90"
                  - generic [ref=e452]:
                    - img [ref=e454]
                    - generic: "Net KES: 75,269,241.20"
                  - generic [ref=e458]:
                    - img [ref=e460]
                    - generic: "Access Fee: 490,550.70"
                  - generic [ref=e464]:
                    - img [ref=e466]
                    - generic: "Gross KES: 122,715.27"
                  - generic [ref=e470]:
                    - img [ref=e472]
                    - generic: "Net KES: 122,170.27"
                  - generic [ref=e476]:
                    - img [ref=e478]
                    - generic: "Access Fee: 545.00"
                  - generic [ref=e482]:
                    - img [ref=e484]
                    - generic: "Gross KES: 7,096,385.05"
                  - generic [ref=e488]:
                    - img [ref=e490]
                    - generic: "Net KES: 7,090,627.10"
                  - generic [ref=e494]:
                    - img [ref=e496]
                    - generic: "Access Fee: 5,740.95"
  - region "Notifications alt+T"
```

# Test source

```ts
  1  | import { Locator } from '@playwright/test';
  2  | 
  3  | /**
  4  |  * Walks up from a label element ancestor-by-ancestor until it finds a level
  5  |  * whose innerText satisfies `pattern`, and returns that text.
  6  |  *
  7  |  * Card/widget containers are frequently restyled with new wrapper divs, which
  8  |  * breaks any fixed "climb exactly N ancestors" assumption. Climbing adaptively
  9  |  * until the expected value pattern actually appears is resilient to that
  10 |  * churn without needing to know the exact DOM depth in advance.
  11 |  */
  12 | export async function getAncestorTextMatching(
  13 |   label: Locator,
  14 |   pattern: RegExp,
  15 |   maxDepth = 6
  16 | ): Promise<string> {
  17 |   for (let depth = 1; depth <= maxDepth; depth++) {
  18 |     const text = await label.locator(`xpath=ancestor::*[${depth}]`).innerText().catch(() => '');
  19 |     if (pattern.test(text)) return text;
  20 |   }
> 21 |   return label.innerText();
     |                ^ Error: locator.innerText: Target page, context or browser has been closed
  22 | }
  23 | 
```