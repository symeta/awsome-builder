# automatically generate incremental data table by step function

## description
the existing data batch processing could be achieved by bulkly executing sql query at one time, e.g. CTAS. the incremental data usually dealt with on daily basis (T + 1) should be automatically driven by an ETL process. aws Step Function offers the capability of orchestrating the data processing steps. the processing steps will be executed automatically to achieve the data warehouse incremental tiering (e.g. DWD, DWS, ADS tiering). 

this blog describes a prototype of using step function to orchestrate the data processing steps. Initially, we assume that the incremental data has already been uploaded into s3 bucket. a glue data crawler will craw the s3 bucket data into glue data catalog. this processs will be trigerred by a lambda function. another lambda function will monitor the status of the crawler. the step function won't move on until the state of crawler is READY. an athena query will be triggered to execute the logic of generating the incremental data table. the process comes to an end by successfully have the incremental data table generated.

## solution diagram
![image](https://user-images.githubusercontent.com/97269758/151703845-c66e9052-5dd4-4afd-9098-e6b8782efbb8.png)

## code
lambda function 1 (the one to start crawler)
```
import json
import boto3

def lambda_handler(event, context):
    target = event["crawlername"]
    glueclient = boto3.client('glue')
    glueclient.start_crawler(Name=target)
```
lambda function 2 (the one to get crawler state)
```
import json
import boto3

def lambda_handler(event, context):
    target = event["crawlername"]
    glueclient = boto3.client('glue')
    response = glueclient.get_crawler(Name=target)
    return {
        'state': response['Crawler']['State']
    }
```
athena sql query sample
```
{
  "QueryString": "create table \"ab23-incremental\".\"dws_activeuser\" as select distinct uid,action,ar,ba,detail,en,entry,extend1,g,hw,l,la,ln,loading_time,md,mid,nw,open_ad_type from \"ab23-incremental\".\"incremental_ab23_incremental\"",
  "WorkGroup": "primary",
  "ResultConfiguration": {
    "OutputLocation": "s3://ab23-incremental-athena/"
  }
```
*pay attention to \", which is a special keyword.*
