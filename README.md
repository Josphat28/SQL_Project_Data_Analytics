# Introduction
As a data analyst and active job seeker, I’ve spent countless hours navigating job boards, dissecting postings, and chasing clarity in a crowded market. What I found was noise, vague requirements, inflated titles, and skill lists that didn’t match the pay. So I built this project to cut through it.

# Background

This project was born out of frustration. As a data enthusiast actively seeking roles, I spent months navigating vague job descriptions, inflated titles, and skill lists that didn’t match the pay. I realized most job seekers face the same confusion and that clarity was missing from the market.

So I built this project to answer five critical questions about the data analytics job landscape:  
1. What are the top paying Data Analyst jobs?  
2. Which skills are required for these top paying jobs?  
3. Which skills are most in demand for Data Analysts?  
4. Which skills are associated with higher salaries?  
5. Which are the most optimal skills to learn?

# Tools Used

To dissect the data analytics job market with precision, I relied on a focused technical stack:

- **SQL** - My primary tool for querying and extracting insights from job posting data.
- **PostgreSQL** - A robust database engine suited for handling large, structured datasets.
- **Visual Studio Code** - Used for writing, debugging, and organizing SQL scripts efficiently.
- **Git & GitHub** - Enabled version control, collaboration, and transparent tracking of my analysis workflow.

# Analysis

This section breaks down the core questions driving this project—each answered through targeted SQL queries and data-backed insights. The goal wasn’t just to explore the job market, but to extract patterns that help job seekers make smarter, more strategic decisions.

## 1. 💰Top Paying Data Analyst Positions 2023
To identify the highest paying jobs, I filtered Data Analyst Positions by Salary and Location, focusing on Remote jobs. This Query highlights the high paying opportunities in the field,
``` sql
SELECT
    job_title,
    job_location,
    job_schedule_type,
    round(salary_year_avg,0),
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
    ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_work_from_home = TRUE 
    AND salary_year_avg IS NOT NULL 
    AND job_title_short = 'Data Analyst'
ORDER BY salary_year_avg DESC
LIMIT 10;
```
### 💼 Top Paying Remote Data Roles

This chart highlights the highest-paying remote data positions currently on the market. 

![Top Paying Roles](Project_sql/assets.sql/1_top_paying_remote_jobs.png)

*Bar chart showing top 10 highest-paying remote data roles.*

🔍 Key Findings.
1. **Salary Range**: Compensation varied significantly, from $184K to $650K indicating significant salary potential.
2. **Diverse Employers**: Companies like Meta, AT&T, Pinterest and SmartAsset appeared in the top results, indicating that both tech giants and niche firms are investing heavily in remote analytics talent.
3. **Job Title Variety**: While all roles fell under the "Data Analyst" umbrella, titles ranged from entry-level analyst to Director and Principal roles, showing that remote opportunities span across seniority levels.

## 2. 💼 Top Paying Job Skills

This query reveals which specific skills are tied to the highest-paying Data Analyst roles. By analyzing the top 10 remote jobs by salary, I identified the tools employers reward most consistently.
``` sql
WITH top_paying_jobs AS(
    SELECT
        job_id,
        job_title_short,
        round(salary_year_avg,0) AS salary_year_avg,
        name as company_name
    FROM job_postings_fact
    LEFT JOIN company_dim
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_work_from_home = TRUE
        AND salary_year_avg IS NOT NULL
        AND job_title_short = 'Data Analyst'
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT 
    top_paying_jobs.job_id,
    job_title_short,
    salary_year_avg,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
ORDER BY salary_year_avg DESC;
```
This bar chart visualizes the most common skills found in the top 10 highest-paying Data Analyst roles.

![Skill Count](Project_sql/assets.sql/2_skill_count_top10_data_analyst_jobs_labeled.png)  
*Bar chart showing frequency of skills in top-paying Data Analyst job postings.*

💡 Key Insights.
- **Universal Core Skills:** SQL is non-negotiable. Every top-paying role listed it. Its strongest signal of a core competency. Python and Tableau appear almost as often, cementing their place in the “triad” of essential data analysis tools.
- **Secondary Tier Skills:** R, Excel, and Cloud data platforms (Azure, AWS, Snowflake) show up repeatedly, suggesting that employers prize flexibility with both statistical languages and enterprise data stacks.
- **Visualization Stack:** Tableau, Power BI, and occasionally Excel/PowerPoint appear together. Employers expect analysts to both crunch numbers and tell a clear story visually.


## 3. 📈 Top Demanded Skills

To identify the most sought-after skills in the data analytics job market, I analyzed all job postings with salary data, focusing on frequency of skill mentions. The results below highlight the tools employers consistently prioritize.

**Data Analyst Roles (Top 5 Skills by Demand)**  
| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 92,628       |
| Excel     | 67,031       |
| Python    | 57,326       |
| Tableau   | 46,554       |
| Power BI  | 39,468       |

**All Data Roles (Top 5 Skills by Demand)**  
| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 385,750      |
| Python    | 381,863      |
| AWS       | 145,718      |
| Azure     | 132,851      |
| R         | 131,285      |

💡 Key Insights:
- **SQL and Python are universal** - Their dominance across both tables confirms they’re foundational across all data roles.
- **Analyst roles favor accessibility** - Excel, Tableau, and Power BI dominate because they bridge technical analysis with business communication.
- **Cloud is the new baseline** - AWS and Azure’s prominence in broader data roles signals a shift toward cloud-native analytics as a core expectation.

## 4. 💰 Skills Associated with Higher Salaries

This query identifies which technical skills are most strongly associated with higher-paying Data Analyst roles. By calculating average salaries per skill, I surfaced the tools that consistently command premium compensation.
``` sql
SELECT
    job_title_short,
    skills,
    round(avg(salary_year_avg),0) as avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE salary_year_avg IS NOT NULL
    AND job_title_short = 'Data Analyst' 
GROUP BY skills, job_title_short
ORDER BY avg_salary DESC
LIMIT 25;
```

**Top 25 Skills by Average Salary**  
| Skill         | Average Salary |
|---------------|----------------|
| SVN           | $400,000       |
| Solidity      | $179,000       |
| Couchbase     | $160,515       |
| DataRobot     | $155,486       |
| Golang        | $155,000       |
| MXNet         | $149,000       |
| Dplyr         | $147,633       |
| VMware        | $147,500       |
| Terraform     | $146,734       |
| Twilio        | $138,500       |
| GitLab        | $134,126       |
| Kafka         | $129,999       |
| Puppet        | $129,820       |
| Keras         | $127,013       |
| PyTorch       | $125,226       |
| Perl          | $124,686       |
| Ansible       | $124,370       |
| Hugging Face  | $123,950       |
| TensorFlow    | $120,647       |
| Cassandra     | $118,407       |
| Notion        | $118,092       |
| Atlassian     | $117,966       |
| Bitbucket     | $116,712       |
| Airflow       | $116,387       |
| Scala         | $115,480       |

💡 Key Insights:
- **SVN stands out** - Though rare, its $400K average suggests niche legacy systems still command top dollar in specific contexts.
- **AI and cloud tools dominate** - Skills like DataRobot, MXNet, Hugging Face, and Terraform reflect the rising value of automation, machine learning, and infrastructure-as-code.
- **Version control and orchestration matter** - GitLab, Puppet, and Airflow show that employers reward analysts who understand deployment and workflow management.

## 5. 🎯 Optimal Skills to Learn

This query combines salary and demand data to identify the most strategic skills for aspiring Data Analysts. The goal: to find tools that are not only popular, but also pay well, offering the best return on learning investment.
``` sql
SELECT
    skills,
    round(avg(salary_year_avg),0) as avg_salary,
    count(skills_job_dim.job_id) as demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills
HAVING 
    (count(skills_job_dim.job_id) > 10)  
ORDER BY demand_count DESC
LIMIT 50;
```
💡 Key Insights:
- **SQL is non-negotiable**: With 398 mentions and a solid $97K average salary, SQL is the backbone of remote data analyst roles. Skip it and you're out of the game.
- **Python pays, but Snowflake and Go pay more**: Python is widely demanded (236 listings, $101K), but niche tools like Snowflake ($113K) and Go ($115K) offer sharper salary spikes with less competition.
- **Cloud and BI tools are the salary accelerators**: Azure ($111K), AWS ($108K), and Power BI ($97K) consistently appear in high-paying roles. Best option is to stack them with core analytics skills for maximum leverage.

# 📚 Learned Lessons
This project sharpened both my technical and strategic thinking. Here’s what I took away:
- **SQL isn’t just querying, it’s storytelling**. Crafting precise queries taught me how to extract meaning, not just data.
- **Data without context is noise**. Salary and demand only matter when tied to real-world decisions.
- **Visualization is leverage**. Charts and tables helped me communicate insights that raw numbers couldn’t.
- **The job market is a dataset**. With the right tools, even career confusion becomes a solvable problem.
# Conclusion
## Insights
1. **What are the top paying Data Analyst jobs?**  
   Remote roles with titles like Principal Analyst and Director reached up to $650K, especially at firms like Meta, AT&T, and Pinterest, proving that high compensation spans both tech giants and niche players.
2. **Which skills are required for these top paying jobs?**  
   SQL, Python, and Tableau appeared in nearly every top-paying listing, with supporting tools like Excel, Pandas, Azure, and AWS forming a consistent secondary stack.
3. **Which skills are most in demand for Data Analysts?**  
   SQL leads by a wide margin, followed by Excel, Python, Tableau, and Power BI thus highlighting a strong employer preference for tools that balance technical depth with business accessibility.
4. **Which skills are associated with higher salaries?**  
   Go, Snowflake, and Hadoop top the pay scale, each averaging over $113K. Cloud platforms like Azure and AWS also rank high, confirming their value in modern data workflows.
5. **Which are the most optimal skills to learn?**  
   SQL and Python offer the best mix of demand and salary. But stacking them with Snowflake, Azure, and Power BI unlocks higher-paying remote roles with strategic career leverage.
## 🧠 Closing Thoughts
This wasn’t just a technical exercise, it was a strategic deep dive into the realities of the data analytics job market. I built this project to help myself and others cut through the noise, focus on what matters, and make smarter career moves.

If you’re an employer, recruiter, or fellow analyst, I hope this project shows not just what I can do, but how I think.

Thanks for reading.
