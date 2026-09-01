# Model and Validation

The model uses a compact star schema so each analytical subject keeps its natural grain while sharing one date dimension. This avoids forcing weekly national, city, category, and monthly channel records into a single wide table. Validation covers the prepared data, calculations, relationships, report interactions, and final rendered pages.

## Model Structure

| Table | Role | Grain |
|---|---|---|
| `Date` | Shared calendar dimension | One row per calendar date |
| `National_Weekly` | National POS fact | One row per calendar week |
| `Main_Cities_Weekly` | Comparable-city POS fact | One row per city per observed week |
| `Categories_Weekly` | Current-taxonomy merchant fact | One row per top-level category per observed week |
| `Digital_Monthly` | POS and mada channel fact | One row per month |
| `_Measures` | Explicit business calculations | Measure holder; no analytical grain |

The four active relationships join the fact dates to `Date[Date]`:

- `National_Weekly[Week End]`
- `Main_Cities_Weekly[Week End]`
- `Categories_Weekly[Week End]`
- `Digital_Monthly[Month]`

The date table supports filtering at weekly and monthly grains without creating fact-to-fact relationships.

## Preparation Logic

Power Query reads the repository's prepared CSV files through a `DataRoot` parameter. The preparation work before modeling included:

- standardizing dates, names, and numeric types;
- converting reported thousand-unit values to actual SAR and transaction counts;
- isolating ten cities with consistent historical coverage;
- separating the current merchant taxonomy from legacy classifications;
- excluding total and nested category rows from top-level analysis; and
- retaining the blank national table row for 18 Jul 2026 rather than interpolating it.

## Weighted Average Ticket

Average ticket is calculated from aggregate value and volume:

```dax
Average Transaction Value =
DIVIDE([Total POS Spending], [Total POS Transactions])
```

This is preferable to averaging row-level ticket values because weeks or groups with different transaction volumes should not receive equal weight.

## Latest and Previous Observations

Latest-period measures search the selected date context for the most recent date containing fact data. The previous-week comparison then searches for the most recent earlier observation rather than subtracting seven days. That distinction prevents the blank prepared-table row from creating a false comparison.

## Rankings and Shares

- City shares divide a selected city's latest spending by the combined latest spending of the ten tracked cities.
- The city ranking deliberately ignores the selected-city slicer so readers retain the full comparison.
- Category Top 8 logic ranks only eligible top-level current-taxonomy categories.
- mada share divides mada sales by the sum of mada and physical POS sales for the latest common month.
- Year-over-year measures compare the latest month with the same month one year earlier.

## Interaction Choices

Each slicer controls only the measures it can interpret correctly. The city slicer changes selected-city cards and trends but not the ten-city ranking. The category slicer changes category cards and the category trend but not the monthly channel comparison.

## Validation Approach

### Source and Prepared-Data Checks

- Confirmed SAMA as the source authority and used KAPSARC only as a structured distribution of SAMA-sourced history.
- Reconciled selected weekly records against original SAMA reports, including the latest modeled week and the merchant-classification boundary.
- Checked unit conversions, dates, row grains, duplicate keys, blank values, negative values, and coverage boundaries.
- Confirmed that the prepared tables do not contain values for the week ending 18 Jul 2026, although SAMA has published the official report.
- Limited the city history to ten consistently covered cities and kept the current merchant taxonomy separate from the legacy structure.
- Excluded totals and nested categories from top-level rankings to prevent double counting.

Exact table coverage and field-level rules are recorded in the [data dictionary](DATA_DICTIONARY.md).

### KPI Reconciliation

The headline measures were recalculated directly from the prepared tables and compared with the dashboard results.

| Check | Recalculated result |
|---|---:|
| Latest weekly POS spending | SAR 14.19B |
| Latest weekly POS transactions | 249.68M |
| Latest weighted average ticket | SAR 56.84 |
| Spending change from previous available week | -2.8% |
| Transaction change from previous available week | -1.5% |
| Riyadh spending in the tracked-ten cohort | SAR 4.84B |
| Riyadh share of tracked-ten spending | 48.6% |
| Latest Food & Beverages spending | SAR 2.20B |
| Jun 2026 mada share of combined POS and mada sales | 36.9% |
| Jun 2026 mada sales year-over-year growth | 29.5% |

Across complete years 2021 to 2025, POS spending rose 49.3%, transactions rose 124.3%, and the weighted average ticket fell 33.4%.

### Model and Report Checks

- Confirmed one-to-many, single-direction relationships from `Date[Date]` to each fact date.
- Verified that average ticket uses total value divided by total transactions rather than an average of row-level ratios.
- Checked latest-period, previous-available-week, ranking, tracked-city share, category Top 8, channel share, and year-over-year measures against prepared-table calculations.
- Reviewed filters and slicer interactions so a city or category selection does not remove the intended comparison frame.
- Refreshed the PBIP from the repository's `data` folder and reviewed all three pages for rendering, labels, units, sorting, and filter behavior.

The [DAX measure reference](DAX_MEASURES.md) contains the complete formulas, and the repository screenshots show the reviewed report pages.
