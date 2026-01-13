# Data Pipeline Engine - Docker Edition

A containerized data pipeline application for processing and ingesting taxi data into PostgreSQL. Built with Python, Docker, and Docker Compose for streamlined ETL workflows.

## Overview

This project provides a complete data engineering infrastructure for:
- **Data Ingestion**: Automated ingestion of taxi trip data with validation and type coercion
- **Data Processing**: Pipeline scripts for transforming and enriching data
- **Database Storage**: PostgreSQL integration for persistent data storage
- **Monitoring & Administration**: PgAdmin UI for database management

## Quick Start

### Prerequisites
- Docker & Docker Compose installed
- Python 3.13+ (for local development)
- Git

### Setup & Run

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd docker-dateengine-Zoomcompany
   ```

2. **Start services with Docker Compose**
   ```bash
   cd pipeline
   docker-compose up -d
   ```
   
   This will start:
   - **PostgreSQL database** (port 5432)
   - **PgAdmin** (port 8085)

3. **Access the services**
   - PgAdmin: http://localhost:8085
     - Email: `admin@admin.com`
     - Password: `root`
   - PostgreSQL: `localhost:5432`
     - User: `root`
     - Password: `root`
     - Database: `ny_taxi`

## Project Structure

```
├── pipeline/
│   ├── Dockerfile           # Container image definition
│   ├── docker-compose.yaml  # Multi-container orchestration
│   ├── pyproject.toml       # Python project dependencies
│   ├── main.py              # Entry point
│   ├── pipeline.py          # Data processing pipeline
│   ├── ingest_data.py       # Data ingestion script
│   └── notebook.ipynb       # Jupyter notebook for exploration
├── test/                    # Test files and utilities
└── README.md
```

## Key Components

### Data Ingestion (`ingest_data.py`)
Reads taxi trip data and loads it into PostgreSQL with:
- Automatic data type validation
- DateTime parsing for trip timestamps
- Progress tracking with tqdm
- Batch processing support

**Supported Data Types:**
- Numeric: `VendorID`, `passenger_count`, `trip_distance`, etc.
- DateTime: `tpep_pickup_datetime`, `tpep_dropoff_datetime`
- String: `store_and_fwd_flag`

### Pipeline Processing (`pipeline.py`)
Processes monthly data with argument-based configuration:
```bash
python pipeline.py <month>
```
- Outputs processed data as Parquet files
- Month-based file naming

### Main Entry Point (`main.py`)
Bootstrap script for container execution.

## Dependencies

Core dependencies (see `pyproject.toml`):
- **pandas** ≥2.3.3 - Data manipulation
- **sqlalchemy** ≥2.0.45 - Database ORM
- **psycopg2-binary** ≥2.9.11 - PostgreSQL adapter
- **pyarrow** ≥22.0.0 - Parquet file support
- **click** ≥8.0.0 - CLI framework
- **tqdm** ≥4.67.1 - Progress bars

Development dependencies:
- **jupyter** - Interactive notebooks
- **pgcli** - PostgreSQL CLI client

## Development Setup

### Install with UV (recommended)
```bash
cd pipeline
uv sync
```

### Or with pip
```bash
cd pipeline
pip install -e .
```

### Run Jupyter Notebook
```bash
jupyter notebook pipeline/notebook.ipynb
```

## Docker Build & Run

### Build Custom Image
```bash
cd pipeline
docker build -t data-pipeline:latest .
```

### Run Pipeline Container
```bash
docker run -e POSTGRES_HOST=pgdatabase \
           -e POSTGRES_USER=root \
           -e POSTGRES_PASSWORD=root \
           data-pipeline:latest
```

## Environment Variables

Configure these when running containers:
- `POSTGRES_USER` - Database user (default: `root`)
- `POSTGRES_PASSWORD` - Database password (default: `root`)
- `POSTGRES_DB` - Database name (default: `ny_taxi`)

## Health Checks

The PostgreSQL service includes automated health checks:
- **Test**: Runs `pg_isready -U root`
- **Interval**: 10 seconds
- **Timeout**: 5 seconds
- **Retries**: 5 attempts

PgAdmin waits for PostgreSQL to be healthy before starting.

## Common Tasks

### View Logs
```bash
docker-compose logs -f pgdatabase
docker-compose logs -f pgadmin
```

### Stop All Services
```bash
docker-compose down
```

### Clean Up (Remove Data)
```bash
docker-compose down -v
```

### Connect to Database Directly
```bash
psql -h localhost -U root -d ny_taxi
```

## File Format Support

- **Input**: CSV data files
- **Output**: Parquet files (efficient columnar format)
- **Database**: PostgreSQL tables

## Troubleshooting

**PostgreSQL connection refused**
- Ensure PostgreSQL container is running: `docker-compose ps`
- Check health: `docker-compose ps` (should show "healthy")
- Wait 30 seconds for service to fully start

**PgAdmin login issues**
- Default credentials: `admin@admin.com` / `root`
- Clear browser cache and try again

**Docker image build fails**
- Ensure all dependency versions are compatible
- Check internet connection for package downloads
- Review `pyproject.toml` for conflicting requirements

## Contributing

1. Create a feature branch
2. Make changes to relevant files
3. Test with `docker-compose up`
4. Submit a pull request

## License

[Add your license information here]
