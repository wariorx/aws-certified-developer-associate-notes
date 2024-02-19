# Athena
* Serverless interactive analytics service built on open-source frameworks
* Supports Open-table and file formats
* Simplified way to analyze petabytes of data where it lives;
* Query using SQL or Python
* 30 available data sources; including S3

## Elements
* **Data source / Catalog**: group of data bases AKA
* **Database / Schema**: group of tables
* **Table**: data organized in rows and columns

## Usage
* Run queries on S3
* Prepare data for ML models; simplify complex tasks such as animly detection, sales predictions
* Big data reconciliation
* Multicloud analytics

## Working with Athena
1. Configure the default workgroup or create a new one; select an s3 bucket key to output results to
2. Create a database 
```CREATE DATABASE asd```
3. Create a table in the database to map data. For example, the following table parses cloudfront logs into data
> 2014-07-05 20:00:09 DFW3 4260 10.0.0.15 GET eabcd12345678.cloudfront.net /test-image-1.jpeg 200 - Mozilla/5.0[Android;%20U;%20Windows%20NT%205.1;%20en-US;%20rv:1.9.0.9)%20Gecko/2009040821%20IE/3.0.9]

``` 
CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
  `Date` DATE,
  Time STRING,
  Location STRING,
  Bytes INT,
  RequestIP STRING,
  Method STRING,
  Host STRING,
  Uri STRING,
  Status INT,
  Referrer STRING,
  ClientInfo STRING
  ) 
  ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '\t'
  LINES TERMINATED BY '\n'
  LOCATION 's3://athena-examples-my-region/cloudfront/plaintext/'; 
```

if You have nested information (IE user agent in datapoint above) you can use Regex SerDe

``` 
CREATE EXTERNAL TABLE IF NOT EXISTS cloudfront_logs (
  `Date` DATE,
  Time STRING,
  Location STRING,
  Bytes INT,
  RequestIP STRING,
  Method STRING,
  Host STRING,
  Uri STRING,
  Status INT,
  Referrer STRING,
  os STRING,
  Browser STRING,
  BrowserVersion STRING
  ) 
  ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
  WITH SERDEPROPERTIES (
  "input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]([^\;]+).*\%20([^\/]+)[\/](.*)$"
  ) LOCATION 's3://athena-examples-myregion/cloudfront/plaintext/';
```

## Saving Queries
* Queries can be saved and named for later re-use
* Queries alway run under the current user's permissions

