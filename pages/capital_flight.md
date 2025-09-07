# Capital Flight Opportunity

Capital flight is the rapid outflow of financial assets or money from a country, often due to economic or political instability, such as currency devaluation or regime change. This phenomenon can lead to a loss of wealth and a decline in the country's economic strength (Wikipedia).

Given DolarApp allows users to convert local currencies to the USD stablecoin, countries which face the aforementioned risks may make ideal target markets for DolarApp to expand into and help users hold and build their wealth.

DolarApp currently operates in Mexico, Argentina, Colombia and Brazil so these will be excluded from any final recommendations.

```sql active_markets
SELECT 
    countryname as country,
    year,
    infl / 100 as inflation_rate,
    USDfx as usd_fx_rate,
    REER as real_effective_exchange_rate,
    rGDP_USD / 1000 as gdp_usd_billions,  -- Convert millions to billions
    pop as population_millions
FROM gmd
WHERE countryname IN ('Mexico', 'Argentina', 'Colombia', 'Brazil')
    AND year >= 2015
    AND infl IS NOT NULL
    AND rGDP_USD IS NOT NULL
ORDER BY countryname, year
```

```sql potential_markets
SELECT 
    countryname as country,
    year,
    infl / 100as inflation_rate,
    USDfx as usd_fx_rate,
    REER as real_effective_exchange_rate,
    rGDP_USD / 1000 as gdp_usd_billions,  -- Convert millions to billions
    pop as population_millions
FROM gmd
WHERE countryname NOT IN ('Mexico', 'Argentina', 'Colombia', 'Brazil')
    AND year >= 2015
    AND infl IS NOT NULL
    AND USDfx IS NOT NULL
    AND rGDP_USD / 1000 > 300  -- Focus on economies with meaningful size (i.e. at least larger than Colombia)
    AND pop > 10  -- Ensure sufficient market size
ORDER BY countryname, year
```

```sql current_markets_analysis
SELECT 
    country,
    year,
    inflation_rate,
    usd_fx_rate,
    -- Calculate USD appreciation (higher = local currency weakening)
    (usd_fx_rate / FIRST_VALUE(usd_fx_rate) OVER (PARTITION BY country ORDER BY year) - 1) as usd_appreciation_pct,
    -- Calculate cumulative inflation
    AVG(inflation_rate) OVER (PARTITION BY country ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as avg_inflation_to_date,
    real_effective_exchange_rate,
    gdp_usd_billions,
    population_millions
FROM ${active_markets}
WHERE year >= 2016  -- Need baseline year for appreciation calculation
ORDER BY country, year
```


```sql inflation_correlation
SELECT 
    country,
    ROUND(AVG(inflation_rate), 2) as avg_inflation_rate,
    ROUND(MAX(usd_appreciation_pct), 2) as max_usd_appreciation,
    ROUND(AVG(usd_appreciation_pct), 2) as avg_usd_appreciation,
    COUNT(*) as years_data,
    -- Calculate correlation score (simplified)
    CASE 
        WHEN AVG(inflation_rate) > 15 AND AVG(usd_appreciation_pct) > 20 THEN 'Strong Positive'
        WHEN AVG(inflation_rate) > 8 AND AVG(usd_appreciation_pct) > 10 THEN 'Moderate Positive'
        WHEN AVG(inflation_rate) > 5 AND AVG(usd_appreciation_pct) > 5 THEN 'Weak Positive'
        ELSE 'Weak/Negative'
    END as correlation_strength
FROM ${current_markets_analysis}
GROUP BY country
ORDER BY avg_inflation_rate DESC
```

<ScatterPlot 
 data={current_markets_analysis}
 x=avg_inflation_to_date
 y=usd_appreciation_pct
 series=country
 title="Inflation Rate vs USD Appreciation by Country"
 xFmt="pct1"
 yFmt="pct1"
 xAxisTitle="Average Inflation Rate (%)"
 yAxisTitle="USD Appreciation (%)"
/>

## Time Series Analysis

<LineChart 
 data={current_markets_analysis}
 x=year
 y=inflation_rate
 series=country
 title="Inflation Rates Over Time - Current Markets"
 yFmt="pct1"
 yAxisTitle="Inflation Rate (%)"
/>

<LineChart 
 data={current_markets_analysis}
 x=year
 y=usd_appreciation_pct
 series=country
 title="USD Appreciation Over Time - Current Markets"
 yFmt="pct1"
 yAxisTitle="USD Appreciation (%)"
/>

## Expansion Opportunities
```sql expansion_analysis
SELECT 
    country,
    year,
    inflation_rate,
    usd_fx_rate,
    -- Calculate USD appreciation for potential markets
    (usd_fx_rate / FIRST_VALUE(usd_fx_rate) OVER (PARTITION BY country ORDER BY year) - 1) as usd_appreciation_pct,
    AVG(inflation_rate) OVER (PARTITION BY country ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as avg_inflation_to_date,
    real_effective_exchange_rate,
    gdp_usd_billions,
    population_millions
FROM ${potential_markets}
WHERE year >= 2016
ORDER BY country, year
```

```sql expansion_ranking
SELECT 
    country,
    ROUND(AVG(inflation_rate), 2) as avg_inflation_rate,
    ROUND(MAX(usd_appreciation_pct), 2) as max_usd_appreciation,
    ROUND(AVG(usd_appreciation_pct), 2) as avg_usd_appreciation,
    ROUND(AVG(gdp_usd_billions), 0) as avg_gdp_billions,
    ROUND(AVG(population_millions), 1) as population_millions,
    -- Create opportunity score
    CASE 
        WHEN AVG(inflation_rate) > 15 AND AVG(usd_appreciation_pct) > 20 THEN 100
        WHEN AVG(inflation_rate) > 10 AND AVG(usd_appreciation_pct) > 15 THEN 80
        WHEN AVG(inflation_rate) > 8 AND AVG(usd_appreciation_pct) > 10 THEN 60
        WHEN AVG(inflation_rate) > 5 AND AVG(usd_appreciation_pct) > 5 THEN 40
        ELSE 20
    END as opportunity_score,
    -- Market attractiveness factors
    CASE 
        WHEN AVG(gdp_usd_billions) > 500 AND AVG(population_millions) > 50 THEN 'Large'
        WHEN AVG(gdp_usd_billions) > 200 AND AVG(population_millions) > 25 THEN 'Medium'
        ELSE 'Small'
    END as market_size
FROM ${expansion_analysis}
GROUP BY country
HAVING COUNT(*) >= 5  -- At least 5 years of data
ORDER BY opportunity_score DESC, avg_gdp_billions DESC
LIMIT 15
```

## Top Expansion Opportunities
<DataTable data={expansion_ranking} rows=15>
    <Column id=country/>
    <Column id=avg_inflation_rate fmt="pct1" title="Avg Inflation"/>
    <Column id=max_usd_appreciation fmt="pct1" title="Max USD Appreciation"/>
    <Column id=opportunity_score contentType=bar title="Opportunity Score"/>
    <Column id=avg_gdp_billions fmt="#,##0" title="GDP (Billions)"/>
    <Column id=population_millions fmt="#,##0.0" title="Population (M)"/>
    <Column id=market_size title="Market Size"/>
</DataTable>

## Insights

```sql key_insights
SELECT 
    'Current Markets' as market_type,
    COUNT(DISTINCT country) as countries,
    ROUND(AVG(avg_inflation_rate), 2) as avg_inflation,
    ROUND(AVG(max_usd_appreciation), 2) as avg_max_appreciation,
    'Established operations' as status
FROM ${inflation_correlation}

UNION ALL

SELECT 
    'High Opportunity' as market_type,
    COUNT(*) as countries,
    ROUND(AVG(avg_inflation_rate), 2) as avg_inflation,
    ROUND(AVG(max_usd_appreciation), 2) as avg_max_appreciation,
    'Expansion targets' as status
FROM ${expansion_ranking}
WHERE opportunity_score >= 60
```

{#each expansion_ranking.filter(d => d.opportunity_score >= 60).slice(0, 5) as opportunity}
{opportunity.country}

Opportunity Score: {opportunity.opportunity_score}/100
Market Dynamics: {opportunity.avg_inflation_rate}% average inflation driving {opportunity.max_usd_appreciation}% peak USD appreciation
Market Size: {opportunity.market_size} market with {opportunity.population_millions}M people and ${opportunity.avg_gdp_billions}B GDP
Recommendation: {opportunity.opportunity_score >= 80 ? 'High Priority - Strong inflation/devaluation pattern similar to Argentina' : 'Medium Priority - Monitor for entry timing'}

{/each}