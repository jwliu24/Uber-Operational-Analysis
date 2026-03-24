# Uber Operational Analysis Project
> A data-driven deep dive into Uber's operational performance, focusing on supply reliability (VTAT) and cancellation trends. Includes a 30-day demand forecast and a vehicle reliability scorecard built with Python and SQL.

## Project Overview
* **Objective:** To identify bottlenecks in ride completion rates and vehicle turnaround times (VTAT) to improve platform reliability.
* **Tech Stack:** Python (Pandas, Matplotlib, Seaborn, SciPy), SQL
* **Environment:** Jupyter Notebook / Google Colab
* **Focus Areas:** Feature Engineering, Exploratory Data Analysis (EDA), Statistical Hypothesis Testing, Time-Series Forecasting

---

## Week 1: Data Cleaning & The "Supply Wall"
The project began by processing 150,000 rows of raw ride data to establish baseline reliability metrics and engineer foundational time-based features (Peak vs. Off-Peak).

### Key Insights
* **The Reliability Gap:** Go Sedan has the lowest reliability, with a 7.22% "No Driver Found" rate.
* **Time Trend:** Supply shortages peak at 1:00 AM, where failure rates jump to nearly 8%.

<img width="1015" height="549" alt="week 1" src="https://github.com/user-attachments/assets/6644c0b6-3eb0-4d68-ade2-f53bbc3aebb0" />

*Above: A visualization of platform supply reliability across a 24-hour period. The data reveals a clear "supply wall" during late-night hours (1:00 AM - 2:00 AM) where driver availability drops significantly, resulting in an 8%+ failure rate to match a rider with a driver.*

---

## Week 2: Feature Engineering & Customer Friction Analysis
The focus shifted from data exploration to Feature Engineering. By creating binary flags and behavioral "buckets," I was able to quantify exactly why and when the business loses revenue.

### Key Technical Accomplishments
* **Binary Flagging:** Developed a suite of boolean flags (`is_successful`, `is_cancelled_customer`, `is_no_driver`) to allow for rapid calculation of success and failure rates.
* **Behavioral Bucketing:** Created the `Wait_Time_Bucket` feature using Avg VTAT (Vehicle Travel Arrival Time) to categorize driver response speeds.
* **Data Pipeline Pipeline:** Implemented robust data handling using `.copy()` to ensure a clean separation between raw data and engineered features.

### Top Insight: The 15-Minute "Revenue Cliff"
By correlating wait times with customer cancellations, I discovered a definitive threshold for customer patience.
* **Patience Zone (0-15 mins):** Cancellation rates remain stable and low (under 10%).
* **The Breaking Point (15+ mins):** The customer cancellation rate spikes to 100%.


<img width="969" height="573" alt="week 2" src="https://github.com/user-attachments/assets/3948f0ce-43a0-4170-bdb8-e9cbc5942b00" />

*Above: An analysis of customer cancellation rates bucketed by estimated driver arrival times. The data reveals a stark "revenue cliff" at the 15-minute mark, where customer patience entirely runs out and the cancellation rate skyrockets from under 8% to exactly 100%.*

> **Strategic Recommendation:** This data proves that any booking assigned to a driver more than 15 minutes away is a guaranteed revenue loss. Operational efforts should focus on keeping "Go Sedan" arrival times under this 15-minute threshold to prevent total churn.

---

## Week 3: Exploratory Data Analysis (EDA) & Business Impact
In Week 3, the focus moved to visual storytelling and uncovering the "why" behind the revenue loss. By analyzing cancellation patterns across vehicle types, I identified a major operational bottleneck.

### Key Insights
* **The $1.15M 'Auto' Leakage:** Contrary to assumptions that premium vehicles drive the most financial loss, the high-frequency Auto segment was identified as the platform's largest absolute revenue leak, totaling over $1,150,000 in unrealized bookings.
* **Pinpointing the Root Cause:** A behavioral deep dive revealed a distinct UI/GPS friction point. The leading reason for customer cancellations was "Wrong address," which directly correlated with the top driver cancellation reason: "Customer related issue."


<img width="1568" height="689" alt="week 3" src="https://github.com/user-attachments/assets/dcefb3ab-c5fb-47b2-9ddb-e5ec3ba5bf9f" />

*Above: A financial breakdown of unrealized revenue across vehicle categories due to cancelled or failed rides. The data reveals that the high-frequency "Auto" segment represents the platform's most severe financial leak, accounting for over $1.15 million in lost booking value.*


> **Strategic Recommendation:** The data proves this is an interface problem, not a driver supply problem. Implementing a "Confirm Exact Pin" prompt or a brief address-edit window for Auto bookings could directly recover a significant portion of this lost revenue.

---

## Week 4: Statistical Validation & Hypothesis Testing
The final phase shifted from visual exploration to rigorous mathematical validation. By applying statistical hypothesis testing using Python's SciPy library, I was able to prove whether the operational bottlenecks identified during EDA were systemic platform issues or just random variance.

### Key Insights
* **The 'Auto' Leakage Reality Check (Chi-Square Test):** While EDA highlighted the Auto segment as the largest absolute revenue leak, a Chi-Square test of independence (p = 0.89) revealed no statistically significant relationship between vehicle type and cancellation rates. This mathematically proves that the "Wrong Address" UI friction is a platform-wide issue affecting all rides equally, rather than a problem isolated to a specific vehicle tier.
* **Validating Peak Revenue Drivers (Two-Sample T-Test):** A Two-Sample T-Test was deployed to compare average booking values between rush hour and off-peak times. This mathematically validated that time-of-day operations significantly drive up average ticket sizes, confirming that temporal features are highly reliable indicators for future predictive modeling.


### 1. Chi-Square Test: Testing the "Day of the Week" Factor
<img width="470" height="186" alt="week 4 chi-test" src="https://github.com/user-attachments/assets/2a0ee554-ff2f-49c6-b5a3-6cc4250e0f9e" />

*Above: Execution of a Chi-Square Test of Independence using Python's SciPy library. With a resulting p-value of 0.402 (well above the 0.05 threshold), this test mathematically proves that the overall proportion of cancelled rides is practically identical regardless of whether the booking occurs on a weekday or a weekend.*



### 2. Two-Sample T-Test: Validating the "Wait Time" Factor
<img width="509" height="157" alt="week 4 t-test" src="https://github.com/user-attachments/assets/395fd1b6-6212-430a-ac9e-95cf76586eff" />

*Above: Execution of an independent Two-Sample T-Test comparing the average wait time (VTAT) of completed rides versus cancelled rides. The resulting p-value of 0.0 mathematically proves that customers who cancel experience a statistically significant longer wait time, establishing the foundational metric for our predictive ML model in Week 5.*


> **Strategic Recommendation:** Although the statistical data proves the UI/GPS bug affects all vehicle types equally, product and engineering teams must still prioritize deploying the interface fix to the Auto segment first. Because Auto commands the highest booking volume, targeting this segment will yield the highest absolute dollar recovery and maximize the immediate ROI of the engineering effort.

---

## Week 5: Predictive Modeling & Machine Learning
The final phase of the project transitioned from statistical validation to predictive modeling. I built a machine learning pipeline designed to predict customer-initiated cancellations before they happen, allowing the business to shift from reactive analysis to proactive retention.

### Key Technical Accomplishments
* **Tackling Severe Class Imbalance:** Customer cancellations represent only ~7% of all rides. I applied `class_weight='balanced'` to the models to ensure they correctly identified the minority class instead of defaulting to "Success" predictions.
* **Optimizing for Business Value:** Rather than focusing on overall accuracy, I prioritized **Recall** (66.9% for Logistic Regression). In this context, a False Negative (missing a cancellation) costs the company a full fare, making high Recall the most critical metric for revenue recovery.
* **Feature Integration:** Engineered and deployed 39 features, including categorical location flags, time-of-day buckets, and temporal pressure signals.


<img width="1127" height="884" alt="week 5 step 6" src="https://github.com/user-attachments/assets/63e9e856-c50e-49b2-954d-db9a37a446be" />

*Above: A heatmap of customer cancellation rates by hour and day. While the baseline is ~7%, the data reveals "hot zones" during weekday rush hours (e.g., Friday at 23:00 hitting 10%), pinpointing the temporal volatility of customer friction.*

<br>

<img width="984" height="384" alt="week 5 step 11" src="https://github.com/user-attachments/assets/4309033a-245e-440e-910c-5f01d03cdce1" />

*Above: A comparative analysis of Logistic Regression versus Decision Tree performance. By prioritizing Recall over Accuracy, we ensured the model is tuned to identify the maximum number of potential cancellations.*

<br>

<img width="984" height="684" alt="week 5 step 12" src="https://github.com/user-attachments/assets/964e3ebc-9b05-455a-9ed2-b95e79953520" />

*Above: The "Smoking Gun" — Feature Importance analysis. The model reveals that **Avg VTAT** (Wait Time) accounted for **99.65%** of predictive power, mathematically proving that wait time is the singular driver of customer churn, regardless of location or vehicle tier.*


> **Strategic Recommendation:** The machine learning pipeline identifies a critical "Patience Threshold" for the platform. To minimize revenue leakage, the engineering team should operationalize these results through an **Automated Retention Trigger**: 
> * When a live booking's estimated VTAT crosses the identified high-risk threshold (15+ mins), the app should automatically trigger a loyalty-point incentive or a small discount. 
> * This proactive intervention targets the exact 0.35% of variance not explained by wait time, incentivizing the customer to wait and recovering what would otherwise be a guaranteed cancellation.

---

## Week 6: Product Analytics & Operations Performance
The final phase moved beyond predictive modeling into deep product analytics. By constructing conversion funnels, tracking cohort retention, and analyzing revenue distribution, I transitioned the focus from identifying what happened to quantifying the exact business impact and prioritizing engineering efforts.

### Key Insights
* **The 62% Completion Bottleneck:** Funnel analysis revealed that for every 100 rides requested, only 62 are successfully completed. 
* **The 'Smoking Gun' of Driver Churn:** Driver-initiated cancellations account for a massive 18% drop-off in the conversion funnel, causing nearly 3x more operational damage to the platform than customer-initiated cancellations.
* **The Pricing Paradox:** Average Revenue Per Ride (ARPR) remains completely flat (~₹508) across all vehicle types. The data proves there is currently no effective pricing premiumization for higher-tier vehicles like the Premier Sedan or Uber XL.
* **Platform Loyalty Pulse:** While the overall market is heavily dominated by one-time bookings, Daily Active User (DAU) tracking and cohort analysis revealed a steady, recurring pulse of ~411 active users, indicating a mature, steady-state demand rather than a hyper-growth phase.

<img width="1550" height="614" alt="week 6 conversion" src="https://github.com/user-attachments/assets/3ceadfb0-e7e9-411b-ad65-41304697eee0" />

*Above: A conversion funnel mapping the lifecycle of a ride request. The visualization exposes the platform's most critical operational leak: an 18% drop-off caused specifically by driver cancellations after a match has been made.*

<br>

<img width="1384" height="484" alt="week 6 stacked" src="https://github.com/user-attachments/assets/2dbd6a10-a678-438b-a85d-305de269c1ae" />

*Above: Monthly ride volume stacked by final booking outcome. This reveals that while top-of-funnel demand remains stable at roughly 12,500 rides per month, the proportion of "lost" rides fluctuates, highlighting periods of severe driver under-supply and operational friction.*

<br>

<img width="1119" height="684" alt="week 6 heatmap" src="https://github.com/user-attachments/assets/e01ed548-2863-4d53-9c0f-a6bb2817b169" />

*Above: A monthly cohort retention heatmap tracking user return rates. This analysis establishes a baseline for platform loyalty and proves the necessity of shifting business focus from expensive new-user acquisition to retaining the existing user base.*

> **Strategic Recommendation:** The product and operations teams must pivot their immediate focus away from top-of-funnel user acquisition and toward **Match-to-Ride Success**. Because driver cancellations (18%) are the primary systemic bottleneck, the highest ROI for the engineering team will be investigating driver incentive alignment (e.g., targeted acceptance bonuses for high-traffic zones) and fixing the flat pricing model to properly monetize premium vehicle tiers.

---

## Week 7: Executive Report & Final Recommendations
The project concluded with a synthesized findings report designed for product and operations stakeholders. This final phase translated the exploratory, statistical, and predictive insights into three concrete business recommendations, while critically evaluating the limitations of the dataset.

### 1. The Largest Leak: Driver Cancellations (18%)
Driver-initiated cancellations after a match is made account for 18% of all bookings (27,000 rides). This is the single largest category of ride loss in the dataset—larger than customer cancellations, no-driver events, and incomplete rides combined. The month-over-month rate is highly stable (17.6%–18.6%), indicating a structural, systemic issue rather than a seasonal anomaly.
* **Recommendation:** Implement a targeted Driver Performance Programme. Track cancellation rates at the individual driver level and engage the highest-cancelling cohorts to align incentives. *(Owner: Driver Operations)*

<img width="1186" height="387" alt="week 7 driver" src="https://github.com/user-attachments/assets/24c7f80a-30c2-45da-bdb1-fde0e12d1430" />

*Above: A comparative breakdown of ride loss by category. The data reveals that driver-initiated cancellations account for a massive 18% of all bookings (27,000 rides), making it the single largest operational leak on the platform—exceeding customer cancellations and unfulfilled matches combined.*

### 2. The Wait Time (VTAT) Correlation & Data Caveat
Customer-cancelled rides feature an average driver arrival time of 12.5 minutes, compared to just 8.5 minutes for successfully completed rides. While there is a moderate positive correlation (r = 0.31) between longer wait times and higher cancellation rates, a critical data anomaly was identified:
* **The Caveat:** The dataset artificially caps completed rides at a maximum VTAT of 15 minutes, while allowing cancelled rides to reach 20 minutes. Therefore, the 100% cancellation rate observed beyond the 15-minute mark is a structural artifact of the synthetic data, not a pure behavioral observation.
* **Recommendation:** The general trend holds (longer waits = higher churn), but the specific "15-minute threshold" must be validated against live, unbounded operational data before deploying automated reassignment algorithms.

### 3. Root Cause Analysis: 78% of Customer Churn is Fixable
Of the 10,500 customer-initiated cancellations, only 22% were due to genuine user decisions (e.g., a change of plans). The remaining 78% (8,147 rides) stemmed from identifiable product, policy, or operational failures.
* **The Breakdown:** The leading fixable causes were "Wrong Address" (2,362), "Driver Not Moving" (2,335), and "Driver Asked to Cancel" (2,295).
* **Recommendation:** Prioritize a "Map Confirmation UX Fix." Because "Wrong Address" is the highest-volume fixable error, implementing a "Confirm Exact Pin" prompt before booking offers the quickest engineering build with the highest immediate revenue recovery.
  
<img width="1167" height="386" alt="week 7 customer" src="https://github.com/user-attachments/assets/7e4f37b4-b111-441a-b184-eed3d02e40d2" />

*Above: A root-cause analysis of customer-initiated cancellations. The breakdown shows that 78% of these cancellations stem from identifiable and fixable friction points—such as map UX issues ("Wrong Address") and driver behavior—rather than a genuine change in the user's travel plans.*

---

## Assumptions, Limitations & Potential Biases
*This section provides critical context for the findings and should be reviewed before acting on any operational recommendations.*

### 1. Key Analytical Assumptions
* **Revenue Opportunity Estimates:** Cancelled rides lack a `Booking Value` because no fare was charged. Any revenue attached to lost rides (e.g., 27,000 × ₹508 = ₹13.7M) assumes that cancelled rides would have earned the same average as completed rides. This assumption may not hold if cancelled rides skew shorter or occur in lower-demand zones. Treat these figures as directional, not absolute.
* **VTAT as a Booking-Time Signal:** The predictive model in Week 5 assumes `Avg VTAT` is available at the time of booking (as a dispatch estimate). If the dataset records the *actual* arrival time after the fact, using it would constitute data leakage. This requires confirmation from the data engineering team.
* **Self-Reported Customer Reasons:** Customer cancellation reasons are selected from a dropdown menu. Customers often choose the nearest available option rather than the precise root cause (e.g., selecting "Wrong Address" for an unrelated issue). These categories provide useful directional signals, but not exact root cause counts.

### 2. Data Limitations (Synthetic Artifacts)
This dataset contains synthetic generation artifacts that limit real-world extrapolation:
* **The VTAT Boundary Anomaly:** `Avg VTAT` is capped at exactly 15 minutes for completed rides but extends to 20 minutes for customer-cancelled rides. The observed "100% cancellation rate above 15 minutes" is a hard boundary created by the data generation process, not a true customer decision threshold.
* **Uniform Distribution of Outcomes:** Both driver and customer cancellation reasons appear in almost exactly equal proportions (~25% and ~22% respectively). In live operational data, reasons rarely distribute this evenly, suggesting random uniform assignment rather than recorded human behavior.
* **Retention Tracking:** The dataset contains 148,788 unique customers across 150,000 rides (a 99.2% one-time user rate). This is highly unrealistic for a ride-hailing platform and renders standard long-term cohort retention analysis uninformative.
* **Missing External Context:** The data lacks external variables such as marketing campaigns, competitor pricing changes, driver demographics, or localized weather events. We can describe the patterns, but cannot definitively isolate external causes.

### 3. Potential Biases
* **Survivorship Bias in ARPR:** Average Revenue Per Ride (ARPR) is calculated strictly from completed rides. Rides that were cancelled may have possessed fundamentally different characteristics (e.g., shorter distances, specific vehicle types, specific times of day). Therefore, the ARPR from completed rides is not a perfectly neutral baseline for all 150,000 bookings.
* **Reporting Bias:** Customers cancelling due to driver behavior (e.g., "Driver asked to cancel") may underreport this reason due to fear of retaliation or lack of trust in the platform's resolution process. The true count of policy violations is likely higher than recorded.
* **Confirmation Bias in Recommendations:** Recommendations were generated by observing data patterns and reasoning backward to a logical fix. This is vulnerable to confirmation bias. Every recommendation (such as the Map Confirmation UX Fix) should be subjected to rigorous A/B testing or a localized pilot program before a full-scale platform rollout.
