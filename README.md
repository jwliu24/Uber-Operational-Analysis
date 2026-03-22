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

<img width="1015" height="549" alt="week 1" src="https://github.com/user-attachments/assets/b588a335-e563-4469-a3c0-ff11ba56acdb" />

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

> **Strategic Recommendation:** This data proves that any booking assigned to a driver more than 15 minutes away is a guaranteed revenue loss. Operational efforts should focus on keeping "Go Sedan" arrival times under this 15-minute threshold to prevent total churn.

<img width="969" height="573" alt="week 2" src="https://github.com/user-attachments/assets/11f5eb87-2393-4dcf-abd2-d881186d40fc" />

---

## Week 3: Exploratory Data Analysis (EDA) & Business Impact
In Week 3, the focus moved to visual storytelling and uncovering the "why" behind the revenue loss. By analyzing cancellation patterns across vehicle types, I identified a major operational bottleneck.

### Key Insights
* **The $1.15M 'Auto' Leakage:** Contrary to assumptions that premium vehicles drive the most financial loss, the high-frequency Auto segment was identified as the platform's largest absolute revenue leak, totaling over $1,150,000 in unrealized bookings.
* **Pinpointing the Root Cause:** A behavioral deep dive revealed a distinct UI/GPS friction point. The leading reason for customer cancellations was "Wrong address," which directly correlated with the top driver cancellation reason: "Customer related issue."

> **Strategic Recommendation:** The data proves this is an interface problem, not a driver supply problem. Implementing a "Confirm Exact Pin" prompt or a brief address-edit window for Auto bookings could directly recover a significant portion of this lost revenue.

<img width="1568" height="689" alt="week 3" src="https://github.com/user-attachments/assets/bf2d19e4-05cf-4a42-9111-9dce6e0d20aa" />

---

## Week 4: Statistical Validation & Hypothesis Testing
The final phase shifted from visual exploration to rigorous mathematical validation. By applying statistical hypothesis testing using Python's SciPy library, I was able to prove whether the operational bottlenecks identified during EDA were systemic platform issues or just random variance.

### Key Insights
* **The 'Auto' Leakage Reality Check (Chi-Square Test):** While EDA highlighted the Auto segment as the largest absolute revenue leak, a Chi-Square test of independence (p = 0.89) revealed no statistically significant relationship between vehicle type and cancellation rates. This mathematically proves that the "Wrong Address" UI friction is a platform-wide issue affecting all rides equally, rather than a problem isolated to a specific vehicle tier.
* **Validating Peak Revenue Drivers (Two-Sample T-Test):** A Two-Sample T-Test was deployed to compare average booking values between rush hour and off-peak times. This mathematically validated that time-of-day operations significantly drive up average ticket sizes, confirming that temporal features are highly reliable indicators for future predictive modeling.

> **Strategic Recommendation:** Although the statistical data proves the UI/GPS bug affects all vehicle types equally, product and engineering teams must still prioritize deploying the interface fix to the Auto segment first. Because Auto commands the highest booking volume, targeting this segment will yield the highest absolute dollar recovery and maximize the immediate ROI of the engineering effort.
