# Analyzing Unicorn Companies

### Project Overview

Did you know that the average return from investing in stocks is 10% per year (https://www.nerdwallet.com/article/investing/average-stock-market-return) (not accounting for inflation)? But who wants to be average?! 

This project entails providing support to an investment firm by conducting analyses of trends among high-growth companies. The objective is to identify which industries are generating the highest company valuations and to assess how quickly new high-value companies are emerging. The insights from this analysis will help the firm better understand current industry trends and make informed decisions on how to optimize and structure their investment portfolio for future growth.

### Data Sources

We have been given access to their `unicorns` database, which contains the following tables:

#### dates
| Column       | Description                                  |
|------------- |--------------------------------------------- |
| `company_id`   | A unique ID for the company.                 |
| `date_joined` | The date that the company became a unicorn.  |
| `year_founded` | The year that the company was founded.       |

#### funding
| Column           | Description                                  |
|----------------- |--------------------------------------------- |
| `company_id`       | A unique ID for the company.                 |
| `valuation`        | Company value in US dollars.                 |
| `funding`          | The amount of funding raised in US dollars.  |
| `select_investors` | A list of key investors in the company.      |

#### industries
| Column       | Description                                  |
|------------- |--------------------------------------------- |
| `company_id`   | A unique ID for the company.                 |
| `industry`     | The industry that the company operates in.   |

#### companies
| Column       | Description                                       |
|------------- |-------------------------------------------------- |
| `company_id`   | A unique ID for the company.                      |
| `company`      | The name of the company.                          |
| `city`         | The city where the company is headquartered.      |
| `country`      | The country where the company is headquartered.   |
| `continent`    | The continent where the company is headquartered. |

### Exploratory Data Analysis (EDA)

EDA involved exploring the unicorns database to answer key questions, such as:
- Which industries produced the highest number of unicorns between 2019 and 2021?
- In which year did the highest number of unicorns emerge within the top industries — 2019, 2020, or 2021?
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

