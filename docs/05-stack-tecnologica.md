# 05 - Stack Tecnologica

> Somos Produtora - Documentacao de Arquitetura Tecnologica
> Versao: 1.0 | Ultima atualizacao: 2026-02-28

---

## Indice

1. [Visao Geral](#visao-geral)
2. [Backend](#backend)
3. [Frontend](#frontend)
4. [Mobile](#mobile)
5. [Banco de Dados](#banco-de-dados)
6. [Agentes de IA](#agentes-de-ia)
7. [Infraestrutura](#infraestrutura)
8. [Monitoramento](#monitoramento)
9. [Tabelas Comparativas](#tabelas-comparativas)
10. [Versionamento e Compatibilidade](#versionamento-e-compatibilidade)

---

## 1. Visao Geral

A stack tecnologica da Somos Produtora foi selecionada com base em cinco criterios fundamentais:

1. **Desempenho** - Capacidade de lidar com operacoes concorrentes (agentes IA, requisicoes web, processamento de midia)
2. **Ecossistema de IA** - Compatibilidade nativa com bibliotecas de inteligencia artificial e modelos de linguagem
3. **Velocidade de Desenvolvimento** - Time-to-market reduzido com ferramentas modernas e produtivas
4. **Custo Operacional** - Preferencia por tecnologias open-source e self-hosted quando viavel
5. **Escalabilidade** - Arquitetura que permita crescimento gradual sem reescritas

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENTES                                 │
│              (Browser / App Mobile / WhatsApp)                  │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                    ┌───────▼───────┐
                    │     Nginx     │
                    │ Reverse Proxy │
                    │  + SSL/TLS    │
                    └───────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
      ┌───────▼──────┐ ┌───▼────┐ ┌──────▼──────┐
      │   Next.js    │ │FastAPI │ │  MinIO      │
      │   Frontend   │ │Backend │ │  Storage    │
      │   (SSR/SSG)  │ │(Async) │ │  (S3)      │
      └──────────────┘ └───┬────┘ └─────────────┘
                           │
           ┌───────────────┼───────────────┐
           │               │               │
   ┌───────▼──────┐ ┌─────▼─────┐ ┌───────▼──────┐
   │ PostgreSQL   │ │   Redis   │ │   Qdrant     │
   │ (Relacional) │ │  (Cache)  │ │ (Vetorial)   │
   └──────────────┘ └───────────┘ └──────────────┘
                           │
                    ┌──────▼──────┐
                    │  CrewAI +   │
                    │  LangChain  │
                    │  (Agentes)  │
                    └─────────────┘
```

---

## 2. Backend

### Python 3.12 + FastAPI 0.110+

| Aspecto              | Detalhe                                                    |
|----------------------|------------------------------------------------------------|
| **Linguagem**        | Python 3.12                                                |
| **Framework**        | FastAPI 0.110+                                             |
| **ASGI Server**      | Uvicorn + Gunicorn (workers)                               |
| **ORM**              | SQLAlchemy 2.0 (async)                                     |
| **Migracoes**        | Alembic                                                    |
| **Validacao**        | Pydantic v2                                                |
| **Task Queue**       | Celery + Redis (broker) ou ARQ (alternativa async)         |
| **Testes**           | Pytest + pytest-asyncio + httpx (async test client)        |
| **Linting**          | Ruff (substitui flake8/isort/black) + mypy                 |
| **Docs API**         | Swagger UI + ReDoc (gerados automaticamente pelo FastAPI)  |

### Justificativas para Python + FastAPI

**Por que Python?**

- **Lingua franca da IA/ML**: Todas as principais bibliotecas de IA (LangChain, CrewAI, Transformers, OpenAI SDK, Anthropic SDK) sao nativas em Python
- **Ecossistema maduro**: Mais de 400.000 pacotes no PyPI, cobrindo praticamente qualquer necessidade
- **Type hints modernos**: Python 3.12 oferece type hints robustos que o FastAPI aproveita para validacao automatica
- **Contratacao facilitada**: Grande pool de desenvolvedores Python no mercado brasileiro
- **Prototipagem rapida**: Ideal para iterar rapidamente em funcionalidades de agentes IA

**Por que FastAPI (e nao Django/Flask)?**

- **Performance async nativa**: Suporta `async/await` nativamente, essencial para chamadas concorrentes a APIs de IA
- **Validacao automatica**: Pydantic v2 valida requests/responses automaticamente com base nos type hints
- **Documentacao automatica**: Gera OpenAPI (Swagger) e ReDoc sem configuracao adicional
- **WebSockets nativos**: Suporte integrado para comunicacao em tempo real (chat, notificacoes)
- **Dependency Injection**: Sistema elegante de injecao de dependencias para auth, DB sessions, etc.

### Estrutura do Backend

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                 # Ponto de entrada FastAPI
│   ├── config.py               # Configuracoes (Pydantic Settings)
│   ├── database.py             # Sessao async SQLAlchemy
│   ├── api/
│   │   ├── v1/
│   │   │   ├── auth.py         # Login, registro, refresh token
│   │   │   ├── users.py        # CRUD usuarios
│   │   │   ├── leads.py        # CRUD leads
│   │   │   ├── deals.py        # Pipeline comercial
│   │   │   ├── projects.py     # Gestao de projetos
│   │   │   ├── finance.py      # Financeiro
│   │   │   ├── agents.py       # Controle de agentes IA
│   │   │   ├── content.py      # Gestao de conteudo
│   │   │   └── reports.py      # Relatorios
│   │   └── deps.py             # Dependencias compartilhadas
│   ├── models/                 # SQLAlchemy models
│   ├── schemas/                # Pydantic schemas
│   ├── services/               # Logica de negocio
│   ├── agents/                 # Agentes CrewAI/LangChain
│   │   ├── hunters/
│   │   ├── sdrs/
│   │   ├── content/
│   │   └── analysis/
│   ├── integrations/           # APIs externas
│   │   ├── whatsapp.py
│   │   ├── linkedin.py
│   │   ├── openai_client.py
│   │   ├── anthropic_client.py
│   │   └── serpapi.py
│   ├── core/
│   │   ├── security.py         # JWT, hashing, RBAC
│   │   ├── middleware.py       # CORS, rate limiting, logging
│   │   └── exceptions.py      # Handlers de erro
│   └── utils/
├── alembic/                    # Migracoes de banco
├── tests/
├── requirements/
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
└── Dockerfile
```

### Dependencias Principais (requirements/base.txt)

```
fastapi>=0.110.0
uvicorn[standard]>=0.27.0
sqlalchemy[asyncio]>=2.0.25
asyncpg>=0.29.0          # Driver async PostgreSQL
alembic>=1.13.0
pydantic>=2.6.0
pydantic-settings>=2.1.0
python-jose[cryptography]>=3.3.0   # JWT
passlib[bcrypt]>=1.7.4
python-multipart>=0.0.6
celery>=5.3.6
redis>=5.0.1
httpx>=0.26.0             # HTTP client async
langchain>=0.1.0
crewai>=0.1.0
openai>=1.12.0
anthropic>=0.18.0
qdrant-client>=1.7.0
boto3>=1.34.0             # MinIO (compativel S3)
pillow>=10.2.0
jinja2>=3.1.3             # Templates de email/documentos
sentry-sdk[fastapi]>=1.40.0
prometheus-fastapi-instrumentator>=6.1.0
```

---

## 3. Frontend

### Next.js 15 + TypeScript + Tailwind CSS + ShadcnUI

| Aspecto              | Detalhe                                                   |
|----------------------|-----------------------------------------------------------|
| **Framework**        | Next.js 15 (App Router)                                   |
| **Linguagem**        | TypeScript 5.4+                                           |
| **Estilizacao**      | Tailwind CSS 3.4+                                         |
| **Componentes UI**   | ShadcnUI (baseado em Radix UI)                            |
| **Gerenciador de Estado** | Zustand (global) + React Query/TanStack Query (server) |
| **Formularios**      | React Hook Form + Zod (validacao)                         |
| **Graficos**         | Recharts ou Tremor                                        |
| **Tabelas**          | TanStack Table v8                                         |
| **Drag & Drop**      | dnd-kit (Kanban boards)                                   |
| **Editor de Texto**  | TipTap (rich text editor)                                 |
| **Icones**           | Lucide React                                              |
| **Testes**           | Vitest + Testing Library + Playwright (E2E)               |
| **Linting**          | ESLint + Prettier + Biome                                 |

### Justificativas para Next.js 15

**Por que Next.js (e nao Vite+React/Remix/Nuxt)?**

- **SSR + SSG hibrido**: Renderizacao server-side para SEO e performance, static generation para paginas que nao mudam frequentemente
- **App Router**: React Server Components reduzem JavaScript enviado ao cliente
- **API Routes**: Possibilidade de criar BFF (Backend for Frontend) leve
- **Image Optimization**: Otimizacao automatica de imagens (importante para produtora audiovisual)
- **Middleware nativo**: Interceptacao de requisicoes para auth, redirecionamento, etc.
- **Ecossistema Vercel**: Opcao de deploy facilitado se necessario no futuro

**Por que ShadcnUI?**

- **Nao e uma dependencia**: Componentes sao copiados para o projeto, sem lock-in
- **Baseado em Radix UI**: Acessibilidade (a11y) nativa e robusta
- **Altamente customizavel**: Facil adaptar ao branding da Somos Produtora
- **Consistencia**: Design system unificado sem esforco adicional
- **Componentes prontos**: Dialog, Dropdown, DataTable, Calendar, Charts, etc.

### Estrutura do Frontend

```
frontend/
├── src/
│   ├── app/
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   └── register/
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx          # Dashboard principal
│   │   │   ├── leads/
│   │   │   ├── deals/
│   │   │   ├── projects/
│   │   │   ├── finance/
│   │   │   ├── content/
│   │   │   ├── agents/
│   │   │   ├── reports/
│   │   │   └── settings/
│   │   ├── layout.tsx
│   │   └── globals.css
│   ├── components/
│   │   ├── ui/                   # ShadcnUI components
│   │   ├── forms/
│   │   ├── layouts/
│   │   ├── charts/
│   │   └── shared/
│   ├── hooks/
│   ├── lib/
│   │   ├── api.ts               # HTTP client (axios/fetch wrapper)
│   │   ├── auth.ts
│   │   └── utils.ts
│   ├── stores/                  # Zustand stores
│   ├── types/
│   └── styles/
├── public/
├── tests/
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── Dockerfile
```

---

## 4. Mobile

### React Native + Expo

| Aspecto              | Detalhe                                              |
|----------------------|------------------------------------------------------|
| **Framework**        | React Native 0.73+                                   |
| **Toolchain**        | Expo SDK 50+                                         |
| **Linguagem**        | TypeScript                                           |
| **Navegacao**        | Expo Router (baseado em file-system)                 |
| **UI Components**    | React Native Paper ou NativeWind (Tailwind for RN)   |
| **Estado**           | Zustand (compartilhado com web)                      |
| **Notificacoes**     | Expo Notifications                                   |
| **Camera**           | Expo Camera (para captura em campo)                  |
| **Storage Local**    | Expo SecureStore (credenciais) + AsyncStorage        |
| **Plataformas**      | iOS + Android (codebase unico)                       |

### Justificativas para React Native

- **Reutilizacao de codigo**: TypeScript, Zustand stores, tipos e logica de negocio compartilhados com o frontend web
- **Codebase unico**: Uma unica base de codigo para iOS e Android
- **Expo**: Elimina complexidade de configuracao nativa, OTA updates sem publicar na App Store
- **Comunidade**: Maior ecossistema de componentes e bibliotecas para mobile multiplataforma
- **Performance**: Near-native com a nova arquitetura (Fabric + TurboModules)

### Funcionalidades Mobile Prioritarias

1. Dashboard resumido com KPIs
2. Notificacoes push de leads e tarefas
3. Aprovacao rapida de conteudo gerado por IA
4. Chat com clientes via WhatsApp integrado
5. Camera para registro de producoes em campo
6. Consulta offline de projetos e contratos

---

## 5. Banco de Dados

### PostgreSQL 16 (Relacional Principal)

| Aspecto              | Detalhe                                              |
|----------------------|------------------------------------------------------|
| **Versao**           | PostgreSQL 16                                        |
| **Driver Python**    | asyncpg (async) + psycopg2 (sync/migrations)        |
| **ORM**              | SQLAlchemy 2.0 (async mode)                          |
| **Migracoes**        | Alembic                                              |
| **Pool**             | SQLAlchemy async pool (min=5, max=20)                |
| **Extensoes**        | pgvector, pg_trgm, pg_stat_statements, uuid-ossp     |
| **Backup**           | pg_dump diario + WAL archiving para PITR             |

**Justificativa**: PostgreSQL e o banco relacional mais avancado open-source. Suporta JSON, full-text search, extensoes como pgvector (embeddings), e e extremamente confiavel para dados financeiros e contratuais.

### Redis 7 (Cache / Sessoes / Filas)

| Uso                  | Configuracao                                         |
|----------------------|------------------------------------------------------|
| **Cache de API**     | TTL de 5-60 minutos conforme endpoint                |
| **Sessoes JWT**      | Armazena refresh tokens com TTL                      |
| **Rate Limiting**    | Sliding window counter por IP/usuario                |
| **Filas Celery**     | Broker para task queue dos agentes IA                |
| **Pub/Sub**          | Notificacoes em tempo real via WebSocket             |
| **Memoria**          | Maximo 2GB, politica de eviction allkeys-lru         |

**Justificativa**: Redis oferece latencia sub-milissegundo, ideal para cache e como broker de mensagens. Elimina a necessidade de RabbitMQ separado.

### Qdrant (Banco de Dados Vetorial)

| Aspecto              | Detalhe                                              |
|----------------------|------------------------------------------------------|
| **Versao**           | Qdrant 1.8+                                          |
| **Uso**              | Busca semantica de leads, conteudo, documentos       |
| **Modelo Embedding** | text-embedding-3-small (OpenAI) ou all-MiniLM-L6-v2 |
| **Dimensoes**        | 1536 (OpenAI) ou 384 (MiniLM)                       |
| **Colecoes**         | leads, content, contracts, knowledge_base            |
| **Distancia**        | Cosine similarity                                    |

**Justificativa**: Qdrant e um banco vetorial nativo, mais performatico que pgvector para grandes volumes. Permite busca semantica: "encontre leads similares a este cliente ideal" ou "busque conteudos sobre producao audiovisual".

### MinIO (Object Storage S3-compatible)

| Aspecto              | Detalhe                                              |
|----------------------|------------------------------------------------------|
| **Versao**           | MinIO RELEASE.2024+                                  |
| **Uso**              | Armazenamento de midias (videos, fotos, PDFs)        |
| **Compatibilidade**  | API S3 completa (boto3 funciona nativamente)         |
| **Buckets**          | media-uploads, generated-content, backups, exports   |
| **Politica**         | Pre-signed URLs para acesso temporario               |
| **Replicacao**       | Erasure coding para redundancia local                |

**Justificativa**: MinIO e self-hosted, eliminando custos de S3 da AWS. Como produtora audiovisual, o volume de midia pode ser alto. MinIO escala facilmente adicionando discos.

---

## 6. Agentes de IA

### CrewAI + LangChain

| Aspecto              | Detalhe                                              |
|----------------------|------------------------------------------------------|
| **Orquestracao**     | CrewAI (coordenacao multi-agente)                    |
| **Chains/Tools**     | LangChain (ferramentas, prompts, memoria)            |
| **Modelos LLM**      | Claude 3.5 Sonnet, GPT-4o, Gemini Pro               |
| **Geracao Imagens**  | DALL-E 3, Flux (via Replicate)                       |
| **Embeddings**       | text-embedding-3-small (OpenAI)                      |
| **Memoria**          | Qdrant (longo prazo) + Redis (curto prazo)           |

### Selecao de Modelos por Tarefa

| Tarefa                        | Modelo Primario      | Fallback            | Justificativa                                    |
|-------------------------------|----------------------|---------------------|--------------------------------------------------|
| Raciocinio complexo           | Claude 3.5 Sonnet    | GPT-4o              | Melhor em analise profunda e nuances             |
| Redacao de conteudo           | Claude 3.5 Sonnet    | GPT-4o              | Qualidade superior em texto longo                |
| Classificacao/Scoring         | GPT-4o               | Gemini Pro          | Rapido e preciso para tarefas estruturadas       |
| Analise de dados/tendencias   | Gemini Pro           | GPT-4o              | Janela de contexto grande, bom em analise        |
| Geracao de imagens            | DALL-E 3             | Flux (Replicate)    | Alta qualidade, controle de estilo               |
| Embeddings/Busca semantica    | text-embedding-3-small| all-MiniLM-L6-v2   | Custo-beneficio para volume alto                 |
| Extracao de dados (scraping)  | GPT-4o-mini          | Gemini Flash        | Barato e rapido para tarefas simples             |

---

## 7. Infraestrutura

### Docker + Docker Compose

| Servico              | Imagem/Contexto                                      |
|----------------------|------------------------------------------------------|
| **backend**          | python:3.12-slim + app FastAPI                       |
| **frontend**         | node:20-alpine + Next.js build                       |
| **worker**           | Mesmo do backend + Celery worker                     |
| **beat**             | Celery beat (agendador)                              |
| **postgres**         | postgres:16-alpine                                   |
| **redis**            | redis:7-alpine                                       |
| **qdrant**           | qdrant/qdrant:latest                                 |
| **minio**            | minio/minio:latest                                   |
| **nginx**            | nginx:alpine + config customizado                    |
| **prometheus**       | prom/prometheus                                      |
| **grafana**          | grafana/grafana                                      |

### Nginx Reverse Proxy

```nginx
# Exemplo simplificado
upstream backend {
    server backend:8000;
}
upstream frontend {
    server frontend:3000;
}

server {
    listen 443 ssl http2;
    server_name app.somosprodutora.com.br;

    ssl_certificate /etc/letsencrypt/live/somosprodutora.com.br/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/somosprodutora.com.br/privkey.pem;

    location /api/ {
        proxy_pass http://backend;
    }
    location / {
        proxy_pass http://frontend;
    }
    location /storage/ {
        proxy_pass http://minio:9000;
    }
}
```

### GitHub Actions CI/CD

Pipeline: `lint` -> `test` -> `build` -> `deploy`

---

## 8. Monitoramento

### Grafana + Prometheus + Sentry

| Ferramenta     | Funcao                                                     |
|----------------|------------------------------------------------------------|
| **Prometheus** | Coleta de metricas (CPU, memoria, requests, latencia)      |
| **Grafana**    | Dashboards visuais, alertas por email/Telegram/Slack       |
| **Sentry**     | Rastreamento de erros/excecoes com stack traces             |
| **Loki**       | Agregacao de logs (alternativa ao ELK, mais leve)          |

### Metricas Monitoradas

- Tempo de resposta da API (p50, p95, p99)
- Taxa de erro por endpoint
- Uso de CPU/memoria por container
- Filas do Celery (tamanho, tempo de processamento)
- Consumo de tokens IA por agente por dia
- Taxa de sucesso/falha dos agentes
- Latencia de queries PostgreSQL
- Hit rate do cache Redis

---

## 9. Tabelas Comparativas

### Backend Framework

| Criterio                | FastAPI       | Django        | Flask         | Express.js    |
|-------------------------|---------------|---------------|---------------|---------------|
| Performance async       | Excelente     | Limitado      | Limitado      | Bom           |
| Ecossistema IA (Python) | Nativo        | Nativo        | Nativo        | Requer ponte  |
| Validacao automatica    | Pydantic v2   | DRF Serializers| Manual       | Zod/Joi       |
| Docs API automatica     | Sim (OpenAPI) | Sim (DRF)     | Nao           | Nao           |
| Curva de aprendizado    | Baixa         | Media         | Baixa         | Baixa         |
| Type safety             | Excelente     | Medio         | Fraco         | Bom (TS)      |
| WebSockets nativos      | Sim           | Channels      | Nao           | Sim           |
| **Escolha**             | **SIM**       | Nao           | Nao           | Nao           |

### Banco de Dados Principal

| Criterio                | PostgreSQL    | MySQL         | MongoDB       | SQLite        |
|-------------------------|---------------|---------------|---------------|---------------|
| Tipos avancados (JSON)  | Excelente     | Bom           | Nativo        | Limitado      |
| Full-text search        | Nativo        | Nativo        | Nativo        | Limitado      |
| Extensoes (pgvector)    | Sim           | Nao           | N/A           | Nao           |
| Confiabilidade ACID     | Excelente     | Bom           | Limitado      | Bom           |
| Escalabilidade          | Alta          | Alta          | Alta          | Baixa         |
| Custo                   | Gratuito      | Gratuito      | Gratuito*     | Gratuito      |
| **Escolha**             | **SIM**       | Nao           | Nao           | Nao           |

### Frontend Framework

| Criterio                | Next.js       | Vite+React    | Remix         | Nuxt (Vue)    |
|-------------------------|---------------|---------------|---------------|---------------|
| SSR/SSG                 | Ambos         | SPA apenas    | SSR           | Ambos         |
| React Server Components | Sim           | Nao           | Parcial       | N/A (Vue)     |
| Ecossistema             | Enorme        | Enorme        | Crescendo     | Grande        |
| Deploy facilitado       | Vercel        | Qualquer      | Fly.io        | Vercel/Netlify|
| Image optimization      | Nativo        | Manual        | Manual        | Nativo        |
| Compartilhar c/ mobile  | Sim (RN)      | Sim (RN)      | Nao           | Nao           |
| **Escolha**             | **SIM**       | Nao           | Nao           | Nao           |

### Geracao de Imagens

| Criterio                | DALL-E 3      | Midjourney    | Stable Diff.  | Flux          |
|-------------------------|---------------|---------------|---------------|---------------|
| Qualidade               | Excelente     | Excelente     | Bom           | Excelente     |
| API disponivel          | Sim           | Nao (Discord) | Sim           | Sim (Replicate)|
| Controle de estilo      | Bom           | Excelente     | Excelente     | Bom           |
| Custo por imagem        | ~$0.04-0.08   | $10/mo plan   | Self-hosted   | ~$0.03-0.05   |
| Integracao Python       | Nativa        | Nao oficial   | Diffusers     | Via Replicate |
| **Escolha**             | **Primario**  | Nao           | Nao           | **Fallback**  |

---

## 10. Versionamento e Compatibilidade

### Versoes Minimas Exigidas

| Tecnologia       | Versao Minima | Versao Recomendada | EOL Estimado    |
|------------------|---------------|---------------------|-----------------|
| Python           | 3.11          | 3.12                | Out 2028        |
| Node.js          | 20 LTS        | 20 LTS              | Abr 2026        |
| PostgreSQL       | 15            | 16                  | Nov 2028        |
| Redis            | 7.0           | 7.2                 | N/A             |
| Docker           | 24.0          | 25.0                | N/A             |
| Docker Compose   | 2.24          | 2.24+               | N/A             |

### Politica de Atualizacao

- **Dependencias criticas de seguranca**: Atualizar em ate 48 horas
- **Versoes minor/patch**: Atualizar semanalmente via Dependabot/Renovate
- **Versoes major**: Avaliar impacto, testar em staging, migrar em ate 30 dias
- **Modelos de IA**: Testar novos modelos a cada lancamento, migrar se houver ganho significativo de custo/qualidade

---

## Resumo Executivo de Custos de Ferramentas

| Categoria        | Ferramenta           | Custo Mensal Estimado |
|------------------|----------------------|-----------------------|
| Backend          | Python/FastAPI       | Gratuito (open-source)|
| Frontend         | Next.js/Tailwind     | Gratuito (open-source)|
| Banco de Dados   | PostgreSQL           | Gratuito (self-hosted)|
| Cache            | Redis                | Gratuito (self-hosted)|
| Vetorial         | Qdrant               | Gratuito (self-hosted)|
| Object Storage   | MinIO                | Gratuito (self-hosted)|
| Monitoramento    | Grafana/Prometheus   | Gratuito (self-hosted)|
| **Total Stack**  |                      | **R$ 0 em licencas**  |

> Nota: Os custos de infraestrutura (VPS, APIs de IA, APIs externas) estao detalhados nos documentos 06-infraestrutura.md e 08-integracoes.md.

---

*Documento mantido pela equipe de arquitetura da Somos Produtora.*
