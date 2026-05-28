# DAX Measures Reference
### Hannah Olagunju — Business Central & Data Transformation

<img src="../assets/logo.jpeg" width="160"/>

---

All measures live in a dedicated blank table called `_Measures` inside the Power BI model. They are grouped into five folders.

---

## Folder: Revenue

```dax
Total Revenue =
SUMX(
    FILTER('Customer Ledger Entry', [Entry Type] = "Invoice"),
    [Amount]
)
```
> Sums all posted sales invoices from the Customer Ledger Entry table.

---

```dax
Revenue YTD =
TOTALYTD([Total Revenue], 'DateTable'[Date])
```
> Cumulative revenue from the start of the fiscal year to the current filter context date.

---

```dax
Revenue vs Prior Year =
[Total Revenue]
    - CALCULATE(
        [Total Revenue],
        SAMEPERIODLASTYEAR('DateTable'[Date])
      )
```
> Variance between current period revenue and the same period last year. Positive = growth, negative = decline.

---

```dax
Revenue Growth % =
DIVIDE(
    [Revenue vs Prior Year],
    CALCULATE([Total Revenue], SAMEPERIODLASTYEAR('DateTable'[Date])),
    0
)
```
> Percentage growth vs prior year. DIVIDE handles zero-division safely.

---

## Folder: Cost

```dax
Total COGS =
SUMX(
    FILTER(
        'G/L Entry',
        'G/L Entry'[G/L Account No.] >= "5000"
            && 'G/L Entry'[G/L Account No.] < "6000"
    ),
    'G/L Entry'[Amount]
)
```
> Sums all G/L entries in the cost of goods sold account range (5000–5999). Adjust the account range to match your chart of accounts.

---

## Folder: Profitability

```dax
Gross Profit =
[Total Revenue] + [Total COGS]
```
> Note: COGS posts as negative in BC, so addition gives the correct gross profit figure.

---

```dax
Gross Margin % =
DIVIDE([Gross Profit], [Total Revenue], 0)
```
> Gross profit as a percentage of revenue.

---

```dax
Operating Expenses =
SUMX(
    FILTER(
        'G/L Entry',
        'G/L Entry'[G/L Account No.] >= "6000"
            && 'G/L Entry'[G/L Account No.] < "7000"
    ),
    'G/L Entry'[Amount]
)
```

---

```dax
Net Profit =
[Gross Profit] + [Operating Expenses]
```

---

## Folder: Debtors

```dax
Outstanding Debtors =
SUMX(
    FILTER('Customer Ledger Entry', [Open] = TRUE()),
    [Remaining Amount]
)
```
> Total unpaid invoices across all customers.

---

```dax
Overdue Amount =
CALCULATE(
    [Outstanding Debtors],
    FILTER('Customer Ledger Entry', [Due Date] < TODAY())
)
```
> Subset of outstanding debtors where due date has passed. Used on the KPI card with red conditional formatting.

---

```dax
Overdue 30 Days =
CALCULATE(
    [Outstanding Debtors],
    FILTER('Customer Ledger Entry',
        [Due Date] < TODAY()
            && [Due Date] >= TODAY() - 30
    )
)
```

---

```dax
Overdue 31-60 Days =
CALCULATE(
    [Outstanding Debtors],
    FILTER('Customer Ledger Entry',
        [Due Date] < TODAY() - 30
            && [Due Date] >= TODAY() - 60
    )
)
```

---

```dax
Overdue 60+ Days =
CALCULATE(
    [Outstanding Debtors],
    FILTER('Customer Ledger Entry',
        [Due Date] < TODAY() - 60
    )
)
```

---

## Folder: Scenarios (What-If)

These measures use the What-If parameter created in Power BI Modelling.

```dax
Projected Revenue =
[Total Revenue] * (1 + 'Revenue Growth %'[Revenue Growth % Value])
```

---

```dax
Projected Gross Profit =
[Projected Revenue] + [Total COGS]
```

---

```dax
Projected Gross Margin % =
DIVIDE([Projected Gross Profit], [Projected Revenue], 0)
```

---

## Date Table (calculated table)

```dax
DateTable =
CALENDAR(DATE(2019, 1, 1), DATE(2026, 12, 31))
```

**Additional columns added to DateTable:**

```dax
Year = YEAR([Date])
Month Number = MONTH([Date])
Month Name = FORMAT([Date], "MMM")
Month Year = FORMAT([Date], "MMM YYYY")
Quarter = "Q" & QUARTER([Date])
Week Number = WEEKNUM([Date])
Day of Week = FORMAT([Date], "ddd")
Is Weekend = IF(WEEKDAY([Date], 2) >= 6, TRUE(), FALSE())

-- UK fiscal year (April start)
Fiscal Year =
IF(
    MONTH([Date]) >= 4,
    "FY" & YEAR([Date]) & "/" & RIGHT(YEAR([Date]) + 1, 2),
    "FY" & YEAR([Date]) - 1 & "/" & RIGHT(YEAR([Date]), 2)
)

Fiscal Month =
IF(MONTH([Date]) >= 4, MONTH([Date]) - 3, MONTH([Date]) + 9)
```

---

## Conditional formatting rules

Applied to KPI cards on the P&L Summary page:

```
Gross Margin % card colour:
  If [Gross Margin %] >= 0.30 → Green (#27500A)
  If [Gross Margin %] >= 0.15 → Amber (#854F0B)
  If [Gross Margin %] < 0.15  → Red (#A32D2D)

Overdue Amount card colour:
  If [Overdue Amount] = 0     → Green
  If [Overdue Amount] > 0     → Red
```

---

<img src="../assets/logo.jpeg" width="80"/>

*Hannah Olagunju — Business Central & Data Transformation*
