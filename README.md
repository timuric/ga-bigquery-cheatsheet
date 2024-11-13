# Bigquery cheat sheet
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


