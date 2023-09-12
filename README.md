# NimbleG API Project: Employee Insights

Due to some restrictions I only was able to upload my answer as a .zip file

## Overview

The main goal of this project is to process the CES time series dataset, store it in a PostgreSQL database, and expose relevant data points through a REST API using PostgREST. 

The data should be used to answer two questions by the corresponding team:

1. How has the evolution of women in government roles changed over time?
2. How has the ratio of "production employees to supervisory employees" evolved over time?

## Expected Output

For the "Women in Government" endpoint (`/women-in-government`), the expected output is an array of records detailing the count of women in government roles by date. An example snippet:

```bash
curl http://localhost:3000/women_in_government
```

That should return:

```json
{
   "result": [ 
          {"date": "January 1964", "valueInThousands": 200},
          ...
          {"date": "April 2022", "valueInThousands": 200}
    ]
}
```

## How to run

1. Start you Postgres database `sudo service postgresql start`
2. Execute `./scripts/main.sh`
3. Launch the API service `postgrest ./connection_file.conf`
4. Query API for women data `curl http://localhost:3000/women_in_government`
5. Query API for employee type ratio data `curl http://localhost:3000/employee_type_ratio`

When you execute `./scripts/main.sh`, the following actions take place:

1. **Directory & Config Setup**: Organizational directories and necessary configuration files are created.
2. **Data Processing**: Raw CES data is cleaned and transformed for database insertion.
3. **Database Creation** & Setup: A PostgreSQL database `nimbleg` is initialized with a schema `api`. Under such schema we have some tables:
    - `women_in_government` Data that answers question 1.
    - `employee_type_ratio` Data that answers question 2.
    - `ces` Processed time series directly from ce.data.0.AllCESSeries that contains only data type codes 01, 06 and 10 for every supersector and industry.
    - `period` Period attributes.
4. **Data Insertion**: Processed data is stored in the PostgreSQL tables.
5. **PostgREST Installation**: The tool that serves our REST API is set up and configured.


## Script Logic

1. **main.sh**
    - This is the orchestrator script. Running this script sets up the necessary directory structures, invokes the other scripts in sequence, and provides the user with guidance on how to consume the exposed API.

2. **data_cleaning.sh**
    - Responsible for pre-processing raw data files. Cleaning includes removing any undesired data, formatting data correctly, and structuring it for easy database insertion.

3. **database_setup.sh**
    - Initiates the PostgreSQL database, creates necessary tables, views, and other DB structures to store and retrieve the cleaned data.

4. **table_filling.sh**
    - Populates the PostgreSQL tables with the cleaned and processed data from the processed_data directory.
    
5. **api_installation.sh**
    - Downloads and installs PostgREST.


## Directory Structure

```
.
│
├── scripts/                    # Main directory for all script files
|   ├── ces_processing/         # Ces data processing scripts
|   ├── other/                  # Other scripts
│   ├── data_cleaning.sh        # Handles data cleaning operations
│   ├── database_setup.sh       # Sets up PostgreSQL database elements
│   ├── table_filling.sh        # Fills database tables with cleaned data
│   ├── api_installation.sh     # Downloads, installs, and sets up PostgREST
│   └── main.sh                 # Main script, orchestrating the execution of other scripts
│
├── processed_data/             # Processed data after cleaning and transformation (created during execution)
│   ├── ces_data_batches/       # Batched data files ready for database insertion
│   └── attributes_data/        # Processed attributes data 
│
├── raw_data/                   # Raw data, as fetched from the source
│   ├── ce.data.0.AllCESSeries  # Manually download this file before running ./scripts/main.sh
│   └── ce.period               # Manually download this file before running ./scripts/main.sh
|
└── connection_file.conf        # Used for launching the API service (created during execution)

```


## Datasets and Resources

- Main Dataset [ce.data.0.AllCESSeries](https://download.bls.gov/pub/time.series/ce/ce.data.0.AllCESSeries)
- Attribute Dataset [ce.period](https://download.bls.gov/pub/time.series/ce/ce.data.0.AllCESSeries)
- [Dataset Explanation](https://download.bls.gov/pub/time.series/ce/ce.txt)
- [Sector Codes](https://download.bls.gov/pub/time.series/ce/ce.supersector)
- [Data Codes](https://download.bls.gov/pub/time.series/ce/ce.datatype)
- [Series IDs Explanation](https://download.bls.gov/pub/time.series/ce/ce.series)

## Considerations

- It was only tested on UBUNTU 20.4


