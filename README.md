## Overview

This document outlines the end-to-end data engineering process for Safaricom Analytics using AWS. The project involves building a robust data pipeline, performing analytics, and applying machine learning models, followed by visualization in Amazon QuickSight. Each step has been carefully crafted to ensure that the architecture is scalable, maintainable, and optimized for business intelligence and operational decision-making. The steps carried are shown in the following high-level architecture diagram below.

## High-level Architecture Diagram
![High-level Architecture Diagram](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/High_Level_Architecture_Diagram.png)

## Table of Contents

1. **VPC and Networking Setup**
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

### 1. **VPC and Networking Setup**
![VPC and Networking Setup](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Success_VPC.png)

**Description:**

The project starts with the creation of an isolated **Amazon Virtual Private Cloud (VPC)** that ensures security and scalability. A VPC allows you to launch AWS resources into a virtual network that you define. The VPC is essential to ensure that all resources (such as EC2 instances, EMR clusters, MSK clusters, and Glue jobs) communicate in a secure environment, free from public internet exposure.

The process includes configuring the VPC with CIDR blocks, subnetting, route tables, and VPC endpoints for private connections. The VPC settings must also support several availability zones (AZs) for high availability.

**Impact:**

- Ensures the security of resources by isolating the network and enforcing fine-grained control over inbound and outbound traffic.
- Scalable network architecture to support growing workloads.
- High availability and fault tolerance through multiple AZs.

**Importance:**

The VPC architecture is crucial for building a secure, reliable, and scalable foundation for the entire data pipeline. It allows other services like MSK, EMR, and Glue to operate without being exposed to public networks, reducing the risk of data breaches.

---

### 2. **Amazon Elastic Kubernetes Service (EKS) Cluster Setup**

![EKS Cluster Setup](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Success_EKS_Cluster.png)

**Description:**

The next step is the creation of an **EKS cluster**. EKS manages Kubernetes clusters on AWS, providing a powerful platform for deploying and scaling containerized applications. EKS allows for seamless orchestration of microservices and batch jobs, vital for managing the data workflows.

**Impact:**

- Provides a flexible and scalable environment for running containerized applications.
- Ensures automated scaling based on demand, reducing the need for manual intervention.

**Importance:**

The EKS cluster is key in managing the deployment of ETL jobs, microservices, and other containerized applications that are part of the data pipeline. The flexibility and scalability of Kubernetes help optimize resource usage and ensure that the architecture can handle growing data workloads.

---

### 3. **Amazon S3 Buckets for Data Storage**

**Description:**

**Amazon S3** is used to store raw, curated, and cleaned data. There are three primary S3 buckets:

1. **Raw Data Bucket (Data Lake)** – Stores data ingested from external sources like BigQuery.

![Raw Data Bucket](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Data%20Lake%20Formation.png)
   
3. **Curated Data Bucket** – Stores cleaned and processed data, ready for analysis.

![Curated Data Bucket](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/S3%20Curated%20Database.png)
  
5. **Cleaned Data Bucket** – Stores final data after feature engineering and model results.

**Impact:**

- **Raw Data**: Serves as the starting point for all data processing activities.
- **Curated Data**: Provides a refined data set for analytics and machine learning.
- **Cleaned Data**: Acts as the final storage for the processed data, available for reporting and visualization.

**Importance:**

S3's scalability and durability ensure that data is safely stored and easily accessible for processing and querying. It provides a flexible architecture for managing data in different stages of the pipeline.

---

## 4. Data Ingestion Process

### From Kafka Producers, EKS to Kinesis Firehose to Data Lake

![Kafka to Kinesis Firehose to Data Lake](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/MSK%20to%20Firehose.png)

**Description:**

In this stage, data is ingested from **Kafka Producers** (which could be streaming applications or microservices) to **Amazon EKS** (Elastic Kubernetes Service) and then routed through **Amazon Kinesis Firehose** to the **Data Lake** stored in **Amazon S3**. **Kinesis Firehose** is used to stream data in real-time, providing a fully managed, scalable solution for ingesting streaming data into AWS.

**Impact:**

- **Real-time Ingestion**: This process enables the real-time ingestion of data from various sources, providing immediate insights into incoming data streams.
- **Scalable Architecture**: Leveraging **Kinesis Firehose** ensures that data can be ingested at scale without worrying about throughput, as Kinesis handles automatic scaling.
- **Low Latency**: With the integration of **EKS** and **Kinesis**, the system minimizes latency, ensuring that data flows seamlessly from Kafka producers to the data lake in real-time.

### From Google BigQuery to AWS Glue

![Secondary Data Ingestion (From Bigquery)](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Third_Party_Job.png)

**Description:**

Data from **Google BigQuery** is ingested into AWS via an AWS connector to **AWS Glue**, a serverless ETL service. This process extracts the data, transforms it to fit the target schema, and loads it into the **Data Lake** stored in **Amazon S3**. The data extracted from BigQuery is processed and refined to meet the requirements of further analytics or machine learning workflows.

**Impact:**

- **Automated ETL**: AWS Glue automates the extraction, transformation, and loading process, significantly reducing the time and resources required for manual data integration.
- **Data Quality**: The transformation ensures that the data is cleaned, formatted, and enriched, making it more suitable for downstream applications like reporting and machine learning.
- **Seamless Integration**: The AWS Glue connector allows for easy and seamless integration of external sources like **Google BigQuery** into AWS, enabling the combination of data across different platforms for richer insights.

### From AWS Glue to Data Lake

![Sample Data Table Migrated](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/fulizadata_awslake.png)

![AWS Glue to Data Lake](https://github.com/lewis-hue/End-to-End-Data-Architecture-and-Analytics-Pipeline-on-AWS/blob/main/Third_Party_Job.png)

**Description:**

After the data has been processed and transformed by **AWS Glue**, it is loaded into the **Data Lake** in **Amazon S3**. The **Data Lake** serves as the central repository where raw, curated, and cleaned data is stored in a highly available and scalable manner, ready for further analytics, reporting, and machine learning tasks.

**Impact:**

- **Centralized Storage**: The **Data Lake** in **Amazon S3** provides a centralized location for storing large amounts of data in its raw and transformed state.
- **Scalable and Cost-effective**: **S3** offers scalable and cost-effective storage, which is crucial for managing large datasets without worrying about running out of space or facing high storage costs.
- **Future-Proof**: Data stored in **S3** can easily be integrated with other AWS services for further analysis, making it future-proof for evolving analytics needs.

"""


### 5. **ETL Processing with AWS Glue**

**Description:**

**AWS Glue** performs ETL (Extract, Transform, Load) processing on the ingested data. It cleans the raw data and applies transformations such as filtering, aggregation, and enrichment. The transformed data is then stored in the curated S3 bucket for analysis.

**Impact:**

- Cleans and formats data for consistency and usability.
- Applies business rules to the data, preparing it for the next stages in the pipeline.
- Ensures the quality of data through automated job execution.

**Importance:**

ETL processing is crucial for maintaining the integrity of the data and ensuring that only relevant, high-quality data is used for analysis, which is key for making informed business decisions.

---

### 6. **Data Lake Organization and Curated Data Setup**

**Description:**

Once the raw data is processed by AWS Glue, it is organized into the curated data lake, making it ready for feature engineering and machine learning.

**Impact:**

- Organizes the data in a structured format, facilitating easier access and retrieval.
- The curated data is structured to facilitate specific business use cases, such as customer analytics.

**Importance:**

The curated data lake acts as the central repository for all processed data, ensuring that the data is readily available for further analysis, machine learning, and reporting.

---

### 7. **Feature Engineering and Machine Learning with Amazon SageMaker**

**Description:**

**Amazon SageMaker** is used for feature engineering and machine learning model training. Feature engineering is performed on the curated data to generate new features that can better predict customer behavior and trends.

A churn prediction model and a loan activity prediction model are built, using relevant features derived from customer transaction history, loan activity, and engagement data.

**Impact:**

- Cleans and enriches data by creating new, predictive features.
- Trains machine learning models on the enriched data to predict key outcomes like customer churn and loan activity.

**Importance:**

Feature engineering is essential for building accurate predictive models. Machine learning models help in making data-driven decisions, such as targeting customers at risk of churn or identifying high-value loan customers.

---

### 8. **Data Migration with AWS DMS**

**Description:**

**AWS Database Migration Service (DMS)** is used to migrate data from the curated S3 data lake to a queryable database for analysis using **Amazon Athena**. DMS ensures that the data remains synchronized and available for querying in real time.

**Impact:**

- Ensures that the curated data is migrated efficiently and accurately.
- Keeps the data flow automated and consistent across different environments.

**Importance:**

Data migration is necessary for preparing the data for easy querying and analysis, reducing manual intervention and ensuring real-time updates.

---

### 9. **Querying with Amazon Athena**

**Description:**

Once the data is migrated, it is queried using **Amazon Athena**, a serverless interactive query service that allows for quick analysis of data stored in S3.

Athena queries are written using SQL and help analyze key metrics such as average transaction amount, loan activity, and repayment ratios by county.

**Impact:**

- Provides fast and scalable querying capabilities on large datasets.
- Reduces the time required to run queries by eliminating the need for traditional ETL processing.

**Importance:**

Athena enables the extraction of actionable insights from large datasets without the need for provisioning servers or complex infrastructure.

---

### 10. **Business Insights from Athena Query Results**

**Description:**

Athena queries produce valuable business insights, such as:

- **Average Transaction Amount by County**:

  | County      | Avg Transaction Amount |
  |-------------|------------------------|
  | Mombasa     | 110.25                 |
  | Nakuru      | 106.67                 |
  | Thika       | 106.67                 |
  | Kitale      | 102.50                 |
  | Nairobi     | 100.25                 |
  | Kisii       | 95.17                  |
  | Eldoret     | 87.50                  |

- **Total Transactions by County**:

  | County      | Total Transactions |
  |-------------|--------------------|
  | Mombasa     | 240.0              |
  | Nakuru      | 180.0              |
  | Kisii       | 160.0              |
  | Thika       | 140.0              |
  | Eldoret     | 100.0              |
  | Kitale      | 100.0              |
  | Nairobi     | 95.0               |

- **Loan Metrics by County**:

  | County      | Avg Loan Amount | Total Loans |
  |-------------|-----------------|-------------|
  | Nairobi     | 21,500.63       | 2           |
  | Mombasa     | 16,375.50       | 4           |
  | Thika       | 15,667.25       | 3           |
  | Nakuru      | 14,834.05       | 3           |
  | Kitale      | 11,750.63       | 2           |
  | Eldoret     | 11,250.18       | 2           |
  | Kisii       | 10,500.63       | 3           |

- **Inactive Subscribers**: 47.37% of customers are inactive.

**Business Implications:**

- **Transaction Metrics**: Mombasa has the highest average transaction amount and the most total transactions. Safaricom can focus on expanding services or offering promotions in Mombasa to capitalize on the high transaction volume.
- **Loan Metrics**: Nairobi has the highest average loan amount and relatively fewer total loans. Safaricom could explore targeting more loans in Nairobi, possibly introducing tailored loan products to further boost loan uptake.
- **Inactive Subscribers**: A significant portion of users (47.37%) are inactive. This indicates an opportunity for retention strategies in Mombasa, Nakuru, and other counties with high inactivity rates.

---

### 11. **Data Visualization with Amazon QuickSight**

**Description:**

After the data is processed and analyzed, it is visualized using **Amazon QuickSight**. QuickSight provides interactive dashboards that allow stakeholders to gain insights from the data easily.

**Impact:**

- Enables business users to interact with the data and make decisions based on visualizations.
- Reduces the complexity of interpreting raw data by presenting it in an easily understandable format.

**Importance:**

Data visualization is crucial for providing insights to non-technical stakeholders. QuickSight ensures that Safaricom's decision-makers can access and understand business metrics to drive actions and improvements.

---

## Conclusion

This comprehensive data pipeline, leveraging AWS services such as EKS, S3, Glue, Athena, SageMaker, and QuickSight, forms the backbone of Safaricom Analytics. It ensures that data flows securely, is processed efficiently, and is ready for analysis and machine learning. The insights generated from the data can directly impact Safaricom's strategic decisions and operational improvements.

By having a scalable and well-organized data architecture, Safaricom can continuously refine its operations, predict customer behavior, and deliver targeted interventions, all of which lead to improved performance and customer satisfaction.
"""

