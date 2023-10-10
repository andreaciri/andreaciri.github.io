+++
title = "Checkup Your Postgres Database"
date = "2023-09-09"
author = "Andrea Cirigliano"
cover = "img/pg-checkup.png"
description = "A collection of utility queries to checkup your Postgres database."
+++


## Cache Hit Ratio
```sql
SELECT
  'index hit rate (memory vs disk)' AS name,
  (sum(idx_blks_hit)) / nullif(sum(idx_blks_hit + idx_blks_read),0) AS ratio
FROM pg_statio_user_indexes
UNION ALL
SELECT
 'table hit rate (memory vs disk)' AS name,
  sum(heap_blks_hit) / nullif(sum(heap_blks_hit) + sum(heap_blks_read),0) AS ratio
FROM pg_statio_user_tables;
```

It gives the percentage of time your data is served from memory vs having to read the disk. Serving data from memory is usually orders of magnitude faster, so the more you can serve from memory the better.
For user facing services you should target > 99%.

If you want to increase the hit ratio one possible solution is to upgrade the memory of your database.


## Index Coverage
```sql
SELECT relname,
   CASE idx_scan
     WHEN 0 THEN 'No data for this table'
     ELSE (100 * idx_scan / (seq_scan + idx_scan))::text
   END percent_of_times_index_used,
   n_live_tup rows_in_table
 FROM
   pg_stat_user_tables
 ORDER BY
   n_live_tup DESC;
```

this query shows, for each table, how much indexes are used compared to scanning the entire table. The tables are sorted from the highest number of rows, so that we can focus on potential slow scans. A percentage lower than 90% can be the evidence that we are missing some useful index or we created the index on the wrong columns.


## Unused Indexes
```sql
SELECT
  schemaname || '.' || relname AS table,
  indexrelname AS index,
  pg_size_pretty(pg_relation_size(i.indexrelid)) AS index_size,
  idx_scan as index_scans
FROM pg_stat_user_indexes ui
JOIN pg_index i ON ui.indexrelid = i.indexrelid
-- less then 50 scans, index size greater than 5 MB
WHERE NOT indisunique AND idx_scan < 50 AND pg_relation_size(i.indexrelid) > 5242880
ORDER BY pg_relation_size(i.indexrelid) / nullif(idx_scan, 0) DESC NULLS FIRST,
pg_relation_size(i.indexrelid) DESC;
```

since your application is constanlty evolving, your database may have something that is now unnecessary. The first thing to look for are unused indexes. In Postgres this means you're getting slower writes with really no benefit from the presence of the index.


## Function Performance
```sql
SELECT
       schemaname,
       funcname,
       calls,
       date_trunc('milliseconds', round(total_time) / 1000 * '1 second'::interval) AS total_time,
       date_trunc('milliseconds', round(self_time) / 1000 * '1 second'::interval) AS self_time,
       trunc((round(total_time) / calls)::numeric, 2) AS average_time_ms,
       (self_time / total_time * 100)::numeric(20,2) AS self_time_ratio
FROM pg_stat_user_functions
ORDER BY calls DESC;
```

this query returns some insights about functions, like the number of total calls and the time spent.
`total_time` includes the time spent in other functions called by it. `self_time` instead does not include other functions called by it.


## Query Performance
```sql
SELECT
  date_trunc('milliseconds', round(total_time) / 1000 * '1 second'::interval) AS total_time,
  trunc((round(total_time) / calls)::numeric, 2) AS average_time_ms,
  calls,
  query
FROM pg_stat_statements
ORDER BY calls DESC
LIMIT 50;
```
It records queries that are run in your database and a number of stats about them. It is useful to look up what queries are slowest on average as well as ones that consume the most total time.


## Long Running
```sql
SELECT
  pid,
  usename,
  now() - pg_stat_activity.query_start AS duration,
  query,
  state
FROM pg_stat_activity
WHERE (now() - pg_stat_activity.query_start) > interval '2 minutes'
ORDER BY duration desc;
```

This query returns the records of `pg_stat_activity`, which is a view of what's happening *right now* on your database. The query filter the results per duration, showing long running processes.

## Replication Slots Size
```sql
WITH slots AS (SELECT slot_name,
            slot_type,
            coalesce(restart_lsn, '0/0'::pg_lsn) AS slot_lsn,
            coalesce(pg_xlog_location_diff(pg_current_xlog_location(), restart_lsn),0) AS delta,
            active
        FROM pg_replication_slots)
        SELECT *, pg_size_pretty(delta) AS delta_pretty FROM slots;
```

This query returns the total size of WAL files for each replication slot. This can be useful to check how many WALs are accumulating on the master when a replica is inactive.

## Estimate Row Count
```sql
SELECT reltuples AS estimate FROM pg_class WHERE relname = 'table_name';
```

If you have a very large table the usual `count(*)` can be extremely slow because it performs a full scan. If you only need an estimate of the count you can use this query.
