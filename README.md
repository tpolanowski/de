# DE lab 1

## Question 1
```bash
t@t 2_DOCKER_SQL % docker run -it --entrypoint bash python:3.12.8
Unable to find image 'python:3.12.8' locally
3.12.8: Pulling from library/python
86f9cc2995ad: Download complete 
c80762877ac5: Download complete 
Digest: sha256:2e726959b8df5cd9fd95a4cbd6dcd23d8a89e750e9c2c5dc077ba56365c6a925
Status: Downloaded newer image for python:3.12.8
root@88f2b827282d:/# pip --version
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)
```

## Question 2
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
docker compose up
http://localhost:8080/browser/ 
db:5342

## Question 3
```sql
SELECT 
    count(*)
FROM public.yellow_taxi_trips
WHERE DATE(lpep_pickup_datetime) >= '2019-10-01'
  AND DATE(lpep_pickup_datetime) < '2019-11-01'
  AND trip_distance <= 1;

SELECT 
    count(*)
FROM public.yellow_taxi_trips
WHERE DATE(lpep_pickup_datetime) >= '2019-10-01'
  AND DATE(lpep_pickup_datetime) < '2019-11-01'
  AND trip_distance > 1
  AND trip_distance <= 3;

...
```
104,802; 198,924; 109,603; 27,678; 35,189

## Question 4
```sql
SELECT 
    DATE(lpep_pickup_datetime) AS pickup_day,
    MAX(trip_distance) AS max_trip_distance
FROM public.yellow_taxi_trips
GROUP BY DATE(lpep_pickup_datetime)
ORDER BY max_trip_distance DESC
LIMIT 1;
```
2019-10-31

## Question 5
```sql
SELECT 
    z."Zone",
    SUM(tt.total_amount) AS total_borough_amount
FROM public.yellow_taxi_trips tt
JOIN zones z
    ON tt."PULocationID" = z."LocationID" 
WHERE DATE(tt.lpep_pickup_datetime) = '2019-10-18'
GROUP BY z."Zone"
HAVING SUM(tt.total_amount) > 13000
ORDER BY total_borough_amount DESC
```
East Harlem North, East Harlem South, Morningside Heights

## Question 6
```sql
SELECT 
    z_drop."Zone" AS dropoff_zone_name,
    MAX(tt.tip_amount) AS max_tip_amount
FROM public.yellow_taxi_trips tt
JOIN zones z_pickup
    ON tt."PULocationID" = z_pickup."LocationID" 
JOIN zones z_drop
    ON tt."DOLocationID" = z_drop."LocationID" 
WHERE DATE(tt.lpep_pickup_datetime) BETWEEN '2019-10-01' AND '2019-10-31'
  AND z_pickup."Zone" = 'East Harlem North'
GROUP BY z_drop."Borough", z_drop."Zone"
ORDER BY max_tip_amount DESC
```
JFK Airport

## Question 7
```
terraform init, terraform apply -auto-approve, terraform destroy
```

# DE lab 2
docker-compose.yml
```docker
volumes:
  postgres-data:
    driver: local
  kestra-data:
    driver: local
  zoomcamp-data:
    driver: local

services:
  postgres:
    image: postgres
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: kestra
      POSTGRES_USER: kestra
      POSTGRES_PASSWORD: k3str4
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}"]
      interval: 30s
      timeout: 10s
      retries: 10

  kestra:
    image: kestra/kestra:latest
    pull_policy: always
    # Note that this setup with a root user is intended for development purpose.
    # Our base image runs without root, but the Docker Compose implementation needs root to access the Docker socket
    # To run Kestra in a rootless mode in production, see: https://kestra.io/docs/installation/podman-compose
    user: "root"
    command: server standalone
    volumes:
      - kestra-data:/app/storage
      - /var/run/docker.sock:/var/run/docker.sock
      - /tmp/kestra-wd:/tmp/kestra-wd
    environment:
      KESTRA_CONFIGURATION: |
        datasources:
          postgres:
            url: jdbc:postgresql://postgres:5432/kestra
            driverClassName: org.postgresql.Driver
            username: kestra
            password: k3str4
        kestra:
          server:
            basicAuth:
              enabled: false
              username: "admin@kestra.io" # it must be a valid email address
              password: kestra
          repository:
            type: postgres
          storage:
            type: local
            local:
              basePath: "/app/storage"
          queue:
            type: postgres
          tasks:
            tmpDir:
              path: /tmp/kestra-wd/tmp
          url: http://localhost:8080/
    ports:
      - "8080:8080"
      - "8081:8081"
    depends_on:
      postgres:
        condition: service_started
    
  postgres_zoomcamp:
    image: postgres
    environment:
      POSTGRES_USER: kestra
      POSTGRES_PASSWORD: k3str4
      POSTGRES_DB: postgres-zoomcamp
    ports:
      - "5432:5432"
    volumes:
      - zoomcamp-data:/var/lib/postgresql/data
    depends_on:
      kestra:
        condition: service_started

  pgadmin:
    image: dpage/pgadmin4
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@admin.com
      - PGADMIN_DEFAULT_PASSWORD=root
    ports:
      - "8085:80"
    depends_on:
      postgres_zoomcamp:
        condition: service_started
```


## Question 1
yellow-tripdata_2020-12.csv 128.3 MiB

## Question 2
green_tripdata_2020-04.csv

## Question 3
24,648,499

## Question 4
1,734,051

## Question 5
1,925,152

## Question 6
America/New_York



