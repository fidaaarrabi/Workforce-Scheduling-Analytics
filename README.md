# Workforce Scheduling & Availability Analytics

Power BI workforce analytics project that combines assigned shifts with employee availability submissions to measure staffing coverage, workload distribution, and schedule alignment.

> Portfolio note: the included Excel workbook is fully synthetic. It reproduces the project schema and business logic without using real employees, emails, dates, or operational records.

## Business questions

- How many shifts and working hours were scheduled?
- How evenly was work distributed across employees and shift types?
- How often did assignments match submitted availability?
- Which employees or shift types had the most assignments outside availability?
- How did staffing levels change by month and day of week?

## Dashboard pages

### Management Overview

- Total hours, total shifts, employees with shifts
- Average hours per employee and average shift length
- Monthly hours trend
- Shift volume by type
- Employee workload comparison

### Scheduling & Availability

- Working days and availability-match KPIs
- Scheduled employees by date and shift type
- Average daily staffing by weekday
- Availability options versus assigned shifts
- Employee-level exceptions table

## Data model

- `FactShifts`: one row per assigned employee shift
- `FactAvailability`: one row per available employee/date/shift option
- `DimEmployee`: employee master data
- `DimDate`: generated calendar table
- `DimShiftType`: normalized shift categories

Recommended model relationships use one-to-many, single-direction filtering from each dimension to both fact tables.

## Key transformation work

- Combined monthly Microsoft Forms extracts
- Standardized column names, dates, times, and shift labels
- Unpivoted availability selections into a row-based fact table
- Deduplicated repeated submissions
- Created an employee mapping layer for inconsistent names
- Built a composite match key using employee, date, and shift type
- Excluded `Manager's Shift` from availability-match calculations because it has no comparable Forms option

## Main result demonstrated by the project

The solution turns separate scheduling and Forms files into a reusable Power BI model that identifies assignments within and outside employee availability, while also providing management-level workload and staffing views.

## Repository contents

```text
sample_data/
  Workforce_Analytics_Synthetic_Data.xlsx
documentation/
  DAX_Measures.md
  Power_Query_Transformations.md
screenshots/
  ADD_SCREENSHOTS.md
CV_Bullet.txt
```

## Recreate the report

1. Load the three data sheets from the synthetic workbook.
2. Create `DimDate` and `DimShiftType` using the examples in the documentation folder.
3. Create the relationships described above.
4. Add the documented DAX measures.
5. Build the two report pages using the dashboard-page descriptions.

## Tools

Power BI Desktop, Power Query, DAX, Microsoft Forms, Excel

