# Live Dashboard
[View Tableau Dashboard](https://public.tableau.com/app/profile/ziang.liu5667/viz/salestransactiondata/MonthlySalesDataMonitoring)

# Overview
This project analyzes and monitors the monthly sales performance of a sample company using Tableau. It aims to provide a interactive dashboard which enables business stakeholders to track KPIs, identify trends, and uncover insights for better decision-making. This README file shows the data visualizations for June as an example. The full data for all months can be explored via the dashboard link above.

# Questions
Below are the questions I want to answer:
1. How is total sales revenue and paying customers trending month-over-month? 
2. How does the Average Order Value (AOV) vary across months?
3. How do installment plans affect purchasing behavior and AOV?
4. How does sales performance vary over time across different regions?
5. How do different installment plans affect total sales and customer adoption?

# Tools
- Tableau: I used Tableau to connect datasets, clean data, visulize the results and build the dashboard
- GitHub: For version control

# Data Preparation

The sales data from April to July was first combined with the August dataset using a union operation, then joined with the sales employees dataset on sales id to enrich the information. The date format was standardized, and the month was extracted as parameter, enabling monthly trend analysis.

Since the August sales data lacked region details, the region value from the employees dataset was used in the final dataset. A hierarchy was then created — Region → Province → Team → Sales ID — to enable drill-down analysis in Tableau and support more granular insights.

# Analysis

## 1. How is total sales revenue and paying customers trending


To analyze the month-over-month trend of total sales revenue and paying customers, both metrics were aggregated by region, and the month-over-month growth rates were calculated. A donut chart was then created to visualize the comparison, providing a clear overview of the overall sales performance.

### Results

![total sales revenue](Images/sales_amount.png)
![total paying customers](Images/paying_customer.png)

### Insights
- East China contributes the largest share of total sales and revenue at approximately 57%, followed by South China at around 24%, while Northwest China contributes the least at about 18%.
- Both total sales and revenue show a consistent month-over-month increase, indicating steady growth in the company’s market presence.


## 2. How does the Average Order Value (AOV) vary across months

After removing all null values, the Average Order Value (AOV) was calculated by dividing total revenue by the number of paying customers. Analyzing Average Order Value (AOV) helps evaluate customer purchasing behavior and revenue efficiency.

### Results
![Average Order Value](Images/average_order_value.png)

### Insights:
- The analysis shows that the average order value has been steadily decreasing over the months.


## 3. How do installment plans affect purchasing behavior and AOV

The Average Order Value (AOV) was aggregated by installment plan to analyze the impact of different payment options on customer purchasing behavior. This helps identify whether customers tend to spend more when choosing higher installment plans compared to paying in full.

### Results
![Average Order Value By Installment](Images/aov_by_installment.png)

### Insights
- Installment plans of 6, 12, and 18 months show significantly higher Average Order Value compared to 1, 3, and 9 months, with the 12-month installment plan resulting in the highest AOV overall.


## 4. How does sales performance vary over time across different regions
The sales amount, number of paying customers, Average Order Value (AOV), and their respective month-over-month (MoM) changes were visualized using bar charts, aggregated by different regions. The sales amount was aggregated by region and visualized using a line chart to illustrate the trend.

### Results
![Sales Detail by Province](Images/sales_detail_by_province.png)
![Regional Sales Trend](Images/regional_sales_trend.png)

### Insights
- The results show some rapid fluctuations, roughly following a 5-day cyclical pattern, but generally align with the insights mentioned above.
- East China contributes the most, sales amount and number of paying customers are increasing, while AOV is decreasing.
- Overall, there is minimal regional variation within the company.

  
## 5. How do different installment plans affect total sales and customer adoption
The sales amount, number of paying customers, average order value (AOV), and their respective month-over-month (MoM) changes were visualized using bar charts, aggregated by different installment plans. Additionally, the sales amount was visualized with a line chart to illustrate its trend across installment options.

### Results

![Sales Detial by Installment](Images/sales_detail_by_installment.png)
![Sales Trend by Installment](Images/sales_trend_by_installment.png)

### Insights
- Installment plans of 6, 12, and 18 months contribute the most to total sales, with higher numbers of paying customers and AOV.
- Among them, the 12-month plan achieves the highest sales amount and AOV overall.


# Insights

This project provided several insights into the company's sales performance:

- The company shows steady month-over-month growth in both total sales and revenue.
- East China contributes the largest share (~57%) of total sales and revenue, followed by South China (~24%), while Northwest China lags behind (~18%).
- Despite overall growth, the average order value (AOV) has been steadily decreasing over the months.
- Installment plans significantly influence purchasing behavior, with 6-, 12-, and 18-month plans generating higher AOVs compared to shorter plans (1, 3, 9 months).
- Among all installment options, the 12-month plan achieves the highest sales amount and the highest AOV overall.
- Longer installment plans (6, 12, 18 months) also contribute the most to total sales due to higher customer uptake and spending.
  
# Business Recommendations
- **Capitalize on East China’s dominance**  
  - Strengthen marketing and promotions to sustain growth in East China.  
  - Use the successful strategies from East China as a reference to improve performance in other regions.

- **Improve Sales in Low-Performing Regions**  
  - Investigate why sales are lower in Northwest China (e.g., pricing, competition, distribution).  
  - Consider targeted promotions, discounts or localized marketing campaigns to boost demand.

- **Address the decline in Average Order Value (AOV)**  
  - Introduce bundled offers, loyalty rewards, or discounts on higher-value items to encourage larger purchases.  
  - Analyze customer segments to understand why spending per order is decreasing.  

- **Promote Longer Installment Plans**  
  - Experiment with exclusive incentives (e.g., cashback or fee waivers) for customers choosing longer installment plans. 
  - Highlight the 12-month plan specifically since it delivers the best overall performance.


