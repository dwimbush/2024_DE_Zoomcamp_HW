# 2024_Data_Engineering_Zoomcamp_Homework
Repository for 2024 Data Engineering Zoomcamp Homework

## Week 2 Workflow Orchestration

### Question 1: Data Loading
```python
import io
import pandas as pd
import requests
if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader
if 'test' not in globals():
    from mage_ai.data_preparation.decorators import test

@data_loader
def load_data_from_api(*args, **kwargs):
    """
    Template for loading data from API
    """
    # url = 'https://github.com/DataTalksClub/nyc-tlc-data/releases/tag/green/download'
    base_url = 'https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2020-{}.csv.gz'

    taxi_dtypes = {
        'vendorID' : pd.Int64Dtype(),
        'passenger_count' : pd.Int64Dtype(),
        'trip_distance' : float,
        'RatecodeID' : pd.Int64Dtype(),
        'store_and_fwd_flag': str,
        'PULocationID' : pd.Int64Dtype(),
        'DOLocationID' : pd.Int64Dtype(),
        'payment_type' : pd.Int64Dtype(),
        'fare_amount' : float,
        'extra': float,
        'mta_tax' : float,
        'tip_amount ': float,
        'tolls_amount' : float,
        'improvement_surcharge' : float,
        'total_amount': float,
        'congestion_surcharge': float
    }

    parse_dates = ['lpep_pickup_datetime', 'lpep_dropoff_datetime']
    
    # Initialize an empty list to store dataframes
    dfs = []

    # Loop through the final quarter months
    for month in ['10', '11', '12']:
   
        # Construct the file URL for each month
        file_url = base_url.format(month)
        # Read the CSV file from the constructed URL
        df = pd.read_csv(file_url, sep=',', compression='gzip', dtype=taxi_dtypes, parse_dates=parse_dates)
        # Append the dataframe to the list
        dfs.append(df)

    # Concatenate all dataframes in the list
    final_df = pd.concat(dfs, ignore_index=True)

    return final_df
        
@test
def test_output(output, *args) -> None:
    """
    Template code for testing the output of the block.
    """
    assert output is not None, 'The output is undefined'
```

### Question 2: Data Transformation
```python
@transformer
def transform(data, *args, **kwargs):
    # Print the number of rows with zero passengers
    print("Rows with zero passengers:", data['passenger_count'].isin([0]).sum())

    # Print the number of rows with zero trip distance
    print("Rows with zero trip distance:", data['trip_distance'].isin([0]).sum())
```

### Question 3: Data Transformation
```python
valid_vendor_ids = [1, 2]  # Example valid vendor IDs, adjust this as necessary
    assert all(output['vendor_id'].isin(valid_vendor_ids)), 'Invalid vendor_id detected'
```

### Question 4: Data Transformation
```python
@test
def test_output(output, *args):
    
    # First, capture and print the unique vendor_ids for informational purposes
    unique_vendor_ids = output['vendor_id'].unique()
    print(f"Unique vendor_id values in the output: {unique_vendor_ids}")
    
    # Use an assertion to ensure this statement is always true; this is a bit unconventional
    # since assertions are typically used for actual tests, not information display.
    # This assertion will always pass unless there's an unexpected modification to the 'vendor_id' values.
    assert set(unique_vendor_ids).issubset(set([1, 2, 3, 4])), "Detected unexpected vendor_id values."
```

### Question 5: Data Transformation
```python
import io
import pandas as pd
import requests
if 'data_loader' not in globals():
    from mage_ai.data_preparation.decorators import data_loader
if 'test' not in globals():
    from mage_ai.data_preparation.decorators import test

@data_loader
def load_data_from_api(*args, **kwargs):
    """
    Template for loading data from API
    """
    # url = 'https://github.com/DataTalksClub/nyc-tlc-data/releases/tag/green/download'
    base_url = 'https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2020-{}.csv.gz'

    taxi_dtypes = {
        'vendorID' : pd.Int64Dtype(),
        'passenger_count' : pd.Int64Dtype(),
        'trip_distance' : float,
        'RatecodeID' : pd.Int64Dtype(),
        'store_and_fwd_flag': str,
        'PULocationID' : pd.Int64Dtype(),
        'DOLocationID' : pd.Int64Dtype(),
        'payment_type' : pd.Int64Dtype(),
        'fare_amount' : float,
        'extra': float,
        'mta_tax' : float,
        'tip_amount ': float,
        'tolls_amount' : float,
        'improvement_surcharge' : float,
        'total_amount': float,
        'congestion_surcharge': float
    }

    parse_dates = ['lpep_pickup_datetime', 'lpep_dropoff_datetime']
    
    # Initialize an empty list to store dataframes
    dfs = []

    # Loop through the final quarter months
    for month in ['10', '11', '12']:
   
        # Construct the file URL for each month
        file_url = base_url.format(month)
        # Read the CSV file from the constructed URL
        df = pd.read_csv(file_url, sep=',', compression='gzip', dtype=taxi_dtypes, parse_dates=parse_dates)
        # Append the dataframe to the list
        dfs.append(df)

    # Concatenate all dataframes in the list
    final_df = pd.concat(dfs, ignore_index=True)

    return final_df
        
@test
def test_output(output, *args) -> None:
    """
    Template code for testing the output of the block.
    """
    assert output is not None, 'The output is undefined'

```

### Question 6: Data Exporting
```python
import pyarrow as pa
import pyarrow.parquet as pq
import os

if 'data_exporter' not in globals():
    from mage_ai.data_preparation.decorators import data_exporter

os.environ['GOOGLE_APPLICATION_CREDENTAILS'] = '/home/src/terraformdemo-412523-1583c7be6c8c.json'

bucket_name = 'mage-zoomcamp-dw'
project_id = 'terraformdemo-412523'

table_name = "nyc_green_taxi_data "

root_path = f'{bucket_name}/{table_name}'

@data_exporter
def export_data(data, *args, **kwargs):
    
    table = pa.Table.from_pandas(data)

    gcs = pa.fs.GcsFileSystem()

    pq.write_to_dataset(
        table,
        root_path=root_path,
        partition_cols=['lpep_pickup_date'],
        filesystem=gcs
    )
```

