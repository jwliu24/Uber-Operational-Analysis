# Uber-Operational-Analysis
A data-driven deep dive into Uber's operational performance, focusing on supply reliability (VTAT) and cancellation trends. Includes a 30-day demand forecast and a vehicle reliability scorecard built with Python and SQL.

# Uber Operational Analysis Project

## Project Objective
To identify bottlenecks in ride completion rates and vehicle turnaround times (VTAT) to improve platform reliability.

## Week 1 Insights: The "Supply Wall"
* **Key Finding:** Go Sedan has the lowest reliability, with a 7.22% "No Driver Found" rate.
* **Time Trend:** Supply shortages peak at 1:00 AM, where failure rates jump to nearly 8%.
* **Data Status:** Cleaned 150,000 rows of ride data and engineered time-based features (Peak vs. Off-Peak).

## Tech Stack
* **Language:** Python (Pandas, Matplotlib, Seaborn)
* **Platform:** Google Colab
* **Focus:** Feature Engineering, Time-Series Forecasting
