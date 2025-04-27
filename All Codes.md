### BEFORE OPTIMIZATION
```
WITH FINANCIALS_CTE AS (
    SELECT *, 
        CASE 
            WHEN UNIT = 'THAUSANDS' THEN REVENUE / 1000
            WHEN UNIT = 'BILLIONS' THEN REVENUE * 1000
            WHEN UNIT = 'MILLIONS' THEN REVENUE
        END AS REVENUE_MLN,
        
        CASE 
            WHEN CURRENCY = 'USD' THEN
                CASE
                    WHEN UNIT = 'THAUSANDS' THEN (REVENUE / 1000) * 80
                    WHEN UNIT = 'BILLIONS' THEN (REVENUE * 1000) * 80
                    WHEN UNIT = 'MILLIONS' THEN REVENUE * 80
                END
            ELSE
                CASE
                    WHEN UNIT = 'THAUSANDS' THEN (REVENUE / 1000)
                    WHEN UNIT = 'BILLIONS' THEN (REVENUE * 1000)
                    WHEN UNIT = 'MILLIONS' THEN REVENUE
                END
        END AS REVENUE_INR,
        
        CASE
            WHEN UNIT = 'THAUSANDS' THEN BUDGET / 1000
            WHEN UNIT = 'BILLIONS' THEN BUDGET * 1000
            WHEN UNIT = 'MILLIONS' THEN BUDGET
        END AS BUDGET_MLN,
        
        CASE 
            WHEN CURRENCY = 'USD' THEN
                CASE
                    WHEN UNIT = 'THAUSANDS' THEN (BUDGET / 1000) * 80
                    WHEN UNIT = 'BILLIONS' THEN (BUDGET * 1000) * 80
                    WHEN UNIT = 'MILLIONS' THEN BUDGET * 80
                END
            ELSE
                CASE
                    WHEN UNIT = 'THAUSANDS' THEN (BUDGET / 1000)
                    WHEN UNIT = 'BILLIONS' THEN (BUDGET * 1000)
                    WHEN UNIT = 'MILLIONS' THEN BUDGET
                END
        END AS BUDGET_INR
    FROM FINANCIALS
)

SELECT *,
    (BUDGET_INR / REVENUE_INR) * 100 AS PROFIT_PCT
FROM FINANCIALS_CTE;

```

### OPTIMIZAED CTE QUERY

```
WITH financials_cte AS (
    SELECT 
        *,
        CASE 
            WHEN UNIT = 'THAUSANDS' THEN REVENUE / 1000
            WHEN UNIT = 'BILLIONS' THEN REVENUE * 1000
            ELSE REVENUE  -- millions or anything else
        END AS revenue_mln,

        CASE 
            WHEN UNIT = 'THAUSANDS' THEN BUDGET / 1000
            WHEN UNIT = 'BILLIONS' THEN BUDGET * 1000
            ELSE BUDGET
        END AS budget_mln
    FROM financials
),
conversion_cte AS (
    SELECT 
        *,
        CASE 
            WHEN CURRENCY = 'USD' THEN revenue_mln * 80
            ELSE revenue_mln
        END AS revenue_inr,

        CASE 
            WHEN CURRENCY = 'USD' THEN budget_mln * 80
            ELSE budget_mln
        END AS budget_inr
    FROM financials_cte
)
SELECT 
    *,
    ROUND((budget_inr / NULLIF(revenue_inr, 0)) * 100, 2) AS profit_pct
FROM conversion_cte;
```
