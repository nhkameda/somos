# 09 - Banco de Dados

> Somos Produtora - Documentacao Completa do Banco de Dados e Persistencia
> Versao: 2.0 | Ultima atualizacao: 2026-02-28

---

## Indice

1. [Visao Geral](#visao-geral)
2. [Diagrama Entidade-Relacionamento](#diagrama-entidade-relacionamento)
3. [Esquema de Tabelas](#esquema-de-tabelas)
4. [Estrategia de Migracoes com Alembic](#estrategia-de-migracoes-com-alembic)
5. [Otimizacao de Indices](#otimizacao-de-indices)
6. [Redis - Cache e Sessoes](#redis---cache-e-sessoes)
7. [Qdrant - Busca Vetorial](#qdrant---busca-vetorial)
8. [MinIO - Armazenamento de Arquivos](#minio---armazenamento-de-arquivos)
9. [Particionamento de Dados](#particionamento-de-dados)
10. [Backup e Recuperacao](#backup-e-recuperacao)
11. [Convencoes e Padroes](#convencoes-e-padroes)

---

## 1. Visao Geral

O banco de dados principal da Somos Produtora utiliza **PostgreSQL 16** como SGBD relacional, acessado via **SQLAlchemy 2.0** (modo assincrono) com **asyncpg** como driver. As migracoes sao gerenciadas pelo **Alembic**. A arquitetura segue uma abordagem **poliglota**, combinando diferentes tecnologias de armazenamento conforme a natureza dos dados.

### Tecnologias de Persistencia

| Tecnologia | Versao | Finalidade |
|---|---|---|
| **PostgreSQL** | 16.x | Banco relacional principal (dados transacionais) |
| **Redis** | 7.x | Cache de aplicacao, sessoes, filas temporarias, rate limiting |
| **Qdrant** | 1.x | Busca vetorial para agentes de IA e similaridade semantica |
| **MinIO** | latest | Armazenamento de objetos (videos, imagens, documentos, backups) |

### Estatisticas Estimadas (Primeiro Ano)

| Metrica | Estimativa |
|---|---|
| Tabelas | 25+ |
| Registros totais | ~500K-2M |
| Tamanho do banco | ~5-20 GB |
| Conexoes simultaneas | 50-100 |
| Queries por segundo (pico) | 200-500 |
| Maior tabela | `agent_logs` (~1M+) |

### Extensoes PostgreSQL Utilizadas

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";        -- Geracao de UUIDs
CREATE EXTENSION IF NOT EXISTS "pg_trgm";           -- Busca por similaridade textual
CREATE EXTENSION IF NOT EXISTS "pgcrypto";          -- Funcoes criptograficas
CREATE EXTENSION IF NOT EXISTS "pg_stat_statements";-- Analise de queries
CREATE EXTENSION IF NOT EXISTS "btree_gin";         -- Indices GIN para tipos basicos
CREATE EXTENSION IF NOT EXISTS "pg_cron";           -- Agendamento de tarefas no banco
```

### Principios de Modelagem

- **Normalizacao ate 3FN** para tabelas transacionais, evitando redundancia
- **Soft delete** (`deleted_at`) em todas as tabelas com dados de usuario (conformidade LGPD)
- **Timestamps padronizados** (`created_at`, `updated_at`) em todas as tabelas
- **UUIDs v4** como chave primaria para evitar enumeracao e facilitar distribuicao futura
- **Auditoria embutida** com campos `created_by` e `updated_by` em tabelas sensiveis
- **Nomes em ingles** para tabelas e colunas (padrao tecnico), interface em portugues

---

## 2. Diagrama Entidade-Relacionamento

### Diagrama Mermaid (Visao Geral)

```mermaid
erDiagram
    USERS ||--o{ USER_ROLES : has
    ROLES ||--o{ USER_ROLES : has
    USERS ||--o{ LEADS : created_by
    USERS ||--o{ DEALS : owned_by
    USERS ||--o{ TASKS : assigned_to
    USERS ||--o{ NOTIFICATIONS : receives
    USERS ||--o{ AUDIT_LOGS : performs

    LEADS ||--o{ CONTACTS : has
    LEADS ||--|| COMPANIES : belongs_to
    LEADS ||--o{ DEALS : converts_to
    LEADS ||--o{ LEAD_INTERACTIONS : has

    COMPANIES ||--o{ CONTACTS : has
    COMPANIES ||--o{ CONTRACTS : has
    COMPANIES ||--o{ INVOICES : billed_to

    DEALS ||--|| PIPELINE_STAGES : is_in
    DEALS ||--o{ DEAL_ACTIVITIES : has

    CONTRACTS ||--o{ CONTRACT_ITEMS : has
    CONTRACTS ||--|| PROJECTS : generates

    PROJECTS ||--o{ SPRINTS : has
    PROJECTS ||--o{ TASKS : has
    PROJECTS ||--o{ BUDGETS : has
    PROJECTS ||--o{ EXPENSES : has

    SPRINTS ||--o{ TASKS : contains

    INVOICES ||--o{ PAYMENTS : has
    INVOICES ||--o{ INVOICE_ITEMS : has

    EXPENSES ||--o{ EXPENSE_ITEMS : has
    EXPENSES ||--|| SUPPLIERS : from

    EQUIPMENT ||--o{ EQUIPMENT_BOOKINGS : has

    CONTENT_PIECES ||--o{ SOCIAL_POSTS : generates
    CONTENT_PIECES ||--|| USERS : created_by

    AGENTS ||--o{ AGENT_LOGS : produces
    AGENTS ||--o{ AGENT_METRICS : has

    SUPPLIERS ||--o{ EXPENSES : provides
```

### Diagrama ASCII (Nucleo do Sistema)

```
┌──────────┐     ┌───────────┐     ┌──────────┐
│  USERS   │────▶│USER_ROLES │◀────│  ROLES   │
│          │     │           │     │          │
│ id (PK)  │     │ user_id   │     │ id (PK)  │
│ email    │     │ role_id   │     │ name     │
│ name     │     └───────────┘     │ perms    │
└────┬─────┘                       └──────────┘
     │
     │ created_by / owned_by
     ▼
┌──────────┐     ┌───────────┐     ┌────────────┐
│  LEADS   │────▶│   DEALS   │────▶│ PIPELINE   │
│          │     │           │     │ _STAGES    │
│ id (PK)  │     │ id (PK)   │     │            │
│ company  │     │ lead_id   │     │ id (PK)    │
│ status   │     │ stage_id  │     │ name       │
│ score    │     │ value     │     │ order      │
└────┬─────┘     └─────┬─────┘     └────────────┘
     │                 │
     ▼                 ▼
┌──────────┐     ┌───────────┐     ┌────────────┐
│COMPANIES │     │ CONTRACTS │────▶│  PROJECTS  │
│          │     │           │     │            │
│ id (PK)  │     │ id (PK)   │     │ id (PK)    │
│ name     │     │ company   │     │ contract   │
│ cnpj     │     │ value     │     │ name       │
│ segment  │     │ status    │     │ status     │
└──────────┘     └───────────┘     └─────┬──────┘
                                         │
                      ┌──────────────────┼──────────────────┐
                      ▼                  ▼                  ▼
               ┌───────────┐     ┌────────────┐     ┌───────────┐
               │  SPRINTS  │     │   TASKS    │     │  BUDGETS  │
               │           │     │            │     │           │
               │ id (PK)   │     │ id (PK)    │     │ id (PK)   │
               │ project   │     │ project    │     │ project   │
               │ name      │     │ sprint     │     │ total     │
               └───────────┘     │ assignee   │     └───────────┘
                                 └────────────┘
```

---

## 3. Esquema de Tabelas

### Convencoes Gerais

- Todos os IDs sao UUID v4 (gerados pelo PostgreSQL via `uuid_generate_v4()`)
- Todas as tabelas possuem `created_at` e `updated_at` com timestamps
- Soft delete via campo `deleted_at` (quando aplicavel, para conformidade LGPD)
- Nomes de tabelas no plural, snake_case
- Foreign keys com sufixo `_id`
- Enum types definidos como PostgreSQL ENUM

---

### 3.1 Tabela: `users`

Usuarios do sistema (humanos e contas de servico).

```sql
CREATE TABLE users (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email           VARCHAR(255) NOT NULL UNIQUE,
    password_hash   VARCHAR(255) NOT NULL,
    full_name       VARCHAR(255) NOT NULL,
    phone           VARCHAR(20),
    avatar_url      VARCHAR(500),
    is_active       BOOLEAN NOT NULL DEFAULT TRUE,
    is_verified     BOOLEAN NOT NULL DEFAULT FALSE,
    last_login_at   TIMESTAMPTZ,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_is_active ON users(is_active) WHERE is_active = TRUE;
```

---

### 3.2 Tabela: `roles`

Papeis de acesso do sistema (RBAC).

```sql
CREATE TYPE role_name AS ENUM ('admin', 'diretor', 'comercial', 'agente_ia', 'cliente');

CREATE TABLE roles (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name        role_name NOT NULL UNIQUE,
    description VARCHAR(255),
    permissions JSONB NOT NULL DEFAULT '{}',
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Dados iniciais
INSERT INTO roles (name, description, permissions) VALUES
('admin', 'Administrador com acesso total', '{"all": true}'),
('diretor', 'Diretor com acesso a relatorios e financeiro', '{"dashboard": true, "reports": true, "finance": true, "projects": true, "agents": true}'),
('comercial', 'Equipe comercial com acesso a leads e deals', '{"leads": true, "deals": true, "contacts": true, "content": {"read": true}}'),
('agente_ia', 'Conta de servico para agentes IA', '{"leads": {"read": true, "create": true}, "content": {"create": true}, "agents": {"read": true}}'),
('cliente', 'Cliente externo com acesso limitado', '{"projects": {"read": true}, "invoices": {"read": true}, "contracts": {"read": true}}');
```

---

### 3.3 Tabela: `user_roles`

Relacao N:N entre usuarios e papeis.

```sql
CREATE TABLE user_roles (
    id          UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id     UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role_id     UUID NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    granted_by  UUID REFERENCES users(id),
    granted_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE(user_id, role_id)
);

CREATE INDEX idx_user_roles_user_id ON user_roles(user_id);
CREATE INDEX idx_user_roles_role_id ON user_roles(role_id);
```

---

### 3.4 Tabela: `companies`

Empresas (clientes e prospects).

```sql
CREATE TABLE companies (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name            VARCHAR(255) NOT NULL,
    trade_name      VARCHAR(255),
    cnpj            VARCHAR(18) UNIQUE,
    segment         VARCHAR(100),
    size            VARCHAR(20) CHECK (size IN ('micro', 'pequena', 'media', 'grande')),
    website         VARCHAR(500),
    linkedin_url    VARCHAR(500),
    instagram_url   VARCHAR(500),
    address_street  VARCHAR(255),
    address_city    VARCHAR(100),
    address_state   VARCHAR(2),
    address_zip     VARCHAR(10),
    notes           TEXT,
    source          VARCHAR(50),
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ
);

CREATE INDEX idx_companies_cnpj ON companies(cnpj);
CREATE INDEX idx_companies_segment ON companies(segment);
CREATE INDEX idx_companies_name_trgm ON companies USING gin(name gin_trgm_ops);
```

---

### 3.5 Tabela: `contacts`

Contatos (pessoas dentro de empresas).

```sql
CREATE TABLE contacts (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    company_id      UUID REFERENCES companies(id),
    full_name       VARCHAR(255) NOT NULL,
    email           VARCHAR(255),
    phone           VARCHAR(20),
    whatsapp        VARCHAR(20),
    job_title       VARCHAR(150),
    department      VARCHAR(100),
    linkedin_url    VARCHAR(500),
    is_decision_maker BOOLEAN DEFAULT FALSE,
    notes           TEXT,
    source          VARCHAR(50),
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at      TIMESTAMPTZ
);

CREATE INDEX idx_contacts_company_id ON contacts(company_id);
CREATE INDEX idx_contacts_email ON contacts(email);
CREATE INDEX idx_contacts_name_trgm ON contacts USING gin(full_name gin_trgm_ops);
```

---

### 3.6 Tabela: `leads`

Leads (oportunidades de prospeccao).

```sql
CREATE TYPE lead_status AS ENUM ('new', 'contacted', 'qualified', 'unqualified', 'converted', 'lost');
CREATE TYPE lead_temperature AS ENUM ('hot', 'warm', 'cold');

CREATE TABLE leads (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    contact_id      UUID REFERENCES contacts(id),
    company_id      UUID REFERENCES companies(id),
    status          lead_status NOT NULL DEFAULT 'new',
    temperature     lead_temperature DEFAULT 'cold',
    score           INTEGER DEFAULT 0 CHECK (score >= 0 AND score <= 100),
    source          VARCHAR(50) NOT NULL,
    source_detail   VARCHAR(255),
    channel         VARCHAR(50),
    assigned_to     UUID REFERENCES users(id),
    first_contact_at TIMESTAMPTZ,
    last_contact_at  TIMESTAMPTZ,
    converted_at     TIMESTAMPTZ,
    notes           TEXT,
    metadata        JSONB DEFAULT '{}',
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_leads_status ON leads(status);
CREATE INDEX idx_leads_temperature ON leads(temperature);
CREATE INDEX idx_leads_score ON leads(score DESC);
CREATE INDEX idx_leads_assigned_to ON leads(assigned_to);
CREATE INDEX idx_leads_source ON leads(source);
CREATE INDEX idx_leads_created_at ON leads(created_at DESC);
CREATE INDEX idx_leads_company_id ON leads(company_id);
CREATE INDEX idx_leads_metadata ON leads USING gin(metadata);
```

---

### 3.7 Tabela: `lead_interactions`

Historico de interacoes com leads (mensagens, chamadas, emails).

```sql
CREATE TYPE interaction_type AS ENUM (
    'whatsapp_sent', 'whatsapp_received',
    'linkedin_sent', 'linkedin_received',
    'email_sent', 'email_opened', 'email_clicked', 'email_replied',
    'call', 'meeting', 'note'
);

CREATE TABLE lead_interactions (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    lead_id         UUID NOT NULL REFERENCES leads(id) ON DELETE CASCADE,
    interaction_type interaction_type NOT NULL,
    direction       VARCHAR(10) CHECK (direction IN ('inbound', 'outbound')),
    channel         VARCHAR(50) NOT NULL,
    subject         VARCHAR(255),
    content         TEXT,
    metadata        JSONB DEFAULT '{}',
    performed_by    UUID REFERENCES users(id),
    agent_id        UUID REFERENCES agents(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_lead_interactions_lead_id ON lead_interactions(lead_id);
CREATE INDEX idx_lead_interactions_type ON lead_interactions(interaction_type);
CREATE INDEX idx_lead_interactions_created_at ON lead_interactions(created_at DESC);
```

---

### 3.8 Tabela: `pipeline_stages`

Estagios do pipeline comercial.

```sql
CREATE TABLE pipeline_stages (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name            VARCHAR(100) NOT NULL,
    slug            VARCHAR(100) NOT NULL UNIQUE,
    description     VARCHAR(255),
    color           VARCHAR(7) DEFAULT '#6B7280',
    sort_order      INTEGER NOT NULL,
    is_won          BOOLEAN DEFAULT FALSE,
    is_lost         BOOLEAN DEFAULT FALSE,
    probability     INTEGER DEFAULT 0 CHECK (probability >= 0 AND probability <= 100),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Dados iniciais
INSERT INTO pipeline_stages (name, slug, sort_order, probability) VALUES
('Prospeccao', 'prospeccao', 1, 10),
('Qualificacao', 'qualificacao', 2, 20),
('Proposta', 'proposta', 3, 50),
('Negociacao', 'negociacao', 4, 70),
('Fechamento', 'fechamento', 5, 90),
('Ganho', 'ganho', 6, 100),
('Perdido', 'perdido', 7, 0);

UPDATE pipeline_stages SET is_won = TRUE WHERE slug = 'ganho';
UPDATE pipeline_stages SET is_lost = TRUE WHERE slug = 'perdido';
```

---

### 3.9 Tabela: `deals`

Negocios/oportunidades no pipeline.

```sql
CREATE TYPE deal_status AS ENUM ('open', 'won', 'lost');

CREATE TABLE deals (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    title           VARCHAR(255) NOT NULL,
    lead_id         UUID REFERENCES leads(id),
    company_id      UUID REFERENCES companies(id),
    contact_id      UUID REFERENCES contacts(id),
    stage_id        UUID NOT NULL REFERENCES pipeline_stages(id),
    status          deal_status NOT NULL DEFAULT 'open',
    value           DECIMAL(12, 2) DEFAULT 0,
    currency        VARCHAR(3) DEFAULT 'BRL',
    expected_close_date DATE,
    actual_close_date   DATE,
    lost_reason     VARCHAR(255),
    owned_by        UUID NOT NULL REFERENCES users(id),
    notes           TEXT,
    metadata        JSONB DEFAULT '{}',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_deals_stage_id ON deals(stage_id);
CREATE INDEX idx_deals_status ON deals(status);
CREATE INDEX idx_deals_owned_by ON deals(owned_by);
CREATE INDEX idx_deals_company_id ON deals(company_id);
CREATE INDEX idx_deals_expected_close ON deals(expected_close_date);
CREATE INDEX idx_deals_value ON deals(value DESC);
```

---

### 3.10 Tabela: `contracts`

Contratos de prestacao de servicos.

```sql
CREATE TYPE contract_status AS ENUM ('draft', 'sent', 'negotiating', 'signed', 'active', 'completed', 'cancelled');

CREATE TABLE contracts (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    contract_number VARCHAR(20) NOT NULL UNIQUE,
    deal_id         UUID REFERENCES deals(id),
    company_id      UUID NOT NULL REFERENCES companies(id),
    contact_id      UUID REFERENCES contacts(id),
    title           VARCHAR(255) NOT NULL,
    description     TEXT,
    status          contract_status NOT NULL DEFAULT 'draft',
    total_value     DECIMAL(12, 2) NOT NULL,
    currency        VARCHAR(3) DEFAULT 'BRL',
    start_date      DATE,
    end_date        DATE,
    signed_at       TIMESTAMPTZ,
    signed_by       VARCHAR(255),
    file_url        VARCHAR(500),
    terms           TEXT,
    notes           TEXT,
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_contracts_company_id ON contracts(company_id);
CREATE INDEX idx_contracts_status ON contracts(status);
CREATE INDEX idx_contracts_number ON contracts(contract_number);
```

---

### 3.11 Tabela: `projects`

Projetos de producao.

```sql
CREATE TYPE project_status AS ENUM ('planning', 'pre_production', 'in_production', 'post_production', 'review', 'delivered', 'archived');

CREATE TABLE projects (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name            VARCHAR(255) NOT NULL,
    description     TEXT,
    contract_id     UUID REFERENCES contracts(id),
    company_id      UUID NOT NULL REFERENCES companies(id),
    status          project_status NOT NULL DEFAULT 'planning',
    project_type    VARCHAR(50),
    start_date      DATE,
    end_date        DATE,
    deadline        DATE,
    budget_total    DECIMAL(12, 2) DEFAULT 0,
    budget_spent    DECIMAL(12, 2) DEFAULT 0,
    progress        INTEGER DEFAULT 0 CHECK (progress >= 0 AND progress <= 100),
    manager_id      UUID REFERENCES users(id),
    notes           TEXT,
    metadata        JSONB DEFAULT '{}',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_projects_company_id ON projects(company_id);
CREATE INDEX idx_projects_status ON projects(status);
CREATE INDEX idx_projects_manager_id ON projects(manager_id);
CREATE INDEX idx_projects_deadline ON projects(deadline);
```

---

### 3.12 Tabela: `sprints`

Sprints dentro de projetos.

```sql
CREATE TYPE sprint_status AS ENUM ('planned', 'active', 'completed', 'cancelled');

CREATE TABLE sprints (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    project_id      UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
    name            VARCHAR(100) NOT NULL,
    goal            TEXT,
    status          sprint_status NOT NULL DEFAULT 'planned',
    start_date      DATE NOT NULL,
    end_date        DATE NOT NULL,
    sort_order      INTEGER NOT NULL DEFAULT 0,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_sprints_project_id ON sprints(project_id);
CREATE INDEX idx_sprints_status ON sprints(status);
```

---

### 3.13 Tabela: `tasks`

Tarefas de projeto.

```sql
CREATE TYPE task_status AS ENUM ('backlog', 'todo', 'in_progress', 'review', 'done', 'cancelled');
CREATE TYPE task_priority AS ENUM ('low', 'medium', 'high', 'urgent');

CREATE TABLE tasks (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    project_id      UUID NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
    sprint_id       UUID REFERENCES sprints(id),
    parent_task_id  UUID REFERENCES tasks(id),
    title           VARCHAR(255) NOT NULL,
    description     TEXT,
    status          task_status NOT NULL DEFAULT 'backlog',
    priority        task_priority NOT NULL DEFAULT 'medium',
    assigned_to     UUID REFERENCES users(id),
    estimated_hours DECIMAL(6, 2),
    actual_hours    DECIMAL(6, 2),
    due_date        DATE,
    completed_at    TIMESTAMPTZ,
    sort_order      INTEGER DEFAULT 0,
    tags            TEXT[] DEFAULT '{}',
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_tasks_project_id ON tasks(project_id);
CREATE INDEX idx_tasks_sprint_id ON tasks(sprint_id);
CREATE INDEX idx_tasks_assigned_to ON tasks(assigned_to);
CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_priority ON tasks(priority);
CREATE INDEX idx_tasks_due_date ON tasks(due_date);
CREATE INDEX idx_tasks_tags ON tasks USING gin(tags);
```

---

### 3.14 Tabela: `invoices`

Faturas e cobrancas.

```sql
CREATE TYPE invoice_status AS ENUM ('draft', 'sent', 'paid', 'partial', 'overdue', 'cancelled', 'refunded');

CREATE TABLE invoices (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    invoice_number  VARCHAR(20) NOT NULL UNIQUE,
    contract_id     UUID REFERENCES contracts(id),
    company_id      UUID NOT NULL REFERENCES companies(id),
    status          invoice_status NOT NULL DEFAULT 'draft',
    subtotal        DECIMAL(12, 2) NOT NULL,
    discount        DECIMAL(12, 2) DEFAULT 0,
    tax_amount      DECIMAL(12, 2) DEFAULT 0,
    total           DECIMAL(12, 2) NOT NULL,
    currency        VARCHAR(3) DEFAULT 'BRL',
    issue_date      DATE NOT NULL DEFAULT CURRENT_DATE,
    due_date        DATE NOT NULL,
    paid_at         TIMESTAMPTZ,
    payment_method  VARCHAR(50),
    nfe_number      VARCHAR(50),
    nfe_url         VARCHAR(500),
    external_id     VARCHAR(100),
    notes           TEXT,
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_invoices_company_id ON invoices(company_id);
CREATE INDEX idx_invoices_status ON invoices(status);
CREATE INDEX idx_invoices_due_date ON invoices(due_date);
CREATE INDEX idx_invoices_number ON invoices(invoice_number);
```

---

### 3.15 Tabela: `payments`

Pagamentos recebidos.

```sql
CREATE TYPE payment_method AS ENUM ('pix', 'boleto', 'credit_card', 'debit_card', 'transfer', 'cash');
CREATE TYPE payment_status AS ENUM ('pending', 'confirmed', 'failed', 'refunded');

CREATE TABLE payments (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    invoice_id      UUID NOT NULL REFERENCES invoices(id),
    amount          DECIMAL(12, 2) NOT NULL,
    method          payment_method NOT NULL,
    status          payment_status NOT NULL DEFAULT 'pending',
    paid_at         TIMESTAMPTZ,
    external_id     VARCHAR(100),
    gateway         VARCHAR(50),
    gateway_response JSONB,
    notes           TEXT,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_payments_invoice_id ON payments(invoice_id);
CREATE INDEX idx_payments_status ON payments(status);
CREATE INDEX idx_payments_paid_at ON payments(paid_at DESC);
```

---

### 3.16 Tabela: `expenses`

Despesas de projetos e operacionais.

```sql
CREATE TYPE expense_status AS ENUM ('pending', 'approved', 'paid', 'rejected', 'cancelled');
CREATE TYPE expense_category AS ENUM ('equipment_rental', 'crew', 'location', 'transport', 'catering', 'software', 'marketing', 'office', 'taxes', 'other');

CREATE TABLE expenses (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    project_id      UUID REFERENCES projects(id),
    supplier_id     UUID REFERENCES suppliers(id),
    budget_id       UUID REFERENCES budgets(id),
    category        expense_category NOT NULL,
    description     VARCHAR(255) NOT NULL,
    amount          DECIMAL(12, 2) NOT NULL,
    currency        VARCHAR(3) DEFAULT 'BRL',
    status          expense_status NOT NULL DEFAULT 'pending',
    expense_date    DATE NOT NULL,
    receipt_url     VARCHAR(500),
    nfe_number      VARCHAR(50),
    approved_by     UUID REFERENCES users(id),
    approved_at     TIMESTAMPTZ,
    paid_at         TIMESTAMPTZ,
    notes           TEXT,
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_expenses_project_id ON expenses(project_id);
CREATE INDEX idx_expenses_supplier_id ON expenses(supplier_id);
CREATE INDEX idx_expenses_category ON expenses(category);
CREATE INDEX idx_expenses_status ON expenses(status);
CREATE INDEX idx_expenses_date ON expenses(expense_date DESC);
```

---

### 3.17 Tabela: `suppliers`

Fornecedores.

```sql
CREATE TABLE suppliers (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name            VARCHAR(255) NOT NULL,
    cnpj_cpf        VARCHAR(18),
    email           VARCHAR(255),
    phone           VARCHAR(20),
    category        VARCHAR(100),
    address         TEXT,
    bank_info       JSONB,
    rating          DECIMAL(3, 2) DEFAULT 0 CHECK (rating >= 0 AND rating <= 5),
    notes           TEXT,
    is_active       BOOLEAN DEFAULT TRUE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_suppliers_category ON suppliers(category);
CREATE INDEX idx_suppliers_name_trgm ON suppliers USING gin(name gin_trgm_ops);
```

---

### 3.18 Tabela: `budgets`

Orcamentos de projetos.

```sql
CREATE TYPE budget_status AS ENUM ('draft', 'sent', 'approved', 'rejected', 'revised');

CREATE TABLE budgets (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    project_id      UUID REFERENCES projects(id),
    deal_id         UUID REFERENCES deals(id),
    company_id      UUID NOT NULL REFERENCES companies(id),
    title           VARCHAR(255) NOT NULL,
    description     TEXT,
    status          budget_status NOT NULL DEFAULT 'draft',
    subtotal        DECIMAL(12, 2) NOT NULL,
    discount        DECIMAL(12, 2) DEFAULT 0,
    taxes           DECIMAL(12, 2) DEFAULT 0,
    margin_percent  DECIMAL(5, 2) DEFAULT 30.00,
    total           DECIMAL(12, 2) NOT NULL,
    valid_until     DATE,
    file_url        VARCHAR(500),
    version         INTEGER DEFAULT 1,
    notes           TEXT,
    created_by      UUID REFERENCES users(id),
    approved_by     UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_budgets_project_id ON budgets(project_id);
CREATE INDEX idx_budgets_company_id ON budgets(company_id);
CREATE INDEX idx_budgets_status ON budgets(status);
```

---

### 3.19 Tabela: `equipment`

Catalogo de equipamentos.

```sql
CREATE TABLE equipment (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name            VARCHAR(255) NOT NULL,
    category        VARCHAR(100) NOT NULL,
    brand           VARCHAR(100),
    model           VARCHAR(100),
    serial_number   VARCHAR(100),
    daily_rate      DECIMAL(10, 2),
    purchase_value  DECIMAL(12, 2),
    purchase_date   DATE,
    condition       VARCHAR(20) CHECK (condition IN ('new', 'good', 'fair', 'needs_repair', 'retired')),
    location        VARCHAR(255),
    notes           TEXT,
    image_url       VARCHAR(500),
    is_available    BOOLEAN DEFAULT TRUE,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_equipment_category ON equipment(category);
CREATE INDEX idx_equipment_available ON equipment(is_available) WHERE is_available = TRUE;
```

---

### 3.20 Tabela: `content_pieces`

Pecas de conteudo (geradas por IA ou manualmente).

```sql
CREATE TYPE content_status AS ENUM ('draft', 'review', 'approved', 'scheduled', 'published', 'archived');
CREATE TYPE content_type AS ENUM (
    'instagram_post', 'instagram_story', 'instagram_carousel',
    'linkedin_post', 'linkedin_article',
    'blog_post', 'email_newsletter', 'video_script', 'caption', 'other'
);

CREATE TABLE content_pieces (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    title           VARCHAR(255) NOT NULL,
    content_type    content_type NOT NULL,
    status          content_status NOT NULL DEFAULT 'draft',
    body            TEXT NOT NULL,
    media_urls      TEXT[] DEFAULT '{}',
    hashtags        TEXT[] DEFAULT '{}',
    platform        VARCHAR(50),
    scheduled_at    TIMESTAMPTZ,
    published_at    TIMESTAMPTZ,
    published_url   VARCHAR(500),
    engagement_data JSONB DEFAULT '{}',
    seo_keywords    TEXT[] DEFAULT '{}',
    generated_by    UUID REFERENCES agents(id),
    approved_by     UUID REFERENCES users(id),
    created_by      UUID REFERENCES users(id),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_content_pieces_type ON content_pieces(content_type);
CREATE INDEX idx_content_pieces_status ON content_pieces(status);
CREATE INDEX idx_content_pieces_scheduled ON content_pieces(scheduled_at) WHERE scheduled_at IS NOT NULL;
CREATE INDEX idx_content_pieces_platform ON content_pieces(platform);
```

---

### 3.21 Tabela: `social_posts`

Publicacoes efetivas em redes sociais (vinculadas a content_pieces).

```sql
CREATE TABLE social_posts (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    content_piece_id UUID NOT NULL REFERENCES content_pieces(id),
    platform        VARCHAR(50) NOT NULL,
    platform_post_id VARCHAR(255),
    post_url        VARCHAR(500),
    published_at    TIMESTAMPTZ,
    impressions     INTEGER DEFAULT 0,
    reach           INTEGER DEFAULT 0,
    likes           INTEGER DEFAULT 0,
    comments        INTEGER DEFAULT 0,
    shares          INTEGER DEFAULT 0,
    clicks          INTEGER DEFAULT 0,
    saves           INTEGER DEFAULT 0,
    engagement_rate DECIMAL(5, 2) DEFAULT 0,
    metrics_updated_at TIMESTAMPTZ,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_social_posts_content_piece ON social_posts(content_piece_id);
CREATE INDEX idx_social_posts_platform ON social_posts(platform);
CREATE INDEX idx_social_posts_published ON social_posts(published_at DESC);
```

---

### 3.22 Tabela: `agents`

Registro dos agentes IA do sistema.

```sql
CREATE TYPE agent_status AS ENUM ('active', 'paused', 'disabled', 'error');

CREATE TABLE agents (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name            VARCHAR(100) NOT NULL UNIQUE,
    slug            VARCHAR(100) NOT NULL UNIQUE,
    crew            VARCHAR(50) NOT NULL,
    description     TEXT,
    persona         TEXT,
    model_primary   VARCHAR(50) NOT NULL,
    model_fallback  VARCHAR(50),
    status          agent_status NOT NULL DEFAULT 'active',
    config          JSONB NOT NULL DEFAULT '{}',
    daily_token_limit INTEGER DEFAULT 500000,
    daily_action_limit INTEGER DEFAULT 200,
    daily_cost_limit DECIMAL(8, 2) DEFAULT 50.00,
    monthly_cost_limit DECIMAL(8, 2) DEFAULT 200.00,
    total_tokens_used BIGINT DEFAULT 0,
    total_cost      DECIMAL(12, 2) DEFAULT 0,
    last_run_at     TIMESTAMPTZ,
    error_message   TEXT,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_agents_status ON agents(status);
CREATE INDEX idx_agents_crew ON agents(crew);
CREATE INDEX idx_agents_slug ON agents(slug);
```

---

### 3.23 Tabela: `agent_logs`

Logs de execucao dos agentes (tabela de alto volume, particionada).

```sql
CREATE TYPE log_level AS ENUM ('debug', 'info', 'warning', 'error', 'critical');

CREATE TABLE agent_logs (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    agent_id        UUID NOT NULL REFERENCES agents(id),
    task_id         VARCHAR(100),
    level           log_level NOT NULL DEFAULT 'info',
    action          VARCHAR(100) NOT NULL,
    input_data      JSONB,
    output_data     JSONB,
    model_used      VARCHAR(50),
    tokens_input    INTEGER DEFAULT 0,
    tokens_output   INTEGER DEFAULT 0,
    cost            DECIMAL(8, 4) DEFAULT 0,
    duration_ms     INTEGER,
    success         BOOLEAN NOT NULL DEFAULT TRUE,
    error_message   TEXT,
    metadata        JSONB DEFAULT '{}',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (created_at);

-- Particoes mensais (ver secao de particionamento)
CREATE TABLE agent_logs_2026_01 PARTITION OF agent_logs
    FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');
CREATE TABLE agent_logs_2026_02 PARTITION OF agent_logs
    FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');
CREATE TABLE agent_logs_2026_03 PARTITION OF agent_logs
    FOR VALUES FROM ('2026-03-01') TO ('2026-04-01');

CREATE INDEX idx_agent_logs_agent_id ON agent_logs(agent_id);
CREATE INDEX idx_agent_logs_created_at ON agent_logs(created_at DESC);
CREATE INDEX idx_agent_logs_level ON agent_logs(level) WHERE level IN ('error', 'critical');
CREATE INDEX idx_agent_logs_action ON agent_logs(action);
```

---

### 3.24 Tabela: `notifications`

Notificacoes do sistema para usuarios.

```sql
CREATE TYPE notification_type AS ENUM ('info', 'success', 'warning', 'error', 'action_required');

CREATE TABLE notifications (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id         UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    type            notification_type NOT NULL DEFAULT 'info',
    title           VARCHAR(255) NOT NULL,
    message         TEXT NOT NULL,
    action_url      VARCHAR(500),
    is_read         BOOLEAN DEFAULT FALSE,
    read_at         TIMESTAMPTZ,
    metadata        JSONB DEFAULT '{}',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_unread ON notifications(user_id, is_read) WHERE is_read = FALSE;
CREATE INDEX idx_notifications_created_at ON notifications(created_at DESC);
```

---

### 3.25 Tabela: `audit_logs`

Log de auditoria para todas as acoes sensiveis (particionada).

```sql
CREATE TABLE audit_logs (
    id              UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id         UUID REFERENCES users(id),
    agent_id        UUID REFERENCES agents(id),
    action          VARCHAR(100) NOT NULL,
    resource_type   VARCHAR(50) NOT NULL,
    resource_id     UUID,
    old_values      JSONB,
    new_values      JSONB,
    ip_address      INET,
    user_agent      VARCHAR(500),
    session_id      VARCHAR(100),
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (created_at);

-- Particoes mensais
CREATE TABLE audit_logs_2026_01 PARTITION OF audit_logs
    FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');
CREATE TABLE audit_logs_2026_02 PARTITION OF audit_logs
    FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');
CREATE TABLE audit_logs_2026_03 PARTITION OF audit_logs
    FOR VALUES FROM ('2026-03-01') TO ('2026-04-01');

CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_action ON audit_logs(action);
CREATE INDEX idx_audit_logs_resource ON audit_logs(resource_type, resource_id);
CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at DESC);
```

---

## 4. Estrategia de Migracoes com Alembic

### Configuracao

**Arquivo**: `alembic.ini`

```ini
[alembic]
script_location = alembic
sqlalchemy.url = postgresql+asyncpg://somos:password@localhost:5432/somos

[loggers]
keys = root,sqlalchemy,alembic

[logger_alembic]
level = INFO
handlers =
qualname = alembic
```

### Estrutura de Migracoes

```
alembic/
├── env.py              # Configuracao do ambiente (async)
├── script.py.mako      # Template para novas migracoes
└── versions/
    ├── 001_create_users_table.py
    ├── 002_create_roles_table.py
    ├── 003_create_companies_contacts.py
    ├── 004_create_leads_pipeline.py
    ├── 005_create_deals.py
    ├── 006_create_contracts_projects.py
    ├── 007_create_finance_tables.py
    ├── 008_create_content_tables.py
    ├── 009_create_agents_tables.py
    ├── 010_create_audit_notifications.py
    ├── 011_seed_roles_pipeline_stages.py
    └── ...
```

### Comandos Alembic

```bash
# Criar nova migracao automatica
alembic revision --autogenerate -m "descricao da mudanca"

# Aplicar migracoes pendentes
alembic upgrade head

# Reverter ultima migracao
alembic downgrade -1

# Ver historico de migracoes
alembic history

# Ver migracao atual
alembic current

# Gerar SQL sem aplicar (para revisao)
alembic upgrade head --sql
```

### Regras de Migracao

1. **Toda migracao deve ser reversivel** (`downgrade` implementado)
2. **Nenhuma migracao pode causar downtime** (usar `ADD COLUMN` com default, nunca `NOT NULL` sem default em tabelas existentes)
3. **Migracoes devem ser idempotentes** quando possivel
4. **Testar em staging** antes de aplicar em producao
5. **Nunca alterar migracoes ja aplicadas** em producao
6. **Nomear migracoes de forma descritiva** (ex: `add_score_column_to_leads`)
7. **Migracoes destrutivas** (DROP COLUMN, DROP TABLE) passam por revisao manual obrigatoria
8. **Data migrations** separadas de schema migrations
9. **Backup automatico** antes de cada migracao em producao

### Politica de Deploy

```
1. CI roda testes com banco limpo (migracoes do zero)
2. Staging aplica migracao automaticamente no merge para develop
3. Producao: backup criado -> migracao aplicada -> health check -> deploy da app
4. Rollback: reverter migracao + restaurar backup se necessario
```

---

## 5. Otimizacao de Indices

### Estrategia de Indexacao

| Tipo de Indice | Quando Usar |
|---|---|
| **B-Tree** | Colunas com `WHERE =`, `<`, `>`, `ORDER BY` (padrao) |
| **GIN** | JSONB, arrays, full-text search, pg_trgm (similaridade) |
| **GiST** | Dados geometricos, range types |
| **BRIN** | Tabelas muito grandes com dados naturalmente ordenados (logs) |
| **Partial** | Filtrar apenas um subconjunto (ex: `WHERE is_active = TRUE`) |
| **Covering** | Incluir colunas no indice para evitar acesso a tabela |

### Queries Criticas e Seus Indices

```sql
-- Query: Dashboard - Deals por stage (usada constantemente)
-- Indice: idx_deals_stage_id + idx_deals_status
SELECT stage_id, COUNT(*), SUM(value)
FROM deals
WHERE status = 'open'
GROUP BY stage_id;

-- Query: Busca de leads por nome/empresa (busca fuzzy)
-- Indice: idx_leads_metadata (GIN) + idx_contacts_name_trgm (GIN trgm)
SELECT l.*, c.full_name, co.name AS company_name
FROM leads l
JOIN contacts c ON l.contact_id = c.id
JOIN companies co ON l.company_id = co.id
WHERE c.full_name % 'joao silva'
  AND l.status != 'lost'
ORDER BY similarity(c.full_name, 'joao silva') DESC
LIMIT 20;

-- Query: Relatorio financeiro mensal
-- Indice: idx_invoices_status + idx_payments_paid_at
SELECT
    DATE_TRUNC('month', p.paid_at) AS mes,
    SUM(p.amount) AS total_recebido,
    COUNT(*) AS num_pagamentos
FROM payments p
WHERE p.status = 'confirmed'
  AND p.paid_at >= '2026-01-01'
GROUP BY DATE_TRUNC('month', p.paid_at)
ORDER BY mes;

-- Query: Custo de tokens por agente (monitoramento diario)
-- Indice: idx_agent_logs_agent_id + idx_agent_logs_created_at
SELECT
    agent_id,
    SUM(tokens_input) AS total_input,
    SUM(tokens_output) AS total_output,
    SUM(cost) AS total_cost
FROM agent_logs
WHERE created_at >= CURRENT_DATE
GROUP BY agent_id;
```

### Monitoramento de Performance

```sql
-- Top 10 queries mais lentas
SELECT
    query,
    calls,
    mean_exec_time::numeric(10,2) AS avg_ms,
    total_exec_time::numeric(10,2) AS total_ms,
    rows
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;

-- Indices nao utilizados (candidatos a remocao)
SELECT
    schemaname, tablename, indexname, idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
  AND indexname NOT LIKE '%_pkey'
ORDER BY pg_relation_size(indexrelid) DESC;
```

### Configuracoes de Performance

| Parametro | Valor | Justificativa |
|---|---|---|
| `shared_buffers` | 256MB | ~25% da RAM disponivel para o container |
| `effective_cache_size` | 768MB | ~75% da RAM |
| `work_mem` | 8MB | Para sorts e hashes em queries complexas |
| `maintenance_work_mem` | 128MB | Para VACUUM e CREATE INDEX |
| `random_page_cost` | 1.1 | Disco SSD (padrao 4.0 para HDD) |
| `effective_io_concurrency` | 200 | SSD suporta alta concorrencia |
| `max_connections` | 100 | Gerenciadas via PgBouncer |

---

## 6. Redis - Cache e Sessoes

### Configuracao Docker

```yaml
redis:
  image: redis:7-alpine
  command: >
    redis-server
    --requirepass ${REDIS_PASSWORD}
    --maxmemory 512mb
    --maxmemory-policy allkeys-lru
    --save 60 1000
    --appendonly yes
  volumes:
    - redis_data:/data
  ports:
    - "6379:6379"
  healthcheck:
    test: ["CMD", "redis-cli", "-a", "${REDIS_PASSWORD}", "ping"]
    interval: 10s
    timeout: 5s
    retries: 3
```

### Estrategia de Cache

| Padrao de Chave | TTL | Conteudo | Invalidacao |
|---|---|---|---|
| `session:{session_id}` | 24h | Dados da sessao JWT do usuario | Logout ou expiracao |
| `user:{user_id}:permissions` | 15min | Permissoes RBAC resolvidas | Alteracao de role |
| `user:{user_id}:profile` | 30min | Dados do perfil do usuario | Update no perfil |
| `pipeline:stages` | 1h | Etapas do pipeline (raramente muda) | Alteracao de stages |
| `dashboard:{user_id}:summary` | 5min | Metricas do dashboard | Tempo (auto-expira) |
| `rate_limit:{ip}:{endpoint}` | 1min | Contador de rate limiting | Tempo (auto-expira) |
| `agent:{agent_id}:spending_daily` | 24h | Gasto acumulado do agente no dia | Reset a meia-noite |
| `agent:{agent_id}:tokens_daily` | 24h | Tokens consumidos no dia | Reset a meia-noite |
| `lead:score:{lead_id}` | 30min | Score calculado pelo agente de IA | Recalculo |
| `lock:agent:{agent_id}` | 5min | Lock distribuido para execucao | Fim da execucao |
| `refresh_token:{token_hash}` | 7d | Refresh token valido | Logout ou rotacao |
| `blacklist:jwt:{jti}` | 24h | Tokens JWT revogados | Tempo (auto-expira) |

### Padroes de Uso no Codigo

```python
# Exemplo: Cache de permissoes com fallback para banco
async def get_user_permissions(user_id: str) -> dict:
    cache_key = f"user:{user_id}:permissions"

    # Tenta buscar no cache
    cached = await redis.get(cache_key)
    if cached:
        return json.loads(cached)

    # Fallback para banco de dados
    permissions = await db.fetch_permissions(user_id)

    # Armazena no cache com TTL de 15 minutos
    await redis.setex(cache_key, 900, json.dumps(permissions))

    return permissions
```

### Invalidacao de Cache

- **Write-through**: Ao atualizar um registro, o cache correspondente e invalidado imediatamente
- **Padroes de chave com prefixo**: Permitem invalidacao em lote (`DEL user:{user_id}:*`)
- **Pub/Sub**: Eventos de invalidacao propagados para multiplas instancias da aplicacao
- **Cache stampede prevention**: Uso de locks distribuidos ao reconstruir cache expirado

---

## 7. Qdrant - Busca Vetorial

### Finalidade

O Qdrant armazena embeddings vetoriais para funcionalidades de IA, permitindo busca semantica e similaridade em tempo real.

### Colecoes

| Colecao | Dimensao | Distancia | Uso |
|---|---|---|---|
| `lead_profiles` | 1536 | Cosine | Busca de leads similares, clusterizacao por segmento |
| `content_briefs` | 1536 | Cosine | Busca semantica em briefings e conteudos |
| `knowledge_base` | 1536 | Cosine | RAG para agentes de IA (documentos internos, processos) |
| `email_templates` | 1536 | Cosine | Templates de email/mensagem por similaridade de contexto |
| `company_profiles` | 1536 | Cosine | Similaridade entre empresas para prospeccao |

### Configuracao Docker

```yaml
qdrant:
  image: qdrant/qdrant:latest
  volumes:
    - qdrant_data:/qdrant/storage
  ports:
    - "6333:6333"
    - "6334:6334"  # gRPC
  environment:
    QDRANT__SERVICE__API_KEY: ${QDRANT_API_KEY}
    QDRANT__STORAGE__OPTIMIZERS__DEFAULT_SEGMENT_NUMBER: 2
```

### Fluxo de Indexacao

```
1. Registro criado/atualizado no PostgreSQL
        |
        v
2. Evento publicado no Redis Pub/Sub
        |
        v
3. Worker extrai texto relevante do registro
        |
        v
4. Texto enviado para API de embeddings (OpenAI text-embedding-3-small)
        |
        v
5. Vetor (1536 dimensoes) armazenado no Qdrant
   - ID do ponto = UUID do registro no PostgreSQL
   - Payload = metadados para filtragem (tipo, status, data)
        |
        v
6. Consultas de similaridade retornam IDs
   que sao resolvidos no PostgreSQL para dados completos
```

### Exemplo de Uso: Busca de Leads Similares

```python
# Encontrar leads com perfil similar a um lead de referencia
async def find_similar_leads(lead_id: str, limit: int = 10):
    # Buscar vetor do lead de referencia
    lead_vector = await qdrant.retrieve(
        collection_name="lead_profiles",
        ids=[lead_id]
    )

    # Buscar similares
    results = await qdrant.search(
        collection_name="lead_profiles",
        query_vector=lead_vector[0].vector,
        limit=limit,
        query_filter=Filter(
            must_not=[FieldCondition(key="status", match=MatchValue(value="lost"))]
        )
    )

    # Retornar IDs para resolver no PostgreSQL
    return [hit.id for hit in results]
```

---

## 8. MinIO - Armazenamento de Arquivos

### Configuracao Docker

```yaml
minio:
  image: minio/minio:latest
  command: server /data --console-address ":9001"
  environment:
    MINIO_ROOT_USER: ${MINIO_USER}
    MINIO_ROOT_PASSWORD: ${MINIO_PASSWORD}
  volumes:
    - minio_data:/data
  ports:
    - "9000:9000"   # API S3-compativel
    - "9001:9001"   # Console web
  healthcheck:
    test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
    interval: 30s
    timeout: 10s
    retries: 3
```

### Buckets e Politicas

| Bucket | Conteudo | Acesso | Retencao |
|---|---|---|---|
| `avatars` | Fotos de perfil dos usuarios | Publico (leitura) | Indefinido |
| `contracts` | PDFs de contratos assinados | Privado | 10 anos (exigencia legal) |
| `receipts` | Comprovantes de pagamento/despesa | Privado | 5 anos (exigencia fiscal) |
| `content` | Videos, imagens, audios de producao | Privado | Vinculado ao projeto |
| `exports` | Relatorios exportados (CSV, PDF) | Privado | 30 dias (auto-delete) |
| `backups` | Dumps do PostgreSQL e snapshots | Privado | 90 dias (rotacao) |
| `temp` | Uploads temporarios em processamento | Privado | 24 horas (auto-delete) |

### Convencao de Caminhos

```
{bucket}/{ano}/{mes}/{entity_type}/{entity_id}/{filename}

Exemplos:
  content/2026/03/video/a1b2c3d4/final_cut_v2.mp4
  contracts/2026/02/contract/e5f6g7h8/contrato_somos_001.pdf
  receipts/2026/02/expense/f9g0h1i2/nfe_12345.pdf
  avatars/users/abc123/profile_200x200.webp
```

### Lifecycle Rules

```json
{
  "Rules": [
    {
      "ID": "auto-delete-exports",
      "Status": "Enabled",
      "Filter": {"Prefix": "exports/"},
      "Expiration": {"Days": 30}
    },
    {
      "ID": "auto-delete-temp",
      "Status": "Enabled",
      "Filter": {"Prefix": "temp/"},
      "Expiration": {"Days": 1}
    },
    {
      "ID": "rotate-backups",
      "Status": "Enabled",
      "Filter": {"Prefix": "backups/"},
      "Expiration": {"Days": 90}
    }
  ]
}
```

### Integracao com a Aplicacao

```python
# Upload de arquivo com pre-signed URL
async def generate_upload_url(bucket: str, path: str, expires: int = 3600) -> str:
    return minio_client.presigned_put_object(
        bucket_name=bucket,
        object_name=path,
        expires=timedelta(seconds=expires)
    )

# Download seguro com pre-signed URL
async def generate_download_url(bucket: str, path: str, expires: int = 3600) -> str:
    return minio_client.presigned_get_object(
        bucket_name=bucket,
        object_name=path,
        expires=timedelta(seconds=expires)
    )
```

---

## 9. Particionamento de Dados

### Tabelas Particionadas

As seguintes tabelas usam **particionamento por range temporal** (mensal):

1. **`agent_logs`** - Volume estimado: 1M+ registros/mes
2. **`audit_logs`** - Volume estimado: 500K+ registros/mes
3. **`lead_interactions`** - Volume estimado: 200K+ registros/mes (futuro)

### Script de Criacao Automatica de Particoes

```sql
-- Funcao para criar particoes automaticamente
CREATE OR REPLACE FUNCTION create_monthly_partition(
    p_table_name TEXT,
    p_year INTEGER,
    p_month INTEGER
) RETURNS VOID AS $$
DECLARE
    v_partition_name TEXT;
    v_start_date DATE;
    v_end_date DATE;
BEGIN
    v_partition_name := p_table_name || '_' || p_year || '_' || LPAD(p_month::TEXT, 2, '0');
    v_start_date := make_date(p_year, p_month, 1);
    v_end_date := v_start_date + INTERVAL '1 month';

    EXECUTE format(
        'CREATE TABLE IF NOT EXISTS %I PARTITION OF %I FOR VALUES FROM (%L) TO (%L)',
        v_partition_name, p_table_name, v_start_date, v_end_date
    );

    RAISE NOTICE 'Particao % criada com sucesso', v_partition_name;
END;
$$ LANGUAGE plpgsql;

-- Criar particoes para os proximos 6 meses
DO $$
DECLARE
    v_date DATE := CURRENT_DATE;
    v_table TEXT;
BEGIN
    FOR i IN 0..5 LOOP
        FOREACH v_table IN ARRAY ARRAY['agent_logs', 'audit_logs'] LOOP
            PERFORM create_monthly_partition(
                v_table,
                EXTRACT(YEAR FROM v_date + (i || ' months')::INTERVAL)::INTEGER,
                EXTRACT(MONTH FROM v_date + (i || ' months')::INTERVAL)::INTEGER
            );
        END LOOP;
    END LOOP;
END $$;

-- Agendamento automatico via pg_cron (executa dia 25 de cada mes as 03:00)
SELECT cron.schedule(
    'create-monthly-partitions',
    '0 3 25 * *',
    $$
    DO $body$
    DECLARE v_table TEXT;
    BEGIN
        FOREACH v_table IN ARRAY ARRAY['agent_logs', 'audit_logs'] LOOP
            PERFORM create_monthly_partition(
                v_table,
                EXTRACT(YEAR FROM now() + interval '1 month')::INTEGER,
                EXTRACT(MONTH FROM now() + interval '1 month')::INTEGER
            );
        END LOOP;
    END $body$;
    $$
);
```

### Politica de Retencao de Particoes

| Tabela | Retencao (Hot) | Retencao (Cold) | Acao Apos Cold |
|---|---|---|---|
| `agent_logs` | 3 meses | 12 meses | Exportar CSV para MinIO + DROP |
| `audit_logs` | 6 meses | 36 meses (LGPD) | Arquivar comprimido no MinIO |
| `lead_interactions` | 6 meses | 24 meses | Exportar CSV para MinIO + DROP |

```sql
-- Script de limpeza mensal (executado via pg_cron)
-- Remove particoes com mais de 12 meses para agent_logs
DO $$
DECLARE
    v_cutoff DATE := CURRENT_DATE - INTERVAL '12 months';
    v_partition TEXT;
BEGIN
    FOR v_partition IN
        SELECT tablename FROM pg_tables
        WHERE tablename LIKE 'agent_logs_%'
          AND tablename != 'agent_logs'
        ORDER BY tablename
    LOOP
        -- Verifica se a particao e anterior ao cutoff
        IF v_partition < 'agent_logs_' || to_char(v_cutoff, 'YYYY_MM') THEN
            EXECUTE format('DROP TABLE IF EXISTS %I', v_partition);
            RAISE NOTICE 'Particao % removida', v_partition;
        END IF;
    END LOOP;
END $$;
```

---

## 10. Backup e Recuperacao

### Estrategia de Backup

| Tipo | Frequencia | Ferramenta | Retencao | Destino |
|---|---|---|---|---|
| Full dump | Diario 03:00 | `pg_dump` + gzip | 30 dias | MinIO `backups/postgres/` |
| WAL archiving | Continuo | `pg_basebackup` + WAL | 7 dias | Volume local |
| Snapshot logico | Semanal (dom) | `pg_dump --format=custom` | 90 dias | MinIO + storage externo |
| Redis RDB | A cada 60s | Snapshot automatico | 7 dias | Volume Docker |
| Qdrant snapshot | Diario 04:00 | API de snapshots | 30 dias | MinIO `backups/qdrant/` |
| MinIO mirror | Semanal | `mc mirror` | Indefinido | Storage externo |

### Script de Backup PostgreSQL

```bash
#!/bin/bash
# /opt/somos/scripts/backup-postgres.sh
set -euo pipefail

TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="somos_db_${TIMESTAMP}.dump"
MINIO_PATH="backups/postgres/${BACKUP_FILE}"

echo "[$(date)] Iniciando backup do PostgreSQL..."

# Dump no formato custom (comprimido, restauracao parcial)
docker compose exec -T postgres pg_dump \
    -U somos \
    -d somos \
    --format=custom \
    --compress=9 \
    -f /tmp/${BACKUP_FILE}

# Upload para MinIO
docker compose exec -T postgres mc cp \
    /tmp/${BACKUP_FILE} \
    minio/${MINIO_PATH}

# Limpeza local
docker compose exec -T postgres rm /tmp/${BACKUP_FILE}

# Remover backups com mais de 90 dias
mc rm --recursive --force --older-than 90d minio/backups/postgres/

echo "[$(date)] Backup concluido: ${MINIO_PATH}"
```

### Restauracao de Backup

```bash
# Restaurar dump completo (formato custom)
docker compose exec -T postgres pg_restore \
    -U somos \
    -d somos \
    --clean \
    --if-exists \
    somos_db_20260228.dump

# Point-in-Time Recovery (PITR)
# Requer WAL archiving configurado
# 1. Parar PostgreSQL
# 2. Restaurar base backup
# 3. Configurar recovery_target_time no postgresql.conf
# 4. Iniciar PostgreSQL em modo recovery
```

### Teste de Recuperacao

- **Mensal**: Restaurar backup em ambiente de staging e validar integridade dos dados
- **Trimestral**: Simulacao completa de desastre com RTO de 1 hora e RPO de 1 hora
- **Anual**: Teste de recuperacao de desastre completo incluindo todos os componentes
- **Documentacao**: Runbook de recuperacao mantido atualizado no repositorio

---

## 11. Convencoes e Padroes

### Nomenclatura

| Elemento | Convencao | Exemplo |
|---|---|---|
| Tabelas | snake_case, plural | `lead_interactions` |
| Colunas | snake_case | `full_name`, `created_at` |
| Primary Key | `id` (UUID) | `id UUID PRIMARY KEY` |
| Foreign Key | `{tabela_singular}_id` | `company_id`, `user_id` |
| Indices | `idx_{tabela}_{coluna}` | `idx_leads_status` |
| Constraints | `chk_{tabela}_{regra}` | `chk_leads_score_range` |
| Enums | `{entidade}_{atributo}` | `lead_status`, `task_priority` |
| Particoes | `{tabela}_{ano}_{mes}` | `agent_logs_2026_02` |

### Colunas Obrigatorias em Todas as Tabelas

| Coluna | Tipo | Descricao |
|---|---|---|
| `id` | UUID (PK) | Identificador unico, gerado pelo PostgreSQL |
| `created_at` | TIMESTAMPTZ | Timestamp de criacao (automatico) |
| `updated_at` | TIMESTAMPTZ | Timestamp de atualizacao (via trigger) |

### Trigger para `updated_at`

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Aplicar a todas as tabelas que possuem updated_at
-- Exemplo para users (repetir para todas as tabelas):
CREATE TRIGGER set_updated_at
    BEFORE UPDATE ON users
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

-- Tabelas com trigger: users, roles, companies, contacts, leads,
-- deals, contracts, projects, sprints, tasks, invoices, payments,
-- expenses, suppliers, budgets, equipment, content_pieces,
-- social_posts, agents, notifications
```

### Connection Pooling com PgBouncer

```ini
; pgbouncer.ini
[databases]
somos = host=postgres port=5432 dbname=somos

[pgbouncer]
listen_addr = 0.0.0.0
listen_port = 6432
auth_type = scram-sha-256
pool_mode = transaction
max_client_conn = 200
default_pool_size = 25
min_pool_size = 5
reserve_pool_size = 5
reserve_pool_timeout = 3
```

---

*Documento mantido pela equipe de engenharia da Somos Produtora. Atualizado a cada alteracao significativa no schema.*
