# Hospital Capacity Planner

An interactive tool that answers a common hospital planning question: **how many beds will this hospital need in 10 years, and does it need to build more?**

It validates monthly inpatient volume data, forecasts bed need under different scenarios, and turns the result into a plain English executive summary.

**Live demo:** (https://kritisingh96.github.io/hospital-capacity-planner/)

![Overview](screenshots/1_Overview_Efficiency_First.png)

> All data is synthetic: a fictional 240 bed community hospital. No real patient or client data is used.

## What it does

### 1. Data quality check
Reads 36 months of volumes (discharges, patient days, staffed beds) and flags common problems in real hospital data:

| Check | Why it matters |
|---|---|
| Duplicate months | Prevents double counting |
| Missing months | Baseline is scaled to a full year |
| Zero or negative values | Often a blank cell exported as zero |
| Average length of stay outside 2.5 to 8 days | Catches keying errors, such as an extra digit |
| Occupancy above 100% | Impossible values that point to bad source data |

Flagged rows can be excluded from the baseline with one click, showing how a single bad number can distort a capital decision.

![Data check](screenshots/2_Data_Quality_Check.png)

### 2. Scenario forecast
Six assumptions drive a 10 year forecast: population growth, use rate change, market share change, shift to outpatient care, length of stay, and target occupancy. Four presets compare common planning stories:

| Scenario | Beds needed in 2035 | vs 240 beds today |
|---|---|---|
| Base case | 266 | 26 short |
| Efficiency first | 230 | 10 to spare |
| High growth | 339 | 99 short |
| Outpatient shift | 233 | 7 to spare |

The key insight: operational improvements such as shorter stays can remove most of the apparent need for new beds.

![Forecast](screenshots/3_Bed_Need_Forecast.png)

### 3. Executive summary
Generates a short summary that leads with the answer, explains the drivers, notes data issues and suggests next steps.

![Summary](screenshots/4_Executive_Summary.png)

## Method

```
Discharges(t)  = baseline × (1 + population growth)^t × (1 + use rate change)^t
                 × (1 + market share change × t/10) × (1 − outpatient shift × t/10)
ALOS(t)        = moves linearly from today's value to the year 10 target
Patient days   = discharges × ALOS
Average daily census = patient days ÷ 365
Beds needed    = census ÷ target occupancy
```

The baseline is the most recent 12 months of clean data, annualized if a month is missing or excluded.

## Run it

No build step or install. Open `index.html` in a browser.

To try your own data, click **Upload CSV** and use the format in `sample_data.csv`:

```
month,discharges,patient_days,staffed_beds
2025-01,1350,6210,240
```

## Limitations

* Forecasts the whole hospital, not individual units (ICU, med surg, obstetrics)
* Uses annual averages rather than peak season demand
* Does not model population by age group
* No financial layer yet (construction cost, revenue, debt capacity)

## Built with

HTML, CSS and vanilla JavaScript, with charts drawn in SVG. No external libraries.

## Author

Kriti Singh · [LinkedIn](https://linkedin.com/in/kriti-singh)
