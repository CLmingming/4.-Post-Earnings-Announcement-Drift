# 4.-Post-Earnings-Announcement-Drift## Problem Overview  
This assignment focuses on investigating the **Post Earnings Announcement Drift (PEAD)** phenomenon in China’s A-share markets. PEAD describes the tendency for a stock’s cumulative abnormal returns (CARs) to continue drifting in the same direction as earnings surprises revealed in announcements.  

Using the event study framework introduced in the lectures, you will analyze whether PEAD exists in China’s A-share markets using interim (semi-annual) and annual earnings announcements from 2016 to 2023. The event window is defined as the 60 trading days before announcements to 60 trading days after announcements.  

---

## Instructions  

### **1. Individual Stock Return Data Preprocessing**  
1.1 Download stock code, trading date, and daily return without cash dividend for all A-share stocks from **2016/1/1 to 2023/12/31** from the `China Stock Market Series/Stock Trading/Individual Stock Trading` table.  
1.2 Convert trading dates into **year-month-day** format (e.g., `29jan2014`).  

---

### **2. Market Return Data Preprocessing**  
2.1 Download daily trading date, market type, and daily market return without cash dividend (equal weight) for all A-share stocks from **2016/1/1 to 2023/12/31** from the `China Stock Market Series/Stock Trading/Market Trading` table.  
2.2 Keep records for **SSE A-share market** (excluding **STAR Market**) based on market type.  
2.3 Convert trading dates into **year-month-day** format.  

---

### **3. EPS Data Preprocessing**  
3.1 Download quarterly records on stock code, stock short name, earnings per share (EPS), statement type, industry code, and ending date of statistics from **2013Q1 to 2022Q4** from the `China Listed Firms Research Series/Financial Indicators/Index per Share` table.  

3.2-3.7 Preprocess the data as follows:  
- Exclude parent statements based on the statement type code.  
- Exclude ST and PT companies (e.g., `ST`, `*ST`, `PT`, `*PT`).  
- Exclude finance companies based on the industry code.  
- Keep interim and annual EPS records with ending dates in the format `YYYY/6/30` or `YYYY/12/31`.  
- Convert ending dates into **half-yearly dates** (e.g., `2016h1`, `2016h2`).  
- Structure the data as a **company-half-year panel** with stock code and ending date of statistics.  

3.8 Replace second-half EPS values with the difference between EPS in the second half of the year and EPS in the first half of the year.  

3.9 Derive **unexpected earnings (UE):**  
   **UE<sub>i,t</sub> = EPS<sub>i,t</sub> − EPS<sub>i,t−2</sub>**  
   (Difference between current EPS and EPS for the same period last year).  

3.10 Derive **standardized unexpected earnings (SUE):**  
   **SUE<sub>i,t</sub> = UE<sub>i,t</sub> / σ<sub>i,t</sub>**,  
   where **σ<sub>i,t</sub>** is the standard deviation of {UE<sub>i,t−3</sub>, UE<sub>i,t−2</sub>, UE<sub>i,t−1</sub>, UE<sub>i,t</sub>}.  

3.11 Derive **SUE deciles** for each firm by the ending date of statistics.  

3.12 Keep stock codes, ending dates, and SUE deciles.  

3.13 Reshape the data from a **long format** (company-half-year panel) into a **wide format** such that each ending date corresponds to an SUE decile column.  

---

### **4. Announcement Data Preprocessing**  
4.1 Download quarterly announcement dates for all A-share stocks from **2016Q1 to 2022Q4** from the `China Listed Firms Research Series/Statements Release Dates` table.  

4.2-4.4 Preprocess the data as follows:  
- Keep interim and annual records based on report type.  
- Structure the data as a **company-year-half panel** with stock code and ending date of statistics.  
- Reshape the data from a **long format** into a **wide format** such that each ending date corresponds to an announcement date column.  

---

### **5. Data Merging**  
5.1 Load the processed individual stock return data from **Step 1.2**.  
5.2 Merge the processed market return data from **Step 2.3** based on trading date.  
5.3 Merge the processed SUE decile data from **Step 3.13** based on stock code.  
5.4 Merge the processed announcement data from **Step 4.4** based on stock code.  

---

### **6. Event Study**  
6.1 Load the merged data from **Step 5.4**.  

6.2 Derive **daily abnormal returns (ARs):**  
   **AR<sub>i,t</sub> = r<sub>i,t</sub> − r<sub>m,t</sub>**,  
   where **r<sub>i,t</sub>** is the individual stock return for company *i* at time *t*, and **r<sub>m,t</sub>** is the market return at time *t*.  

6.3 For each EPS announcement event:  
- Derive the **event date index** as the number of trading days between trading dates and the announcement date.  
- Keep records where **event date index ∈ [−60, 60]**.  
- Derive mean ARs by event date index and SUE deciles. Collapse the data into a **decile-event-date panel**.  
- For each **SUE decile**, calculate **cumulative abnormal returns (CARs)**:  
  **CAR<sub>p,t<sub>n</sub></sub> = ∑<sub>j=0</sub><sup>n</sup> AR<sub>p,t<sub>j</sub></sub>**,  
  where *p* is the portfolio (SUE decile) and *n* is the trading day in the event window.  

6.4 Aggregate all decile-event-date panels into a single data set and derive **mean CARs** over all events for each SUE decile and event date index.  

6.5 Plot **CARs** with respect to the event date index by SUE deciles.  

---

## Hints  
1. This empirical design is based on **Foster, Olsen, and Shevlin (1984)** and **Bernard and Thomas (1989)**. Reviewing these papers may be helpful.  
2. Use the **ending date of statistics** to identify the period covered by each EPS announcement.  
3. Use the **announcement date** for when investors receive the earnings report.  
4. Re-index trading records using the **event date index** (e.g., the event date index for the announcement date is 0).  
5. Tools like **bysort** and **asrol** in Stata can help with repetitive calculations (e.g., deriving SUE).  

---
