# Data Dictionary

The model contains one shared date table, four fact tables, and one dedicated measure table. Raw numeric columns are hidden in Report view; business-facing calculations are exposed through explicit DAX measures.

## Date

- **Business purpose:** Shared calendar for weekly and monthly analysis.
- **Granularity:** One row per calendar date.
- **Coverage:** 1 Jan 2019 to 15 Aug 2026 (2,784 rows).
- **Important fields:** `Date`, `Year`, `Quarter`, `Month Number`, `Month Name`, `Year-Month`.
- **Rules:** `Month Name` sorts by `Month Number`; automatic date/time is disabled. All four fact tables filter through `Date` using their observation-end date.

## National_Weekly

- **Business purpose:** National Saudi POS spending, transactions, and average-ticket analysis.
- **Granularity:** One row per SAMA week ending date.
- **Coverage:** 10 May 2020 to 15 Aug 2026 (327 calendar-week rows, including one blank model row).
- **Important fields:** `Week Start`, `Week End`, `Transaction Count`, `Transaction Value SAR`, `Average Ticket SAR`, `Data Status`.
- **Units:** Actual transaction count; actual SAR; SAR per transaction.
- **Rules:** The week ending 18 Jul 2026 remains blank in the prepared table. An official SAMA report exists for that week, but its values are not incorporated in this version. Dashboard average ticket is recalculated as total value divided by total count rather than averaged from row-level ratios.

## Main_Cities_Weekly

- **Business purpose:** Consistent long-run comparison of ten named Saudi cities.
- **Granularity:** One row per week ending date and city.
- **Coverage:** 10 May 2020 to 15 Aug 2026 (3,260 rows across 326 observed weeks; 18 Jul 2026 is omitted).
- **Important fields:** `Week Start`, `Week End`, `City`, `Transaction Count`, `Transaction Value SAR`, `Average Ticket SAR`.
- **Units:** Actual transaction count; actual SAR; SAR per transaction.
- **Rules:** Limited to Abha, Buraidah, Dammam, Hail, Jeddah, Khobar, Madinah, Makkah, Riyadh, and Tabuk. Historical Other/Others is excluded because its residual definition changed as SAMA expanded named-city coverage.

## Categories_Weekly

- **Business purpose:** Current merchant-category mix and selected-category trend analysis.
- **Granularity:** One row per week ending date and top-level category.
- **Coverage:** 29 Jun 2025 to 15 Aug 2026 (1,102 rows; 19 categories across 58 observed weeks, with 18 Jul 2026 omitted).
- **Important fields:** `Week Start`, `Week End`, `Category`, `Transaction Count`, `Transaction Value SAR`, `Average Ticket SAR`.
- **Units:** Actual transaction count; actual SAR; SAR per transaction.
- **Rules:** Uses only the current SAMA classification. `Total` and nested/detail categories are excluded to prevent double counting. Legacy categories are not appended across the Jul 2025 classification break.

## Digital_Monthly

- **Business purpose:** Comparison of physical POS and mada e-commerce activity.
- **Granularity:** One row per calendar month.
- **Coverage:** Jan 2019 to Jun 2026 (90 rows).
- **Important fields:** `Month`, `POS Sales SAR`, `POS Transactions`, `mada E-commerce Sales SAR`, `mada E-commerce Transactions`.
- **Units:** Actual SAR and actual transaction counts.
- **Rules:** Restricted to the common period available for both channels. Sales marked as thousand SAR in the source were multiplied by 1,000 during preparation.

## Measures (`_Measures`)

- **Business purpose:** Central business-logic layer for KPIs, rankings, shares, and display scaling.
- **Granularity:** No fact grain; the table contains a hidden holder column and explicit DAX measures.
- **Folders:** `01 Executive`, `02 Cities`, `03 Categories`, `04 Digital`.
- **Important logic:** Latest observed dates, previous available week, weighted average ticket, tracked-city share, category Top 8, mada sales share, and mada year-over-year growth.
- **Rules:** Implicit measures are discouraged. SAR-billion and transaction-million measures are display layers over unscaled base measures. See the [DAX measure reference](DAX_MEASURES.md) for the complete catalog.
