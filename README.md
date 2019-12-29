# SnowFlakeDBUtils

# SnowFlake Documenation :
   https://docs.snowflake.net/manuals/user-guide.html
   
# SnowFlake UI Url :
   https://via74631.us-east-1.snowflakecomputing.com/console#/internal/worksheet

# SnoFlake sql scripts
  ```  
   create database snowflake_db;
   
   

``` 

# Data Loading :
   ```
    1) WebUI  
	2) SnowSQL (SQL)
	3) SnowPipe
	4) Third Party Tools ETL 
Structed :
   CSV ,Delimited Files 
Semi Structed :
  JSON,Avro,
  Parquet,ORC 
  
Phase 1:
  Loading to (user --> Any Cloud Storage(S3)
Phase 2:
 Loading the data 


CREATE DATABASE MULTI_COLUMN_DB;
CREATE DATABASE SINGLE_VARIANT_DB;

CREATE SCHEMA "MULTI_COLUMN_DB".POC;
CREATE SCHEMA "SINGLE_VARIANT_DB".POC;

CREATE TABLE "MULTI_COLUMN_DB"."POC".DEVICEINFO (
id                       string,
manufacturer             string,
serialNumber             string,
etimestamp               timestamp,
model                    string,
accuracy                int,
batch_id                 bigint,
event_date               date)
cluster by (event_date);

CREATE TABLE “SINGLE_VARIANT_DB”.”POC”.DEVICEINFO_VAR (V VARIANT);

CREATE "SINGLE_VARIANT_DB"."POC”.VIEW DEVICEINFO as
SELECT
$1:_col0::string as id,
$1:_col1::string as manufacturer,
$1:_col2::string as serialNumber ,
$1:_col3::timestamp as etimestamp ,
$1:_col4::string as model ,
$1:_col5::number as accuraccy,
$1:_col6::number as batch_id ,
$1:_col4::date as event_date,
FROM DEVICEINFO_VAR;



CREATE STAGE MULTI_COLUMN_DB"."POC".ORC_SNOWFLAKE
URL = 's3://ntgr-snowflake'
CREDENTIALS = (AWS_KEY_ID = 'xxxxxx' AWS_SECRET_KEY = '******');
 

```

	
https://medium.com/hashmapinc/snowflakes-cloud-data-warehouse-what-i-learned-and-why-i-m-rethinking-the-data-warehouse-75a5daad271c


https://github.com/snowflakedb/snowflake-sqlalchemy
	

https://www.youtube.com/watch?v=QMeYjd2A0Nc



# Snowflake Integration with Databricks:
https://databricks.com/blog/2018/08/27/by-customer-demand-databricks-and-snowflake-integration.html
https://docs.databricks.com/data/data-sources/snowflake.html
https://docs.databricks.com/_static/notebooks/snowflake-scala.html

