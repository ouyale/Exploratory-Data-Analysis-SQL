# Data Analyst Job Market — SQL Exploration 💼 &nbsp; [![View Queries](https://img.shields.io/badge/SQL-View_Queries-orange?logo=postgresql)](Project_sql/)

![SQL](https://img.shields.io/badge/SQL-Advanced-4479A1?logo=postgresql&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?logo=postgresql&logoColor=white)
![VS Code](https://img.shields.io/badge/VS_Code-Editor-007ACC?logo=visualstudiocode&logoColor=white)
![Git](https://img.shields.io/badge/Git-Version_Control-F05032?logo=git&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-success)

> **Exploring the data analyst job market with SQL — uncovering the highest-paying roles, most in-demand skills, and optimal skills for career advancement through 5 progressive analytical queries.**

<br>

<p align="center">
  <img src="https://img.shields.io/badge/💰_Top_Salary-$115K-green?style=for-the-badge" alt="Top Salary"/>
  &nbsp;&nbsp;
  <img src="https://img.shields.io/badge/🔍_Queries-5_Analyses-blue?style=for-the-badge" alt="Queries"/>
  &nbsp;&nbsp;
  <img src="https://img.shields.io/badge/📊_Focus-Remote_Data_Analyst-orange?style=for-the-badge" alt="Focus"/>
</p>

<br>

## Table of Contents

- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Analysis](#analysis)
  - [1. Top Paying Jobs](#1-top-paying-data-analyst-jobs)
  - [2. Skills for Top Jobs](#2-skills-required-for-top-paying-jobs)
  - [3. Most In-Demand Skills](#3-most-in-demand-skills)
  - [4. Salary by Skill](#4-average-salary-by-skill)
  - [5. Optimal Skills](#5-optimal-skills)
- [Key Findings](#key-findings)
- [Technologies Used](#technologies-used)
- [How to Run](#how-to-run)
- [Author](#author)

<br>

## Problem Statement

What skills should an aspiring data analyst focus on to maximize both employability and earning potential? This project explores real job market data using **SQL** to answer five progressive questions — from identifying the highest-paying remote roles to finding the optimal intersection of high demand and high salary skills.

<br>

## Dataset

| Property | Detail |
|----------|--------|
| **Tables** | `job_postings_fact`, `company_dim`, `skills_job_dim`, `skills_dim` |
| **Focus** | Data Analyst roles, remote ("Anywhere") positions |
| **Setup** | Database creation and loading scripts in `sql_load/` |

<br>

## Analysis

### 1. Top Paying Data Analyst Jobs

Identified the **top 10 highest-paying remote** Data Analyst positions:

```sql
SELECT job_id, job_title, salary_year_avg, name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst'
  AND job_location = 'Anywhere'
  AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10
```

### 2. Skills Required for Top-Paying Jobs

Used a **CTE** to find which skills the highest-paying roles demand:

```sql
WITH top_paying_jobs AS (
    SELECT job_id, job_title, salary_year_avg, name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Analyst'
      AND job_location = 'Anywhere'
      AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT top_paying_jobs.*, skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC
```

| Rank | Skill | Avg Salary |
|------|-------|------------|
| 1 | Go | $115,320 |
| 2 | Confluence | $114,210 |
| 3 | Hadoop | $113,193 |
| 4 | Snowflake | $112,948 |
| 5 | Azure | $111,225 |

### 3. Most In-Demand Skills

Aggregated skill demand across **all remote Data Analyst** postings:

```sql
SELECT skills, COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND job_location = 'Anywhere'
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5
```

| Rank | Skill | Demand Count |
|------|-------|-------------|
| 1 | Python | 236 |
| 2 | Tableau | 230 |
| 3 | R | 148 |
| 4 | SAS | 63 |
| 5 | Looker | 49 |

### 4. Average Salary by Skill

Ranked skills by their associated **average salary**:

```sql
SELECT skills, ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
  AND salary_year_avg IS NOT NULL
  AND job_work_from_home = True
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25
```

### 5. Optimal Skills

Combined demand and salary to find the **best skills to learn** — high demand AND high pay:

```sql
SELECT skills_dim.skills, COUNT(skills_job_dim.job_id) AS demand_count,
       ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
  AND salary_year_avg IS NOT NULL
  AND job_work_from_home = True
GROUP BY skills_dim.skill_id
HAVING COUNT(skills_job_dim.job_id) > 10
ORDER BY avg_salary DESC, demand_count DESC
LIMIT 25
```

<p align="center">
  <img src="Assets/top_10_in_demand_skills_with%20their%20corresponding_salaries.png" width="700" alt="Top In-Demand Skills with Salaries"/>
</p>

<p align="center">
  <img src="Assets/Top_demand%20skills%20for%20data%20analysts.png" width="700" alt="Top Demand Skills for Data Analysts"/>
</p>

<br>

## Key Findings

1. **SQL, Python, and Tableau** are foundational — the three most demanded skills across all remote Data Analyst postings
2. **Specialized cloud/big data skills** (Go, Hadoop, Snowflake, Azure) command the highest salaries ($111K–$115K)
3. **High demand does not equal highest pay** — Python has 236 postings but niche tools like PySpark and Databricks pay more
4. **The sweet spot** is skills with both strong demand (>10 postings) and high average salary — these are the optimal learning targets

<br>

## Technologies Used

| Tool | Purpose |
|------|---------|
| SQL | Data querying & analysis |
| PostgreSQL | Database management |
| Visual Studio Code | SQL editing & execution |
| Git & GitHub | Version control |

<br>

## How to Run

```bash
# Clone the repository
git clone https://github.com/ouyale/Exploratory-Data-Analysis-SQL.git
cd Exploratory-Data-Analysis-SQL

# Set up the database
# 1. Install PostgreSQL
# 2. Run the setup scripts in sql_load/
# 3. Execute queries in Project_sql/ (1 through 5)
```

<br>

## Author

**Barbara Obayi** — Machine Learning Engineer

[![GitHub](https://img.shields.io/badge/GitHub-ouyale-181717?logo=github)](https://github.com/ouyale)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Barbara_Obayi-0A66C2?logo=linkedin)](https://www.linkedin.com/in/barbara-weroba-obayi31/)
[![Portfolio](https://img.shields.io/badge/Portfolio-ouyale.github.io-4fc3f7)](https://ouyale.github.io)

---
