# 2024_Data_Engineering_Zoomcamp_Homework
Repository for 2024 Data Engineering Zoomcamp Homework

## Week 1 Code Responses

### Question 1: Knowing docker tags
```bash
docker run --help
```

### Question 2: Understanding docker first run
```bash
docker run -it python:3.9 bash
```

### Question 3: Count Records
```sql
SELECT count(1)
FROM green_taxi_trips gtt 
WHERE lpep_pickup_datetime::DATE = '2019-09-18' AND lpep_dropoff_datetime::DATE = '2019-09-18';
```

### Question 4: Largest Trip for Each Day
```sql
SELECT lpep_pickup_datetime::Date, max(trip_distance)
FROM green_taxi_trips gtt
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```

### Question 5: Three Biggest Pick Up Boroughs
```sql
SELECT 
    zl."Borough",
    SUM(gtt.total_amount) AS total_amount_sum
FROM 
    green_taxi_trips gtt
JOIN 
    zone_lookup zl ON gtt."PULocationID" = zl."LocationID"
WHERE 
    zl."Borough" != 'Unknown'
    AND gtt."lpep_pickup_datetime"::date = '2019-09-18'
GROUP BY 
    zl."Borough"
HAVING 
    SUM(gtt.total_amount) > 5000
ORDER BY 
    total_amount_sum DESC
LIMIT 3;
```

### Question 6: Largest Tip
```sql
SELECT 
    dropoff_zone."Zone" AS dropoff_zone_name,
    gtt.tip_amount
FROM 
    green_taxi_trips gtt
JOIN 
    zone_lookup pickup_zone ON gtt."PULocationID" = pickup_zone."LocationID"
JOIN 
    zone_lookup dropoff_zone ON gtt."DOLocationID" = dropoff_zone."LocationID"
WHERE 
    pickup_zone."Zone" = 'Astoria'
    AND gtt."lpep_pickup_datetime" >= '2019-09-01'
    AND gtt."lpep_pickup_datetime" < '2019-10-01'
ORDER BY 
    gtt.tip_amount DESC
LIMIT 1;
```

