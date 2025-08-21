# Overview
This project analyzes and monitors the monthly sales performance of a sample company using Tableau. It aims to provide a interactive dashboard which enables business stakeholders to track KPIs, identify trends, and uncover insights for better decision-making.

# Live Dashboard
[View Tableau Dashboard](https://public.tableau.com/app/profile/ziang.liu5667/viz/salestransactiondata/MonthlySalesDataMonitoring)


# Questions
Below are the questions I want to answer:
1. How is total sales revenue trending month-over-month? 
2. How many paying customers are there each month, and how is this changing?
3. How does the average order value (AOV) vary across months and regions?
4. How do installment plans affect purchasing behavior and AOV?
5. Are there regional differences in customer adoption of installment options? Which region or province is contributing most to total sales?
6. What is the customer retention rate month-over-month? Are customers returning or churning?

# Tools
- Tableau: I used Tableau to connect datasets, clean data, visulize the results and build the dashboard
- GitHub: For version control

# Data Preparation

I start by make the sales data from april to july union with the august sales data, and then connect them with the sales employees data. I changed the format of the date and extracted the month from the date.

```python
import pandas as pd
import numpy as np
import ast
import seaborn as sns
import matplotlib.pyplot as plt
from datasets import load_dataset

# Loading
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df_clean = df.drop_duplicates().copy()
df_clean = df_clean.drop_duplicates(subset=['job_title','company_name','job_country'])
df_clean['job_skills'] = df_clean['job_skills'].apply(lambda skills:ast.literal_eval(skills)if pd.notna(skills)else skills)
```


For some analysis taks, I filtered the dataset to keep only the Data Analyst roles and removed the rows that has empty salary. Then I expanded the skills column to make it clearer.

```python
df_clean = df_clean[df_clean['job_title_short']=='Data Analyst'].dropna(subset='salary_year_avg')

df_skills = df_clean.explode('job_skills')
```


# Analysis

## 1. What are the most demanded skills for the Top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I first filtered out the duplicated rows and found out the 3 most popular data roles. Next I filtered out the top 5 skills for these 3 roles. This query highlights the most popular job titles and their top skills, showing which skill I should pay attention to depending on the role I'm interested in.

View my notebook with detailed steps here:
[2_Skills_Counting.ipynb](Project/2_Skills_Counting.ipynb)


### Visualize data

```python
fig, ax = plt.subplots(len(Top_3),1)
sns.set_theme(style='ticks')

for i, title in enumerate(Top_3):
    skills = df_skills_percent[df_skills_percent['job_title_short']==title].head(5)
    # skills.plot(kind='barh', ax=ax[i], x='job_skills', y='percent', title=title)
    sns.barplot(data=skills, ax=ax[i], x='percent', y='job_skills', hue='percent', palette='dark:b_r')
    ax[i].legend().set_visible(False)
    ax[i].set_title(title)
    ax[i].set_xlim(0, 80)
    # ax[i].invert_yaxis()
    ax[i].set_ylabel("")
    ax[i].set_xlabel("")
    if i != len(Top_3)-1:
        ax[i].set_xticks([])

    for n,v in enumerate(skills['percent']):
        ax[i].text(v+1,n,f'{v:.0f}%',va='center')

fig.suptitle('Likelihood of Skills Requested in US Job Postings', fontsize=15)
fig.tight_layout(h_pad=0.5)
```

### Results

![Visualization of Top Skills](Project/Images/SkillDemand.png)

### Insights
- Python is a versatile skill, highly demanded across all three roles, but most prominently for Data Scientists (69%) and Data Engineers (63%).
- SQL is the most requested skills for Data Analyst (50%) and Data Engineers (67%). It is also highly demanded for Data Scientist (50%).
- Data Engineers require more specialized technical skills (AWS, Spark, Azure) compared to Data Analysts and Data Scientists who are expected to be more proficient in data management and analysis tools (Excel, Tableau).


## 2. How are in-demand skills trending for Data Analysts?

### Visualize Data

```python

from matplotlib.ticker import PercentFormatter
sns.lineplot(data=df_plot, dashes=False, palette='tab10')
sns.set_theme(style='ticks')
sns.despine()

plt.title('Trending Top Skills for Data Analysis in US')
plt.ylabel('Likelihood in Job Postings')
plt.xlabel('2023')
plt.legend().remove()

ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))

plt.show()


```

### Results

![Trending top Skills for Data Analysts in the US](Project/Images/Skill_Trend_DA.png)

### Insights:

- SQL remains the most consistently demanded skills through the year, although it shows a gradual decrease in demand.
- Excel experienced a decrease from September to November, but it is the second most demanded skills over the year.
- Tableau and Python shows a relatively stable demand through the year with some fluctuations, but they remain the essential skills for DA. Sas while less demanded than others, shows a slightly downward trend through the year. 


## 3. How well do jobs and skills pay for Data Analysts?
### Visualize Data

```python
sns.boxplot(data=df_US_top6, x='salary_year_avg', y='job_title_short', order=job_order)
sns.set_theme(style='ticks')

plt.title('Salary Distribution in the US')
plt.ylabel('')
plt.xlabel('Yearly Salary (USD)')
plt.xlim(0, 600000)
ticks_x = plt.FuncFormatter(lambda y,_:f'${int(y/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()
```

![Salary Distribution of Data Jobs in the US](Project/Images/salary_boxplot.png)

### Insights
- Salary ranges vary widely across job titles, with Senior Data Scientist roles offering the highest earning potential—reaching up to $600K—highlighting the industry’s strong demand for advanced expertise and experience.

- Senior Data Engineer and Senior Data Scientist positions exhibit a notable number of high-end outliers, suggesting that exceptional skills or circumstances can lead to high pay in these roles. In contrast, Data Analyst roles tend to have more stable salaries with fewer outliers.

- Median salaries rise with both seniority and specialization. Senior-level roles not only earn more on average but also show greater variability in pay, reflecting the broader range of responsibilities and influence within these positions.

## Highest Paid & Most Demanded Skills for Data Analysts
### Visualize Data
```python
fig, ax = plt.subplots(2, 1)

sns.set_theme(style='ticks')
sns.barplot(data=df_DA_top_pay, x='median', ax=ax[0], y=df_DA_top_pay.index, hue='median', palette='dark:b_r')
ax[0].legend().remove()
ax[0].set_title('Top 10 Highest Paid skills for Data Analysts')
ax[0].set_ylabel('')
ax[0].set_xlabel('')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x,_: f'${int(x/1000)}K'))

sns.barplot(data=df_DA_skills, x='median', y=df_DA_skills.index, ax=ax[1], hue='median', palette='light:b')
ax[1].set_ylabel('')
ax[1].legend().remove()
ax[1].set_title('Top 10 Most In-Demand Skills for Data Analysts')
ax[1].set_xlabel('Median Salary USD')
ax[1].set_xlim(ax[0].get_xlim())
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x,_: f'${int(x/1000)}K'))

plt.tight_layout()
plt.show()
```

![The Highest Paid & Most Demanded Skills for Data Analysts](Project/Images/HighestPaid_MostDemandedSkills.png)

### Insights
- The top graph shows specialized technical skills like mxnet, bitbucket, hugging face and dplyr are linked to higher salary levels, with some roles paying up to $200K, suggesting that mastering advanced tools can significantly boost earning potential.

- The bottom graph highlights that foundational skills like Python, R, SQL server, and Tableau are the most in-demand, even though they may not lead to the highest salaries. This shows the importance of these core skills for data analysis roles.

- A clear contrast exists between the highest-paying and most demanded skills. To improve career competency, data analysts should aim to build a well-rounded skill set that combines high-value specialized expertise with widely required foundational tools.

## 4. What is the most optimal skills to learn for Data Analysts?

#### Visualize Data
```python
from adjustText import adjust_text
from matplotlib.ticker import PercentFormatter

sns.scatterplot(data=skills_stat, x='skill_percentage', y='median_salary')
sns.despine()
sns.set_theme(style='ticks')

texts = []
for i,txt in enumerate(skills_stat.index):
    texts.append(plt.text(skills_stat['skill_percentage'].iloc[i],skills_stat['median_salary'].iloc[i],txt))


adjust_text(texts, arrowprops=dict(arrowstyle='->',color='gray'))
plt.xlabel('Percentage of Job Postings')
plt.ylabel('Median Yearly Salary')
plt.title(f'Most  for Data Analysts')

ax = plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K'))
ax.xaxis.set_major_formatter(PercentFormatter(decimals=0))
plt.tight_layout()
plt.show()
```

#### Results

![Most optimal skills for Data Analysts](Project/Images/Optimal_Skill.png)

#### Insights
- The scatter plot reveals that programming skills (blue),particularly Python and R, tend to be associated with higher salaries, with Python offering the highest median salary despite a moderate demand.

- Analyst tools (orange) such as Tableau, Power BI, and Excel appear frequently in job postings, especially Excel, which leads in demand but offers lower median salaries. In contrast, Tableau has a good balance between demand and salary, indicating strong value in visualization tools.

- Database technologies like SQL and SQL Server also rank high. While SQL is the most in-demand skill overall, it has a slightly lower salary than SQL Server, which offers a higher median salary at lower demand. This pattern reflects the strong demand for data management capabilities across analytics roles.


# Insights

This project provided several insights into the data job market for analysts:

- Core skills like SQL, Python, and Excel remain highly in demand, especially for Data Analysts, Data Scientists, and Data Engineers. However, specialized tools such as Hugging Face, Bitbucket, and mxnet command higher salaries despite being less frequently requested.

- Visualization shows that Python consistently offers one of the highest median salaries, while SQL remains the most demanded skill overall. Tableau and Power BI strike a strong balance between demand and compensation, making them valuable tools for aspiring analysts.

- Senior roles, especially in Data Science and Engineering, show significantly higher and more variable salary ranges, indicating greater value and reward for expertise and experience.

- Ultimately, the most optimal skillset for a data analyst includes a mix of high-demand foundational tools and high-paying niche skills. Staying current with market trends and diversifying technical competencies is key to increasing employability and maximizing salary potential.

# What I Learned From This Project
- The importance of aligning one's skills with market demand. Understanding the relationship between skill demand, salary, and job availability allows for more strategic career planning in the tech industry.
- How to perform data analysis using Python.
- Data cleaning and filtering is so important before analyzing anything. It could remove all the unnecessary and wrong data to make the analysis more reliable.
- Visualization is the last but most important step to show your results. The axis, color, title, theme etc, they all reflect how you express your insights.
- Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.
- How to balance the overview of the landscape while diving into each analysis.
