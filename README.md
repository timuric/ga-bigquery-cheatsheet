# Google Analytics Cookbook for BigQuery
Cheat sheet for Bigquery using Google Analytics dataset

### Table of contents
- [Working with dates](#working-with-dates)
- [Data Quality Check](#data-quality-check)
  - [Events](#events)
  - [Ecommerce](#ecommerce)
  - [User](#user)
  - [Items](#items)
  - [Traffic Source](#traffic-source)
  - [Device](#device)
  - [Geo](#geo)
- [Funnel Analysis](#funnel-analysis)
  - [Checkout by Basket Size](#checkout-by-basket-size)

## Working with dates

Relative date range:

```sql
FROM `project.analytics_123.events_*`
WHERE regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 30) AND format_date('%Y%m%d', current_date())
```

Today:

```sql
FROM `project.analytics_123.events_*`
WHERE regexp_extract(_table_suffix,'[0-9]+') = format_date('%Y%m%d', current_date())
```

## Data Quality Check

### Null Checks

Items:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(items.item_id IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_id,
ROUND(COUNTIF(items.item_name IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_name,
ROUND(COUNTIF(items.item_brand IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_brand,
ROUND(COUNTIF(items.item_variant IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_variant,
ROUND(COUNTIF(items.item_category IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_category,
ROUND(COUNTIF(items.item_category2 IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_category2,
ROUND(COUNTIF(items.item_category3 IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_category3,
ROUND(COUNTIF(items.item_category4 IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_category4,
ROUND(COUNTIF(items.item_category5 IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_category5,
ROUND(COUNTIF(items.price_in_usd IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_price_in_usd,
ROUND(COUNTIF(items.price IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_price,
ROUND(COUNTIF(items.quantity IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_quantity,
ROUND(COUNTIF(items.item_revenue_in_usd IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_revenue_in_usd,
ROUND(COUNTIF(items.item_revenue IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_revenue,
ROUND(COUNTIF(items.item_refund_in_usd IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_refund_in_usd,
ROUND(COUNTIF(items.item_refund IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_refund,
ROUND(COUNTIF(items.coupon IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_coupon,
ROUND(COUNTIF(items.affiliation IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_affiliation,
ROUND(COUNTIF(items.location_id IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_location_id,
ROUND(COUNTIF(items.item_list_id IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_list_id,
ROUND(COUNTIF(items.item_list_name IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_list_name,
ROUND(COUNTIF(items.item_list_index IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_item_list_index,
ROUND(COUNTIF(items.promotion_id IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_promotion_id,
ROUND(COUNTIF(items.promotion_name IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_promotion_name,
ROUND(COUNTIF(items.creative_name IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_creative_name,
ROUND(COUNTIF(items.creative_slot IS NOT NULL) * 100.0 / COUNT(*),2 ) as items_creative_slot
FROM `project.analytics_123.events_*`
  UNNEST(event_params) AS events,
  UNNEST(items) AS items
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
ORDER BY 1
LIMIT 100
```

Ecommerce:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(ecommerce.total_item_quantity IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_total_item_quantity,
ROUND(COUNTIF(ecommerce.purchase_revenue_in_usd IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_purchase_revenue_in_usd,
ROUND(COUNTIF(ecommerce.purchase_revenue IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_purchase_revenue,
ROUND(COUNTIF(ecommerce.refund_value_in_usd IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_refund_value_in_usd,
ROUND(COUNTIF(ecommerce.refund_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_refund_value,
ROUND(COUNTIF(ecommerce.shipping_value_in_usd IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_shipping_value_in_usd,
ROUND(COUNTIF(ecommerce.shipping_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_shipping_value,
ROUND(COUNTIF(ecommerce.tax_value_in_usd IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_tax_value_in_usd,
ROUND(COUNTIF(ecommerce.tax_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_value,
ROUND(COUNTIF(ecommerce.transaction_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_transaction_id,
ROUND(COUNTIF(ecommerce.unique_items IS NOT NULL) * 100.0 / COUNT(*), 2) AS ecommerce_unique_items,
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
ORDER BY 1
LIMIT 100
```

User:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(is_active_user IS NOT NULL) * 100.0 / COUNT(*), 2) AS is_active_user,
ROUND(COUNTIF(user_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS user_id,
ROUND(COUNTIF(user_first_touch_timestamp IS NOT NULL) * 100.0 / COUNT(*), 2) AS user_first_touch_timestamp,
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
order by 1
limit 100
```

Events:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(events.value.double_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS events_value_double_value,
ROUND(COUNTIF(event_timestamp IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_timestamp,
ROUND(COUNTIF(event_date IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_date,
ROUND(COUNTIF(event_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_name,
ROUND(COUNTIF(event_value_in_usd IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_event_value_in_usd,
ROUND(COUNTIF(event_server_timestamp_offset IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_server_timestamp_offset,
ROUND(COUNTIF(event_params.key IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_params_key,
ROUND(COUNTIF(event_params.value IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_params_value,
ROUND(COUNTIF(event_params.value.string_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_params_value_string_value,
ROUND(COUNTIF(event_params.value.int_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_params_value_int_value,
ROUND(COUNTIF(event_params.value.double_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_params_value_double_value,
ROUND(COUNTIF(event_params.value.float_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_params_value_float_value,
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
order by 1
limit 100
```

Geo:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(geo.continent IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_continent,
ROUND(COUNTIF(geo.country IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_country,
ROUND(COUNTIF(geo.region IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_region,
ROUND(COUNTIF(geo.sub_continent IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_sub_continent,
ROUND(COUNTIF(geo.metro IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_metro,
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
order by 1
limit 100
```

Traffic source:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(traffic_source.name IS NOT NULL) * 100.0 / COUNT(*), 2) AS traffic_source_name,
ROUND(COUNTIF(traffic_source.medium IS NOT NULL) * 100.0 / COUNT(*), 2) AS traffic_source_medium,
ROUND(COUNTIF(traffic_source.source IS NOT NULL) * 100.0 / COUNT(*), 2) AS traffic_source_source,
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
order by 1
limit 100
```

Device:

```sql
select 
event_name,
COUNT(*) as event_count,
ROUND(COUNTIF(device.operating_system IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_operating_system,
ROUND(COUNTIF(device.mobile_brand_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_brand_name,
ROUND(COUNTIF(device.mobile_model_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_model_name,
ROUND(COUNTIF(device.mobile_marketing_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_marketing_name,
ROUND(COUNTIF(device.mobile_os_hardware_model IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_os_hardware_model,
ROUND(COUNTIF(device.advertising_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_advertising_id,
ROUND(COUNTIF(device.browser IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_browser,
ROUND(COUNTIF(device.browser_version IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_browser_version,
ROUND(COUNTIF(device.is_limited_ad_tracking IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_is_limited_ad_tracking
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
order by 1
limit 100
```
### Funnel Analysis

#### Checkout by Basket Size
```sql
with t as (
SELECT
  CASE
    WHEN ecommerce.total_item_quantity < 200 THEN '01. 0-200'
    WHEN ecommerce.total_item_quantity BETWEEN 200 AND 399 THEN '02. 200-400'
    WHEN ecommerce.total_item_quantity BETWEEN 400 AND 599 THEN '03. 400-600'
    WHEN ecommerce.total_item_quantity BETWEEN 600 AND 799 THEN '04. 600-800'
    WHEN ecommerce.total_item_quantity BETWEEN 800 AND 999 THEN '05. 800-1000'
    WHEN ecommerce.total_item_quantity BETWEEN 1000 AND 1199 THEN '06. 1000-1200'
    WHEN ecommerce.total_item_quantity BETWEEN 1200 AND 1399 THEN '07. 1200-1400'
    WHEN ecommerce.total_item_quantity BETWEEN 1400 AND 1599 THEN '08. 1400-1600'
    WHEN ecommerce.total_item_quantity BETWEEN 1600 AND 1799 THEN '09. 1600-1800'
    WHEN events.value.double_value BETWEEN 1800 AND 1999 THEN '10. 1800-2000'
    ELSE '11. 2000-...'
  END AS bins,
  count(DISTINCT CASE
    WHEN event_name = 'begin_checkout' THEN user_pseudo_id
    ELSE NULL
  END) AS begin_checkout,
  count(DISTINCT CASE
    WHEN event_name = 'add_shipping_info' THEN user_pseudo_id
    ELSE NULL
  END) AS add_shipping_info,
  count(DISTINCT CASE
    WHEN event_name = 'add_payment_info' THEN user_pseudo_id
    ELSE NULL
  END) AS add_payment_info,
  count(DISTINCT CASE
    WHEN event_name = 'purchase' THEN user_pseudo_id
    ELSE NULL
  END) AS purchase
FROM
  FROM `project.analytics_123.events_*`
  UNNEST(event_params) AS events
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  AND user_pseudo_id IS NOT NULL
  AND (
    event_name = 'begin_checkout'
    AND events.value.double_value IS NOT NULL
  )
  OR event_name IN(
    'begin_checkout',
    'add_shipping_info',
    'add_payment_info',
    'purchase'
  )
GROUP BY
  1
order by 1
)
select *,
  round((begin_checkout - add_shipping_info) / begin_checkout * 100, 2) as checkout_to_shipping_drop,
  round((add_shipping_info - add_payment_info) / add_shipping_info * 100, 2) as shipping_to_payment_drop,
  round((add_payment_info - purchase) / add_payment_info * 100, 2) as payment_to_purchase_drop
from `t`;
```
