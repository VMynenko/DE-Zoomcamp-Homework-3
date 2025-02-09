# Data Warehouse and BigQuery

## Homework Assignment Summary

**Task**: Download Yellow Taxi Trip Records (Jan–Jun 2024 Parquet files), upload to GCS bucket, and create tables in BigQuery.  
To download files from [NYC TLC Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page), I used the script [load_yellow_taxi_data.py](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/cohorts/2025/03-data-warehouse/load_yellow_taxi_data.py)

## Quiz Questions

__1. What is count of records for the 2024 Yellow Taxi Data?__
```sql
SELECT COUNT(1) 
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`
```
- `20,332,093`

__2. Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?__
```sql
SELECT COUNT(DISTINCT PULocationID) 
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`;

SELECT COUNT(DISTINCT PULocationID) 
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024_ext`
```
- `0 MB for the External Table and 155.12 MB for the Materialized Table`

__3. Write a query to retrieve the PULocationID from the table (not the external table) in BigQuery. Now write a query to retrieve the PULocationID and DOLocationID on the same table. Why are the estimated number of Bytes different?__
```sql
SELECT PULocationID
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`;

SELECT PULocationID, DOLocationID
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`
```
- `BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.`

__4. How many records have a fare_amount of 0?__
```sql
SELECT COUNT(1)
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`
WHERE fare_amount = 0
```
- `8,333`

__5. What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy)__
```sql
CREATE TABLE `de_zoomcamp_hw_3.yellow_tripdata_2024_optimized`
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY VendorID AS
SELECT * FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`
```
- `Partition by tpep_dropoff_datetime and Cluster on VendorID`

__6. Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime 2024-03-01 and 2024-03-15 (inclusive)  
Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 5 and note the estimated bytes processed. What are these values?  
Choose the answer which most closely matches.__
```sql
SELECT DISTINCT VendorID
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024`
WHERE tpep_dropoff_datetime BETWEEN '2024-03-01' AND '2024-03-15';

SELECT DISTINCT VendorID
FROM `de_zoomcamp_hw_3.yellow_tripdata_2024_optimized`
WHERE tpep_dropoff_datetime BETWEEN '2024-03-01' AND '2024-03-15'
```
- `310.24 MB for non-partitioned table and 26.84 MB for the partitioned table`

__7. Where is the data stored in the External Table you created?__
- `GCP Bucket`

__8. It is best practice in Big Query to always cluster your data?__
- `False`
