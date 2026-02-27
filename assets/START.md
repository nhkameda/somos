# START.md — Prompts de Implementacao por Dia

> **Somos Produtora** — Sistema Integrado de Gestao Inteligente
> Cada bloco abaixo e um prompt completo para ser copiado e colado no terminal.
> **Importante:** Execute um dia por vez. Aguarde a conclusao antes de iniciar o proximo.
> **Maquina nova:** Comece pela PRE-INSTALACAO, depois PASSO 0, depois DIA 0 em diante.
> **Pasta do projeto:** `/SOMOSAI/` (raiz do disco)

---

## PRE-INSTALACAO — Instalar Claude Code (fazer primeiro de tudo)

> Antes de qualquer coisa, voce precisa do Node.js e do Claude Code instalados.
> Se a maquina e nova e nao tem NADA instalado, execute estes comandos na ordem.

### Instalar Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> Apos instalar, copie e cole os comandos que o Homebrew exibir para adicionar ao PATH:
> ```bash
> echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
> eval "$(/opt/homebrew/bin/brew shellenv)"
> ```

### Instalar Node.js (necessario para o Claude Code)

```bash
brew install node@20
echo 'export PATH="/opt/homebrew/opt/node@20/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
node --version
```

### Instalar Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### Configurar a API Key do Claude

```bash
export ANTHROPIC_API_KEY="sk-ant-SUA-CHAVE-AQUI"
echo 'export ANTHROPIC_API_KEY="sk-ant-SUA-CHAVE-AQUI"' >> ~/.zshrc
source ~/.zshrc
```

> Substitua `sk-ant-SUA-CHAVE-AQUI` pela sua chave real da Anthropic.
> Obtenha em: https://console.anthropic.com/settings/keys

### Verificar Claude Code

```bash
claude --version
```

> Se retornar a versao, o Claude Code esta pronto. Siga para o **PASSO 0**.

---

## PASSO 0 — Instalacao do Ambiente Completo (Mac Novo)

> **Execute estes comandos manualmente no Terminal, um bloco por vez.**
> Este passo so precisa ser feito UMA VEZ na maquina nova.
> O Homebrew e Node.js ja foram instalados na PRE-INSTALACAO acima.

### 0.1 — Instalar dependencias restantes via Homebrew

```bash
brew install git python@3.12 nvm
brew install --cask docker
```

### 0.2 — Configurar Python 3.12

```bash
echo 'export PATH="/opt/homebrew/opt/python@3.12/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
python3 --version
```

> Deve mostrar `Python 3.12.x`

### 0.3 — Configurar NVM (gerenciador de versoes Node.js)

```bash
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
echo '[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"' >> ~/.zshrc
source ~/.zshrc
nvm install 20
nvm use 20
nvm alias default 20
```

### 0.4 — Iniciar Docker Desktop

> Abra o Docker Desktop pela primeira vez: **Finder > Aplicativos > Docker**
> Aceite os termos e aguarde o Docker inicializar (icone da baleia no menu bar fica verde).
> Depois verifique:

```bash
docker --version
docker compose version
```

### 0.5 — Clonar o repositorio e criar a pasta do projeto

```bash
sudo mkdir -p /SOMOSAI
sudo chown $(whoami) /SOMOSAI
cd /SOMOSAI
git clone https://github.com/nhkameda/somos.git .
```

> O ponto final (`.`) clona o conteudo diretamente dentro de `/SOMOSAI/` sem criar subpasta.

### 0.6 — Verificar que tudo esta instalado

```bash
echo "=== Verificacao de Ambiente ===" && \
git --version && \
node --version && \
npm --version && \
python3 --version && \
docker --version && \
docker compose version && \
claude --version && \
echo "=== Tudo instalado com sucesso! ==="
```

> Se todos os comandos retornarem versoes, o ambiente esta pronto.
> Agora siga para o **DIA 0** abaixo.

---

## DIA 0 — Setup & Infraestrutura

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. A fase de planejamento esta 100% concluida em /docs/. Agora inicie a IMPLEMENTACAO.

LEIA PRIMEIRO estes documentos (sao a fonte da verdade):
- /CLAUDE.md (convencoes do projeto)
- /docs/11-roadmap-implementacao.md (roadmap completo)
- /docs/05-stack-tecnologica.md (stack e estrutura de pastas)
- /docs/02-arquitetura-sistema.md (arquitetura do sistema)
- /docs/06-infraestrutura.md (Docker Compose, Nginx, CI/CD)
- /docs/09-banco-dados.md (schema do banco de dados)
- /docs/10-seguranca.md (autenticacao JWT e RBAC)

EXECUTE O DIA 0 — Setup & Infraestrutura. Entregaveis:

0.1 REPOSITORIO E ESTRUTURA:
- Configurar monorepo com estrutura de pastas para backend/, frontend/, mobile/, agents/
- Instalar e configurar linters: Ruff (Python), ESLint + Prettier (TypeScript)
- Configurar pre-commit hooks (ruff check, eslint, prettier)
- Criar .editorconfig e configuracoes de IDE

0.2 DOCKER COMPOSE:
- Criar docker-compose.yml com todos os servicos conforme /docs/06-infraestrutura.md:
  - PostgreSQL 16 (com health check, volumes persistentes, configuracao de performance)
  - Redis 7 (maxmemory 2gb, allkeys-lru, appendonly, senha)
  - Qdrant latest (com API key, volumes)
  - MinIO latest (com credenciais, volumes, console na porta 9001)
  - Nginx Alpine (reverse proxy com configuracao completa)
  - Backend FastAPI (com Gunicorn + Uvicorn workers)
  - Frontend Next.js (production build)
  - Celery Worker + Celery Beat
- Criar docker-compose.dev.yml para desenvolvimento local (hot-reload, portas expostas)
- Criar .env.example com todas as variaveis de ambiente necessarias
- Todos os servicos devem ter health checks, restart policies e limites de memoria

0.3 CI/CD PIPELINE:
- Criar .github/workflows/ci.yml conforme /docs/06-infraestrutura.md secao 8:
  - Job lint: Ruff (backend) + ESLint (frontend)
  - Job test-backend: pytest com PostgreSQL e Redis como services
  - Job test-frontend: vitest com coverage
  - Job build: Docker build multi-stage para backend e frontend
  - Job deploy: SSH deploy para producao (apenas na branch main)

0.4 BANCO DE DADOS:
- Inicializar projeto backend Python com pyproject.toml ou requirements/
- Configurar SQLAlchemy 2.0 async com asyncpg
- Configurar Alembic para migrations
- Criar migration inicial com as tabelas base conforme /docs/09-banco-dados.md:
  - users (id, email, full_name, hashed_password, role, is_active, avatar_url, created_at, updated_at)
  - roles e permissions (RBAC completo)
  - audit_logs (user_id, action, resource, details, ip_address, timestamp)
- Configurar conexao Redis para cache/sessoes
- Configurar cliente Qdrant para embeddings
- Configurar cliente MinIO (boto3) para uploads

0.5 AUTENTICACAO:
- Implementar sistema JWT completo conforme /docs/10-seguranca.md:
  - Access token (15 min expiry, RS256 ou HS256)
  - Refresh token (7 dias, armazenado no Redis com rotacao)
  - Endpoints: POST /api/v1/auth/register, POST /api/v1/auth/login, POST /api/v1/auth/refresh, POST /api/v1/auth/logout
  - Hash de senha com bcrypt (passlib)
- Implementar RBAC com 5 roles: admin, gerente, comercial, operacional, visualizador
- Middleware de autorizacao por modulo (decorator ou dependency)
- Seed de usuario admin padrao

0.6 API BASE:
- Scaffold FastAPI completo conforme /docs/05-stack-tecnologica.md secao Backend:
  - Estrutura modular: app/api/v1/, app/models/, app/schemas/, app/services/, app/core/
  - CORS configurado para frontend
  - Rate limiting com slowapi
  - Error handling padronizado (HTTPException handler customizado)
  - Logging estruturado com structlog
  - Swagger UI e ReDoc configurados e acessiveis em /docs e /redoc
  - Health check endpoint GET /api/v1/health
  - Middleware de request ID para rastreamento
  - Pydantic Settings para configuracao via .env

0.7 FRONTEND SCAFFOLD:
- Criar projeto Next.js 15 com App Router e TypeScript
- Instalar e configurar Tailwind CSS 3.4+
- Instalar e configurar ShadcnUI (componentes base: Button, Input, Card, Dialog, DropdownMenu, DataTable, Sidebar)
- Criar layout base conforme /docs/05-stack-tecnologica.md secao Frontend:
  - Layout principal com sidebar recolhivel e header
  - Paginas de auth: /login e /register com formularios funcionais
  - Pagina dashboard vazia com placeholder
  - Dark mode toggle
  - Tema customizado Somos Produtora (cores: preto #0A0A0A, dourado #C9A84C, branco)
- Instalar Zustand para state management
- Instalar React Query (TanStack Query) para data fetching
- Instalar React Hook Form + Zod para formularios
- Configurar HTTP client (axios ou fetch wrapper) apontando para a API
- Configurar autenticacao no frontend (interceptor de token, redirect para login)
- Criar Dockerfile multi-stage para producao

CRITERIOS DE CONCLUSAO DO DIA 0:
- docker-compose up -d sobe toda a stack sem erros
- API responde em /docs com Swagger funcional
- Login/logout funcional com JWT (via API e frontend)
- Frontend renderiza com layout base, sidebar e navegacao
- Migrations rodam sem erros
- Todos os Dockerfiles constroem com sucesso

Ao finalizar, faca git add dos arquivos relevantes, commit com mensagem 'feat: dia 0 - setup e infraestrutura completa' e push para origin main. Tome todas as decisoes sozinho, sem fazer perguntas."
```

---

## DIA 1 — CRM Pipeline

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao do sistema.

LEIA PRIMEIRO:
- /CLAUDE.md (convencoes)
- /docs/11-roadmap-implementacao.md (Dia 1)
- /docs/04-modulos-detalhados/crm-pipeline.md (especificacao completa do modulo)
- /docs/09-banco-dados.md (schema das tabelas de CRM)

O Dia 0 ja foi concluido (infraestrutura, auth, scaffolds). Agora execute o DIA 1 — CRM Pipeline.

1.1 MODELS & MIGRATIONS:
- Criar models SQLAlchemy conforme /docs/09-banco-dados.md para:
  - contacts (id, first_name, last_name, email, phone, company_id, position, source, score, tags, notes, avatar_url, created_at, updated_at)
  - companies (id, name, cnpj, sector, size, website, address, city, state, notes, created_at, updated_at)
  - deals (id, title, value, currency, probability, expected_close_date, stage_id, contact_id, company_id, owner_id, source, notes, created_at, updated_at)
  - deal_stages (id, name, position, color, is_default, pipeline_id)
  - deal_activities (id, deal_id, user_id, type, description, scheduled_at, completed_at)
  - tags (id, name, color) com tabela associativa contact_tags
- Criar migration Alembic para todas as tabelas
- Seed de deal_stages padrao: Novo Lead, Qualificado, Reuniao Agendada, Proposta Enviada, Negociacao, Fechado Ganho, Fechado Perdido

1.2 API — CONTATOS:
- CRUD completo: GET /api/v1/contacts (com paginacao, busca, filtros por empresa/source/score)
- POST /api/v1/contacts (criacao com validacao Pydantic)
- GET /api/v1/contacts/{id} (detalhe com empresa e deals relacionados)
- PUT /api/v1/contacts/{id} (atualizacao parcial)
- DELETE /api/v1/contacts/{id} (soft delete)
- POST /api/v1/contacts/import (importacao em massa via CSV)
- GET /api/v1/contacts/{id}/timeline (timeline de atividades/interacoes)

1.3 API — EMPRESAS:
- CRUD completo: listagem com filtros, criacao, detalhe (com contatos vinculados), atualizacao, soft delete
- Validacao de CNPJ
- Segmentacao por setor

1.4 API — DEALS:
- CRUD completo com listagem por stage (para Kanban)
- PATCH /api/v1/deals/{id}/move (mover entre stages, registrar no historico)
- Calculo automatico de valor total por stage
- Historico de mudancas de stage
- Filtros: por valor, responsavel, data, probabilidade

1.5 FRONTEND — KANBAN BOARD:
- Instalar dnd-kit para drag-and-drop
- Criar pagina /deals com board Kanban:
  - Colunas representando cada stage (cores diferentes)
  - Cards com: titulo do deal, valor formatado (R$), nome do contato, probabilidade, dias no stage
  - Drag-and-drop entre colunas (ao soltar, chama API para mover)
  - Filtros no topo: por responsavel, range de valor, data
  - Botao para criar novo deal (abre modal com formulario)
- Valor total por coluna exibido no header de cada stage

1.6 FRONTEND — PAGINAS:
- /contacts — Listagem com DataTable (TanStack Table): colunas sortaveis, busca, paginacao, acoes (editar, ver, deletar)
- /contacts/new — Formulario de criacao (React Hook Form + Zod)
- /contacts/[id] — Pagina de detalhe com timeline de atividades, deals vinculados, informacoes da empresa
- /companies — Listagem com DataTable similar
- /companies/new — Formulario de criacao
- /companies/[id] — Detalhe com contatos e deals vinculados

1.7 TESTES:
- Testes unitarios dos services (pytest): criacao de contato, movimentacao de deal, calculo de valores
- Testes de integracao dos endpoints (httpx AsyncClient): CRUD completo de cada recurso
- Teste basico do fluxo Kanban (criar deal, mover entre stages)

CRITERIOS DE CONCLUSAO:
- CRUD funcional para contatos, empresas e deals via API e frontend
- Board Kanban renderiza com drag-and-drop funcional
- Filtros e busca operacionais
- Timeline de atividades registrando interacoes
- Testes passando

Ao finalizar, faca commit com mensagem 'feat: dia 1 - modulo CRM pipeline completo' e push. Tome todas as decisoes sozinho."
```

---

## DIA 2 — Gestao de Usuarios & Dashboard

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 2)
- /docs/10-seguranca.md (RBAC, permissoes, perfis de acesso)
- /docs/04-modulos-detalhados/analytics-reports.md (referencia para dashboard)

Os Dias 0-1 ja foram concluidos. Agora execute o DIA 2 — Gestao de Usuarios & Dashboard.

2.1 GESTAO DE USUARIOS:
- CRUD completo de usuarios: listagem (admin only), criacao com convite por email, edicao, desativacao
- Atribuicao de roles (admin, gerente, comercial, operacional, visualizador)
- Upload de avatar (salvar no MinIO)
- Preferencias de usuario (tema, idioma, notificacoes)

2.2 SISTEMA DE PERMISSOES:
- Implementar middleware granular de permissoes por modulo conforme /docs/10-seguranca.md:
  - Cada modulo tem 4 permissoes: ler, criar, editar, deletar
  - Hierarquia de roles (admin herda todas, gerente herda comercial+operacional, etc.)
  - Decorator/dependency @require_permission('modulo', 'acao')
  - Audit log de acessos negados
- Matriz de permissoes completa para os 5 roles

2.3 DASHBOARD PRINCIPAL:
- Criar pagina / (dashboard) com layout de widgets:
  - Resumo do pipeline: valor total por stage (cards coloridos)
  - Leads do dia: novos contatos criados hoje
  - Tarefas pendentes: deals sem atividade recente
  - Atividades recentes: ultimas 10 acoes no sistema (timeline vertical)
- Dados reais vindos da API (usar React Query para fetch)

2.4 WIDGETS E GRAFICOS:
- Instalar Recharts (ou Tremor)
- Criar graficos:
  - Funil de vendas (deals por stage — grafico de funil ou barras horizontais)
  - Receita por periodo (line chart — ultimos 6 meses)
  - Leads por fonte (pie chart — LinkedIn, Google, indicacao, etc.)
  - Taxa de conversao por stage (bar chart)
- Widgets devem ser responsivos e funcionar em dark mode

2.5 NOTIFICACOES:
- Backend: model notifications (id, user_id, type, title, message, is_read, data_json, created_at)
- API: GET /api/v1/notifications (paginado), PATCH /api/v1/notifications/{id}/read, PATCH /api/v1/notifications/read-all
- Frontend: icone de sino no header com badge de contagem, dropdown com lista de notificacoes, marcar como lida
- Criar service de notificacao no backend para uso por outros modulos

2.6 PERFIL DO USUARIO:
- Pagina /settings/profile: edicao de dados pessoais, upload de foto
- Pagina /settings/security: troca de senha
- Pagina /settings/preferences: tema (light/dark/system), preferencias de notificacao

2.7 TESTES:
- Testes de autorizacao: verificar que cada role so acessa o que deve
- Testes dos endpoints de dashboard
- Testes de notificacao

CRITERIOS DE CONCLUSAO:
- Usuarios podem ser criados e gerenciados com roles diferentes
- Permissoes bloqueiam corretamente acesso a modulos nao autorizados
- Dashboard exibe dados reais do CRM com graficos interativos
- Notificacoes aparecem com badge e podem ser marcadas como lidas

Ao finalizar, commit 'feat: dia 2 - gestao de usuarios, dashboard e notificacoes' e push. Tome todas as decisoes sozinho."
```

---

## DIA 3 — Contratos & Financeiro Base

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 3)
- /docs/04-modulos-detalhados/gestao-contratos.md
- /docs/04-modulos-detalhados/financeiro-erp.md
- /docs/09-banco-dados.md (tabelas de contratos e financeiro)

Os Dias 0-2 ja foram concluidos. Agora execute o DIA 3 — Contratos & Financeiro Base.

3.1 MODELS — CONTRATOS:
- Criar models e migration:
  - contracts (id, title, contract_number, deal_id, company_id, template_id, status, value, start_date, end_date, content_html, pdf_url, created_by, created_at, updated_at)
  - contract_templates (id, name, description, content_html, variables_json, is_active)
  - contract_versions (id, contract_id, version_number, content_html, changed_by, created_at)
  - Status enum: draft, review, approved, sent, signed, active, expired, cancelled

3.2 TEMPLATES DE CONTRATO:
- Criar sistema de templates com Jinja2:
  - Variaveis dinamicas: {{empresa}}, {{cnpj}}, {{valor}}, {{escopo}}, {{prazo}}, {{data_inicio}}, {{data_fim}}
  - API: CRUD de templates, GET /api/v1/contracts/templates
  - Pelo menos 2 templates padrao: Contrato de Servico de Producao, Contrato de Gestao de Redes Sociais
- Endpoint para preview: POST /api/v1/contracts/preview (recebe template_id + dados, retorna HTML renderizado)

3.3 GERACAO DE PDF:
- Instalar WeasyPrint (ou reportlab como fallback)
- POST /api/v1/contracts/{id}/generate-pdf:
  - Renderiza template com dados do deal/empresa
  - Insere logo da Somos Produtora, cabecalho, rodape com paginacao
  - Marca d'agua 'RASCUNHO' para status draft
  - Salva PDF no MinIO, atualiza pdf_url no contrato
- GET /api/v1/contracts/{id}/pdf — retorna o PDF para download

3.4 CICLO DE VIDA:
- Implementar maquina de estados para contratos:
  - draft -> review -> approved -> sent -> signed -> active -> expired/cancelled
  - Cada transicao gera: nova versao, notificacao ao responsavel, registro no audit log
  - Validacoes: so admin/gerente pode aprovar, so pode enviar se aprovado, etc.
- PATCH /api/v1/contracts/{id}/transition (recebe novo status)

3.5 FINANCEIRO BASE:
- Criar models e migration:
  - invoices (id, invoice_number, contract_id, company_id, type [receivable/payable], status [pending/paid/overdue/cancelled], value, due_date, paid_date, paid_value, description, created_at)
  - payments (id, invoice_id, value, payment_date, payment_method, proof_url, notes)
  - cost_centers (id, name, description, budget)
  - Vincular invoices com contratos e empresas

3.6 FRONTEND — CONTRATOS:
- Pagina /contracts — Listagem com DataTable: numero, titulo, empresa, valor, status (badge colorido), data, acoes
- /contracts/new — Wizard: selecionar template -> preencher dados -> preview HTML -> salvar
- /contracts/[id] — Detalhe: informacoes, timeline de versoes, visualizador de PDF integrado, botoes de transicao de status
- Modal de aprovacao/rejeicao com campo de comentario

3.7 FRONTEND — FINANCEIRO:
- Pagina /finance — Dashboard financeiro:
  - Cards: total a receber, total a pagar, saldo, faturas vencidas
  - Grafico: fluxo de caixa mensal (barras — entradas vs saidas)
  - Grafico: receitas vs despesas (line chart)
- /finance/invoices — Tabela de faturas com filtros por tipo, status, periodo
- /finance/invoices/new — Formulario de criacao de fatura

3.8 TESTES:
- Teste de geracao de PDF (verificar que o arquivo e criado corretamente)
- Teste do fluxo completo de contrato: criar -> aprovar -> gerar PDF -> enviar
- Testes de calculos financeiros

CRITERIOS DE CONCLUSAO:
- Contratos criados a partir de templates com dados dinamicos
- PDF gerado com formatacao profissional
- Fluxo de aprovacao funcional com notificacoes
- Dashboard financeiro mostra dados de contas a pagar/receber

Ao finalizar, commit 'feat: dia 3 - contratos com PDF e financeiro base' e push. Tome todas as decisoes sozinho."
```

---

## DIA 4 — Hunter Intelligence

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 4)
- /docs/04-modulos-detalhados/hunter-intelligence.md
- /docs/07-agentes-ia.md (arquitetura de agentes, Crew 1 Prospeccao)
- /docs/08-integracoes.md (SerpAPI, LinkedIn, redes sociais)

Os Dias 0-3 ja foram concluidos. Agora execute o DIA 4 — Hunter Intelligence.

4.1 INFRAESTRUTURA DE AGENTES:
- Instalar e configurar CrewAI + LangChain no backend
- Criar estrutura em backend/app/agents/:
  - base.py (classe base para agentes com logging, rate limiting, fallback de modelos)
  - config.py (configuracao de modelos: Claude 3.5 Sonnet primario, GPT-4o fallback)
  - tools/ (diretorio de ferramentas compartilhadas)
- Configurar Celery queue dedicada 'agents' para execucao assincrona
- Criar model agent_executions (id, agent_type, status, input_data, output_data, tokens_used, cost, duration_seconds, error, started_at, completed_at)
- Logging estruturado de cada execucao de agente

4.2 HUNTER — GOOGLE/SERPAPI:
- Criar agente HunterGoogle usando CrewAI:
  - Tool: SerpAPI search (busca avancada com operadores site:, intitle:)
  - Recebe: criterios de busca (setor, regiao, tipo de empresa)
  - Retorna: lista de leads com nome, empresa, website, email (se disponivel), fonte
  - Rate limiting: max 100 buscas/dia
- Implementar ferramenta de scraping basico de paginas de contato de websites encontrados

4.3 HUNTER — LINKEDIN (SIMULADO):
- Criar agente HunterLinkedIn:
  - Como LinkedIn nao tem API publica de scraping, implementar via busca Google com 'site:linkedin.com/in' e 'site:linkedin.com/company'
  - Extrair dados publicos de perfis via SerpAPI
  - Retorna: nome, cargo, empresa, localizacao, URL do perfil
- Nota: implementar interface para futura integracao com LinkedIn API real

4.4 HUNTER — REDES SOCIAIS (SIMULADO):
- Criar agente HunterSocial:
  - Busca empresas e perfis relevantes via Google com 'site:instagram.com' e 'site:facebook.com'
  - Identifica empresas que podem precisar de servicos de producao audiovisual
  - Retorna: nome da empresa, rede social, URL, seguidores (se disponivel), tipo de conteudo

4.5 ENRIQUECIMENTO DE DADOS:
- Pipeline que recebe leads de todos os hunters:
  - Normaliza dados (padroniza nomes, emails, telefones)
  - Remove duplicatas (matching por nome + empresa)
  - Enriquece com dados adicionais quando possivel (CNPJ via consulta publica)
  - Armazena embeddings no Qdrant para busca por similaridade futura

4.6 SCORING DE LEADS:
- Algoritmo de pontuacao 0-100 baseado em:
  - Fit com ICP (setor, tamanho, regiao) — peso 30%
  - Presenca digital (tem website, redes sociais ativas) — peso 20%
  - Sinais de necessidade (conteudo desatualizado, sem video) — peso 25%
  - Completude dos dados (email, telefone, CNPJ) — peso 25%
- Salvar score no campo score do contato

4.7 FRONTEND — HUNTER DASHBOARD:
- Pagina /hunter — Dashboard de prospeccao:
  - Botao 'Iniciar Busca' (abre modal: selecionar tipo de busca, criterios, iniciar)
  - Tabela de leads descobertos: nome, empresa, score (barra colorida), fonte, data, acoes
  - Acoes rapidas: 'Adicionar ao CRM' (cria contato automaticamente), 'Descartar'
  - Status de execucao dos agentes (executando, concluido, erro)
  - Metricas: total de leads encontrados, media de score, leads por fonte (pie chart)

4.8 TESTES:
- Testes dos agentes com mocks (mockar chamadas a SerpAPI)
- Testes do algoritmo de scoring
- Teste de integracao: hunter encontra lead -> enriquece -> salva no banco

CRITERIOS DE CONCLUSAO:
- Agentes executam buscas via SerpAPI/Google
- Leads sao enriquecidos e pontuados automaticamente
- Dashboard mostra leads descobertos com score e acoes
- Leads podem ser importados para o CRM com um clique

Ao finalizar, commit 'feat: dia 4 - hunter intelligence com agentes de prospeccao' e push. Tome todas as decisoes sozinho."
```

---

## DIA 5 — SDR Automation

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 5)
- /docs/04-modulos-detalhados/sdr-automation.md
- /docs/07-agentes-ia.md (Crew 2 Vendas)
- /docs/08-integracoes.md (WhatsApp Evolution API, Email Resend, LinkedIn)

Os Dias 0-4 ja foram concluidos. Agora execute o DIA 5 — SDR Automation.

5.1 INTEGRACAO — WHATSAPP (EVOLUTION API):
- Criar integracao com Evolution API (ou mock se API key nao disponivel):
  - backend/app/integrations/whatsapp.py
  - Funcoes: enviar mensagem de texto, enviar template, receber webhook de resposta
  - Gestao de sessao (conectar instancia, verificar status)
  - Rate limiting: respeitar limites da API
- Endpoint webhook: POST /api/v1/webhooks/whatsapp (receber respostas)

5.2 INTEGRACAO — EMAIL (RESEND):
- Criar integracao com Resend (ou mock):
  - backend/app/integrations/email.py
  - Funcoes: enviar email HTML, templates responsivos, tracking de abertura/clique
  - Templates HTML de email: introducao comercial, follow-up, proposta de valor
- Endpoint webhook: POST /api/v1/webhooks/email (tracking events)

5.3 INTEGRACAO — LINKEDIN (SIMULADO):
- Criar interface de integracao LinkedIn:
  - Registra acoes planejadas (conexao, mensagem) para execucao manual
  - Tracking de status (pendente, enviado, respondido)
  - Estrutura pronta para futura automacao real

5.4 CADENCIAS DE OUTREACH:
- Models e migration:
  - campaigns (id, name, description, status, target_criteria, created_by, created_at)
  - campaign_steps (id, campaign_id, step_number, channel [whatsapp/email/linkedin], delay_days, template_id, message_template)
  - campaign_enrollments (id, campaign_id, contact_id, current_step, status [active/paused/completed/replied/unsubscribed], enrolled_at)
  - outreach_messages (id, enrollment_id, step_id, channel, content, status [pending/sent/delivered/opened/replied/failed], sent_at, delivered_at, opened_at, replied_at)
- Motor de cadencia (Celery task):
  - Verifica diariamente quais contatos devem receber a proxima mensagem
  - Envia pela canal apropriado
  - Pausa automaticamente se o contato responder

5.5 AGENTE SDR:
- Criar agente SDR usando CrewAI que personaliza mensagens:
  - Recebe: perfil do lead (nome, empresa, setor, cargo)
  - Gera: mensagem personalizada adaptada ao canal (curta para WhatsApp, profissional para email)
  - Adapta tom baseado no setor (corporativo, criativo, startup)
  - Modelo: Claude 3.5 Sonnet para geracao de texto

5.6 QUALIFICACAO AUTOMATICA:
- Agente que classifica respostas recebidas:
  - Categorias: interessado, nao_agora, sem_interesse, reuniao_agendada, spam
  - Usa IA para analisar o texto da resposta
  - Acao automatica: se 'interessado' -> cria deal no CRM, se 'reuniao_agendada' -> notifica comercial
  - Modelo: GPT-4o-mini (rapido e barato para classificacao)

5.7 FRONTEND — SDR DASHBOARD:
- Pagina /sdr — Dashboard de campanhas:
  - Lista de campanhas com status, total de contatos, taxa de resposta
  - /sdr/campaigns/new — Wizard para criar campanha:
    - Passo 1: Nome e descricao
    - Passo 2: Selecionar contatos (filtros ou importar do Hunter)
    - Passo 3: Configurar cadencia (arrastar passos, definir canal e delay)
    - Passo 4: Revisar e iniciar
  - /sdr/campaigns/[id] — Detalhe: metricas (enviados, abertos, respondidos), timeline por contato
  - Graficos: taxa de resposta por canal, conversoes por campanha

5.8 TESTES:
- Testes de envio com mocks das APIs (WhatsApp, Email)
- Teste do motor de cadencia (verificar sequencia e delays)
- Teste de classificacao de resposta (com textos de exemplo)

CRITERIOS DE CONCLUSAO:
- Integracoes WhatsApp e Email configuradas (com mocks funcionais)
- Cadencias executam automaticamente com delays configurados
- Respostas classificadas automaticamente por IA
- Dashboard mostra metricas de cada campanha

Ao finalizar, commit 'feat: dia 5 - SDR automation com cadencias multicanal' e push. Tome todas as decisoes sozinho."
```

---

## DIA 6 — Integracao CRM + IA

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 6)
- /docs/04-modulos-detalhados/crm-pipeline.md (automacoes)
- /docs/07-agentes-ia.md (integracao entre crews)

Os Dias 0-5 ja foram concluidos. Agora execute o DIA 6 — Integracao CRM + IA.

6.1 PIPELINE AUTOMATIZADO:
- Implementar fluxo end-to-end automatico:
  - Hunter descobre lead -> score calculado -> se score >= 60: cria contato no CRM automaticamente
  - Contato criado -> inicia cadencia SDR automaticamente (campanha padrao)
  - Resposta positiva do SDR -> cria deal no pipeline (stage 'Novo Lead')
  - Deal criado -> notifica responsavel comercial
- Celery tasks encadeadas para orquestrar o fluxo
- Configuracao de thresholds editavel pelo admin

6.2 SCORING V2 — DINAMICO:
- Evoluir o scoring para combinar dados do Hunter + SDR:
  - Score base (Hunter): fit com ICP, dados, presenca digital
  - Score de engajamento (SDR): abriu email (+5), respondeu (+15), clicou link (+10), agendou reuniao (+30)
  - Score decai com o tempo (lead frio perde pontos)
- Atualizar score automaticamente a cada interacao
- Exibir historico de score no perfil do contato

6.3 AUTOMACOES DE PIPELINE:
- Criar sistema de triggers/automacoes:
  - deal muda de stage -> notifica responsavel
  - deal parado > 3 dias no mesmo stage -> alerta automatico
  - deal qualificado (stage >= Reuniao Agendada) -> sugere proxima acao com IA
  - deal fechado ganho -> cria invoice automaticamente
- Model automations (id, name, trigger_type, trigger_config, action_type, action_config, is_active)
- Celery beat verifica triggers periodicamente

6.4 ASSISTENTE COMERCIAL:
- Criar agente IA 'Assistente Comercial':
  - Endpoint: POST /api/v1/deals/{id}/ai-suggestions
  - Analisa perfil do lead, historico de interacoes, estagio atual
  - Sugere: melhor horario para contato, canal preferido, argumentos de venda personalizados, objecoes provaveis e respostas sugeridas
  - Modelo: Claude 3.5 Sonnet
- Exibir sugestoes como card no detalhe do deal

6.5 RELATORIOS COMERCIAIS:
- Endpoints de relatorios:
  - GET /api/v1/reports/pipeline — funil de conversao com taxas entre stages
  - GET /api/v1/reports/velocity — velocidade media por stage (dias)
  - GET /api/v1/reports/forecast — previsao de receita baseada em deals abertos * probabilidade
  - GET /api/v1/reports/performance — performance por vendedor, canal, campanha
- Filtros por periodo (7d, 30d, 90d, custom)

6.6 FRONTEND — INTEGRACOES:
- Pagina /agents — Painel de status dos agentes:
  - Cards por agente: nome, status (ativo/parado/erro), ultima execucao, tokens consumidos
  - Log de atividades recentes
  - Botao para executar manualmente
- Pagina /reports — Dashboard de relatorios comerciais:
  - Funil de conversao visual (grafico de funil)
  - Velocity chart (barras horizontais por stage)
  - Forecast de receita (numero grande + grafico de tendencia)
  - Tabela de performance por vendedor
- /settings/automations — Configurar regras de automacao (ligar/desligar, editar thresholds)

6.7 TESTES E2E:
- Teste completo do fluxo: hunter descobre lead -> enriquece -> SDR aborda -> resposta positiva -> deal criado -> movimentacao no pipeline
- Testes dos relatorios com dados mockados
- Teste das automacoes (trigger dispara corretamente)

CRITERIOS DE CONCLUSAO:
- Fluxo completo Hunter -> SDR -> CRM funcionando end-to-end
- Scoring dinamico atualizando conforme interacoes
- Automacoes de pipeline disparando corretamente
- Relatorios comerciais com dados reais

Ao finalizar, commit 'feat: dia 6 - integracao CRM + IA com automacoes e relatorios' e push. Tome todas as decisoes sozinho."
```

---

## DIA 7 — Financeiro ERP & Inventario

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 7)
- /docs/04-modulos-detalhados/financeiro-erp.md
- /docs/04-modulos-detalhados/inventario.md
- /docs/09-banco-dados.md (tabelas financeiras e de inventario)

Os Dias 0-6 ja foram concluidos. Agora execute o DIA 7 — Financeiro ERP & Inventario.

7.1 NOTAS FISCAIS:
- Criar integracao simulada com API de NF-e:
  - Model: fiscal_notes (id, invoice_id, nf_number, nf_series, status [pending/authorized/cancelled], xml_url, pdf_url, access_key, issuer_cnpj, recipient_cnpj, value, tax_data_json, issued_at)
  - Endpoint: POST /api/v1/finance/fiscal-notes (emitir NF-e — salva com status simulado)
  - Endpoint: GET /api/v1/finance/fiscal-notes/{id} (consultar)
  - Endpoint: POST /api/v1/finance/fiscal-notes/{id}/cancel (cancelar)
  - Gerar PDF da nota fiscal (formato simplificado)
- Estrutura preparada para integracao com NFe.io ou Focus NFe no futuro

7.2 PAGAMENTOS:
- Expandir modelo de payments existente:
  - Registro de pagamentos parciais e totais
  - Vinculo com invoices
  - Upload de comprovante (salvar no MinIO)
  - Calculo automatico de saldo restante
  - Alertas de faturas vencidas (Celery beat — verifica diariamente, notifica responsavel)
- Endpoints CRUD completos com filtros

7.3 FLUXO DE CAIXA:
- Endpoint: GET /api/v1/finance/cashflow?period=monthly&months=12
  - Retorna: entradas e saidas por mes, saldo acumulado
  - Projecao futura baseada em invoices a receber com due_date futuro
  - Categorizacao por centro de custo
- Endpoint: GET /api/v1/finance/summary — resumo geral (total a receber, a pagar, saldo, vencidos)

7.4 DRE SIMPLIFICADO:
- Endpoint: GET /api/v1/finance/dre?year=2026&month=2
  - Receita bruta (total de invoices receivable pagas no periodo)
  - Deducoes (impostos estimados)
  - Receita liquida
  - Custos dos servicos (invoices payable pagas no periodo)
  - Despesas operacionais
  - Lucro/prejuizo do periodo
- Exportar DRE em PDF

7.5 INVENTARIO — MODELS:
- Criar models e migration:
  - equipment (id, name, description, model, serial_number, category_id, status [available/in_use/maintenance/retired], location, purchase_date, purchase_value, current_value, photo_url, qr_code, notes, created_at)
  - equipment_categories (id, name, description, icon)
  - equipment_bookings (id, equipment_id, project_id, booked_by, start_date, end_date, status [pending/confirmed/active/returned/cancelled], notes)
  - maintenance_records (id, equipment_id, type [preventive/corrective], description, cost, performed_by, performed_at, next_maintenance_date)

7.6 INVENTARIO — FUNCIONALIDADES:
- CRUD completo de equipamentos com upload de foto
- Sistema de reserva/agendamento:
  - POST /api/v1/inventory/equipment/{id}/book (reservar para projeto)
  - Verificacao de conflitos de agenda
  - Calendario de disponibilidade por equipamento
- Historico de uso por equipamento
- Alertas de manutencao (Celery beat verifica next_maintenance_date)
- Geracao de QR code unico por equipamento (biblioteca qrcode)

7.7 FRONTEND — FINANCEIRO EXPANDIDO:
- /finance — Dashboard ERP completo:
  - DRE visual (tabela estilizada com cores para lucro/prejuizo)
  - Fluxo de caixa interativo (bar chart — entradas verdes, saidas vermelhas, linha de saldo)
  - Cards resumo: receita do mes, despesas, lucro, faturas vencidas
- /finance/fiscal — Listagem de NFs com status, acoes (emitir, cancelar, ver PDF)
- /finance/payments — Tabela de pagamentos com filtros

7.8 FRONTEND — INVENTARIO:
- /inventory — Catalogo visual de equipamentos:
  - Grid de cards com foto, nome, categoria, status (badge colorido)
  - Filtros por categoria e status
  - Busca por nome ou numero de serie
- /inventory/[id] — Detalhe: informacoes, foto, historico de uso, manutencoes, calendario de reservas
- /inventory/[id]/book — Formulario de reserva (selecionar projeto, datas)
- /inventory/calendar — Calendario visual (tipo timeline) mostrando reservas de todos os equipamentos

CRITERIOS DE CONCLUSAO:
- Notas fiscais podem ser criadas e consultadas
- Fluxo de caixa e DRE exibem dados consolidados
- Equipamentos cadastrados com status e disponibilidade
- Reservas de equipamento funcionais com calendario visual

Ao finalizar, commit 'feat: dia 7 - financeiro ERP completo e inventario de equipamentos' e push. Tome todas as decisoes sozinho."
```

---

## DIA 8 — Fornecedores, Orcamento & Projetos

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 8)
- /docs/04-modulos-detalhados/fornecedores.md
- /docs/04-modulos-detalhados/orcamento-inteligente.md
- /docs/04-modulos-detalhados/gestao-projetos.md
- /docs/09-banco-dados.md (tabelas de fornecedores, orcamentos e projetos)

Os Dias 0-7 ja foram concluidos. Agora execute o DIA 8 — Fornecedores, Orcamento & Projetos.

8.1 FORNECEDORES — CRUD:
- Models e migration:
  - suppliers (id, name, company_name, cnpj, email, phone, categories, service_area, city, state, rating, portfolio_url, notes, is_active, created_at)
  - supplier_categories (id, name, description) — ex: Videomaker, Fotografo, Editor, Locacao, Catering, Cenografia
  - supplier_history (id, supplier_id, project_id, service, value, rating, feedback, date)
- CRUD completo com filtros por categoria, regiao, rating
- Upload de portfolio/documentos no MinIO

8.2 FORNECEDORES — COMPARATIVO:
- Endpoint: GET /api/v1/suppliers/compare?category=videomaker&region=SP
  - Retorna tabela comparativa: nome, rating medio, preco medio, quantidade de projetos, pontualidade
  - Ranking por criterio (preco, qualidade, pontualidade)
- Sugestao automatica: dado um tipo de projeto, sugere os top 3 fornecedores

8.3 ORCAMENTO INTELIGENTE:
- Criar agente IA 'Budget Generator':
  - Endpoint: POST /api/v1/budgets/generate
  - Recebe: briefing do projeto (tipo, duracao, locacao, entregaveis, prazo)
  - Consulta: fornecedores cadastrados, equipamentos disponiveis, historico de custos
  - Gera: orcamento detalhado com itens:
    - Equipe (profissionais necessarios, diarias)
    - Equipamentos (aluguel ou proprio, custo)
    - Locacoes e logistica
    - Pos-producao (edicao, color grading, motion)
    - Custos administrativos e margem
  - Modelo: Claude 3.5 Sonnet para analise e geracao
- Model: budgets (id, project_id, deal_id, title, briefing, items_json, subtotal, taxes, margin_percent, total, status [draft/sent/approved/rejected], generated_by, pdf_url, created_at)

8.4 TEMPLATES DE ORCAMENTO:
- Modelos pre-configurados por tipo de producao:
  - Video Institucional, Evento/Cobertura, Gestao de Redes Sociais, Documentario, Campanha Publicitaria
- Cada template define itens padrao que o agente IA ajusta conforme o briefing
- Exportacao para PDF profissional (formatacao similar aos contratos, com logo e identidade visual)

8.5 GESTAO DE PROJETOS — MODELS:
- Models e migration:
  - projects (id, name, description, deal_id, contract_id, budget_id, client_company_id, status [planning/in_progress/review/delivered/closed], start_date, end_date, created_by, created_at)
  - project_tasks (id, project_id, title, description, assignee_id, status [todo/in_progress/review/done], priority [low/medium/high/urgent], due_date, completed_at, position)
  - project_milestones (id, project_id, title, due_date, completed_at, deliverables)
  - project_team (id, project_id, user_id, role, joined_at)
  - project_deliverables (id, project_id, title, description, file_url, status [pending/uploaded/approved/rejected], approved_by, approved_at)

8.6 GESTAO DE PROJETOS — BOARD:
- Board Kanban de tarefas por projeto (reutilizar componente do CRM com adaptacoes):
  - Colunas: A Fazer, Em Andamento, Revisao, Concluido
  - Cards com: titulo, assignee (avatar), prioridade (cor), due date
  - Drag-and-drop entre colunas
  - Adicionar tarefa rapida (inline)
  - Checklists dentro de cada tarefa
  - Comentarios em tarefas

8.7 FRONTEND — ORCAMENTO:
- /budgets — Listagem de orcamentos com status e valores
- /budgets/new — Wizard inteligente:
  - Passo 1: Briefing (formulario: tipo de producao, descricao, prazo, local, entregaveis)
  - Passo 2: IA gera orcamento (loading com animacao, exibir resultado editavel)
  - Passo 3: Ajustar itens manualmente (tabela editavel)
  - Passo 4: Preview PDF -> Salvar/Enviar
- /budgets/[id] — Detalhe com PDF viewer e acoes

8.8 FRONTEND — PROJETOS:
- /projects — Listagem de projetos com status, cliente, datas
- /projects/[id] — Pagina do projeto:
  - Header com info do projeto, cliente, datas, progresso (%)
  - Tabs: Board (kanban), Timeline (milestones), Equipe, Entregaveis, Orcamento
  - Board com drag-and-drop
  - Upload de entregaveis com aprovacao

CRITERIOS DE CONCLUSAO:
- Fornecedores cadastrados com comparativo funcional
- Orcamento gerado por IA a partir de briefing
- PDF de orcamento profissional exportado
- Board de projetos funcional com tarefas e timeline

Ao finalizar, commit 'feat: dia 8 - fornecedores, orcamento inteligente e gestao de projetos' e push. Tome todas as decisoes sozinho."
```

---

## DIA 9 — Content Studio & Social Scheduler

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 9)
- /docs/04-modulos-detalhados/content-studio.md
- /docs/04-modulos-detalhados/social-scheduler.md
- /docs/07-agentes-ia.md (Crew 3 Conteudo)
- /docs/08-integracoes.md (Instagram Graph API, Facebook Pages API, LinkedIn Marketing API)

Os Dias 0-8 ja foram concluidos. Agora execute o DIA 9 — Content Studio & Social Scheduler.

9.1 CONTENT STUDIO — TEXTO:
- Criar agente ContentWriter usando CrewAI:
  - Endpoint: POST /api/v1/content/generate-text
  - Recebe: tipo (post_instagram, post_linkedin, roteiro_video, legenda, copy_ad, carousel), tema, tom (formal, casual, inspiracional), palavras-chave, publico-alvo
  - Retorna: texto gerado, hashtags sugeridas, variantes (3 opcoes)
  - Modelo: Claude 3.5 Sonnet
- Banco de prompts por tipo de conteudo

9.2 CONTENT STUDIO — IMAGEM:
- Criar integracao com OpenAI DALL-E 3 (ou mock):
  - Endpoint: POST /api/v1/content/generate-image
  - Recebe: descricao da imagem, estilo (fotografico, ilustracao, minimalista), formato (quadrado 1:1, stories 9:16, landscape 16:9)
  - Retorna: URL da imagem gerada (salva no MinIO)
  - Fallback: se DALL-E nao disponivel, retorna placeholder com mensagem
- Galeria de imagens geradas para reutilizacao

9.3 EDITOR DE CONTEUDO:
- Models:
  - content_pieces (id, title, body, type, status [draft/review/approved/scheduled/published], media_urls, hashtags, platform, scheduled_for, published_at, published_url, created_by, project_id, created_at)
  - content_assets (id, name, file_url, type [image/video/audio/document], size, mime_type, created_by, created_at)
- API: CRUD completo de content_pieces e content_assets
- Upload de assets para MinIO

9.4 SOCIAL SCHEDULER — SETUP:
- Criar integracoes simuladas (interface pronta para APIs reais):
  - backend/app/integrations/instagram.py (Instagram Graph API — mock)
  - backend/app/integrations/facebook.py (Facebook Pages API — mock)
  - backend/app/integrations/linkedin_marketing.py (LinkedIn Marketing API — mock)
- Cada integracao tem: publicar_post(content, media_url), agendar_post(content, media_url, scheduled_time), obter_metricas()
- Celery task para publicacao agendada: verifica a cada minuto se ha posts para publicar

9.5 CALENDARIO EDITORIAL:
- Model: social_posts (id, content_piece_id, platform [instagram/facebook/linkedin/youtube], scheduled_for, published_at, status [draft/scheduled/publishing/published/failed], engagement_data_json, error_message)
- Endpoint: GET /api/v1/content/calendar?month=2026-03 — retorna posts organizados por dia
- Endpoints CRUD para social_posts

9.6 PUBLICACAO AUTOMATICA:
- Motor de publicacao (Celery worker):
  - Pega posts com status='scheduled' e scheduled_for <= now()
  - Chama integracao da plataforma correspondente
  - Atualiza status para 'published' ou 'failed'
  - Notifica o usuario sobre resultado
  - Retry automatico (max 3 tentativas) em caso de falha

9.7 FRONTEND — CONTENT:
- /content — Workspace criativo:
  - Sidebar esquerda: lista de conteudos (filtros por status, plataforma)
  - Area central: editor de texto (textarea rica ou TipTap) + preview
  - Sidebar direita: gerador IA (formulario: tipo, tom, tema -> gerar texto)
  - Botao para gerar imagem com IA
  - Preview por rede social (mostrar como ficaria no Instagram, LinkedIn, etc. — mockup visual)
- /content/assets — Banco de assets (grid de imagens com upload drag-and-drop)
- /content/calendar — Calendario editorial:
  - Visao mensal e semanal
  - Cards de posts coloridos por plataforma (Instagram roxo, LinkedIn azul, Facebook azul escuro)
  - Drag-and-drop para reagendar (mover entre dias)
  - Clicar no card abre detalhe para editar
  - Botao '+' em cada dia para criar novo post

9.8 TESTES:
- Testes de geracao de texto com mock do modelo de IA
- Testes do motor de agendamento
- Teste de publicacao com mock das APIs de redes sociais

CRITERIOS DE CONCLUSAO:
- Textos gerados por IA com qualidade
- Editor com preview por rede social funcional
- Calendario editorial com agendamento drag-and-drop
- Publicacao automatica funcionando (com mocks das APIs)

Ao finalizar, commit 'feat: dia 9 - content studio e social scheduler' e push. Tome todas as decisoes sozinho."
```

---

## DIA 10 — App Android, Analytics & Integracao Final

```bash
cd /SOMOSAI && claude --dangerously-skip-permissions "Voce e o Lead Developer do projeto Somos Produtora. Continue a implementacao.

LEIA PRIMEIRO:
- /CLAUDE.md
- /docs/11-roadmap-implementacao.md (Dia 10)
- /docs/04-modulos-detalhados/app-android.md
- /docs/04-modulos-detalhados/analytics-reports.md
- /docs/13-metricas-sucesso.md

Os Dias 0-9 ja foram concluidos. Agora execute o DIA 10 — App Android, Analytics & Integracao Final.

10.1 APP MOBILE — SETUP:
- Criar projeto React Native com Expo (na pasta mobile/):
  - npx create-expo-app somos-mobile --template expo-template-blank-typescript
  - Configurar Expo Router (file-system routing)
  - Instalar NativeWind (Tailwind para React Native) ou React Native Paper
  - Configurar tema Somos Produtora (cores: preto, dourado #C9A84C, branco)
  - Configurar Zustand para estado global
  - Configurar HTTP client apontando para a API backend
  - Implementar autenticacao JWT (login, armazenamento seguro de tokens com Expo SecureStore, auto-refresh)
  - Navegacao: Tab bar com icones (Dashboard, Pipeline, Notificacoes, Perfil)

10.2 APP — FUNCIONALIDADES:
- Tela Dashboard:
  - Cards resumo: leads do dia, deals abertos, valor total pipeline, tarefas pendentes
  - Grafico simples: funil de vendas (barras)
- Tela Pipeline:
  - Lista de deals agrupados por stage (scroll horizontal entre stages)
  - Tocar no deal abre detalhe com informacoes e acoes rapidas
- Tela Notificacoes:
  - Lista de notificacoes com badge de nao-lidas
  - Configurar Expo Notifications para push (setup basico, sem Firebase completo se nao houver credenciais)
- Tela Perfil:
  - Informacoes do usuario, logout
- Tela de Aprovacao Rapida:
  - Lista de itens pendentes de aprovacao (conteudo, orcamentos, contratos)
  - Botoes aprovar/rejeitar inline

10.3 ANALYTICS — DASHBOARD CENTRAL:
- Backend: consolidar metricas de todos os modulos:
  - GET /api/v1/analytics/overview — KPIs principais
  - GET /api/v1/analytics/sales — metricas de vendas (funil, receita, conversao, velocity)
  - GET /api/v1/analytics/operations — projetos ativos, entregas pendentes, equipamentos em uso
  - GET /api/v1/analytics/marketing — conteudo publicado, alcance estimado, engajamento
  - GET /api/v1/analytics/financial — receita/despesa mensal, margem, forecast
- Frontend: /analytics — Dashboard central com todos os graficos:
  - Section Vendas: funil, receita mensal, taxa de conversao, top vendedores
  - Section Operacoes: projetos por status, entregas por mes, utilizacao de equipamentos
  - Section Marketing: posts publicados, engajamento por plataforma
  - Section Financeiro: receita vs despesa, margem, projecao
- Filtro global de periodo (7d, 30d, 90d, custom)

10.4 ANALYTICS — RELATORIOS PDF:
- Endpoint: POST /api/v1/reports/generate
  - Recebe: tipo (monthly_performance, roi_campaign, project_summary), periodo, filtros
  - Gera PDF profissional com:
    - Capa com logo Somos Produtora e periodo
    - Graficos renderizados como imagens (matplotlib ou similar)
    - Tabelas com dados formatados
    - Resumo executivo gerado por IA (Claude)
  - Salva no MinIO, retorna URL
- Agendamento de relatorios automaticos:
  - Celery beat envia relatorio mensal por email no primeiro dia de cada mes

10.5 INTEGRACAO FINAL — TESTES E2E:
- Verificar e corrigir fluxo completo end-to-end:
  1. Hunter descobre lead via SerpAPI
  2. Lead e enriquecido e pontuado
  3. SDR inicia cadencia de outreach
  4. Resposta positiva classifica lead como interessado
  5. Deal criado automaticamente no pipeline CRM
  6. Deal avanca stages -> contrato gerado -> PDF criado
  7. Projeto criado a partir do deal
  8. Orcamento gerado por IA
  9. Conteudo criado e agendado no calendario editorial
  10. Faturamento registrado no financeiro
  11. Analytics consolida todos os dados
- Corrigir quaisquer bugs encontrados nos fluxos

10.6 PERFORMANCE & OTIMIZACAO:
- Backend:
  - Adicionar cache Redis nos endpoints mais acessados (dashboard, analytics)
  - Otimizar queries com select_related/joinedload onde necessario
  - Verificar N+1 queries
- Frontend:
  - Configurar lazy loading para rotas
  - Verificar bundle size (npm run analyze se disponivel)
  - Adicionar loading states e skeleton screens onde faltam

10.7 DOCUMENTACAO FINAL:
- Atualizar README.md com instrucoes de:
  - Como rodar o projeto localmente (docker-compose up)
  - Variaveis de ambiente necessarias
  - Endpoints principais da API
  - Estrutura do projeto
- Verificar que Swagger/ReDoc esta completo e funcional
- Criar .env.example atualizado com todas as variaveis

10.8 SMOKE TESTS:
- Executar testes de fumaca em todos os modulos:
  - Auth: login, registro, refresh token
  - CRM: criar contato, empresa, deal, mover no kanban
  - Contratos: criar, aprovar, gerar PDF
  - Financeiro: criar fatura, registrar pagamento
  - Hunter: executar busca
  - SDR: criar campanha
  - Inventario: cadastrar equipamento, reservar
  - Projetos: criar projeto, adicionar tarefa
  - Conteudo: gerar texto, agendar post
  - Analytics: verificar dashboards
- Listar quaisquer issues encontradas como TODO no README.md

CRITERIOS DE CONCLUSAO:
- App mobile funcional com features essenciais
- Dashboard de analytics consolidando dados de todos os modulos
- Relatorios PDF gerados automaticamente
- Todos os fluxos end-to-end testados e validados
- Sistema pronto para demonstracao

Ao finalizar, commit 'feat: dia 10 - app mobile, analytics, integracao final e testes' e push. Tome todas as decisoes sozinho."
```

---

## REFERENCIA RAPIDA

| Etapa | O que faz | Comando inicia com |
|-------|-----------|-------------------|
| PRE-INSTALACAO | Instala Homebrew + Node.js + Claude Code | Comandos manuais |
| PASSO 0 | Instala Git, Python, Docker, clona repo em /SOMOSAI | Comandos manuais |
| DIA 0 | Setup & Infraestrutura | `cd /SOMOSAI && claude ...` |
| DIA 1 | CRM Pipeline | `cd /SOMOSAI && claude ...` |
| DIA 2 | Usuarios & Dashboard | `cd /SOMOSAI && claude ...` |
| DIA 3 | Contratos & Financeiro | `cd /SOMOSAI && claude ...` |
| DIA 4 | Hunter Intelligence | `cd /SOMOSAI && claude ...` |
| DIA 5 | SDR Automation | `cd /SOMOSAI && claude ...` |
| DIA 6 | Integracao CRM + IA | `cd /SOMOSAI && claude ...` |
| DIA 7 | Financeiro ERP & Inventario | `cd /SOMOSAI && claude ...` |
| DIA 8 | Fornecedores, Orcamento & Projetos | `cd /SOMOSAI && claude ...` |
| DIA 9 | Content Studio & Social Scheduler | `cd /SOMOSAI && claude ...` |
| DIA 10 | App Mobile, Analytics & Final | `cd /SOMOSAI && claude ...` |

### Commits por Dia

| Dia | Commit Message |
|-----|----------------|
| 0 | `feat: dia 0 - setup e infraestrutura completa` |
| 1 | `feat: dia 1 - modulo CRM pipeline completo` |
| 2 | `feat: dia 2 - gestao de usuarios, dashboard e notificacoes` |
| 3 | `feat: dia 3 - contratos com PDF e financeiro base` |
| 4 | `feat: dia 4 - hunter intelligence com agentes de prospeccao` |
| 5 | `feat: dia 5 - SDR automation com cadencias multicanal` |
| 6 | `feat: dia 6 - integracao CRM + IA com automacoes e relatorios` |
| 7 | `feat: dia 7 - financeiro ERP completo e inventario de equipamentos` |
| 8 | `feat: dia 8 - fornecedores, orcamento inteligente e gestao de projetos` |
| 9 | `feat: dia 9 - content studio e social scheduler` |
| 10 | `feat: dia 10 - app mobile, analytics, integracao final e testes` |

---

## NOTAS IMPORTANTES

1. **Execute na ordem:** PRE-INSTALACAO -> PASSO 0 -> DIA 0 -> DIA 1 -> ... -> DIA 10
2. **Verifique o resultado** antes de passar para o proximo dia. Rode `docker-compose up` e teste manualmente.
3. **Se um dia falhar no meio**, voce pode re-executar o mesmo prompt — o Claude Code vai detectar o que ja existe e continuar de onde parou.
4. **Variaveis de ambiente**: crie um arquivo `.env` baseado no `.env.example` gerado no Dia 0 com suas API keys reais antes de rodar os dias de IA (4-6, 8-9).
5. **API keys necessarias** (pode comecar sem elas, os mocks funcionam):
   - `ANTHROPIC_API_KEY` (Claude) — obrigatoria para o Claude Code funcionar
   - `OPENAI_API_KEY` (GPT-4o + DALL-E) — Dias 4, 5, 9
   - `SERPAPI_API_KEY` — Dia 4
   - Evolution API (WhatsApp) — Dia 5
   - `RESEND_API_KEY` (Email) — Dia 5
6. **Tempo estimado por dia**: 10-14 horas de execucao do Claude Code (varia conforme complexidade).
7. **Custo estimado de tokens**: cada dia pode consumir entre 500K-2M tokens. Monitore seu uso em https://console.anthropic.com.
8. **Pasta do projeto**: tudo fica em `/SOMOSAI/`. Se precisar acessar manualmente: `cd /SOMOSAI`.
9. **Docker Desktop deve estar aberto** antes de executar qualquer DIA. Verifique com `docker ps`.

---

> *Gerado em: Fevereiro 2026*
> *Projeto: Somos Produtora — Sistema Integrado de Gestao Inteligente*
> *Desenvolvedor: Zhuhai Kameda Technology Co., Ltd.*
