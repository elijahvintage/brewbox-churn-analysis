# BrewBox Churn Analysis

**A recruiter-ready analytics case study | Python, pandas, SQL-ready tables, seaborn**

> **Data note:** BrewBox is a fictional direct-to-consumer coffee-subscription company. The data is synthetic and intentionally messy. The findings below describe this dataset only; they are not claims about a real business.

## Executive summary

BrewBox has **6,000 customers** in the cleaned snapshot dated **2026-09-30**. **2,266 customers (37.8%)** have a recorded cancellation. Using a 180-day observation window to reduce right-censoring, the matured-customer churn rate is **41.8%**.

The clearest commercial signal is acquisition quality: matured churn is **60.6% for Paid Social** versus **22.0% for Referral**. The **FIRST50** promotion also has higher matured churn (**64.6%**) than **WELCOME20** (**38.8%**). Retention drops quickly in early tenure: the risk-set retention curve falls from **75.4% at month 6** to **67.1% at month 12**.

The analysis does **not** treat delivery or ticket relationships as causal. In this synthetic dataset, customers with late deliveries actually show lower churn than customers without a recorded late delivery, and customers with support tickets show lower churn than customers without tickets. Those patterns are likely affected by exposure, tenure, and reverse causality; they should not be used as a basis for cutting service investment without an experiment.

## Business questions answered

### 1. When did churn rise?

Monthly churn peaked at **5.2% in 2026-05**, compared with an average of **3.8% in Q1 2026** and **3.2% in Q3 2026**. Monthly churn is calculated as customers who churned during the month divided by customers active at the start of the month; the first month is excluded because there is no opening customer base.

![Monthly churn rate](images/01_monthly_churn_rate.png)

### 2. Who should BrewBox save first?

The priority audience is **matured Paid Social customers**, especially those acquired with the **FIRST50** offer. Paid Social has a **60.6%** matured churn rate across **1,393** eligible customers, versus **22.0%** for Referral. FIRST50 has **64.6%** matured churn across **794** eligible customers.

![Churn by acquisition channel](images/02_churn_by_channel.png)

![Churn by plan](images/03_churn_by_plan.png)

### 3. What should marketing change?

The data supports **quality-based acquisition testing**, not simply more spend. A transparent scenario is: if Paid Social matured churn fell by **10 percentage points** with better targeting/onboarding, and the retained customers generated six months of their observed average monthly delivered net revenue, the implied six-month value preserved would be approximately **$29,271**. For FIRST50, the same 10-point improvement implies approximately **$15,555**. These are scenario estimates, not forecasts or causal impact estimates.

## Retention view

![Retention by tenure](images/05_churn_by_tenure.png)

The tenure curve uses a proper risk set: at each tenure month, only customers who had reached that month by the snapshot are included in the denominator. This avoids treating a recent active signup as equivalent to a long-observed active subscriber.

## Operational driver checks

A late delivery is defined here as **more than five days** from shipping to delivery. Late delivery occurs on **12.6% of orders** with a recorded delivery time. The observed matured churn rates are **25.8% for customers with at least one late order** versus **70.2% for customers with orders but no late order**. This inverse relationship is a warning against causal interpretation: late delivery may be correlated with different order histories or customer maturity.

Similarly, customers with a support ticket have **26.3%** matured churn versus **41.8%** without a ticket. This likely reflects engagement and selection effects; support contact may be a symptom or a retention opportunity, not proof that tickets reduce churn.

![Operational driver comparisons](images/04_driver_comparisons.png)

## Data preparation

The cleaning pipeline is reproducible in [`src/clean.py`](src/clean.py). It:

- removes **55 duplicate customer rows** and **40 duplicate order rows**;
- parses **720 mixed-format signup dates** explicitly as ISO or day-first dates;
- standardises inconsistent acquisition-channel labels and ticket categories;
- preserves missing ages as missing, labels missing regions as `Unknown`, and labels missing promotion codes as `No promo`;
- corrects 15 negative gross values and 25 implied 10x order-value entry errors;
- checks referential integrity so every order and ticket maps to a customer.

| Cleaned table | Rows | Purpose |
|---|---:|---|
| Customers | 6,000 | Signup, plan, channel, churn and profile fields |
| Orders | 79,899 | Delivered revenue, discount, delivery and order status |
| Support tickets | 3,371 | Category, resolution and customer-service signals |

## Limitations and next steps

This is observational synthetic data. Segment differences are not causal estimates, and right-censoring remains relevant for recent signups. The next production step would be a holdout experiment: test Paid Social audience/offer changes and onboarding interventions, while tracking churn, delivered net revenue, refund rate and service outcomes by cohort.

## Reproduce

```bash
pip install -r requirements.txt
python src/clean.py
python src/analyze.py
```

Analysis outputs are in [`analysis/`](analysis/), charts are in [`images/`](images/), and the data definitions are in [`docs/data_dictionary.md`](docs/data_dictionary.md).

## Skills demonstrated

**Python/pandas data cleaning · cohort and retention analysis · KPI definition · segmentation · operational driver checks · transparent scenario modelling · reproducible documentation · recruiter-friendly data storytelling**

## Author

**Elijah Vintage** · Data analyst focused on subscription and e-commerce retention · [GitHub](https://github.com/elijahvintage)
