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

Technologies Used:

                        a) Amazon Kinesis: It is chosen for its ability to handle real-time data streaming at scale. Kinesis Streams collect and temporarily store the raw event data, making it available for processing.
b) AWS Lambda: Used for its serverless compute capabilities, allowing us to run code in response to Kinesis data streams without managing servers. Lambda is ideal for event data transformation because it can scale automatically with the volume of incoming data.
c) Amazon S3: Selected as the storage solution for its flexibility as a data lake repository. S3 can store large volumes of data in various formats and is easily accessible for analysis and processing with other AWS services.
d) Terraform: Terraform is used for infrastructure as code (IaC) to automate the deployment of the AWS resources needed for this solution. It ensures our infrastructure is reproducible and can be version-controlled.
e) Python 3: Python is chosen for the Lambda function creation and later for data processing tasks.
Makefile: A Makefile is used to automate the setup and testing of our environment, streamlining the development and deployment process.
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Design Questions:
                       a) Handling Duplicate Events: To handle duplicate events, we can use a combination of the event_uuid and created_at fields to create a composite key.
 This key can be used to detect duplicates either during ingestion or at the storage level. At a scripting level we can ensure that even if the same event is processed more than once, it will not result in duplicate records in the storage.

 b) Data Partitioning for Performance and Scalability: Partitioning the data is crucial for both querying performance and scalability. The data should be partitioned based on event_type and created_datetime. This approach allows efficient querying by event type and time range, which are likely the most common query patterns. 
For example using Amazon S3 as the storage layer,  partitioning can be done in the S3 key design as, s3://our-bucket/event_type=payment/created_date=2024-03-17/.

c) Storage Format: For storage format, Parquet can be used due to its columnar storage format. This format is ideal for any workloads beacause it  allows a faster querying and data retrieval. 

d) Testing the Architecture Components:
Testing can be done in multiple layers i.e.
° Unit tests for individual components. Here we ensure that each unit works correctly in isolation.
° Complete test to ensure that different components of the system (in our case: ingestion, processing, and storage) work together as expected.
° Load testing to check if the expected volume of events can be handled by the system  without performance issues.
° The data quality can be tested by the output of our data extraction script (Generally, any discripancies in data can be found out on creating a data frame using the pandas library).

e) Ensuring Replicability Across Environments:
To ensure the architecture is replicable across environments, use Infrastructure as Code (IaC) tools like Kubernates, Jenkins or Terraform for provisioning infrastructure. 
This approach ensures that the entire infrastructure setup (e.g., Kinesis streams, S3 buckets, Lambda functions) can be versioned, shared and deployed consistently across different environments (example : development, staging, production).

f) Adjusting the Solution Based on Event Volume: 
If the amount of events changes significantly (either 1000 times smaller or bigger), the fundamental architecture could remain the same, but the scaling strategies might change. For smaller volumes, we might not need as many resources or as complex a partitioning strategy. For larger volumes, we might need to scale up the resources and possibly introduce more partitions to maintain the performance.

g) Adjusting to removal of field additions/transformations:
If adding fields or transforming data is no longer needed, the architecture could be simplified by removing the processing layer 
responsible for these tasks.
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
