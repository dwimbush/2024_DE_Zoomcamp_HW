# 2024_Data_Engineering_Zoomcamp_Homework
Repository for 2024 Data Engineering Zoomcamp Homework

## Week 3 Data Warehouse

### Question 1: What is count of records for the 2022 Green Taxi Data?
```SQL
--Count of records in external table
SELECT count(1) FROM terraformdemo-412523.ny_taxi.external_green_2022_tripdata 
```
### Question 2: What is the estimated amount of data in the tables?
```SQL
--Count of pulocation_id from external table
SELECT sum(distinct(pulocation_id)) AS pulocation_id_sum
FROM terraformdemo-412523.ny_taxi.external_green_2022_tripdata;

--Count of pulocation_id from materialized non-partitioned table
SELECT sum(distinct(pulocation_id)) AS pulocation_id_sum
FROM terraformdemo-412523.ny_taxi.green_tripdata_non_partitioned;
```

### Question 3:  How many records have a fare_amount of 0?
```SQL
--Count of records where fare_amount equal to zero
SELECT count(fare_amount) AS fare_amount_count
FROM  terraformdemo-412523.ny_taxi.external_green_2022_tripdata
WHERE fare_amount = 0;
```

### Question 4: What is the best strategy to make an optimized table in Big Query?
<p style="margin-left: 40px;">
Partitioning by PUlocationID: Not typically recommended because IDs usually have high cardinality, which can result in a large number of small partitions, leading to inefficient query performance and potential quota issues within BigQuery.

<p style="margin-left: 40px;">
Clustering by lpep_pickup_datetime: While clustering can improve performance for queries that filter on date, it's less effective than partitioning by date due to potentially higher data scan volumes, increasing query costs and time.
</p>

### Question 5: What's the size of the tables?
```SQL
--Count of Distinct pickup locations between a specific range with Materialized table
SELECT DISTINCT(pulocation_id) 
FROM `terraformdemo-412523.ny_taxi.external_green_2022_tripdata_DateFixed`
WHERE DATE(lpep_pickup_datetime) BETWEEN '2022-06-01' AND '2022-06-30';

--Count of Distinct pickup locations between a specific range with Partitioned and Clustered table
SELECT DISTINCT(pulocation_id) 
FROM `terraformdemo-412523.ny_taxi.green_2022_tripdata_partitioned`
WHERE lpep_pickup_datetime BETWEEN '2022-06-01' AND '2022-06-30';
```

### Question 6: Where is the data stored in the External Table you created?
<p style="margin-left: 40px;">
Google Cloud Storage is one of the most common use cases for BigQuery external tables. Files such as CSV, JSON, Avro, Parquet, and ORC files can be stored and referenced there.
</p>

### Question 7:  It is best practice in Big Query to always cluster your data
<p style="margin-left: 40px;">
The performance benefits of clustering are minimal for small datasets, as BigQuery can efficiently manage these without additional data organization.

<p style="margin-left: 40px;">
Clustering offers limited advantages when queries uniformly scan entire datasets or target high-cardinality columns that undergo frequent updates, potentially leading to suboptimal data organization.

<p style="margin-left: 40px;">
So no, it's not always best practice to cluster data in BigQuery
</p>




