# AWS-Kinesis
Welcome to the respository AWS-Kinesis, where I will be creating a Data Lake with the events coming from Kinesis Stream.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
UseCase:
        We will create a data lake with the events coming from a Kinesis stream.
The stream delivers around 1M events/hour and there are 100 different types of events, all mixed together.
Events are json objects. All of them contain the common fields:
● event_uuid - unique identifier of the event
● event_name - string identifying the type of the event, it can consist of multiple parts separated by ":",
for example: "account:created", "lesson:started", “payment:order:completed”
● created_at - Unix timestamp of the event creation
The rest of the payload consists of fields related to the event type.
The saved events should have the following, additional fields:
● created_datetime - date and time from the created_at field, in the ISO 8601 format
● event_type - the first element from the event_name field
● event_subtype - the second element from the event_name field
and assume that the system will be working on the AWS cloud.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Creating a data lake with events coming from an Amazon Kinesis stream involves various steps and components. Here's a general approach to set this up with AWS services:

Setup:
        1) Initially, set up an Amazon Kinesis stream that captures and temporarily store the streaming data. 
2) Next, define the number of shards for our  stream based on the volume of data and the throughput we need.
3) We can then integrate Kinesis Data Firehose with our Kinesis stream. It helps in capturing and automatically loading the streaming data into our data lake. Configuring Kinesis Data Firehose will also help us to transform the data before loading it into the data lake. This can be done using AWS Lambda for real-time data transformation.
4) We then configure Kinesis Data Firehose to deliver the streaming data to our data lake storage. 
Amazon S3 can be used for storage purposes.
5) Next, we set up an S3 bucket which serves as the data lake storage and we now organize our data into folders based on our criteria for our use case (e.g., created_datetime, event type).
 

6) We can partition our data using storage format like Parquet or ORC if we'll be querying the data with Amazon Athena.
7)Optionally, we can use AWS Lambda for processing or transformation of the data before it gets loaded into S3. This is useful for  filtering or converting formats.
8)Once the data is in S3, use Amazon Athena or Amazon Redshift Spectrum to run queries directly against our data lake. This allows us to gain insights without the need to load our data into a separate analytics tool.
9)AWS Glue can also be used to run our ETL  jobs to prepare data for analytics or reporting.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Implementation Steps:

                    1) Create a Kinesis Stream: Create a Kinesis Stream in the AWS Management Console.
2) Set Up Kinesis Data Firehose: Create a Kinesis Data Firehose delivery stream and select our Kinesis stream as the source.
3) Configure S3 Bucket: Set up our S3 bucket and define it as the destination for your Kinesis Data Firehose delivery stream.
4) Transformations: If needed, we can use AWS Lambda to process or transform our data before it reaches S3.
5) Set up Athena or Redshift Spectrum to run queries on our data.
6) Monitoring: Use Amazon CloudWatch to monitor the performance of our Kinesis streams and Firehose delivery streams.
7) We also have to ensure that our S3 buckets and Kinesis streams are properly secured using IAM roles.
   
This general approach should get us started with building a data lake from events coming through a Kinesis stream.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
