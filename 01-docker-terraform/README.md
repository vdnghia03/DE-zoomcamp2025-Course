
# Environment: github codespace have available docker and docker-compose


## Question 1. Understanding docker first run

```bash
    docker run -it --entrypoint bash python:3.12.8

    pip --version
```
![Alt text](/01-docker-terraform/image/pip_version.png "Optional title")

Result: 24.3.1


## Question 2. Understanding Docker networking and docker-compose

```yaml
    services:
    db:
        container_name: postgres
        image: postgres:17-alpine
        environment:
        POSTGRES_USER: 'postgres'
        POSTGRES_PASSWORD: 'postgres'
        POSTGRES_DB: 'ny_taxi'
        ports:
        - '5433:5432'
        volumes:
        - vol-pgdata:/var/lib/postgresql/data

    pgadmin:
        container_name: pgadmin
        image: dpage/pgadmin4:latest
        environment:
        PGADMIN_DEFAULT_EMAIL: "pgadmin@pgadmin.com"
        PGADMIN_DEFAULT_PASSWORD: "pgadmin"
        ports:
        - "8080:80"
        volumes:
        - vol-pgadmin_data:/var/lib/pgadmin  

    volumes:
    vol-pgdata:
        name: vol-pgdata
    vol-pgadmin_data:
        name: vol-pgadmin_data
```

- docker compose up
- http://localhost:8080

![Alt text](/01-docker-terraform/image/conect_postgres.png "Optional title")


Result: hostname: db and port: 5432

## Prepare postgres

```shell
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv
```

```shell
URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz"

docker run -it \
  --network=01-docker-terraform_default \
  taxi_ingest:v003 \
    --user=postgres \
    --password=postgres \
    --host=db \
    --port=5432 \
    --db=ny_taxi \
    --table_name=green_taxi_trips \
    --url=${URL}
```

```shell
URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv"

docker run -it \
  --network=01-docker-terraform_default \
  taxi_ingest:v003 \
    --user=postgres \
    --password=postgres \
    --host=db \
    --port=5432 \
    --db=ny_taxi \
    --table_name=taxi_zone \
    --url=${URL}
```


## Question 3. Trip Segmentation Count

```sql
SELECT
    CASE
        WHEN trip_distance <= 1 THEN 'Up to 1 miles'
        WHEN trip_distance > 1 AND trip_distance <= 3 THEN 'Between 1 and 3 miles'
        WHEN trip_distance > 3 AND trip_distance <= 7 THEN 'Between 3 and 7 miles'
        WHEN trip_distance > 7 AND trip_distance <= 10 THEN 'Between 7 and 10 miles'
        ELSE 'Over 10 miles'
    END AS trip_segment
    , COUNT(*) AS count_segment
FROM public.green_taxi_trips
WHERE
    lpep_pickup_datetime >= '2019-10-01' AND lpep_pickup_datetime < '2019-11-01'
GROUP BY
	trip_segment
```

![Alt text](/01-docker-terraform/image/answer3_1.png "Optional title")


