# Power BI Setup Guide
### Hannah Olagunju — Business Central & Data Transformation

<img src="../assets/logo.jpeg" width="160"/>

---

## Prerequisites

- Power BI Desktop installed (free from [powerbi.microsoft.com](https://powerbi.microsoft.com))
- BC OData URL ready (from `docs/01-bc-setup-guide.md`)
- Microsoft 365 account with BC access

---

## Step 1 — Open the Power BI file

1. Download **`powerbi/dashboard.pbix`** from this repository
2. Open it in Power BI Desktop
3. You will see a message saying the data source credentials need updating — this is expected

---

## Step 2 — Update the OData data source

1. In Power BI Desktop, go to **Home → Transform data → Data source settings**
2. Select the OData source and click **Change Source**
3. Replace the URL with your own BC OData URL:

```
https://api.businesscentral.dynamics.com/v2.0/{your-tenant-id}/{environment}/ODataV4/
```

4. Click **OK**
5. Click **Edit Permissions** → **Edit** → sign in with your Microsoft 365 account
6. Click **Close**

---

## Step 3 — Refresh the data

1. Click **Home → Refresh**
2. Power BI will query each BC table — this may take 1–2 minutes the first time
3. Once complete, all 5 report pages should populate with your BC data

---

## Step 4 — Review the data model

1. Click the **Model view** icon (left sidebar, looks like three connected boxes)
2. You should see these tables connected:

```
DateTable ←─── Customer Ledger Entry (via Posting Date)
DateTable ←─── G/L Entry (via Posting Date)
DateTable ←─── Job Ledger Entry (via Posting Date)
DateTable ←─── Vendor Ledger Entry (via Posting Date)
Customer  ←─── Customer Ledger Entry (via Customer No.)
```

3. If any relationship is missing, right-click the canvas → **Manage relationships** → **New**

---

## Step 5 — Check the date table

The date table is critical for all time-intelligence measures (YTD, prior year comparisons). Verify it:

1. In the **Fields** pane, find **DateTable**
2. Right-click → **Mark as date table** → select the **Date** column
3. This enables Power BI's time intelligence functions

---

## Step 6 — Review the measures

All DAX measures are stored in the **_Measures** table (blank table). Key ones to verify:

```dax
-- Check these in the Fields pane under _Measures:
Total Revenue
Gross Profit
Gross Margin %
Revenue YTD
Revenue vs Prior Year
Outstanding Debtors
Overdue Amount
Projected Revenue (what-if)
```

Full documentation of every measure: [`docs/04-dax-measures.md`](./04-dax-measures.md)

---

## Step 7 — Set up the What-If parameter

The Scenarios page uses a What-If parameter. If it's not working:

1. Go to **Modelling → New parameter**
2. Name: **Revenue Growth %**
3. Data type: **Decimal number**
4. Minimum: **-0.20** | Maximum: **0.50** | Increment: **0.01**
5. Default: **0**
6. Click **OK** — this adds a slicer and a measure automatically

---

## Step 8 — Take screenshots for GitHub

Once the report is populated with data, take screenshots of each page for the README:

| File name | Which page |
|---|---|
| `01-pl-summary.png` | P&L Summary page |
| `02-cash-flow.png` | Cash Flow page |
| `03-aged-debtors.png` | Aged Debtors page |
| `04-project-margins.png` | Project Margins page |
| `05-what-if-scenario.png` | What-If Scenarios page |

Save them to: `powerbi/screenshots/`

**How to take a good screenshot:**
- Use full screen (F11 in Power BI Desktop)
- Make sure filters are cleared so all data shows
- On the What-If page, set the slider to around 15% so the change is visible

---

## Step 9 — Publish to Power BI Service (optional but impressive)

1. In Power BI Desktop: **Home → Publish**
2. Select your workspace
3. Once published, go to [app.powerbi.com](https://app.powerbi.com)
4. Find your report → **File → Embed report → Website or portal**
5. Copy the embed URL and add it to your README as a live demo link

---

## Troubleshooting

| Issue | Fix |
|---|---|
| "Unable to connect" error | Re-enter credentials in Data source settings |
| Blank visuals after refresh | Check the relationship between DateTable and fact tables |
| What-If slider not working | Re-create the parameter (Step 7) |
| Slow refresh | BC OData paginates — first refresh is always slowest |

---

## Next step

→ [docs/03-powerapps-setup.md](./03-powerapps-setup.md) — Deploy the Finance Approvals App

---

<img src="../assets/logo.jpeg" width="80"/>

*Hannah Olagunju — Business Central & Data Transformation*
