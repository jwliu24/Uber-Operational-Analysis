# Uber-Operational-Analysis
A data-driven deep dive into Uber's operational performance, focusing on supply reliability (VTAT) and cancellation trends. Includes a 30-day demand forecast and a vehicle reliability scorecard built with Python and SQL.

# Uber Operational Analysis Project

## Project Objective
To identify bottlenecks in ride completion rates and vehicle turnaround times (VTAT) to improve platform reliability.

## Week 1 Insights: The "Supply Wall"
* **Key Finding:** Go Sedan has the lowest reliability, with a 7.22% "No Driver Found" rate.
* **Time Trend:** Supply shortages peak at 1:00 AM, where failure rates jump to nearly 8%.
* **Data Status:** Cleaned 150,000 rows of ride data and engineered time-based features (Peak vs. Off-Peak).

### Tech Stack
* **Language:** Python (Pandas, Matplotlib, Seaborn)
* **Platform:** Google Colab
* **Focus:** Feature Engineering, Time-Series Forecasting

## Week 2: Feature Engineering & Customer Friction Analysis
In the second week of the project, the focus shifted from data exploration to Feature Engineering. By creating binary flags and behavioral "buckets," I was able to quantify exactly why and when the business loses revenue.

### Key Technical Accomplishments
Binary Flagging: Developed a suite of boolean flags (is_successful, is_cancelled_customer, is_no_driver) to allow for rapid calculation of success and failure rates across 150,000+ rows.

Behavioral Bucketing: Created the Wait_Time_Bucket feature using Avg VTAT (Vehicle Travel Arrival Time) to categorize driver response speeds.

Deep Copying: Implemented a robust data pipeline using .copy() to ensure a clean separation between raw data and engineered features.

### Top Insight: The 15-Minute "Revenue Cliff"
By correlating wait times with customer cancellations, I discovered a definitive threshold for customer patience.

Patience Zone (0-15 mins): Cancellation rates remain stable and low (under 10%).

The Breaking Point (15+ mins): The customer cancellation rate spikes to 100%.

Strategic Recommendation: This data proves that any booking assigned to a driver more than 15 minutes away is a guaranteed revenue loss. Operational efforts should focus on keeping "Go Sedan" arrival times under this 15-minute threshold to prevent total churn.
