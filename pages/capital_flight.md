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

The following table is used to for calculating cumulative inflation and depreciation of the local currency against the USD for longer term trends.  YoY fluctuations could be used in future work to better understand currency volatility.

```sql current_markets_analysis
SELECT 
    country,
    year,
    inflation_rate,
    usd_fx_rate,
    -- Cumulative USD appreciation (higher = local currency weakening)
    (usd_fx_rate / FIRST_VALUE(usd_fx_rate) OVER (PARTITION BY country ORDER BY year) - 1) as usd_appreciation_pct,
    -- Cumulative inflation
    AVG(inflation_rate) OVER (PARTITION BY country ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as avg_inflation_to_date,
    real_effective_exchange_rate,
    gdp_usd_billions,
    population_millions
FROM ${active_markets}
-- WHERE year >= 2010  -- Baseline year for appreciation calculation
ORDER BY country, year
```

## Correlating Inflation with Currency Devaluation

Below, we can observe a very obvious positive relationship between rising inflation and local currency devaluation for the Argentinian Peso.  While Argentina is an extreme example facing both hyper-inflation and hyper-devaluation, it represents a textbook case for USD service demand.  DolarApp's other markets are less extreme - Mexico being the most stable with almost normal levels of inflation at 5%.

```sql inflation_correlation
SELECT 
    country,
    ROUND(AVG(inflation_rate), 2) as avg_inflation_rate,
    ROUND(MAX(usd_appreciation_pct), 2) as max_usd_appreciation,
    ROUND(AVG(usd_appreciation_pct), 2) as avg_usd_appreciation,
    COUNT(*) as years_data,
    -- Calculate correlation score
    CASE 
        WHEN AVG(inflation_rate) > 0.15 AND AVG(usd_appreciation_pct) > 0.2 THEN 'Strong Positive'
        WHEN AVG(inflation_rate) > 0.08 AND AVG(usd_appreciation_pct) > 0.1 THEN 'Moderate Positive'
        WHEN AVG(inflation_rate) > 0.05 AND AVG(usd_appreciation_pct) > 0.05 THEN 'Weak Positive'
        ELSE 'Weak/Negative'
    END as correlation_strength
FROM ${current_markets_analysis}
GROUP BY country
ORDER BY avg_inflation_rate DESC
```

<ScatterPlot 
 data={inflation_correlation}
 x=avg_inflation_rate
 y=avg_usd_appreciation
 series=country
 title="Inflation Rate vs USD Appreciation by Country"
 xFmt="pct1"
 yFmt="pct1"
 xAxisTitle="Average Inflation Rate (%)"
 yAxisTitle="USD Appreciation (%)"
/>

## Time Series Analysis
The 2 line charts below show inflation and currency devaluation (USD appreciation) over time for current markets.  For Argentina, we can see a slight lag in currency devaluation relative to inflation between 2022 and 2025.  Inflation could therefore be used as a predictor  of currency appreciation/devaluation in other markets, however there are many other macroeconomic factors which could be influencing these trends so further research would be required.
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
Based on the information above, we would want to target countries which have high inflation and currency devaluation against the USD.  

```sql expansion_analysis
-- Same query as current_markets_analysis above but for potential_markets
SELECT 
    country,
    year,
    inflation_rate,
    usd_fx_rate,
    -- Cumulative USD appreciation (higher = local currency weakening)
    (usd_fx_rate / FIRST_VALUE(usd_fx_rate) OVER (PARTITION BY country ORDER BY year) - 1) as usd_appreciation_pct,
    -- Cumulative inflation
    AVG(inflation_rate) OVER (PARTITION BY country ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as avg_inflation_to_date,
    real_effective_exchange_rate,
    gdp_usd_billions,
    population_millions
FROM ${potential_markets}
-- WHERE year >= 2010
ORDER BY country, year
```
We can create a scoring system to assign a numeric value for each country to indicate an attractiveness to expand into.  This is called the **Opportunity Score**.
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
        WHEN AVG(inflation_rate) > 0.2 AND AVG(usd_appreciation_pct) > 0.2 THEN 100
        WHEN AVG(inflation_rate) > 0.15 AND AVG(usd_appreciation_pct) > 0.15 THEN 80
        WHEN AVG(inflation_rate) > 0.1 AND AVG(usd_appreciation_pct) > 0.1 THEN 60
        WHEN AVG(inflation_rate) > 0.05 AND AVG(usd_appreciation_pct) > 0.05 THEN 40
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
    <Column id=avg_usd_appreciation fmt="pct1" title="Avg USD Appreciation"/>
    <Column id=opportunity_score contentType=bar title="Opportunity Score"/>
    <Column id=avg_gdp_billions fmt="#,##0" title="GDP (Billions)"/>
    <Column id=population_millions fmt="#,##0.0" title="Population (M)"/>
    <Column id=market_size title="Market Size"/>
</DataTable>

## Summary Insights
A summary table is given below showing a high-level representation of current markets along with high-opportunity markets i.e. an Opportunity score of 80 or more.  The average inflation in <Value data={key_insights} /> is <Value data={key_insights} column=avg_inflation fmt=pct0 /> compared with <Value data={key_insights} row=1 /> markets where it's <Value data={key_insights} column=avg_inflation row=1 fmt=pct0 />.  While the inflations numbers are similar, the average max appreciation of the USD is lower (<Value data={key_insights} column=avg_max_appreciation fmt=pct0 /> in <Value data={key_insights} /> vs <Value data={key_insights} column=avg_max_appreciation row=1 fmt=pct0 /> in <Value data={key_insights} row=1/>) which would indicate greater currency stability (though still significant enough to provide business opportunities).
```sql key_insights
SELECT 
    'Current Markets' as market_type,
    COUNT(DISTINCT country) as countries,
    ROUND(AVG(avg_inflation_rate), 2) as avg_inflation,
    ROUND(AVG(max_usd_appreciation), 2) as avg_max_appreciation
FROM ${inflation_correlation}

UNION ALL

SELECT 
    'High Opportunity' as market_type,
    COUNT(*) as countries,
    ROUND(AVG(avg_inflation_rate), 2) as avg_inflation,
    ROUND(AVG(max_usd_appreciation), 2) as avg_max_appreciation
FROM ${expansion_ranking}
WHERE opportunity_score >= 80
```
The top 3 countries for expansion which exhibit high inflation, a devaluing currency and a large market size are given below (given current geopolitical issues, Iran would be excluded.  However, Turkey and Nigeria alone provide a market size of 300m people):

{#each expansion_ranking.filter(d => d.opportunity_score >= 80).slice(0, 3) as opportunity}
**{opportunity.country}**

Opportunity Score: {opportunity.opportunity_score}/100
Market Dynamics: {fmt(opportunity.avg_inflation_rate,'pct0')} average inflation driving {fmt(opportunity.max_usd_appreciation,'pct0')} peak USD appreciation
Market Size: {opportunity.market_size} market with {opportunity.population_millions}M people and ${opportunity.avg_gdp_billions}B GDP
Recommendation: {opportunity.opportunity_score >= 80 ? 'High Priority - Strong inflation/devaluation pattern similar to Argentina' : 'Medium Priority - Monitor for entry timing'}

{/each}

## Assumptions and Future Work
- Ignoring regulatory differences and difficulties in new markets
- While the above analysis helps acquire more users for growth, looking at countries with larger remittances out/inflows will likely convert this growth into profitability given the $3 transfer fees.
- Isolate a current market which has proven to generate the highest revenues/profits and use that as a target to finding new markets - possibly through feature engineering to identify key categories and using these in a ML model.
- Covid-19 will have impacted countries differently.  The effects of which have not been considered in this analysis given a starting period of 2015 has been used.
