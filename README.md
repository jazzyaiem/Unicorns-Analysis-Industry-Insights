# **Unicorns Analysis: Industry Insights**

This project analyzes trends in the unicorn industry, focusing on the number of unicorns created across various industries in 2019, 2020, and 2021. It identifies the top-performing industries and provides insights into the average valuation of unicorns within these industries.

## **Objective**

The purpose of this analysis is to:
1. Identify the top 3 industries with the most unicorns created between 2019 and 2021.
2. Calculate the number of unicorns created in each year for these top industries.
3. Compute the average valuation of unicorns in billions for each industry.

## **Dataset**

The analysis uses the following tables:
- **`industries`**: Contains the `company_id` and the industry each company belongs to.
- **`dates`**: Contains the `company_id` and the date the company became a unicorn.
- **`funding`**: Contains the `company_id` and the valuation of each company.
- **`companies`**: Contains the `company_id`, `company` name, and additional company information.

## **SQL Queries**

The following SQL queries were executed to gather the necessary information:

### **1. Count the Number of Unicorns by Industry and Year**

This query calculates the number of unicorns created in each industry in the years 2019, 2020, and 2021.

```sql
WITH unicorn_counts AS (
    SELECT 
        i.industry, 
        EXTRACT(YEAR FROM d.date_joined) AS year, 
        COUNT(*) AS num_unicorns
    FROM 
        industries i
    JOIN dates d ON i.company_id = d.company_id
    WHERE EXTRACT(YEAR FROM d.date_joined) IN (2019, 2020, 2021) 
    GROUP BY i.industry, EXTRACT(YEAR FROM d.date_joined)
)
SELECT * FROM unicorn_counts;
```
- **Explanation:**
  - This query extracts the year from the date_joined field to calculate the number of unicorns per industry and year.
  - EXTRACT(YEAR FROM d.date_joined) is used to get the year from the date.
  - The query filters the data for the years 2019, 2020, and 2021.

### **2. Find the Top 3 Industries Based on Total Unicorns**

This query identifies the top 3 industries based on the total number of unicorns created over the three years.

```sql
WITH unicorn_counts AS (
    SELECT 
        i.industry,
        EXTRACT(YEAR FROM d.date_joined) AS year,
        COUNT(*) AS num_unicorns
    FROM 
        industries i
    JOIN dates d ON i.company_id = d.company_id
    WHERE EXTRACT(YEAR FROM d.date_joined) IN (2019, 2020, 2021)
    GROUP BY i.industry, EXTRACT(YEAR FROM d.date_joined)
)
SELECT 
    industry,
    SUM(num_unicorns) AS total_unicorns
FROM unicorn_counts
GROUP BY industry
ORDER BY total_unicorns DESC
LIMIT 3;
```
- **Explanation:**
  - This query sums the unicorn counts from the previous query to find the total number of unicorns for each industry.
  - The results are ordered in descending order, and the LIMIT 3 statement ensures that only the top 3 industries are returned.

##**3. Get Industry, Year, Number of Unicorns, and Average Valuation**
The final query retrieves the number of unicorns, average valuation, and the year they were created for the top 3 industries.

```sql
WITH unicorn_counts AS (
    SELECT 
        i.industry,
        EXTRACT(YEAR FROM d.date_joined) AS year,
        COUNT(*) AS num_unicorns
    FROM 
        industries i
    JOIN dates d ON i.company_id = d.company_id
    WHERE EXTRACT(YEAR FROM d.date_joined) IN (2019, 2020, 2021)
    GROUP BY i.industry, EXTRACT(YEAR FROM d.date_joined)
),
top_industries AS (
    SELECT 
        industry,
        SUM(num_unicorns) AS total_unicorns
    FROM unicorn_counts
    GROUP BY industry
    ORDER BY total_unicorns DESC
    LIMIT 3
)
SELECT 
    i.industry,
    EXTRACT(YEAR FROM d.date_joined) AS year,
    COUNT(*) AS num_unicorns,
    ROUND(AVG(f.valuation) / 1e9, 2) AS average_valuation_billions
FROM 
    industries i
JOIN dates d ON i.company_id = d.company_id
JOIN funding f ON i.company_id = f.company_id
WHERE 
    i.industry IN (SELECT industry FROM top_industries)
    AND EXTRACT(YEAR FROM d.date_joined) IN (2019, 2020, 2021)
GROUP BY 
    i.industry, EXTRACT(YEAR FROM d.date_joined)
ORDER BY 
    EXTRACT(YEAR FROM d.date_joined) DESC,
    num_unicorns DESC;
```
- **Explanation:**
  - This query combines the previous CTEs to calculate the number of unicorns and average valuations for each year in the top 3 industries.
  - It uses AVG(f.valuation) to calculate the average valuation, converts it to billions, and rounds it to two decimal places.
 
## **Final Output**


| Industry                        | Year | Number of Unicorns | Average Valuation (Billions) |
|----------------------------------|------|---------------------|------------------------------|
| Fintech                         | 2021 | 138                 | 2.75                         |
| Internet software & services    | 2021 | 119                 | 2.15                         |
| E-commerce & direct-to-consumer | 2021 | 47                  | 2.47                         |
| Internet software & services    | 2020 | 20                  | 4.35                         |
| E-commerce & direct-to-consumer | 2020 | 16                  | 4                            |
| Fintech                         | 2020 | 15                  | 4.33                         |
| Fintech                         | 2019 | 20                  | 6.8                          |
| Internet software & services    | 2019 | 13                  | 4.23                         |
| E-commerce & direct-to-consumer | 2019 | 12                  | 2.58                         |

 
## **Visualizations**
### Unicorns by Industry and Year (2019–2021)
<img src = "https://github.com/user-attachments/assets/ecd2a77a-12a4-4f84-900d-3570c2efb6dc" width = "600">

This chart shows the number of unicorns created in **Fintech**, **Internet Software & Services**, and **E-commerce & Direct-to-Consumer** from 2019 to 2021. The bars represent the number of unicorns each year, with darker colors indicating more unicorns:

- **Fintech:**
  - 2019: Few unicorns, light color.
  - 2020: Small increase.
  - 2021: Significant rise, darkest color.
    
- **Internet Software & Services:**
  - 2019: Few unicorns.
  - 2020: Moderate growth.
  - 2021: Major increase, dark color.
    
- **E-commerce & Direct-to-Consumer:**
  - 2019-2021: Steady growth, with a notable spike in 2021.
 
### **Average Valuation by Industry (2019 -2021)**
    
<img src= "https://github.com/user-attachments/assets/734bb5f3-058e-4bc3-8340-2103d86af42b" width = "600">

This line plot shows the average valuation of unicorns by industry for the years 2019-2021.

**Key Observations**:
- **Fintech** had the highest valuation in 2019, but declined in 2020 and 2021.
- **Internet Software & Services** had a steady decline in valuation.
- **E-commerce & Direct-to-Consumer** experienced growth, especially in 2021.

## **Tools and Technologies**

- **Database**: PostgreSQL
- **SQL Functions**: `EXTRACT()`, `ROUND()`, `COUNT()`, `AVG()`, `SUM()`
- **Libraries**:
  - `pandas` for data analysis
  - `matplotlib` and `seaborn` for visualization

## **Conclusion**

- The **top 3 industries** producing the most unicorns were identified from 2019 to 2021.
- **Average valuation** of unicorns within these industries has been calculated, with results in billions of dollars.
- This analysis provides valuable insights into high-growth industries and can assist investors in making informed decisions based on unicorn emergence trends.



 
  




