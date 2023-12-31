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

## 1: Introduction

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


### Creating a custom pipeline with Docker</br>
Let's create an example pipeline. We will create a dummy `pipeline.py` Python script that receives and prints an argument.</br>

```
import sys
import pandas # we don't need this but it's useful for the example

# print arguments
print(sys.argv)

# argument 0 is the name os the file
# argumment 1 contains the actual first argument we care about
day = sys.argv[1]

# cool pandas stuff goes here

# print a sentence with the argument
print(f'job finished successfully for day = {day}')
```

We can run this script with python pipeline.py <some_number>, and it should print 2 lines:
```
- ['pipeline.py', '<some_number>']
- job finished successfully for day = <some_number>
```

Let's containerize it by creating a Docker image. Create the following Dockerfile file:

```
# base Docker image that we will build on
FROM python:3.9.1

# set up our image by installing prerequisites; pandas, in this case
RUN pip install pandas

# set up the working directory inside the container
WORKDIR /app
# copy the script to the container. 1st name is the source file, 2nd is a destination
COPY pipeline.py pipeline.py

# define what to do first when the container runs
# in this example, we will just run the script
ENTRYPOINT ["python", "pipeline.py"]
```

Let's build the image:
```
docker build -t test:pandas .
```

- The image name will be tested, and its tag will be pandas. If the tag isn't specified, it will default to the latest.
We can now run the container and pass an argument to it so that our pipeline will receive it:

```
docker run -it test:pandas some_number
```

You should get the same output you did when you ran the pipeline script by itself.

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

#### Running Postgres in a container

In later parts of the course, we will use Airflow, which uses PostgreSQL internally. We can use PostgreSQL (or just Postgres) for simpler tests.

You can run a containerized version of Postgres that doesn't require installation steps. You only need to provide a few environment variables and a volume for storing data.

Create a folder anywhere you'd like for Postgres to store data. We will use the example folder `ny_taxi_postgres_data`. Here's how to run the container:

```
docker run -it \
    -e POSTGRES_USER="root" \
    -e POSTGRES_PASSWORD="root" \
    -e POSTGRES_DB="ny_taxi" \
    -v $(pwd)/ny_taxi_postgres_data:/var/lib/postgresql/data \
    -p 5432:5432 \
    postgres:13
```

##### Running Postgres
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/0d05f443-96e7-4ff7-80d9-9c61e70ba995)

##### Output
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/79d48205-e722-4701-a457-991adc214a92)

- The container needs 3 environmental variables:
  - POSTGRES_USER is the username for logging into the database. We chose root.
  - POSTGRES_PASSWORD is the password for the database. We chose root
    - IMPORTANT: These values are only meant for testing. Please change them for any serious project.
  - POSTGRES_DB is the name that we will give the database. We chose ny_taxi.
- -v points to the volume directory. The colon: separates the first part (path to the folder in the host computer) from the second part (path to the folder inside the container).
  - Path names must be absolute. If you're in a UNIX-like system, you can use pwd to print your local folder as a shortcut; this example should work with both bash and zsh shells, but fish will require you to remove the $.
  - This command will only work if you run it from a directory that contains the ny_taxi_postgres_data subdirectory you created above.
- The -p is for port mapping. We map the default Postgres port to the same port in the host.
- The last argument is the image name and tag. We run the official Postgres image on version 13.</br>

Once the container is running, we can log into our database with pgcli with the following command:

```
pip install pgcli
pgcli -h localhost -p 5432 -u root -d ny_taxi
```

##### Testing connection to the database
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/76e0584c-1f8e-45bd-9873-ba18c2c80a27)

- -h is the host. Since we're running locally, we can use localhost.
- -p is the port.
- -u is the username.
- -d is the database name.
- The password is not provided; it will be requested after running the command.

#### Ingesting data to Postgres with Python

We will now create a Jupyter Notebook `upload-data.ipynb` file, which we will use to read a CSV file and export to Postgres.

We will use data from the NYC TLC Trip Record Data website. Specifically, we will use the Yellow Taxi trip records CSV file for January 2021. A dictionary to understand each field is available here.

Check the completed `upload-data.ipynb` in this link for a detailed guide. Feel free to copy the file to your work directory; in the same directory, you must have the CSV file linked above and the `ny_taxi_postgres_data` subdirectory.


##### Running Jupyter Notebook
```
jupyter notebook
```

##### Downloading the data
```
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz
```
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/23b0eedc-70d0-4932-88fb-7e7aec686fd6)

##### Dataset
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/4951c78b-6995-4b3c-a204-848ee2604f7b)

##### Data Dictionary: https://www.nyc.gov/assets/tlc/downloads/pdf/data_dictionary_trip_records_yellow.pdf

##### Importing dataset into jupyter notebook
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/b052c698-7fa3-46b2-9668-88c76b9c1c23)

##### Creating Data Definition Language (DDL) Schema
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/d22ec9b0-4b03-40e2-a936-ff84217dce8b)

###### Installing Python library for dealing with SQL: SQLAlchemy
```
pip install sqlalchemy
```

Install Dependencies for connecting to PostgreSQL: pip install psycopg2-binary

##### Creating a jupyter notebook connection to PostgreSQL
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/26cefb8e-dc88-43af-9e36-88d98672a61d)
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/62b6827e-49b4-4b3e-916f-6c0a71cac3c8)

##### Checking the table created in PostgreSQL
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/898bcdde-307e-43ee-ab85-7b4f99e51228)

##### Inserting data into the table
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/2cc209fa-8073-49f3-b56a-0dd561be34c8)
##### Checking output in PostgreSQL
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/f27a6b5c-52e9-491a-a6ee-a01a91ee11e0)
##### Inserting the rest of the DB into the table
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/c0541acb-46a4-45c2-8f21-8db3b2f8aedd)
##### Checking output in PostgreSQL
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/4339ba1c-d697-4955-957f-1e4711560445)


#### Connecting pgAdmin and Postgres

##### Exploring the data
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/5242afc3-3612-4d3e-95b0-e4ab66799206)

Image to run PGAdmin: https://hub.docker.com/r/dpage/pgadmin4/

Comment to run PGAdmin in Docker:
```
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -p 8080:80 \
  dpage/pgadmin4
```

#### Logging into pgAdmin
##### Creating a server in pgAdmin
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/d3ae55a7-4d8a-4659-932b-18ed658358fc)

##### Connecting pgAdmin to Postgres using Docker Network:
Link: https://docs.docker.com/engine/reference/commandline/network_create/

```
docker network create pg-network
```

##### Docker command: Running Postgres (change the path)
```
docker run -it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v $(pwd)/ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  --network=pg-network \
  --name pg-database \
  postgres:13
```

```
pgcli -h localhost -p 5432 -u root -d ny_taxi
```

Checking the data again
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/ff1968f9-e935-4b73-8027-5f3442c93341)

#### To run PGAdmin in the same network:
```
docker run -it \
  -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
  -e PGADMIN_DEFAULT_PASSWORD="root" \
  -p 8080:80 \
  --network=pg-network \
  --name pgadmin-2 \
  dpage/pgadmin4
```
pgAdmin connection settings
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/f3c4cba1-639a-48f4-964c-d8966ed29931)

![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/b2545966-7cad-4b1a-9ef6-50f8810c3e30)

##### Querying first 100 rows of database using pgAdmin
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/9b62752c-429b-4b64-b8f5-64140f9ec779)

##### Querying from the query tool of pgAdmin
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/752c1591-9557-4408-a15b-c47a5eedba1a)

### Dockerizing the Ingestion Script

##### Convert Jupyter Notebook into Python Script
jupyter nbconvert --to=script upload-data.ipynb

#### Create file ingest_data.py and create variable arguments
```
#!/usr/bin/env python
# coding: utf-8

import os
import argparse

from time import time

import pandas as pd
from sqlalchemy import create_engine


def main(params):
    user = params.user
    password = params.password
    host = params.host 
    port = params.port 
    db = params.db
    table_name = params.table_name
    url = params.url
    
    # the backup files are gzipped, and it's important to keep the correct extension
    # for pandas to be able to open the file
    if url.endswith('.csv.gz'):
        csv_name = 'output.csv.gz'
    else:
        csv_name = 'output.csv'

    os.system(f"wget {url} -O {csv_name}")

    engine = create_engine(f'postgresql://{user}:{password}@{host}:{port}/{db}')

    df_iter = pd.read_csv(csv_name, iterator=True, chunksize=100000)

    df = next(df_iter)

    df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime)
    df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)

    df.head(n=0).to_sql(name=table_name, con=engine, if_exists='replace')

    df.to_sql(name=table_name, con=engine, if_exists='append')


    while True: 

        try:
            t_start = time()
            
            df = next(df_iter)

            df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime)
            df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)

            df.to_sql(name=table_name, con=engine, if_exists='append')

            t_end = time()

            print('inserted another chunk, took %.3f second' % (t_end - t_start))

        except StopIteration:
            print("Finished ingesting data into the postgres database")
            break

if __name__ == '__main__':
    parser = argparse.ArgumentParser(description='Ingest CSV data to Postgres')

    parser.add_argument('--user', required=True, help='user name for postgres')
    parser.add_argument('--password', required=True, help='password for postgres')
    parser.add_argument('--host', required=True, help='host for postgres')
    parser.add_argument('--port', required=True, help='port for postgres')
    parser.add_argument('--db', required=True, help='database name for postgres')
    parser.add_argument('--table_name', required=True, help='name of the table where we will write the results to')
    parser.add_argument('--url', required=True, help='url of the csv file')

    args = parser.parse_args()

    main(args)
```

##### Check the data again: It shows that the table exits
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/c9fb9e75-b8dc-4e18-9b5c-88d348942df6)

##### Recreate the table again after dropping the table
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/1fd4310d-609b-4614-a651-f80d24da6288)

##### Data Ingestion
```
URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

python ingest_data.py \
  --user=root \
  --password=root \
  --host=localhost \
  --port=5432 \
  --db=ny_taxi \
  --table_name=yellow_taxi_trips \
  --url=${URL}
```

![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/c8467eb0-169d-475e-94ee-734b319998f4)


##### Checking if the program finished successfully
```
echo $?
```



##### Check for the database in pgAdmin
![image](https://github.com/srsapireddy/Data_Engineering/assets/32967087/9cfb8b63-354d-4dc7-9719-5ead738c7e8d)


##### Dockrizing the code
##### Change the Dockerfile to dockerize the code: ingest_data.py
```
FROM python:3.9.1

RUN pip install pandas
RUN pip install pandas sqlalchemy psycopg2

WORKDIR /app
COPY ingest_data.py pipeline.py

ENTRYPOINT [ "python", "ingest_data.py" ]
```

##### Build docker image
```
docker build -t taxi_ingest:v001 .
```
##### Running the new docker image again
```
docker run -it \
  --network=pg-network \
  taxi_ingest:v001 \
    --user=root \
    --password=root \
    --host=pg-database \
    --port=5432 \
    --db=ny_taxi \
    --table_name=yellow_taxi_trips \
    --url=${URL}
```

### Stopping all the Docker Containers
```
docker stop $(docker ps -a -q)
docker container rm $(docker container ls -aq)
docker container ls -a
```

## Running Postgres and pgAdmin with Docker-Compose





















