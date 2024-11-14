# Google Analytics Cookbook for BigQuery
Cheat sheet for Bigquery using Google Analytics dataset

## Dates ranges

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
count(*) as event_count,
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
count(*) as event_count,
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
  UNNEST(event_params) AS events,
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
count(*) as event_count,
ROUND(COUNTIF(user_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS user_id,
ROUND(COUNTIF(user_pseudo_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS user_pseudo_id,
FROM `project.analytics_123.events_*`
  UNNEST(event_params) AS events,
  UNNEST(items) AS items
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
count(*) as event_count,
ROUND(COUNTIF(events.value.double_value IS NOT NULL) * 100.0 / COUNT(*), 2) AS events_value_double_value,
ROUND(COUNTIF(event_timestamp IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_timestamp_null_check,
ROUND(COUNTIF(event_date IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_date_null_check,
ROUND(COUNTIF(event_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS event_name_null_check
FROM `project.analytics_123.events_*`
  UNNEST(event_params) AS events,
  UNNEST(items) AS items
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
count(*) as event_count,
ROUND(COUNTIF(geo.continent IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_continent_null_check,
ROUND(COUNTIF(geo.country IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_country_null_check,
ROUND(COUNTIF(geo.region IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_region_null_check,
ROUND(COUNTIF(geo.sub_continent IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_sub_continent_null_check,
ROUND(COUNTIF(geo.metro IS NOT NULL) * 100.0 / COUNT(*), 2) AS geo_metro_null_check,
FROM `project.analytics_123.events_*`
  UNNEST(event_params) AS events,
  UNNEST(items) AS items
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
count(*) as event_count,
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
count(*) as event_count,
ROUND(COUNTIF(device.operating_system IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_operating_system_null_check,
ROUND(COUNTIF(device.mobile_brand_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_brand_name_null_check,
ROUND(COUNTIF(device.mobile_model_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_model_name_null_check,
ROUND(COUNTIF(device.mobile_marketing_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_marketing_name_null_check,
ROUND(COUNTIF(device.mobile_os_hardware_model IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_mobile_os_hardware_model_null_check,
ROUND(COUNTIF(device.advertising_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_advertising_id_null_check,
ROUND(COUNTIF(device.browser IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_browser_null_check,
ROUND(COUNTIF(device.browser_version IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_browser_version_null_check,
ROUND(COUNTIF(device.is_limited_ad_tracking IS NOT NULL) * 100.0 / COUNT(*), 2) AS device_is_limited_ad_tracking_null_check
FROM `project.analytics_123.events_*`
WHERE
  regexp_extract(_table_suffix, '[0-9]+') BETWEEN format_date('%Y%m%d', current_date() - 1) AND format_date('%Y%m%d', current_date())
  group by event_name
order by 1
limit 100

```


