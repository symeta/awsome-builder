# sagemaker gets data from athena

## code
```py
import boto3

client = boto3.client('athena')

response = client.start_query_execution(
    QueryString="select * from \"ab23-db\".\"dws_user_behavior\"",
    QueryExecutionContext={
        'Database': "ab23-db"
    },
    ResultConfiguration={
        'OutputLocation': 's3://ab23-athena-output'
    }
)
```
