# 📊 DAX Measures Library
### Power BI | US Healthcare & Group Insurance Analytics
**Author:** Bhavesh Hingu — Senior Data Analytics & Power BI Specialist, Accenture  
**Domain:** US Healthcare | Group Insurance | BPO Operations  
**Experience:** 10+ Years | Power BI | DAX | SQL | Power Automate

---

## About This Library

A curated collection of production-ready DAX measures built and refined over 10+ years of real-world analytics work in US Healthcare and Group Insurance domains. Each measure includes the **business use case**, **DAX code**, and **usage notes** to help you implement it quickly in your own Power BI models.

---

## Table of Contents

1. [Year-to-Date (YTD) Volume](#1-year-to-date-ytd-volume)
2. [Prior Year YTD Comparison](#2-prior-year-ytd-comparison)
3. [YTD vs Prior YTD % Change](#3-ytd-vs-prior-ytd--change)
4. [Rolling 30-Day Average](#4-rolling-30-day-average)
5. [Rolling 90-Day Average](#5-rolling-90-day-average)
6. [Month-over-Month % Change](#6-month-over-month--change)
7. [RANKX — Agent Performance Ranking](#7-rankx--agent-performance-ranking)
8. [RANKX — Team/Queue Ranking](#8-rankx--teamqueue-ranking)
9. [SLA Compliance Rate](#9-sla-compliance-rate)
10. [TAT (Turnaround Time) Average](#10-tat-turnaround-time-average)
11. [Production Hours vs Target](#11-production-hours-vs-target)
12. [Utilization Rate %](#12-utilization-rate-)
13. [Non-Production Time %](#13-non-production-time-)
14. [Cases in Pending / WIP Count](#14-cases-in-pending--wip-count)
15. [Dynamic Period Comparison (MTD / QTD / YTD Switch)](#15-dynamic-period-comparison-mtd--qtd--ytd-switch)
16. [Cumulative Volume (Running Total)](#16-cumulative-volume-running-total)
17. [Forecast vs Actual Variance](#17-forecast-vs-actual-variance)
18. [Headcount Efficiency Index](#18-headcount-efficiency-index)
19. [Error Rate %](#19-error-rate-)
20. [Selected Period Label (Dynamic Title)](#20-selected-period-label-dynamic-title)

---

## 1. Year-to-Date (YTD) Volume

**Business Use Case:**  
Track cumulative case/transaction volume from the start of the current fiscal or calendar year to the selected date. Widely used in healthcare operations dashboards to monitor annual throughput targets.

```dax
YTD Volume =
TOTALYTD(
    SUM( Transactions[Volume] ),
    'Date'[Date]
)
```

> **Notes:**  
> - Requires a properly marked Date table in your model.  
> - For fiscal year, add the fiscal year end date: `TOTALYTD( SUM(...), 'Date'[Date], "03/31" )`

---

## 2. Prior Year YTD Comparison

**Business Use Case:**  
Compare current YTD performance against the same period in the prior year. Essential for leadership reporting in Group Insurance to show annual growth or decline in claims/enrollments.

```dax
Prior Year YTD Volume =
CALCULATE(
    [YTD Volume],
    SAMEPERIODLASTYEAR( 'Date'[Date] )
)
```

> **Notes:**  
> - Automatically adjusts for leap years.  
> - Pair with [YTD Volume] in a KPI card for instant year-over-year context.

---

## 3. YTD vs Prior YTD % Change

**Business Use Case:**  
Quantify the percentage growth or decline in YTD volume versus last year. Used in executive scorecards and monthly business reviews to highlight performance trends.

```dax
YTD vs PY YTD % Change =
VAR CurrentYTD = [YTD Volume]
VAR PriorYTD   = [Prior Year YTD Volume]
RETURN
    IF(
        PriorYTD = 0,
        BLANK(),
        DIVIDE( CurrentYTD - PriorYTD, PriorYTD )
    )
```

> **Notes:**  
> - Format as Percentage in the Modeling pane.  
> - The `DIVIDE` function safely handles zero denominators.

---

## 4. Rolling 30-Day Average

**Business Use Case:**  
Smooth out daily volume spikes and show underlying trends in case processing or call volumes. Particularly useful in BPO operations dashboards to detect capacity issues early.

```dax
Rolling 30-Day Avg Volume =
CALCULATE(
    AVERAGEX(
        VALUES( 'Date'[Date] ),
        CALCULATE( SUM( Transactions[Volume] ) )
    ),
    DATESINPERIOD(
        'Date'[Date],
        LASTDATE( 'Date'[Date] ),
        -30,
        DAY
    )
)
```

> **Notes:**  
> - Works best on a line chart with Date on the X-axis.  
> - Adjust `-30` to any window size as needed.

---

## 5. Rolling 90-Day Average

**Business Use Case:**  
Provides a longer-term trend view for utilization or quality metrics. Used in workforce management reporting to inform quarterly staffing and capacity planning decisions.

```dax
Rolling 90-Day Avg Volume =
CALCULATE(
    AVERAGEX(
        VALUES( 'Date'[Date] ),
        CALCULATE( SUM( Transactions[Volume] ) )
    ),
    DATESINPERIOD(
        'Date'[Date],
        LASTDATE( 'Date'[Date] ),
        -90,
        DAY
    )
)
```

> **Notes:**  
> - Combine with Rolling 30-Day Avg on the same chart to show short vs long-term trends.

---

## 6. Month-over-Month % Change

**Business Use Case:**  
Measure the percentage change in volume or revenue between the current month and the previous month. Common in Group Insurance enrollment and billing dashboards.

```dax
MoM % Change =
VAR CurrentMonth =
    CALCULATE(
        SUM( Transactions[Volume] ),
        DATESMTD( 'Date'[Date] )
    )
VAR PriorMonth =
    CALCULATE(
        SUM( Transactions[Volume] ),
        DATEADD( DATESMTD( 'Date'[Date] ), -1, MONTH )
    )
RETURN
    IF(
        PriorMonth = 0,
        BLANK(),
        DIVIDE( CurrentMonth - PriorMonth, PriorMonth )
    )
```

> **Notes:**  
> - Format as Percentage.  
> - Use conditional formatting on a table to colour positive/negative values green/red.

---

## 7. RANKX — Agent Performance Ranking

**Business Use Case:**  
Rank individual agents by their processed volume, quality score, or efficiency metric. Used in call centre and BPO dashboards to identify top performers and agents needing coaching.

```dax
Agent Rank by Volume =
RANKX(
    ALLSELECTED( Agents[Agent Name] ),
    CALCULATE( SUM( Transactions[Volume] ) ),
    ,
    DESC,
    DENSE
)
```

> **Notes:**  
> - `DENSE` ranking ensures no gaps when agents have equal volume.  
> - Use `ALLSELECTED` (not `ALL`) so the ranking respects page-level slicers.  
> - Add to a table visual alongside Agent Name and Volume for a live leaderboard.

---

## 8. RANKX — Team/Queue Ranking

**Business Use Case:**  
Rank teams or queues by throughput to support operational prioritisation and resource reallocation decisions across departments or client accounts.

```dax
Team Rank by Throughput =
RANKX(
    ALL( Teams[Team Name] ),
    CALCULATE( SUM( Transactions[Volume] ) ),
    ,
    DESC,
    SKIP
)
```

> **Notes:**  
> - `SKIP` is used here (vs DENSE above) to reflect true competitive rank — teams with equal throughput share a rank and the next rank is skipped.  
> - Replace `Teams[Team Name]` with your actual team/queue dimension column.

---

## 9. SLA Compliance Rate

**Business Use Case:**  
Calculate the percentage of transactions or cases completed within the defined SLA window. A critical metric in US Healthcare operations for client contractual reporting.

```dax
SLA Compliance Rate % =
DIVIDE(
    COUNTROWS(
        FILTER(
            Transactions,
            Transactions[TAT Days] <= Transactions[SLA Target Days]
        )
    ),
    COUNTROWS( Transactions )
)
```

> **Notes:**  
> - Format as Percentage.  
> - `TAT Days` = actual turnaround days; `SLA Target Days` = contractual limit per transaction type.  
> - Add a KPI visual with a target line at your SLA threshold (e.g. 95%).

---

## 10. TAT (Turnaround Time) Average

**Business Use Case:**  
Measure the average time taken to process a transaction or resolve a case from receipt to completion. Used in healthcare claims and insurance enrollment dashboards to identify bottlenecks.

```dax
Avg TAT Days =
AVERAGEX(
    Transactions,
    Transactions[Completion Date] - Transactions[Receipt Date]
)
```

> **Notes:**  
> - Ensure both date columns are Date data type (not DateTime) for clean integer output.  
> - For working-days TAT, you'll need a custom NETWORKDAYS calculation using a helper calendar table.

---

## 11. Production Hours vs Target

**Business Use Case:**  
Compare actual productive hours logged by agents or teams against planned targets. Key for workforce management reporting and capacity planning in BPO environments.

```dax
Production vs Target % =
DIVIDE(
    SUM( TimeLog[Production Hours] ),
    SUM( TimeLog[Target Hours] )
)
```

> **Notes:**  
> - Format as Percentage.  
> - A value above 100% indicates over-performance; below indicates a gap to investigate.  
> - Can be segmented by team, agent, date, or activity type using slicers.

---

## 12. Utilization Rate %

**Business Use Case:**  
Show the proportion of available work hours that were spent on productive activities. A core metric in Group Insurance and healthcare BPO for operational efficiency reporting to clients.

```dax
Utilization Rate % =
DIVIDE(
    SUM( TimeLog[Production Hours] ),
    SUM( TimeLog[Available Hours] )
)
```

> **Notes:**  
> - Available Hours = Scheduled Hours − Leave/Absence.  
> - Typical benchmark target is 75–85% depending on the client/process.  
> - Display on a gauge visual with a target line for instant status visibility.

---

## 13. Non-Production Time %

**Business Use Case:**  
Track the share of time spent on non-productive activities (training, meetings, breaks, system downtime). Helps management identify shrinkage and improve scheduling efficiency.

```dax
Non-Production Time % =
DIVIDE(
    SUM( TimeLog[Non-Production Hours] ),
    SUM( TimeLog[Available Hours] )
)
```

> **Notes:**  
> - Non-Production Hours = Available Hours − Production Hours.  
> - Complements [Utilization Rate %] — the two should sum to 100%.  
> - Useful for drill-down by activity category (training, meetings, idle, etc.).

---

## 14. Cases in Pending / WIP Count

**Business Use Case:**  
Count of cases currently in a pending or work-in-progress state. Used in real-time operational dashboards to manage queue backlogs and trigger escalation workflows.

```dax
WIP Case Count =
CALCULATE(
    COUNTROWS( Cases ),
    Cases[Status] IN { "Pending", "In Progress", "On Hold" }
)
```

> **Notes:**  
> - Adjust the status values to match your data model's actual status labels.  
> - Add to a card visual on your operations dashboard with conditional formatting to turn red above a threshold.

---

## 15. Dynamic Period Comparison (MTD / QTD / YTD Switch)

**Business Use Case:**  
Allow end users to switch between MTD, QTD, and YTD views using a slicer without rebuilding the report. Widely used in leadership dashboards where multiple stakeholders have different reporting preferences.

```dax
-- Step 1: Create a disconnected table named "Period Selector"
-- Period Selector = { "MTD", "QTD", "YTD" }

Dynamic Volume =
VAR SelectedPeriod = SELECTEDVALUE( 'Period Selector'[Value], "MTD" )
RETURN
    SWITCH(
        SelectedPeriod,
        "MTD", CALCULATE( SUM( Transactions[Volume] ), DATESMTD( 'Date'[Date] ) ),
        "QTD", CALCULATE( SUM( Transactions[Volume] ), DATESQTD( 'Date'[Date] ) ),
        "YTD", CALCULATE( SUM( Transactions[Volume] ), DATESYTD( 'Date'[Date] ) ),
        BLANK()
    )
```

> **Notes:**  
> - The disconnected "Period Selector" table must NOT be related to any other table in the model.  
> - Add it as a slicer on your report page — selecting MTD/QTD/YTD updates all visuals using this measure.

---

## 16. Cumulative Volume (Running Total)

**Business Use Case:**  
Show a running total of cases or transactions over time. Used in operations and sales dashboards to visually track progress toward monthly or annual targets on a line chart.

```dax
Cumulative Volume =
CALCULATE(
    SUM( Transactions[Volume] ),
    FILTER(
        ALL( 'Date'[Date] ),
        'Date'[Date] <= MAX( 'Date'[Date] )
    )
)
```

> **Notes:**  
> - Always use `ALL( 'Date'[Date] )` inside FILTER to remove the existing date filter context before applying the running total logic.  
> - Plot on a line chart with Date on the X-axis for a clear trend view.

---

## 17. Forecast vs Actual Variance

**Business Use Case:**  
Measure the difference between forecasted and actual volume/revenue. Used in volume forecasting and capacity planning dashboards to assess prediction accuracy and adjust staffing models.

```dax
Forecast vs Actual Variance =
[Actual Volume] - [Forecast Volume]

Forecast Variance % =
DIVIDE(
    [Forecast vs Actual Variance],
    [Forecast Volume]
)
```

> **Notes:**  
> - A positive variance means actual exceeded forecast (over-delivery).  
> - A negative variance means under-delivery — useful to flag for root cause analysis.  
> - Format `Forecast Variance %` as Percentage with 1 decimal place.

---

## 18. Headcount Efficiency Index

**Business Use Case:**  
Measure output (cases processed) per headcount unit. Used in workforce management to compare team efficiency, justify hiring decisions, and benchmark across processes or client accounts.

```dax
Headcount Efficiency Index =
DIVIDE(
    SUM( Transactions[Volume] ),
    SUM( Headcount[FTE Count] )
)
```

> **Notes:**  
> - A higher index = more output per FTE.  
> - Trend this measure over time to track productivity improvements from automation or training.  
> - Segment by team, process, or site using slicers for comparative analysis.

---

## 19. Error Rate %

**Business Use Case:**  
Track the percentage of transactions processed with errors or quality defects. A critical quality metric in US Healthcare operations for audit reporting and Lean/Six Sigma improvement initiatives.

```dax
Error Rate % =
DIVIDE(
    COUNTROWS(
        FILTER( Transactions, Transactions[Has Error] = TRUE() )
    ),
    COUNTROWS( Transactions )
)
```

> **Notes:**  
> - Requires a Boolean column `Has Error` in your Transactions table (TRUE = error found in audit).  
> - Target benchmark is typically < 2% for healthcare transaction processing.  
> - Drill through to a detail table to identify error types and responsible agents.

---

## 20. Selected Period Label (Dynamic Title)

**Business Use Case:**  
Automatically update chart and card titles to reflect the currently selected date period. Improves report clarity and prevents confusion when users apply date slicers — the title always matches the context shown.

```dax
Selected Period Label =
VAR MinDate = FORMAT( MIN( 'Date'[Date] ), "DD MMM YYYY" )
VAR MaxDate = FORMAT( MAX( 'Date'[Date] ), "DD MMM YYYY" )
RETURN
    "Period: " & MinDate & " – " & MaxDate
```

> **Notes:**  
> - Use this measure in a card visual or in the **Title** expression field of any chart visual.  
> - No additional configuration needed — it auto-updates whenever a date slicer is changed.

---

## Model Requirements

For these measures to work correctly, your Power BI model should have:

| Requirement | Detail |
|---|---|
| **Date Table** | A dedicated date table marked as a Date Table in Power BI, with a continuous date range covering all transaction dates |
| **Date Relationships** | All fact tables related to the Date table via a single active relationship on the date column |
| **Consistent Column Naming** | Adjust column references (e.g. `Transactions[Volume]`) to match your actual table and column names |
| **Status Values** | Update status filters (e.g. in WIP Count) to match your data's actual status labels |

---

## How to Use This Library

1. Open your Power BI Desktop file
2. Go to **Modeling → New Measure**
3. Copy and paste the DAX code
4. Rename the measure as needed
5. Adjust table/column references to match your data model
6. Format the measure (%, integer, decimal) in the **Measure Tools** ribbon

---

## Connect

**Bhavesh Hingu**  
Senior Data Analytics & Power BI Specialist  
📧 bhaveshh694@gmail.com  
📍 Mulund, Mumbai  

*Built with 10+ years of hands-on experience in Power BI across US Healthcare and Group Insurance domains.*
