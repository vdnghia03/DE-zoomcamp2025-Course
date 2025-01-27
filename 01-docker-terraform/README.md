
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
![Alt text](/01-docker-terraform/image/conect_postgres.png "Optional title")
