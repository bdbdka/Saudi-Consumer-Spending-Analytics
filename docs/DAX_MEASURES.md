# DAX Measures

Dashboard calculations are defined as explicit measures on the dedicated `_Measures` table. Raw SAR/count measures stay unscaled; separate display measures divide by one billion or one million and use simple formats. Latest-period measures search for observed fact rows, and the national prior-week measures search for the previous available observation.

## Executive

### Total POS Spending

National POS spending in SAR for the active filter context.

Format: `0`

```dax
SUM(National_Weekly[Transaction Value SAR])
```

### Total POS Transactions

National POS transaction count for the active filter context.

Format: `0`

```dax
SUM(National_Weekly[Transaction Count])
```

### Average Transaction Value

Weighted national average ticket in SAR.

Format: `0.00`

```dax
DIVIDE([Total POS Spending], [Total POS Transactions])
```

### Latest Week

Latest selected date with a nonblank national spending observation.

Format: `yyyy-MM-dd`

```dax
VAR _dates = FILTER(ALLSELECTED('Date'[Date]), CALCULATE(COUNTROWS(FILTER(National_Weekly, NOT ISBLANK(National_Weekly[Transaction Value SAR])))) > 0) RETURN MAXX(_dates, 'Date'[Date])
```

### Previous Available Week

Most recent prior date with nonblank national spending; skips the blank prepared-table row.

Format: `yyyy-MM-dd`

```dax
VAR _latest = [Latest Week] RETURN MAXX(FILTER(ALL('Date'[Date]), 'Date'[Date] < _latest && CALCULATE(COUNTROWS(FILTER(National_Weekly, NOT ISBLANK(National_Weekly[Transaction Value SAR])))) > 0), 'Date'[Date])
```

### Latest Weekly Spending

National POS spending at the latest available week.

Format: `0`

```dax
VAR _d = [Latest Week] RETURN CALCULATE([Total POS Spending], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Latest Weekly Transactions

National POS transactions at the latest available week.

Format: `0`

```dax
VAR _d = [Latest Week] RETURN CALCULATE([Total POS Transactions], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Latest Average Ticket

Latest weekly weighted average ticket in SAR.

Format: `0.00`

```dax
DIVIDE([Latest Weekly Spending], [Latest Weekly Transactions])
```

### Previous Available Week Spending

National spending at the previous available week.

Format: `0`

```dax
VAR _d = [Previous Available Week] RETURN CALCULATE([Total POS Spending], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Previous Available Week Transactions

National transactions at the previous available week.

Format: `0`

```dax
VAR _d = [Previous Available Week] RETURN CALCULATE([Total POS Transactions], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Weekly Spending Change %

Week-over-week spending change using the previous available observation.

Format: `0.0%`

```dax
DIVIDE([Latest Weekly Spending] - [Previous Available Week Spending], [Previous Available Week Spending])
```

### Weekly Transaction Change %

Week-over-week transaction change using the previous available observation.

Format: `0.0%`

```dax
DIVIDE([Latest Weekly Transactions] - [Previous Available Week Transactions], [Previous Available Week Transactions])
```

### Latest Weekly Spending SAR B

Latest weekly spending in SAR billions.

Format: `0.00`

```dax
DIVIDE([Latest Weekly Spending], 1000000000)
```

### Latest Weekly Transactions M

Latest weekly transactions in millions.

Format: `0.00`

```dax
DIVIDE([Latest Weekly Transactions], 1000000)
```

### Weekly POS Spending SAR B

Weekly national POS spending in SAR billions.

Format: `0.00`

```dax
DIVIDE([Total POS Spending], 1000000000)
```

### Weekly POS Transactions M

Weekly national POS transactions in millions.

Format: `0.00`

```dax
DIVIDE([Total POS Transactions], 1000000)
```

### Latest Week Label

Human-readable latest-week label.

Format: `text / inherited`

```dax
FORMAT([Latest Week], "dd MMM yyyy")
```

## Cities

### City Spending

Tracked-city POS spending in SAR.

Format: `0`

```dax
SUM(Main_Cities_Weekly[Transaction Value SAR])
```

### City Transactions

Tracked-city POS transactions.

Format: `0`

```dax
SUM(Main_Cities_Weekly[Transaction Count])
```

### City Average Ticket

Weighted tracked-city average ticket in SAR.

Format: `0.00`

```dax
DIVIDE([City Spending], [City Transactions])
```

### Latest City Week

Latest city observation date independent of city selection.

Format: `yyyy-MM-dd`

```dax
MAXX(FILTER(ALL('Date'[Date]), CALCULATE(COUNTROWS(Main_Cities_Weekly), REMOVEFILTERS(Main_Cities_Weekly[City])) > 0), 'Date'[Date])
```

### Latest City Spending

City spending at the latest city week.

Format: `0`

```dax
VAR _d = [Latest City Week] RETURN CALCULATE([City Spending], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Latest City Transactions

City transactions at the latest city week.

Format: `0`

```dax
VAR _d = [Latest City Week] RETURN CALCULATE([City Transactions], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Latest City Average Ticket

Latest weighted city average ticket in SAR.

Format: `0.00`

```dax
DIVIDE([Latest City Spending], [Latest City Transactions])
```

### Selected City Name

Selected city, with Riyadh as the default fallback.

Format: `text / inherited`

```dax
SELECTEDVALUE(Main_Cities_Weekly[City], "Riyadh")
```

### Selected City Latest Spending

Latest spending for the selected city.

Format: `0`

```dax
VAR _city = [Selected City Name] RETURN CALCULATE([Latest City Spending], REMOVEFILTERS(Main_Cities_Weekly[City]), TREATAS({_city}, Main_Cities_Weekly[City]))
```

### Selected City Latest Transactions

Latest transactions for the selected city.

Format: `0`

```dax
VAR _city = [Selected City Name] RETURN CALCULATE([Latest City Transactions], REMOVEFILTERS(Main_Cities_Weekly[City]), TREATAS({_city}, Main_Cities_Weekly[City]))
```

### Selected City Latest Average Ticket

Latest average ticket for the selected city.

Format: `0.00`

```dax
DIVIDE([Selected City Latest Spending], [Selected City Latest Transactions])
```

### Selected City Spending

Weekly spending trend for the selected city.

Format: `0`

```dax
VAR _city = [Selected City Name] RETURN CALCULATE([City Spending], REMOVEFILTERS(Main_Cities_Weekly[City]), TREATAS({_city}, Main_Cities_Weekly[City]))
```

### Selected City Transactions

Weekly transaction trend for the selected city.

Format: `0`

```dax
VAR _city = [Selected City Name] RETURN CALCULATE([City Transactions], REMOVEFILTERS(Main_Cities_Weekly[City]), TREATAS({_city}, Main_Cities_Weekly[City]))
```

### Selected City Average Ticket

Weighted average ticket for the selected city in the active date context.

Format: `0.00`

```dax
DIVIDE([Selected City Spending], [Selected City Transactions])
```

### Leading City

Top city by latest-week spending.

Format: `text / inherited`

```dax
VAR _t = TOPN(1, ADDCOLUMNS(ALL(Main_Cities_Weekly[City]), "__v", [Latest City Spending]), [__v], DESC, Main_Cities_Weekly[City], ASC) RETURN MAXX(_t, Main_Cities_Weekly[City])
```

### Selected City Share of Tracked Top 10 Cities

Selected-city share of spending across the ten tracked cities at the latest week.

Format: `0.0%`

```dax
DIVIDE([Selected City Latest Spending], CALCULATE([Latest City Spending], REMOVEFILTERS(Main_Cities_Weekly[City])))
```

### Latest City Spending SAR B

Latest city spending in SAR billions for ranking.

Format: `0.00`

```dax
DIVIDE([Latest City Spending], 1000000000)
```

### Selected City Latest Spending SAR B

Selected-city latest spending in SAR billions.

Format: `0.00`

```dax
DIVIDE([Selected City Latest Spending], 1000000000)
```

### Selected City Latest Transactions M

Selected-city latest transactions in millions.

Format: `0.00`

```dax
DIVIDE([Selected City Latest Transactions], 1000000)
```

### Selected City Spending SAR B

Selected-city weekly spending trend in SAR billions.

Format: `0.00`

```dax
DIVIDE([Selected City Spending], 1000000000)
```

### Selected City Transactions M

Selected-city weekly transactions in millions.

Format: `0.00`

```dax
DIVIDE([Selected City Transactions], 1000000)
```

## Categories

### Category Spending

Current-taxonomy category spending in SAR.

Format: `0`

```dax
SUM(Categories_Weekly[Transaction Value SAR])
```

### Category Transactions

Current-taxonomy category transactions.

Format: `0`

```dax
SUM(Categories_Weekly[Transaction Count])
```

### Category Average Ticket

Weighted category average ticket in SAR.

Format: `0.00`

```dax
DIVIDE([Category Spending], [Category Transactions])
```

### Latest Category Week

Latest current-taxonomy category observation date.

Format: `yyyy-MM-dd`

```dax
MAXX(FILTER(ALL('Date'[Date]), CALCULATE(COUNTROWS(Categories_Weekly), REMOVEFILTERS(Categories_Weekly[Category])) > 0), 'Date'[Date])
```

### Latest Category Spending

Category spending at the latest category week.

Format: `0`

```dax
VAR _d = [Latest Category Week] RETURN CALCULATE([Category Spending], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Latest Category Transactions

Category transactions at the latest category week.

Format: `0`

```dax
VAR _d = [Latest Category Week] RETURN CALCULATE([Category Transactions], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Selected Category Name

Selected category, with Food & Beverages as the default fallback.

Format: `text / inherited`

```dax
SELECTEDVALUE(Categories_Weekly[Category], "Food & Beverages")
```

### Selected Category Latest Spending

Latest spending for the selected category.

Format: `0`

```dax
VAR _category = [Selected Category Name] RETURN CALCULATE([Latest Category Spending], REMOVEFILTERS(Categories_Weekly[Category]), TREATAS({_category}, Categories_Weekly[Category]))
```

### Selected Category Latest Transactions

Latest transactions for the selected category.

Format: `0`

```dax
VAR _category = [Selected Category Name] RETURN CALCULATE([Latest Category Transactions], REMOVEFILTERS(Categories_Weekly[Category]), TREATAS({_category}, Categories_Weekly[Category]))
```

### Selected Category Latest Average Ticket

Latest weighted average ticket for the selected category.

Format: `0.00`

```dax
DIVIDE([Selected Category Latest Spending], [Selected Category Latest Transactions])
```

### Selected Category Spending

Weekly spending trend for the selected category.

Format: `0`

```dax
VAR _category = [Selected Category Name] RETURN CALCULATE([Category Spending], REMOVEFILTERS(Categories_Weekly[Category]), TREATAS({_category}, Categories_Weekly[Category]))
```

### Selected Category Transactions

Transactions for the selected category in the active date context.

Format: `0`

```dax
VAR _category = [Selected Category Name] RETURN CALCULATE([Category Transactions], REMOVEFILTERS(Categories_Weekly[Category]), TREATAS({_category}, Categories_Weekly[Category]))
```

### Selected Category Average Ticket

Weighted average ticket for the selected category in the active date context.

Format: `0.00`

```dax
DIVIDE([Selected Category Spending], [Selected Category Transactions])
```

### Leading Category

Top current-taxonomy category by latest-week spending.

Format: `text / inherited`

```dax
VAR _t = TOPN(1, ADDCOLUMNS(ALL(Categories_Weekly[Category]), "__v", [Latest Category Spending]), [__v], DESC, Categories_Weekly[Category], ASC) RETURN MAXX(_t, Categories_Weekly[Category])
```

### Category Rank

Dynamic latest-week category spending rank.

Format: `0`

```dax
RANKX(ALL(Categories_Weekly[Category]), [Latest Category Spending], , DESC, Dense)
```

### Top 8 Categories Latest Spending SAR B

Latest category spending in SAR billions, blank outside the dynamic top eight.

Format: `0.00`

```dax
IF([Category Rank] <= 8, DIVIDE([Latest Category Spending], 1000000000))
```

### Selected Category Latest Spending SAR B

Selected-category latest spending in SAR billions.

Format: `0.00`

```dax
DIVIDE([Selected Category Latest Spending], 1000000000)
```

### Selected Category Latest Transactions M

Selected-category latest transactions in millions.

Format: `0.00`

```dax
DIVIDE([Selected Category Latest Transactions], 1000000)
```

### Selected Category Spending SAR B

Selected-category weekly spending trend in SAR billions.

Format: `0.00`

```dax
DIVIDE([Selected Category Spending], 1000000000)
```

## Digital

### POS Monthly Sales

Monthly POS sales in SAR.

Format: `0`

```dax
SUM(Digital_Monthly[POS Sales SAR])
```

### mada E-commerce Sales

Monthly mada e-commerce sales in SAR.

Format: `0`

```dax
SUM(Digital_Monthly[mada E-commerce Sales SAR])
```

### Total Card Sales

Combined POS and mada e-commerce monthly sales.

Format: `0`

```dax
[POS Monthly Sales] + [mada E-commerce Sales]
```

### mada Sales Share

mada e-commerce share of combined POS and mada sales.

Format: `0.0%`

```dax
DIVIDE([mada E-commerce Sales], [Total Card Sales])
```

### POS Monthly Transactions

Monthly POS transaction count.

Format: `0`

```dax
SUM(Digital_Monthly[POS Transactions])
```

### mada E-commerce Transactions

Monthly mada e-commerce transaction count.

Format: `0`

```dax
SUM(Digital_Monthly[mada E-commerce Transactions])
```

### Latest Digital Month

Latest monthly digital observation.

Format: `yyyy-MM`

```dax
MAXX(FILTER(ALL('Date'[Date]), CALCULATE(COUNTROWS(Digital_Monthly)) > 0), 'Date'[Date])
```

### Latest mada Sales Share

mada sales share at the latest monthly observation.

Format: `0.0%`

```dax
VAR _d = [Latest Digital Month] RETURN CALCULATE([mada Sales Share], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### Latest mada Sales

mada e-commerce sales at the latest monthly observation.

Format: `0`

```dax
VAR _d = [Latest Digital Month] RETURN CALCULATE([mada E-commerce Sales], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date]))
```

### mada Sales YoY

Latest mada e-commerce sales year-over-year growth.

Format: `0.0%`

```dax
VAR _d = [Latest Digital Month] VAR _current = CALCULATE([mada E-commerce Sales], REMOVEFILTERS('Date'), TREATAS({_d}, 'Date'[Date])) VAR _prior = CALCULATE([mada E-commerce Sales], REMOVEFILTERS('Date'), TREATAS({EDATE(_d, -12)}, 'Date'[Date])) RETURN DIVIDE(_current - _prior, _prior)
```

### POS Monthly Sales SAR B

Monthly POS sales in SAR billions.

Format: `0.00`

```dax
DIVIDE([POS Monthly Sales], 1000000000)
```

### mada E-commerce Sales SAR B

Monthly mada e-commerce sales in SAR billions.

Format: `0.00`

```dax
DIVIDE([mada E-commerce Sales], 1000000000)
```

### POS Monthly Transactions M

Monthly POS transactions in millions.

Format: `0.00`

```dax
DIVIDE([POS Monthly Transactions], 1000000)
```

### mada E-commerce Transactions M

Monthly mada e-commerce transactions in millions.

Format: `0.00`

```dax
DIVIDE([mada E-commerce Transactions], 1000000)
```

## Modeling Notes

- `Weekly Spending Change %` and `Weekly Transaction Change %` compare against the previous available row, so the blank July 2026 model row does not create a false comparison.
- City and category default-selection helpers return Riyadh and Food & Beverages when no single selection is active.
- `Category Rank` ranks current top-level categories by latest-week spending. `Top 8 Categories Latest Spending SAR B` returns blank after rank eight so the native bar chart displays a dynamic Top 8.
- Sales-share and YoY measures use the monthly digital fact table; sales are compared in the common Jan 2019 to Jun 2026 source window.
