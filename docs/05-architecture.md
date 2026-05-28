# System Architecture
### Hannah Olagunju — Business Central & Data Transformation

<img src="../assets/logo.jpeg" width="160"/>

---

## How the components connect

```
┌─────────────────────────────────────────────────────────┐
│                  Business Central (ERP)                  │
│                                                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │ G/L Entries  │  │ Cust. Ledger │  │  Purch. Hdrs │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
│           │                │                │           │
│           └────────────────┴────────────────┘           │
│                            │                            │
│                    OData v4 API                         │
└────────────────────────────┼────────────────────────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
    ┌─────────▼──────────┐      ┌───────────▼──────────┐
    │     Power BI        │      │      Power Apps       │
    │                     │      │                       │
    │  Semantic model     │      │  Finance Actions App  │
    │  DAX measures       │      │  Canvas UI            │
    │  5 report pages     │      │  5 screens            │
    └─────────────────────┘      └───────────┬───────────┘
                                             │
                                    User action
                                             │
                              ┌──────────────▼──────────┐
                              │      Power Automate      │
                              │                          │
                              │  Chase Invoice flow      │
                              │  Add Note flow           │
                              │  PO Approval flow        │
                              └──────────────┬───────────┘
                                             │
                                    Write back
                                             │
                              ┌──────────────▼───────────┐
                              │   Business Central (BC)   │
                              │   BC Connector (native)   │
                              └──────────────────────────┘
```

---

## Data flow — Power BI (read-only)

```
BC OData API
    → Power Query (M code transforms + data type casting)
        → Power BI semantic model (star schema)
            → DAX measures
                → Report visuals (5 pages)
                    → Scheduled refresh (daily)
```

**Key design decisions:**

- Star schema with a central DateTable connected to all fact tables via posting date
- All measures in a single `_Measures` blank table — easier to maintain and find
- Fiscal year calculated column supports UK April year-start
- What-If parameter stored as a separate table to avoid circular dependency

---

## Data flow — Power Apps (read + write)

```
BC OData API (read)
    → Power Apps gallery / form controls
        → User takes action (tap button)
            → Power Automate flow triggered (with parameters)
                → BC Connector (write action)
                    → BC record updated
                        → Confirmation sent (Teams / Email)
```

**Key design decisions:**

- `varSelectedInvoice` variable stores the selected record to avoid re-querying BC on every screen navigation
- Loading spinner (`varLoading`) shows during any async operation — prevents double-taps
- BC Connector used for write-back (not raw OData PATCH) — more reliable, handles BC's ETag concurrency model
- Flows parameterised from Power Apps — not hardcoded — so the same flow works for any invoice

---

## Why not use the BC Power BI content pack?

Microsoft ships a pre-built BC content pack for Power BI. This project intentionally does not use it because:

1. The content pack uses fixed schemas — you cannot add custom measures or dimensions
2. It does not support project/job margin analysis
3. The What-If scenario page is not possible with the content pack
4. Building a custom semantic model demonstrates deeper technical knowledge

This approach is what a senior BI consultant would recommend for a client who needs flexibility.

---

## Known limitations and production considerations

| Limitation | Impact | Production fix |
|---|---|---|
| OData API paginates at 20,000 rows | Slow initial load for large datasets | Implement incremental refresh in Power BI |
| Power Apps OData delegation limit | Gallery shows max 500 rows without server-side delegation | Use a dataflow or Azure SQL staging layer |
| No row-level security | All users see all data | Add RLS in Power BI semantic model by user email |
| BC connector rate limits | Heavy use of flows may throttle | Add retry logic and exponential backoff in flows |

Documenting these limitations shows architectural maturity — a junior would leave them out.

---

<img src="../assets/logo.jpeg" width="80"/>

*Hannah Olagunju — Business Central & Data Transformation*
