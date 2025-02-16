# Exploratory-Data-Analysis-in-MySQL
## LayOff-Data
## [Download Link to Full Analysis here](https://1drv.ms/f/c/6466f9908878bb62/EsCX79P4XEBNovinpMYVkZ4BhWJr0o5CCDaevj5thUaw4g?e=0S0DGI)


```sql
select *
FROM layoffs_staging2;
```

Maximum laid off in terms of number and percentage
```sql
SELECT max(total_laid_off), max(percentage_laid_off)
FROM layoffs_staging2;
```

To identify companies where percentage laid off employee is 1, which accounts for 100% of their workforce.
```sql
ELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY total_laid_off DESC;
```

To identify hiow big each of the company who laid 100% of their workforce is, I used the funds raised.
```sql
SELECT *
FROM world_layoffs.layoffs_staging2
WHERE  percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```

Top 5 Companies with the biggest single Layoff
```sql
SELECT company, total_laid_off
FROM world_layoffs.layoffs_staging
ORDER BY 2 DESC
LIMIT 5;
```

Top 10 Companies with the most Total Layoffs
```sql
SELECT company, SUM(total_laid_off)
FROM world_layoffs.layoffs_staging2
GROUP BY company
ORDER BY 2 DESC
LIMIT 10;
```



Top 10 Layoffs by location
```sql
SELECT location, SUM(total_laid_off)
FROM world_layoffs.layoffs_staging2
GROUP BY location
ORDER BY 2 DESC
LIMIT 10;
```

Sum of layoffs by country
```sql
SELECT country, SUM(total_laid_off)
FROM world_layoffs.layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```

Sum of Layoffs per year omitting the null
```sql
SELECT YEAR(date), SUM(total_laid_off)
FROM layoffs_staging2
WHERE YEAR(date) IS NOT NULL
GROUP BY YEAR(date)
ORDER BY 1 ASC ;
```

Layoffs by Industry
```sql
SELECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;
```
Layoffs by Stage
```sql
SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```

Companies with most lay offs per year
```sql
WITH Company_Year AS 
(
  SELECT company, YEAR(date) AS years, SUM(total_laid_off) AS total_laid_off
  FROM layoffs_staging2
  GROUP BY company, YEAR(date)
)
, Company_Year_Rank AS (
  SELECT company, years, total_laid_off, DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS ranking
  FROM Company_Year
)
SELECT company, years, total_laid_off, ranking
FROM Company_Year_Rank
WHERE ranking <= 3
AND years IS NOT NULL
ORDER BY years ASC, total_laid_off DESC;
```


-- Rolling Total of Layoffs Per Month
```sql
SELECT SUBSTRING(date,1,7) as dates, SUM(total_laid_off) AS total_laid_off
FROM layoffs_staging2
GROUP BY dates
ORDER BY dates ASC;
```

```sql
WITH DATE_CTE AS 
(
SELECT SUBSTRING(date,1,7) as dates, SUM(total_laid_off) AS total_laid_off
FROM layoffs_staging2
GROUP BY dates
ORDER BY dates ASC
)
SELECT dates, SUM(total_laid_off) OVER (ORDER BY dates ASC) as rolling_total_layoffs
FROM DATE_CTE
ORDER BY dates ASC;
```


