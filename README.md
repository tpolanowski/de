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
http://localhost:8080/browser/ -> db:5342
