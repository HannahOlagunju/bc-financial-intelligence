# Power Apps Setup Guide — Finance Actions App
### Hannah Olagunju — Business Central & Data Transformation

<img src="../assets/logo.jpeg" width="160"/>

---

## What This App Does

The Finance Actions App is a canvas app that sits on top of your BC data. Finance managers use it to:
- See overdue invoices and chase customers in one tap
- Approve or reject purchase orders without opening BC
- Code unposted expense entries directly from their phone or browser

Every action writes back to Business Central via Power Automate.

---

## Prerequisites

- Microsoft 365 account with Power Apps licence (included in most M365 plans)
- BC OData connection working (from `docs/01-bc-setup-guide.md`)
- Power Automate flows imported (see Step 3 below)

---

## Step 1 — Import the canvas app

1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Make sure you're in the correct **environment** (top right dropdown)
3. Click **Apps** in the left sidebar
4. Click **Import canvas app**
5. Upload the file: `powerapps/FinanceActionsApp.msapp`
6. Click **Upload** → **Import**

---

## Step 2 — Reconnect the data sources

After importing, the app will show broken connections. Fix them:

1. Open the imported app in **Edit mode**
2. Click **Data** (left sidebar, cylinder icon)
3. You'll see greyed-out connections — click each one and select **Remove**
4. Re-add each data source:

**Add Business Central (OData):**
- Click **Add data** → search for **OData**
- Paste your BC OData URL
- Sign in with your Microsoft 365 account
- Select these tables:
  - `CustomerLedgerEntries`
  - `PurchaseHeaders`
  - `GLEntries`

**Add Office 365 Users:**
- Click **Add data** → search for **Office 365 Users**
- Sign in — this connects automatically

5. Click **Save**

---

## Step 3 — Import the Power Automate flows

The app triggers three flows when users take actions. Import them first:

**How to import each flow:**
1. Go to [make.powerautomate.com](https://make.powerautomate.com)
2. Click **My flows** → **Import** → **Import Package (Legacy)**
3. Upload the flow file from `powerautomate/` folder
4. For each connection reference, click **Select during import** and pick your existing connection
5. Click **Import**

**Repeat for all three flows:**

| File | Flow name | What it does |
|---|---|---|
| `chase-invoice-flow.json` | Chase Invoice | Emails customer, posts Teams message, creates Planner task |
| `add-invoice-note-flow.json` | Add Invoice Note | Writes note back to BC customer ledger |
| `po-approval-flow.json` | PO Approval | Updates PO status in BC, emails requestor |

---

## Step 4 — Connect the flows to the app

1. Back in the app (Edit mode in Power Apps)
2. Click **Power Automate** icon in the left sidebar
3. Click **Add flow**
4. You'll see the three flows you just imported — add all three
5. They will now be callable from buttons in the app

---

## Step 5 — Test the app

Work through each screen to verify everything connects:

**Home screen:**
- [ ] Your name appears in the welcome message
- [ ] The three KPI count cards show numbers (not errors)
- [ ] All three navigation buttons work

**Overdue invoices screen:**
- [ ] The gallery loads a list of invoices
- [ ] Search bar filters results
- [ ] Tapping a row opens the invoice detail screen

**Invoice detail screen:**
- [ ] Invoice details display correctly
- [ ] "Chase customer" button shows a confirmation banner
- [ ] Notes text input accepts text and the Save button works

**PO approvals screen:**
- [ ] List of pending POs loads
- [ ] Approve and Reject buttons trigger correctly

---

## Step 6 — Take screenshots for GitHub

Once the app is working, take screenshots of each screen:

| File name | Screen |
|---|---|
| `01-home-dashboard.png` | Home with KPI cards populated |
| `02-overdue-invoices.png` | Invoice list with data |
| `03-invoice-detail.png` | Invoice detail with action buttons visible |
| `04-po-approvals.png` | PO list |

Save them to: `powerapps/screenshots/`

**Tips for good screenshots:**
- Use a browser at full width (not the phone preview)
- Make sure real data is showing — not empty states
- Show the action buttons clearly on the detail screen

---

## Step 7 — Share the app

To let others view it (or for your Loom demo):

1. In Power Apps: **File → Share**
2. Search for a colleague's email or, for a demo, share with yourself
3. They'll receive an email with a direct link

For your portfolio, share with anyone you want to demo it to.

---

## Troubleshooting

| Issue | Fix |
|---|---|
| "Delegation warning" yellow triangle | Normal for OData — means filter runs locally. Fine for portfolio use. |
| Blank gallery after connecting OData | Check the table name matches exactly — BC table names are case-sensitive |
| Flow not triggering | Re-add the flow in the Power Automate panel (Step 4) |
| "Connection not found" error | Remove and re-add the OData data source (Step 2) |

---

## Next step

→ [docs/04-dax-measures.md](./04-dax-measures.md) — Full DAX measure reference

---

<img src="../assets/logo.jpeg" width="80"/>

*Hannah Olagunju — Business Central & Data Transformation*
