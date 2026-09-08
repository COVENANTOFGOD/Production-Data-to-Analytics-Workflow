# Production Data, Analytics Workflow & Business Intelligence

After the raw data had been profiled, staged, cleaned, and validated, the trusted data was organized into production tables. From there, we established relationships, defined business KPIs, and created analytical views for reporting and analysis.

```text
Production Tables
       >>
Relationships
       >>
KPIs
       >>
Analytical Views
```
**Screenshot**
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/55fda965-5d9b-4dc5-ad7a-70658a105c5b" />

## 1. Production Tables

The **production tables** contain the cleaned and validated data that successfully passed the data-quality checks.

In this project, the production layer included tables such as:

```text
dbo.Employees
dbo.Projects
dbo.PerformanceReviews
```
**Screenshot**
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/10ecb6e0-1e3a-4548-a0c6-4227d66762f0" />

These tables represent the core business entities in the TechCorp project-management database.

For example:

### Employees

Contains information about employees, such as:

* EmployeeID
* FullName
* Department
* Job-related attributes
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/43fac274-f81b-4c90-add0-41f3676db35b" />

### Projects

Contains project information, including:

* ProjectID
* ProjectName
* ProjectManagerID
* Project-related attributes
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/913b32a7-ebf0-4bf8-bc63-1b55c733652f" />

### PerformanceReviews

Contains employee performance information, including:

* EmployeeID
* Review information
* OverallRating
* Other performance metrics
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/aac04314-4e69-43b8-8bb8-e41420e96b02" />

At this stage, the data is trusted and ready to be used for analysis.

**Goal:**
Create a reliable production layer containing clean, validated business data.

---

## 2. Relationships

Once the production tables were established, we defined how the tables relate to one another.

This is where **primary keys and foreign keys** become important.

For example:

```text
Employees
   >>
   │ EmployeeID
   >>
PerformanceReviews
```
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/fb1f9e88-f505-414f-90e3-34d6f091f9a8" />

An employee can have one or more performance reviews.

Similarly:

```text
Employees
   >>
   │ EmployeeID
   >>
Projects
   >>
   │ ProjectManagerID
   >>
Project Manager
```
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/018cb19b-5183-456c-97e1-517de38084cf" />

The relationships allow us to combine information from different tables using SQL `JOIN`s.

For example:

```sql
SELECT
    e.EmployeeID,
    e.FullName,
    e.Department,
    pr.OverallRating
FROM dbo.Employees e
INNER JOIN dbo.PerformanceReviews pr
    ON e.EmployeeID = pr.EmployeeID;
```

The relationship:

```sql
e.EmployeeID = pr.EmployeeID
```

allows us to connect employee information with their performance-review information.

**Goal:**
Connect the production tables so that meaningful business analysis can be performed across the database.
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/f58ab85c-2faf-4d5e-b677-cd1d67ced683" />

---

## 3. KPIs

After establishing the relationships, we can calculate **Key Performance Indicators (KPIs)**.

A KPI is a measurable value used to evaluate business or operational performance.

For the TechCorp project, examples include:

### Average Employee Performance

```sql
AVG(OverallRating)
```

This measures the average performance-review score.

### Average Performance by Department

We can group employees by department:

```text
Department
     >>
Employees
     >>
Performance Reviews
     >>
Average Rating
```

This allows management to compare departmental performance.

### Number of Projects

We can calculate:

```sql
COUNT(ProjectID)
```

to determine the number of projects.

### Projects per Project Manager

This can help identify project-management workload.

### Employee Performance Distribution

We can examine how employees are distributed across different performance-rating levels.

For example:

```text
Rating 5 → Excellent
Rating 4 → Very Good
Rating 3 → Average
...
```

**Goal:**
Convert raw business records into measurable indicators that answer specific business questions.
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/3b021bc5-692c-4466-b861-34e5e63f97f2" />

---

## 4. Analytical Views

Instead of repeatedly writing complex joins and calculations, we can create **SQL views** that package commonly required analysis.

An analytical view can combine:

* Multiple production tables
* Business calculations
* Aggregations
* KPIs
* Business-friendly column names

For example:

```sql
CREATE VIEW dbo.vw_EmployeePerformance
AS
SELECT
    e.EmployeeID,
    e.FullName,
    e.Department,
    AVG(pr.OverallRating) AS AvgOverallRating
FROM dbo.Employees e
INNER JOIN dbo.PerformanceReviews pr
    ON e.EmployeeID = pr.EmployeeID
GROUP BY
    e.EmployeeID,
    e.FullName,
    e.Department;
```

Instead of running the entire analytical query every time, analysts can simply query:

```sql
SELECT *
FROM dbo.vw_EmployeePerformance;
```

The view therefore acts as an **analytical layer between the production database and reporting tools**.

### Why analytical views are useful

They provide:

* Reusable analytical logic
* Consistent KPI calculations
* Simpler queries for analysts
* Cleaner datasets for Power BI or Excel
* Separation between production tables and reporting logic

**Goal:**
Create analysis-ready datasets without modifying the underlying production tables.
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/386fcd2e-c717-402e-aec8-85c4a009a6dd" />

---

# Overall Architecture

The complete analytical workflow can be represented as:

```text
                  PRODUCTION
                      >>
             ┌─────────────────┐
             │ Production      │
             │ Tables          │
             │                 │
             │ Employees       │
             │ Projects        │
             │ Performance     │
             │ Reviews         │
             └────────┬────────┘
                      >>
             ┌─────────────────┐
             │ Relationships   │
             │                 │
             │ PKs / FKs       │
             │ JOINs           │
             └────────┬────────┘
                      >>
             ┌─────────────────┐
             │      KPIs       │
             │                 │
             │ Avg Rating      │
             │ Project Count   │
             │ Performance     │
             │ Workload        │
             └────────┬────────┘
                      >>
             ┌─────────────────┐
             │ Analytical      │
             │ Views           │
             │                 │
             │ vw_Employee     │
             │ Performance     │
             │ Other Analysis  │
             └────────┬────────┘
```
# Business Intelligence Layer

The analytical layer is then evaluated from a business perspective, not just a technical one.

1. Business Validation

Business validation checks whether the analytical results actually make sense according to the business context.

This is different from technical data validation.

Technical validation asks:

"Is the data structurally correct?"

For example:

Are foreign keys valid?
Are there duplicate IDs?
Are required fields populated?
Business validation asks:

"Do the results make business sense?"

For example:

Does the average performance score fall within the expected rating range?
Are departments being compared fairly?
Does the number of projects assigned to managers appear reasonable?
Are KPI results consistent with the underlying records?
Do the analytical results support the original business questions?

This stage helps prevent technically correct SQL from producing misleading business conclusions.

Purpose:
Verify that the analytical results are logically consistent and meaningful from a business perspective.
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/4ea712f3-3d6a-49f1-95d3-0be47dd9234c" />

11. Business Insights

After the KPIs and analytical views have been validated, the results can be translated into business insights.

Instead of simply reporting:

"The average performance rating is 3.8."

we can interpret the result in context:

"The organization's overall employee performance is relatively strong, but differences between departments may indicate areas where additional management attention or performance support is required."

Business insights should answer questions such as:

What is happening?
Why does it matter?
Which areas are performing well?
Which areas require attention?
What action could management take?

The goal is to move from descriptive metrics to decision-support information.

Purpose:
Turn validated analytical results into conclusions that can support business decisions.
<img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/0e3a60f6-9be3-44ae-abcb-cf35ef7194ed" />

Complete Project Architecture

The complete project can therefore be represented as:

                  BUSINESS VALIDATION
                          
Do the results make business sense?
   >>
                   BUSINESS INSIGHTS
                          
Findings
   >>
Recommendations
   >>
Decision Support
The Complete Data-to-Decision Journey

The project ultimately follows this principle:

Trusted Production Data
    >>
Connected Data
    >>
KPIs
    >>
Analytical Views
    >>
Validated Results
    >>
Business Insights
    >>
Better Decisions

This demonstrates a workflow where SQL Server is not only used for storing data, but also for data quality, relational modeling, analytical processing, KPI development, and business intelligence.
