# Spring Boot API + MySQL using Docker Compose

A complete beginner-friendly Spring Boot project that runs a REST API and MySQL database together using Docker Compose.

## Requirements

- Docker Desktop (running)
- IntelliJ IDEA or Visual Studio Code (optional, for editing)
- Java 17 and Maven are only needed if you want to run/build the app outside Docker.

## Project structure

```text
spring-boot-docker-compose-task/
├── Dockerfile
├── docker-compose.yml
├── pom.xml
├── .dockerignore
├── .gitignore
└── src/
    └── main/
        ├── java/com/example/dockertask/
        │   ├── DockerComposeTaskApplication.java
        │   ├── controller/
        │   │   ├── HealthController.java
        │   │   └── ProductController.java
        │   ├── model/Product.java
        │   └── repository/ProductRepository.java
        └── resources/application.properties
```

## Run the complete task

1. Extract the ZIP file.
2. Open the extracted `spring-boot-docker-compose-task` folder in IntelliJ IDEA.
3. Start Docker Desktop and wait until Docker Engine is running.
4. Open the IntelliJ terminal in the project root.
5. Run:

   ```bash
   docker compose up --build
   ```

6. Wait until the logs show that Spring Boot has started on port 8080.
7. Open `http://localhost:8080/` or `http://localhost:8080/api/hello`.

To see running containers, open a second terminal and run:

```bash
docker ps
```

You should see `spring-boot-app` and `mysql-db`.

## API endpoints

| Method | URL | Purpose |
|---|---|---|
| GET | `http://localhost:8080/` | Application status |
| GET | `http://localhost:8080/api/hello` | Test API |
| GET | `http://localhost:8080/api/products` | List products |
| GET | `http://localhost:8080/api/products/{id}` | Get one product |
| POST | `http://localhost:8080/api/products` | Create product |
| PUT | `http://localhost:8080/api/products/{id}` | Update product |
| DELETE | `http://localhost:8080/api/products/{id}` | Delete product |

### Example POST request

In Postman, choose `POST` and enter:

`http://localhost:8080/api/products`

Set Body -> raw -> JSON:

```json
{
  "name": "Keyboard",
  "price": 799.00
}
```

The product is stored in MySQL.

## Useful Docker commands

```bash
# Start and build
docker compose up --build

# Run in background
docker compose up --build -d

# Show running containers
docker ps

# Follow application logs
docker compose logs -f app

# Follow MySQL logs
docker compose logs -f db

# Stop and remove containers (database volume is retained)
docker compose down

# Stop containers AND delete the database volume/data
docker compose down -v
```

## Important configuration

- Spring Boot runs on port `8080`.
- MySQL runs on port `3306`.
- Docker Compose service name for MySQL is `db`.
- Database name: `springdb`.
- Practice root password: `root123`.
- The MySQL health check makes the app wait until MySQL is ready.
- The named volume `mysql_data` preserves database data when containers are stopped and removed with `docker compose down`.

The credentials are for local learning only. Do not use them in production.

## Troubleshooting

- If Docker commands fail, start Docker Desktop and wait for the engine.
- If port 8080 or 3306 is already in use, stop the other service or change the host-side port mapping in `docker-compose.yml`.
- If you change MySQL credentials after the database volume has already been initialized, the old volume retains its original database credentials. For a fresh practice database, run `docker compose down -v` and then `docker compose up --build`.
- If the application fails, inspect `docker compose logs app` and `docker compose logs db`.

## GitHub upload

Create an empty GitHub repository, then run these commands from the project root (replace the URL):

```bash
git init
git add .
git commit -m "Complete Spring Boot Docker Compose task"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
git push -u origin main
```


## Verify JSON request logs with Docker Compose

The application writes one JSON log event for each HTTP request. The request event includes `event`, `method`, `path`, `status`, and `duration_ms`.

From the project root, run these commands:

```bash
docker compose down
docker compose build --no-cache app
docker compose up -d
docker compose ps
```

The REST resource in this project is `/api/products` (not `/api/books`). Make requests from PowerShell:

```powershell
curl.exe -i http://localhost:8080/api/products
curl.exe -i http://localhost:8080/api/hello
curl.exe -i http://localhost:8080/api/products/999
```

View the application container logs:

```bash
docker logs spring-boot-app
```

Follow logs live:

```bash
docker logs -f spring-boot-app
```

Save logs to a local file (PowerShell):

```powershell
docker logs spring-boot-app 2>&1 | Out-File -Encoding utf8 app-logs.json
```

Each request log should be a separate JSON line. Example (timestamp and duration will vary):

```json
{"@timestamp":"2026-11-09T03:30:00.000Z","level":"INFO","logger":"com.example.dockertask.config.RequestLoggingFilter","message":"HTTP request completed","event":"http_request","method":"GET","path":"/api/products","status":200,"duration_ms":18}
```
