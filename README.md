# Resume Generator

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-Azure%20Linux-blue)](https://azure.microsoft.com/services/virtual-machines/)

A modern, containerized web application for creating professional resumes with ease.

## Section 1: Overview

### Main Function

The Resume Generator is a full-stack web application designed to help users create professional, well-formatted resumes quickly and efficiently. It consists of two main components:

1. **FastAPI REST API Server** - A backend service that handles resume generation logic, data processing, and provides a RESTful API for client applications.
2. **Nginx Web UI** - A static HTML interface served by Nginx that provides a user-friendly experience for creating and managing resumes.

### Core Value Proposition

- **Zero Setup Required**: Pre-built Docker images pulled directly from Docker Hub eliminate the need for local development environments or complex build processes.
- **Quick Deployment**: Get a fully functional resume generator up and running in minutes using Docker Compose.
- **Scalable Architecture**: Containerized services with health checks ensure reliability and easy horizontal scaling.
- **Modern Tech Stack**: Built with FastAPI for high-performance API services and Nginx for efficient static content delivery.

### Key Features

| Feature | Description |
|---------|-------------|
| **RESTful API** | Clean, documented API endpoints for resume operations |
| **Health Monitoring** | Built-in health checks for both API and Web UI services |
| **Container Isolation** | Each service runs in its own container for security and stability |
| **Custom Networking** | Dedicated bridge network for inter-service communication |
| **Auto-Restart** | Services automatically restart on failure for high availability |

### User Workflows

#### Creating a Resume

1. **Access the Web UI** - Open your browser and navigate to `http://localhost:8079`
2. **Fill in Resume Details** - Complete the form with your personal information, experience, education, and skills
3. **Generate Resume** - Submit the form to trigger the resume generation process
4. **Download Result** - Receive your professionally formatted resume in the desired format (PDF/DOCX)

#### API Integration

Developers can interact directly with the API:

```bash
# Check API health
curl http://localhost:8077/api/health

# Generate a resume (example endpoint)
curl -X POST http://localhost:8077/api/generate \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "position": "Software Engineer"}'
```

---

## Section 2: Deployment Guide

### Prerequisites

Before deploying the Resume Generator, ensure you have the following installed:

| Prerequisite | Minimum Version | Verification Command |
|--------------|-----------------|---------------------|
| **Docker** | 20.10+ | `docker --version` |
| **Docker Compose** | 2.0+ | `docker compose version` |
| **Curl** | Any | `curl --version` (for health checks) |

> **Note**: Docker Desktop (Windows/Mac) or Docker Engine (Linux) includes both Docker and Docker Compose.

### Installation Steps

#### Step 1: Clone the Repository

```bash
git clone https://github.com/congdnguyen/resume-generator
cd resume-generator
```

#### Step 2: Verify Docker Compose File

Ensure you have the `docker-compose.yml` file in your project directory. This file defines:

- **api-server**: FastAPI backend service (port 8077)
- **web-ui**: Nginx static web interface (port 8079)
- **resume-network**: Custom bridge network for service communication

#### Step 3: Create Environment Configuration (Optional)

While this deployment uses pre-built images with default configurations, you can create a `.env` file for custom settings:

```bash
# .env (optional - only needed for custom configurations)
API_PORT=8077
WEB_PORT=8079
```

#### Step 4: Start the Services

Run the following command from the project root directory:

```bash
docker compose up -d
```

This will:
1. Pull the latest Docker images from Docker Hub:
   - `congnguyen12/resume-api:latest`
   - `congnguyen12/resume-web:latest`
2. Create a bridge network named `resume-generator_resume-network`
3. Start both containers in detached mode

#### Step 5: Verify Deployment

Check that all services are running:

```bash
docker compose ps
```

Expected output:

```
NAME          STATUS         PORTS                    NAMES
api-server    Up (healthy)   0.0.0.0:8077->8000/tcp   api-server
web-ui        Up (healthy)   0.0.0.0:8079->80/tcp     web-ui
```

#### Step 6: Access the Application

| Service | URL | Description |
|---------|-----|-------------|
| **Web UI** | `http://localhost:8079` | User interface for creating resumes |
| **API Health** | `http://localhost:8077/api/health` | API health check endpoint |
| **API Root** | `http://localhost:8077/` | API documentation (typically Swagger/OpenAPI) |

**Access the Web UI:**
Open your browser and navigate to `http://localhost:8079`

**Test the API:**
```bash
curl http://localhost:8077/api/health
```

Expected response:
```json
{"status": "healthy", "service": "resume-api"}
```

### Service Configuration Details

#### API Server (api-server)

- **Image**: `congnguyen12/resume-api:latest`
- **Exposed Port**: 8077 (maps to container port 8000)
- **Health Check**: `curl -f http://localhost:8000/api/health`
- **Restart Policy**: `unless-stopped`
- **Network**: resume-network

#### Web UI (web-ui)

- **Image**: `congnguyen12/resume-web:latest`
- **Exposed Port**: 8079 (maps to container port 80)
- **Health Check**: `curl -f http://localhost/`
- **Dependencies**: Depends on api-server
- **Restart Policy**: `unless-stopped`
- **Network**: resume-network

### Stopping the Services

To stop all services:

```bash
docker compose down
```

To stop and remove volumes (clean slate):

```bash
docker compose down -v
```

### Viewing Logs

View real-time logs:

```bash
docker compose logs -f
```

View logs for a specific service:

```bash
docker compose logs -f api-server
docker compose logs -f web-ui
```

### Troubleshooting

#### Service Won't Start

1. Check Docker images are pulled:
   ```bash
   docker images | grep resume
   ```

2. Check for port conflicts:
   ```bash
   netstat -ano | findstr ":8077"
   netstat -ano | findstr ":8079"
   ```

3. View detailed logs:
   ```bash
   docker compose logs api-server
   docker compose logs web-ui
   ```

#### Health Check Failing

Wait for the start_period (30s for API, 10s for Web UI) before checking health status. If issues persist:

```bash
# Check container logs
docker logs api-server
docker logs web-ui

# Restart services
docker compose restart
```

### Updating to New Versions

To pull and deploy the latest images:

```bash
docker compose pull
docker compose up -d
```

### Network Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Docker Network                        │
│                 (resume-network)                         │
│                                                          │
│  ┌──────────────┐              ┌──────────────┐         │
│  │   Web UI     │              │   API Server │         │
│  │   (Nginx)    │◄────────────►│   (FastAPI)  │         │
│  │   Port 8079  │   Internal   │   Port 8077  │         │
│  └──────────────┘   Network   └──────────────┘         │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `docker compose up -d` | Start services in background |
| `docker compose down` | Stop services |
| `docker compose ps` | Show running containers |
| `docker compose logs -f` | View logs (follow) |
| `docker compose pull` | Pull latest images |
| `docker compose restart` | Restart services |

---

## License

This project is licensed under the MIT License.

## Support

For issues and feature requests, please open an issue on the [GitHub repository](https://github.com/congdnguyen/resume-generator).
