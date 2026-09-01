# Case Study: Saudi Consumer Spending Analytics

## Business Problem

Saudi Central Bank (SAMA) payment statistics provide a useful view of electronic retail activity, but the relevant information spans national weekly performance, city records, merchant categories, and monthly POS and mada e-commerce series. Reading these sources separately makes it difficult to answer a connected set of questions: Is POS activity growing? Is growth coming from more transactions or larger purchases? Which consistently tracked cities account for the most activity? What is the current merchant mix? How is mada e-commerce changing relative to physical POS?

The project brings those questions together in a three-page Power BI dashboard. Its scope is limited to Saudi POS and mada payment activity rather than presenting the data as a complete measure of cash, transfers, or every form of consumer spending in Saudi Arabia.

## Data Challenge

The analysis combines official SAMA publications with structured KAPSARC distributions of SAMA data. The main challenge was deciding which records were genuinely comparable and then checking the structured history against official reports.

I initially considered extending the category trend further back, but SAMA's classification change around Jul 2025 made that comparison unreliable. The current data also contains parent and nested detail rows, so summing every row would double count activity. City coverage presented a similar problem: ten named cities have consistent histories, while the composition of `Other`/`Others` changed as named-city reporting expanded.

The prepared model has a separate limitation. Its national table contains a blank row for the week ending 18 Jul 2026, and the city and category tables omit that week. SAMA's official report is available, but its values are not incorporated in this version.

## Approach

Source values were reconciled before being converted into UTF-8 CSV inputs. Dates, names, monetary units, and transaction counts were standardized, and each prepared table was checked at its intended grain.

The semantic model uses a shared contiguous Date table and four fact tables: `National_Weekly`, `Main_Cities_Weekly`, `Categories_Weekly`, and `Digital_Monthly`. Four one-to-many, single-direction relationships carry date filters into the fact tables. A dedicated `_Measures` table holds explicit DAX measures organized into Executive, Cities, Categories, and Digital folders.

The report has three 1280 × 720 pages. Executive Overview presents latest-week KPIs and national trends. City Analysis compares a stable ten-city cohort and provides selected-city detail. Categories & Digital Shift combines the current merchant mix with monthly physical POS and mada e-commerce trends. Native visuals keep the report easy to open and maintain, while focused slicer interactions preserve the intended comparisons.

## Key Analytical Decisions

- **City cohort:** Long-run comparisons use Abha, Buraidah, Dammam, Hail, Jeddah, Khobar, Madinah, Makkah, Riyadh, and Tabuk. Historical residual city records are excluded because their composition changed.
- **Taxonomy boundary:** Legacy and current merchant classifications remain separate. Dashboard category analysis starts on 29 Jun 2025 and uses the current classification only.
- **Category hierarchy:** `Total` and nested detail rows are excluded, leaving 19 top-level categories and avoiding parent-child double counting.
- **Weighted average:** Average Transaction Value equals total transaction value divided by total transaction count, so groups with different transaction volumes are weighted correctly.
- **Blank prepared week:** Previous-week measures find the previous available observation instead of subtracting seven calendar days. This prevents the blank Jul 2026 model row from creating a false comparison.
- **Visual comparison:** The city ranking uses bars rather than a map because the dataset supports a controlled ten-city comparison, not complete geographic coverage. Bars also make close values easier to compare.
- **Interaction scope:** City and category slicers change their related cards and trends without removing the full comparison ranking or implying that the monthly channel series is category-specific.

## Findings

For the week ending 15 Aug 2026, national POS spending was SAR 14.19B across 249.68M transactions, producing a weighted average ticket of SAR 56.84. Spending decreased 2.8% and transactions decreased 1.5% from the previous available week, 8 Aug 2026.

The longer history shows transaction volume growing much faster than average ticket. Average valid weekly transactions increased from 62.93M in the partial May to Dec 2020 period to 240.06M in the partial Jan to Aug 2026 period. Average weekly spending rose from SAR 7.49B to SAR 14.37B, while the weighted average ticket declined from SAR 118.96 to SAR 59.85. The pattern is consistent with spending growth becoming more volume-driven, but it does not establish the cause.

Riyadh led the tracked cities with SAR 4.84B and represented 48.6% of spending within the ten-city comparison. This is a cohort concentration measure, not Riyadh's share of national POS spending. Food & Beverages led the latest current-category ranking at SAR 2.20B, followed closely by Others at SAR 2.16B and Restaurants & Cafés at SAR 1.82B.

In Jun 2026, physical POS sales were SAR 57.48B and mada e-commerce sales were SAR 33.63B. mada accounted for 36.9% of their combined sales and grew 29.5% year over year. mada transactions grew 30.5%, compared with 9.8% for POS transactions. mada e-commerce therefore grew faster over this period, while physical POS remained the larger channel in both sales and transaction count.

## Business Value

The dashboard supports market monitoring, comparable-city review, current-category tracking, and payment-channel analysis. It separates value growth from transaction growth and helps identify movements that merit further investigation.

## Future Extension

A future version could first incorporate the available 18 Jul 2026 report, then evaluate forecasts for national spending, transaction counts, tracked-city spending, or monthly mada activity. The current-category series has only 58 observed weeks, so it is a weaker forecasting target. Suitable tests would begin with naive and seasonal-naive baselines, use chronological walk-forward validation, and add calendar or economic features only when reliable. Any forecasts should include prediction intervals and remain separate from the descriptive dashboard until validated.

## Outcome

The repository brings together a Power BI Project report, a six-table semantic model, explicit DAX measures, three business-facing pages, and prepared source-derived data. The result is a reproducible descriptive analysis. Forecasting remains future work.
