# Module 1 Homework: Docker & SQL

Shell or SQL commands necessary for answering the questions.


## Question 1. Understanding docker first run

Commands:

```bash
docker run --rm -it --entrypoint=bash python:3.12.8
# inside the container:
pip --version
```

Output:

```plaintext
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)
```


## Question 2. Understanding Docker networking and docker-compose

Commands:

```bash
# the given 'docker-compose.yaml' is used
docker compose up -d
```

Output:

```plaintext
[+] Running 2/2
 ✔ Container pgadmin   Started      0.4s 
 ✔ Container postgres  Started      0.4s 
```

Command:

```bash
pgcli -h localhost -p 5433 -u postgres --no-password -d ny_taxi
```

Output:

```plaintext
Server: PostgreSQL 17.2
Version: 4.1.0
Home: http://pgcli.com
postgres@localhost:ny_taxi>
```

## Question 3. Trip Segmentation Count

Query:
```sql
SELECT
    SUM(
        CASE
            WHEN trip_distance <= 1.0 THEN 1
            ELSE 0
        END
    ) AS from_0_to_1_mile,
    SUM(
        CASE
            WHEN trip_distance > 1.0 AND trip_distance <= 3.0 THEN 1
            ELSE 0
        END
    ) AS from_1_to_3_miles,
    SUM(
        CASE
            WHEN trip_distance > 3.0 AND trip_distance <= 7.0 THEN 1
            ELSE 0
        END
    ) AS from_3_to_7_miles,
    SUM(
        CASE
            WHEN trip_distance > 7.0 AND trip_distance <= 10.0 THEN 1
            ELSE 0
        END
    ) AS from_7_to_10_miles,
    SUM(
        CASE
            WHEN trip_distance > 10.0 THEN 1
            ELSE 0
        END
    ) AS from_10_to_inf_miles
FROM green_tripdata_2019_10
WHERE lpep_pickup_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime < '2019-11-01';
```

Data output:

```csv
| from_0_to_1_mile | from_1_to_3_miles | from_3_to_7_miles | from_7_to_10_miles | from_10_to_inf_miles |
|------------------|-------------------|-------------------|--------------------|----------------------|
| 104802           | 198924            | 109603            | 27678              | 35189                |
```


## Question 4. Longest trip for each day

Query:

```sql
SELECT DATE(lpep_pickup_datetime)
FROM green_tripdata_2019_10
WHERE trip_distance = (
    SELECT MAX(trip_distance)
    FROM green_tripdata_2019_10
);
```

Data output:

```csv
| date |
|------|
| 2019-10-31 |
```


## Question 5. Three biggest pickup zones

Query:

```sql
WITH biggest_pickup_loc_ids AS (
    SELECT "PULocationID"
    FROM green_tripdata_2019_10
    WHERE DATE(lpep_pickup_datetime) = '2019-10-18'
    GROUP BY "PULocationID"
    HAVING SUM(total_amount) > 13000.0
)
SELECT "Zone"
FROM taxi_zone_lookup
WHERE "LocationID" IN (SELECT * FROM biggest_pickup_loc_ids);
```

Data output:

```csv
| Zone |
|------|
| East Harlem North |
| East Harlem South |
| Morningside Heights |
```

## Question 6. Largest tip

Query:

```sql
SELECT
    zn_do."Zone"
FROM
    green_tripdata_2019_10 AS tr
    INNER JOIN taxi_zone_lookup AS zn_pu
        ON tr."PULocationID" = zn_pu."LocationID"
    INNER JOIN taxi_zone_lookup AS zn_do
        ON tr."DOLocationID" = zn_do."LocationID"
WHERE DATE_TRUNC('MONTH', tr.lpep_pickup_datetime) = '2019-10-01'
  AND zn_pu."Zone" = 'East Harlem North'
ORDER BY tr.tip_amount DESC
LIMIT 1;
```

Data output:

```csv
| Zone |
|------|
| JFK Airport |
```

