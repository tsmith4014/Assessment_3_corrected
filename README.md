# Project README: Dockerized Three-Tier Web Application

This README documents the process of setting up, deploying, and managing a dockerized three-tier web application, consisting of a frontend, a backend, and a PostgreSQL database. It includes steps for Dockerization, environment variable management, Docker Compose configuration, database initialization, and automated image building and pushing using GitHub Actions.

## Project Structure

- **Backend**: A Node.js application serving as the API layer.
- **Frontend**: A web application built with technologies like React.
- **Database**: A PostgreSQL database for data persistence.
- **Dockerfiles**: Separate Dockerfiles for the frontend and backend.
- **Docker Compose**: A tool used to define and run multi-container Docker applications.
- **GitHub Actions**: For continuous integration and deployment.

## Milestones Checklist

1. **Code Analysis and Environment Variables:**

   - [x] Identified and replaced hardcoded values with environment variables.

2. **Dockerfile Creation:**

   - [x] Created Dockerfiles for frontend and backend.
   - [x] Selected appropriate base images.
   - [x] Copied application code into Docker images.
   - [x] Installed dependencies within the Docker images.
   - [x] Exposed necessary ports for communication.

3. **Docker Compose Configuration:**

   - [x] Defined services for frontend, backend, and database in `docker-compose.yml`.
   - [x] Established network communication between services.
   - [x] Configured volumes for data persistence, especially for the database.

4. **Database Initialization:**

   - [x] Executed an SQL script for dataset initialization within the database container.

5. **Image Building and Pushing:**
   - [x] Built Docker images for frontend and backend using `docker-compose build`.
   - [x] Pushed images to Docker Hub using `docker-compose push`.
   - [x] Set up Docker image build and push using GitHub Actions CI.

## Building and Pushing Images

### Manual Process

1. **Build Images:**

   ```bash
   docker-compose build
   ```

2. **Push Images to Docker Hub:**
   First, log in to Docker Hub:
   ```bash
   docker login --username your-username
   ```
   Then, push the images:
   ```bash
   docker-compose push
   ```

### Automated Process with GitHub Actions

- Set up a CI/CD pipeline in `.github/workflows/ci.yml` to automatically build and push images upon code pushes to the repository.

## Running the Application

1. **Start the Application:**

   ```bash
   docker-compose up -d
   ```

2. **Access the Application:**

   - Frontend at `http://localhost:3000`
   - Backend at `http://localhost:3001/data`

3. **Stop the Application:**
   ```bash
   docker-compose down
   ```

## Dockerfiles

### Backend Dockerfile

```Dockerfile
FROM node:12
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3001
CMD ["node", "index.js"]
```

### Frontend Dockerfile

```Dockerfile
FROM node:12
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

## Docker Compose File (`docker-compose.yml`)

```yaml
version: "3.8"
services:
  db:
    image: postgres:11.2-alpine
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "${DB_PORT}:5432"
    volumes:
      - db-data:/var/lib/postgresql/data
      - ./init_sql_scripts:/docker-entrypoint-initdb.d
  backend:
    build: ./backend
    ports:
      - "${PORT_BACKEND}:3001"
    environment:
      DATABASE_URL: postgres://${DB_USER}:${DB_PASSWORD}@db:${DB_PORT}/${DB_NAME}
      PORT: ${PORT_BACKEND}
    depends_on:
      - db
  frontend:
    build: ./frontend
    ports:
      - "${PORT_FRONTEND}:3000"
    environment:
      REST_API_URL: http://backend:${PORT_BACKEND}/data
      PORT: ${PORT_FRONTEND}
    depends_on:
      - backend
volumes:
  db-data:
```

## SQL Script for Database Initialization (`init.sql`)

```sql
CREATE TABLE movie_hero (
  movie TEXT,
  hero TEXT
);
INSERT INTO movie_hero(movie, hero) VALUES ('Titanic', 'Leonardo DiCaprio');
```

## Evidence of Docker Hub Push

- Ensure to capture logs or screenshots confirming the successful push of Docker images to Docker Hub.

## GitHub Actions CI YAML

File (`ci.yml`)

- GitHub Actions configuration for automated build and push (see `.github/workflows/ci.yml` in the repository).

---

## Environment Variables

- Ensure environment variables are correctly set in `docker-compose.yml` and used in Dockerfiles and application code.

## Documentation

- Detailed documentation is provided for each step of the setup and deployment process.
- Comments included in code to explain the purpose of environment variables and Dockerfile instructions.

---
