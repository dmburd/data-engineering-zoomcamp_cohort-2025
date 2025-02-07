# Module 3 Homework: BigQuery

Shell commands or SQL queries necessary for answering the questions.


## Question 1. What is count of records for the 2024 Yellow Taxi Data?

```sql
SELECT COUNT(*) AS num_records
FROM `taxi-rides-ny.nytaxi.external_yellow_tripdata_2024`;
```

| num_records |
|------|
| 20332093 |


## Question 2. Count the distinct number of PULocationIDs on both tables.

```sql
SELECT COUNT(DISTINCT PULocationID)
FROM `taxi-rides-ny.nytaxi.external_yellow_tripdata_2024`;
-- ^ This query will process 0 B when run
```

```sql
SELECT COUNT(DISTINCT PULocationID)
FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024`;
-- ^ This query will process 155.12 MB when run
```


## Question 3. Selecting one column vs selecting two columns.

```sql
SELECT PULocationID
FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024`;
-- ^ This query will process 155.12 MB when run
```

```sql
SELECT PULocationID, DOLocationID
FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024`;
-- ^ This query will process 310.24 MB when run
```


## Question 4. How many records have a fare_amount of 0?

```sql
SELECT COUNT(*) AS num_records
FROM `taxi-rides-ny.nytaxi.external_yellow_tripdata_2024`
WHERE fare_amount = 0;
```

| num_records |
|------|
| 8333 |


## Question 5. The best strategy to make an optimized table.

```sql
CREATE OR REPLACE TABLE `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024_partitioned_clustered`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS
SELECT * FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024`;
-- ^ This query will process 2.72 GB when run
```


## Question 6. Retrieve the distinct VendorIDs for the specified dropoff datetime range.

```sql
SELECT DISTINCT VendorID
FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024`
WHERE tpep_dropoff_datetime BETWEEN '2024-03-01' AND '2024-03-15';
-- ^ This query will process 310.24 MB when run
```

```sql
SELECT DISTINCT VendorID
FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024_partitioned_clustered`
WHERE tpep_dropoff_datetime BETWEEN '2024-03-01' AND '2024-03-15';
-- ^ This query will process 26.84 MB when run
```

<!-- Question 7. Where is the data stored in the External Table you created? -->
<!-- Answer: GCP Bucket. -->


<!-- Question 8. It is best practice in Big Query to always cluster your data. -->
<!-- Answer: False. -->


## Question 9. SELECT COUNT(*) FROM the partitioned and clustered table.
```sql
SELECT COUNT(*)
FROM `taxi-rides-ny.nytaxi.native_yellow_tripdata_2024_partitioned_clustered`;
-- ^ This query will process 0 B when run.
-- ^ That's because BigQuery uses known table metadata for COUNT(*) on partitioned tables.
```

