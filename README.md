# End-to-End Spotify Data Pipeline Using AWS S3, Athena, Glue and QuickSight

## Project Overview

In today's data-driven world, building scalable and cost-efficient data pipelines in the cloud is a key skill for aspiring data engineers. This project demonstrates how to build an end-to-end data engineering pipeline on AWS, using a real-world dataset from Spotify. We’ll process, analyze, and visualize a real-world dataset from Spotify, using tools like Amazon S3, AWS Glue, Athena, and QuickSight.

## Architecture
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1C4w89z0UMc9_cTItqgWs6FoJeKhAaDHd"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />


## Tools & Services

This project leverages several key AWS services to handle the full data pipeline:

- **Amazon S3** is used for storing both raw and transformed data. It's a scalable object storage service that serves as the backbone of our pipeline's data lake.
- **AWS Glue** is a serverless ETL (Extract, Transform, Load) service that allows us to visually or programmatically build data workflows. In this project, it transforms raw CSV data into optimized Parquet files.
- **AWS Glue Crawler** automatically scans the processed data in S3 and creates schema definitions (tables) in the AWS Glue Data Catalog, which makes the data queryable in Athena.
- **AWS Athena** is a serverless, interactive SQL query service. It allows you to write SQL queries directly on data stored in S3 without needing to set up any servers.
- **AWS QuickSight** is a powerful business intelligence tool used to create interactive dashboards and visualizations based on the insights generated through Athena queries.
- **AWS IAM (Identity and Access Management)** is used to create secure users and roles, ensuring that only authorized entities can access or modify your AWS resources.

## Dataset Details

The dataset used in this project comes from Kaggle - Spotify Dataset 2023. The dataset is available in CSV format and has been pre-processed for use in this project. The dataset includes details on:

- **Albums:** Contains details of all the albums, including album ID, name, popularity, and release date.
- **Artists:** Contains information about the artists, including their names, number of followers, and genres.
- **Tracks:** Contains track-level data, including track ID, popularity, and other features like danceability and energy.

All files are manually uploaded to S3 for this project.

## The Workflow: Step-by-Step

### Step 1: Setting Up AWS IAM User and IAM Role

1. **Create a new IAM User**
    - In the AWS Management Console, search for IAM and open the service.
    - Navigate to Users > Create user.
    - Assign the following permission policies to the new IAM user:
        - Amazon S3 Full Access
        - AWS Glue Console Full Access
        - Amazon Athena Full Access
        - AWS QuickSight Athena Access
        - AWS QuickSight Describe RDS

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1Gli50B9BfmtD7Y9s2sR0dZCHVIOtwGqR"  
    style="margin-top: 40px; margin-bottom: 40px;"  />
</p>

2. **Create a new IAM role for S3 & Glue access**
    - Create a new role with the following permission policies:
        - Amazon S3 Full Access
        - AWS Glue Console Full Access

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1RcvcT6Vq3b7HAEJowLSVfVpUbU2fskCB" style="margin-top: 40px; margin-bottom: 40px;" />
</p>

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1P0cRZrn0L-j2RNllYiemwIdjiiEKTu6N" style="margin-top: 40px; margin-bottom: 40px;" />
</p>


### Step 2: Creating S3 Bucket

1. **Create a new S3 bucket with two folders:** `staging` and `datawarehouse`.
    - `staging/`: for uploading source data files
    - `datawarehouse/`: for storing transformed data

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1aABEeU4gas9zuKZvX_Fy0vDjZKUVmyOu" style="margin-top: 40px; margin-bottom: 40px;" />
</p>


2. **Upload Pre-Processed CSV Files to the staging folder**

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1R5V4jqvFPRN8GeDLja3JZAyLwnyU_gWy" style="margin-top: 40px; margin-bottom: 40px;" />
</p>


### Step 3: Setting Up AWS Glue for ETL

1. **Create a New Glue Job**
    - Navigate to the AWS Glue Console and select Visual ETL to create a visual workflow that transforms data from the staging folder and loads it into the datawarehouse folder.

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1UNk93GGM2SouQCjiTbFCJq-yxXToWK1n" style="margin-top: 40px; margin-bottom: 40px;" />
</p>

2. **Set Up Data Sources**
    - Since there are three source files, use three Amazon S3 source nodes.

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1f-RBuY_amXEzj2OTEqqhnXiTmYrx-goe" style="margin-top: 40px; margin-bottom: 40px;" />
</p>

 <p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1Tdm4OhUlVWNCamtBGDvZK0_t31Q8NJrd" style="margin-top: 40px; margin-bottom: 40px;" />
</p>

 <p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1_qrQiPvW5_3Ya-Lv0CbyNVYzUYcvmTuE" style="margin-top: 40px; margin-bottom: 40px;" />
</p>

3. **Configure Data Transformations:**
    - To join album and artist, click on the ADD symbol, select join from Transforms and connect nodes as per your workflow.

<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1J8OUlKj6NElw87eXJxPKdonbOMK34PG9" style="margin-top: 40px; margin-bottom: 40px;" />
</p>

-  Add a join condition where `artist.id = albums.artist_id` and rename this join as **"Join Album and Artist"**.
-  Add another **Join Transform** to join the **tracks S3 bucket** with the result of the previous join.
-  To drop unnecessary columns, select **Drop Fields** from the **Transforms** node.
   
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1nIXTlVuB89CUlrHsOx6JUP3pvaCiY6lC"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

    
4. **Set Up Data Target**
    - Add the destination as the Amazon S3 bucket in the Targets Section.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1AgrrvDYYaolodFla0icPo8PoGUkryNLI"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1W88pZfGZgrj3PMUksZ4K3M4NpV5gmkrA"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

    - Add the job name, select the IAM role created above, and save the visual ETL.
  
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1MBYyJ8GrISQs-vxTbc9rOI6XiH_v6Pvr"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />
      
5. **Run the Glue Job**
    - Click "Run job" to start the ETL process, transforming and moving data from the staging layer to the data warehouse.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1S_w-Kazg1nFx5_qxfO8cmvJJrJAoVlZz"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1tfKbH9-L1HlO2lQ71I_jaEai1kFXJV7t"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />
  
    - Check whether the transformed data is inside the datawarehouse folder.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1FogU4P7RWzbo2NFmaaflqcd4IMLvkNvc"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

### Step 4: Creating a Data Catalog with AWS Glue Crawler

1. **Create a New Database**
    - Go to AWS Glue > Data Catalog > Databases, and create a new database.
  
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1ybOS5lxl7yD9TK-Og-kWSOa4G3TEaXjX"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

2. **Create a New Crawler**
    - In the AWS Glue dashboard, go to the Data Catalog section and click on Crawlers, then click Create crawler.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1F3h-cKifWK7mfKZYz3zOdtG9eaetip3E"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />
  
    - Provide a Crawler Name.

 <p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=11d07m7fsssE6CoVNa_BJj67gvQR_8w2S"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

    - Add Data Store: Select S3 and provide the path to the datawarehouse folder.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1LHP_HtNi5cgZCQtmgp5HbgLBoGtQz1tk"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

  <p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1eoWxRvOwt_lm7wwdeB1dS2fW_4rJJjiI"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />
  
    - Provide the IAM Role created earlier.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=15_1lovaEK3jAZD7XyMgYYzWBqApI2yV0"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />
    - Select the created database.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1DEPNnMVUSB6-_tP-0_lk4mShhXf320M8"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

3. **Run the Crawler**
    - After setting up the crawler, click "Run crawler."
    - The crawler will scan the data in the datawarehouse folder and create corresponding tables in the spotify_database database.
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1PleXClvTvmZOqjUx_kLaE5rL3lia9NKb"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

    - Check the database tables.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1VqDT0FHuQGoDMelnPoi0t-OTOxZXmDZl"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1v6QEyGg31C7N0LV5nbRYwhhZQoHXfwVl"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />


### Step 5: Querying Data with AWS Athena

1. **Set Up Query Result Storage**
    - Navigate to AWS Athena from the AWS Management Console, then go to Launch query editor.
    - Before running any queries, specify a location for query results.
    - Create a new S3 bucket to store Athena query results.
      
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1D2CT2irK-NakPm1FAppgfk_21Ro-3Dh3"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

    - In the Athena settings, set the query result location to this new bucket.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1R7RiHV6cMzCiB24Hrvh7uKnDGcdvv6hH"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

2. **Write SQL Queries**
    - Select data source and database.
    - Now start querying the data.
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=17NF_lftFkRIWCR1yIEgTXM_qiouvvwJw"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1n5pqXVHWo0VqPkHT5EFm5x2hEKoAIvBv"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

### Step 6: Visualizing Data with AWS QuickSight

1. **Sign Up for AWS QuickSight**
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=10KrWVk27nUL0ryAVUWzXq73arCVvjTT5"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

2. **Connect QuickSight to Athena**
    - Once signed in, go to "Datasets" and click "New dataset."
    - Select "Athena" as the data source and give it a name.
      
<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1ptX1nWuUYckkX7KOyGRkEyLlmeJFG871"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

    - Choose the spotify_data database and the datawarehouse table.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1yhza-4_PTA5vlD9AjzW4lWWX0ldTrTkU"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />

3. **Create Visualizations**
    - After importing the data, you can create various types of visualizations (e.g., bar charts, line charts, pie charts) using the fields from the datawarehouse table.

<p align="center">
  <img 
    src="https://drive.google.com/uc?export=view&id=1cKCkU9MUDLNv85y6LfQUQqruYiO4oK3h"
    style="margin-top: 40px; margin-bottom: 40px;" 
  />
---

*Feel free to add screenshots or diagrams for each step to enhance clarity!*
