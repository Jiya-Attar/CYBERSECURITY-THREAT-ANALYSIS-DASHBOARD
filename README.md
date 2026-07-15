# Cybersecurity Threat Analytics - Network Intrusion Detection

---

## Overview

This project analyzes real network traffic data from the CICIDS 2017 
dataset to detect DDoS attacks, classify threat severity, and identify 
anomalous behavior using SQL analytics and machine learning.

The entire pipeline goes from raw network logs to a Power BI dashboard 
that a security operations team can actually use to monitor threats.

---

## Problem Statement


Modern IT infrastructure generates thousands of network events every second. 
Security teams cannot manually monitor and analyze every connection to 
identify potential threats. DDoS attacks — one of the most common and damaging 
cyberattacks — often go undetected until significant damage is already done because
traditional rule-based systems fail to adapt to evolving attack patterns. There is
a need for an automated analytics system that can process raw network traffic, 
detect anomalous behavior using statistical and machine learning methods, classify
threats by severity, and present actionable insights through a single dashboard — enabling 
faster investigation and response by security operations teams.

---

## Why I Built This

Most cybersecurity tools flag threats after damage is done. 
This project focuses on identifying suspicious patterns before 
they escalate — using statistical methods and unsupervised ML 
on real network flow data rather than synthetic or toy datasets.

---

## Dataset

Source: CICIDS 2017 — Canadian Institute of Cybersecurity  
File: Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv  
Records Used: 6,134 (balanced sample — 3,067 BENIGN, 3,067 DDoS)  
Features: 12 network flow features including flow duration, 
bytes per second, packet statistics, and TCP flags

---

## What This Project Does

### Data Cleaning
The raw dataset had infinite values in flow-based features like 
bytes per second — a common issue when packets are near zero 
duration. These were replaced with NaN and dropped. Column names 
had trailing whitespace which caused KeyErrors until stripped.

### SQL Analytics Layer
All threat analysis queries were run using SQLite inside Google 
Colab. Key queries built:

- Traffic distribution between BENIGN and DDoS
- Average behavioral differences between attack and normal traffic
- Threat severity classification using CASE WHEN thresholds
- Z-score based anomaly scoring using window functions
- Port vulnerability analysis using HAVING and GROUP BY

The Z-score query revealed that DDoS traffic had flow_bytes_per_sec 
values more than 3 standard deviations above the mean — confirming 
statistical separation between attack and normal traffic.

### Machine Learning — Isolation Forest
Algorithm: Isolation Forest (unsupervised)  
Contamination: 10 percent  
Result: 614 anomalies flagged out of 6,134 records  

One important finding — the model flagged more BENIGN records as 
anomalies than DDoS records in initial runs. This is because DDoS 
traffic in this dataset is highly uniform, making it look normal 
to an unsupervised model. This is a known challenge called the 
false positive problem in security analytics. The SQL Z-score 
layer was more effective at separating DDoS from BENIGN traffic 
than ML alone — showing that a layered approach works better than 
relying on a single detection method.

### Severity Scoring
A rule-based severity engine was built on top of flow_bytes_per_sec:

- Above 1,000,000 bytes/sec with SYN flags = CRITICAL
- Above 500,000 bytes/sec = HIGH  
- Above 100,000 bytes/sec = MEDIUM
- Below 100,000 bytes/sec = LOW

Results:
- CRITICAL: 594 records
- HIGH: 279 records
- MEDIUM: 525 records
- LOW: 4,736 records

### Key Finding — Port 80
100 percent of DDoS attacks in this dataset targeted Port 80, 
the HTTP web server port. This is consistent with real-world 
DDoS patterns where web servers are the primary target. 
The security recommendation from this finding is to prioritize 
rate limiting and WAF rules specifically for Port 80 traffic.


---

## Tech Stack

- Python 3.12
- Pandas and NumPy for data processing
- SQLite for SQL analytics
- Scikit-learn for Isolation Forest
- Power BI Desktop for dashboard
- Google Colab as development environment

---

## Power BI Dashboard

The dashboard contains 7 visuals on a single page:

- 4 KPI cards showing total records, anomalies, DDoS threats, 
  and critical threats
- Donut chart for traffic type distribution
- Scatter plot showing normal vs anomaly clusters
- Bar chart for severity distribution
- Bar chart for port attack analysis
- Line chart for anomaly score distribution
- Slicer filter for interactive cross-filtering

Dashboard Preview:
https://github.com/YourUsername/Cybersecurity-Threat-Analytics/blob/main/dashboard_preview.png

---

## SQL Concepts Used

- Window functions for Z-score calculation
- CASE WHEN for severity classification
- HAVING for post-aggregation filtering
- NULLIF for divide by zero prevention
- Subqueries for percentage calculations
- GROUP BY for behavioral profiling

---

## Results Summary

Total Records: 6,134  
Anomalies Detected: 614  
Critical Threats: 594  
Primary Attack Port: Port 80  
DDoS Detection via Z-Score: Records with z_score above 3  
ML Model: Isolation Forest — contamination 0.1  

---

AUTHOR
**Jiya Attar**
Aspiring Data Analyst | Business Intelligence | Power BI | SQL | Python
