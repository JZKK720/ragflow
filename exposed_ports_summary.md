# RAGFlow - Exposed Ports Summary Report

## Overview
This document provides a comprehensive list of all configured exposed container ports and host ports in the RAGFlow project. The report covers Docker Compose configurations, Dockerfiles, Helm charts, and service configuration files.

**Report Generated:** January 20, 2026  
**Repository:** infiniflow/ragflow

---

## 1. Docker Compose Configurations

### 1.1 docker-compose.yml (Main Production Configuration)
**Location:** `c:\ragflow\docker\docker-compose.yml`

#### RAGFlow Service (ragflow-cpu / ragflow-gpu)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${SVR_WEB_HTTP_PORT:-1081}:80` | `SVR_WEB_HTTP_PORT` | 1081:80 | Web interface HTTP (nginx listens on port 80 inside container) |
| `${SVR_WEB_HTTPS_PORT:-1445}:443` | `SVR_WEB_HTTPS_PORT` | 1445:443 | Web interface HTTPS (nginx listens on port 443 inside container) |
| `${SVR_HTTP_PORT}:9380` | `SVR_HTTP_PORT` | 9380:9380 | RAGFlow HTTP API service |
| `${ADMIN_SVR_HTTP_PORT}:9381` | `ADMIN_SVR_HTTP_PORT` | 9381:9381 | Admin server HTTP API |
| `${SVR_MCP_PORT}:9382` | `SVR_MCP_PORT` | 9382:9382 | MCP (Model Context Protocol) server |

**Profiles:** Both `cpu` and `gpu` profiles use the same port mappings.

**Nginx Reverse Proxy Configuration:**
- **Nginx listens internally on:**
  - Port 80 (HTTP) - Serves static files from `/ragflow/web/dist`
  - Port 443 (HTTPS) - When HTTPS is configured (see `ragflow.https.conf`)
- **Nginx proxies to backend services:**
  - `/api/v1/admin` → `http://localhost:9381` (Admin Server)
  - `/v1/*` and `/api/*` → `http://localhost:9380` (RAGFlow API)
  - `/` → Static files (React frontend)
- **Configuration files:**
  - Main: `docker/nginx/nginx.conf`
  - HTTP vhost: `docker/nginx/ragflow.conf`
  - HTTPS vhost: `docker/nginx/ragflow.https.conf` (optional)
  - Proxy settings: `docker/nginx/proxy.conf`

---

### 1.2 docker-compose-base.yml (Dependencies)
**Location:** `c:\ragflow\docker\docker-compose-base.yml`

#### Elasticsearch (es01)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${ES_PORT}:9200` | `ES_PORT` | 1200:9200 | Elasticsearch HTTP API |

**Profile:** `elasticsearch`

#### OpenSearch (opensearch01)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${OS_PORT}:9201` | `OS_PORT` | 1201:9201 | OpenSearch HTTP API |

**Profile:** `opensearch`

#### Infinity (Document Vector Engine)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${INFINITY_THRIFT_PORT}:23817` | `INFINITY_THRIFT_PORT` | 23817:23817 | Infinity Thrift API |
| `${INFINITY_HTTP_PORT}:23820` | `INFINITY_HTTP_PORT` | 23820:23820 | Infinity HTTP API |
| `${INFINITY_PSQL_PORT}:5432` | `INFINITY_PSQL_PORT` | 5432:5432 | Infinity PostgreSQL interface |

**Profile:** `infinity`

#### OceanBase (oceanbase)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${OCEANBASE_PORT:-2881}:2881` | `OCEANBASE_PORT` | 2881:2881 | OceanBase database |

**Profile:** `oceanbase`

#### SeekDB (seekdb)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${SEEKDB_PORT:-2881}:2881` | `SEEKDB_PORT` | 2881:2881 | SeekDB database |

**Profile:** `seekdb`

#### Sandbox Executor Manager
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${SANDBOX_EXECUTOR_MANAGER_PORT:-9385}:9385` | `SANDBOX_EXECUTOR_MANAGER_PORT` | 9385:9385 | Sandbox executor management API |

**Profile:** `sandbox`

#### MySQL (mysql)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${MYSQL_PORT}:3306` | `MYSQL_PORT` | 5455:3306 | MySQL database |

**Note:** MySQL 8.0.39 is used, no profile required (always active). Host port 5455 maps to container port 3306.

#### MinIO (Object Storage)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${MINIO_PORT}:9000` | `MINIO_PORT` | 9000:9000 | MinIO S3 API |
| `${MINIO_CONSOLE_PORT}:9001` | `MINIO_CONSOLE_PORT` | 9001:9001 | MinIO web console |

#### Redis (redis)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${REDIS_PORT}:6379` | `REDIS_PORT` | 6379:6379 | Redis cache/message queue |

**Note:** Uses Valkey 8 (Redis fork) image.

#### Text Embeddings Inference (TEI)
**TEI-CPU Profile:**
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${TEI_PORT:-6380}:80` | `TEI_PORT` | 6380:80 | Text embeddings API |

**Profile:** `tei-cpu`

**TEI-GPU Profile:**
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${TEI_PORT:-6380}:80` | `TEI_PORT` | 6380:80 | Text embeddings API (GPU) |

**Profile:** `tei-gpu`

#### Kibana (kibana)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${KIBANA_PORT:-5601}:5601` | `KIBANA_PORT` | 5601:5601 | Kibana web interface |

**Profile:** `kibana`

---

### 1.3 docker-compose-macos.yml (macOS Configuration)
**Location:** `c:\ragflow\docker\docker-compose-macos.yml`

#### RAGFlow Service (ragflow)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${SVR_HTTP_PORT}:9380` | `SVR_HTTP_PORT` | 9380:9380 | RAGFlow HTTP API service |
| `1081:80` | - | 1081:80 | Web interface HTTP (hardcoded) |
| `1445:443` | - | 1445:443 | Web interface HTTPS (hardcoded) |

**Platform:** linux/amd64

---

### 1.4 docker-compose-CN-oc9.yml (China OpenEuler 9 Configuration)
**Location:** `c:\ragflow\docker\docker-compose-CN-oc9.yml`

#### RAGFlow Service (ragflow-cpu / ragflow-gpu)
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${SVR_HTTP_PORT}:9380` | `SVR_HTTP_PORT` | 9380:9380 | RAGFlow HTTP API service |
| `1081:80` | - | 1081:80 | Web interface HTTP (hardcoded) |
| `1445:443` | - | 1445:443 | Web interface HTTPS (hardcoded) |

**Note:** Team does not actively maintain this configuration.

---

### 1.5 Sandbox docker-compose.yml
**Location:** `c:\ragflow\sandbox\docker-compose.yml`

#### Sandbox Executor Manager
| Port Mapping | Environment Variable | Default Mapping | Description |
|--------------|---------------------|-----------------|-------------|
| `${EXECUTOR_PORT:-9385}:9385` | `EXECUTOR_PORT` | 9385:9385 | Sandbox executor management API |

---

## 2. Dockerfiles

### 2.1 Main Dockerfile
**Location:** `c:\ragflow\Dockerfile`

**Exposed Ports:** No EXPOSE directives found in the Dockerfile.  
**Note:** Ports are configured via docker-compose and service configuration files. Nginx is included in the container and configured to listen on ports 80 and 443.

---

## 3. Nginx Reverse Proxy Architecture

### 3.1 Port Flow
RAGFlow uses nginx as a reverse proxy inside the main container:

```
External Request → Host Port → Container Port → Nginx → Backend Service

HTTP:  http://localhost:1081 → 1081:80 → nginx:80 → [proxy_pass] → localhost:9380 (API) or localhost:9381 (Admin)
HTTPS: https://localhost:1445 → 1445:443 → nginx:443 → [proxy_pass] → localhost:9380 (API) or localhost:9381 (Admin)
```

### 3.2 Nginx Configuration Details

#### HTTP Configuration (ragflow.conf)
**Location:** `docker/nginx/ragflow.conf`
- **Listens on:** Port 80
- **Routes:**
  - `/api/v1/admin` → proxies to `http://localhost:9381` (Admin Server)
  - `/v1/*` and `/api/*` → proxies to `http://localhost:9380` (RAGFlow API)
  - `/` → serves static files from `/ragflow/web/dist` (React frontend)
  - `/static/(css|js|media)/` → static assets with 10-year cache

#### HTTPS Configuration (ragflow.https.conf - Optional)
**Location:** `docker/nginx/ragflow.https.conf`
- **Port 80:** Redirects all HTTP traffic to HTTPS
- **Port 443:** HTTPS listener with SSL certificates
  - Requires SSL certificates at:
    - `/etc/nginx/ssl/fullchain.pem`
    - `/etc/nginx/ssl/privkey.pem`
  - Same proxy routes as HTTP configuration

#### Proxy Settings (proxy.conf)
**Location:** `docker/nginx/proxy.conf`
- Configures proxy headers, timeouts, and buffering
- `proxy_read_timeout: 3600s` (1 hour)
- `proxy_send_timeout: 3600s` (1 hour)
- Buffer sizes optimized for large requests

#### Main Configuration (nginx.conf)
**Location:** `docker/nginx/nginx.conf`
- Global settings: `client_max_body_size 1024M`
- Includes `ragflow.conf` by default
- Worker processes: auto-detected

### 3.3 Backend Service Ports (Inside Container)
All backend services run inside the same container and communicate via localhost:
- **Port 9380:** RAGFlow API Server (handles `/v1/*` and `/api/*` requests)
- **Port 9381:** Admin API Server (handles `/api/v1/admin` requests)
- **Port 9382:** MCP Server (if enabled with `--enable-mcpserver` flag)

---

## 4. Kubernetes Helm Charts

### 4.1 Dockerfile_tei
**Location:** `c:\ragflow\Dockerfile_tei`

**Base Image:** `ghcr.io/huggingface/text-embeddings-inference:cpu-1.8`  
**Exposed Ports:** Inherits from base image (port 80).

### 4.2 Sandbox Executor Manager Dockerfile
**Location:** `c:\ragflow\sandbox\executor_manager\Dockerfile`

**Exposed Ports:** Port 9385 (defined in CMD: `uvicorn main:app --host 0.0.0.0 --port 9385`)

---

## 5. Kubernetes Helm Charts

### 5.1 RAGFlow Deployment
**Location:** `c:\ragflow\helm\templates\ragflow.yaml`

#### RAGFlow Container
| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 80 | http | LoadBalancer/ClusterIP | Web interface HTTP |
| 9380 | http-api | LoadBalancer/ClusterIP | RAGFlow API |

**Services:**
- Main service exposes port 80
- API service (optional) exposes port 80 targeting container port 9380

**Note:** In Kubernetes, nginx runs as part of the RAGFlow container and proxies requests to backend services on ports 9380 and 9381 inside the same pod.

### 5.2 MySQL StatefulSet
**Location:** `c:\ragflow\helm\templates\mysql.yaml`

| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 3306 | mysql | ClusterIP | MySQL database |

### 5.3 Infinity StatefulSet
**Location:** `c:\ragflow\helm\templates\infinity.yaml`

| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 23817 | thrift | ClusterIP | Infinity Thrift API |
| 23820 | http | ClusterIP | Infinity HTTP API |
| 5432 | psql | ClusterIP | Infinity PostgreSQL interface |

**Condition:** Only deployed when `DOC_ENGINE=infinity`

### 5.4 Elasticsearch StatefulSet
**Location:** `c:\ragflow\helm\templates\elasticsearch.yaml`

| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 9200 | http | ClusterIP | Elasticsearch HTTP API |
| 9300 | transport | - | Elasticsearch transport (internal) |

**Service exposes:** Port 9200 only  
**Condition:** Only deployed when `DOC_ENGINE=elasticsearch`

### 5.5 OpenSearch StatefulSet
**Location:** `c:\ragflow\helm\templates\opensearch.yaml`

| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 9201 | http | ClusterIP | OpenSearch HTTP API |

**Condition:** Only deployed when `DOC_ENGINE=opensearch`

### 5.6 MinIO StatefulSet
**Location:** `c:\ragflow\helm\templates\minio.yaml`

| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 9000 | s3 | ClusterIP | MinIO S3 API |
| 9001 | console | ClusterIP | MinIO web console |

### 5.7 Redis StatefulSet
**Location:** `c:\ragflow\helm\templates\redis.yaml`

| Container Port | Port Name | Service Type | Description |
|---------------|-----------|--------------|-------------|
| 6379 | redis | ClusterIP | Redis cache/message queue |

**Note:** Uses headless service and a separate named service.

---

## 6. Service Configuration Files

### 6.1 service_conf.yaml
**Location:** `c:\ragflow\conf\service_conf.yaml`

#### Internal Container Ports (Used within Docker network)

| Service | Port | Description |
|---------|------|-------------|
| ragflow | 9380 | RAGFlow HTTP API (internal) |
| admin | 9381 | Admin server HTTP API (internal) |
| mysql | 5455 | MySQL database (via host mapping) |
| minio | 9000 | MinIO S3 API (internal) |
| redis | 6379 | Redis (internal) |
| es | 1200 | Elasticsearch HTTP (via host mapping) |
| os | 1201 | OpenSearch HTTP (via host mapping) |
| infinity | 23817 | Infinity Thrift API (internal) |
| infinity | 5432 | Infinity PostgreSQL (internal) |
| oceanbase | 2881 | OceanBase database (internal) |
| embedding (TEI) | 6380 | Text embeddings (via host mapping) |

**Note:** These are internal service configurations. Actual host ports are defined in docker-compose files.

---

## 7. Web Development Server

### 7.1 Vite Development Server
**Location:** `c:\ragflow\web\vite.config.ts`

| Port | Environment Variable | Default | Description |
|------|---------------------|---------|-------------|
| `${PORT}` | PORT | 9222 | Vite development server |

**Note:** Only used during frontend development, not in production.

---

## 8. Environment Variables Summary

All port configurations are managed through environment variables defined in the `.env` file. A `.env.example` template file is provided for reference.

### 8.1 Docker Environment Variables
**Locations:**
- **Active Configuration:** `c:\ragflow\docker\.env` (used by Docker Compose)
- **Template:** `c:\ragflow\docker\.env.example` (example/reference file)

**Note:** The `.env` file contains all port mappings in the format `host:container`. Copy `.env.example` to `.env` and customize as needed.

| Variable Name | Default Value | Description |
|--------------|---------------|-------------|
| `ES_PORT` | 1200 | Elasticsearch host port |
| `OS_PORT` | 1201 | OpenSearch host port |
| `INFINITY_THRIFT_PORT` | 23817 | Infinity Thrift host port |
| `INFINITY_HTTP_PORT` | 23820 | Infinity HTTP host port |
| `INFINITY_PSQL_PORT` | 5432 | Infinity PostgreSQL host port |
| `OCEANBASE_PORT` | 2881 | OceanBase host port |
| `SEEKDB_PORT` | 2881 | SeekDB host port |
| `SANDBOX_EXECUTOR_MANAGER_PORT` | 9385 | Sandbox executor host port |
| `MYSQL_PORT` | 5455 | MySQL host port |
| `MINIO_PORT` | 9000 | MinIO S3 API host port |
| `MINIO_CONSOLE_PORT` | 9001 | MinIO console host port |
| `REDIS_PORT` | 6379 | Redis host port |
| `SVR_HTTP_PORT` | 9380 | RAGFlow API host port |
| `SVR_WEB_HTTP_PORT` | 1081 | RAGFlow web HTTP host port |
| `SVR_WEB_HTTPS_PORT` | 1445 | RAGFlow web HTTPS host port |
| `ADMIN_SVR_HTTP_PORT` | 9381 | Admin server host port |
| `SVR_MCP_PORT` | 9382 | MCP server host port |
| `TEI_PORT` | 6380 | Text embeddings host port |
| `KIBANA_PORT` | 5601 | Kibana host port |

### 8.2 Sandbox Environment Variables
**Location:** `c:\ragflow\sandbox\.env.example`

| Variable Name | Default Value | Description |
|--------------|---------------|-------------|
| `SANDBOX_EXECUTOR_MANAGER_PORT` | 9385 | Sandbox executor manager port |

---

## 9. Port Usage Matrix

### 9.1 Default Port Mappings

| Port Mapping | Service | Protocol | Purpose |
|--------------|---------|----------|---------|
| 1081:80 | RAGFlow Web | HTTP | Web UI |
| 1200:9200 | Elasticsearch | HTTP | Search engine |
| 1201:9201 | OpenSearch | HTTP | Search engine (alternative) |
| 1445:443 | RAGFlow Web | HTTPS | Secure web UI |
| 2881:2881 | OceanBase/SeekDB | TCP | Database |
| 5432:5432 | Infinity | TCP | PostgreSQL interface |
| 5455:3306 | MySQL | TCP | Database |
| 5601:5601 | Kibana | HTTP | Elasticsearch UI |
| 6379:6379 | Redis | TCP | Cache/Queue |
| 6380:80 | TEI | HTTP | Text embeddings |
| 9000:9000 | MinIO | HTTP | Object storage API |
| 9001:9001 | MinIO | HTTP | Object storage console |
| 9222 | Vite Dev Server | HTTP | Frontend development (local only) |
| 9380:9380 | RAGFlow API | HTTP | Backend API |
| 9381:9381 | Admin Server | HTTP | Admin API |
| 9382:9382 | MCP Server | HTTP | Model Context Protocol |
| 9385:9385 | Sandbox Manager | HTTP | Code execution sandbox |
| 23817:23817 | Infinity | Thrift | Vector engine (Thrift) |
| 23820:23820 | Infinity | HTTP | Vector engine (HTTP) |

### 9.2 Port Ranges Used
- **1000-2000:** Web interfaces and search engines
- **2000-3000:** Databases (OceanBase, SeekDB)
- **5000-6000:** Databases (MySQL, PostgreSQL) and utilities
- **6000-7000:** Redis and embedding services
- **9000-9500:** Object storage, RAGFlow services, APIs

---

## 10. Network Architecture

### 10.1 Docker Network
**Network Name:** `ragflow` (defined in docker-compose files)

All services communicate internally using container names and internal ports. Only explicitly mapped ports are accessible from the host machine.

### 10.2 Kubernetes Network
**Namespace:** Defined by Helm release namespace

Services use ClusterIP by default for internal communication. LoadBalancer or Ingress can be configured for external access.

---

## 11. Security Considerations

### 11.1 Port Exposure Recommendations
- **Production:** Only expose necessary ports (80/443, 9380) to external networks
- **Development:** Additional ports (1200, 5455, 9000, etc.) may be needed for debugging
- **Sandbox:** Port 9385 should be protected as it executes arbitrary code

### 11.2 Default Passwords
⚠️ **WARNING:** Change all default passwords before deploying to production!
- MySQL: `infini_rag_flow`
- Redis: `infini_rag_flow`
- MinIO: Set via `MINIO_PASSWORD`
- Elasticsearch: Set via `ELASTIC_PASSWORD`
- OpenSearch: `infini_rag_flow_OS_01`

---

## 12. Configuration File Locations

### 12.1 Docker Deployment
- Main compose: `docker/docker-compose.yml`
- Base services: `docker/docker-compose-base.yml`
- **Active environment:** `docker/.env`
- **Template environment:** `docker/.env.example`
- Service config template: `docker/service_conf.yaml.template`

### 12.2 Kubernetes Deployment
- Helm chart: `helm/`
- Values: `helm/values.yaml`
- Templates: `helm/templates/*.yaml`

### 12.3 Service Configuration
- Local config: `conf/service_conf.yaml`
- Template: `docker/service_conf.yaml.template`

---

## 13. Profile-Based Deployment

### 13.1 Docker Compose Profiles
RAGFlow uses Docker Compose profiles to enable/disable services:

| Profile | Services Enabled |
|---------|------------------|
| `cpu` | RAGFlow (CPU mode) |
| `gpu` | RAGFlow (GPU mode with NVIDIA support) |
| `elasticsearch` | Elasticsearch search engine |
| `opensearch` | OpenSearch search engine |
| `infinity` | Infinity vector engine |
| `oceanbase` | OceanBase database |
| `seekdb` | SeekDB database |
| `sandbox` | Code execution sandbox |
| `tei-cpu` | Text embeddings (CPU) |
| `tei-gpu` | Text embeddings (GPU) |
| `kibana` | Elasticsearch web UI |

**Example Usage:**
```bash
# Start with Elasticsearch and CPU RAGFlow
docker compose --profile elasticsearch --profile cpu up -d

# Start with Infinity and GPU RAGFlow
docker compose --profile infinity --profile gpu up -d
```

---

## 14. Port Conflicts and Resolution

### 14.1 Common Conflicts
- **Port 9000:** MinIO conflicts with some development tools (PHPStorm, etc.)
- **Port 3306:** Standard MySQL port - changed to 5455 by default
- **Port 6379:** Standard Redis port
- **Port 80/443:** Standard web ports - changed to 1081/1445 by default

### 14.2 Customization
To change any port, modify the corresponding environment variable in `docker/.env` before starting services.

**Setup Steps:**
1. Copy the example file: `cp docker/.env.example docker/.env`
2. Edit `docker/.env` with your preferred port mappings
3. Start/restart services: `docker compose -f docker/docker-compose.yml up -d`

**Example:**
```env
# Change MinIO console from 9001 to 9091
# Port mapping changes from 9001:9001 to 9091:9001
MINIO_CONSOLE_PORT=9091

# Change web interface from 1081 to 8080
# Port mapping changes from 1081:80 to 8080:80
SVR_WEB_HTTP_PORT=8080
```

---

## 15. Documentation References

- **Configuration Guide:** [docs/configurations.md](docs/configurations.md)
- **Docker README:** [docker/README.md](docker/README.md)
- **Helm Chart README:** [helm/README.md](helm/README.md)
- **Main README:** [README.md](README.md)

---

## 16. Changelog

| Date | Version | Changes |
|------|---------|---------|
| 2026-01-20 | 1.0 | Initial comprehensive port mapping report |

---

## Appendix A: Quick Reference Table

### Essential Ports for Basic Operation

| Purpose | Port Mapping | Service |
|---------|--------------|---------|
| **Web Access** | 1081:80 | RAGFlow Web UI (HTTP) |
| **API Access** | 9380:9380 | RAGFlow REST API |
| **Database** | 5455:3306 | MySQL |
| **Storage** | 9000:9000 | MinIO (S3) |
| **Cache** | 6379:6379 | Redis |
| **Search** | 1200:9200 or 1201:9201 | Elasticsearch/OpenSearch |
| **Vector DB** | 23817:23817 / 23820:23820 | Infinity |

### Optional Ports

| Purpose | Port Mapping | Service |
|---------|--------------|---------|
| **Admin Interface** | 9381:9381 | Admin API |
| **MCP Server** | 9382:9382 | Model Context Protocol |
| **Sandbox** | 9385:9385 | Code Execution |
| **Embeddings** | 6380:80 | TEI Service |
| **Storage Console** | 9001:9001 | MinIO Web UI |
| **Search UI** | 5601:5601 | Kibana |
| **Secure Web** | 1445:443 | HTTPS Web UI |

---

## Appendix B: Health Check Endpoints

| Service | Health Check URL (from host) | Port Mapping |
|---------|-------------------------------|--------------|
| RAGFlow API | `http://localhost:9380/` | 9380:9380 |
| Admin API | `http://localhost:9381/` | 9381:9381 |
| Elasticsearch | `http://localhost:1200/` | 1200:9200 |
| OpenSearch | `http://localhost:1201/` | 1201:9201 |
| Infinity | `http://localhost:23820/admin/node/current` | 23820:23820 |
| MinIO | `http://localhost:9000/minio/health/live` | 9000:9000 |
| Sandbox | `http://localhost:9385/healthz` | 9385:9385 |
| Kibana | `http://localhost:5601/api/status` | 5601:5601 |
| Redis | `redis-cli -h localhost -p 6379 -a <password> ping` | 6379:6379 |
| MySQL | `mysqladmin ping -h localhost -P 5455 -u root -p<password>` | 5455:3306 |

---

**End of Report**
