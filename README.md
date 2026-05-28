# BC Financial Intelligence Layer & Finance Approvals App
### By Hannah Olagunju — Business Central & Data Transformation

<p align="center">
  <img src="assets/logo.jpeg" alt="Hannah Olagunju — Business Central & Data Transformation" width="280"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
  <img src="https://img.shields.io/badge/Business%20Central-0078D4?style=for-the-badge&logo=microsoft&logoColor=white"/>
  <img src="https://img.shields.io/badge/Power%20Apps-742774?style=for-the-badge&logo=powerapps&logoColor=white"/>
  <img src="https://img.shields.io/badge/Power%20Automate-0066FF?style=for-the-badge&logo=powerautomate&logoColor=white"/>
  <img src="https://img.shields.io/badge/DAX-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
  <img src="https://img.shields.io/badge/OData%20API-E44D26?style=for-the-badge"/>
</p>

---

## What This Project Does

Most companies running Business Central still produce financial reports manually — exporting data to Excel, copy-pasting, reformatting every month. This project eliminates that entirely.

It consists of **two connected components**:

| Component | Purpose |
|---|---|
| **Power BI Financial Intelligence Layer** | Live reports connected directly to BC via OData API — P&L, cash flow, aged debtors, project margins, and what-if scenarios. Always up to date. No manual exports. |
| **Finance Approvals Canvas App** | A Power Apps app that reads the same BC data and lets managers action overdue invoices, approve purchase orders, and code expenses — writing results back to BC via Power Automate. |

Together they form a **complete finance platform**: Power BI for insight, Power Apps for action, Power Automate for automation, Business Central as the system of record.

> Any BC customer can deploy both components in under 2 hours using the guides in this repo.

---

## Live Demo

> **[Watch the 2-minute walkthrough on Loom →](#)** *(replace # with your Loom link after recording)*

---

## Repository Structure

```
bc-financial-intelligence/
│
├── README.md                         ← You are here
├── assets/
│   └── logo.jpeg                     ← Hannah Olagunju branding
│
├── powerbi/
│   ├── dashboard.pbix                ← Power BI report file
│   └── screenshots/
│       ├── 01-pl-summary.png
│       ├── 02-cash-flow.png
│       ├── 03-aged-debtors.png
│       ├── 04-project-margins.png
│       └── 05-what-if-scenario.png
│
├── powerapps/
│   ├── FinanceActionsApp.msapp       ← Exported canvas app
│   └── screenshots/
│       ├── 01-home-dashboard.png
│       ├── 02-overdue-invoices.png
│       ├── 03-invoice-detail.png
│       └── 04-po-approvals.png
│
├── powerautomate/
│   ├── chase-invoice-flow.json
│   ├── add-invoice-note-flow.json
│   └── po-approval-flow.json
│
└── docs/
    ├── 01-bc-setup-guide.md
    ├── 02-powerbi-setup.md
    ├── 03-powerapps-setup.md
    ├── 04-dax-measures.md
    └── 05-architecture.md
```

---

## The Problem This Solves

Construction and SME finance teams running Business Central face a common pain:

- **Reporting** happens monthly, manually, in Excel — even though all the data is already in BC
- **Overdue invoices** are chased by email chains with no tracking
- **Purchase order approvals** require logging into BC directly — most managers avoid it
- **Expense coding** backlogs because no one has a simple way to action them

This project replaces all four pain points with a connected Power Platform solution built on top of BC's existing data.

---

## Power BI Reports — What's Included

### Report pages

| Page | What It Shows |
|---|---|
| P&L Summary | Revenue vs prior year, gross margin %, top accounts with conditional KPI cards |
| Cash Flow | Rolling 13-week cash position, incoming vs outgoing by week |
| Aged Debtors | Ageing buckets (0–30, 31–60, 61–90, 90+ days) by customer |
| Project Margins | Revenue vs cost vs margin % by job, filterable by project manager |
| What-If Scenarios | Drag a Revenue Growth % slider and see projected P&L update live |

### Key DAX measures

```dax
Total Revenue =
SUMX(
    FILTER('Customer Ledger Entry', [Entry Type] = "Invoice"),
    [Amount]
)

Gross Margin % =
DIVIDE([Gross Profit], [Total Revenue], 0)

Revenue YTD =
TOTALYTD([Total Revenue], 'DateTable'[Date])

Revenue vs Prior Year =
[Total Revenue] - CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR('DateTable'[Date])
)

Overdue Amount =
CALCULATE(
    [Outstanding Debtors],
    FILTER('Customer Ledger Entry', [Due Date] < TODAY())
)
```

Full measure documentation: [`docs/04-dax-measures.md`](./docs/04-dax-measures.md)

---

## Power Apps — Finance Actions App

### Screens

| Screen | What The User Can Do |
|---|---|
| Home dashboard | See count of overdue invoices, pending POs, uncoded expenses at a glance |
| Overdue invoices | Browse and search all overdue invoices, sorted by days overdue |
| Invoice detail | Chase the customer, add a note, or escalate — written back to BC |
| PO approvals | Approve or reject purchase orders with comments |
| Expense coding | Assign G/L codes to unposted entries |

### Write-back flows

```
User taps "Chase customer"
    → Sends templated email to customer via Office 365
    → Posts Teams notification to finance channel
    → Creates follow-up task in Planner
    → Logs action in BC customer ledger

User taps "Approve PO"
    → Updates PO status to "Released" in BC
    → Sends confirmation email to requestor
    → Posts audit record
```

---

## Screenshots

### Power BI Dashboard

| P&L Summary | Aged Debtors |
|---|---|
| ![P&L](./powerbi/screenshots/01-pl-summary.png) | ![Aged Debtors](./powerbi/screenshots/03-aged-debtors.png) |

| Cash Flow | What-If Scenario |
|---|---|
| ![Cash Flow](./powerbi/screenshots/02-cash-flow.png) | ![What-If](./powerbi/screenshots/05-what-if-scenario.png) |

### Power Apps

| Home | Overdue Invoices | Invoice Detail |
|---|---|---|
| ![Home](./powerapps/screenshots/01-home-dashboard.png) | ![Invoices](./powerapps/screenshots/02-overdue-invoices.png) | ![Detail](./powerapps/screenshots/03-invoice-detail.png) |

---

## How to Deploy This

### Prerequisites
- Microsoft 365 account (or free developer tenant at developer.microsoft.com)
- Business Central environment (trial or production)
- Power BI Desktop (free download from Microsoft)
- Power Apps licence (included in Microsoft 365)

### Quick start

```
Step 1 → Follow docs/01-bc-setup-guide.md    to expose your BC OData endpoints
Step 2 → Follow docs/02-powerbi-setup.md     to connect and publish the dashboard
Step 3 → Follow docs/03-powerapps-setup.md   to import and configure the canvas app
```

Full setup takes approximately 2 hours for someone familiar with the Power Platform.

---

## About

Built by **Hannah Olagunju** — Business Central Functional Consultant & Data Analyst, UK.

- LinkedIn: [Add your LinkedIn URL]
- GitHub: [Add your GitHub URL]

---

<p align="center">
  <img src="assets/logo.jpeg" width="120"/><br>
  <em>Hannah Olagunju — Business Central & Data Transformation</em>
</p>
