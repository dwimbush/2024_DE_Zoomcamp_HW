# 2024_Data_Engineering_Zoomcamp_Homework
Repository for 2024 Data Engineering Zoomcamp Homework

## Week 4 Analytics Engineering

### Question 1: What happens when we execute dbt build --vars '{'is_test_run':'true'}'
```bash
dbt build --vars '{'is_test_run':'true'}'
```
It applies a limit 100 to all of our models

### Question 2: What is the code that our CI job will run? Where is this code coming from?
The code that is behind the object on the dbt_cloud_pr_ schema

### Question 3:  What is the count of records in the model fact_fhv_trips after running all dependencies with the test run variable disabled (:false)?
```bash
dbt build --select+fact_trips+ --vars'{'is_test_run':'false'}'
```

### Question 4: What is the service that had the most rides during the month of July 2019 month with the biggest amount of rides after building a tile for the fact_fhv_trips table?







