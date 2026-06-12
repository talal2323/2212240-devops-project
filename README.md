# 2212240 — DevOps Final Project

> **Name:** Talal Yousuf Salat  
> **Registration Number:** 2212240  
> **Course:** DevOps Fundamentals  
> **Live URL:** http://100.30.172.126:8000  

---

## 🏗️ Architecture Description

```text
GitHub Push
    │
    ├── CI Pipeline (GitHub Actions)
    │       ├── flake8 lint
    │       └── pytest (with PostgreSQL service container)
    │
    └── CD Pipeline (GitHub Actions)
            └── SSH into EC2
                    └── git pull + docker-compose -f docker-compose.prod.yml up -d --build
```

**Services Backend Component:**
- `web` — FastAPI enterprise application running on Python 3.12-slim, exposed on port 8000 via a production-hardened non-root user setup.
- `db`  — PostgreSQL 15 relational database backend running on port 5432, isolated inside the virtual network and utilizing a persistent named volume to safeguard user and system records.

---

## 🚀 Local Setup Instructions

**Prerequisites:** Docker, Docker Compose, Python 3.12

**1. Clone the repository**
```bash
git clone [https://github.com/talal2323/2212240-devops-project.git](https://github.com/talal2323/2212240-devops-project.git)
cd 2212240-devops-project
```

**2. Create your local environment configuration file**
```bash
cp .env.example .env
```
*(Open `.env` and configure your database credentials. Note that `.env` is omitted from Git via `.gitignore` to protect sensitive local secrets.)*

**3. Build and Orchestrate Services**
Spin up both the web API and PostgreSQL database containers concurrently in detached background mode:
```bash
docker compose up --build -d
```

**4. Verify the Local Deployment**
You can test the health checks and endpoints directly via your terminal or web browser:
* **Health Check API Endpoint:** `curl http://localhost:8000/health` (Expects student confirmation string `"2212240"`)
* **Interactive Open-API Documentation:** Navigate to `http://localhost:8000/docs`

---

## 📑 Core API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| **GET** | `/health` | Live system health check + active Database connection status validation |
| **POST** | `/students` | Insert a new record into the PostgreSQL persistent data layer |
| **GET** | `/students` | Fetch and compile a collection list of all saved records |
| **GET** | `/students/{reg_no}` | Query details for a specific record via its unique registration identifier |
| **DELETE** | `/students/{student_id}` | Remove a specific student record from the database via its unique ID |

---

## ☁️ Production EC2 Deployment (Reference Guide)

The live application is hosted on an AWS EC2 instance. The environment is separated from local development using a dedicated production configuration.

```bash
# Securely connect into the cloud computing instance
ssh -i ~/.ssh/2212240-project-key ubuntu@100.30.172.126

# Navigate to the project directory (assuming it is already cloned)
cd ~/2212240-devops-project

# Ensure the production environment variables are active
cp .env.production .env

# Build and deploy the production infrastructure
docker-compose -f docker-compose.prod.yml up -d --build
```

**Data Persistence Management:**
To restart the server without losing data:
```bash
docker-compose -f docker-compose.prod.yml restart
```
To perform a complete wipe of the database (Warning: Destroys all data):
```bash
docker-compose -f docker-compose.prod.yml down -v
```