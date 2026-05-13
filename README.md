# 📊 DAX Measures Library

> A production-ready collection of 20 DAX patterns for Power BI — built from 10+ years of real-world analytics experience in US Healthcare and Group Insurance domains.

---

## What's Inside

This library covers the most commonly needed DAX patterns across operational analytics, workforce management, and executive reporting. Every measure includes a **business use case** and **usage notes** so you can implement it in minutes — not hours.

| Category | Measures |
|---|---|
| ⏱ Time Intelligence | YTD Volume, Prior Year YTD, YTD % Change, Rolling 30/90-Day Average, MoM % Change |
| 🏆 Ranking | RANKX Agent Performance, RANKX Team/Queue Ranking |
| ✅ Operations & SLA | SLA Compliance Rate, Average TAT Days |
| 👥 Workforce & Utilization | Production vs Target, Utilization Rate %, Non-Production Time % |
| 🔄 Advanced Patterns | WIP Case Count, Dynamic MTD/QTD/YTD Switch, Running Total, Forecast vs Actual Variance |
| 📈 Quality & Reporting | Headcount Efficiency Index, Error Rate %, Dynamic Period Label |

---

## How to Use

### Requirements
Before using these measures, ensure your Power BI model has:
- A **dedicated Date table** marked as a Date Table (`Modeling → Mark as Date Table`)
- An **active relationship** between your fact table(s) and the Date table
- Column names updated to match your own data model (each measure clearly states the expected columns)

### Adding a Measure to Your Report

1. Open your Power BI Desktop file
2. Go to **Modeling → New Measure** (or right-click a table in the Fields pane)
3. Copy and paste the DAX code from [`DAX_Measures_Library.md`](./DAX_Measures_Library.md)
4. Rename the measure to suit your naming convention
5. Update any table or column references to match your data model
6. Set the correct format (%, whole number, decimal) from the **Measure Tools** ribbon

### Quick Example

```dax
-- Paste this into a New Measure to track YTD volume
YTD Volume =
TOTALYTD(
    SUM( Transactions[Volume] ),
    'Date'[Date]
)
```

### Tips
- All measures use `DIVIDE()` instead of `/` to safely handle zero denominators
- `ALLSELECTED` is used in ranking measures so they respect page-level slicers
- The Dynamic Period Switch (Measure 15) requires a disconnected slicer table — setup steps are included in the measure notes

---

## Files in This Repository

| File | Description |
|---|---|
| `README.md` | This file — overview and setup guide |
| `DAX_Measures_Library.md` | All 20 measures with business context and DAX code |

---

## About the Author

**Bhavesh Hingu**  
Senior Data Analytics & Power BI Specialist at Accenture Pvt. Ltd.  
10+ years experience · US Healthcare & Group Insurance · Mumbai, India

**Core skills:** Power BI · DAX · SQL · Power Apps · Power Automate · SharePoint · Advanced Excel · RPA · Lean & Six Sigma

📧 bhaveshh694@gmail.com  
💼 [LinkedIn](https://www.linkedin.com/in/bhavesh-hingu)

---

## License

This library is shared for learning and reference purposes. Feel free to use and adapt any measure in your own Power BI projects. Attribution appreciated but not required.

---

*If you find this useful, consider giving the repo a ⭐ — it helps others discover it too!*
