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
SELECT count(*) FROM public.yellow_taxi_trips
WHERE DATE(lpep_pickup_datetime) = '2019-09-18'
  AND DATE(lpep_dropoff_datetime) = '2019-09-18';
```
15612

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
2019-09-26

## Question 5
```sql
SELECT 
    z."Borough",
    SUM(tt.total_amount) AS total_borough_amount
FROM public.yellow_taxi_trips tt
JOIN zones z
    ON tt."PULocationID" = z."LocationID"  -- assuming pickup_zone_id maps to zone_id in zones table
WHERE DATE(tt.lpep_pickup_datetime) = '2019-09-18'
  AND z."Borough" != 'Unknown'
GROUP BY z."Borough"
HAVING SUM(tt.total_amount) > 50000
ORDER BY total_borough_amount DESC
LIMIT 3;
```
"Brooklyn" "Manhattan" "Queens"

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
WHERE DATE(tt.lpep_pickup_datetime) BETWEEN '2019-09-01' AND '2019-09-30'
  AND z_pickup."Zone" = 'Astoria'
GROUP BY z_drop."Borough", z_drop."Zone"
ORDER BY max_tip_amount DESC
LIMIT 1;
```
JFK Airport

## Question 7
```
```

