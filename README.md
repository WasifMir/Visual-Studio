# Introduction
This project analyses high-paying data analyst skills using job posting and salary datasets. The goal is to identify which technical skills correlate with higher salaries and to uncover trends in how the data analyst role is evolving toward analytics engineering and machine learning.

SQL queries? Check them out here: [project_sql folder](/project_sql/)
# Background
The data industry has changed significantly in recent years. Traditional business intelligence roles focused on SQL, Excel, and dashboards, but modern organisations increasingly expect analysts to work with cloud platforms, big data technologies, and machine learning tools.

To understand this shift, I analysed:

- The most in-demand skills from analyst job postings

- The top 25 highest-paying skills for data analysts

## The questions I wanted to answer through my sql queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-payings jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

This provides insights into how skill requirements and compensation are evolving.

# Tools I Used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- **SQL :**  The backbone of my analysis, enabling me to query the database and extract critical insights.

- **PostgreSQL :** The database management system used to store and manage job posting data efficiently.

- **Visual Studio Code :** My primary environment for database management and executing SQL queries.

- **Git & GitHub :** Used for version control, sharing SQL scripts, tracking project changes, and enabling collaboration.

# The Analysis
Each query in this project focused on investigating a specific aspect of the data analyst job market. Below is the approach I used to answer each question and extract insights from the data.

### 1. Top Paying Data Analyst Roles

To identify the highest-paying data analyst roles, I filtered job postings by average yearly salary and location, focusing specifically on fully remote positions. This query highlights the most lucrative opportunities in the market and provides insight into compensation trends for remote analytics roles.

```SQL
SELECT 
    job_id,
    job_title,
    job_location, 
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
    
FROM 
    job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id

WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL

ORDER BY 
    salary_year_avg DESC
LIMIT 10;
```
This analysis helped identify:

 - The highest-paying data analyst roles globally

 - Which companies offer premium compensation for remote analysts

 - Salary ranges for top-tier analyst positions.

 ### 2. Skills for Top Paying Jobs
By linking job postings with skills data, this analysis highlights the key skills employers look for in the highest-paying roles

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
        
    FROM 
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id

    WHERE
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL

    ORDER BY 
        salary_year_avg DESC

    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id

ORDER BY 
    salary_year_avg DESC;
```

From analysing high-paying analyst job postings, SQL and Python consistently dominate, 
followed by BI tools like Tableau. However, the interesting trend is the growing demand 
for cloud platforms like Snowflake and Azure and engineering practices like Git and CI/CD. 
This shows analyst roles are evolving toward analytics engineering, where analysts 
build scalable data models and pipelines, not just dashboards.

### 3. In-Demand Skills for Data Analysts
This SQL query identifies the most frequently requested skills in remote Data Analyst job postings. By joining job listings with associated skill tags, it counts how often each skill appears and returns the top five in-demand capabilities.

```sql
SELECT 
    skills,
    COUNT (skills_job_dim.job_id) AS 
    demand_count
FROM job_postings_fact 
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
      job_title_short = 'Data Analyst' AND
      job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY 
    demand_count DESC
LIMIT 5;
```
The output highlights which technical skills employers prioritise most, helping focus learning and upskilling efforts on areas with the highest market demand.

Key steps performed:

 - Filters job postings to Data Analyst roles that are work‑from‑home.

- Joins job listings with their associated skills.

- Aggregates and counts how often each skill appears.

- Sorts results by demand and returns the top five.

This analysis provides a data‑driven view of the skills landscape, supporting targeted career development and strategic decision‑making.

### 4. Skills Based On Salary

This SQL query identifies the top‑paying skills associated with remote Data Analyst job postings. By linking job listings with their required skills and calculating the average salary for each, it highlights which technical capabilities command the highest compensation in the market.

```sql
SELECT 
    skills,
    ROUND (AVG(salary_year_avg),0) AS avg_salary
FROM job_postings_fact 
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
      job_title_short = 'Data Analyst' 
      AND salary_year_avg IS NOT NULL
      AND job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY 
    avg_salary DESC
LIMIT 25;
```
 - From salary data, the highest-paying analyst skills are actually engineering-focused like PySpark Databricks, and Kubernetes. This shows the market is shifting analysts toward analytics engineering and ML-driven roles, where analysts build pipelines and 
scalable data platforms rather than just dashboards.

 - The salary data also shows that skills related to big data platforms and distributed systems—like Couchbase, Elasticsearch, PostgreSQL, and Databricks—command significantly higher salaries. This suggests organisations are paying a premium for analysts  who can work with large-scale, high-performance data environments rather than traditional relational databases. 

 - It reflects the shift toward real-time analytics, scalable cloud warehouses, and large data volumes that require engineering-level 
skills to manage.”

 - Another key trend is that machine learning and advanced analytics tools like scikit-learn, DataRobot, Jupyter, Pandas, and NumPy are strongly associated with higher salaries. This indicates that analysts who can go beyond descriptive reporting into predictive analytics, experimentation, and modelling are positioned closer to data scientist roles and therefore command higher compensation.

Overall, the data shows that the highest-paying analyst roles combine engineering, big data platforms, and machine learning skills. This highlights a clear market shift where analysts are expected to build scalable pipelines and advanced analytical models, not just dashboards.”


### 5 . Most Optimal Skills to Learn
This query combines skill demand and salary insights to identify the most valuable skills for remote Data Analyst positions. By using two CTEs—one measuring how often each skill appears in job postings, and another calculating the average salary associated with those skills—it highlights the intersection of market demand and earning potential.

```sql
WITH skills_demand AS 

    (SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT (skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact 
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY 
        skills_dim.skill_id

    ), 
    
    avg_salary AS ( 
        SELECT 
        skills_job_dim.skill_id,
        ROUND (AVG(salary_year_avg),0) AS avg_salary
    FROM job_postings_fact 
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY 
        skills_job_dim.skill_id
)

SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary

FROM
    skills_demand

INNER JOIN avg_salary ON skills_demand.skill_id = avg_salary.skill_id 

WHERE
    demand_count > 10
ORDER BY 
    avg_salary DESC,
    demand_count DESC
    
LIMIT 25;
```
This analysis highlights the skills that sit at the intersection of high employer demand and strong salary potential, offering a data‑driven view of where upskilling delivers the greatest strategic value. By combining frequency of appearance in job postings with the average salary associated with each skill, the results surface capabilities that are not only widely requested but also command premium compensation.

This dual‑lens approach is important because:

- **High‑demand skills** indicate what employers consistently look for when hiring Data Analysts, signalling essential competencies for employability.

- **High‑salary skills** reveal where organisations are willing to invest more, often reflecting specialised expertise, technical scarcity, or direct business impact.

- **Skills that score highly on both dimensions** represent the “sweet spot” for career growth—skills that increase both job opportunities and earning potential.

For learners, job seekers, or professionals planning their development roadmap, this combined view helps prioritise skills that offer the highest return on time invested. Instead of guessing which tools or technologies matter most, the analysis provides clear, evidence‑based guidance on where to focus to maximise market competitiveness and long‑term career value.
# What I learned 
This project pushed my SQL capabilities far beyond the basics and into the territory of real analytical craftsmanship. Each query became an opportunity to sharpen technique, structure thinking, and extract meaningful insight from messy, multi‑table data.

- **Advanced Query Engineering**  
Built fluency in constructing complex SQL logic, joining multiple tables with intention, and using WITH clauses (CTEs) to break down problems into clean, modular steps. This shifted my workflow from “write a query” to “design a solution.”

- **Aggregation as an Insight Engine**  
Strengthened my command of GROUP BY, COUNT(), AVG(), and other aggregate functions turning raw datasets into structured, decision‑ready summaries. These tools became essential for quantifying patterns, ranking skills, and comparing salary signals.

- **Analytical Thinking in Action**  
Learned to translate open‑ended business questions into precise SQL operations. Instead of querying for the sake of querying, I focused on shaping data into insights that answer real‑world questions about demand, salary, and skill value.

# Conclusions

## Insights
This project brought together real‑world job market data, structured SQL analysis, and a clear analytical framework to uncover which skills truly matter for remote Data Analyst roles. By combining demand signals with salary insights, the work moves beyond surface‑level observations and delivers a grounded, evidence‑based view of the skills that shape employability and earning power in today’s market.

The analysis revealed a clear, data‑driven picture of the capabilities that genuinely influence success in this field. By merging demand frequency with salary impact, the project highlights the skills that sit at the intersection of what employers consistently require and what they reward with higher compensation. This dual‑metric approach cuts through noise and surfaces the skills that offer the strongest return on investment—skills that expand job opportunities while also lifting earning potential. It also demonstrates how SQL can be used not just to retrieve data, but to uncover commercially meaningful patterns that support strategic decision‑making.

## Final Thoughts
Ultimately, the findings provide a practical roadmap for targeted upskilling: focus on the skills that employers consistently seek and that demonstrably lift salary potential. With this foundation, future analysis can expand into trend tracking, role comparisons, or predictive modelling—but the core principle remains the same: let the data guide the strategy.

This project showcased the power of structured SQL analysis in answering real‑world questions with clarity and evidence. From building multi‑layered CTE logic to interpreting the story behind the numbers, the work strengthened both technical capability and analytical judgement. The insights gained offer a deeper understanding of the Data Analyst job market and reinforce a mindset that will carry into future work: let the data lead, let the insights speak, and use SQL as a tool for turning complexity into direction.