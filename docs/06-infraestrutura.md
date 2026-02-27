# 06 - Infraestrutura

> Somos Produtora - Documentacao de Infraestrutura e DevOps
> Versao: 1.0 | Ultima atualizacao: 2026-02-28

---

## Indice

1. [Visao Geral da Infraestrutura](#visao-geral-da-infraestrutura)
2. [Diagrama de Infraestrutura](#diagrama-de-infraestrutura)
3. [Hardware Recomendado](#hardware-recomendado)
4. [Sistema Operacional e Configuracao Base](#sistema-operacional-e-configuracao-base)
5. [Docker e Containerizacao](#docker-e-containerizacao)
6. [Nginx Reverse Proxy](#nginx-reverse-proxy)
7. [Seguranca do Servidor](#seguranca-do-servidor)
8. [CI/CD com GitHub Actions](#cicd-com-github-actions)
9. [Estrategia de Backups](#estrategia-de-backups)
10. [Monitoramento e Observabilidade](#monitoramento-e-observabilidade)
11. [Plano de Recuperacao de Desastres](#plano-de-recuperacao-de-desastres)
12. [Estrategia de Escalabilidade](#estrategia-de-escalabilidade)

---

## 1. Visao Geral da Infraestrutura

A infraestrutura da Somos Produtora segue a filosofia de **self-hosting pragmatico**: todos os componentes rodam em servidores proprios via Docker, reduzindo custos mensais e mantendo controle total sobre os dados. Servicos de cloud sao usados apenas para APIs de IA e como backup externo.

### Principios

- **Simplicidade**: Docker Compose como orquestrador principal (sem Kubernetes no inicio)
- **Seguranca**: Acesso apenas via SSH com chaves, firewall restritivo, SSL obrigatorio
- **Automacao**: Deploy automatizado via GitHub Actions, backups agendados
- **Observabilidade**: Metricas, logs e alertas centralizados
- **Custo-beneficio**: VPS dedicada ao inves de cloud pay-per-use

---

## 2. Diagrama de Infraestrutura

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              INTERNET                                           │
└────────────────────────────────┬────────────────────────────────────────────────┘
                                 │
                          ┌──────▼──────┐
                          │  Cloudflare  │
                          │    DNS +     │
                          │   DDoS Prot  │
                          └──────┬──────┘
                                 │
                          ┌──────▼──────┐
                          │   UFW        │
                          │  Firewall    │
                          │  (80,443,22) │
                          └──────┬──────┘
                                 │
┌────────────────────────────────▼────────────────────────────────────────────────┐
│                        VPS PRINCIPAL (8 vCPU / 32GB RAM / 500GB NVMe)          │
│                        Ubuntu 22.04 LTS + Docker                                │
│                                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────────┐    │
│  │                         NGINX (Reverse Proxy)                           │    │
│  │                    Let's Encrypt SSL / HTTP2 / Gzip                      │    │
│  │                         Port 80 → 443 redirect                          │    │
│  └────────┬──────────────────┬───────────────────┬────────────────────┘    │
│           │                  │                   │                          │
│  ┌────────▼──────┐  ┌───────▼───────┐  ┌───────▼───────┐                  │
│  │   FRONTEND    │  │   BACKEND     │  │    MINIO      │                  │
│  │   Next.js     │  │   FastAPI     │  │  Object Store │                  │
│  │   Port 3000   │  │   Port 8000   │  │  Port 9000    │                  │
│  │   (2 inst.)   │  │   (4 workers) │  │  Console 9001 │                  │
│  └───────────────┘  └───────┬───────┘  └───────────────┘                  │
│                             │                                               │
│           ┌─────────────────┼─────────────────┐                            │
│           │                 │                 │                              │
│  ┌────────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐                      │
│  │  POSTGRESQL   │  │    REDIS    │  │   QDRANT    │                      │
│  │   Port 5432   │  │  Port 6379  │  │  Port 6333  │                      │
│  │   16 GB RAM   │  │  2 GB RAM   │  │  4 GB RAM   │                      │
│  └───────────────┘  └──────┬──────┘  └─────────────┘                      │
│                            │                                                │
│           ┌────────────────┼────────────────┐                              │
│           │                │                │                                │
│  ┌────────▼──────┐  ┌─────▼───────┐  ┌─────▼──────┐                       │
│  │ CELERY WORKER │  │ CELERY BEAT │  │  FLOWER    │                       │
│  │  (4 workers)  │  │ (scheduler) │  │ (monitor)  │                       │
│  └───────────────┘  └─────────────┘  └────────────┘                       │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                    MONITORAMENTO                                 │       │
│  │  ┌────────────┐  ┌────────────┐  ┌──────┐  ┌────────────────┐  │       │
│  │  │ Prometheus │  │  Grafana   │  │ Loki │  │ Node Exporter  │  │       │
│  │  │ Port 9090  │  │ Port 3001  │  │ 3100 │  │    9100        │  │       │
│  │  └────────────┘  └────────────┘  └──────┘  └────────────────┘  │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                 │
                          ┌──────▼──────┐
                          │   NAS       │
                          │  Externo    │
                          │  (Backups + │
                          │   Media)    │
                          └─────────────┘
```

### Diagrama de Rede (Mermaid)

```mermaid
graph TB
    Internet((Internet)) --> Cloudflare[Cloudflare DNS + DDoS]
    Cloudflare --> Firewall[UFW Firewall]
    Firewall --> Nginx[Nginx Reverse Proxy<br/>SSL Termination]

    Nginx --> Frontend[Next.js Frontend<br/>Port 3000]
    Nginx --> Backend[FastAPI Backend<br/>Port 8000]
    Nginx --> MinIO[MinIO Storage<br/>Port 9000]

    Backend --> PostgreSQL[(PostgreSQL 16<br/>Port 5432)]
    Backend --> Redis[(Redis 7<br/>Port 6379)]
    Backend --> Qdrant[(Qdrant<br/>Port 6333)]
    Backend --> CeleryWorker[Celery Workers]

    CeleryWorker --> Redis
    CeleryWorker --> ExternalAPIs{APIs Externas}

    ExternalAPIs --> Anthropic[Anthropic API]
    ExternalAPIs --> OpenAI[OpenAI API]
    ExternalAPIs --> WhatsApp[WhatsApp API]
    ExternalAPIs --> LinkedIn[LinkedIn API]
    ExternalAPIs --> SerpAPI[SerpAPI]

    PostgreSQL --> Backup[Backup Diario]
    Backup --> NAS[NAS Externo]
    Backup --> CloudBackup[Cloudflare R2]

    subgraph Monitoramento
        Prometheus[Prometheus] --> Grafana[Grafana]
        Loki[Loki] --> Grafana
        Sentry[Sentry Cloud]
    end

    Backend --> Prometheus
    Backend --> Sentry
```

---

## 3. Hardware Recomendado

### Servidor Principal (VPS)

| Especificacao         | Minimo             | Recomendado          | Ideal                |
|-----------------------|--------------------|----------------------|----------------------|
| **vCPU**              | 4 cores            | 8 cores              | 16 cores             |
| **RAM**               | 16 GB              | 32 GB                | 64 GB                |
| **Disco**             | 256 GB NVMe SSD    | 500 GB NVMe SSD      | 1 TB NVMe SSD        |
| **Banda**             | 1 Gbps             | 1 Gbps               | 10 Gbps              |
| **Transferencia**     | 10 TB/mes          | 20 TB/mes            | Ilimitado            |
| **Custo Estimado**    | ~R$ 250/mes        | ~R$ 450/mes          | ~R$ 900/mes          |

### Provedores Recomendados (Brasil)

| Provedor         | Plano Sugerido           | Custo Mensal    | Datacenter Brasil |
|------------------|--------------------------|-----------------|-------------------|
| Hetzner          | CPX41 (8vCPU/16GB)      | EUR 30 (~R$170) | Nao (Alemanha)    |
| Contabo          | VPS XL (10vCPU/32GB)    | EUR 30 (~R$170) | Nao               |
| DigitalOcean     | Premium 8vCPU/32GB      | $192 (~R$960)   | Nao               |
| Vultr            | High Freq. 8vCPU/32GB   | $192 (~R$960)   | Sim (Sao Paulo)   |
| HostGator Brasil | VPS Enterprise           | R$ 299          | Sim               |

**Recomendacao**: Iniciar com **Hetzner CPX41** ou **Contabo VPS XL** pelo custo-beneficio. A latencia da Europa para o Brasil e aceitavel (~150ms) e o Cloudflare CDN mitiga isso para o frontend.

### Armazenamento de Midia (NAS/Storage Adicional)

| Especificacao         | Recomendado                                    |
|-----------------------|------------------------------------------------|
| **Tipo**              | NAS dedicado ou volume adicional no VPS        |
| **Capacidade**        | 2 TB inicial (expansivel)                      |
| **Uso**               | Videos de producao, fotos, PDFs, backups       |
| **Backup**            | Replicado para Cloudflare R2 ou Backblaze B2   |
| **Custo**             | ~R$ 50-100/mes (Hetzner Storage Box 2TB)       |

### Distribuicao de RAM por Servico

```
Total: 32 GB RAM
├── PostgreSQL:      8 GB  (shared_buffers=4GB, effective_cache=6GB)
├── Redis:           2 GB  (maxmemory=2gb)
├── Qdrant:          4 GB  (vetores em memoria)
├── Backend (4w):    4 GB  (1GB por worker Uvicorn)
├── Celery (4w):     4 GB  (1GB por worker)
├── Frontend (2i):   2 GB  (1GB por instancia Next.js)
├── MinIO:           2 GB
├── Nginx:           512 MB
├── Monitoramento:   2 GB  (Prometheus + Grafana + Loki)
├── SO + Docker:     3.5 GB
└── Reserva:         0 GB
```

---

## 4. Sistema Operacional e Configuracao Base

### Ubuntu 22.04 LTS

```bash
# Atualizacao inicial
sudo apt update && sudo apt upgrade -y

# Pacotes essenciais
sudo apt install -y \
    curl wget git htop iotop ncdu \
    ufw fail2ban \
    apt-transport-https ca-certificates \
    software-properties-common \
    unattended-upgrades

# Configurar atualizacoes automaticas de seguranca
sudo dpkg-reconfigure -plow unattended-upgrades

# Configurar timezone
sudo timedatectl set-timezone America/Sao_Paulo

# Configurar locale
sudo locale-gen pt_BR.UTF-8
sudo update-locale LANG=pt_BR.UTF-8

# Configurar swap (se necessario)
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Otimizacoes de kernel para alto throughput
cat << 'EOF' | sudo tee -a /etc/sysctl.conf
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_tw_reuse = 1
vm.overcommit_memory = 1
vm.swappiness = 10
fs.file-max = 2097152
EOF
sudo sysctl -p
```

### Instalacao do Docker

```bash
# Remover versoes antigas
sudo apt remove -y docker docker-engine docker.io containerd runc

# Adicionar repositorio oficial
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Adicionar usuario ao grupo docker
sudo usermod -aG docker $USER

# Configurar Docker daemon
cat << 'EOF' | sudo tee /etc/docker/daemon.json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2",
  "default-address-pools": [
    {"base": "172.20.0.0/16", "size": 24}
  ]
}
EOF

sudo systemctl restart docker
sudo systemctl enable docker
```

---

## 5. Docker e Containerizacao

### docker-compose.yml (Producao)

```yaml
version: "3.9"

services:
  # ============================================
  # NGINX - Reverse Proxy
  # ============================================
  nginx:
    image: nginx:alpine
    container_name: somos-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./certbot/conf:/etc/letsencrypt:ro
      - ./certbot/www:/var/www/certbot:ro
    depends_on:
      - frontend
      - backend
    restart: always
    networks:
      - somos-network

  certbot:
    image: certbot/certbot
    container_name: somos-certbot
    volumes:
      - ./certbot/conf:/etc/letsencrypt
      - ./certbot/www:/var/www/certbot
    entrypoint: "/bin/sh -c 'trap exit TERM; while :; do certbot renew; sleep 12h & wait $${!}; done;'"
    networks:
      - somos-network

  # ============================================
  # FRONTEND - Next.js
  # ============================================
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      target: production
    container_name: somos-frontend
    environment:
      - NEXT_PUBLIC_API_URL=https://app.somosprodutora.com.br/api
      - NODE_ENV=production
    restart: always
    deploy:
      replicas: 2
      resources:
        limits:
          memory: 1G
    networks:
      - somos-network

  # ============================================
  # BACKEND - FastAPI
  # ============================================
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
      target: production
    container_name: somos-backend
    environment:
      - DATABASE_URL=postgresql+asyncpg://somos:${DB_PASSWORD}@postgres:5432/somos
      - REDIS_URL=redis://redis:6379/0
      - QDRANT_URL=http://qdrant:6333
      - MINIO_ENDPOINT=minio:9000
      - MINIO_ACCESS_KEY=${MINIO_ACCESS_KEY}
      - MINIO_SECRET_KEY=${MINIO_SECRET_KEY}
      - SECRET_KEY=${SECRET_KEY}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - GOOGLE_AI_API_KEY=${GOOGLE_AI_API_KEY}
    command: >
      gunicorn app.main:app
      --workers 4
      --worker-class uvicorn.workers.UvicornWorker
      --bind 0.0.0.0:8000
      --timeout 120
      --graceful-timeout 30
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
      qdrant:
        condition: service_started
    restart: always
    deploy:
      resources:
        limits:
          memory: 4G
    networks:
      - somos-network

  # ============================================
  # CELERY WORKER - Tarefas em Background
  # ============================================
  celery-worker:
    build:
      context: ./backend
      dockerfile: Dockerfile
      target: production
    container_name: somos-celery-worker
    command: >
      celery -A app.celery_app worker
      --loglevel=info
      --concurrency=4
      --max-tasks-per-child=100
      -Q default,agents,notifications,reports
    environment:
      - DATABASE_URL=postgresql+asyncpg://somos:${DB_PASSWORD}@postgres:5432/somos
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/1
    depends_on:
      - redis
      - postgres
    restart: always
    deploy:
      resources:
        limits:
          memory: 4G
    networks:
      - somos-network

  celery-beat:
    build:
      context: ./backend
      dockerfile: Dockerfile
      target: production
    container_name: somos-celery-beat
    command: >
      celery -A app.celery_app beat
      --loglevel=info
      --schedule=/tmp/celerybeat-schedule
    environment:
      - REDIS_URL=redis://redis:6379/0
      - CELERY_BROKER_URL=redis://redis:6379/1
    depends_on:
      - redis
    restart: always
    networks:
      - somos-network

  # ============================================
  # POSTGRESQL
  # ============================================
  postgres:
    image: postgres:16-alpine
    container_name: somos-postgres
    environment:
      - POSTGRES_DB=somos
      - POSTGRES_USER=somos
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./backups/postgres:/backups
    ports:
      - "127.0.0.1:5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U somos"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: always
    deploy:
      resources:
        limits:
          memory: 8G
    command: >
      postgres
      -c shared_buffers=4GB
      -c effective_cache_size=6GB
      -c work_mem=64MB
      -c maintenance_work_mem=512MB
      -c max_connections=200
      -c wal_buffers=64MB
      -c checkpoint_completion_target=0.9
      -c random_page_cost=1.1
      -c log_min_duration_statement=1000
    networks:
      - somos-network

  # ============================================
  # REDIS
  # ============================================
  redis:
    image: redis:7-alpine
    container_name: somos-redis
    command: >
      redis-server
      --maxmemory 2gb
      --maxmemory-policy allkeys-lru
      --appendonly yes
      --requirepass ${REDIS_PASSWORD}
    volumes:
      - redis_data:/data
    ports:
      - "127.0.0.1:6379:6379"
    healthcheck:
      test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: always
    deploy:
      resources:
        limits:
          memory: 2G
    networks:
      - somos-network

  # ============================================
  # QDRANT - Banco Vetorial
  # ============================================
  qdrant:
    image: qdrant/qdrant:latest
    container_name: somos-qdrant
    volumes:
      - qdrant_data:/qdrant/storage
    ports:
      - "127.0.0.1:6333:6333"
    environment:
      - QDRANT__SERVICE__API_KEY=${QDRANT_API_KEY}
    restart: always
    deploy:
      resources:
        limits:
          memory: 4G
    networks:
      - somos-network

  # ============================================
  # MINIO - Object Storage
  # ============================================
  minio:
    image: minio/minio:latest
    container_name: somos-minio
    command: server /data --console-address ":9001"
    environment:
      - MINIO_ROOT_USER=${MINIO_ACCESS_KEY}
      - MINIO_ROOT_PASSWORD=${MINIO_SECRET_KEY}
    volumes:
      - minio_data:/data
    ports:
      - "127.0.0.1:9000:9000"
      - "127.0.0.1:9001:9001"
    restart: always
    deploy:
      resources:
        limits:
          memory: 2G
    networks:
      - somos-network

  # ============================================
  # MONITORAMENTO
  # ============================================
  prometheus:
    image: prom/prometheus:latest
    container_name: somos-prometheus
    volumes:
      - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    ports:
      - "127.0.0.1:9090:9090"
    restart: always
    networks:
      - somos-network

  grafana:
    image: grafana/grafana:latest
    container_name: somos-grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_PASSWORD}
      - GF_SERVER_ROOT_URL=https://monitor.somosprodutora.com.br
    volumes:
      - grafana_data:/var/lib/grafana
      - ./monitoring/grafana/dashboards:/etc/grafana/provisioning/dashboards
      - ./monitoring/grafana/datasources:/etc/grafana/provisioning/datasources
    ports:
      - "127.0.0.1:3001:3000"
    restart: always
    networks:
      - somos-network

  loki:
    image: grafana/loki:latest
    container_name: somos-loki
    volumes:
      - loki_data:/loki
    ports:
      - "127.0.0.1:3100:3100"
    restart: always
    networks:
      - somos-network

  node-exporter:
    image: prom/node-exporter:latest
    container_name: somos-node-exporter
    ports:
      - "127.0.0.1:9100:9100"
    restart: always
    networks:
      - somos-network

volumes:
  postgres_data:
  redis_data:
  qdrant_data:
  minio_data:
  prometheus_data:
  grafana_data:
  loki_data:

networks:
  somos-network:
    driver: bridge
```

---

## 6. Nginx Reverse Proxy

### Configuracao Principal (`nginx/nginx.conf`)

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 4096;
    multi_accept on;
    use epoll;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for" '
                    'rt=$request_time';
    access_log /var/log/nginx/access.log main;

    # Performance
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    client_max_body_size 100M;

    # Gzip
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css application/json application/javascript
               text/xml application/xml application/xml+rss text/javascript
               image/svg+xml;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';" always;

    # Rate limiting zones
    limit_req_zone $binary_remote_addr zone=api:10m rate=30r/s;
    limit_req_zone $binary_remote_addr zone=login:10m rate=5r/m;

    include /etc/nginx/conf.d/*.conf;
}
```

### Configuracao do Site (`nginx/conf.d/somos.conf`)

```nginx
upstream backend_servers {
    server backend:8000;
}

upstream frontend_servers {
    server frontend:3000;
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name app.somosprodutora.com.br;
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }
    location / {
        return 301 https://$host$request_uri;
    }
}

# Main HTTPS server
server {
    listen 443 ssl http2;
    server_name app.somosprodutora.com.br;

    # SSL
    ssl_certificate /etc/letsencrypt/live/somosprodutora.com.br/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/somosprodutora.com.br/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # HSTS
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;

    # API Backend
    location /api/ {
        limit_req zone=api burst=50 nodelay;
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 120s;
    }

    # Login rate limiting
    location /api/v1/auth/login {
        limit_req zone=login burst=3 nodelay;
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # WebSocket
    location /ws/ {
        proxy_pass http://backend_servers;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_read_timeout 86400;
    }

    # MinIO storage (pre-signed URLs)
    location /storage/ {
        proxy_pass http://minio:9000/;
        proxy_set_header Host $host;
    }

    # Frontend
    location / {
        proxy_pass http://frontend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # Static assets caching
    location /_next/static/ {
        proxy_pass http://frontend_servers;
        expires 365d;
        add_header Cache-Control "public, immutable";
    }
}
```

---

## 7. Seguranca do Servidor

### UFW Firewall

```bash
# Resetar regras
sudo ufw reset

# Politica padrao: bloquear tudo de entrada, permitir saida
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir SSH (porta customizada)
sudo ufw allow 2222/tcp comment 'SSH'

# Permitir HTTP e HTTPS
sudo ufw allow 80/tcp comment 'HTTP'
sudo ufw allow 443/tcp comment 'HTTPS'

# Ativar firewall
sudo ufw enable
sudo ufw status verbose
```

### SSH Hardening (`/etc/ssh/sshd_config`)

```
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
MaxAuthTries 3
LoginGraceTime 60
ClientAliveInterval 300
ClientAliveCountMax 2
AllowUsers deploy
X11Forwarding no
AllowTcpForwarding no
```

### Fail2ban

```ini
# /etc/fail2ban/jail.local
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3
action = %(action_mwl)s

[sshd]
enabled = true
port = 2222
maxretry = 3
bantime = 86400

[nginx-http-auth]
enabled = true

[nginx-limit-req]
enabled = true
logpath = /var/log/nginx/error.log
maxretry = 10
```

### Atualizacoes Automaticas de Seguranca

```bash
# /etc/apt/apt.conf.d/50unattended-upgrades
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
};
Unattended-Upgrade::AutoFixInterruptedDpkg "true";
Unattended-Upgrade::MinimalSteps "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "false";
```

---

## 8. CI/CD com GitHub Actions

### Pipeline Principal (`.github/workflows/deploy.yml`)

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # ==========================================
  # ETAPA 1: Lint
  # ==========================================
  lint:
    name: Lint & Type Check
    runs-on: ubuntu-latest
    strategy:
      matrix:
        include:
          - name: Backend
            working-directory: ./backend
            commands: |
              pip install ruff mypy
              ruff check .
              mypy app/
          - name: Frontend
            working-directory: ./frontend
            commands: |
              npm ci
              npm run lint
              npm run type-check
    steps:
      - uses: actions/checkout@v4
      - name: Run linting (${{ matrix.name }})
        working-directory: ${{ matrix.working-directory }}
        run: ${{ matrix.commands }}

  # ==========================================
  # ETAPA 2: Testes
  # ==========================================
  test-backend:
    name: Test Backend
    runs-on: ubuntu-latest
    needs: lint
    services:
      postgres:
        image: postgres:16-alpine
        env:
          POSTGRES_DB: somos_test
          POSTGRES_USER: somos
          POSTGRES_PASSWORD: test_password
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: "pip"
      - name: Install dependencies
        working-directory: ./backend
        run: pip install -r requirements/dev.txt
      - name: Run tests
        working-directory: ./backend
        run: pytest --cov=app --cov-report=xml -v
        env:
          DATABASE_URL: postgresql+asyncpg://somos:test_password@localhost:5432/somos_test
          REDIS_URL: redis://localhost:6379/0
      - name: Upload coverage
        uses: codecov/codecov-action@v4

  test-frontend:
    name: Test Frontend
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
          cache-dependency-path: ./frontend/package-lock.json
      - name: Install dependencies
        working-directory: ./frontend
        run: npm ci
      - name: Run tests
        working-directory: ./frontend
        run: npm run test -- --coverage

  # ==========================================
  # ETAPA 3: Build
  # ==========================================
  build:
    name: Build Docker Images
    runs-on: ubuntu-latest
    needs: [test-backend, test-frontend]
    if: github.ref == 'refs/heads/main'
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Build and push backend
        uses: docker/build-push-action@v5
        with:
          context: ./backend
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/backend:${{ github.sha }}
      - name: Build and push frontend
        uses: docker/build-push-action@v5
        with:
          context: ./frontend
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/frontend:${{ github.sha }}

  # ==========================================
  # ETAPA 4: Deploy
  # ==========================================
  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: deploy
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          port: 2222
          script: |
            cd /opt/somos
            git pull origin main
            docker compose pull
            docker compose up -d --remove-orphans
            docker compose exec backend alembic upgrade head
            docker image prune -f
            echo "Deploy concluido: $(date)"
```

---

## 9. Estrategia de Backups

### Backup Automatizado Diario

```bash
#!/bin/bash
# /opt/somos/scripts/backup.sh
# Executado via cron diariamente as 03:00

set -euo pipefail

BACKUP_DIR="/opt/somos/backups"
DATE=$(date +%Y-%m-%d_%H-%M-%S)
RETENTION_DAYS=30

echo "[$(date)] Iniciando backup..."

# 1. Backup PostgreSQL
echo "Backup PostgreSQL..."
docker compose exec -T postgres pg_dump -U somos somos | gzip > \
    "${BACKUP_DIR}/postgres/somos_${DATE}.sql.gz"

# 2. Backup Redis (RDB snapshot)
echo "Backup Redis..."
docker compose exec -T redis redis-cli -a ${REDIS_PASSWORD} BGSAVE
sleep 5
docker cp somos-redis:/data/dump.rdb "${BACKUP_DIR}/redis/redis_${DATE}.rdb"

# 3. Backup volumes Docker (Qdrant, configs)
echo "Backup Qdrant..."
tar czf "${BACKUP_DIR}/qdrant/qdrant_${DATE}.tar.gz" \
    -C /var/lib/docker/volumes/somos_qdrant_data/_data .

# 4. Backup configuracoes
echo "Backup configs..."
tar czf "${BACKUP_DIR}/configs/configs_${DATE}.tar.gz" \
    /opt/somos/docker-compose.yml \
    /opt/somos/nginx/ \
    /opt/somos/monitoring/ \
    /opt/somos/.env

# 5. Sync para storage externo
echo "Sincronizando para storage externo..."
rclone sync "${BACKUP_DIR}" remote:somos-backups/ \
    --transfers 4 --progress

# 6. Limpeza de backups antigos
echo "Limpando backups com mais de ${RETENTION_DAYS} dias..."
find "${BACKUP_DIR}" -name "*.gz" -mtime +${RETENTION_DAYS} -delete
find "${BACKUP_DIR}" -name "*.rdb" -mtime +${RETENTION_DAYS} -delete

echo "[$(date)] Backup concluido com sucesso!"
```

### Cron Schedule

```bash
# crontab -e (usuario deploy)
# Backup completo diario as 03:00
0 3 * * * /opt/somos/scripts/backup.sh >> /var/log/somos/backup.log 2>&1

# Backup incremental PostgreSQL WAL a cada hora
0 * * * * /opt/somos/scripts/wal-backup.sh >> /var/log/somos/wal-backup.log 2>&1

# Limpeza de logs antigos semanalmente
0 4 * * 0 /opt/somos/scripts/cleanup-logs.sh >> /var/log/somos/cleanup.log 2>&1
```

### Politica de Retencao

| Tipo de Backup          | Frequencia  | Retencao   | Destino                     |
|-------------------------|-------------|------------|-----------------------------|
| PostgreSQL full dump     | Diario 03h  | 30 dias    | Local + Cloudflare R2       |
| PostgreSQL WAL archive   | Horario     | 7 dias     | Local                       |
| Redis RDB snapshot       | Diario 03h  | 14 dias    | Local + Cloudflare R2       |
| Qdrant vetores           | Diario 03h  | 14 dias    | Local                       |
| Configuracoes            | Diario 03h  | 90 dias    | Local + Cloudflare R2       |
| Midia (MinIO)            | Semanal     | Indefinido | NAS + Cloudflare R2         |

---

## 10. Monitoramento e Observabilidade

### Prometheus (`monitoring/prometheus.yml`)

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']

rule_files:
  - "/etc/prometheus/alerts/*.yml"

scrape_configs:
  - job_name: 'fastapi'
    static_configs:
      - targets: ['backend:8000']
    metrics_path: '/metrics'

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']

  - job_name: 'postgres'
    static_configs:
      - targets: ['postgres-exporter:9187']

  - job_name: 'redis'
    static_configs:
      - targets: ['redis-exporter:9121']

  - job_name: 'nginx'
    static_configs:
      - targets: ['nginx-exporter:9113']

  - job_name: 'celery'
    static_configs:
      - targets: ['flower:5555']
```

### Dashboards Grafana Recomendados

| Dashboard                        | Metricas Chave                                         |
|----------------------------------|--------------------------------------------------------|
| **Visao Geral do Sistema**       | CPU, RAM, disco, rede de todos os containers           |
| **API FastAPI**                  | Requests/s, latencia p50/p95/p99, erros por endpoint   |
| **PostgreSQL**                   | Queries/s, conexoes ativas, cache hit ratio, locks      |
| **Redis**                        | Comandos/s, memoria usada, hit rate, keys expiradas    |
| **Celery Workers**               | Tarefas/min, tarefas na fila, tempo de execucao        |
| **Agentes IA**                   | Tokens consumidos, custo/dia, taxa sucesso/falha       |
| **Negocio**                      | Leads/dia, conversoes, deals em pipeline, receita       |

### Alertas Criticos

```yaml
# monitoring/prometheus/alerts/critical.yml
groups:
  - name: critical_alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 90
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "CPU acima de 90% por mais de 5 minutos"

      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 90
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Memoria acima de 90%"

      - alert: DiskSpaceLow
        expr: (node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100 < 10
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Disco com menos de 10% livre"

      - alert: PostgreSQLDown
        expr: pg_up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "PostgreSQL esta fora do ar"

      - alert: HighAPILatency
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 2
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "Latencia p95 da API acima de 2 segundos"

      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Taxa de erro 5xx acima de 5%"

      - alert: CeleryQueueBacklog
        expr: celery_tasks_queue_length > 1000
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Mais de 1000 tarefas na fila Celery"
```

---

## 11. Plano de Recuperacao de Desastres

### Classificacao de Incidentes

| Nivel    | Descricao                                           | Tempo Resposta | Tempo Resolucao |
|----------|-----------------------------------------------------|----------------|-----------------|
| **P1**   | Sistema completamente fora do ar                    | 15 minutos     | 1 hora          |
| **P2**   | Funcionalidade critica indisponivel (pagamentos)    | 30 minutos     | 2 horas         |
| **P3**   | Funcionalidade secundaria com problema              | 2 horas        | 8 horas         |
| **P4**   | Bug menor, sem impacto em producao                  | 24 horas       | 72 horas        |

### Procedimento de Recuperacao Total

1. **Provisionar novo servidor** (VPS com mesmas specs)
2. **Instalar Docker** (script automatizado em `scripts/setup-server.sh`)
3. **Restaurar configuracoes** do backup em Cloudflare R2
4. **Restaurar PostgreSQL** a partir do dump mais recente
5. **Restaurar Redis** a partir do RDB snapshot
6. **Restaurar Qdrant** a partir do backup de vetores
7. **Atualizar DNS** no Cloudflare para novo IP
8. **Verificar integridade** dos dados e funcionalidades

**RTO (Recovery Time Objective)**: 2 horas
**RPO (Recovery Point Objective)**: 1 hora (com WAL archiving)

### Testes de Recuperacao

- **Mensal**: Restaurar backup do PostgreSQL em ambiente de teste
- **Trimestral**: Simulacao completa de desastre com provisionamento de novo servidor
- **Semestral**: Revisao completa do plano com toda a equipe

---

## 12. Estrategia de Escalabilidade

### Fase 1: Vertical (Atual)

Servidor unico com Docker Compose. Escalar adicionando mais CPU/RAM ao VPS.

**Capacidade estimada**: ate 100 usuarios simultaneos, 50 agentes IA ativos.

### Fase 2: Multi-servidor

Separar servicos em servidores dedicados:

```
Servidor 1 (App):      Frontend + Backend + Nginx
Servidor 2 (Database): PostgreSQL + Redis + Qdrant
Servidor 3 (Workers):  Celery Workers + Agentes IA
Servidor 4 (Storage):  MinIO + Backups
```

**Trigger**: Quando uso de CPU/RAM consistentemente acima de 70%.

### Fase 3: Docker Swarm

Orquestracao com Docker Swarm para replicacao automatica:

```bash
# Inicializar Swarm
docker swarm init --advertise-addr <IP_PRINCIPAL>

# Adicionar workers
docker swarm join --token <TOKEN> <IP_PRINCIPAL>:2377

# Deploy do stack
docker stack deploy -c docker-compose.yml somos
```

**Trigger**: Quando necessario mais de 3 servidores.

### Fase 4: Kubernetes (Futuro)

Migracao para Kubernetes apenas se a escala justificar a complexidade operacional. Usar managed Kubernetes (DigitalOcean DOKS, GKE) ao inves de self-hosted.

**Trigger**: Mais de 10 servidores ou necessidade de auto-scaling sofisticado.

---

## Checklist de Lancamento em Producao

- [ ] VPS provisionada e configurada
- [ ] UFW ativado com regras corretas
- [ ] SSH com chaves (senha desativada)
- [ ] Fail2ban configurado e ativo
- [ ] Docker e Docker Compose instalados
- [ ] Certificado SSL via Let's Encrypt
- [ ] Todas as variaveis de ambiente em `.env`
- [ ] Backup automatico configurado e testado
- [ ] Monitoramento (Prometheus + Grafana) ativo
- [ ] Sentry configurado para erros
- [ ] DNS apontando para o servidor (Cloudflare)
- [ ] Teste de carga basico executado
- [ ] Plano de rollback documentado
- [ ] Acesso de emergencia testado

---

*Documento mantido pela equipe de infraestrutura da Somos Produtora.*
