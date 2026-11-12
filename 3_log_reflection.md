# Assignment 2 — The Dirty Ledger
## Audit record and reflection

**Dataset:** `teaching_prices_messy.csv`  
**Audit outcome:** Issues were identified and documented; the source records were not cleaned destructively.

### Outcome at a glance

| Measure | Result |
|---|---:|
| Records and fields | 97 rows × 6 columns |
| Missing source cells | 1 |
| Exact duplicate occurrences | 1 (two matching rows) |
| Repeated date–item–city records, original labels | 2 |
| Repeated records after item-label normalization | 4 |
| Numeric prices obtained | 96 |
| Non-missing price conversion failures | 0 |
| Date conversion failures | 0 |
| Non-positive prices | 0 |
| Distinct dates / cities | 12 / 2 |
| Rows removed or source prices imputed | 0 / 0 |

The notebook is a diagnostic audit. Its additional date and numeric-price fields are created in the working copy; the six original columns remain unchanged.

### Evidence notes

**A missing observation.** One price is blank: `Pulse C`, City 2, dated 2026-01-01 (dataframe index 5). The notebook reports the row and leaves the price missing.

**An exact repeat.** The records at dataframe indices 8 and 96 are identical: `Staple A`, City 1, 2026-01-08, priced at 99.5. Both occurrences are displayed. Neither is removed automatically.

**A label collision.** The item appears as both `Oil B` and `oil b`. The lowercase spelling occurs on 2026-02-05 in City 1, where an `Oil B` record is also present. The two prices are 108.5 and 318. Normalizing case reveals the repeated key, but the price difference means that the records should be checked against the source before either is changed.

**Price formatting and conversion.** The source value `1,250` uses a thousands separator. The notebook handles a correctly grouped thousands separator in a separate conversion series, so the value becomes numeric without changing the original text. Across the non-missing price entries, 96 convert successfully and none fail.

**Values flagged by the IQR screen.** The median price is 200. The 1.5 × IQR rule gives an upper fence of 546.625 and flags two observations: 1,250 (`Staple A`, City 2, 2026-01-15) and 9,999 (`Sweetener D`, City 1, 2026-01-22). The first is still a valid numeric representation; being flagged means it is unusually high relative to this dataset, not that it is necessarily incorrect. Both values are retained for source verification.

**Other fields.** There are 12 distinct dates and two cities. Four original item labels have 24 records each, while the lowercase `oil b` label has one. After case-folding, `Oil B` and `oil b` form one normalized label with 25 records. All 97 rows use `PKR per kg (illustrative)` and carry the same disclaimer: `ILLUSTRATIVE TEACHING DATA - NOT ACTUAL PBS OBSERVATIONS`.

### Decision record

| Operation considered | Decision | Reason |
|---|---|---|
| Load the CSV and retain a raw copy | KEEP | The source must remain available for comparison |
| Parse dates and prices in additional columns | KEEP | Conversion can be checked without overwriting source values |
| Remove commas from every price string without checking the format | MODIFY | Only a correctly grouped thousands separator should be removed |
| Drop rows with missing values | REJECT | The one blank price does not justify deleting its observation |
| Delete exact duplicates automatically | REJECT | The repeated rows should be reviewed before removal |
| Treat every repeated normalized key as an exact duplicate | REJECT | The `Oil B` / `oil b` pair has different prices |
| Recode item labels automatically | REJECT | The source meaning needs confirmation |
| Delete IQR-flagged values | REJECT | The IQR rule identifies unusual values, not proven data-entry errors |
| Replace the missing price with an average | REJECT | No evidence supports an estimated value |
| Change units or the illustrative-data disclaimer | REJECT | Both are consistent across the file and carry important context |
| Overwrite the original CSV | REJECT | The audit should not destroy the source evidence |

### Reflection

The audit made clear that a data-quality flag and a confirmed error are not the same thing. The missing price is genuinely unavailable in the file, the repeated `Staple A` row is an exact match, and the `Oil B` label difference creates a key collision. However, the notebook cannot determine from these patterns alone whether a record should be removed or renamed.

The price `1,250` was a useful technical test. A direct numeric conversion would not interpret the comma-formatted text as a number. Converting it separately avoids treating a valid value as a parsing failure. At the same time, the IQR screen flags both 1,250 and 9,999. This shows why statistical screening needs interpretation: a value can be correctly parsed and still be unusual.

The next responsible step would be to compare the flagged records with the dataset's source or the instructions that produced it. Until that evidence is available, retaining the original values is more defensible than guessing. The audit therefore ends with a documented set of issues rather than a supposedly cleaned dataset.
