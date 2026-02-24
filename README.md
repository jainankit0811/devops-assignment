As part of the next step in the hiring process, we’d like you to complete the following technical assignment. This task is designed to evaluate your hands-on skills with containerization, CI/CD, and cloud deployment.

**Link for the folders:** https://drive.google.com/drive/folders/1oBLJlhq0QaSC0A3arUZwORjQOC3sRFUH?usp=sharing

## 🛠️ Task Overview

You are required to set up, containerize, and deploy a full-stack MEAN (MongoDB, Express, Angular, Node.js) application.

The application code is provided in the `crud-dd-task-mean-app.zip` file. Once extracted, you'll find separate frontend and backend directories. A `README.md` file is included in the project for additional guidance.

## ✅ What You Need to Do

### 1) Repository Setup

- Create a new GitHub repository for this project.
- Push the complete code to the repository.

### 2) Containerization & Deployment

- Create Docker files for both the frontend and backend.
- Build and push Docker images to your Docker Hub account.
- Set up a new Ubuntu virtual machine on any cloud platform (AWS, Azure, or similar).
- Use Docker Compose to deploy the application on this VM.

### 3) Database Setup

Choose one of the following options:

- Install MongoDB directly on the Ubuntu VM.
- OR use the official MongoDB Docker image as part of your Docker Compose setup.

### 4) CI/CD Pipeline Configuration

Use GitHub Actions or Jenkins to implement a CI/CD pipeline.

The pipeline should:

- Build updated Docker images when changes are pushed to GitHub.
- Push those images to Docker Hub.
- Automatically pull the latest images and restart containers on the VM.

### 5) Nginx Reverse Proxy

- Set up Nginx as a reverse proxy.
- The entire application should be accessible via port 80 (HTTPS and domain mapping not required).

## 📦 Deliverables

Please share the GitHub repository link once you’ve completed the task.

Make sure your repository includes:

- All Dockerfiles and the Docker Compose file.
- The complete CI/CD configuration.
- A well-documented `README.md` with:
	- Step-by-step setup and deployment instructions.
	- Screenshots showcasing:
		- CI/CD configuration and execution.
		- Docker image build and push process.
		- Application deployment and working UI.
		- Nginx setup and infrastructure details.


# DevOps Assignment - Setup Guide

This repository contains a full-stack CRUD application with:

- Frontend: Angular 15
- Backend: Node.js + Express
- Database: MongoDB
- Reverse Proxy: Nginx
- Orchestration: Docker Compose
- CI/CD: GitHub Actions

## 1. Project Structure

```
devops-assignment/
├─ backend/
├─ frontend/
├─ nginx/
├─ docker-compose.yml
└─ .github/workflows/deploy.yml
```

## 2. Prerequisites

For local setup and deployment, install:

- Git
- Docker Engine + Docker Compose plugin
- Node.js 18+ (only needed for non-Docker local development)
- npm

## 3. Environment Configuration

Create/update `.env` in project root:

```env
MONGO_USERNAME=your_mongo_user
MONGO_PASSWORD=your_mongo_password
MONGO_DB=test
```

`docker-compose.yml` uses these values to start MongoDB and build the backend Mongo connection string.

## 4. Run with Docker Compose (Recommended)

From repository root:

```bash
docker compose up -d --build
```

Check running containers:

```bash
docker compose ps
```

Stop services:

```bash
docker compose down
```

### Access URLs

- App (Nginx): http://localhost
- API via Nginx: http://localhost/api/tutorials

Nginx routes are defined in `nginx/devops-assignment.conf`:

- `/` -> frontend container
- `/api/` -> backend container

## 5. Optional: Run Without Docker

### Backend

```bash
cd backend
npm install
```

Set `MONGODB_URL` (example):

```bash
mongodb://<username>:<password>@localhost:27017/<db>?authSource=admin
```

Run backend:

```bash
node server.js
```

### Frontend

```bash
cd frontend
npm install
ng serve --port 4200
```

Open: http://localhost:4200

## 6. Build and Push Docker Images

Backend image:

```bash
cd backend
docker build -t ankitd002/devops-assignment-backend:latest .
docker push ankitd002/devops-assignment-backend:latest
```

Frontend image:

```bash
cd frontend
docker build -t ankitd002/devops-assignment-frontend:latest .
docker push ankitd002/devops-assignment-frontend:latest
```

## 7. VM Deployment (Ubuntu)

Install Docker + Compose plugin on VM, then:

```bash
git clone <your-repo-url>
cd devops-assignment
docker compose pull
docker compose up -d
```

Expose port `80` in VM firewall/security group.

## 8. CI/CD Pipeline (GitHub Actions)

Workflow file: `.github/workflows/deploy.yml`

Pipeline behavior on push to `main`:

1. Build backend image and push to Docker Hub
2. Build frontend image and push to Docker Hub
3. SSH to VM and run deployment commands:
   - `docker-compose down`
   - `docker-compose pull`
   - `docker-compose up -d`

### Required GitHub Secrets

- `DOCKER_USERNAME`
- `DOCKER_PASSWORD`
- `EC2_HOST`
- `EC2_USER`
- `EC2_KEY`

## 9. Health Checks / Verification

- Open `http://<vm-public-ip>/` and verify the Angular UI loads
- Verify API: `http://<vm-public-ip>/api/tutorials`
- Check logs if needed:

```bash
docker compose logs -f nginx
docker compose logs -f backend
docker compose logs -f frontend
docker compose logs -f mongodb
```

## 10. Notes

- Backend listens on port `8080` inside container.
- Frontend is served by Nginx inside container on port `80`.
- External traffic is exposed through Nginx on host port `80`.
