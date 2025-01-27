# DE

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

