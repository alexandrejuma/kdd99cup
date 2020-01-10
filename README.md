# KDD'99 Cup Anomaly Detection with PySpark Kmeans 

**Author**: Alexandre Juma
**Date**: 09/01/2019

Yet another atempt to detect anomalies from the KDD'99 Network connection dataset. This time SparkML was used to train a KMeansModel with centroid distance based threshold to detect anomalies.

## Specifics of trial
The main mechanisms at work in this trial:

 1. Exploratory data analysis using PCA 
 2. Encoded categorical features with StringIndexer and OneHotEncoder
 3. Vectorized encoded categorical and numerical features in a single Feature Vector
 4. Standardized feature distribution with StandardScaler (mean = 0; std = 1)
 5. Basic Grid search for the best K 
 6. Supported KMeans metrics silhouette score, within set sum of squared errors and weighted cluster entropy
 7. Anomaly represented as violation of a max distance threshold to cluster centroid
 8. Plotting results and analysis

## Environment

 1. HARK Cluster: AWS EMR 0.28
 2. Apache Hadoop: 2.8.5
 3. Apache Spark: 2.4.4
 4. Jupyterhub: 1.0.0
 5. Apache Zeppelin: 0.8.2
 6. Apache Livy (Incubator): 0.6.0

Suggested EMR cluster optimized for training/inference: 1 Master (m5a.2xlarge), 2 Workers (c5.9xlarge).

PS: With this setup, and the correct SparkContext configurations, the whole workflow (including hyper-parameter optimization) will run in a couple of minutes. If you're not in a hurry, you can virtually run any EMR cluster. I've tested with a simple setup (2 executors with 2 cores and 4GB RAM each) and it ran in 10 minutes.

**AWS CLI Template**

`aws emr create-cluster --applications Name=Spark Name=Zeppelin Name=Hadoop Name=JupyterHub --tags 'cost_tag=jumatest' --ebs-root-volume-size 10 --ec2-attributes '{"KeyName":"<key_name>","InstanceProfile":"EMR_EC2_DefaultRole"}' --service-role EMR_DefaultRole --release-label emr-5.28.0 --name 'ABD_JUMA' --instance-groups '[{"InstanceCount":1,"EbsConfiguration":{"EbsBlockDeviceConfigs":[{"VolumeSpecification":{"SizeInGB":32,"VolumeType":"gp2"},"VolumesPerInstance":4}]},"InstanceGroupType":"MASTER","InstanceType":"m5a.4xlarge","Name":"Master Instance Group"}]' --scale-down-behavior TERMINATE_AT_TASK_COMPLETION --region eu-west-1`

## How to run

**Pre-requisites:**

 1. A VPC with an available subnet and with an Internet Gateway attached
 2. A S3 Bucket accessible by the EMR Cluster
 3. A EMR Master Node Security Group with the required rules for access

**You can find two Notebooks and a PySpark script:**

 1. IPYNB Notebook to load on Jupyter (see limitations)
 2. JSON Notebook to load on Apache Zeppelin
 3. Python ready to run with spark-submit or via pyspark shell

**With any of these options, it's required to:**

 1. configure at least an accessible S3 URI in the 2nd paragraph.
 2. The rest of the configuration can be kept with the defaults.
 3. If you know what you're doing, you can tune the Spark Context resource parameters to add more executors, cores, memory, etc
 

## Limitations

 1. When ran in Jupyter with PySpark kernel, no buffered output is possible
 2. When ran in Jupyter with a Livy based kernel, dynamic YARN resource alocation is not working properly. Tune the 1st paragraph manually.
