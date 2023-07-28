# Data_Engineering

## Overview

### Architecture diagram
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/d7762c6a-ba07-4f2a-8eab-dfcc3c3d8d74)

#### Technologies </br>
Google Cloud Platform (GCP): Cloud-based auto-scaling platform by Google</br>
Google Cloud Storage (GCS): Data Lake</br>
BigQuery: Data Warehouse</br>
Terraform: Infrastructure-as-Code (IaC)</br>
Docker: Containerization</br>
SQL: Data Analysis & Exploration</br>
Prefect: Workflow Orchestration</br>
dbt: Data Transformation</br>
Spark: Distributed Processing</br>
Kafka: Streaming</br>

#### Tools
For this course, you'll need to have the following software installed on your computer:</br>
Docker and Docker-Compose</br>
Python 3 (e.g. via Anaconda)</br>
Google Cloud SDK</br>
Terraform

### Introduction to Data Engineering
#### Data pipelines
A data pipeline is a service that receives data as input and outputs more data—for example, reading a CSV file, transforming it, and storing it as a table in a PostgreSQL database.

![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/98f7ef4a-ff38-47ce-b4f2-e59f52eedd8f)

#### Docker and Postgres</br>
#### Docker basic concepts</br>

Docker is a containerization software that allows us to isolate software similarly to virtual machines but in a much cleaner way.</br>

A Docker image is a snapshot of a container we can define to run our software or, in this case, our data pipelines. We can run our containers, exporting Docker images to Cloud providers such as Amazon Web Services or Google Cloud Platform.</br>

Docker provides the following advantages:</br>

- Reproducibility</br>
- Local experimentation</br>
- Integration tests (CI/CD)</br>
- Running pipelines on the cloud (AWS Batch, Kubernetes jobs)</br>
- Spark (analytics engine for large-scale data processing)</br>
- Serverless (AWS Lambda, Google functions)</br>
- Docker containers are stateless: any changes inside will NOT be saved when the container is killed and started again. This is an advantage because it allows us to restore any container to its initial state in a reproducible manner. However, you will have to store data elsewhere if needed; a common way is with volumes.</br>

### Testing Docker 
```
docker adduser $USER docker
sudo reboot
docker run hello-world
```
#### Output
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/ffaa3c90-de41-4ac2-b8f9-528a7f3ab594)

#### Running Docker Ubuntu in interactive mode
```
docker run -it ubuntu bash
```
#### Output
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/2bc4f8fa-5c3f-4bbb-8668-bf397750dbb9)

#### Running Python 3.9 in interactive mode
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/7e8db211-5c78-45b2-9d4f-de52efd08d20)
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/e65992ec-b30d-407c-9316-c7e736154027)

#### Creating Docker Image
```
docker build -t test:pandas .
```
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/afcda330-800f-40ee-92ea-dde1ecc2a7fa)

#### Running Docker Image
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/79c200d1-a12a-4cd7-bc45-6dfe7f450526)

#### Running docker image with command line arguments
##### Docker File
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/2cd63a0a-e8e8-4a6d-935d-5890d593aa9d)
##### pipeline.py file
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/cc322bf3-17c4-47b6-9c59-b2aeb71d553d)

```
docker build -t test:pandas .
docker run -it test:pandas 2023-01-15
```

##### Output
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/c241c6a6-a9aa-4b87-833c-4bc8018e9e59)







