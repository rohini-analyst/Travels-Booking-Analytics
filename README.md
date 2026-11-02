# Travel Booking Analysis (Excel + Power BI)

An end-to-end data analysis project on 1,000 travel bookings (Jan 2024 - Mar 2025): data cleaning and validation, KPI analysis, an Excel dashboard, and a Power BI report built on a proper data model with DAX.

**Tools:** Advanced Excel (Tables, SUMIFS/COUNTIFS/AVERAGEIFS, SUMPRODUCT, conditional formatting, charts) | Power BI (Power Query, star-schema model, DAX, time intelligence)

---

## Business questions

1. How much revenue is the business really earning, and how much is lost to cancellations?
2. Which destinations, travel companies, channels and customer segments perform best and worst?
3. What drives cancellations (lead time, payment mode, channel, age, city)?
4. Where is customer experience (rating) weakest?
5. How do bookings and revenue trend month by month?

## Dataset

`travel_booking_dataset.csv` - 1,000 rows x 22 columns.

| Group | Columns |
|---|---|
| Booking | Booking_ID, Booking_Date, Departure_Date, Return_Date, Booking_Status, Channel, Agent (travel company), Payment_Mode |
| Customer | Customer_ID, Customer_Name, Age, Gender, City |
| Trip | Travel_Type, Origin, Destination, Package_Type, Passengers |
| Money / quality | Base_Price, Discount, Total_Amount, Rating |

**Pricing rule (verified on all 1,000 rows):** `Total_Amount = (Base_Price - Discount) x Passengers` - price and discount are *per person*.

## Data cleaning and validation

| Finding | Decision | Reason |
|---|---|---|
| 250 blank `Package_Type` | Labelled "Not Specified" | Keeps all rows visible; guessing would invent data |
| 315 blank `Return_Date` | Left blank | All are one-way trips - logically no return |
| 140 blank `Rating` | Left blank | All are cancelled bookings - cannot be rated; filling 0 would distort averages |
| 140 cancelled bookings still carry an amount | Revenue counts **Completed only** | Summing everything overstates revenue by 16.6% |
| `Origin` identical to `City` in all rows | Removed in Power BI | Redundant column |
| 99 rows share a `Customer_ID` across different people | Flagged, kept | ID collisions - not usable for repeat-customer analysis |
| Duplicate Booking_IDs, invalid dates, ratings outside 1-5, ages outside 18-80 | None found | Verified by formula on the `Data_Quality` sheet |

Derived columns: lead time, trip duration, age group, lead-time bucket, gross amount, total discount, discount %, cancelled flag, realized revenue, lost revenue.

## Key results

| KPI | Value |
|---|---|
| Total bookings | 1,000 |
| Realized revenue (completed only) | Rs 20,372,753 |
| Revenue lost to cancellations | Rs 3,383,134 |
| Cancellation rate | 14.0% |
| Average booking value (completed) | Rs 23,689 |
| Average rating | 3.02 |

**Findings**
- Cancellations are the biggest revenue leak: 14.0% of bookings, Rs 33.8 lakh lost.
- Bookings made 31-60 days ahead cancel most (15.9% vs 10.5% for 15-30 days).
- Online (16.6%) and cash (16.8%) bookings cancel more than agent-channel (11.3%) and card (12.9%) bookings.
- Shimla leads on revenue (Rs 26.2 lakh); Goa (2.68) and Udaipur (2.79) have the lowest ratings.
- The four travel companies trade off: Wagh Tours has the highest revenue and booking value but the worst cancellation rate (17.8%); BlueSky has the lowest cancellation (10.9%) but the lowest rating (2.85).
- 25% of bookings have no package type recorded - a data-capture problem worth fixing at source.

> Differences between groups are modest and no significance testing was done, so findings are leads to investigate, not proven effects.

## Repository contents

```
.
|-- data/
|   |-- travel_booking_dataset.csv        # original data (untouched)
|   `-- travel_booking_cleaned.csv        # cleaned version
|-- excel/
|   `-- Travel_Booking_Analysis.xlsx      # cleaning, analysis, dashboard, step log
|-- powerbi/
|   |-- PowerQuery_Bookings.m             # Power Query cleaning script
|   |-- DAX_Measures.dax                  # calendar table + all measures
|   |-- TravelBooking_Theme.json          # report theme
|   `-- PowerBI_Step_by_Step_Guide.docx   # build steps with reasons
`-- README.md
```

### Excel workbook sheets

| Sheet | Purpose |
|---|---|
| Steps_Log | Every step with what, why, and how to do it in Excel |
| Dashboard | 8 KPI cards + 7 charts |
| Insights | Findings and recommendations |
| Analysis | 11 formula-driven summary tables |
| Data_Quality | 15 live validation checks (PASS / REVIEW / INFO) |
| Clean_Data | Cleaned Excel Table (`tblBookings`) with derived columns |
| Raw_Data | Original data, untouched for audit |

## How to reproduce the Power BI report

1. Open Power BI Desktop. Create a text parameter `FilePath` pointing to the CSV.
2. Get Data > Blank Query > Advanced Editor > paste `PowerQuery_Bookings.m`; name the query `Bookings`; Close & Apply.
3. Create the `Dim_Date` table and all measures from `DAX_Measures.dax`; mark `Dim_Date` as the date table.
4. Relate `Dim_Date[Date]` to `Bookings[Booking_Date]` (active) and `Bookings[Departure_Date]` (inactive).
5. Import `TravelBooking_Theme.json` and build the three pages described in the guide: Executive Summary, Cancellation and Risk, Customers and Experience.
6. Check your totals against the KPI table above.

Full reasoning for every step is in `powerbi/PowerBI_Step_by_Step_Guide.docx`.

## Recommendations

- Reduce cancellations first: reminders and part-payment or non-refundable deposits for bookings made more than 30 days ahead; steer cash and online customers toward card/UPI.
- Learn from the lowest-cancellation company and channel (BlueSky, Agent channel) and the highest-value company (Wagh Tours).
- Investigate service quality in Goa and Udaipur before promoting them.
- Make `Package_Type` mandatory and fix `Customer_ID` generation.

## Skills demonstrated

Data cleaning and validation, business-rule checking, Excel Tables and conditional aggregation, KPI design, dashboarding, Power Query (M), star-schema modelling, DAX (calculated tables, time intelligence, `USERELATIONSHIP`, `RANKX`, dynamic titles), and clear communication of assumptions and limitations.

## Author

<your name> - <LinkedIn / email>
