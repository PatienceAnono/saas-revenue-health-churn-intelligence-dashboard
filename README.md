# SaaS Revenue Health & Churn Intelligence Dashboard

**MRR grew from $1,832 in January 2022 to $416,815 in December 2024. Enterprise customers are 28% of the customer base but generate 65% of revenue. Product usage drops 79% in the weeks before a customer cancels. This project finds, quantifies and visualises all of it.**

---

## What this project is about

I built this project around a question that every subscription business eventually has to answer: is the revenue we are seeing actually healthy, or are we papering over churn with new customer acquisition?

To answer it properly, you need to look at more than total revenue. You need to know where MRR is coming from (new customers, upgrades, reactivations) and where it is leaking (cancellations, downgrades). You need to know which customers are genuinely engaged with the product and which ones are paying but barely logging in. You need to know which marketing channels are producing customers who actually stay versus customers who try the product and leave.

This notebook does all of that in one place, from six raw CSV files through to a set of Looker Studio-ready exports.

---

## The dataset

Six CSV files, 86,402 rows total, covering January 2022 to November 2024.

| File | Rows | What it contains |
|:---|:---|:---|
| `customers.csv` | 10,000 | Company name, country, industry, signup date |
| `marketing.csv` | 10,000 | Acquisition channel, campaign, CAC per customer |
| `subscriptions.csv` | 13,725 | Full subscription history — every plan change, upgrade and cancellation |
| `payments.csv` | 25,703 | Monthly payment records with plan and amount |
| `product_usage.csv` | 15,984 | Weekly sessions, active users, API calls per customer |
| `support_tickets.csv` | 10,990 | Tickets with priority, resolution time and CSAT scores |

The subscriptions table has more rows than customers because each plan change creates a new record. A customer who started on Starter, upgraded to Pro and eventually cancelled appears three times. That structure is what makes the MRR waterfall possible — every revenue movement has a traceable event behind it.

---

## What I found — the actual numbers

### Revenue

MRR started at $1,832 in January 2022 and reached $416,815 by December 2024 — a 228× increase over roughly three years. The ARR run rate at the end of the period was $5,001,780. Active customers grew to 1,865 with an average revenue per user of $223.49.

### The plan split is dramatic

| Plan | Active Customers | MRR | Revenue Share |
|:---|:---|:---|:---|
| Starter | 325 | $9,425 | 2.3% |
| Pro | 441 | $34,839 | 8.7% |
| Business | 489 | $97,311 | 24.2% |
| Enterprise | 523 | $260,977 | **64.8%** |

Enterprise customers were 28% of the active base but generated almost two thirds of MRR. This concentration matters for how you think about churn — losing one Enterprise account at $499 per month costs the same as losing 17 Starter customers at $29. The churn numbers confirmed this concern: 485 Starter customers churned over the period versus 75 Enterprise, but the revenue exposure per Enterprise churn event was nearly 7× higher.

### MRR waterfall — where revenue is leaking

The waterfall broke down all MRR movement outside of recurring payments:

| Component | Total (all time) |
|:---|:---|
| Contraction MRR (downgrades) | $131,788 |
| Churned MRR (cancellations) | $115,124 |
| Expansion MRR (upgrades) | $63,795 |
| Reactivation MRR | $3,304 |

Contraction MRR exceeded churned MRR — more revenue was lost to downgrades than to full cancellations. This was the most unexpected finding in the revenue analysis. It meant the business had a plan retention problem (customers moving to cheaper plans) that was costing more than the customer retention problem (customers leaving entirely).

### Retention — the first 90 days are the problem

Cohort retention averaged:
- Month 1: **91.6%** still paying
- Month 3: **81.3%** still paying
- Month 6: **72.0%** still paying
- Month 12: **60.2%** still paying

The steepest drop was between month 1 and month 3. Customers who were going to leave almost always left early. After month 6, the curve flattened considerably — customers who reached the six-month mark tended to stay. The implication was clear: if onboarding investment had to be prioritised, the first 90 days were worth more than any other period.

### Acquisition channels — not all customers are equal

| Channel | Avg CAC | Avg LTV | LTV:CAC |
|:---|:---|:---|:---|
| Email | $90 | $1,409 | **15.6×** |
| Referral | $115 | $1,505 | **13.1×** |
| Organic | $150 | $1,559 | 10.4× |
| Direct | $149 | $1,518 | 10.2× |
| LinkedIn | $622 | $3,256 | 5.2× |
| Google Ads | $320 | $1,503 | 4.7× |
| Meta Ads | $250 | $925 | **3.7×** |

Email and Referral had the best ratios by a wide margin. Meta Ads had the worst — customers from Meta paid $250 to acquire but generated only $925 in lifetime revenue, a 3.7× ratio that was barely above the 3× threshold generally considered healthy.

LinkedIn looked expensive on paper ($622 CAC) but produced the highest average LTV at $3,256 — because LinkedIn customers skewed toward Business and Enterprise plans. A decision to cut LinkedIn based on CAC alone would have been the wrong call.

### Product usage — the 79% drop before cancellation

Normal customers averaged 95.2 sessions per week. Customers in the 4–8 weeks before cancellation averaged 20.3 sessions — a 79% drop. This was the clearest early warning signal in the entire dataset.

The drop appeared before the cancellation decision, not as a result of it. That meant a customer success team monitoring weekly usage had a window to intervene. By the time a customer submitted a cancellation, the signal had already been visible for weeks.

### Support — CSAT scores drop 1.31 points before churn

Overall CSAT averaged 3.23/5. Customers who were about to cancel averaged 2.44/5 — a 1.31-point gap compared to the 3.74/5 average for customers who stayed. This was the second-strongest churn signal after usage decline, and it fired earlier. CSAT drops showed up before usage metrics deteriorated, making it the most actionable leading indicator.

### Revenue at risk right now

The customer health model — combining usage decline, CSAT trend and support ticket volume — scored all 1,778 active customers:

| Risk Category | Customers | MRR at Risk |
|:---|:---|:---|
| High Risk | 131 | $21,489 |
| Medium Risk | 332 | $92,598 |
| Healthy | 1,315 | $288,465 |

$114,087 in MRR across 463 accounts was showing warning signals. None of those customers had cancelled yet — the signals appeared before cancellation. The top accounts at risk were medium-risk Enterprise customers, each carrying $499 in monthly MRR.

---

## How the notebook is structured

12 sections, each one answering a specific business question. Every code cell is followed by a plain-language explanation of what the output showed and what it meant for the business — written for someone who reads the findings, not the code.

| Section | Question it answers |
|:---|:---|
| 1. Setup & Loading | Did all six files load correctly? |
| 2. Data Quality Check | Is the data complete and trustworthy? |
| 3. Revenue Analysis | How much are we making and is it growing? |
| 4. MRR Waterfall | Where is revenue coming from and where is it leaking? |
| 5. Customer Analysis | How many customers do we have and how many are we losing? |
| 6. Cohort Retention | Which customers stay the longest? |
| 7. Acquisition Channels | Which channels bring the most valuable customers? |
| 8. Product Usage | Are customers actually using what they pay for? |
| 9. Support Analysis | Are support patterns signalling churn risk? |
| 10. Revenue at Risk | Which active accounts need attention before they cancel? |
| 11. Key Findings | Plain-language summary for the leadership team |
| 12. Looker Studio Export | Seven CSV files for dashboard visualisation |

---

## Charts produced

| File | What it shows |
|:---|:---|
| `01_mrr_trend.png` | MRR growth and active customer count, 2022–2024 |
| `02_mrr_waterfall.png` | Monthly MRR components stacked bar chart |
| `03_customer_analysis.png` | New signups per month, churn by plan, revenue share by plan |
| `04_cohort_retention.png` | Cohort heatmap and average retention curve |
| `05_channel_analysis.png` | LTV:CAC ratio by channel and CAC vs LTV comparison |
| `06_product_usage.png` | Usage by plan and pre-churn usage drop comparison |
| `07_support_analysis.png` | CSAT by plan, normal vs pre-churn CSAT, monthly trend |
| `08_revenue_at_risk.png` | MRR at risk by health category and health score distribution |

---

## Looker Studio exports

The final section saves seven pre-aggregated CSV files to a `looker_export/` folder:

| File | Rows | Dashboard page |
|:---|:---|:---|
| `monthly_metrics.csv` | 36 | Executive overview — MRR trend, ARR, growth |
| `customer_health.csv` | 1,778 | Revenue at risk — health scores per customer |
| `channel_performance.csv` | 7 | Acquisition — LTV:CAC by channel |
| `cohort_retention.csv` | 513 | Retention — cohort heatmap |
| `plan_revenue.csv` | 143 | Plan analytics — MRR by plan over time |
| `support_summary.csv` | 141 | Support — CSAT and ticket trends |
| `mrr_at_risk.csv` | 3 | Risk summary — MRR at risk by category |

To connect to Looker Studio: upload each file to a separate tab in one Google Sheet, share the sheet publicly, then in Looker Studio go to Add Data → Google Sheets and select the spreadsheet. Each tab feeds its corresponding dashboard page.

---

## How to run it

**1. Clone the repository**

```bash
git clone https://github.com/PatienceAnono/saas-bi-dashboard.git
cd saas-bi-dashboard
```

**2. Install dependencies**

```bash
pip install pandas numpy matplotlib seaborn faker
```

**3. Generate the data** (if you do not already have the CSV files)

```bash
python generate_saas_data.py
```

**4. Open the notebook**

```bash
jupyter notebook SaaS_Dashboard_EDA_v2.ipynb
```

**5. Update the file path**

In Section 1, update `DATA_PATH` to match where your CSV files are saved:

```python
DATA_PATH = 'C:/Users/YourName/path/to/your/data/'
```

Use forward slashes — Python on Windows handles them correctly.

**6. Run all cells** — top to bottom. The `looker_export/` folder is created automatically when Section 12 runs.

---

## Project structure

```
saas-bi-dashboard/
│
├── SaaS_Dashboard_EDA_v2.ipynb     # Main analysis notebook
├── generate_saas_data.py           # Data generation script
├── phase2_sql_analytics.py         # SQL views and business queries
├── saas_analytical_views.sql       # All 10 SQL views for MySQL/SQLite
│
├── data/
│   └── raw/
│       ├── customers.csv
│       ├── marketing.csv
│       ├── subscriptions.csv
│       ├── payments.csv
│       ├── product_usage.csv
│       └── support_tickets.csv
│
├── looker_export/                  # Generated by notebook Section 12
│   ├── monthly_metrics.csv
│   ├── customer_health.csv
│   ├── channel_performance.csv
│   ├── cohort_retention.csv
│   ├── plan_revenue.csv
│   ├── support_summary.csv
│   └── mrr_at_risk.csv
│
└── README.md
```

---

## Key definitions

**MRR** (Monthly Recurring Revenue) — the total amount customers pay each month. This is the heartbeat metric for any subscription business.

**ARR** (Annual Recurring Revenue) — MRR multiplied by 12. Shows the business's revenue at an annualised rate.

**ARPU** (Average Revenue Per User) — total MRR divided by active customers. Going up means you are moving customers toward higher-value plans.

**Churn** — a customer cancelling their subscription. Measured as a percentage of active customers per month.

**LTV** (Lifetime Value) — the total revenue a customer generates over their entire relationship with the business.

**CAC** (Customer Acquisition Cost) — how much was spent in marketing to acquire each customer.

**LTV:CAC ratio** — how many dollars you get back for every dollar you spend to acquire a customer. 3× is generally considered the minimum healthy benchmark.

**Cohort** — a group of customers who signed up in the same month. Cohort analysis tracks how many of them are still paying 1, 3, 6 and 12 months later.

**CSAT** — Customer Satisfaction score, rated 1 to 5 on support tickets.

**Health score** — a composite 0–100 score built from usage trend (40 points), CSAT (30 points), support ticket volume (20 points) and recent activity (10 points). Lower scores mean higher churn risk.


---

**Patience Anono** · PA Data Analytics · [padataanalytics.com](https://padataanalytics.com) · hello@padataanalytics.com
