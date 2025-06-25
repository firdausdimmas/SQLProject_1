# Analyzing Unicorn Companies

### Project Overview

![consulting](https://github.com/user-attachments/assets/eb1be9a4-9eb6-4eb8-b4f7-2ea1acdbea96)

Did you know that the average return from investing in stocks is 10% per year (https://www.nerdwallet.com/article/investing/average-stock-market-return) (not accounting for inflation)? But who wants to be average?! 

This project entails providing support to an investment firm by conducting analyses of trends among high-growth companies. The objective is to identify which industries are generating the highest company valuations and to assess how quickly new high-value companies are emerging. The insights from this analysis will help the firm better understand current industry trends and make informed decisions on how to optimize and structure their investment portfolio for future growth.

<h2>Data Sources</h2>
<p>We have been given access to their <code>unicorns</code> database, which contains the following tables:</p>

<h3>dates</h3>
<table>
  <tr>
    <th>Column</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>company_id</td>
    <td>A unique ID for the company.</td>
  </tr>
  <tr>
    <td>date_joined</td>
    <td>The date that the company became a unicorn.</td>
  </tr>
  <tr>
    <td>year_founded</td>
    <td>The year that the company was founded.</td>
  </tr>
</table>

<h3>funding</h3>
<table>
  <tr>
    <th>Column</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>company_id</td>
    <td>A unique ID for the company.</td>
  </tr>
  <tr>
    <td>valuation</td>
    <td>Company value in US dollars.</td>
  </tr>
  <tr>
    <td>funding</td>
    <td>The amount of funding raised in US dollars.</td>
  </tr>
  <tr>
    <td>select_investors</td>
    <td>A list of key investors in the company.</td>
  </tr>
</table>

<h3>industries</h3>
<table>
  <tr>
    <th>Column</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>company_id</td>
    <td>A unique ID for the company.</td>
  </tr>
  <tr>
    <td>industry</td>
    <td>The industry that the company operates in.</td>
  </tr>
</table>

<h3>companies</h3>
<table>
  <tr>
    <th>Column</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>company_id</td>
    <td>A unique ID for the company.</td>
  </tr>
  <tr>
    <td>company</td>
    <td>The name of the company.</td>
  </tr>
  <tr>
    <td>city</td>
    <td>The city where the company is headquartered.</td>
  </tr>
  <tr>
    <td>country</td>
    <td>The country where the company is headquartered.</td>
  </tr>
  <tr>
    <td>continent</td>
    <td>The continent where the company is headquartered.</td>
  </tr>
</table>

### Exploratory Data Analysis (EDA)

EDA involved exploring the unicorns database to answer key questions, such as:
- Which industries produced the highest number of unicorns between 2019 and 2021?
- In which year did the highest number of unicorns emerge within the top industries?
- Are there significant differences in average valuations across industries?

### Data Analysis

Include some interesting code/features worked with

```sql
-- Top 3 Best Performing Unicorn Industries in 2019,2020, and 2021
WITH top_industries AS
(
    SELECT i.industry, 
        COUNT(i.*)
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    WHERE EXTRACT(year FROM d.date_joined) in ('2019', '2020', '2021')
    GROUP BY industry
    ORDER BY COUNT DESC
    LIMIT 3
),

yearly_rankings AS 
(
    SELECT COUNT(i.*) AS num_unicorns,
        i.industry,
        EXTRACT(year FROM d.date_joined) AS year,
        AVG(f.valuation) AS average_valuation
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    INNER JOIN funding AS f
        ON d.company_id = f.company_id
    GROUP BY industry, year
)

SELECT industry,
    year,
    num_unicorns,
    ROUND(AVG(average_valuation / 1000000000), 2) AS average_valuation_billions
FROM yearly_rankings
WHERE year in ('2019', '2020', '2021')
    AND industry in (SELECT industry
                    FROM top_industries)
GROUP BY industry, num_unicorns, year
ORDER BY year DESC, num_unicorns DESC;
```

### Results/Findings

The analysis results are summarized as follows:
- FinTech, Internet Sotware & Services, and E-commerce & Direct-to-Customer are the top three industries with the highest number of unicorns during this period.
- 2021 recorded the highest number of new unicorns across all top industries, reflecting a post-pandemic investment boom.
- FinTech has the highest average valuations, suggesting stronger investor confidence in financial technology startups compared to other sectors.

### Recommendations

Based on the analysis, we recommend the following actions:
- The firm should prioritize allocating more funds to financial technology startups, as this sector demonstrates both high growth and strong investor confidence.
-  Expand investments into Internet Software & Services and E-commerce & Direct-to-Customer (D2C) sectors, as these industries also show strong unicorn growth, particularly driven by digital transformation and changing consumer behavior.
-  Since 2021 recorded a record number of new unicorns, this indicates a strong post-pandemic recovery and investor appetite for high-growth startups. The firm should consider acting quickly to capitalize on favorable market conditions before they normalize.

