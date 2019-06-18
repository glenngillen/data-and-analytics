# Build It Days: Data & Analytics

A list of common areas people want to focus on when improving their current data, analytics, event pipelines, and related solutions.

# Wanting to implement some user analytics (either web or mobile)?

* Amazon Pinpoint is the easiest way to get something valuable deployed https://aws.amazon.com/pinpoint/features/analytics/usage-analytics/
* It does a lot of heavy lifting out of the box (e.g., https://aws.amazon.com/blogs/messaging-and-targeting/amazon-pinpoints-analytics-suite-now-includes-event-metric-visualizations/). If you ever want to augment that with some custom solutions (internal reporting, custom ML, etc.) you can stream events into Kinesis and from there follow whatever your preferred data processing pipeline options are https://docs.aws.amazon.com/pinpoint/latest/developerguide/analytics-streaming.html

# Wanting to centralize your logs 

* Here's a common approach: https://aws.amazon.com/solutions/centralized-logging/
* Once the logs are in CloudWatch you can start saving them to S3 (your first data lake! ;) https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Sending-Logs-Directly-To-S3.html
* Here's how to query them once they're there: https://aws.amazon.com/blogs/big-data/analyzing-data-in-s3-using-amazon-athena/G

# Wanting to build your first data lake?

## Getting Data into S3A

Getting data into S3 in a structured format is, in my experience, the bulk of the effort in getting an MVP data lake implemented. The options are:

### Kinesis Firehose

* If you can get it into Kinesis Firehose this is easiest option, as it will take care of batching and saving to S3 for you. For the sake of getting something working quickly I typically use the Kinesis Data Generator (https://awslabs.github.io/amazon-kinesis-data-generator/web/help.html) to stream in something "production-like". It means I can focus on building out the query and visualisation stages, and come back to connect/stream actual production data as the final step. 
* I've had success in the past with providing a custom prefix for the delivery stream, something like `year=!{timestamp:YYYY}/month=!{timestamp:MM}/day=!{timestamp:dd}/hour=!{timestamp:HH}/`, so that data is saved in the right format later for partitioning without an extra step. Where this is feasible or desirable is highly dependent on your specific data and query requirements, happy to talk about that. More details on prefix options is at https://docs.aws.amazon.com/firehose/latest/dev/s3-prefixes.html

### Just exporting and uploading

* Low-tech and good for the sake of proving value quickly. Just export as much as you can from the existing systems and upload CSV/JSON/whatever. You'll want to automate that at some point though.

### "Pre-packaged" solution

* Here's an example that was the foundation used by the Proserv team in Australia for a data lake for a major project. It's quick to get setup and provides good practice around having a staging area for new data + an example of how to process and move into the primary data lake https://github.com/aws-samples/accelerated-data-lake

### Glue 

AWS Glue is both a catalog of data you and your team have available, as well as an orchestration layer for pulling in and updating data in your data lake.

* Here's an example that runs through the full end-to-end process of populating the catalog, kicking off a job, notifying on outcomes, etc. https://aws.amazon.com/blogs/big-data/build-and-automate-a-serverless-data-lake-using-an-aws-glue-trigger-for-the-data-catalog-and-etl-jobs/
* If you just want an example of running a crawler, this example uses step functions to make it flexible and easy to debug if you run into problems https://aws.amazon.com/blogs/big-data/how-to-export-an-amazon-dynamodb-table-to-amazon-s3-using-aws-step-functions-and-aws-glue/
https://github.com/aws-samples/accelerated-data-lake

# Wanting to analyze and run queries on your data lake?

* Jump straight into Athena https://aws.amazon.com/blogs/big-data/analyzing-data-in-s3-using-amazon-athena/
* JSON is often the easiest to get started, but can be the most challenging given the flexible structure. Here's some guidance on defining a schema for it: https://aws.amazon.com/blogs/big-data/create-tables-in-amazon-athena-from-nested-json-and-mappings-using-jsonserde/

# Wanting to visualize your data?

* You can ignore the fact this is IoT and jump straight to the visualization/Quicksight bit, it'll show you how to connect do you data lake: https://aws.amazon.com/blogs/big-data/build-a-visualization-and-monitoring-dashboard-for-iot-data-with-amazon-kinesis-analytics-and-amazon-quicksight/

# Wanting to optimise cost efficiency and/or performance?

* Data formats play an important part. You're charged for data retrieved from S3, and some formats are more compact than others. Here's the ones we support: https://docs.aws.amazon.com/redshift/latest/dg/c-spectrum-data-files.html
* Compression! Again you're charged for data retrieved so if you can compress it then less is retrieved. Compression options are also listed on https://docs.aws.amazon.com/redshift/latest/dg/c-spectrum-data-files.html
* Partitioning: The more hints you can provide to reduce the places the query engine needs to look for data, the less data that needs to be scanned and the more performant the queries will be https://docs.aws.amazon.com/athena/latest/ug/partitions.html
* Different storage classes: S3 has a multitude of options to help align costs with your usage requirements. As a rough rule of thumb data you're accessing regularly will typically be in On Demand, rarely using but need to be access quickly if required is in Infrequent Access, and anything that you're almost certainly not accessing but need to keep for compliance/regulatory/emergency reasons should be in Glacier. You can automate the transition through these states using lifecycle policies https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-lifecycle.html

# Time to start your machine learning journey?

* SageMaker makes scaling training and deployment of customer models much easier, each new instance has these examples available on it https://github.com/awslabs/amazon-sagemaker-examples
* The above examples include a couple of recommendation engines (https://github.com/awslabs/amazon-sagemaker-examples/blob/master/introduction_to_applying_machine_learning/gluon_recommender_system/gluon_recommender_system.ipynb and https://github.com/awslabs/amazon-sagemaker-examples/blob/master/introduction_to_amazon_algorithms/object2vec_movie_recommendation/object2vec_movie_recommendation.ipynb), but we also released a managed service recently to make it even easier to get started on recommendations https://aws.amazon.com/personalize/