# Table of Contents

- [Find and kill long running queries](#find-and-kill-long-running-queries)

## Find and kill long running queries

Stolen from [here](https://medium.com/little-programming-joys/finding-and-killing-long-running-queries-on-postgres-7c4f0449e86d)

### Find

```sql
SELECT
  pid,
  now() - pg_stat_activity.query_start AS duration,
  query,
  state
FROM pg_stat_activity
WHERE 
  (now() - pg_stat_activity.query_start) > interval '5 minutes';
```

### Kill

(`__pid__` is value returned by above query)

cancel

```sql
SELECT pg_cancel_backend(__pid__);
```

terminate

```sql
SELECT pg_terminate_backend(__pid__);
```
