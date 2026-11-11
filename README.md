# 👥 Atlas Lab – HR Analytics Dashboard (Power BI)

> An end-to-end **HR analytics** project built in **Power BI**: data exploration, a Power Pivot style data model with **DAX measures**, and a 4-page interactive dashboard covering workforce overview, demographics, employee performance, and attrition.

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-8A2BE2?style=for-the-badge)
![Power Query](https://img.shields.io/badge/Power%20Query-217346?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-In%20Progress-orange?style=for-the-badge)

![Overview Page](images/overview.png)

---

## 📑 Table of Contents

- [🎯 Project Overview](#-project-overview)
- [🗂️ Repository Structure](#️-repository-structure)
- [🔄 Workflow at a Glance](#-workflow-at-a-glance)
- [🔍 Stage 1: Data Exploration](#-stage-1-data-exploration-in-power-bi)
- [🧩 Stage 2: Data Modeling](#-stage-2-data-modeling-with-power-pivot-inside-power-bi)
- [📊 Stage 3: Dashboard](#-stage-3-power-bi-dashboard)
- [💡 Key Insights](#-key-insights)
- [🛠️ Tools & Skills](#️-tools--skills)
- [🚀 How to Use](#-how-to-use)
- [👤 Author](#-author)

---

## 🎯 Project Overview

| 📌 Item | 📝 Details |
|---|---|
| **Project Name** | Atlas Lab – Analytics & Insights |
| **Business Goal** | Understand workforce composition, employee performance and satisfaction, and the drivers of attrition |
| **Scale** | 👥 1,470 employees · ✅ 1,233 active · ❌ 237 inactive · 📉 16% attrition |
| **Departments** | Technology, Sales, Human Resources |
| **Time Span** | Hire years 2012–2022 · performance reviews 2016–2022 |
| **Tools** | Power BI, Power Query, Data Model (Power Pivot), DAX |
| **Deliverable** | Interactive 4-page dashboard with slicers and page navigation |

---

## 🗂️ Repository Structure

```text
📦 atlas-lab-hr-dashboard
 ┣ 📂 data
 ┃ ┗ 📄 raw_data.xlsx            # Source data (optional)
 ┣ 📂 images
 ┃ ┣ 🖼️ data_model.png           # Data model (schema)
 ┃ ┣ 🖼️ overview.png             # Dashboard page 1
 ┃ ┣ 🖼️ demographics.png         # Dashboard page 2
 ┃ ┣ 🖼️ performance_tracker.png  # Dashboard page 3
 ┃ ┗ 🖼️ attrition.png            # Dashboard page 4
 ┣ 📊 Employee_data.pbix          # Power BI report
 ┗ 📄 README.md
```

---

## 🔄 Workflow at a Glance

| Stage | Icon | Phase | Tool | Output |
|:---:|:---:|---|---|---|
| 1 | 🔍 | Data Exploration | Power BI | Data understanding & issues list |
| 2 | 🧩 | Data Modeling | Power Pivot (Data Model) + DAX | Schema, relationships, measures |
| 3 | 📊 | Visualization | Power BI report | 4-page interactive dashboard |

```text
Raw Data ➜ Explore ➜ Model (Relationships + DAX) ➜ Dashboard (4 pages)
```

---

## 🔍 Stage 1: Data Exploration in Power BI

**Goal:** Understand the structure, quality, and content of the HR data before building the model.

### 📋 Dataset Description

> 🚧 *Columns are taken from the data model and report. Please confirm data types and add descriptions.*

| Table | Column | Description |
|---|---|---|
| 👤 **DimEmployee** | `EmployeeID` | Unique employee key |
| | `Full Name` | Employee name |
| | `Age`, `Age Bins` | Age and age groups (`<20`, `20-29`, `30-39`, `40-49`, `50>`) |
| | `Gender`, `Ethnicity`, `MaritalStatus` | Demographic attributes |
| | `Department`, `JobRole` | Position in the organization |
| | `HireDate`, `YearsAtCompany` | Tenure information |
| | `Attrition` | Whether the employee left (`Yes` / `No`) |
| | `BusinessTravel`, `OverTime` | Work conditions |
| | `DistanceFromHome (KM)` | Commute distance |
| | `Education`, `EducationField` | Education details |
| 📝 **FactPerformanceRating** | `PerformanceID`, `EmployeeID`, `ReviewDate` | Review identifiers and date |
| | `JobSatisfaction`, `EnvironmentSatisfaction`, `RelationshipSatisfaction`, `WorkLifeBalance` | Satisfaction scores (1 to 5) |
| | `ManagerRating`, `SelfRating` | Performance ratings (1 to 5) |
| | `TrainingOpportunitiesTaken` | Training activity |

### 🔎 What Was Checked

- ✅ Number of rows and columns per table
- ✅ Data types (dates, numbers, text)
- ✅ Missing / blank values
- ✅ Duplicate records and duplicate keys
- ✅ Outliers (age range **18 to 51**)
- ✅ Consistency of categories (department, job role, ethnicity)

### ⚠️ Issues Found

> 🚧 *Add the real issues you found (the rows below are examples).*

| # | Issue | Column | Severity |
|:---:|---|---|:---:|
| 1 | One `Sales Executive` appears under the Technology department | `Department`, `JobRole` | 🟡 Low |
| 2 | `[e.g., Date stored as text]` | `HireDate` | 🔴 High |
| 3 | `[e.g., Age needed grouping]` | `Age` | 🟠 Medium (solved with `Age Bins`) |

---

## 🧩 Stage 2: Data Modeling with Power Pivot inside Power BI

**Goal:** Build a relational model that enables fast, flexible analysis across employees, performance reviews, and time.

![Data Model](images/data_model.png)

### 🏗️ Model Design

The model uses **one fact table** (`FactPerformanceRating`), a **main dimension** (`DimEmployee`), and **lookup dimensions** for dates, ratings, satisfaction levels, and education levels. A separate `Measure` table keeps all DAX measures in one place.

| Table | Type | Key Columns | Purpose |
|---|:---:|---|---|
| 📝 **FactPerformanceRating** | 🟦 Fact | `EmployeeID`, `PerformanceID`, `ReviewDate` | Performance reviews, satisfaction and rating scores |
| 👤 **DimEmployee** | 🟩 Dimension | `EmployeeID` | Employee attributes (demographics, job, attrition) |
| 📅 **DimDate** | 🟩 Dimension | `Date`, `DayName`, `DayNameShort`, `DayNumber`, `DayOfWeek` | Time analysis by year, month, and day |
| ⭐ **DimRatingLevel** | 🟩 Lookup | `RatingID`, `RatingLevel` | Text labels for ratings (Unacceptable, Needs Improvement, Meets Expectation, …) |
| 😊 **DimSatisfiedLevel** | 🟩 Lookup | `SatisfactionID`, `SatisfactionLevel` | Text labels for satisfaction (Very Dissatisfied, Dissatisfied, Neutral, …) |
| 🎓 **DimEducationLevel** | 🟩 Lookup | `EducationLevelID`, `EducationLevel` | Text labels for education levels |
| 🧮 **Measure** | 🟪 Measures | N/A | All DAX measures |

### 🔗 Relationships

| From (One side) | To (Many side) | Cardinality | Notes |
|---|---|:---:|---|
| `DimEmployee[EmployeeID]` | `FactPerformanceRating[EmployeeID]` | `1 : *` | Main relationship |
| `DimDate[Date]` | `FactPerformanceRating[ReviewDate]` | `1 : *` | Review date |
| `DimDate[Date]` | `DimEmployee[HireDate]` | `1 : *` | Hire date (dotted line: inactive relationship, confirm) |
| `DimRatingLevel[RatingID]` | `FactPerformanceRating[ManagerRating]` / `[SelfRating]` | `1 : *` | Two roles for the same lookup table |
| `DimSatisfiedLevel[SatisfactionID]` | `FactPerformanceRating` (4 satisfaction columns) | `1 : *` | Four roles: Job, Environment, Relationship, Work Life Balance |
| `DimEducationLevel[EducationLevelID]` | `DimEmployee[Education]` | `1 : *` | Snowflake link |

> 💡 *Dotted lines are inactive relationships. They are normally activated inside DAX with `USERELATIONSHIP()`. Please confirm which ones are active.*
<img width="861" height="433" alt="Screenshot 2026-11-11 204457" src="https://github.com/user-attachments/assets/b16da92c-1aad-4cc0-b9d4-786316f2992e" />

### 📐 DAX Measures (in the `Measure` table)

| Measure | Purpose |
|---|---|
| 👥 `Total Employee` | Count of all employees |
| ✅ `Active Employee` | Employees who have not left |
| ❌ `InActive Employee` | Employees who left |
| 📉 `Attrition Rate` | Inactive employees ÷ total employees |
| 💵 `Average of Salary` | Average salary |
| 😊 `Job Satisfaction`, `Environment Satisfaction`, `Relationship Satisfaction`, `Work Life Balance` | Satisfaction scores per employee/year |
| ⭐ `Manager Rating`, `Self Rating` | Rating scores per employee/year |
| 🧒 `Youngest Employees` / 🧓 `Oldest Employees` | Minimum and maximum age |
| 📅 `Last Review` / `Next Review` | Review dates for the selected employee |

> 🚧 *Paste the DAX formulas here if you want them documented.*

---

## 📊 Stage 3: Power BI Dashboard

**Goal:** Present HR insights in a branded, easy-to-navigate dashboard with a left navigation panel and global filters.

| 🧭 Feature | 📝 Details |
|---|---|
| **Pages** | Overview · Demographics · Performance Tracker · Attrition |
| **Slicers** | Year, Department, Gender (on every page) |
| **Navigation** | Buttons on the left panel |
| **Theme** | Charcoal and lime, with the "Atlas Lab" brand |

### 1️⃣ Overview Page

<img width="1168" height="577" alt="Screenshot 2026-11-11 204539" src="https://github.com/user-attachments/assets/290bb70c-f7cc-4eb8-b7d4-3251a06e0edb" />

| 🔢 KPI | Value |
|---|:---:|
| ✅ Active Employees | **1,233** |
| ❌ Inactive Employees | **237** |
| 👥 Total Employees | **1,470** |
| 📉 Attrition Rate | **16%** |

**Total Employees by Year and Attrition** (stacked columns, by hire year)

| Year | 2012 | 2013 | 2014 | 2015 | 2016 | 2017 | 2018 | 2019 | 2020 | 2021 | 2022 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| ✅ Stayed | 127 | 113 | 113 | 112 | 90 | 95 | 114 | 124 | 99 | 116 | 130 |
| ❌ Left | 24 | 23 | 23 | 15 | 24 | 11 | 22 | 21 | 28 | 21 | 25 |
| **Total** | 151 | 136 | 136 | 127 | 114 | 106 | 136 | 145 | 127 | 137 | 155 |

**Total Employees by Department** (decomposition tree)

| Department | Employees | Top Job Roles |
|---|:---:|---|
| 💻 Technology | 961 | Software Engineer (294), Data Scientist (261), Machine Learning Engineer (146), Senior Software Engineer (132), Engineering Manager (75), Analytics Manager (52) |
| 💼 Sales | 446 | N/A |
| 🧑‍💼 Human Resources | 63 | N/A |

### 2️⃣ Demographics Page

<img width="1173" height="574" alt="Screenshot 2026-11-11 204553" src="https://github.com/user-attachments/assets/5f80ed7e-4a9e-4aff-94ee-5ff6546877e9" />

| 📊 Visual | Insight |
|---|---|
| 🧒 Youngest Employee | **18** years old |
| 🧓 Oldest Employee | **51** years old |
| 💍 Marital Status (donut) | Married **624 (42%)** · Single **549 (37%)** · Divorced **297 (20%)** |
| 🌳 Gender ➜ Age Bins (decomposition tree) | Female 675 · Male 651 · Non-Binary 124 · Prefer not to say 20 |
| 🌍 Employees & Average Salary by Ethnicity (combo chart) | See table below |

| Ethnicity | Employees | Avg. Salary |
|---|:---:|:---:|
| White | 860 | $115K |
| Black or African American | 207 | $112K |
| Mixed or multiple ethnic groups | 198 | $106K |
| Asian or Asian American | 113 | $110K |
| American Indian or Alaska Native | 50 | $112K |
| Native Hawaiian | 26 | $115K |
| Other | 16 | $102K |

| 👩 Female by Age Bin | `<20` | 20-29 | 30-39 | 40-49 | 50+ |
|---|:---:|:---:|:---:|:---:|:---:|
| Employees | 38 | 388 | 150 | 96 | 3 |

### 3️⃣ Performance Tracker Page

<img width="1172" height="572" alt="Screenshot 2026-11-11 204608" src="https://github.com/user-attachments/assets/8a685151-efbe-4d7c-9aec-69ad1c4dc112" />

An **individual employee view**: pick an employee from the dropdown (example: *Ariadne Isakov*) to see their review history.

| 🧱 Section | Content |
|---|---|
| 🧑 Employee Card | Start Date (31/10/2013), Last Review (01/09/2022), Next Review (01/09/2023) |
| 😊 Satisfaction | Line charts for Job Satisfaction, Environment Satisfaction, Relationship Satisfaction, Work Life Balance (2016 to 2022) |
| ⭐ Rating | Line charts for Manager Rating and Self Rating (2016 to 2022) |
| 🏷️ Level Legends | `Satisfaction Level` (1 Very Dissatisfied, 2 Dissatisfied, 3 Neutral, …) and `Rating Level` (1 Unacceptable, 2 Needs Improvement, 3 Meets Expectation, …) so the 1 to 5 scores are easy to read |

### 4️⃣ Attrition Page

<img width="1171" height="573" alt="Screenshot 2026-11-11 204622" src="https://github.com/user-attachments/assets/34874193-b187-4cf3-8b08-c004e9905526" />

| 📊 Visual | Key Numbers |
|---|---|
| 🏢 Attrition by Department (donut) | Technology 961 (65%) · Sales 446 (30%) · Human Resources 63 (4%) |
| ✈️ Attrition Rate by Business Travel | Some Travel **15%** (1,043 employees) · Frequent Traveller **25%** (277) · No Travel **8%** (150) |
| ⏰ Attrition Rate by OverTime | Overtime **31%** vs. No overtime **10%** |
| 📅 Attrition by Hire Date | Lowest **10%** (2017) · Highest **22%** (2020) |
| 🕰️ Attrition Rate by Years at Company | Ranges from **1% to 34%**; the two highest bars (**34%** and **32%**) are highlighted in orange |

| 📅 Hire Year | 2012 | 2013 | 2014 | 2015 | 2016 | 2017 | 2018 | 2019 | 2020 | 2021 | 2022 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 📉 Attrition Rate | 16% | 17% | 17% | 12% | 21% | 10% | 16% | 14% | 22% | 15% | 16% |

---

## 💡 Key Insights

1. 📉 **Overall attrition is 16%**: 237 of 1,470 employees have left.
2. ⏰ **Overtime is the strongest attrition signal**: employees working overtime leave at **31%**, about **3x** the rate of those who do not (10%).
3. ✈️ **Frequent travellers leave the most (25%)**, about 3x the rate of employees who never travel (8%).
4. 🕰️ **Early and mid tenure groups carry the highest risk** (up to 34% and 32% in the highlighted bars).
5. 📅 **Hire cohorts vary**: 2020 (22%) and 2016 (21%) have the highest attrition, while 2017 (10%) has the lowest.
6. 💻 **Technology is 65% of the workforce** (961 employees), and two roles, Software Engineer and Data Scientist, make up about 38% of all employees.
7. 🌍 **Workforce diversity**: Female 46%, Male 44%, Non-Binary 8%; **57% of female employees are aged 20 to 29**.
8. 💵 **Average salary ranges from $102K to $115K across ethnic groups**, with the lowest in the smallest group (Other, 16 employees) and the highest for White and Native Hawaiian employees.

---

## 🛠️ Tools & Skills

| Tool | Usage |
|---|---|
| 📊 Power BI Desktop | Report design, slicers, navigation |
| ⚡ Power Query | Data loading and shaping |
| 🧩 Data Model (Power Pivot) | Relationships and star/snowflake schema |
| 🧮 DAX | KPI and rating measures |
| 🌳 Decomposition Tree, Combo, Donut, Line charts | Visual analysis |

---

## 🚀 How to Use

1. 📥 Clone the repository
   ```bash
   git clone https://github.com/[username]/atlas-lab-hr-dashboard.git
   ```
2. 📂 Open `Employee_data.pbix` in **Power BI Desktop**
3. 🔄 Click **Refresh** to reload the data
4. 🎛️ Use the left navigation panel and the Year / Department / Gender slicers

---

## 👤 Author

**[Your Name]**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://linkedin.com/in/your-profile)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/your-username)

⭐ *If you found this project useful, please give it a star!*
