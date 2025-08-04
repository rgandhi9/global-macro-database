========================
CODE SNIPPETS
========================
TITLE: Creating a Line Chart Component
DESCRIPTION: This snippet shows how to integrate a Line Chart component in Evidence, using inline data to visualize sales over months. It demonstrates the declaration of a component with attributes for data and titles.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_2

LANGUAGE: markdown
CODE:
```
<LineChart 
    data = {orders_by_month}    
    y = sales_usd 
    title = 'Sales by Month, USD' 
/>
```

----------------------------------------

TITLE: SQL Queries with Parameters in Evidence
DESCRIPTION: Illustrates SQL queries with parameters in Evidence, which can be derived from component inputs or URL parameters. Parameters enhance query flexibility and are indicated using '${inputs.parameter_name}' or '${params.parameter_name}' within the SQL logic. Queries should account for the provided parameter type and ensure correct mapping to variables.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/queries/index.md#2025-04-21_snippet_5

LANGUAGE: markdown
CODE:
```
```sql sales_by_month
select
    date_trunc('month', date) as month,
    sum(sales) as sales
from needful_things.orders
where category = '${inputs.category}'
group by 1
```
```

----------------------------------------

TITLE: Creating a Bar Chart with Evidence Component in HTML
DESCRIPTION: This snippet demonstrates how to use the BarChart component in Evidence to create a chart displaying sales by category. It shows how to pass data and configuration options as properties.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/components/index.md#2025-04-21_snippet_0

LANGUAGE: html
CODE:
```
<BarChart
	data="{orders_by_month}"
	x="order_month"
	y="sales_usd0k"
	series="category"
	title="Sales by Category"
/>
```

----------------------------------------

TITLE: Querying Most Liked Tags by Time Period in SQL
DESCRIPTION: This SQL query identifies the top 5 hashtags that received the most likes within a specified time range. It counts likes for each hashtag per day, groups the results, and filters to include only the most liked tags.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/test-env/pages/social-media/trends.md#2025-04-21_snippet_1

LANGUAGE: sql
CODE:
```
WITH EVERYTHING AS (
    SELECT  COUNT(l.id) as likes,
            h.tag,
            date_trunc('day', p.created_at) as w,
            h.id as hashtag_id
        FROM likes l
            INNER JOIN posts p on l.post_id = p.id
            INNER JOIN post_tags pt ON p.id = pt.post_id
            INNER JOIN hashtags h ON pt.hashtag_id = h.id
        WHERE p.created_at > (CURRENT_DATE - INTERVAL ${inputs.time_range.value})
        GROUP BY h.tag, date_trunc('day', p.created_at), h.id
        ORDER BY 1 DESC
)
SELECT * FROM EVERYTHING
GROUP BY ALL
HAVING tag IN (
    SELECT tag FROM EVERYTHING GROUP BY ALL ORDER BY SUM(likes) DESC LIMIT 5
)
```

----------------------------------------

TITLE: Installing and Running Evidence Template
DESCRIPTION: This snippet outlines the steps to create a new project using the Evidence template. It uses 'npx' to download the template without cloning the repository and then installs project dependencies. The final command starts the development server. Dependencies include npm, npx, and a Node.js setup.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/test-env/README.md#2025-04-21_snippet_0

LANGUAGE: bash
CODE:
```
npx degit evidence-dev/template my-project
cd my-project
npm install
npm run dev
```

----------------------------------------

TITLE: Querying and Displaying Recent Orders in Evidence
DESCRIPTION: The snippet refines the previous data query to select the most recent 100 orders, displaying only essential columns using another DataTable in Evidence. The focus is on sending discount codes to these customers based on the recent order data obtained.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/build-your-first-app/index.md#2025-04-21_snippet_5

LANGUAGE: sql
CODE:
```
select order_datetime, first_name, last_name, email from needful_things.my_query order by order_datetime desc limit 100
```

LANGUAGE: markdown
CODE:
```
<DataTable data={my_query_summary_top100} />
```

----------------------------------------

TITLE: Using Loops in Evidence Markdown
DESCRIPTION: This snippet demonstrates how to loop through a dataset in Evidence Markdown. It uses the `{#each}` syntax to iterate over `orders_by_month` and display a list of orders for each month.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_3

LANGUAGE: markdown
CODE:
```
{#each orders_by_month as month}

- There were <Value data={month} column=number_of_orders/> orders in <Value data={month} />.

{/each}
```

----------------------------------------

TITLE: Applying Custom Aggregations and Formats in DataTable
DESCRIPTION: This snippet demonstrates how to use custom aggregation values and formats for the total row in a DataTable. It shows setting static text and using a specific data value for aggregation.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_15

LANGUAGE: svelte
CODE:
```
<DataTable data={countries} totalRow=true rows=5>
  <Column id=country totalAgg="Just the USA"/>
  <Column id=gdp_usd totalAgg={countries[0].gdp_usd} totalFmt=usd/>
</DataTable>
```

----------------------------------------

TITLE: Grouped DataTable with Advanced Column Configuration
DESCRIPTION: Creates a DataTable grouped by category with custom column configurations including color scales, formatted numbers, and weighted means for aggregations.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_45

LANGUAGE: svelte
CODE:
```
<DataTable data={orders} groupBy=category subtotals=true totalRow=true> 
 	<Column id=state totalAgg=countDistinct totalFmt='0 "states"'/> 
	<Column id=category totalAgg=Total/> 
	<Column id=item  totalAgg=countDistinct totalFmt='0 "items"'/> 
	<Column id=orders contentType=colorscale/> 
	<Column id=sales fmt=usd0k/> 
	<Column id=growth contentType=delta fmt=pct totalAgg=weightedMean weightCol=sales/> 
</DataTable>
```

----------------------------------------

TITLE: Filtering Rows in Query - Markdown with SQL Statement
DESCRIPTION: This code snippet demonstrates how to filter rows based on a specific condition using the `.where()` function. It is implemented in a DataTable component to visualize the filtered data.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/query-functions/index.md#2025-04-21_snippet_1

LANGUAGE: markdown
CODE:
```
<DataTable data={orders.where(`sales > 100`)} />
```

----------------------------------------

TITLE: Chaining SQL Queries in Evidence
DESCRIPTION: This snippet showcases how to create dependencies between SQL queries in Evidence by referencing existing queries within new ones using the '${query_name}' syntax. This allows for dynamic query construction and propagation of intermediary results. Dependencies must be handled carefully to avoid circular references or unresolved identifiers.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/queries/index.md#2025-04-21_snippet_2

LANGUAGE: sql
CODE:
```
```sql sales_by_item
select
    item,
    sum(sales) as sales
from needful_things.orders
group by 1
```

```sql average_sales
select
    avg(sales) as average_sales
from ${sales_by_item}
```
```

----------------------------------------

TITLE: Using Frontmatter in Markdown
DESCRIPTION: This snippet demonstrates the use of frontmatter in Evidence Markdown for configuring metadata such as title, description, and associated SQL queries.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_6

LANGUAGE: markdown
CODE:
```
---
title: Evidence uses Markdown
description: Evidence uses Markdown to write expressively in text.
og:
  image: /my-social-image.png
queries:
  - orders_by_month.sql
---
```

----------------------------------------

TITLE: Skipping Rows in Query Result - Markdown with SQL Function
DESCRIPTION: This code snippet illustrates the use of the `.offset()` function to skip a certain number of rows in the query results, facilitating pagination or similar functionalities.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/query-functions/index.md#2025-04-21_snippet_5

LANGUAGE: markdown
CODE:
```
<DataTable data={orders.offset(20)} />
```

----------------------------------------

TITLE: Implementing Dynamic Queries in Svelte
DESCRIPTION: Demonstrates how to create a component with dynamic queries that respond to user input, allowing for interactive data exploration with controls for query parameters.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/custom/component-queries/index.md#2025-04-21_snippet_2

LANGUAGE: html
CODE:
```
<script>
    import { QueryLoad } from '@evidence-dev/core-components';
    import { getQueryFunction } from '@evidence-dev/component-utilities/buildQuery';
    import { Query } from '@evidence-dev/sdk/usql';

    // This will hold our current query result
    let query;

    // Create a reactive query function
    const queryFunction = Query.createReactive({
        execFn: getQueryFunction(),
        callback: v => query = v
    });

    // These values will control our query
    let limit = 10;
    let schemaName = 'public';

    // This reactive statement runs whenever limit or schemaName change
    $: queryFunction(`
        SELECT * 
        FROM information_schema.tables 
        WHERE table_schema = '${schemaName}'
        LIMIT ${limit}
    `);
</script>

<div>
    <label>
        Rows to show:
        <input type="number" bind:value={limit} min={0} />
    </label>
    <label>
        Schema:
        <input type="text" bind:value={schemaName} />
    </label>
</div>

<QueryLoad data={query} let:loaded={tables}>
    <svelte:fragment slot="skeleton" />
    <ul>
        {#each tables as table}
            <li>{table.table_name}</li>
        {/each}
    </ul>
</QueryLoad>
```

----------------------------------------

TITLE: Visualizing Monthly Orders Using a Bar Chart in Evidence
DESCRIPTION: The snippet adds a Bar Chart to an Evidence page, querying monthly order counts. The data is selected by grouping orders monthly and renders in a chart, with customization of the x and y axis labels for clarity.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/build-your-first-app/index.md#2025-04-21_snippet_7

LANGUAGE: sql
CODE:
```
select order_month, count(*) as orders from needful_things.my_query group by order_month order by order_month desc limit 12
```

LANGUAGE: markdown
CODE:
```
<BarChart
    data={orders_by_month}
    x=order_month
    y=orders
	xFmt="mmm yyyy"
	xAxisTitle="Month"
	yAxisTitle="Orders"
/>
```

----------------------------------------

TITLE: Each Loop Syntax in Evidence
DESCRIPTION: This snippet demonstrates the basic syntax for using an `{#each}` loop in Evidence. It iterates over the rows in a query result and allows referencing each row using an alias.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/loops/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
"{#each query_name as alias}

{alias.column_name}

{/each}"
```

----------------------------------------

TITLE: Limiting Query Results - Markdown with SQL Function
DESCRIPTION: This snippet demonstrates how to limit the number of rows returned from a query using the `.limit()` function. It is used within a DataTable component to display a subset of data.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/query-functions/index.md#2025-04-21_snippet_4

LANGUAGE: markdown
CODE:
```
<DataTable data={orders.limit(5)} />
```

----------------------------------------

TITLE: Counting Unique Authors for a Selected Tag in SQL
DESCRIPTION: This SQL query counts the number of unique users who have created posts with a specific hashtag within a selected time range. It joins users, posts, and post_tags tables to identify distinct authors.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/test-env/pages/social-media/trends.md#2025-04-21_snippet_4

LANGUAGE: sql
CODE:
```
SELECT COUNT(DISTINCT u.ID) as authorCount
FROM users u
    INNER JOIN posts p ON p.user_id = u.id
    INNER JOIN post_tags pt ON pt.post_id = p.id
WHERE pt.hashtag_id = ${inputs.selected_tag.value} AND p.created_at > CURRENT_DATE - INTERVAL ${inputs.time_range.value}
GROUP BY pt.hashtag_id
```

----------------------------------------

TITLE: Using QueryLoad Component in Svelte
DESCRIPTION: Shows how to use the QueryLoad component to handle query execution, loading states, and error handling when working with database queries.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/custom/component-queries/index.md#2025-04-21_snippet_1

LANGUAGE: html
CODE:
```
<QueryLoad data={query} let:loaded={tableData}>
    <svelte:fragment slot="skeleton" />
    <!-- Your component content here -->
</QueryLoad>
```

----------------------------------------

TITLE: Basic Value Component Usage in Markdown
DESCRIPTION: Shows the basic syntax for using the Value component to display the first row of the first column from a query result.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/value/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
<Value data={query_name} />
```

----------------------------------------

TITLE: Filtering Query with DateRange Component
DESCRIPTION: Shows how to use the date range picker to filter a SQL query. The query uses template literals to reference the selected start and end dates from the date range picker.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/inputs/date-range/index.md#2025-04-21_snippet_5

LANGUAGE: markdown
CODE:
```
<DateRange
    name=range_filtering_a_query
    data={orders_by_day}
    dates=day
/>

```sql filtered_query
select 
    *
from ${orders_by_day}
where day between '${inputs.range_filtering_a_query.start}' and '${inputs.range_filtering_a_query.end}'
```

<LineChart
    data={filtered_query}
    x=day
    y=sales
/>
```

----------------------------------------

TITLE: Visualizing EV Charging Stations by State Using a USMap Component
DESCRIPTION: This snippet explains how to create a US map visualizing the count of EV charging stations per state, excluding California. It uses a Markdown Query and the USMap component in Evidence to render the geographic data.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/build-your-first-app/index.md#2025-04-21_snippet_9

LANGUAGE: sql
CODE:
```
select State, count(*) AS ev_station_count from ev_stations.us_alt_fuel_stations where State not in ('CA') group by State order by ev_station_count desc
```

LANGUAGE: markdown
CODE:
```
<USMap data={ev_map} state=State abbreviations=true value=ev_station_count/>
```

----------------------------------------

TITLE: Each Loop Example with Location Data
DESCRIPTION: This snippet shows how to use an `{#each}` loop with the `location_summary` query to generate a header and a paragraph for each city. It utilizes the `city` alias to access the data for each city and dynamically insert it into the content.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/loops/index.md#2025-04-21_snippet_2

LANGUAGE: markdown
CODE:
```
"Daily sales:

{#each location_summary as city}

## {city.name}

<Value data={city} column=sales_usd/> in sales at a <Value data={city} column=gross_margin_pct/> gross margin.

{/each}"
```

----------------------------------------

TITLE: JavaScript Expressions in Markdown
DESCRIPTION: This snippet illustrates how to use JavaScript expressions within Evidence Markdown for dynamic data rendering. It can compute and display results inline.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_7

LANGUAGE: markdown
CODE:
```
2 + 2 = {2 + 2}

<!-- Result: 2 + 2 = 4 -->

There are {orders.length} months of data.

<!-- Result: There are 36 months of data. -->

There were {orders_by_month[0].number_of_orders} orders last month.

<!-- Result: There were 3634 orders last month. -->
```

----------------------------------------

TITLE: Creating a Static Query Component in Svelte
DESCRIPTION: Demonstrates how to create a Svelte component that fetches and displays a list of tables from the database using a static SQL query.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/custom/component-queries/index.md#2025-04-21_snippet_0

LANGUAGE: html
CODE:
```
<script>
    import { buildQuery } from '@evidence-dev/component-utilities/buildQuery';
    import { QueryLoad } from '@evidence-dev/core-components';

    const query = buildQuery(
        'SELECT * FROM information_schema.tables',
    );
</script>

<QueryLoad data={query} let:loaded={tables}>
    <svelte:fragment slot="skeleton" />
    
    <ul>
        {#each tables as table}
            <li>{table.table_name}</li>
        {/each}
    </ul>
</QueryLoad>
```

----------------------------------------

TITLE: Adding Aggregation Operations to Queries - Markdown with SQL Function
DESCRIPTION: This snippet shows how to apply aggregation functions using the `.agg()` feature in conjunction with the `.groupBy()` method. It enables calculations like sums on grouped data for analysis.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/query-functions/index.md#2025-04-21_snippet_6

LANGUAGE: markdown
CODE:
```
<DataTable 
    data={orders.groupBy(["category", "item"]).agg({sum: "sales"})}/>
```

----------------------------------------

TITLE: Hide Table if Empty in Evidence
DESCRIPTION: This code snippet demonstrates how to conditionally render a DataTable component in Evidence based on the length of the data array. If the query_name array has a length greater than 0, then the DataTable will be rendered.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/if-else/index.md#2025-04-21_snippet_1

LANGUAGE: javascript
CODE:
```

{#if query_name.length !== 0}

<DataTable data={query_name}>

{/if}

```

----------------------------------------

TITLE: Using Inputs in SQL Queries for Evidence
DESCRIPTION: This snippet shows how to use configured inputs within SQL queries in Evidence. It demonstrates the syntax for referencing input values using ${input.name} format.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/inputs/index.md#2025-04-21_snippet_1

LANGUAGE: sql
CODE:
```
select *
from orders
where region = ${input.region}
  and product = ${input.product}
```

----------------------------------------

TITLE: Source Query File Structure
DESCRIPTION: Demonstrates the file structure for configuring source queries in Evidence, showing how to organize SQL query files for a specific data source

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/data-sources/index.md#2025-04-21_snippet_0

LANGUAGE: code
CODE:
```
.-- sources/
   `-- my_source/
      |-- connection.yaml
      `-- my_source_query.sql
```

----------------------------------------

TITLE: Executing SQL Queries in Markdown
DESCRIPTION: This snippet illustrates how to write SQL queries within Evidence Markdown using DuckDB SQL dialect. The SQL query retrieves order statistics grouped by month.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_1

LANGUAGE: sql
CODE:
```
select
    date_trunc('month', order_datetime) as order_month,
    count(*) as number_of_orders,
    sum(sales) as sales_usd
from needful_things.orders
group by 1, order by 1 desc
```

----------------------------------------

TITLE: Creating Basic Bar Chart in Evidence
DESCRIPTION: Demonstrates how to create a simple bar chart using the BarChart component. The chart displays sales data by category over time.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/bar-chart/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
<BarChart 
    data={orders_by_category_2021}
    x=month
    y=sales
    series=category
    title="Sales by Category"
/>
```

----------------------------------------

TITLE: Formatting with JavaScript Format Function
DESCRIPTION: This markdown snippet shows how to use the JavaScript format function for inline data formatting. The `fmt` function formats a calculation result using a specified format code. This requires a markdown environment that supports code execution and access to data objects such as `sales_per_year`.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/formatting/index.md#2025-04-21_snippet_5

LANGUAGE: markdown
CODE:
```
```sql sales_per_year
select
    date_part('year', order_datetime) AS year,
    sum(sales) AS total_sales
from needful_things.orders
group by year
order by year desc
```

Sales are {fmt(sales_per_year[0].total_sales - sales_per_year[1].total_sales, '+#,##0;-#,##0')} vs last year.
```

----------------------------------------

TITLE: Conditional Logic with If/Else in Evidence
DESCRIPTION: This code snippet demonstrates the general structure of if/else conditional blocks in Evidence. It shows how to display different content based on whether a condition, or a series of conditions, are met.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/if-else/index.md#2025-04-21_snippet_0

LANGUAGE: javascript
CODE:
```

{#if condition}

Display some content.

{:else if another condition}

Another thing instead.

{:else }

Something completely different.

{/if}

```

----------------------------------------

TITLE: Custom Svelte Component for Evidence
DESCRIPTION: Example of a custom Svelte component for Evidence, showing how to accept props, import Evidence components, and structure the component.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/custom/custom-component/index.md#2025-04-21_snippet_2

LANGUAGE: html
CODE:
```
<!-- To allow the component to accept data, you need to use the 'export let' syntax
     If you need any Evidence components inside your custom component, you must import them explicitly -->
&lt;script&gt;
	export let myData;
	import { BarChart } from '@evidence-dev/core-components';
&lt;/script&gt;

<p>
	Here is a BarChart in a Component, with some accompanying text. Components stored in the
	/components/ folder will be included in your app.
</p>

<BarChart data={myData} />
```

----------------------------------------

TITLE: Displaying Single Value in Text using Evidence Value Component
DESCRIPTION: This example shows how to use the Value component to display a single value from a SQL query result within text. It includes both the SQL query and the markdown syntax for embedding the value.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/components/index.md#2025-04-21_snippet_1

LANGUAGE: sql
CODE:
```
SELECT
    '2021-01-01' AS date,
    100 AS num_orders
```

LANGUAGE: markdown
CODE:
```
The number of orders yesterday was <Value data = {orders} column = num_orders />.
```

----------------------------------------

TITLE: Executing SQL Queries Using Markdown and DuckDB
DESCRIPTION: This snippet demonstrates how to run SQL queries in Evidence using markdown code fences with the DuckDB dialect. It requires a query name to be specified after the opening code fence. Key dependencies include a data source like 'needful_things'. The input is a SQL query embedded within markdown, and the output is the query result integrated into components.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/queries/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
```sql sales_by_category
select 
  category, sum(sales) as sales
from needful_things.orders
group by 1
```
```

----------------------------------------

TITLE: Rendering Sparklines in DataTable with Svelte
DESCRIPTION: This Svelte component uses the DataTable to display category data with sparkline, sparkarea, and sparkbar visualizations. It demonstrates how to configure different types of sparklines with custom colors.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_11

LANGUAGE: svelte
CODE:
```
<DataTable data={categories}>
    <Column id=category/>
    <Column id=sales title="Orders" contentType=sparkline sparkX=date sparkY=sales />
    <Column id=sales title="Sales" contentType=sparkarea sparkX=date sparkY=sales sparkColor=#53768a/>
    <Column id=sales title="AOV" contentType=sparkbar sparkX=date sparkY=sales sparkColor=#97ba99/>
</DataTable>
```

----------------------------------------

TITLE: Conditional Rendering with If / Else
DESCRIPTION: This JavaScript snippet illustrates how to use conditional rendering in Evidence Markdown. It checks the sales data between months and displays different messages based on the results.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_4

LANGUAGE: javascript
CODE:
```
{#if orders_by_month[0].sales_usd > orders_by_month[1].sales_usd}

Sales are up month-over-month.

{:else}

Sales are down vs last month. See [category detail](/sales-by-category).

{/if}
```

----------------------------------------

TITLE: Rendering Line Chart with Labels and Reference Areas in HTML
DESCRIPTION: This code snippet illustrates a LineChart configuring multiple reference areas with personalized label positions and colors over a dataset, allowing for immediate interpretation of data points.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/annotations/index.md#2025-04-21_snippet_13

LANGUAGE: html
CODE:
```
<LineChart data={orders_by_month} x=month y=sales yFmt=usd0 >
    <ReferenceArea xMax='2019-04-01' label=info color=info/>
    <ReferenceArea xMin='2019-04-01' xMax='2019-11-01' label=negative color=negative/>
    <ReferenceArea xMin='2019-11-01' xMax='2020-07-01' label=warning color=warning/>
    <ReferenceArea xMin='2020-07-01' xMax='2021-02-01' label=positive color=positive/>
    <ReferenceArea xMin='2021-02-01' xMax='2021-09-01' label=base-content-muted color=base-content-muted/>
    <ReferenceArea xMin='2021-09-01' label=custom color=#f2dbff labelColor=#4d1070/>
</LineChart>
```

----------------------------------------

TITLE: Querying Most Posted Tags by Time Period in SQL
DESCRIPTION: This SQL query identifies the top 5 most frequently used hashtags within a specified time range. It counts the number of posts for each hashtag per day, groups the results, and filters to include only the most popular tags.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/test-env/pages/social-media/trends.md#2025-04-21_snippet_0

LANGUAGE: sql
CODE:
```
WITH EVERYTHING AS (
    SELECT
        COUNT(*) as post_count,
        DATE_TRUNC('day', p.created_at) as w,
        h.tag,
        h.id as hashtag_id
    FROM hashtags h
        INNER JOIN post_tags pt on h.id = pt.hashtag_id
        INNER JOIN posts p on pt.post_id = p.id

    WHERE w > (CURRENT_DATE - INTERVAL ${inputs.time_range.value})
    GROUP BY DATE_TRUNC('day', p.created_at), h.tag, h.id
    ORDER BY 2, 1 desc
)
    SELECT * FROM EVERYTHING
    GROUP BY ALL
    HAVING tag IN (
        SELECT tag FROM EVERYTHING GROUP BY ALL ORDER BY SUM(post_count) DESC LIMIT 5
    )
```

----------------------------------------

TITLE: Configuring Inputs in Frontmatter for Evidence Pages
DESCRIPTION: This snippet demonstrates how to configure inputs in the frontmatter of an Evidence page. It shows the structure for defining input options, including the name, type, and default value.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/inputs/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
---
title: My Page
inputs:
  - name: region
    type: select
    options: ["North", "South", "East", "West"]
    default: "North"
  - name: product
    type: text
    default: "Widget"
---
```

----------------------------------------

TITLE: Using API Key from Environment Variable in JavaScript
DESCRIPTION: This JavaScript snippet retrieves an API key from an environment variable and uses it to authenticate a request to an API.  It fetches data from the API and exports the results. It shows how to access and use environment variables within a JavaScript data source in Evidence.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/data-sources/javascript/index.md#2025-04-21_snippet_2

LANGUAGE: javascript
CODE:
```
let key = process.env.EVIDENCE_API_KEY;
let url = 'https://whatever.com/api';

const response = await fetch(url, {
	headers: {
		'x-api-key': key
	}
});

const json = await response.json();
const data = json.results;

export { data };
```

----------------------------------------

TITLE: Format and Limit Country Data - SQL
DESCRIPTION: This SQL query retrieves all columns from the countries dataset and limits the result to 5 records, facilitating a concise preview of the dataset.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/tables/total-rows/+page.md#2025-04-21_snippet_2

LANGUAGE: SQL
CODE:
```
select * from ${countries}
limit 5
```

----------------------------------------

TITLE: Using Evidence-flavored Markdown
DESCRIPTION: This snippet demonstrates the basic usage of Evidence-flavored Markdown for structuring reports. It includes support for lists, bolding, and images, showcasing the flexibility of Markdown syntax in Evidence.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/syntax/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
---
title: Evidence uses Markdown
---

Markdown can be used to write expressively in text.

- it supports lists,
- **bolding**, _italics_ and `inline code`,
- links to [external sites](https://google.com) and other [Evidence pages](/another/page)

## Images 🖼️

Evidence looks for images in your `static` folder, e.g. `static/my-logo.png`.
![Company Logo](/my-logo.png)
```

----------------------------------------

TITLE: Referencing Query Results in Evidence Components
DESCRIPTION: Illustrates referencing SQL query results within Evidence components by utilizing the 'data={query_name}' syntax. This is applicable following successful query execution, where specified query names map to corresponding components. The snippet shows an example with a 'LineChart' component receiving data from 'sales_by_category'.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/queries/index.md#2025-04-21_snippet_1

LANGUAGE: markdown
CODE:
```
<LineChart data={sales_by_category}/>
```

----------------------------------------

TITLE: Basic BigValue Implementation
DESCRIPTION: Shows the basic implementation of BigValue component with sparkline and comparison features.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/big-value/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
<BigValue 
  data={orders_with_comparisons} 
  value=num_orders
  sparkline=month
  comparison=order_growth
  comparisonFmt=pct1
  comparisonTitle="vs. Last Month"
/>
```

----------------------------------------

TITLE: Rendering a Bubble Map with Color Palette in Svelte
DESCRIPTION: This snippet demonstrates how to render a BubbleMap component in Svelte, specifying data points and a color palette to create a gradient based on sales values. Important props such as latitude, longitude, value, and size are bound to the respective data fields to visualize the grouped locations effectively.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/maps/bubble-map/index.md#2025-04-21_snippet_5

LANGUAGE: svelte
CODE:
```
<BubbleMap
    data={grouped_locations}
    lat=lat
    long=long
    value=sales
    size=sales
    colorPalette={['#C65D47', '#4A8EBA']}
    valueFmt=usd
/>
```

----------------------------------------

TITLE: Creating a Grid Layout with Charts in Svelte
DESCRIPTION: Svelte code demonstrating how to use the Grid component to arrange various chart types in a 2-column layout. It includes LineChart, BarChart, ScatterPlot, and AreaChart components.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/ui/grid/index.md#2025-04-21_snippet_1

LANGUAGE: svelte
CODE:
```
<Grid cols=2>
    <LineChart data={orders_by_category} x=order_month y=orders/>
    <BarChart data={orders_by_category} x=order_month y=orders fillColor=#00b4e0/>
    <ScatterPlot data={orders_by_category} x=order_month y=orders fillColor=#015c08/>
    <AreaChart data={orders_by_category} x=order_month y=orders fillColor=#b8645e lineColor=#b8645e/>
</Grid>
```

----------------------------------------

TITLE: DataTable with Sorting
DESCRIPTION: Svelte component that sorts the DataTable by the 'sales' column in descending order while displaying only specific columns.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_7

LANGUAGE: svelte
CODE:
```
<DataTable data={orders_summary} sort="sales desc">
    <Column id=category/> 
    <Column id=item/> 
    <Column id=sales fmt=usd/> 
</DataTable>
```

----------------------------------------

TITLE: Stringifying Query Results in JavaScript
DESCRIPTION: These JavaScript expressions demonstrate how to stringify query results in Evidence. They use the JSON.stringify() method to convert the 'working_query' data object into a JSON string.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/queries/writing-queries/+page.md#2025-04-21_snippet_2

LANGUAGE: javascript
CODE:
```
{JSON.stringify(data.working_query)}
```

LANGUAGE: javascript
CODE:
```
{JSON.stringify(working_query)}
```

----------------------------------------

TITLE: Rendering Line Chart with Multiple Reference Areas in HTML
DESCRIPTION: This code creates a LineChart that illustrates sales data with multiple reference areas, each configured to show informative labels and positioning options within a specific date range.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/annotations/index.md#2025-04-21_snippet_12

LANGUAGE: html
CODE:
```
<LineChart data={orders_by_month} x=month y=sales yFmt=usd0>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=topLeft labelPosition=topLeft areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=top labelPosition=top areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=topRight labelPosition=topRight areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=left labelPosition=left areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=center labelPosition=center areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=right labelPosition=right areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=bottomLeft labelPosition=bottomLeft areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=bottom labelPosition=bottom areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
    <ReferenceArea xMin='2019-07-01' xMax='2021-07-31' label=bottomRight labelPosition=bottomRight areaColor="hsla(206.25, 80%, 80%, 0.01)"/>
</LineChart>
```

----------------------------------------

TITLE: DuckDB Source Query Example
DESCRIPTION: SQL query to select all data from orders table in the needful_things database

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/build-your-first-app/index.md#2025-04-21_snippet_2

LANGUAGE: sql
CODE:
```
select * from orders
```

----------------------------------------

TITLE: Defining Chart Properties in JSX with React
DESCRIPTION: This snippet defines properties for a chart component using JSX syntax. Each property is annotated with a name, description, possible options, and default values where applicable, facilitating configuration by users.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/bar-chart/index.md#2025-04-21_snippet_13

LANGUAGE: JavaScript
CODE:
```
<PropListing
    name=title
    description="Chart title. Appears at top left of chart."
    options="string"
/>
<PropListing
    name=subtitle
    description="Chart subtitle. Appears just under title."
    options="string"
/>
<PropListing
    name=legend
    description="Turns legend on or off. Legend appears at top center of chart."
    options={['true', 'false']}
    defaultValue="true for multiple series"
/>
<PropListing
    name=chartAreaHeight
    description="Minimum height of the chart area (excl. header and footer) in pixels. Adjusting the height affects all viewport sizes and may impact the mobile UX."
    options="number"
    defaultValue=180
/>
<PropListing
    name=renderer
    description="Which chart renderer type (canvas or SVG) to use. See ECharts' <a href='https://echarts.apache.org/handbook/en/best-practices/canvas-vs-svg/' class=markdown>documentation on renderers</a>."
    options={['canvas', 'svg']}
    defaultValue=canvas
/>
<PropListing
    name="downloadableData"
    description="Whether to show the download button to allow users to download the data"
    required=false
    options={['true', 'false']}
    defaultValue="true"
/>
<PropListing
    name="downloadableImage"
    description="Whether to show the button to allow users to save the chart as an image"
    required=false
    options={['true', 'false']}
    defaultValue="true"
/>
```

----------------------------------------

TITLE: Stacked Bar Chart with Value Labels in Evidence
DESCRIPTION: A stacked bar chart displaying sales data by category over months with value labels shown for each segment of the stacked bars.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/charts/bar-chart/+page.md#2025-04-21_snippet_13

LANGUAGE: jsx
CODE:
```
<BarChart 
    data={orders_by_category_2021} 
    x=month 
    y=sales_usd0k 
    series=category
    labels=true
/>
```

----------------------------------------

TITLE: Compiled SQL from Chained Queries
DESCRIPTION: Provides the compiled SQL output after query chaining in Evidence. Demonstrates SQL transformation and execution logic specific to Evidence's processing engine. Input queries are resolved into executable SQL code with nested sub-query structures.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/queries/index.md#2025-04-21_snippet_3

LANGUAGE: sql
CODE:
```
select
    avg(sales) as average_sales
from (
    select
        item,
        sum(sales) as sales
    from needful_things.orders
    group by 1
)
```

----------------------------------------

TITLE: Querying Simplified Country Data - SQL
DESCRIPTION: This SQL snippet is a simplified version of the previous one, focusing on extracting fewer fields from the dataset. It queries the date, value in USD, country name, category, country ID, country code, and associated URLs. Like the previous version, it uses 'UNION ALL' for compiling results. The requirements remain unchanged.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/tables/new-table/+page.md#2025-04-21_snippet_2

LANGUAGE: sql
CODE:
```
select date '2020-04-30' as date, 87 as value_usd, 'Austria' as country, 'B' as category, 100384 as country_id, 'AT' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/AT.png' as flag, 'https://www.google.ca/search?q=austria' as country_url
union all
select date '2020-05-01' as date, 95 as value_usd, 'Australia' as country, 'C' as category, 104942 as country_id, 'AU' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/AU.png' as flag, 'https://www.google.ca/search?q=australia' as country_url
union all
select date '2020-05-02' as date, 163 as value_usd, 'Brazil' as country, 'A' as category, 100842 as country_id, 'BR' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/BR.png' as flag, 'https://www.google.ca/search?q=brazil' as country_url
union all
select date '2020-05-03' as date, 174 as value_usd, 'Canada' as country, 'A' as category, 104975 as country_id, 'CA' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/CA.png' as flag, 'https://www.google.ca/search?q=canada' as country_url
union all
select date '2020-05-04' as date, 214 as value_usd, 'Chile' as country, 'B' as category, 100644 as country_id, 'CL' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/CL.png' as flag, 'https://www.google.ca/search?q=chile' as country_url
union all
select date '
```

----------------------------------------

TITLE: Implementing Various Chart Components in HTML
DESCRIPTION: Demonstrates usage of different chart components including LineChart, AreaChart, BarChart, ScatterPlot, and BubbleChart with various configurations and data sources.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/chart-testing/stringMulty/+page.md#2025-04-21_snippet_1

LANGUAGE: html
CODE:
```
<h1>Series Column with Numeric X Axis</h1>
<h2>Line Chart</h2>
<LineChart data={full} title="Full Data"/>
<LineChart data={nulls}  title="Nulls"/>
<Chart data={full}>
    <Line y=y1/>
    <Bar y=y2/>
</Chart>

<h2>Area Chart</h2>
<AreaChart data={full}  title="Full Data"/>
<AreaChart data={nulls}  title="Nulls"/>

<h2>100% Stacked Area Chart</h2>
<AreaChart data={full}  title="Full Data" type=stacked100/>
<AreaChart data={nulls}  title="Nulls" type=stacked100/>

<h2>Stacked Bar Chart</h2>
<BarChart data={full}  title="Full Data"/>
<BarChart data={nulls}  title="Nulls"/>

<h2>100% Stacked Bar Chart</h2>
<BarChart data={full}  title="Full Data" type=stacked100/>
<BarChart data={nulls}  title="Nulls" type=stacked100/>

<h2>Horizontal Stacked Bar Chart</h2>
<BarChart data={full}  swapXY=true title="Full Data" xType=value sort={false}/>
<BarChart data={nulls}  swapXY=true title="Nulls" sort=false/>

<h2>Horizontal 100% Stacked Bar Chart</h2>
<BarChart data={full}  swapXY=true title="Full Data" xType=value sort={false} type=stacked100/>
<BarChart data={nulls}  swapXY=true title="Nulls" sort=false type=stacked100/>

<h2>Grouped Bar Chart</h2>
<BarChart data={full}  type=grouped title="Full Data"/>
<BarChart data={nulls}  type=grouped title="Nulls"/>

<h2>horizontal Grouped Bar Chart</h2>
<BarChart data={full}  swapXY=true type=grouped title="Full Data"/>
<BarChart data={nulls}  swapXY=true type=grouped title="Nulls"/>

<h2>Scatter Plot</h2>
<ScatterPlot data={full}  title="Full Data"/>
<ScatterPlot data={nulls}  title="Nulls"/>

<h2>Bubble Chart</h2>
<BubbleChart data={full} size=y1 title="Full Data" legend=true/>
<BubbleChart data={nulls}  size=y1 title="Nulls"/>
```

----------------------------------------

TITLE: Querying Sales Data by Category - SQL
DESCRIPTION: This SQL snippet aggregates sales data from orders grouped by category. It constructs a link for each category and computes the total sales in USD, ordering the results by sales amount in descending order. The query uses a placeholder for the orders data source.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/templated-pages/+page.md#2025-04-21_snippet_0

LANGUAGE: sql
CODE:
```
select
category,
'/templated-pages/' || category as category_link,
sum(sales_usd0k) as sales_usd0k
from ${orders_by_category}
group by category
order by sales_usd0k desc
```

----------------------------------------

TITLE: Implementing Horizontal Bar Chart in Evidence
DESCRIPTION: Demonstrates how to create a horizontal bar chart by using the swapXY property. This chart displays total sales for different items, formatted in thousands of dollars.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/bar-chart/index.md#2025-04-21_snippet_5

LANGUAGE: markdown
CODE:
```
<BarChart 
    data={orders_by_item_all_time}
    x=item
    y=sales 
    swapXY=true
    yFmt=usd0k
/>
```

----------------------------------------

TITLE: Basic Area Chart Implementation
DESCRIPTION: Simple area chart showing sales data over time using the orders_by_month dataset.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/area-chart/index.md#2025-04-21_snippet_0

LANGUAGE: markdown
CODE:
```
<AreaChart 
    data={orders_by_month}
    x=month
    y=sales
/>
```

----------------------------------------

TITLE: Stacked Area Chart Implementation
DESCRIPTION: Area chart showing sales data by category over time using stacked layout.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/area-chart/index.md#2025-04-21_snippet_1

LANGUAGE: markdown
CODE:
```
<AreaChart 
    data={orders_by_category_2021}
    x=month
    y=sales
    series=category
/>
```

----------------------------------------

TITLE: Scheduled Build with GitHub Actions for Netlify
DESCRIPTION: This YAML snippet is a GitHub Actions workflow for scheduling Netlify builds. It demonstrates how to define a cron job that triggers a build webhook for Netlify daily at a specified time.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/deployment/self-host/netlify/index.md#2025-04-21_snippet_1

LANGUAGE: yaml
CODE:
```
name: Schedule Netlify Build
on:
  workflow_dispatch:
  schedule:
    - cron: '0 10 * * *' # Once a day around 6am ET (10am UTC)
jobs:
  build:
    name: Request Netlify Webhook
    runs-on: ubuntu-latest
    steps:
      - name: POST to Build Hook
        env:
          BUILD_HOOK: ${{ secrets.NETLIFY_BUILD_HOOK }}
        run: curl -X POST -d {} $BUILD_HOOK
```

----------------------------------------

TITLE: Adding Total Row with Default Aggregation in DataTable
DESCRIPTION: This snippet demonstrates how to add a total row to a DataTable with default sum aggregation. It also shows how to format different columns, including percentage and large number formatting.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_13

LANGUAGE: svelte
CODE:
```
<DataTable data={country_example} totalRow=true rows=5>
  <Column id=country/>
  <Column id=gdp_usd/>
  <Column id=gdp_growth fmt='pct2'/>
  <Column id=population fmt='#,##0"M"'/>
</DataTable>
```

----------------------------------------

TITLE: Points from Data Callout Example (HTML, JSX)
DESCRIPTION: Demonstrates how to create callouts from a dataset that identifies specific points of interest on a line chart through SQL-like querying. This enables multiple callouts from a single dataset.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/charts/annotations/index.md#2025-04-21_snippet_23

LANGUAGE: html
CODE:
```
<LineChart data={orders_by_month} x=month y=sales yFmt=usd0>
    <Callout data={sales_drops} x=month y=sales label=label labelPosition=bottom align=right />
</LineChart>
```

----------------------------------------

TITLE: Implementing Multi-Select Dropdown in Evidence
DESCRIPTION: Shows how to create a multi-select dropdown using the 'multiple' attribute, and how to use the selected values in an SQL query with the IN operator.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/input-components/dropdown/+page.md#2025-04-21_snippet_7

LANGUAGE: svelte
CODE:
```
<Dropdown 
    name=multi_select 
    data={categories} 
    value=category multiple 
/>
```

----------------------------------------

TITLE: Configuring Bar Chart Columns in DataTable
DESCRIPTION: This example shows how to create bar chart columns in a DataTable. It demonstrates different ways to customize the bar charts, including setting colors for bars and backgrounds.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_12

LANGUAGE: svelte
CODE:
```
<DataTable data={country_summary}>
	<Column id=country />
	<Column id=category align=center/>
	<Column id=value_usd title="Sales" contentType=bar/>
  	<Column id=value_usd title="Sales" contentType=bar barColor=#aecfaf/>
  	<Column id=value_usd title="Sales" contentType=bar barColor=#ffe08a backgroundColor=#ebebeb/>
</DataTable>
```

----------------------------------------

TITLE: DataTable with Search Functionality
DESCRIPTION: Svelte component that enables search functionality on the DataTable by setting the search property to true.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/components/data/data-table/index.md#2025-04-21_snippet_6

LANGUAGE: svelte
CODE:
```
<DataTable data={orders_summary} search=true/>
```

----------------------------------------

TITLE: Variable Interpolation in SQL Query
DESCRIPTION: Demonstrates correct template string syntax for variable interpolation in SQL queries.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/packages/lib/sdk/README.md#2025-04-21_snippet_1

LANGUAGE: svelte
CODE:
```
<pre><code evidence-query-name="myFirstQuery" lang="sql">
SELECT * FROM my_first_table WHERE user_id = '${selectedUserId}'
</code></pre>
```

----------------------------------------

TITLE: Using SQL File Queries in Evidence
DESCRIPTION: Explains how to set up and use SQL queries stored in files within the Evidence framework. Files must be placed in a 'queries' directory and referenced in the markdown frontmatter. This setup facilitates reuse and modularity across multiple pages.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/docs/pages/core-concepts/queries/index.md#2025-04-21_snippet_4

LANGUAGE: yaml
CODE:
```
---
queries:
  - q4_data: my_file_query.sql
  - q4_sales_reps: some_category/my_category_file_query.sql
---
```

----------------------------------------

TITLE: Querying Economic Data - SQL
DESCRIPTION: This SQL snippet retrieves economic data for various countries, including date, USD values, year-over-year changes, country name, category, country ID, country code, and URLs for flags and detailed searches. The data is unified using 'UNION ALL' to compile results for subsequent analysis or reporting. This query requires no additional dependencies.

SOURCE: https://github.com/evidence-dev/evidence/blob/next/sites/example-project/src/pages/tables/new-table/+page.md#2025-04-21_snippet_0

LANGUAGE: sql
CODE:
```
select date '2020-04-30' as date, 87 as value_usd, 0.0234 as yoy, 'Austria' as country, 'B' as category, 100384 as country_id, 'AT' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/AT.png' as flag, 'https://www.google.ca/search?q=austria' as country_url
union all
select date '2020-05-01' as date, 95 as value_usd, 0.0534 as yoy, 'Australia' as country, 'C' as category, 104942 as country_id, 'AU' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/AU.png' as flag, 'https://www.google.ca/search?q=australia' as country_url
union all
select date '2020-05-02' as date, 163 as value_usd, 0.0264 as yoy, 'Brazil' as country, 'A' as category, 100842 as country_id, 'BR' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/BR.png' as flag, 'https://www.google.ca/search?q=brazil' as country_url
union all
select date '2020-05-03' as date, 174 as value_usd, 0.0727 as yoy, 'Canada' as country, 'A' as category, 104975 as country_id, 'CA' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/CA.png' as flag, 'https://www.google.ca/search?q=canada' as country_url
union all
select date '2020-05-04' as date, 214 as value_usd, -0.1223 as yoy, 'Chile' as country, 'B' as category, 100644 as country_id, 'CL' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/CL.png' as flag, 'https://www.google.ca/search?q=chile' as country_url
union all
select date '2020-05-05' as date, 342 as value_usd, 0.0124 as yoy, 'Denmark' as country, 'B' as category, 102948 as country_id, 'DK' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/DK.png' as flag, 'https://www.google.ca/search?q=denmark' as country_url
union all
select date '2020-05-06' as date, 331 as value_usd, 0.0252 as yoy, 'Estonia' as country, 'D' as category, 102495 as country_id, 'EE' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/EE.png' as flag, 'https://www.google.ca/search?q=estonia' as country_url
union all
select date '2020-05-07' as date, 98 as value_usd, 0.0754 as yoy, 'Finland' as country, 'B' as category, 104962 as country_id, 'FI' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/FI.png' as flag, 'https://www.google.ca/search?q=finland' as country_url
union all
select date '2020-05-08' as date, 128 as value_usd, -0.0246 as yoy, 'Ghana' as country, 'C' as category, 100599 as country_id, 'GH' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/GH.png' as flag, 'https://www.google.ca/search?q=ghana' as country_url
union all
select date '2020-05-09' as date, 153 as value_usd, 0.0447 as yoy, 'Honduras' as country, 'D' as category, 102494 as country_id, 'HN' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/HN.png' as flag, 'https://www.google.ca/search?q=honduras' as country_url
union all
select date '2020-05-10' as date, 384 as value_usd, -0.0255 as yoy, 'India' as country, 'A' as category, 101948 as country_id, 'IN' as country_code,  'https://flaglog.com/codes/standardized-rectangle-120px/IN.png' as flag, 'https://www.google.ca/search?q=india' as country_url
union all
select date '2020-05-11' as date, 234 as value_usd, 0.0855 as yoy, 'Ireland' as country, 'B' as category, 100987 as country_id, 'IE' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/IE.png' as flag, 'https://www.google.ca/search?q=ireland' as country_url
union all
select date '2020-05-12' as date, 67 as value_usd, 0.0635 as yoy, 'Jamaica' as country, 'C' as category, 101248 as country_id, 'JM' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/JM.png' as flag, 'https://www.google.ca/search?q=jamaica' as country_url
union all
select date '2020-05-13' as date, 125 as value_usd, -0.0232 as yoy, 'Kenya' as country, 'C' as category, 101947 as country_id, 'KE' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/KE.png' as flag, 'https://www.google.ca/search?q=kenya' as country_url
union all
select date '2020-05-14' as date, 118 as value_usd, -0.0343 as yoy, 'Lebanon' as country, 'D' as category, 108849 as country_id, 'LB' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/LB.png' as flag, 'https://www.google.ca/search?q=lebanon' as country_url
union all
select date '2020-05-15' as date, 263 as value_usd, 0.0883 as yoy, 'Mexico' as country, 'B' as category, 100763 as country_id, 'MX' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/MX.png' as flag, 'https://www.google.ca/search?q=mexico' as country_url
union all
select date '2020-05-16' as date, 211 as value_usd, 0.0395 as yoy, 'Nigeria' as country, 'A' as category, 100837 as country_id, 'NG' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/NG.png' as flag, 'https://www.google.ca/search?q=nigeria' as country_url
union all
select date '2020-05-17' as date, 192 as value_usd, 0.0234 as yoy, 'Oman' as country, 'D' as category, 100993 as country_id, 'OM' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/OM.png' as flag, 'https://www.google.ca/search?q=oman' as country_url
union all
select date '2020-05-18' as date, 59 as value_usd, 0.0828 as yoy, 'Philippines' as country, 'D' as category, 104128 as country_id, 'PH' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/PH.png' as flag, 'https://www.google.ca/search?q=philippines' as country_url
union all
select date '2020-05-19' as date, 113 as value_usd, 0.0554 as yoy, 'Qatar' as country, 'C' as category, 100181 as country_id, 'QA' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/QA.png' as flag, 'https://www.google.ca/search?q=qatar' as country_url
union all
select date '2020-05-20' as date, 190 as value_usd, 0.0134 as yoy, 'Romania' as country, 'A' as category, 101384 as country_id, 'RO' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/RO.png' as flag, 'https://www.google.ca/search?q=romania ! startups' as country_url
union all
select date '2020-05-21' as date, 190 as value_usd, -0.0554 as yoy, 'Sweden' as country, 'B' as category, 101847 as country_id, 'SE' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/SE.png' as flag, 'https://www.google.ca/search?q=sweden' as country_url
union all
select date '2020-05-22' as date, 248 as value_usd, 0.0254 as yoy, 'Thailand' as country, 'C' as category, 104837 as country_id, 'TH' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/TH.png' as flag, 'https://www.google.ca/search?q=thailand' as country_url
union all
select date '2020-05-23' as date, 168 as value_usd, 0.0294 as yoy, 'Ukraine' as country, 'C' as category, 101938 as country_id, 'UA' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/UA.png' as flag, 'https://www.google.ca/search?q=ukraine' as country_url
union all
select date '2020-05-24' as date, 101 as value_usd, 0.0234 as yoy, 'Vietnam' as country, 'A' as category, 104948 as country_id, 'VN' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/VN.png' as flag, 'https://www.google.ca/search?q=vietnam' as country_url
union all
select date '2020-05-25' as date, 67 as value_usd, 0.0294 as yoy, 'Yemen' as country, 'B' as category, 100774 as country_id, 'YE' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/YE.png' as flag, 'https://www.google.ca/search?q=yemen' as country_url
union all
select date '2020-05-26' as date, 100 as value_usd, 0.011 as yoy, 'Zimbabwe' as country, 'A' as category, 100337 as country_id, 'ZW' as country_code, 'https://flaglog.com/codes/standardized-rectangle-120px/ZW.png' as flag, 'https://www.google.ca/search?q=zimbabwe' as country_url
```