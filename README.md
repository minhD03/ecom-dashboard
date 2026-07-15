# Ecom Dashboard: [Live Report](https://app.fabric.microsoft.com/view?r=eyJrIjoiM2VlNTA3NWQtNzlhZS00NjkxLTg5OTYtMmNmOTNhZjVmYTc4IiwidCI6IjZhNjhlMmQxLWQ4OGQtNDEyYi1iOTgyLWQ0YWVkNWY1MTcxNiJ9)

## Table of Contents

- [1) Introduction](#1-introduction)
- [2) Data Model](#2-data-model)
- [3) Dashboard Pages](#3-dashboard-pages)
- [4) Key DAX & Analytical Techniques](#4-key-dax--analytical-techniques)
- [5) Insights & Recommendations](#5-insights--recommendations)
- [6) Conclusions](#6-conclusions)

## 1) Introduction: 
This dashboard analyzes an e-commerce platform's sales, delivery, and customer
satisfaction data across three focused pages, each built around a specific
business question rather than a generic table-by-table breakdown. It sits on top
of a dimensional model produced by an upstream data engineering pipeline (Dagster →
S3 → Spark → Snowflake → dbt — see the [Main project](https://github.com/minhD03/ecom-project.git) for that layer), and
connects live to the resulting Snowflake dimensional model.

The analysis goes beyond descriptive reporting: several measures compute statistical
relationships (e.g. delay-to-satisfaction correlation), weighted efficiency metrics
(freight cost per kg, not just raw averages), and revenue-concentration analysis
(Pareto-style seller dependency), rather than only aggregating totals.

Furthermore, this dashboard was built to answer specific operational and strategic questions,
not just to visualize tables. Each question below maps directly to a page and set
of measures described in Section 4:

1. **Is delivery performance actually hurting customer satisfaction, and if so, by
   how much?** (Page 2) — Answered quantitatively via a computed correlation
   coefficient between delivery delay and review score, not just a visual
   impression from two side-by-side bars.

2. **Which specific sellers or regions are responsible for the worst delivery
   times and lowest review scores — not just which state looks bad on average?**
   (Page 2, Page 3) — Answered at the individual seller level with volume-aware
   filtering, so a single seller's bad luck on one order doesn't get confused with
   a genuine, systemic performance problem.

3. **Is the business financially dependent on a small number of top sellers, and
   how concentrated is that risk?** (Page 3) — Answered via a Pareto-style
   cumulative revenue share analysis.

4. **Are there product categories that generate strong revenue but are quietly
   underperforming on customer satisfaction?** (Page 3) — Answered via a combined
   revenue-and-quality index, surfacing categories that would look healthy on a
   revenue-only view but are a hidden risk.


## 2) Data Model: 
![alt text](https://github.com/minhD03/ecom-dashboard/blob/06a5510854000a8e79968de8bd88a8458560efc6/images/data-model.jpg)

## 3) Dashboard Pages:
![alt text](https://github.com/minhD03/ecom-dashboard/blob/06a5510854000a8e79968de8bd88a8458560efc6/images/dashboard-1.jpg)
![alt text](https://github.com/minhD03/ecom-dashboard/blob/06a5510854000a8e79968de8bd88a8458560efc6/images/dashboard-2.jpg)
![alt text](https://github.com/minhD03/ecom-dashboard/blob/06a5510854000a8e79968de8bd88a8458560efc6/images/dashboard-3.jpg)

## 4) Key DAX & Analytical Techniques:

A few measures in this dashboard go beyond simple totals and averages to answer
deeper business questions:

**Delivery Delay vs. Satisfaction**: instead of just eyeballing two bars side by
side, this dashboard calculates a statistical correlation between how late a
delivery is and how customers rate their experience, giving a defensible, numeric
answer to "does slow shipping actually hurt satisfaction?"

**Seller Revenue Concentration**: ranks sellers by revenue and shows what share of
total sales comes from the top performers, making it possible to see at a glance
how dependent the business is on a small group of sellers versus a broadly
distributed base.

**Freight Efficiency by Weight**: shipping cost is measured per kilogram rather
than as a flat average per order, so a heavy item and a light item aren't treated
as equally expensive to ship, giving a more honest picture of where freight costs
are actually inflated.

**Volume-Aware Quality Rankings**: "worst performer" lists for sellers and
categories only include those with a meaningful number of reviews, so a single bad
review on a brand-new seller doesn't unfairly outrank a seller with hundreds of
mostly positive reviews.

**Seller Quality Segmentation**: every seller is grouped into a quality tier (Poor
through Excellent, or Not Yet Reviewed) based on their average rating, making it
possible to see the overall health of the seller base at a glance instead of only
a ranked list of outliers.

## 5) Insights & Recommendations:

### a) Sales and Revenue Performance:
- With nearly 13.6 millions from 99 thousands orders, the customers focused mostly from South America (mainly Brazil).
- While the values for each order went peak at nearly 300, the price had recently stabled at around 150, an affordable price for most of the customers.
- Howerver, this also results in the reduce of total revenue from over 100k to nearly 50k.
- The revenue mostly came from health and beauty, waches and gifts, bed bath table, sports and computers.
- Surprisingly, the payments came mostly from credit cards, followed by boleto.
- These payments were mostly paid immediately but there were still a portion of payment installments spanned from 2 to 12 months with some even over 12+ months.
  
### b) Customer and Delivery Experience:
- Over 96k customers around South America, there were only 3k customers repeatedly purchasing items, indicating a low reliability from customers for this platform.
- With 95% on time delivery rate, it seems that the Late or Not Yet Delivered items were received with extremely low score than on time items. This shows that customers were also take delivery process into consideration equally to item quality.
- While the on time delivery were always sonner around 2 weeks or more, the late delivers were even reach a peak at more than 5 months before dropped down to 2 weeks.
- Moreover, with nearly 40k on time deliveries, there were also a huge concentration of Late or Not Yet Delivered around Sao Paolo, followed by Rio Grande do Sul.
- With the processing period, there had been a large improvements in processing days, from 50 days to 1 day.

## c) Product and Seller Performance:
- When taking into consideration with revenue and quality together, the difference between top categories and the later one were significantly observable. While the top categories like Heath and Beauty, Watches and Gifts remains from the top, Computers and Sports drops nearly half from the tops.
- Noticably, on average reviews, books, cds and tools are on the higher spots with over 4.5. Only the sports left on the top list while leaving other above categories behind.
- Moving to seller reviews, while most of them spans from 3 to 5 with over 2000 sellers from 4 above.
- However, only when increasing the number of reviews from above 500, there was only 20 sellers were actually reliable with above 3.
- In terms of freight cost, while American Samoa shows the most exorbitant cost (77$/kg), huge states like Sao Paolo or Rio Grande do Sul were affordable with only around 10$/kg.
- While the technologies like telephone, dvds and console games had high freight cost (from 40$ to 60$), there were also a huge numbers of items in fashion categories appeared on this list.
- The top 10 sellers each contribute a similar, modest share of revenue (no single dominant seller), with cumulative share only reaching ~15% across this group, suggesting revenue is relatively distributed rather than concentrated in a handful of top accounts.

## d) Recommendations:
- Investigate root causes of extreme delivery delays (some exceeding 5 months) concentrated in São Paulo and Rio Grande do Sul, even though these are high-volume states, their delay outliers are dragging down satisfaction disproportionately; prioritize a carrier/logistics audit in these two regions specifically.
- Since late and not-yet-delivered orders show materially lower review scores than on-time orders, treat delivery reliability as a satisfaction lever equal to product quality, set an internal SLA (e.g. "no order should exceed X weeks") and alert on outliers before they reach the customer.
- Capitalize on and protect the processing-time improvement (50 days → 1 day) by documenting what changed operationally, so the gain is sustained rather than reverting.
- With only 3K repeat customers out of 96K total, prioritize a retention/loyalty program, even a modest improvement in repeat rate would have an outsized revenue impact given the current base is overwhelmingly one-time buyers.
- Investigate whether the revenue decline from 100K to 50K per period correlates with the average order value stabilizing around $150 (down from a $300 peak), determine if this is intentional (broader affordability strategy) or a symptom of reduced basket size that needs addressing via upsell/bundling.
- Double down on categories that perform well on both revenue and quality, Health & Beauty and Watches & Gifts, as the safest areas for continued investment or marketing spend.
- Flag Computers and Sports for review: strong revenue but a significant drop in relative standing once quality is factored in signals a category-level satisfaction issue worth investigating (product quality, seller performance, or delivery-related, given the delivery findings above).
- Recognize Books, CDs, and Tools as under-leveraged strengths, high review scores (4.5+) but not top-line revenue drivers; consider increased visibility, marketing, or cross-sell placement for these categories to convert existing satisfaction into more sales.
- Treat the "20 reliable sellers with 500+ reviews and 3+ average score" group as your platform's benchmark tier, study what they do differently (fulfillment practices, communication, etc.) and use it as a model for onboarding/training other sellers.
- Address the extreme freight cost in American Samoa ($77/kg vs. $10/kg in São Paulo and Rio Grande do Sul), this is a significant outlier worth a dedicated logistics review, whether that means renegotiating carrier rates for that region or reconsidering fulfillment strategy there entirely.
- Review pricing/shipping strategy for high-freight categories (telephones, DVDs, console games, and a notable share of fashion items at $40-60/kg), consider whether shipping costs are being adequately reflected in pricing or absorbed as margin loss, and whether packaging/consolidation improvements could reduce cost for these specific categories.
- The revenue base is healthily distributed (top 10 sellers only 15% of cumulative revenue), this is a genuine strength, not a gap needing correction. Recommend maintaining current seller-diversification practices and monitoring this metric over time to catch any future drift toward concentration risk early.

## 6) Conclusions:
This analysis shows a platform with real underlying strengths, an affordable, stabilized price point, strong performance in several core categories, a healthily distributed seller base with no dangerous revenue concentration, and a dramatically improved order-processing time but also several concrete, addressable weaknesses that are directly limiting growth and satisfaction. The most urgent of these is delivery reliability: late and undelivered orders are clearly and measurably driving down customer satisfaction, concentrated in specific high-volume states, making logistics investment in those regions the single highest-leverage recommendation from this analysis. Close behind is customer retention, where a 96K-customer base with only 3K repeat buyers represents significant untapped revenue potential relative to the cost of acquiring each new customer. Addressing these two areas, delivery reliability and retention while continuing to reinforce already-strong categories and reliable sellers, offers the clearest path to converting this platform's structural strengths into sustained revenue growth.
