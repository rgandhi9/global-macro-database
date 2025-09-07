# Capital Flight as a Growth Opportunity

## Nominal FX vs REER Divergence — Argentina

We compare the nominal USD/ARS exchange rate with the Real Effective Exchange Rate (REER), indexed to 100 in 2010. If the FX rate rises faster than REER falls, it suggests high inflation is offsetting nominal depreciation — eroding real purchasing power.

```sql fx_reer
WITH fx_2010 AS (
  SELECT countryname, USDfx AS USDfx_2010
  FROM gmd
  WHERE year = 2010
    --AND countryname IN ('Brazil', 'Mexico', 'Colombia')
)
SELECT
  m.countryname,
  m.year,
  m.pop,
  m.USDfx,
  m.REER,
  m.USDfx / f.USDfx_2010 * 100 AS fx_indexed
FROM gmd m
JOIN fx_2010 f 
  ON m.countryname = f.countryname
WHERE m.year BETWEEN 2010 AND 2025
  AND m.iso3 IN ('BLZ', 'CRI', 'SLV', 'GTM', 'HND', 'NIC', 'PAN', 'ARG', 'BOL', 'BRA', 'CHL', 'COL', 'ECU', 'GUY', 'PRY', 'PER', 'SUR', 'URY', 'VEN', 'MEX')
```

```sql argentina
SELECT
  countryname,
  year,
  USDfx,
  REER,
  fx_indexed,
  fx_indexed - REER AS divergence
FROM ${fx_reer}
WHERE countryname = 'Argentina'
ORDER BY countryname, year;
```

```sql active_countries
SELECT
  countryname,
  year,
  USDfx,
  REER,
  fx_indexed,
  fx_indexed - REER AS divergence
FROM ${fx_reer}
WHERE countryname IN ('Brazil', 'Mexico', 'Colombia')
ORDER BY countryname, year;
```
<LineChart
data={argentina}
x="year"
y={["divergence"]}
series="countryname"
title="Nominal FX vs REER (Indexed to 2010 = 100)"
/>

<LineChart
data={active_countries}
x="year"
y={["divergence"]}
series="countryname"
title="Nominal FX vs REER (Indexed to 2010 = 100)"
/>

```sql top5_non_active
WITH all_countries AS (
  SELECT
    countryname,
    year,
    pop,
    USDfx,
    REER,
    fx_indexed,
    fx_indexed - REER AS divergence,
    AVG(fx_indexed - REER) OVER(PARTITION BY countryname) AS avg_div
  FROM ${fx_reer}
  WHERE countryname NOT IN ('Argentina', 'Brazil', 'Mexico', 'Colombia')
),
ranked AS (
SELECT
  countryname,
  year,
  USDfx,
  REER,
  fx_indexed,
  divergence,
  avg_div,
  DENSE_RANK() OVER(ORDER BY avg_div DESC) AS rnk
FROM all_countries
)
SELECT *
FROM ranked
WHERE rnk <= 10
ORDER BY countryname, year;
```

<LineChart
data={top5_non_active}
x="year"
y={["divergence"]}
series="countryname"
title="Nominal FX vs REER (Indexed to 2010 = 100)"
/>