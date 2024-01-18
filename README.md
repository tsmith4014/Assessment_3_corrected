# Project README: Dockerized Three-Tier Web Application

This README provides detailed instructions and code snippets for setting up and deploying a three-tier web application, consisting of a frontend, backend, and a PostgreSQL database, all containerized using Docker.

## Project Structure

- **Backend**: Node.js application.
- **Frontend**: React or similar web application.
- **Database**: PostgreSQL database.
- **Dockerfiles**: Separate Dockerfiles for frontend and backend.
- **Docker Compose**: Used to orchestrate all three services.

## Milestones Checklist

### Environment Variables

- Hardcoded values in both frontend and backend are replaced with environment variables for flexibility and security.

### Dockerfile Creation

- Dockerfiles are created for both frontend and backend.
- Appropriate base images are selected.
- Application code is copied into Docker images.
- Dependencies are installed.
- Ports required for communication are exposed.

### Docker Compose Configuration

- Docker Compose is used to define and run multi-container Docker applications.
- Services for the frontend, backend, and database are defined.
- Network communication between services is established using Docker networks.
- Volumes are used for data persistence in the database.

### Database Initialization

- An SQL script is used for initializing the database with a sample dataset.

## Building and Pushing Images

### Building Images

Use Docker Compose to build images:

```bash
docker-compose build
```

### Pushing Images to Docker Hub

1. Log in to Docker Hub:

   ```bash
   docker login --username <your-docker-hub-username>
   ```

2. Push the images:

   ```bash
   docker-compose push
   ```

### GitHub Actions CI/CD

A GitHub Actions workflow (`ci.yml`) is set up in the `.github/workflows` directory to automate the building and pushing of images to Docker Hub.

## Running the Application

1. **Start the Application**:

   ```bash
   docker-compose up -d
   ```

2. **Access the Application**:

   - Frontend: `http://localhost:<FRONTEND_PORT>`
   - Backend: `http://localhost:<BACKEND_PORT>/data`

3. **Stop the Application**:

   ```bash
   docker-compose down
   ```

## Dockerfiles

### Backend Dockerfile

```Dockerfile
# Dockerfile for backend
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
# Dockerfile for frontend
FROM node:12
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

## Docker Compose File

`docker-compose.yml`:

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

## SQL Script for Database Initialization

Example `init.sql`:

```sql
CREATE TABLE movie_hero (
  movie TEXT,
  hero TEXT
);
INSERT INTO movie_hero(movie, hero) VALUES ('Titanic', 'Leonardo DiCaprio');
```

## Evidence of Docker Hub Push

Ensure to capture logs or screenshots confirming the successful push of Docker images to Docker Hub.

## GitHub Actions CI YAML File

The `ci.yml` file in the `.github/workflows` directory:

```yaml
# GitHub Actions configuration
# ... [ci.yml content here] ...
```

---

Step 3: Configuring GitHub Secrets
In your GitHub repository:

Go to Settings > Secrets.
Click on New repository secret.
Add DOCKER_HUB_USERNAME as the name and your Docker Hub username (tsmith4014) as the value.
Add DOCKER_HUB_ACCESS_TOKEN as the name. Use a personal access token from Docker Hub (create this token from your Docker Hub account settings).

git add .
git commit -m "Setup monorepo with CI/CD"
git push origin main
