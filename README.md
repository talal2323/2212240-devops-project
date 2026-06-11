# 2212240 — DevOps Final Project

> **Name:** Talal Yousuf Salat  
> **Registration Number:** 2212240  
> **Course:** DevOps Fundamentals  
> **Live URL:** http://YOUR_EC2_IP:8000  

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
                    └── git pull + docker compose up --build
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

---

## ☁️ Production EC2 Deployment (Reference Guide)

```bash
# Securely connect into the cloud computing instance
ssh -i your-key.pem ubuntu@YOUR_EC2_IP

# Initialize updates and prepare the Docker container runtime engine
sudo apt update && sudo apt install -y docker.io docker-compose-plugin
sudo usermod -aG docker ubuntu

# Clone core codebase, provision environment secrets, and build infrastructure live
git clone [https://github.com/talal2323/2212240-devops-project](https://github.com/talal2323/2212240-devops-project) ~/devops-project
cd ~/devops-project
cp .env.example .env
docker compose up -d --build
```