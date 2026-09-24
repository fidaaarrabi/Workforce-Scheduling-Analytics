# Power Query Transformation Notes

## Recommended query flow

1. Import the shift export and monthly availability files.
2. Promote headers and keep only required fields.
3. Apply data types using an explicit locale for ambiguous dates.
4. Trim and clean employee names, emails, and shift labels.
5. Append the monthly availability queries.
6. Unpivot shift-option columns into `AvailabilityDate` and `ShiftType` rows.
7. Remove blank and duplicate availability selections.
8. Apply the employee-name mapping table.
9. Create the same match key in both fact tables.
10. Merge the availability key into the shifts query and assign the match status.

## Locale-safe date conversion

Use the locale that matches the source file rather than relying on the computer's default regional settings.

```powerquery
= Table.TransformColumnTypes(
    PreviousStep,
    {{"ShiftDate", type date}},
    "en-GB"
)
```

## Text normalization

```powerquery
= Table.TransformColumns(
    PreviousStep,
    {
        {"EmployeeName", each Text.Trim(Text.Clean(_)), type text},
        {"Email", each Text.Lower(Text.Trim(_)), type text},
        {"ShiftType", each Text.Trim(Text.Clean(_)), type text}
    }
)
```

## Shift-label normalization

```powerquery
= Table.ReplaceValue(
    PreviousStep,
    "LongNoon",
    "Long Noon",
    Replacer.ReplaceText,
    {"ShiftType"}
)
```

## Composite match key

Create the key in both `FactShifts` and `FactAvailability` after employee IDs and shift types are standardized.

```powerquery
= Table.AddColumn(
    PreviousStep,
    "AvailabilityKey",
    each [EmployeeID] & "|" &
         Date.ToText([ShiftDate], "yyyy-MM-dd") & "|" &
         [ShiftType],
    type text
)
```

For the availability table, replace `[ShiftDate]` with `[AvailabilityDate]`.

## Match-status logic

After a left outer merge from shifts to distinct availability keys:

```powerquery
= Table.AddColumn(
    PreviousStep,
    "AvailabilityStatus",
    each
        if [ShiftType] = "Manager's Shift" then "Not Comparable"
        else if [MatchedAvailabilityKey] <> null then "Within Availability"
        else "Outside Availability",
    type text
)
```

## Employee mapping pattern

Keep a small two-column mapping query named `EmployeeNameMap`:

```text
SourceName | StandardName
```

Merge it into the availability data and use `StandardName` when present; otherwise retain the cleaned source name. In a production model, a stable employee ID is preferable to name-based matching.

