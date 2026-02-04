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
LIMIT 25


/* “From salary data, the highest-paying analyst skills are actually engineering-focused like PySpark, Databricks, and Kubernetes. 
This shows the market is shifting analysts toward analytics engineering and ML-driven roles, where analysts build pipelines and 
scalable data platforms rather than just dashboards.”

“The salary data also shows that skills related to big data platforms and distributed systems—like Couchbase, Elasticsearch, 
PostgreSQL, and Databricks—command significantly higher salaries. This suggests organisations are paying a premium for analysts 
who can work with large-scale, high-performance data environments rather than traditional relational databases. 
It reflects the shift toward real-time analytics, scalable cloud warehouses, and large data volumes that require engineering-level 
skills to manage.”

“Another key trend is that machine learning and advanced analytics tools like scikit-learn, DataRobot, Jupyter, Pandas, and NumPy 
are strongly associated with higher salaries. This indicates that analysts who can go beyond descriptive reporting into predictive 
analytics, experimentation, and modelling are positioned closer to data scientist roles and therefore command higher compensation.”

“Overall, the data shows that the highest-paying analyst roles combine engineering, big data platforms, and machine learning skills. 
This highlights a clear market shift where analysts are expected to build scalable pipelines and advanced analytical models, 
not just dashboards.”

*/