# SaaS Customer Churn: EDA, Business Insights & Churn Risk Prediction

**Author:** Gulshan Murtuzzada

## What is this project about?

This project looks at a SaaS (Software-as-a-Service) company: any business that sells access to software through a subscription (e.g. Notion, Netflix, Spotify, or any tool you pay for monthly/yearly). The whole business model depends on customers staying subscribed, so one of the biggest questions in a SaaS company is: **who is likely to cancel (churn), and why?**

That's what this notebook explores: cleaning a messy raw dataset, digging into customer behavior, extracting insights from the data, and building a simple model to predict churn.

## About the dataset

This dataset was generated with AI. Most datasets found online are already clean and ready to plug into a model: not what real data actually looks like. In an actual job, most of the work is figuring out *why* the data is broken before you can say anything meaningful about it. So this dataset was built to be closer to that experience.

It contains inconsistent country names, mixed currencies, percentage signs beside numbers, missing values, duplicates — the works.

The dataset has **1,225 rows** and **15 columns**, each row representing one customer:

| Column | What it means |
|---|---|
| `customer_id` | Unique ID for each customer |
| `company` | Name of the customer's company |
| `industry` | Industry the customer's company operates in |
| `country` | Country the customer is based in |
| `signup_date` | Date the customer signed up |
| `plan_level` | Subscription tier: Free, Basic, Pro, Enterprise |
| `billing_cycle` | Whether they're billed Monthly or Annually |
| `monthly_revenue` | How much the customer pays per month (originally in mixed currencies) |
| `active_days` | Number of days the customer was active on the platform |
| `feature_usage_score` | How much of the product's features the customer actually uses (as a %) |
| `support_tickets` | Number of support tickets (issues) the customer has raised |
| `acquisition_channel` | How the customer found the service (Google Ads, Referral, Organic, etc.) |
| `last_login_days_ago` | Days since the customer last logged in |
| `nps_score` | Net Promoter Score: how likely the customer is to recommend the product |
| `churned` | Target variable: 1 = customer left, 0 = customer stayed |

## EDA Steps

### Cleaning
- Standardized country names (US, USA, U.S.A., and America were all the same country, just written differently)
- Fixed inconsistent casing in `billing_cycle` and `acquisition_channel`
- Stripped out currency symbols and converted everything to USD
- Removed stray "+" signs from numeric columns
- Converted `signup_date` into one consistent date format
- Handled missing values (dropped the single missing `company` row, filled missing `nps_score` values with the median instead of the mean since it's less sensitive to outliers)
- Removed duplicate rows

### Feature engineering and outliers
- Cleaned up `feature_usage_score` into a proper percentage
- Applied a log transformation to revenue and support tickets to tame the skew from extreme values

### Modeling
Built a **Logistic Regression** model to predict churn, landing at **95% accuracy**, with precision, recall, and F1 all around **94–96%**.

## Findings

- **Recency of login is the strongest early-warning signal.** Churned customers hadn't logged in for 36 days on average, versus 17 for retained customers. This lines up with the correlation heatmap too, where `last_login_days_ago` had one of the strongest relationships with churn.
- **Support tickets affect churn.** Customers who churned had raised more than twice as many support tickets on average (6.2 vs 2.9). More friction with the product seems to translate pretty directly into people leaving — it tells us a lot about where the actual risk in the business sits.
- **Engagement matters more than plan level.** Feature usage score was clearly higher among retained customers (0.62 vs 0.32). People who actually use the product stick around. People who don't, leave regardless of what they're paying.
- **Google Ads brings the most customers but not loyalty.** It's the top acquisition channel by customer count, but also has the highest churn rate. Referral and Organic customers churn the least — probably because they arrive with more intent or trust, rather than just clicking an ad.
- **Free and Basic plans dominate the customer base.** This makes sense — they require low commitment and are easy to try. Enterprise customers are the smallest group but generate the highest average revenue per customer, so losing even a few of them matters a lot more than losing a batch of Free users.
- **The US leads by a wide margin in customer count**, followed by the UK and Germany (Europe), which tracks with where most SaaS demand originates.

Put together, the patterns point toward a useful conclusion: churn isn't random — it's foreshadowed by disengagement (login gaps, low feature usage) and friction (support tickets), well before a customer actually cancels. That's the kind of signal that's genuinely useful to catch early, rather than after the fact.

## Recommendations

- **Identify disengaged customers early.** Customers who haven't logged in for several weeks and have low feature usage are at higher risk of churning. These users could be targeted with reminder emails, onboarding sessions, or product tutorials before they decide to leave.
- **Improve the customer support experience.** Churned customers raised more than twice as many support tickets as retained customers. Resolving issues faster and identifying recurring problems could meaningfully impact retention.
- **Look beyond acquisition volume.** Google Ads brings in the largest number of customers, but also the highest churn rate. Rather than optimizing purely for acquisition, it's worth evaluating customer lifetime value (LTV) by acquisition channel and allocating marketing spend toward channels that attract customers who stay longer, such as Referral and Organic.
- **Pay attention to Enterprise customers.** Although they're the smallest segment, they generate the highest average revenue per customer. Losing even a small number of them can have an outsized financial impact, making personalized support more valuable.
- **Build an early warning system.** The Logistic Regression model shows that churn can be predicted with high accuracy. In a real SaaS business, these signals could be monitored continuously to flag at-risk customers before they cancel.

## Tools used

Python, pandas, numpy, matplotlib, seaborn, scikit-learn (Logistic Regression, StandardScaler, OneHotEncoder, ColumnTransformer)
