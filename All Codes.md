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
            ELSE REVENUE
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



### JOINS IN SQL
```
SELECT *
FROM movies
JOIN FINANCIALS
ON MOVIES.MOVIE_ID = FINANCIALS.MOVIE_ID;
```

### MOVIE_ID IS THE SAME IN BOTH TABLE THAT IS WHY WE NEED TO SPECIFY IT USING MOVIES.MOVIE_ID

#### INNER JOIN 
```
SELECT 
	MOVIES.MOVIE_ID, TITLE, CURRENCY, BUDGET, UNIT
FROM MOVIES
JOIN
	FINANCIALS
    ON MOVIES.MOVIE_ID = FINANCIALS.MOVIE_ID;
```
#### Same same but different style
```
SELECT
M.MOVIE_ID, TITLE, CURRENCY, UNIT
FROM MOVIES M 
INNER JOIN FINANCIALS F 
ON M.MOVIE_ID =F.MOVIE_ID;
```

### LEFT JOIN
```
SELECT
M.MOVIE_ID, TITLE, CURRENCY, UNIT
FROM MOVIES M 
LEFT JOIN FINANCIALS F 
ON M.MOVIE_ID =F.MOVIE_ID;
```
### RIGHT JOIN 
```
SELECT
F.MOVIE_ID, TITLE, CURRENCY, UNIT
FROM MOVIES M 
RIGHT JOIN FINANCIALS F 
ON M.MOVIE_ID =F.MOVIE_ID;
```
### FULL JOIN 
```
SELECT
M.MOVIE_ID, TITLE, CURRENCY, UNIT
FROM MOVIES M 
LEFT JOIN FINANCIALS F 
ON M.MOVIE_ID =F.MOVIE_ID

UNION 

SELECT
F.MOVIE_ID, TITLE, CURRENCY, UNIT
FROM MOVIES M 
RIGHT JOIN FINANCIALS F 
ON M.MOVIE_ID =F.MOVIE_ID;
```
### USING --------------- THIS CAN BE USED TO JOIN BASED ON MULTIPLE COLUMNS -----------------------
```
SELECT
MOVIE_ID, TITLE, CURRENCY, UNIT
FROM MOVIES
RIGHT JOIN FINANCIALS
USING(MOVIE_ID);
```
### 1. Show all the movies with their language names
```
SELECT * 
FROM movies
JOIN LANGUAGES
ON MOVIES.LANGUAGE_ID = LANGUAGES.LANGUAGE_ID;
```
### 2. Show all Telugu movie names (assuming you don't know the languageid for Telugu)
```
SELECT MOVIES.TITLE
FROM movies
JOIN LANGUAGES
ON MOVIES.LANGUAGE_ID = LANGUAGES.LANGUAGE_ID
WHERE  LANGUAGES.NAME LIKE "%TELUGU%";
```
### 3.Show the language and number of movies released in that language
```
SELECT 
LANGUAGES.NAME,
COUNT(MOVIES.LANGUAGE_ID)
FROM MOVIES
JOIN LANGUAGES
ON MOVIES.LANGUAGE_ID = LANGUAGES.LANGUAGE_ID
WHERE LANGUAGES.NAME LIKE "%TELUGU%"
GROUP BY LANGUAGES.NAME;
```

### CROSS JOIN - CARTESIAN PRODUCT X * X
```
USE FOOD_DB;

SELECT *,
CONCAT(NAME, " ", VARIANT_NAME) AS FULL_NAME,
(PRICE+VARIANT_PRICE) AS FULL_PRICE
FROM food_db.ITEMS
CROSS JOIN VARIANTS;
    *,
    ROUND((budget_inr / NULLIF(revenue_inr, 0)) * 100, 2) AS profit_pct
FROM conversion_cte;
```
