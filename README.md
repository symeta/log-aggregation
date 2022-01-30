# log-aggregation

quite a number of customers have their critical systems deployed on-premise, especially for those who are sensitive towards data security.
however, logs collected from those on-prem systems with large volumes and values dispered between countless records are usually less sensitive, and when it comes to cross geo-region aggregation scenario, it is much more efficient and cost-effective to store those logs on cloud to achieve the aggregated anaylsis, as cloud provides a cross region integrated platform with high scalability and durability.

this doc describes a log aggregation solution leveraging on aws cloud services. logs are initially generated by different geo-regions. Afterwards, these logs will be collected to the nearest aws regions, and then synced to a central region for further aggregation analysis. the processing steps are as follows.

## 1. on-prem logs transfered to nearest aws region

### 1.1 first, a client agent needs to be installed on customers on-prem enviroment. the agent is named as Kinesis Agent
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
### 1.2 Kinesis Data Streams + Kinesis Data Firhose pipeline
the log data will be transferred through the KDS + KDF pipepline and finally stored in s3 bucket.
Please refer to the below link for further setup guidance:
   
   https://docs.aws.amazon.com/firehose/latest/dev/writing-with-kinesis-streams.html
    
    
## 2. logs ingested and stored in different aws regions aggregation
   
   the aggregation of logs stored in different regions are achieved leveraging on aws s3 cross region replication feature.
   please refer to the below link for further guidance:
   
   https://docs.aws.amazon.com/zh_cn/AmazonS3/latest/userguide/replication-walkthrough1.html

## 3. analyze those logs through aws opensearch
   
   the logs stored in s3 will be ingested into opensearch service domain for further analysis.
   please refer to the lambda function explanation as the below link:
   
   https://docs.aws.amazon.com/opensearch-service/latest/developerguide/integrations.html#integrations-s3-lambda
   
   and the docs link below for detail packaging guidance:
   
   https://docs.aws.amazon.com/lambda/latest/dg/python-package.html#python-package-create-package-no-dependency
   
   the lambda function needs to be authenticated by opensearch cluster. the authentication role mapping guidance is as below:
   
   https://docs.aws.amazon.com/opensearch-service/latest/developerguide/fgac.html#fgac-mapping
   
   
the solution diagram is shown as below:

assume that customer has on-prem system deployments at Singapore and Australia. Singapore will be the central hub for all logs. 
![Screen Shot 2022-01-07 at 12 48 17 PM](https://user-images.githubusercontent.com/97269758/148493379-3d8a64f6-8ca8-4d66-9662-33701b725a28.png)
