# Sources

Saudi Central Bank (SAMA) is the source authority for every payment series used in the project. KAPSARC is used only as a structured distribution of SAMA-sourced records; it is not presented as the regulator or original publisher.

## Official Saudi Central Bank Sources

| Source | Publisher | Frequency / coverage | How it was used |
|---|---|---|---|
| [Weekly Point of Sale Transactions archive](https://sama.gov.sa/en-US/Statistics/Indices/Pages/POS.aspx) | Saudi Central Bank (SAMA) | Weekly | Primary official archive for national, merchant-activity, and city POS values. |
| [Legacy POS report, 19 Jul 2025](https://www.sama.gov.sa/ar-sa/Statistics/Indices/POS/POS_Report_19_Jul_2025.pdf) | SAMA | Weekly report | Confirmed the final week of the legacy merchant classification. |
| [Expanded POS report, 26 Jul 2025](https://www.sama.gov.sa/ar-sa/Statistics/Indices/POS/POS_Report_26_Jul_2025.pdf) | SAMA | Weekly report | Confirmed the expanded merchant classification and the taxonomy boundary. |
| [Weekly POS report, 18 Jul 2026](https://sama.gov.sa/en-US/Statistics/Indices/POS_EN/Weekly_Points_of_Sale_Transactions_Report_18_Jul_2026.pdf) | SAMA | Weekly report | Confirms that official values exist for the week that remains blank or omitted in the prepared model tables. |
| [Weekly POS report, 15 Aug 2026](https://www.sama.gov.sa/ar-sa/Statistics/Indices/POS/Weekly_Points_of_Sale_Transactions_Report_15_Aug_2026.pdf) | SAMA | Latest included weekly report | Source check for the final week, covering 9 to 15 Aug 2026. |
| [Monthly Bulletin Statistics](https://sama.gov.sa/en-US/Statistics/Pages/MonthlyStatistics.aspx) | SAMA | Monthly; modeled through Jun 2026 | Official reference for monthly physical POS and mada e-commerce series. |
| [Publication timeline](https://sama.gov.sa/en-US/Statistics/Pages/AdvancedReleaseCalendar.aspx) | SAMA | Release schedule | Freshness and publication-timing control; not a modeled fact table. |

## Structured Historical Distribution

| Distribution | Source authority | Frequency / coverage | How it was used |
|---|---|---|---|
| [Point of Sale Transactions by Sector and City](https://datasource.kapsarc.org/explore/dataset/point-of-sale-transactions-by-sector-and-city/?flg=en-gb) | SAMA data distributed by KAPSARC | Weekly historical records | Structured historical weekly source for the national and consistent-city series. It was cross-validated against original SAMA weekly reports. |
| [Points of Sale and E-Commerce Transactions (mada cards)](https://datasource.kapsarc.org/explore/dataset/pos-transactions/?flg=en-gb) | SAMA data distributed by KAPSARC | Monthly, Jan 2019 to Jun 2026 in the model | Structured monthly distribution used for the physical POS versus mada e-commerce analysis. |
| [Monthly dataset export](https://datasource.kapsarc.org/explore/assets/pos-transactions/export/) | SAMA data distributed by KAPSARC | CSV export | CSV export used to prepare `Digital_Monthly.csv`. |

## Source-Handling Notes

- Historical KAPSARC records were treated as a structured mirror and checked against official SAMA publications before use.
- Weekly values reported in thousands were converted to actual SAR and transaction counts during preparation.
- Source files were treated as read-only, and the prepared tables were reconciled before modeling.
- The official report for the week ending 18 Jul 2026 is available, but its values are not incorporated in the current prepared tables.
- The public `data` directory contains the prepared CSV inputs used by Power BI.
