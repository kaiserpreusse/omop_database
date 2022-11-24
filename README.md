# Simple Postgres for OMOP
The goal of this project is to collect resources to set up a Postgres database
with the OMOP Common Data Model for testing purposes.

Database setup has to be done manually but this guide facilitates the process.

> :point_right: See this project to load some synthetic health data to the OMOP CDM database: https://github.com/kaiserpreusse/synthea2omop

## Basic steps

  **Once**:

- [Download the OHDSI standardized vocabularies ](#download-vocabularies) (only once, keep the dataset)

**For every Postgres OMOP CDM database**

1. [Run the database in Docker](#run-the-database-in-docker)
2. [Connect to the database and create OMOP CDM tables](#connect-to-database-and-create-omop-cdm)
3. [Load the vocabularies into the OMOP CDM tables](#load-vocabularies)
4. [Create indexes and constraints](#create-indexes-and-constraints)

### Download vocabularies
The vocabulary download has to be done only once, the vocabularies can be reused when setting up new OMOP databases.
Keeping a single vocabulary set makes testing easier.

- create an account on https://athena.ohdsi.org
- go to the downloads section (https://athena.ohdsi.org/vocabulary/list)
- select the vocabularies required and click `Download vocabularies`
- the vocabulary download is prepared and the download link is available
  a few minutes later (sent by email and available at https://athena.ohdsi.org/vocabulary/download-history)
- download and unpack the vocabularies
- if CPT4 was included, process CPT4 as described in the CPT4 documentation included in the vocabulary download
- **store vocabulary files in `./vocabularies`**, this directory is mounted into the Postgres Docker container

### Run the database in Docker
- the `docker-compose.yml` file runs a standard Postgres database
- configuration is done with a `.env` file, see `example.env` for the ENV values

```
POSTGRES_PASSWORD=test
POSTGRES_DB=omop
POSTGRES_USER=postgres
```

- the local directories `./vocabularies` and `./data` are mapped to the container
- `./vocabularies` should contain the vocabulary files
- `./data` contains the Postgres data

### Connect to database and create OMOP CDM
- database setup is done with SQL scripts
- scripts for different versions of the OMOP CDM are available at `scripts/version`
- use any database manager to connect to the Postgres database
- create a database and schema to hold the OMOP CDM
- use the `_ddl.sql` script to create OMOP CDM tables in the schema

### Load vocabularies
- make sure the vocabularies are stored in `./vocabularies`
- use any database manager to connect to the Postgres database
- use the `_load_vocabularies.sql` script in `scripts/version` to load the vocabulary tables

### Create indexes and constraints
- use any database manager to connect to the Postgres database
- run the following SQL scripts in `scripts/version`:
  - `_primary_keys.sql`
  - `_indices.sql`
  - `_constraints.sql`
