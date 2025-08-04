---
title: Global Macro Database - GDP Analysis
description: Analysis of global GDP trends from the Global Macro Database
---

# Global GDP Trends Since 1900

This dashboard analyzes global GDP data from the Global Macro Database, showing aggregate GDP trends across all countries since 1900.

```sql global_gdp_by_year
SELECT 
    year,
    SUM(nGDP_USD) as total_gdp_usd_billions,
    COUNT(DISTINCT countryname) as countries_with_data
FROM gmd 
WHERE year >= 1900 
    AND nGDP_USD IS NOT NULL 
    AND nGDP_USD > 0
GROUP BY year 
ORDER BY year
```

## Total Global GDP in USD (Billions) Since 1900

<LineChart 
    data={global_gdp_by_year}
    x=year
    y=total_gdp_usd_billions
    title="Global GDP in USD (Billions)"
    yAxisTitle="GDP (Billions USD)"
    xAxisTitle="Year"
    yFmt="#,##0,,"
/>

### Key Statistics

- **Countries in Dataset**: <Value data={global_gdp_by_year} column=countries_with_data agg=max/> countries have GDP data
- **Latest Year GDP**: $<Value data={global_gdp_by_year} column=total_gdp_usd_billions agg=max fmt="#,##0,,"/> trillion USD
- **Data Coverage**: <Value data={global_gdp_by_year} column=year agg=min/> - <Value data={global_gdp_by_year} column=year agg=max/>

```sql recent_growth
SELECT 
    year,
    total_gdp_usd_billions,
    total_gdp_usd_billions - LAG(total_gdp_usd_billions, 1) OVER (ORDER BY year) as gdp_growth_billions,
    ROUND(((total_gdp_usd_billions - LAG(total_gdp_usd_billions, 1) OVER (ORDER BY year)) / LAG(total_gdp_usd_billions, 1) OVER (ORDER BY year)) * 100, 2) as gdp_growth_pct
FROM ${global_gdp_by_year}
WHERE year >= 2000
ORDER BY year DESC
LIMIT 10
```

## Recent GDP Growth (Last 10 Years)

<DataTable 
    data={recent_growth}
    rows=10
>
    <Column id=year title="Year"/>
    <Column id=total_gdp_usd_billions title="GDP (Billions USD)" fmt="#,##0,,"/>
    <Column id=gdp_growth_billions title="Growth (Billions USD)" fmt="+#,##0;-#,##0"/>
    <Column id=gdp_growth_pct title="Growth %" fmt="+#,##0.0%;-#,##0.0%" contentType=delta/>
</DataTable>