# Ingest notes · SafeLink daily / biweekly reports by promo code

Promo codes split **2026-11-09 at about 3:40 PM ET** (`dailyReports.promoCodeSplitEffective` stays the date `2026-11-09`; time is in `promoCodeSplitEffectiveAt` / `promoCodeSplitEffectiveTimeEt`). Keep the date field as `YYYY-MM-DD` so the dashboard can compare it to row dates.

Each campaign uses its own final URL:

| Promo | Campaign | `segmentId` | URL |
|---|---|---|---|
| **ABJ0020** | Nationwide Search (US ex CA) | `nationwide` | `…?promotionCode=ABJ0020` |
| **ABJ0021** | CA Search (SafeLink - CA) | `ca_search` | `…?promotionCode=ABJ0021` |
| **ABJ0022** | CA Display (SafeLink - CA Display) | `ca_display` | `…?promotionCode=ABJ0022` |
| **ABJ0023** | CA Demand Gen | `ca_demand_gen` | `…?promotionCode=ABJ0023` |
| **ABJ0024** | CA Competitors (not live yet) | `ca_competitors` | `…?promotionCode=ABJ0024` |

Base URL: `https://www.safelinkwireless.com/en/#!/newHome?promotionCode=<CODE>`

## Pre-split rows (Nov 6–8, 2026)

Before the split, **all three campaigns** used **ABJ0020**. Those rows stay:

- `segmentId`: `"all"`
- `promoCode`: `"ABJ0020"`
- `preSplit`: `true`

**Do not** reassign them to Nationwide. They remain the combined pre-split baseline on the Combined tab.

## Nov 9 is a partial split day

Codes went live at about **3:40 PM ET**. Before then, all clicks still used ABJ0020. Nov 9 per-code `spendUsd` is an approximation: each campaign's **full-day** Google Ads spend is attributed to that campaign's code. Mark Nov 9 rows `partial: true` and keep the note on `dailyReports.promoCodeSplitNote`.

## Adding a daily report row (post-split)

Append one object to `dailyReports.rows[]` **per promo code present in the report**:

```json
{
  "date": "2026-11-09",
  "segmentId": "nationwide",
  "promoCode": "ABJ0020",
  "qualified": 1,
  "pending": 0,
  "unqualified": 0,
  "rejected": 0,
  "total": 1,
  "spendUsd": 46.25,
  "source": "SafeLink daily report"
}
```

Rules:

1. Set `promoCode` and matching `segmentId` from the table above.
2. Never invent counts — use the report, or leave totals at `0`.
3. After adding rows, refresh `dailyReports.byPromoCode.<CODE>` **cumulative totals from 2026-11-09** (`qualified`, `pending`, `unqualified`, `rejected`, `total`). Set `sinceDate` / `throughDate` (and `byPromoCodeSinceDate` / `byPromoCodeThroughDate`) to the tally window.
4. Set `spendUsd` on that promo’s `byPromoCode` entry to **that campaign’s ad spend from 2026-11-09 through `throughDate`**. Then:
   - `costPerApplicationUsd = spendUsd / total` (Javier's main metric; counts every status; only when `total > 0`)
   - `costPerQualifiedUsd = spendUsd / qualified` (only when `qualified > 0`, else `null`)
   - Keep `costPerEnrollmentUsd` equal to `costPerApplicationUsd` for the existing UI
5. Update `dailyReports.summary` only for the **pre-split** combined block (`segmentId: "all"`). Do not fold post-split promo rows into it.
6. Bump `meta.updatedAt`, commit, and push to `main` (GitHub Pages redeploys in ~1 minute).

## Biweekly enrollments

Same mapping. Each row in `biweeklyEnrollments.rows[]` should include `promoCode` + `segmentId`. Pre-split biweekly periods use `segmentId: "all"` and `promoCode: "ABJ0020"`.

## Dashboard behavior

- The “Enrollments by promo code” panel shows the **cumulative post-split tally** (`byPromoCode`) from Nov 9, including cost / application and cost / qualified.
- Combined tab also shows the **pre-split** Nov 6–8 block, labeled clearly, plus post-split daily rows by code.
- Combined post-split total = ABJ0020 + ABJ0021 + ABJ0022 + ABJ0023 (excludes pre-split). Omit ABJ0024 until it is live (`notLive: true` if a placeholder row is needed).
