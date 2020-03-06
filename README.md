# DBT
A [dbt](https://www.getdbt.com/) demo project to reproduce bug with drop view cascade on `dbt run`.

For more details, filed a bug at https://github.com/fishtown-analytics/dbt/issues/2185

## Steps to reproduce
Terminal 1
```bash
# Start Postgres locally for dbt to use.
docker run --name postgres-dbt-test -p 5432:5432 -e POSTGRES_PASSWORD=yikes -d postgres

# (1) Initialize the views.
dbt run --profiles-dir .

# (2) The bug is here, you can open another terminal to check out the Postgres database.
# When "view_root" finishes running, it'll DROP CASCADE which drop its dependents views.
# i.e. dropping "view_middle" and "view_leaf"
dbt run --profiles-dir .
```

Terminal 2
```bash
# Get into the container running the Postgres database.
docker exec -it postgres-dbt-test bash

# Get into the Postgres database.
psql -U postgres

# Command to see the views.
# After (1) all views will exist.
# During (2) "view_root" finishes running, you will only see "view_root"
# because "view_middle" and "view_leaf" are dropped.
\dv
```