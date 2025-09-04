# Global GDP Trends Since 1900
<LastRefreshed prefix="Data last updated"/>

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