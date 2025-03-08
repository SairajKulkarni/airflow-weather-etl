# Weather ETL Pipeline - Apache Airflow

![Airflow](https://img.shields.io/badge/Apache_Airflow-017CEE?style=for-the-badge&logo=Apache-Airflow&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2CA5E0?style=for-the-badge&logo=docker&logoColor=white)

A robust ETL (Extract, Transform, Load) pipeline that automatically fetches weather data from Open-Meteo API, transforms it, and loads it into a PostgreSQL database. This project is orchestrated using Apache Airflow.

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Pipeline](#running-the-pipeline)
- [DAG Explanation](#dag-explanation)
- [Database Schema](#database-schema)
- [Customization](#customization)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project demonstrates a practical implementation of a data pipeline using Apache Airflow. The pipeline:

1. **Extracts** weather data for Bhopal, India (configurable to any location) from Open-Meteo API
2. **Transforms** the raw data into a structured format
3. **Loads** the processed data into a PostgreSQL database for storage and analysis

The entire process is containerized using Docker, making deployment simple across different environments.

## Project Structure

```
airflow-weather-etl/
├── dags/
│   └── weather_etl_dag.py       # Main Airflow DAG for the ETL pipeline
├── include/                     # Additional files (if required)
├── plugins/                     # Custom or community plugins
├── .env                         # Environment variables (create this file)
├── .gitignore                   # Git ignore file
├── Dockerfile                   # Defines Airflow environment
├── docker-compose.yml           # Defines multi-container Docker setup
├── airflow_settings.yaml        # Airflow settings configuration
├── packages.txt                 # OS-level dependencies
├── requirements.txt             # Python dependencies
└── README.md                    # Project documentation
```

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/)
- [Git](https://git-scm.com/downloads)
- Access to [Open-Meteo API](https://open-meteo.com/) (free tier available)

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/SairajKulkarni/airflow-weather-etl.git
   cd airflow-weather-etl
   ```

2. Create the `.env` file with your configuration:
   ```bash
   cp .env.example .env
   # Edit the .env file with your credentials
   ```

3. Build and start the containers:
   ```bash
   docker-compose up -d
   ```

## Configuration

### Environment Variables

Create a `.env` file in the project root directory with the following variables:

```ini
# PostgreSQL Configuration
POSTGRES_USER=airflow
POSTGRES_PASSWORD=airflow
POSTGRES_DB=airflow
POSTGRES_HOST=postgres
POSTGRES_PORT=5432

# API Configuration
API_KEY=your_weather_api_key_here
```

### Airflow Connections

The DAG requires two Airflow connections:

1. **postgres_default**: Connection to the PostgreSQL database
   - Conn Type: Postgres
   - Host: postgres
   - Schema: airflow
   - Login: airflow
   - Password: airflow
   - Port: 5432

2. **open_meteo_api**: Connection to the Open-Meteo API
   - Conn Type: HTTP
   - Host: api.open-meteo.com
   - Schema: https

You can set these up via the Airflow UI or using the `airflow_settings.yaml` file.

## Running the Pipeline

1. Access the Airflow UI at http://localhost:8080 (default credentials: airflow/airflow)
2. Enable the `weather_etl_pipeline` DAG
3. The DAG will run daily by default, but you can trigger it manually for immediate execution

## DAG Explanation

The `weather_etl_pipeline` DAG consists of three main tasks:

### 1. Extract Weather Data (`extract_weather_data`)
- Uses an HTTP hook to connect to the Open-Meteo API
- Fetches current weather data for the specified location (Bhopal, India by default)
- Returns the raw JSON response

### 2. Transform Weather Data (`transform_weather_data`)
- Processes the raw API response
- Extracts relevant weather information (temperature, wind speed, etc.)
- Structures the data for database insertion

### 3. Load Weather Data (`load_weather_data`)
- Creates the database table if it doesn't exist
- Inserts the transformed data into the PostgreSQL database
- Appends a timestamp for historical tracking

## Database Schema

The weather data is stored in a table called `weather_data` with the following schema:

```sql
CREATE TABLE IF NOT EXISTS weather_data (
    latitude FLOAT,
    longitude FLOAT,
    temperature FLOAT,
    windspeed FLOAT,
    winddirection FLOAT,
    weathercode INT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Customization

### Changing Location

To fetch weather data for a different location, modify the `LATITUDE` and `LONGITUDE` variables in the `weather_etl_dag.py` file:

```python
# Change these values for your desired location
LATITUDE = '23.2599'  # Bhopal, India
LONGITUDE = '77.4126'
```

### Modifying Schedule

The DAG is configured to run daily. To change the schedule, modify the `schedule_interval` parameter in the DAG definition:

```python
with DAG(
    dag_id='weather_etl_pipeline',
    default_args=default_args,
    schedule_interval='@daily',  # Change this to your desired schedule
    catchup=False,
) as dag:
```

Common options include:
- `@hourly` - Run once an hour
- `@daily` - Run once a day
- `@weekly` - Run once a week
- `@monthly` - Run once a month
- Cron expressions (e.g., `'0 0 * * *'` for daily at midnight)

## Troubleshooting

### Common Issues

1. **Database Connection Errors**
   - Ensure PostgreSQL container is running: `docker ps`
   - Verify database credentials in the `.env` file
   - Check the Airflow connection configuration

2. **API Connection Issues**
   - Confirm internet connectivity
   - Verify the API connection settings in Airflow
   - Check if the API has usage limits or requires authentication

3. **Docker-related Problems**
   - Ensure Docker is running
   - Try rebuilding containers: `docker-compose down && docker-compose up -d --build`
   - Check container logs: `docker-compose logs airflow-webserver`

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


Created by [Sairaj Kulkarni](https://github.com/SairajKulkarni)
