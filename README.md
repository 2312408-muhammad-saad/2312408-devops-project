# 2312408-devops-project

## Student Details

Name: Muhammad Saad  
Registration Number: 2312408  
GitHub Username: 2312408-muhammad-saad  
GitHub Account Email: 2312408.muhammadsaad@gmail.com  
Git Author Email for this project: 2312408.muhammadsaad+2312408@gmail.com

## Project Title

Containerised FastAPI Microservice with PostgreSQL, Docker Compose,
GitHub Actions CI/CD, and AWS EC2 deployment.

## Architecture

The system runs two Docker containers orchestrated by Docker Compose: a FastAPI +
Uvicorn web service on port 8000 and a PostgreSQL 15 database on port 5432.
The web service stores student records in PostgreSQL through a named volume, so the
data persists across container restarts and redeployments.

## What This Project Does

This project runs a FastAPI web service and a PostgreSQL database in
containers. The FastAPI service stores student records in PostgreSQL. The
project also includes automated CI/CD using GitHub Actions.

When code is pushed to GitHub:

1. GitHub Actions checks out the code.
2. Python 3.12 is installed on a fresh Ubuntu runner.
3. Dependencies are installed from `requirements.txt`.
4. `flake8` checks the code style.
5. `pytest` runs the API tests.
6. On push to `main`, the Docker image is built and pushed to Docker Hub.
7. The deployment job SSHs into EC2 and restarts the live container.

## How to Run Locally

Build the images and start both containers with a single command:

```bash
docker compose up --build
```

The API is then available at `http://localhost:8000` and the interactive docs at
`http://localhost:8000/docs`.

## Application Architecture

Browser or FastAPI Docs  
-> FastAPI web container on port 8000  
-> PostgreSQL database container on port 5432  
-> Docker named volume `postgres_data` keeps database records persistent

Services:

- `web`: FastAPI + Uvicorn application
- `db`: PostgreSQL 15 database
- `postgres_data`: named volume for database persistence

## API Endpoints Reference

| Method | Endpoint            | Description                                  |
| ------ | ------------------- | -------------------------------------------- |
| GET    | `/health`           | Returns service status and DB connection     |
| POST   | `/students`         | Creates a new student record                 |
| GET    | `/students`         | Returns all student records                  |
| GET    | `/students/{reg_no}`| Returns a single student by registration no. |

## API Endpoints

### GET `/health`

Expected response:

```json
{
  "status": "ok",
  "db": "connected",
  "student": "2312408"
}
```

### POST `/students`

Open `http://localhost:8000/docs` and use the POST `/students` endpoint.

Example JSON body:

```json
{
  "name": "Muhammad Saad",
  "reg_no": "2312408",
  "email": "2312408.muhammadsaad@gmail.com",
  "course": "DevOps Fundamentals"
}
```

### GET `/students`

Returns all student records from PostgreSQL.

### GET `/students/{reg_no}`

Example:

```text
/students/2312408
```

Returns one student record or 404 if not found.

## Project Structure

```text
2312408-devops-project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── database.py
│   ├── models.py
│   └── tests/
│       ├── conftest.py
│       ├── test_health.py
│       └── test_students.py
├── Dockerfile
├── docker-compose.yml
├── docker-compose.prod.yml
├── requirements.txt
├── .env.example
├── .gitignore
├── .dockerignore
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── cd.yml
└── README.md
```

## Local Setup with Docker Compose

Run the project locally:

```bash
docker compose up --build
```

Open in browser:

```text
http://localhost:8000
http://localhost:8000/health
http://localhost:8000/docs
```

Check running services:

```bash
docker compose ps
```

View web logs:

```bash
docker compose logs web
```

Open PostgreSQL shell:

```bash
docker compose exec db psql -U devops -d appdb
```

Useful PostgreSQL commands inside `psql`:

```text
\l
\dt
SELECT * FROM students;
\q
```

Stop the containers:

```bash
docker compose down
```

Stop containers and delete the volume:

```bash
docker compose down -v
```

## Local Testing

Install dependencies:

```bash
pip install -r requirements.txt
```

Run linting:

```bash
flake8 app/ --max-line-length=88
```

Run tests:

```bash
pytest app/tests/ -v
```

## Git Setup

Set your Git username and project author email:

```bash
git config user.name "Muhammad Saad"
git config user.email "2312408.muhammadsaad+2312408@gmail.com"
```

Start repository and push to GitHub:

```bash
git init
git add .
git commit -m 'feat: add FastAPI PostgreSQL DevOps project'
git remote add origin https://github.com/2312408-muhammad-saad/2312408-devops-project.git
git branch -M main
git push -u origin main
```

## Suggested Git Workflow for Marks

The project requires 10+ meaningful commits and at least 2 feature branches
merged through pull requests.

Example feature branch commands:

```bash
git checkout -b feature/docker-setup
git add .
git commit -m 'feat: add Dockerfile and Docker Compose setup'
git push origin feature/docker-setup
```

Second feature branch:

```bash
git checkout -b feature/cicd-pipeline
git add .
git commit -m 'feat: add CI/CD pipeline with build push deploy'
git push origin feature/cicd-pipeline
```

Open pull requests on GitHub and merge them into `main`.

## GitHub Secrets Required for CD

Add these secrets in GitHub:

Repository -> Settings -> Secrets and variables -> Actions -> New repository secret

```text
DOCKER_USERNAME = saad2312408
DOCKER_PASSWORD = your Docker Hub password or access token
EC2_HOST = your EC2 public IP address
EC2_SSH_KEY = full contents of your .pem key file
```

## CI Pipeline

The CI workflow is in:

```text
.github/workflows/ci.yml
```

It runs on:

- push to `main`
- pull request to `main`

It performs:

- checkout repository
- setup Python 3.12
- install dependencies
- run `flake8`
- run `pytest`

## CD Pipeline

The CD workflow is in:

```text
.github/workflows/cd.yml
```

It has three jobs:

1. `test`
2. `build-push`
3. `deploy`

The deployment job runs only after tests pass and only on push to `main`.

## EC2 Deployment

On every push to `main`, the CD pipeline builds the Docker image, pushes it to
Docker Hub, then SSHs into the EC2 instance to pull the latest image and restart
the `myapp` and `devops-db` containers on the shared `app-network`. The app is
served on port 8000 of the EC2 public IP.

## EC2 Verification

After deployment, open:

```text
http://YOUR_EC2_PUBLIC_IP:8000
http://YOUR_EC2_PUBLIC_IP:8000/health
http://YOUR_EC2_PUBLIC_IP:8000/docs
```

SSH into EC2 if manual checking is needed:

```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_IP
```

Check running containers:

```bash
docker ps
```

Check logs:

```bash
docker logs myapp
```

## Demo Video Checklist

Show these in the 5-minute demo:

1. GitHub repository is public and named `2312408-devops-project`.
2. GitHub Actions pipeline is green.
3. EC2 URL opens the FastAPI application.
4. `/health` returns registration number `2312408`.
5. Open `/docs` and POST a student record.
6. Open GET `/students` and show the record is saved.
7. Restart or redeploy and show the data is still present.

## Simple Explanation for Viva

Docker packages the FastAPI app with its dependencies so it runs the same on
my laptop and on EC2. Docker Compose runs two containers together: the web app
and PostgreSQL. The app connects to the database using the Compose service name
`db`, not localhost, because each container is separate. The named volume keeps
PostgreSQL data safe even if containers are stopped and recreated. GitHub
Actions runs tests and linting automatically. If the code is pushed to main and
all checks pass, the pipeline builds a Docker image, pushes it to Docker Hub,
then SSHs into EC2 and runs the latest version.
