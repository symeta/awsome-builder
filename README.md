# data uploading from on-prem to aws region

## description
### first, a client agent needs to be installed on customers on-prem enviroment. the agent is named as Kinesis Agent
Please refer to the below link for further installation guidance:
   
https://docs.aws.amazon.com/streams/latest/dev/writing-with-agents.html
   
a sample of agent.json:　
```
   {
      "kinesis.endpoint": "kinesis.ap-southeast-1.amazonaws.com", 
      "flows": [
                 {
                   "filePattern": "/tmp/app.log*",
                   "kinesisStream": "jan5-kds-sg"
                 }
       ]
    }
```

### second, build a Kinesis Data Streams + Kinesis Data Firhose pipeline to store data in s3 
the log data will be transferred through the KDS + KDF pipepline and finally stored in s3 bucket.
Please refer to the below link for further setup guidance:
   
https://docs.aws.amazon.com/firehose/latest/dev/writing-with-kinesis-streams.html

the solution diagram is shown as below:
![image](https://user-images.githubusercontent.com/97269758/151689784-262ba700-cfbc-4399-ad6a-5bd93514a6a9.png)

