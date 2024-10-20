# Introduction

Investigating the data analyst job market by analysing the top-paying jobs, in-demand skills and where high demand meets high salary in data analytics. 

SQL queries? Check them out here: [project_sql](/project_sql/) 

# Background
This project was created to explore the data analyst job market more effectively, focusing on the top-paid and in-demand skills. This data is from Luke Barousse's [SQL Course](https://lukebarousse.com/sql)

### This project focused on answering the following questions:
1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn for a data analyst looking to maximize job market value?

# Tools Used
The tools that were utilised for this project were:
- **SQL** (Structure Query Language): Programming language used manage data, interact with the database, and use queries to answer the project's questions. 
- **PostgreSQL**: Database management system used to store, query and manipulate data.
- **Visual Studio Code**: Streamlined code editor used to manage the database and execute SQL queries.
- **Git & GitHub**: Visual platform used to share SQL scripts and analysis.

# Analysis
Each query was designed to build on the previous to target the data for the job search. These queries focused on remote data analyst jobs.

### 1. Top Paying Data Analyst Jobs
To identify the top 10 highest-paying Data Analyst roles that are available remotely, the data for data analyst roles was filtered by average yearly salary and location. This query illustrated the highest paid opportunities in the field.

 ```sql
 SELECT 
    job_id,
    job_title,
    company_dim.name AS company_name,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date
FROM
  job_postings_fact
LEFT JOIN
    company_dim ON job_postings_fact.company_id=company_dim.company_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```

![Top Paying Roles](Assets\top_jobs.png)
*Top 10 salaries for data analyst jobs for 2023; Generated with Excel using results from the SQL query.*

The results illustrate the following:
- **Wide Salary Range:** The Top 10 data analyst roles have a diverse salary range from $184,000 to $650,000. This exemplifies a significant salary potential in this field. 
- **Job Title Variety:** A broad range of job titles were identified, indicating the diverse roles and specialisations within data analytics.

### 2. Skills for Top Paying Jobs
To identify the specific skills required for these roles, the jobs postings and skills data were joined to provide a detailed look at which high-paying jobs demand certain skills. This would assist job seekers to understand which skills to develop that align with top salaries.

```sql
WITH top_jobs AS (
  SELECT 
      job_id,
      job_title,
      company_dim.name AS company_name,
      salary_year_avg
  FROM
    job_postings_fact
  LEFT JOIN
      company_dim ON job_postings_fact.company_id=company_dim.company_id
  WHERE
      job_title_short = 'Data Analyst'
      AND job_location = 'Anywhere'
      AND salary_year_avg IS NOT NULL
  ORDER BY
      salary_year_avg DESC
  LIMIT 10
)
SELECT 
  top_jobs.*,
  skills
FROM top_jobs
INNER JOIN skills_job_dim ON top_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
  salary_year_avg DESC;
```
![Top Skills](Assets\top_skills.png)
*Skill count for the Top 10 paying data analyst jobs for 2023; Generated with ChatGPT using results from the SQL query.*

The most frequently mentioned skill was SQL. It is critical for querying databases and handling large datasets, making it an essential skill for data analysts. Another highly requested skill, Python is popular due to its versatility in data manipulation, analysis, and machine learning.

The skill breakdown depicted in this graph highlights the blend of programming, statistical analysis, and cloud-based tools that are essential for modern Data Analyst roles.

### 3. In-Demand Skills for Data Analyst
The Top 5 in-demand skills for remote data analyst roles was then investigated. 

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5

```

| Skills    | Demand Count |
|-----------|--------------|
| SQL       | 7,291        |
| Excel     | 4,611        |
| Python    | 4,330        |
| Tableau   | 3,745        |
| Power BI  | 2,609        |
*Summary of the Top 5 skills in data analyst job postings*

This query identifed that fundamental skills in SQL and Excel are in high demand for data analyst jobs. These tools can be utilised for database management and querying as well as basic analysis. Programming and visualisation tools such as Python, Tableau and Power BI illustrate the increasing importance of data storytelling and decision support for data analyst roles.  

### 4. Skills Based on Salary
The different skills were then compared to average salaries for remote data analysts to identify the most financially rewarding skills to acquire or improve.

```sql
SELECT 
   skills,
   ROUND(AVG(salary_year_avg),0) AS avg_salary
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
LIMIT 10
```
| Skills         | Average Salary ($) |
|----------------|--------------------|
| PySpark        | 208,172            |
| Bitbucket      | 189,155            |
| Watson         | 160,515            |
| Couchbase      | 160,515            |
| DataRobot      | 155,486            |
| GitLab         | 154,500            |
| Swift          | 153,750            |
| Jupyter        | 152,777            |
| Pandas         | 151,821            |
| Elasticsearch  | 145,000            |
*Average salary for the top 10 paying skills for data analysts*

The data reveals that high-paying roles are associated with advanced and specialized tools in big data, AI, cloud, and software collaboration. Analysts who master these technologies are highly valued for their ability to drive business insights and work in multidisciplinary environments.

### 5. Optimal Skills to Learn
Combining analysis of the demand and salary data, this query aim to identify skills that are both high in demand and have high salaries, to provide a strategic focus for career development.  

```sql
WITH skills_demand AS (
  SELECT
    skills_dim.skill_id,
		skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count
  FROM
    job_postings_fact
	  INNER JOIN
	    skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
	  INNER JOIN
	    skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
  WHERE
    job_postings_fact.job_title_short = 'Data Analyst'
		AND job_postings_fact.salary_year_avg IS NOT NULL
    AND job_postings_fact.job_work_from_home = True
  GROUP BY
    skills_dim.skill_id
),
-- Skills with high average salaries for Data Analyst roles
-- Use Query #4 (but modified)
average_salary AS (
  SELECT
    skills_job_dim.skill_id,
    AVG(job_postings_fact.salary_year_avg) AS avg_salary
  FROM
    job_postings_fact
	  INNER JOIN
	    skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
	  -- There's no INNER JOIN to skills_dim because we got rid of the skills_dim.name 
  WHERE
    job_postings_fact.job_title_short = 'Data Analyst'
		AND job_postings_fact.salary_year_avg IS NOT NULL
    AND job_postings_fact.job_work_from_home = True
  GROUP BY
    skills_job_dim.skill_id
)
-- Return high demand and high salaries for 10 skills 
SELECT
  skills_demand.skills,
  skills_demand.demand_count,
  ROUND(average_salary.avg_salary, 2) AS avg_salary --ROUND to 2 decimals 
FROM
  skills_demand
	INNER JOIN
	  average_salary ON skills_demand.skill_id = average_salary.skill_id
-- WHERE demand_count > 10
ORDER BY
  demand_count DESC, 
	avg_salary DESC
LIMIT 10;
```

| Skills     | Demand Count | Average Salary ($) |
|------------|--------------|--------------------|
| SQL        | 398          | 97,237.16          |
| Excel      | 256          | 87,288.21          |
| Python     | 236          | 101,397.22         |
| Tableau    | 230          | 99,287.65          |
| R          | 148          | 100,498.77         |
| Power BI   | 110          | 97,431.30          |
| SAS        | 63           | 98,902.37          |
| PowerPoint | 58           | 88,701.09          |
| Looker     | 49           | 103,795.30         |
*Most optimal skills for data analysts based on demand*

The data suggests that foundational skills like SQL and Excel are widely demanded but don't necessarily lead to the highest salaries. Technical skills such as Python and R, and visualization tools such as Looker and Tableau command higher pay. This reflects the increase of complexity and importance of handling, analyzing, and communicating insights from data. Specialized tools like SAS also offer high rewards for those working in niche sectors.

# What You Learned
This course provided me with an introduction to develop my SQL skills. This includes Query Crafting, Data Aggregation and Analytical Problem Solving.

# Insights
 Through this investigation based on the data analyst job market of 2023, the following were determined:
 
1. **Top Paying Data Analyst Jobs**: Data analyst roles have a diverse salary range and job titles, with the highest salary potenial being $650,000.00.

2. **Skills for Top Paying Jobs**: The highest paying jobs require proficiency in SQL, suggesting it's a critical skill for earning top salary. 

3. **In-Demand Skills for Data Analyst**: SQL is also the most demanded skill in the data analyst job market. It is therefore essential for job seekers. 

4. **Skills Based on Salary**: Specialised skills are associated with high-paying roles, indicating a premium on niche expertise. 

5. **Optimal Skills to Learn**: SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximise their market value. 

# Conclusion
- This project provided an introduction to develop my SQL skills and provide valuable insights into the data analyst job market. The findings provide a guide in priortising skill development and job search efforts. 
