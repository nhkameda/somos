# 03 — Árvore de Documentação do Projeto

> **Somos Produtora** — Sistema Integrado de Gestão Comercial e Operacional com IA
> Documento de referência: estrutura completa de arquivos e diretórios do projeto

---

## Visão Geral

Este documento mapeia toda a estrutura de arquivos do projeto **Somos Produtora**, descrevendo a finalidade, conteúdo e justificativa de existência de cada elemento. Serve como guia de navegação para desenvolvedores, auditores e stakeholders que precisam localizar rapidamente qualquer artefato do sistema.

---

## Árvore Completa

```
somos/
├── README.md
├── CLAUDE.md
├── .gitignore
├── assets/
│   ├── ESCOPO.md
│   └── PROMPT.md
├── docs/
│   ├── 01-analise-escopo.md
│   ├── 02-arquitetura-sistema.md
│   ├── 03-arvore-documentacao.md
│   ├── 04-modulos-detalhados/
│   │   ├── hunter-intelligence.md
│   │   ├── sdr-automation.md
│   │   ├── crm-pipeline.md
│   │   ├── gestao-contratos.md
│   │   ├── gestao-projetos.md
│   │   ├── financeiro-erp.md
│   │   ├── inventario.md
│   │   ├── fornecedores.md
│   │   ├── orcamento-inteligente.md
│   │   ├── content-studio.md
│   │   ├── social-scheduler.md
│   │   ├── rh-agentes.md
│   │   ├── intelligence-feed.md
│   │   ├── analytics-reports.md
│   │   └── app-android.md
│   ├── 05-stack-tecnologica.md
│   ├── 06-infraestrutura.md
│   ├── 07-agentes-ia.md
│   ├── 08-integracoes.md
│   ├── 09-banco-dados.md
│   ├── 10-seguranca.md
│   ├── 11-roadmap-implementacao.md
│   ├── 12-estimativa-custos.md
│   └── 13-metricas-sucesso.md
├── backend/
├── frontend/
├── mobile/
└── agents/
```

---

## Descrição Detalhada por Elemento

### Raiz do Projeto (`somos/`)

| Arquivo | Descrição | Conteúdo | Justificativa |
|---------|-----------|----------|---------------|
| `README.md` | Visão geral estratégica do projeto | Apresentação do sistema, objetivos de negócio, stack tecnológica resumida, instruções de setup rápido e links para documentação detalhada | Ponto de entrada obrigatório para qualquer pessoa que acesse o repositório pela primeira vez. Define o contexto e direciona para a documentação específica |
| `CLAUDE.md` | Instruções para Claude Code | Diretivas de comportamento, padrões de código, convenções de nomenclatura, estrutura de pastas esperada e regras de commit que o assistente IA deve seguir | Garante consistência e qualidade do código gerado por IA ao longo de todo o desenvolvimento. Funciona como um "manual do desenvolvedor" para o assistente |
| `.gitignore` | Arquivos ignorados pelo Git | Padrões de exclusão para `node_modules/`, `__pycache__/`, `.env`, arquivos de build, logs, uploads temporários e artefatos de IDE | Evita que arquivos sensíveis (credenciais), temporários ou desnecessários sejam versionados, mantendo o repositório limpo e seguro |

---

### Diretório `assets/`

O diretório `assets/` armazena os **documentos fonte** que originaram todo o planejamento do sistema. São artefatos de referência que não devem ser modificados após a fase de planejamento.

| Arquivo | Descrição | Conteúdo | Justificativa |
|---------|-----------|----------|---------------|
| `ESCOPO.md` | Documento original do escopo (transcrição) | Transcrição completa e fiel do briefing do cliente sobre o que o sistema deve fazer, incluindo todos os módulos, funcionalidades desejadas e regras de negócio descritas em linguagem natural | Preserva a visão original do cliente sem interpretações. Serve como "fonte da verdade" para resolver ambiguidades durante o desenvolvimento. Qualquer decisão de design pode ser rastreada até este documento |
| `PROMPT.md` | Prompt de planejamento | Prompt estruturado utilizado para gerar a análise do escopo, arquitetura e documentação técnica a partir do escopo bruto | Documenta a metodologia de planejamento utilizada. Permite reproduzir ou refinar o processo de análise caso seja necessário replanejar módulos específicos |

---

### Diretório `docs/`

O diretório `docs/` contém toda a **documentação técnica e de negócio** do projeto, organizada em ordem numérica progressiva — partindo da análise conceitual até métricas de sucesso.

#### Documentos de Análise e Arquitetura

| Arquivo | Descrição | Conteúdo | Justificativa |
|---------|-----------|----------|---------------|
| `01-analise-escopo.md` | Análise estruturada do escopo | Decomposição do escopo bruto em requisitos funcionais e não funcionais organizados por módulo, com priorização (P0/P1/P2), identificação de dependências entre módulos e mapeamento de riscos técnicos | Transforma o escopo narrativo em requisitos acionáveis para desenvolvimento. É o documento que conecta "o que o cliente pediu" com "o que será construído" |
| `02-arquitetura-sistema.md` | Arquitetura completa do sistema | Diagrama de arquitetura de alto nível, definição de microsserviços/módulos, fluxos de dados entre componentes, padrões de comunicação (REST, WebSocket, filas), estratégia de deploy e escalabilidade | Define as decisões técnicas fundamentais que impactam todo o desenvolvimento. Alterações neste documento afetam múltiplos módulos e devem passar por revisão criteriosa |
| `03-arvore-documentacao.md` | Este documento (árvore de arquivos) | Mapa completo de todos os arquivos e diretórios do projeto com descrições detalhadas de propósito e conteúdo | Facilita a navegação no projeto e onboarding de novos membros. Também serve como checklist para garantir que toda documentação planejada foi criada |

#### Subdiretório `04-modulos-detalhados/`

Este subdiretório contém a **documentação individual de cada módulo** do sistema. Cada arquivo detalha um módulo específico com: visão geral, requisitos funcionais, modelo de dados, endpoints da API, fluxos de usuário, integrações com outros módulos e critérios de aceite.

| Arquivo | Módulo | Descrição Detalhada |
|---------|--------|---------------------|
| `hunter-intelligence.md` | Hunter Intelligence | Módulo de prospecção ativa com agentes IA que vasculham LinkedIn, Google, redes sociais e diretórios empresariais para identificar leads potenciais. Documenta os scrapers, algoritmos de scoring, fontes de dados e pipeline de enriquecimento de leads |
| `sdr-automation.md` | SDR Automation | Módulo de automação de abordagem comercial (SDR virtual). Cobre os fluxos de outreach multicanal (WhatsApp via Evolution API, LinkedIn, e-mail), cadências de follow-up, templates de mensagem com IA e regras de qualificação automática |
| `crm-pipeline.md` | CRM Pipeline | Sistema de gestão do funil de vendas com board Kanban. Detalha as etapas do pipeline, regras de movimentação de deals, campos customizáveis, integração com contatos/empresas e gatilhos de automação |
| `gestao-contratos.md` | Gestão de Contratos | Módulo de criação, edição e gestão do ciclo de vida de contratos. Inclui geração automática de PDF a partir de templates, assinatura digital, controle de vencimentos e alertas de renovação |
| `gestao-projetos.md` | Gestão de Projetos | Sistema de gerenciamento de projetos de produção audiovisual. Documenta boards de tarefas, timelines, alocação de recursos (equipe e equipamentos), milestones e entregas por projeto |
| `financeiro-erp.md` | Financeiro / ERP | Módulo financeiro completo com contas a pagar e receber, emissão de notas fiscais (integração com APIs de NF-e), fluxo de caixa, DRE simplificado e conciliação bancária |
| `inventario.md` | Inventário | Controle de equipamentos e materiais da produtora. Rastreamento de câmeras, lentes, iluminação, áudio e acessórios com status (disponível, em uso, manutenção), histórico de uso e agendamento |
| `fornecedores.md` | Fornecedores | Base de dados de fornecedores com avaliação de performance, histórico de contratações, comparativo de preços e categorização por tipo de serviço (locação, freelancers, pós-produção, etc.) |
| `orcamento-inteligente.md` | Orçamento Inteligente | Gerador de orçamentos com IA que analisa briefings, sugere composição de equipe, equipamentos e custos com base em projetos similares anteriores. Inclui templates customizáveis e exportação para PDF |
| `content-studio.md` | Content Studio | Plataforma de criação de conteúdo com IA. Geração de textos (posts, roteiros, legendas) via Claude/GPT-4o e imagens via DALL-E/Stable Diffusion. Inclui editor visual, banco de assets e versionamento |
| `social-scheduler.md` | Social Scheduler | Agendador e publicador de conteúdo para redes sociais. Suporte a Instagram, Facebook, LinkedIn, TikTok e YouTube. Calendário editorial, preview de posts, analytics básico e publicação automática |
| `rh-agentes.md` | RH & Agentes | Gestão de equipe interna e freelancers. Cadastro de profissionais, habilidades, disponibilidade, histórico de projetos, controle de pagamentos e avaliação de desempenho |
| `intelligence-feed.md` | Intelligence Feed | Feed inteligente que agrega notícias, tendências e oportunidades do mercado audiovisual e dos setores-alvo dos clientes. Usa IA para filtrar, resumir e alertar sobre informações relevantes |
| `analytics-reports.md` | Analytics & Reports | Dashboard central de indicadores e geração de relatórios. Métricas de vendas, marketing, operações e financeiro consolidadas com visualizações interativas e exportação para PDF/Excel |
| `app-android.md` | App Android | Aplicativo mobile Android (React Native) para acesso em campo. Funcionalidades essenciais: notificações push, visualização de pipeline, aprovações rápidas, checklist de produção e câmera para documentação |

#### Documentos Técnicos e Operacionais

| Arquivo | Descrição | Conteúdo | Justificativa |
|---------|-----------|----------|---------------|
| `05-stack-tecnologica.md` | Stack tecnológica | Definição completa de todas as tecnologias utilizadas: linguagens, frameworks, bibliotecas, ferramentas de build, linters, formatadores e suas versões. Inclui justificativa para cada escolha tecnológica | Centraliza as decisões de tecnologia para evitar duplicidade ou inconsistência. Facilita setup de ambiente e troubleshooting |
| `06-infraestrutura.md` | Infraestrutura | Arquitetura de deploy: servidores (VPS), Docker Compose, Nginx/Traefik, certificados SSL, estratégia de backup, monitoramento (Prometheus/Grafana), logs centralizados e plano de disaster recovery | Garante que o sistema pode ser implantado, mantido e recuperado de forma confiável e reproduzível |
| `07-agentes-ia.md` | Agentes de IA | Documentação detalhada de todos os agentes inteligentes do sistema: arquitetura (CrewAI/LangChain), prompts, ferramentas (tools), fluxos de trabalho, modelos utilizados e estratégias de fallback | Os agentes IA são o diferencial competitivo do sistema. Este documento garante que podem ser mantidos, otimizados e debugados |
| `08-integracoes.md` | Integrações | Mapeamento de todas as integrações externas: WhatsApp (Evolution API), LinkedIn, Google APIs, SerpAPI, gateways de pagamento, APIs de NF-e, redes sociais e serviços de e-mail (Resend) | Consolida informações de APIs de terceiros, suas limitações, rate limits, custos e alternativas de fallback |
| `09-banco-dados.md` | Banco de Dados | Modelagem completa do banco de dados: esquema relacional (PostgreSQL), estrutura de cache (Redis), índices vetoriais (Qdrant) e object storage (MinIO). Inclui diagrama ER, migrations e políticas de retenção | Define a fundação de dados do sistema. Decisões de modelagem impactam diretamente a performance e escalabilidade |
| `10-seguranca.md` | Segurança | Políticas de segurança: autenticação (JWT), autorização (RBAC com granularidade por módulo), criptografia de dados sensíveis, LGPD compliance, auditoria de acessos, proteção contra OWASP Top 10 e hardening de infraestrutura | Segurança não é opcional. Este documento garante que o sistema protege dados de clientes e está em conformidade com a legislação brasileira |
| `11-roadmap-implementacao.md` | Roadmap de Implementação | Plano detalhado de execução em 10 dias com sprints diários, milestones, entregáveis, dependências e critérios de "done" para cada dia | Permite acompanhar o progresso do desenvolvimento e identificar atrasos antes que se tornem críticos |
| `12-estimativa-custos.md` | Estimativa de Custos | Projeção financeira completa: custo de desenvolvimento, infraestrutura mensal, APIs de IA, ferramentas e serviços. Inclui análise de ROI e payback | Permite ao cliente tomar decisão informada sobre o investimento e planejar fluxo de caixa para manutenção do sistema |
| `13-metricas-sucesso.md` | Métricas de Sucesso | KPIs de negócio, produtividade e técnicos com metas por período (30/60/90 dias). Define como o sucesso do sistema será mensurado objetivamente | Sem métricas claras, não há como avaliar se o sistema está entregando valor. Este documento estabelece o framework de avaliação |

---

### Diretórios de Código-Fonte

Estes diretórios contêm (ou conterão) o código-fonte efetivo do sistema, organizados por camada da aplicação.

#### `backend/` — API e Lógica de Negócio

| Aspecto | Detalhes |
|---------|----------|
| **Framework** | FastAPI (Python 3.12+) |
| **Estrutura planejada** | Arquitetura modular com separação por domínio (modules/), cada módulo contendo seus próprios routers, services, schemas e models |
| **Subdiretórios esperados** | `app/`, `modules/`, `core/`, `database/`, `tests/`, `migrations/` |
| **Responsabilidade** | Toda a lógica de negócio, APIs REST, WebSockets, processamento em background (Celery), integrações com serviços externos e orquestração dos agentes IA |
| **Justificativa** | FastAPI oferece performance assíncrona nativa, tipagem forte com Pydantic, documentação automática (Swagger/ReDoc) e ecossistema Python rico para IA/ML |

#### `frontend/` — Interface Web

| Aspecto | Detalhes |
|---------|----------|
| **Framework** | Next.js 15 (React 19, App Router) |
| **Estrutura planejada** | App Router com rotas agrupadas por módulo, componentes compartilhados, hooks customizados e state management com Zustand |
| **Subdiretórios esperados** | `app/`, `components/`, `lib/`, `hooks/`, `stores/`, `types/`, `public/` |
| **Responsabilidade** | Interface do usuário completa: dashboards, formulários, boards Kanban, calendários, editores de conteúdo, visualizações de dados e gestão de todos os módulos |
| **Justificativa** | Next.js 15 com Server Components oferece SSR/SSG para performance, ShadcnUI para componentes acessíveis e customizáveis, e TailwindCSS para estilização eficiente |

#### `mobile/` — Aplicativo Android

| Aspecto | Detalhes |
|---------|----------|
| **Framework** | React Native (Expo) |
| **Estrutura planejada** | Expo Router para navegação, componentes nativos otimizados para Android, offline-first para funcionalidades críticas |
| **Subdiretórios esperados** | `app/`, `components/`, `lib/`, `assets/`, `hooks/` |
| **Responsabilidade** | Versão mobile com funcionalidades essenciais: notificações push (Firebase), visualização de pipeline, aprovações rápidas, checklist de produção e captura de fotos/vídeos |
| **Justificativa** | React Native permite compartilhar lógica com o frontend web, reduzindo tempo de desenvolvimento. Expo simplifica build e distribuição |

#### `agents/` — Agentes de Inteligência Artificial

| Aspecto | Detalhes |
|---------|----------|
| **Frameworks** | CrewAI e LangChain (Python) |
| **Estrutura planejada** | Agentes organizados por função (prospecção, SDR, conteúdo, análise), cada um com definição de papel, ferramentas, prompts e workflows |
| **Subdiretórios esperados** | `crews/`, `tools/`, `prompts/`, `configs/`, `tests/` |
| **Responsabilidade** | Todos os agentes autônomos e semi-autônomos do sistema: prospecção de leads, qualificação, outreach automatizado, geração de conteúdo, análise de mercado e recomendações inteligentes |
| **Justificativa** | Separar os agentes IA em diretório próprio permite desenvolvimento, teste e deploy independente. CrewAI facilita orquestração de multi-agentes enquanto LangChain provê ferramentas e abstrações de LLM |

---

## Convenções de Nomenclatura

| Tipo | Convenção | Exemplo |
|------|-----------|---------|
| Documentação | Prefixo numérico + nome descritivo em kebab-case | `01-analise-escopo.md` |
| Módulos detalhados | Nome do módulo em kebab-case | `hunter-intelligence.md` |
| Diretórios de código | Nome em lowercase, singular | `backend/`, `frontend/` |
| Arquivos de configuração | Uppercase para raiz | `README.md`, `CLAUDE.md` |

---

## Ordem de Leitura Recomendada

Para compreensão completa do projeto, recomenda-se a seguinte sequência:

1. `README.md` — Contexto geral
2. `assets/ESCOPO.md` — Entender o que o cliente solicitou
3. `docs/01-analise-escopo.md` — Ver como o escopo foi interpretado
4. `docs/02-arquitetura-sistema.md` — Entender a solução técnica
5. `docs/03-arvore-documentacao.md` — Navegar pela documentação (este arquivo)
6. `docs/04-modulos-detalhados/` — Aprofundar em módulos específicos
7. `docs/05-stack-tecnologica.md` a `docs/10-seguranca.md` — Detalhes técnicos
8. `docs/11-roadmap-implementacao.md` — Plano de execução
9. `docs/12-estimativa-custos.md` — Investimento necessário
10. `docs/13-metricas-sucesso.md` — Como medir resultados

---

> **Última atualização:** Fevereiro 2026
> **Mantido por:** Zhuhai Kameda Technology
> **Status:** Documento vivo — atualizado conforme o projeto evolui
