# Ingest notes · SafeLink daily / biweekly reports by promo code

Promo codes split **2026-11-09**. Each campaign uses its own final URL:

| Promo | Campaign | `segmentId` | URL |
|---|---|---|---|
| **ABJ0020** | Nationwide (SafeLink - US ex CA) | `nationwide` | `…?promotionCode=ABJ0020` |
| **ABJ0021** | CA Search (SafeLink - CA) | `ca_search` | `…?promotionCode=ABJ0021` |
| **ABJ0022** | CA Display (SafeLink - CA Display) | `ca_display` | `…?promotionCode=ABJ0022` |

Base URL: `https://www.safelinkwireless.com/en/#!/newHome?promotionCode=<CODE>`

## Pre-split rows (Nov 6–8, 2026)

Before the split, **all three campaigns** used **ABJ0020**. Those rows stay:

- `segmentId`: `"all"`
- `promoCode`: `"ABJ0020"`
- `preSplit`: `true`

**Do not** reassign them to Nationwide. They remain the combined pre-split baseline on the Combined tab.

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
  "source": "SafeLink daily report"
}
```

Rules:

1. Set `promoCode` and matching `segmentId` from the table above.
2. Never invent counts — use the report, or leave totals at `0`.
3. After adding rows, refresh `dailyReports.byPromoCode.<CODE>` totals (`qualified`, `pending`, `unqualified`, `rejected`, `total`).
4. For cost / enrollment, set `spendUsd` on that promo’s `byPromoCode` entry to **that campaign’s ad spend from 2026-11-09 on**, then `costPerEnrollmentUsd = spendUsd / total` (only when `total > 0`). Leave `spendUsd` / `costPerEnrollmentUsd` as `null` until spend for that window is known.
5. Update `dailyReports.summary` only for the **pre-split** combined block (`segmentId: "all"`). Do not fold post-split promo rows into it.
6. Bump `meta.updatedAt`, commit, and push to `main` (GitHub Pages redeploys in ~1 minute).

## Biweekly enrollments

Same mapping. Each row in `biweeklyEnrollments.rows[]` should include `promoCode` + `segmentId`. Pre-split biweekly periods use `segmentId: "all"` and `promoCode: "ABJ0020"`.

## Dashboard behavior

- Segment cards and the “Enrollments by promo code” panel show **post-split** totals per code.
- Combined tab also shows the **pre-split** Nov 6–8 block, labeled clearly.
- Combined post-split total = ABJ0020 + ABJ0021 + ABJ0022 (excludes pre-split).
