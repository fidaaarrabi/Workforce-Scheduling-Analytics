# Core DAX Measures

Adjust table or column names if your Power BI model uses different names.

```DAX
Total Shifts =
COUNTROWS(FactShifts)

Total Hours =
SUM(FactShifts[Hours])

Employees with Shifts =
DISTINCTCOUNT(FactShifts[EmployeeID])

Average Hours per Employee =
DIVIDE([Total Hours], [Employees with Shifts])

Average Shift Length =
DIVIDE([Total Hours], [Total Shifts])

Working Days =
DISTINCTCOUNT(FactShifts[ShiftDate])

Availability Options =
COUNTROWS(FactAvailability)

Comparable Assignments =
CALCULATE(
    [Total Shifts],
    FactShifts[AvailabilityStatus] <> "Not Comparable"
)

Assignments Within Availability =
CALCULATE(
    [Total Shifts],
    FactShifts[AvailabilityStatus] = "Within Availability"
)

Assignments Outside Availability =
CALCULATE(
    [Total Shifts],
    FactShifts[AvailabilityStatus] = "Outside Availability"
)

Availability Match % =
DIVIDE(
    [Assignments Within Availability],
    [Comparable Assignments]
)

Average Daily Employees =
AVERAGEX(
    VALUES(DimDate[Date]),
    CALCULATE(DISTINCTCOUNT(FactShifts[EmployeeID]))
)

Data Period =
"Data period: " &
FORMAT(MIN(FactShifts[ShiftDate]), "dd MMM yyyy") &
" – " &
FORMAT(MAX(FactShifts[ShiftDate]), "dd MMM yyyy")
```

## Calculated table examples

```DAX
DimDate =
ADDCOLUMNS(
    CALENDAR(
        MIN(FactShifts[ShiftDate]),
        MAX(FactShifts[ShiftDate])
    ),
    "Year", YEAR([Date]),
    "MonthNo", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMMM"),
    "YearMonth", FORMAT([Date], "yyyy-MM"),
    "DayName", FORMAT([Date], "dddd"),
    "DayOfWeekNo", WEEKDAY([Date], 2)
)

DimShiftType =
DISTINCT(
    UNION(
        SELECTCOLUMNS(FactShifts, "ShiftType", FactShifts[ShiftType]),
        SELECTCOLUMNS(FactAvailability, "ShiftType", FactAvailability[ShiftType])
    )
)
```

Sort `DimDate[DayName]` by `DimDate[DayOfWeekNo]`, and sort month labels using a numeric month or date key.

