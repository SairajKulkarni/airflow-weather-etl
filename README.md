# **Weather ETL Pipeline - Apache Airflow**
Welcome to the **Weather ETL Pipeline**! This project automates data extraction from an API, transformation, and loading it into a **PostgreSQL database** using **Apache Airflow**.

---

## **Project Contents**

Your **Airflow** project contains the following files and directories:

- **`dags/`**  
  - Contains Python scripts for Airflow **DAGs**.  
  - Example DAG: `weather_etl_dag.py` fetches weather data and stores it in PostgreSQL.  

- **`Dockerfile`**  
  - Defines the Airflow environment using **Astronomer Runtime**.  

- **`include/`**  
  - Additional files (if required).  

- **`packages.txt`**  
  - Install OS-level dependencies here.  

- **`requirements.txt`**  
  - Define Python dependencies here.  

- **`plugins/`**  
  - Custom or community plugins.  

- **`airflow_settings.yaml`**  
  - Store Airflow **Connections**, **Variables**, and **Pools**.  

---

## **Setup Environment Variables (.env file)**

Create a `.env` file in your project root and add the following details:

```ini
POSTGRES_USER=airflow
POSTGRES_PASSWORD=airflow
POSTGRES_DB=airflow
POSTGRES_HOST=postgres
POSTGRES_PORT=5432
API_KEY=your_weather_api_key_here
