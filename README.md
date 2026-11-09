# GO Advertising · SafeLink Mission Control

Live dashboard for SafeLink Wireless Google Ads (GO Advertising).

- **Live URL:** https://xavidalmau9.github.io/go-safelink-mission-control/
- **Repo:** https://github.com/xavidalmau9/go-safelink-mission-control
- **Account:** 734-978-8944 · Live since Nov 6, 2026

## How this differs from Assurance Mission Control

SafeLink has **no Google Ads conversion tracking**. Enrollments and activations come from SafeLink’s own reports:

1. **Biweekly enrollment reports** (primary source for activations / cost per enrollment)
2. **Daily reports** (partial info; format TBD — may start arriving Nov 9, 2026)

Ads KPIs (spend, clicks, impressions, CTR, CPC) come from Google Ads reviews pasted into `data.json`.

## Segments (tabs)

| Tab | Campaign | ID | Budget |
|---|---|---|---|
| Combined | All three | — | $100/day |
| CA Search | SafeLink - CA | 24426532464 | $40/day |
| CA Display | SafeLink - CA Display | 24440250130 | $20/day |
| Nationwide | SafeLink - US ex CA | 24431672876 | $40/day |

Nationwide excludes CA, NE, OR, SD. Mobile bid adjustment is **0%** (removed Nov 9).


## Promo codes (from 2026-11-09)

| Promo | Campaign | `segmentId` |
|---|---|---|
| ABJ0020 | Nationwide (SafeLink - US ex CA) | `nationwide` |
| ABJ0021 | CA Search (SafeLink - CA) | `ca_search` |
| ABJ0022 | CA Display (SafeLink - CA Display) | `ca_display` |

Pre-split Nov 6–8 enrollments used shared **ABJ0020** and stay as `segmentId: "all"` (not reassigned to Nationwide). See **`ingest-notes.md`** for how to append daily / biweekly rows per promo.

## Updating data

Edit **`data.json`** only (do not invent numbers). Then commit and push to `main` — GitHub Pages redeploys in ~1 minute.

### Where to paste future reports

| Data | Path in `data.json` |
|---|---|
| Overview totals | `overview` |
| Per-segment ads KPIs + ad groups + devices | `bySegment.ca_search` / `ca_display` / `nationwide` |
| Daily spend/clicks series (enables trend line) | `dailyAds` (combined) and/or `bySegment.*.dailyAds` |
| Top / wasted / good search terms | `searchTerms` (set `segmentId` when known) |
| Keyword performance | `keywords[]` |
| Negatives you added | `negativesAdded[]` |
| SafeLink daily report rows (include `promoCode`) | `dailyReports.rows[]` + `dailyReports.byPromoCode` |
| Promo code → campaign map | `promoCodes` |
| SafeLink biweekly enrollments | `biweeklyEnrollments.rows[]` (include `promoCode` + `segmentId`) |
| State performance (choropleth) | `bySegment.*.statePerformance[]` as `{state, spendUsd, clicks, ...}` |
| Recommendations / changelog | `recommendations[]` / `changelog[]` |

Until `dailyAds` has rows, the trend chart shows the **window snapshot** only.

Until `statePerformance` has rows, the US map shows targeting footprint (CA highlight, Nationwide eligible vs excluded) with an “awaiting data” overlay.

Cost per enrollment appears when enrollments exist in reports and can be joined to spend.

## Design

Modeled on [go-mission-control](https://xavidalmau9.github.io/go-mission-control/) (same GO Advertising dark theme, Space Mono + DM Sans, cyan/purple/green accents), rebuilt as a lighter glassmorphic UI with Chart.js + D3-geo map. Free CDN libraries only. No paid services. No Miami Metro branding.

## Local preview

```bash
python3 -m http.server 8080
# open http://localhost:8080
```
