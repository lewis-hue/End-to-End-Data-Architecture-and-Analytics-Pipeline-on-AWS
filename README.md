## Overview

This document provides a detailed walkthrough of the end-to-end data engineering pipeline designed for Safaricom Analytics using Amazon Web Services (AWS). The project encompasses the construction of a scalable and reliable data pipeline, executing analytics, applying machine learning models, and leveraging visualization through Amazon QuickSight. Every stage of the pipeline is meticulously planned to ensure a robust architecture that supports business intelligence, operational decision-making, and growth. The high-level architecture of this project is illustrated below.

## High-level Architecture Diagram
![High-level Architecture Diagram](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/High_Level_Architecture_Diagram.png)

## Table of Contents

1. **VPC, Networking Setup, and Workload Definition**
2. **Amazon Elastic Kubernetes Service (EKS) Cluster Setup**
3. **Amazon S3 Buckets for Data Storage**
4. **Data Ingestion from Google BigQuery to AWS Glue**
5. **ETL Processing with AWS Glue**
6. **Data Lake Organization and Curated Data Setup**
7. **Feature Engineering and Machine Learning with Amazon SageMaker**
8. **Data Migration with AWS DMS**
9. **Querying with Amazon Athena**
10. **Business Insights from Athena Query Results**
11. **Data Visualization with Amazon QuickSight**

---

### 1. **VPC, Networking Setup, and Workload Definition**

![VPC and Networking Setup](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Success_VPC.png)

![Workload Definition](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Successful%20Definition%20of%20a%20Workload.png)

**Description:**

The project begins with configuring an **Amazon Virtual Private Cloud (VPC)**, which is fundamental for establishing a secure and scalable networking environment. A VPC ensures that AWS resources—such as EC2 instances, EMR clusters, MSK clusters, and Glue jobs—are isolated in a private network, ensuring data protection and preventing unauthorized access.

The configuration involves setting up CIDR blocks, subnets, route tables, and VPC endpoints for private connections. This VPC design supports multiple availability zones (AZs) to ensure high availability and fault tolerance.

**Impact:**

- **Security**: Protects resources by isolating the network and enforcing strict access control.
- **Scalability**: Ensures that the network architecture can expand to accommodate growing workloads.
- **Availability**: Enables high availability by utilizing multiple AZs, enhancing fault tolerance.

**Importance:**

The VPC setup is the backbone of the architecture, providing a secure, reliable, and scalable foundation for all subsequent stages of the data pipeline. It ensures that AWS services, like MSK, EMR, and Glue, can operate securely without exposure to the public internet.

---

### 2. **Amazon Elastic Kubernetes Service (EKS) Cluster Setup**

![EKS Cluster Setup](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Success_EKS_Cluster.png)

**Description:**

Following VPC setup, an **Amazon EKS** cluster is provisioned to manage and orchestrate containerized applications. EKS automates the scaling and management of Kubernetes clusters, which is essential for running data processing jobs and microservices efficiently.

EKS streamlines the deployment of ETL jobs, microservices, and batch jobs, supporting the scalable execution of data workflows.

**Impact:**

- **Scalability**: Automatically adjusts the number of running containers based on workload demands, reducing manual intervention.
- **Flexibility**: Allows containerized applications to run with minimal management overhead.

**Importance:**

The EKS cluster is crucial for handling the operational deployment of ETL jobs and other applications, ensuring that resources are efficiently utilized and scalable to handle larger workloads as the data pipeline grows.

---

### 3. **Amazon S3 Buckets for Data Storage**

**Description:**

**Amazon S3** is used as the data storage solution for all stages of the data pipeline, offering robust scalability and security. Three main S3 buckets are created to handle different stages of data processing:

1. **Raw Data Bucket (Data Lake)**: Stores unprocessed data ingested from external sources, such as Google BigQuery.

   ![Raw Data Bucket](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Data%20Lake%20Formation.png)

2. **Curated Data Bucket**: Contains the processed and cleaned data, ready for further analysis.

   ![Curated Data Bucket](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/S3%20Curated%20Database.png)

3. **Cleaned Data Bucket**: Holds the final version of the data after feature engineering and machine learning model outputs.

**Impact:**

- **Raw Data**: Serves as the initial stage for data processing and transformation.
- **Curated Data**: Facilitates easier analysis and machine learning by storing cleaned and processed datasets.
- **Cleaned Data**: Provides finalized datasets that are ready for reporting and decision-making.

**Importance:**

S3's scalability and durability ensure reliable data storage across all stages of the pipeline, from raw ingestion to final processing. It allows easy access and seamless integration with AWS services for further analysis.

---

### 4. **Data Ingestion Process**

#### From Kafka Producers, EKS to Kinesis Firehose to Data Lake

![Kafka to Kinesis Firehose to Data Lake](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/MSK%20to%20Firehose.png)

**Description:**

In this phase, data is ingested from **Kafka Producers** (streaming applications or microservices) to **Amazon EKS**, and subsequently routed through **Amazon Kinesis Firehose** into the **Data Lake** in **Amazon S3**. Kinesis Firehose ensures real-time streaming data ingestion, scaling automatically to handle large volumes of incoming data.

**Impact:**

- **Real-time Processing**: Enables the real-time ingestion of data, providing timely insights.
- **Scalable**: Kinesis Firehose automatically scales to accommodate varying data throughput.
- **Low Latency**: EKS and Kinesis ensure low-latency data streaming, making the system responsive.

#### From Google BigQuery to AWS Glue

![Secondary Data Ingestion (From Bigquery)](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Third_Party_Job.png)

**Description:**

Data is ingested from **Google BigQuery** using the AWS Glue connector, a serverless ETL service that automates data extraction, transformation, and loading (ETL). The transformed data is then stored in the **Data Lake** on **Amazon S3**, ready for further analysis.

**Impact:**

- **Automated ETL**: AWS Glue reduces manual data integration work by automating the extraction and transformation processes.
- **Data Enrichment**: The transformation process ensures data consistency, quality, and readiness for downstream analytics.
- **Seamless Integration**: The AWS Glue connector ensures smooth integration of BigQuery with AWS.

#### From AWS Glue to Data Lake

![Sample Data Table Migrated](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/fulizadata_awslake.png)

**Description:**

After transformation, the data processed by **AWS Glue** is stored in the **Data Lake** (Amazon S3), which serves as a central repository for all types of data, whether raw, curated, or cleaned.

**Impact:**

- **Centralized Storage**: All data is stored in one highly available and scalable location.
- **Scalable and Cost-Effective**: Amazon S3 offers scalable storage with low-cost options, making it a cost-effective solution for large datasets.
- **Future-Proof**: Data in S3 can easily be integrated with other AWS services for further analysis.

---

### 5. **ETL Processing with AWS Glue**

![ETL Processing with AWS Glue](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Curated_Data_Runs_AWS_Glue.png)

**Description:**

**AWS Glue** is responsible for ETL processing, where it cleans the raw data and applies necessary transformations (such as filtering, aggregation, and enrichment). The resulting transformed data is stored in the **Curated S3 Bucket** for use in analysis.

**Impact:**

- **Data Quality**: Ensures that data is cleaned, formatted, and transformed to meet the needs of downstream applications.
- **Business Rules Application**: Implements specific business rules to align the data with operational needs.
- **Automated Processing**: ETL jobs are automated, ensuring consistent and efficient data processing.

**Importance:**

ETL processing ensures that only the most relevant and high-quality data is used in the analytics pipeline, enabling better decision-making and accurate insights.

---

### 6. **Data Lake Organization and Curated Data Setup**

**Description:**

After data is processed by AWS Glue, it is organized into the curated data lake, making it ready for use in feature engineering and machine learning applications.

**Impact:**

- **Structured Data**: Organizes data for easy access and retrieval, enhancing the efficiency of subsequent stages.
- **Business-Centric**: The curated data structure aligns with specific business use cases.

**Importance:**

The curated data lake serves as a well-organized and accessible repository for all processed data, enabling faster access for analysis, machine learning, and reporting.

---

### 7. **Feature Engineering and Machine Learning with Amazon SageMaker**

![Feature Engineering](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Feature_Engineering_Sagemaker.png)

![Prediction models](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Machine_Learning_Model%20(Sagemaker).png)

**Description:**

**Amazon SageMaker** is used for feature engineering and machine learning. It processes the curated data to create new features that improve model predictions. For instance, churn prediction and loan activity prediction models are built based on customer behavior data.

**Impact:**

- **Feature Engineering**: Generates new predictive features, improving model accuracy.
- **Machine Learning Models**: Trains predictive models that provide actionable insights into customer behavior and trends.

**Importance:**

Feature engineering and machine learning are crucial for building predictive models that help businesses make data-driven decisions, such as identifying high-value customers or predicting churn.

---

### 8. **Data Migration with AWS DMS**

![Data Migration with AWS DMS](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Success_MigrationTask_RDS.png)

**Description:**

The **AWS Database Migration Service (DMS)** migrates curated data from Amazon S3 to a queryable database, ensuring the data is readily available for querying using **Amazon Athena**.

**Impact:**

- **Efficient Data Migration**: Ensures smooth, automated data migration with minimal downtime.
- **Real-time Synchronization**: Keeps data synchronized between systems, ensuring consistency.

**Importance:**

Data migration is essential for making the curated data easily accessible for querying and further analysis, improving the efficiency of the overall pipeline.

---

### 9. **Querying with Amazon Athena**

**Description:**

Once the data is migrated to a queryable database, **Amazon Athena** is used to run interactive SQL queries directly on data stored in Amazon S3. Athena enables fast, serverless querying capabilities on large datasets, reducing the time to gain insights.

**Impact:**

- **Fast Querying**: Quickly processes large datasets without the need for complex infrastructure.
- **Cost-Effective**: Serverless architecture eliminates the need for managing query infrastructure.

**Importance:**

Athena simplifies querying large datasets, providing fast access to actionable insights for decision-making.

---

### 10. **Business Insights from Athena Query Results**

**Description:**

Athena queries generate valuable business insights, including transaction amounts, loan metrics, and subscriber activity:

- **Average Transaction Amount by County**:

   ![Data Migration with AWS DMS](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Avg_Transaction_Amount_Per_County.png)

- **Loan Metrics by County**:

   ![Loan Metrics by County](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/counties_total_loans.png)

- **Inactive Subscribers**:

   ![Inactive Subscribers](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Percentage_Inactive_Subscribers.png)

**Business Implications:**

- **Transaction Insights**: Focus on expanding services or offering promotions in Mombasa, where the transaction volume is highest.
- **Loan Insights**: Explore tailored loan products in Nairobi, where high loan amounts are recorded, but loan uptake is lower.
- **Retention Strategy**: Address high inactivity rates in specific regions with targeted retention efforts.

---

### 11. **Data Visualization with Amazon QuickSight**

![Dashboard](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/SafaricomAnalytics%20Dashboard.png)

**Description:**

**Amazon QuickSight** is used for interactive data visualization. Dashboards and reports provide business stakeholders with actionable insights from the data, making it easier to track key metrics and make data-driven decisions.

**Impact:**

- **Actionable Insights**: Allows stakeholders to explore data through intuitive visualizations.
- **Improved Decision-Making**: Facilitates data-driven decision-making by presenting insights in a digestible format.

**Importance:**

QuickSight enhances the accessibility of data insights for non-technical users, empowering them to make informed decisions that drive business outcomes.

---

## Conclusion

This end-to-end data pipeline, powered by AWS services such as EKS, S3, Glue, Athena, SageMaker, and QuickSight, provides Safaricom with a powerful, scalable, and secure analytics solution. By integrating these services, the pipeline ensures smooth data processing and analysis, delivering actionable insights that directly impact Safaricom’s operational strategies and decision-making processes.
