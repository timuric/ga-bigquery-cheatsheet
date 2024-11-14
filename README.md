# Google analytics cookbook for Bigquery
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
ROUND(COUNTIF(items.item_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_item_id_null_check,
ROUND(COUNTIF(items.item_name IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_item_name_null_check,
ROUND(COUNTIF(items.item_brand IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_item_brand_null_check,
ROUND(COUNTIF(items.item_category IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_item_category_null_check,
ROUND(COUNTIF(items.item_category2 IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_item_category2_null_check,
ROUND(COUNTIF(items.item_list_id IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_item_list_id,
ROUND(COUNTIF(items.price IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_price_null_check,
ROUND(COUNTIF(items.quantity IS NOT NULL) * 100.0 / COUNT(*), 2) AS items_quantity_null_check
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
ROUND(COUNTIF(traffic_source.source IS NOT NULL) * 100.0 / COUNT(*), 2) AS traffic_source_source
FROM `project.analytics_123.events_*`
  UNNEST(event_params) AS events
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


