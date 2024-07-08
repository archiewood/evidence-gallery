---
title: Component Gallery
---

```sql orders_by_category
select 
  date_trunc('month', order_datetime) as month,
  category,
  sum(sales) as total_sales,
  count(*) as num_orders,
  total_sales / num_orders as avg_order_value
from needful_things.orders
where order_datetime > '2021-01-01'
and ${inputs.grid}
group by all
order by 3 desc
```

```sql daily_sales
select 
  date_trunc('day', order_datetime) as day,
  sum(sales) as total_sales
from needful_things.orders
where ${inputs.grid}
group by all
```

```sql all_orders
select 
  state, 
  category, 
  item, 
  channel, 
  sales
from needful_things.orders
```

```sql orders_vs_aov
select 
  item,
  category,
  sum(sales) as total_sales,
  count(*) as num_orders,
  total_sales / num_orders as avg_order_value
from needful_things.orders
where ${inputs.grid}
group by all
order by 2 desc
```

```sql orders_by_state
select 
  state,
  sum(sales) as total_sales,
  count(*) as num_orders,
  total_sales / num_orders as avg_order_value
from needful_things.orders
where ${inputs.grid}
and state != 'Alaska' and state != 'Hawaii'
group by all
```

```sql sankey
select
  channel as source,
  'Revenue' as target,
  sum(sales) as sales
from needful_things.orders
where ${inputs.grid}
group by all
union all
select
  'Revenue' as source,
  category as target,
  sum(sales) as sales
from needful_things.orders
where ${inputs.grid}
group by all
```

```sql funnel_data
select 'Page Views' as step, 389 as count union all
select 'Add to Cart', 290 union all
select 'Checkout', 120 union all
select 'Purchase', 50
order by 2 desc
```

```sql daily_orders
select 
  category, 
  dayname(order_datetime) as day,
  case 
    when day = 'Sunday' then 'Su'
    when day = 'Monday' then 'M'
    when day = 'Tuesday' then 'Tu'
    when day = 'Wednesday' then 'W'
    when day = 'Thursday' then 'Th'
    when day = 'Friday' then 'F'
    when day = 'Saturday' then 'Sa'
  end as day_short,
  dayofweek(order_datetime) as day_num, 
  count(*) as order_count 
from needful_things.orders
where ${inputs.grid}
group by all
order by category, day_num
```

<Grid cols=4>
  <BarChart
    title="Bar Chart"
    data={orders_by_category}
    x=month
    y=total_sales
    yFmt=usd
    series=category
    legend=false
    renderer=svg
  />
  <LineChart
    title="Line Chart"
    data={orders_by_category}
    x=month
    y=total_sales
    yFmt=usd
    series=category
    legend=false
  />
  <AreaChart
    title="Area Chart"
    data={orders_by_category}
    x=month
    y=total_sales
    yFmt=usd
    series=category
    legend=false
  />
  <ScatterPlot
    title="Scatter Plot"
    data={orders_vs_aov}
    x=avg_order_value
    y=total_sales
    yFmt=usd0k
    xFmt=usd0
    series=category
    legend=false
  />
  <Histogram 
    title="Histogram"
    data={daily_sales} 
    x=total_sales
    xFmt=usd
  />
  <FunnelChart
    title="Funnel Chart"
    data={funnel_data}
    nameCol=step
    valueCol=count
    height=160
    legend=false
    echartsOptions={{
      series: {
        orient: 'horizontal',
      }
    }}
  />
  <Heatmap
    title="Heatmap"
    data={daily_orders}
    x=day_short
    y=category
    value=order_count
    colorPalette={["rgb(35,106,164)", "rgb(165,205,238)"]}
  />
  <AreaMap 
      data={orders_by_state} 
      areaCol=state
      geoJsonUrl=https://d2ad6b4ur7yvpq.cloudfront.net/naturalearth-3.3.0/ne_110m_admin_1_states_provinces.geojson
      geoId=name
      value=num_orders
      height=200
  />
</Grid>
<Grid cols=2>
  <SankeyDiagram
    title="Sankey Diagram"
    chartAreaHeight=180
    data={sankey}
    sourceCol=source
    targetCol=target
    valueCol=sales
    valueFmt=usd0k
    nodeLabels=full
    echartsOptions={{
      series: {
        layoutIterations: 32
      }
    }}
  />
  <DimensionGrid data={all_orders} label=dimesion metric=sum(sales) name=grid/>
</Grid>