# 🚀 CLAUDE CODE — PROMPT INICIAL DE PLANEJAMENTO: SISTEMA SOMOS PRODUTORA

---

## 📋 CONTEXTO E MISSÃO

Você é o **Arquiteto Principal** de um projeto de alta complexidade e alto impacto para a **Somos Produtora**, uma empresa de comunicação audiovisual e agência 360 com quase 30 anos de mercado. Sua missão nesta sessão é **exclusivamente de planejamento e documentação** — nenhuma linha de código deverá ser escrita.

Leia atentamente o escopo completo em `/assets/ESCOPO.md` antes de prosseguir com qualquer ação.

---

## 🎯 OBJETIVO DA SESSÃO

Planejar, arquitetar e documentar um **ecossistema digital completo e integrado** para a Somos Produtora, contemplando:

- Plataforma Web (Dashboard / SaaS interno)
- Backend em servidor Linux
- App Android nativo
- Infraestrutura de Agentes de IA
- Integrações com APIs externas pagas (Claude, OpenAI, Gemini, serviços de imagem, automações de redes sociais)
- Possibilidade de uso de templates pagos premium para acelerar o desenvolvimento

Ao final, entregar toda a **documentação estratégica**, um **README.md** abrangente e um **PDF de proposta profissional** com identidade visual de alto impacto, além de realizar o **commit e push no repositório GitHub `nhkameda/somos`**.

---

## 📁 ESTRUTURA DE TRABALHO

```
/assets/ESCOPO.md          ← Leia este arquivo primeiro (fonte da verdade)
/docs/                     ← Toda documentação de planejamento gerada aqui
/README.md                 ← Documento principal de estratégia e visão
/PROPOSAL.pdf              ← PDF da proposta comercial de desenvolvimento
```

---

## 🔍 FASE 1 — ANÁLISE E COMPREENSÃO DO ESCOPO

**Tarefa:** Leia `/assets/ESCOPO.md` na íntegra e produza internamente um mapa mental dos seguintes elementos antes de continuar:

1. **Dores identificadas** — Quais os problemas centrais que o sistema resolve?
2. **Atores do sistema** — Quem são os usuários? Quem são os agentes de IA? Quais são os papéis?
3. **Fluxos de processo** — Mapeie o funil completo: Hunter → SDR Ativo → Humano Comercial → Pós-Venda → Produção → Financeiro → Entrega
4. **Módulos funcionais** — Identifique todos os módulos distintos mencionados (CRM, ERP, Financeiro, Inventário, RH, Conteúdo, etc.)
5. **Integrações externas** — LinkedIn, WhatsApp, Instagram, Facebook, YouTube, Google, portais fiscais, contabilidade
6. **Requisitos de IA** — Onde agentes autônomos substituem ou assistem humanos?

**Output esperado:** Um documento `/docs/01-analise-escopo.md` com o mapeamento completo e estruturado desses 6 elementos.

---

## 🏗️ FASE 2 — ARQUITETURA DO SISTEMA

**Tarefa:** Com base na análise do escopo, projete a **arquitetura completa do sistema**. Considere as melhores práticas de sistemas SaaS modernos, arquitetura de microsserviços, e sistemas orientados a eventos para suportar agentes de IA autônomos.

### 2.1 — Arquitetura Geral

Defina e documente:

- **Padrão arquitetural** (ex: Microsserviços, Monolito Modular, Event-Driven)
- **Stack tecnológica recomendada** com justificativas para cada escolha
- **Diagrama de componentes** em formato texto/ASCII ou Mermaid
- **Diagrama de fluxo de dados** entre módulos
- **Estratégia de banco de dados** (relacional + vetorial para IA)
- **Estratégia de mensageria** para comunicação entre agentes e serviços
- **Estratégia de autenticação e autorização** (RBAC para múltiplos perfis)

### 2.2 — Módulos do Sistema (definir para cada um)

Para cada módulo abaixo, documente: **propósito, entidades principais, funcionalidades, integrações, tecnologias sugeridas**:

| # | Módulo | Descrição |
|---|--------|-----------|
| 1 | **Hunter Intelligence** | Agentes de IA que buscam leads em múltiplas plataformas |
| 2 | **SDR Automation** | Agentes de IA que qualificam e abordam leads automaticamente |
| 3 | **CRM Pipeline** | Gestão visual do funil comercial com kanban e automações |
| 4 | **Gestão de Contratos** | Geração automatizada de contratos a partir de dados do CRM |
| 5 | **Gestão de Projetos** | Cards de tarefas, sprints, status reports por cliente/projeto |
| 6 | **Financeiro & ERP** | Contas a pagar/receber, notas fiscais, margens, comissões |
| 7 | **Inventário** | Controle de equipamentos, disponibilidade, locações |
| 8 | **Base de Fornecedores** | Freelancers, equipamentos externos, serviços terceirizados |
| 9 | **Orçamento Inteligente** | Gerador de orçamentos com base no inventário e serviços |
| 10 | **Content Studio** | Geração de imagens, PDFs, posts, carrosséis via IA |
| 11 | **Social Scheduler** | Agendamento e publicação de conteúdo nas redes sociais |
| 12 | **RH & Agentes** | Gestão de agentes IA + humanos, metas, avaliações, substituições |
| 13 | **Intelligence Feed** | Agentes de tendências: busca constante de oportunidades de mercado |
| 14 | **Analytics & Reports** | Dashboards executivos, KPIs, gráficos de performance |
| 15 | **App Android** | Acompanhamento de projetos e notificações para clientes e equipe |

### 2.3 — Infraestrutura de Servidor Linux

Documente a infraestrutura recomendada:

- Configuração de servidor(es) — CPU, RAM, Storage recomendados
- Sistema operacional e distribuição Linux
- Containers (Docker / Kubernetes)
- Reverse proxy (Nginx / Caddy)
- SSL/TLS, segurança e firewall
- CI/CD pipeline
- Estratégia de backup e disaster recovery
- Monitoramento e observabilidade (logs, métricas, alertas)

### 2.4 — Ecossistema de Agentes de IA

Documente a arquitetura dos agentes:

- **Framework de agentes** recomendado (LangChain, CrewAI, AutoGen, Agentiq, etc.)
- **Modelos de IA utilizados por módulo:**
  - Modelos de linguagem (Claude 3.5 Sonnet, GPT-4o, Gemini Pro, etc.)
  - Modelos de geração de imagem (DALL-E 3, Midjourney API, Stable Diffusion, Flux)
  - Modelos de embeddings para busca semântica
- **Memória e contexto dos agentes** — como cada agente mantém estado
- **Orquestração** — como agentes se comunicam e delegam tarefas
- **Avaliação e melhoria contínua dos agentes** (feedback loop, scoring de performance)
- **Segurança** — rate limits, auditoria de ações dos agentes

### 2.5 — Integrações Externas

Mapeie cada integração necessária:

- **Redes Sociais:** LinkedIn API, Instagram Graph API, Facebook API, YouTube Data API
- **Mensageria:** WhatsApp Business API (Twilio/Z-API/Evolution API)
- **Email:** SMTP / SendGrid / Resend
- **Fiscal:** Portal de Notas Fiscais (Nota Fiscal Paulista / sistemas municipais)
- **Financeiro:** Bancos Open Finance, geração de boletos
- **Modelos de IA:** Anthropic API, OpenAI API, Google AI API, Replicate
- **Design:** Canva API, Adobe Express API (para templates)
- **Busca:** Google Custom Search API, SerpAPI, ProxyCrawl

**Output esperado:** Um documento `/docs/02-arquitetura-sistema.md` com toda a arquitetura detalhada, diagramas e justificativas técnicas.

---

## 📂 FASE 3 — ÁRVORE COMPLETA DE DOCUMENTAÇÃO

**Tarefa:** Crie a estrutura completa de pastas e arquivos do projeto, explicando o propósito de cada elemento.

A árvore deve contemplar:

```
somos/
├── README.md                          ← Visão geral e estratégia
├── PROPOSAL.pdf                       ← Proposta comercial
├── CLAUDE.md                          ← Instruções para Claude Code
├── .gitignore
├── assets/
│   └── ESCOPO.md
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
├── backend/                           ← (estrutura vazia planejada)
├── frontend/                          ← (estrutura vazia planejada)
├── mobile/                            ← (estrutura vazia planejada)
└── agents/                            ← (estrutura vazia planejada)
```

**Output esperado:** Documento `/docs/03-arvore-documentacao.md` com a árvore completa e descrição de cada elemento. Crie fisicamente todas as pastas e arquivos `.md` vazios conforme a estrutura acima.

---

## 📄 FASE 4 — DOCUMENTAÇÃO DETALHADA DE CADA MÓDULO

**Tarefa:** Para **cada um dos 15 módulos** listados na Fase 2.2, crie um documento detalhado em `/docs/04-modulos-detalhados/[nome-modulo].md` contendo:

```markdown
# [Nome do Módulo]

## Propósito e Visão
[O que resolve, por que existe, qual o impacto no negócio]

## Atores Envolvidos
[Humanos e/ou agentes de IA que interagem com este módulo]

## Funcionalidades Principais
[Lista detalhada de todas as funcionalidades]

## Fluxo de Dados
[Como os dados entram, são processados e saem]

## Entidades e Modelo de Dados
[Principais entidades, atributos e relacionamentos]

## Interface e UX
[Como será a experiência visual e de uso]

## Integrações
[APIs e serviços externos utilizados]

## Agentes de IA
[Se aplicável: papel, modelo, ferramentas, memória]

## Tecnologias Recomendadas
[Stack específica para este módulo]

## Dependências
[Quais outros módulos este depende e quais dependem dele]

## Métricas de Sucesso
[Como medir se este módulo está funcionando bem]
```

---

## 📊 FASE 5 — DOCUMENTOS COMPLEMENTARES

Crie os seguintes documentos:

### `/docs/05-stack-tecnologica.md`
Stack completa com justificativas:
- **Backend:** Node.js + Fastify OU Python + FastAPI — justifique
- **Frontend:** Next.js 15 + TypeScript + Tailwind + ShadcnUI
- **Mobile:** React Native OU Flutter — justifique
- **Database:** PostgreSQL + Redis + Qdrant (vetorial) + MinIO (arquivos)
- **Agentes:** Framework recomendado + modelos por função
- **Infra:** Docker + Nginx + GitHub Actions CI/CD
- **Monitoramento:** Grafana + Prometheus + Sentry

### `/docs/06-infraestrutura.md`
- Diagrama de infraestrutura completo
- Especificações de hardware recomendadas
- Estratégia de deploy
- Configurações de segurança

### `/docs/07-agentes-ia.md`
- Catálogo completo de todos os agentes
- Persona, objetivo, ferramentas, modelo, memória
- Como são criados, avaliados e substituídos
- Custo estimado de tokens por agente/mês

### `/docs/08-integracoes.md`
- Mapa completo de todas as integrações
- Autenticação (OAuth, API Keys, Webhooks)
- Rate limits e estratégias de fallback

### `/docs/09-banco-dados.md`
- Diagrama ERD completo (todas as entidades)
- Schema inicial das tabelas principais
- Estratégia de migrations
- Índices e otimizações

### `/docs/10-seguranca.md`
- Modelo de autenticação e autorização (RBAC)
- Perfis de acesso (Admin, Diretor, Comercial, Agente, Cliente)
- Criptografia, LGPD, auditoria

### `/docs/11-roadmap-implementacao.md`
- Roadmap de 10 dias dividido em sprints
- Day 0: Setup e infraestrutura
- Days 1-3: Core do sistema (Auth, CRM base, Database)
- Days 4-6: Módulos Hunter + SDR + Pipeline
- Days 7-8: Financeiro + Inventário + Contratos
- Days 9-10: Content Studio + Android App + Agentes IA
- Milestones e entregáveis por dia

### `/docs/12-estimativa-custos.md`
- Custo de desenvolvimento (horas × valor)
- Custo de infraestrutura mensal
- Custo de APIs de IA mensal (estimativa por volume)
- Custo de templates e ferramentas pagas
- ROI esperado para o cliente

### `/docs/13-metricas-sucesso.md`
- KPIs do sistema após implantação
- Métricas de negócio: leads gerados, taxa de conversão, tempo economizado
- Métricas técnicas: uptime, performance, erros
- Metas para 30, 60 e 90 dias pós-lançamento

---

## 📖 FASE 6 — README.md PRINCIPAL

**Tarefa:** Crie o `README.md` na raiz do projeto. Este documento deve ser **completo, estratégico e inspirador**, comunicando a visão completa do sistema para qualquer pessoa que o leia.

```markdown
# SOMOS PRODUTORA — Sistema Integrado de Gestão Inteligente

## 🎯 Visão do Projeto
[Visão clara e impactante do que é o sistema]

## 💡 O Problema que Resolvemos
[As dores da Somos Produtora antes deste sistema]

## 🚀 A Solução
[Descrição clara e envolvente do sistema]

## 🧠 Inteligência Artificial no Centro
[Como IA transforma cada área da empresa]

## 🏗️ Arquitetura do Sistema
[Visão geral com diagrama Mermaid]

## 📦 Módulos do Sistema
[Descrição de cada um dos 15 módulos com propósito e impacto]

## 🛠️ Tecnologias
[Stack completa organizada por camada]

## 🤖 Ecossistema de Agentes
[Apresentação dos agentes de IA como "funcionários digitais"]

## 📱 App Android
[O que o app oferece para clientes e equipe]

## 📈 Resultados Esperados
[Métricas e impacto após implantação completa]

## 🗓️ Roadmap de 10 Dias
[Timeline visual do desenvolvimento]

## 💰 Modelo de Custos
[Resumo executivo de custos]

## 🏢 Sobre o Desenvolvedor
[Zhuhai Kameda Technology Co., Ltd. — apresentação]

## 📞 Contato
[Informações de contato]
```

---

## 📑 FASE 7 — PDF DE PROPOSTA COMERCIAL

**Tarefa:** Crie um PDF com altíssimo impacto visual e qualidade profissional, representando a proposta formal de desenvolvimento pela **Zhuhai Kameda Technology Co., Ltd.**

### Especificações do PDF:

**Identidade Visual:**
- Paleta de cores: Preto (#0A0A0A), Dourado (#C9A84C), Branco (#FFFFFF), Cinza (#F5F5F5)
- Tipografia moderna e profissional
- Ícones SVG para cada seção
- Diagramas e gráficos visuais

**Estrutura do PDF (mínimo 12 páginas):**

1. **Capa** — Logo Kameda, nome do projeto, data, tipo do documento
2. **Sumário Executivo** — O que é, por que agora, resultados esperados
3. **Sobre a Kameda** — Apresentação da empresa desenvolvedora
4. **Diagnóstico do Problema** — Dores mapeadas da Somos Produtora com dados e impacto
5. **Nossa Solução** — Visão geral do sistema com diagrama visual de alto nível
6. **Arquitetura Tecnológica** — Diagrama de arquitetura visual com tecnologias
7. **Os 15 Módulos do Sistema** — Cards visuais para cada módulo com descrição curta
8. **Ecossistema de Agentes IA** — Apresentação visual dos agentes como "time digital"
9. **App Android** — Mockup conceitual e funcionalidades
10. **Roadmap de Implementação** — Timeline visual de 10 dias com marcos
11. **Investimento** — Tabela de custos com breakdown detalhado
12. **ROI e Resultados Esperados** — Projeções visuais de impacto (gráficos)
13. **Termos e Condições** — Condições de pagamento, SLA, suporte
14. **Contrafação/Assinatura** — Espaço para assinaturas e aceite formal

**Elementos gráficos obrigatórios:**
- Pelo menos 3 gráficos/charts (pizza, barras, linha de tempo)
- Diagrama de arquitetura em SVG
- Cards visuais para cada módulo
- Timeline visual do roadmap de 10 dias
- Tabela de investimento profissional
- Cabeçalho e rodapé em todas as páginas com logo e numeração

---

## 🔀 FASE 8 — COMMIT E PUSH NO GITHUB

**Tarefa:** Após concluir toda a documentação, execute os seguintes passos:

```bash
# 1. Inicialize o repositório se necessário
git init
git remote add origin https://github.com/nhkameda/somos.git

# 2. Configure identidade
git config user.email "kameda@kamedate.com"
git config user.name "Zhuhai Kameda Technology"

# 3. Adicione todos os arquivos
git add .

# 4. Commit estruturado
git commit -m "feat: planejamento completo do sistema Somos Produtora

- Análise completa do escopo e mapeamento de dores
- Arquitetura do sistema com 15 módulos definidos
- Documentação detalhada de cada módulo
- Stack tecnológica completa com justificativas
- Ecossistema de agentes de IA planejado
- Roadmap de implementação em 10 dias
- README.md estratégico e completo
- Proposta comercial em PDF (Kameda Technology)

Co-authored-by: Claude Sonnet <claude@anthropic.com>"

# 5. Push para o repositório
git push -u origin main
```

---

## ✅ CHECKLIST FINAL — ANTES DE ENCERRAR

Verifique se todos os itens foram concluídos:

- [ ] `/assets/ESCOPO.md` lido e compreendido integralmente
- [ ] `/docs/01-analise-escopo.md` — Análise completa do escopo
- [ ] `/docs/02-arquitetura-sistema.md` — Arquitetura completa documentada
- [ ] `/docs/03-arvore-documentacao.md` — Árvore de documentação criada
- [ ] `/docs/04-modulos-detalhados/` — 15 documentos de módulos criados
- [ ] `/docs/05-stack-tecnologica.md` — Stack completa justificada
- [ ] `/docs/06-infraestrutura.md` — Infraestrutura Linux documentada
- [ ] `/docs/07-agentes-ia.md` — Catálogo de agentes documentado
- [ ] `/docs/08-integracoes.md` — Todas as integrações mapeadas
- [ ] `/docs/09-banco-dados.md` — Modelo de banco de dados completo
- [ ] `/docs/10-seguranca.md` — Estratégia de segurança documentada
- [ ] `/docs/11-roadmap-implementacao.md` — Roadmap de 10 dias detalhado
- [ ] `/docs/12-estimativa-custos.md` — Custos estimados e ROI
- [ ] `/docs/13-metricas-sucesso.md` — KPIs e métricas definidas
- [ ] `README.md` — Documento principal completo e inspirador
- [ ] `PROPOSAL.pdf` — PDF de proposta profissional com alto impacto visual
- [ ] Commit realizado com mensagem descritiva e estruturada
- [ ] Push realizado no repositório `nhkameda/somos`

---

## ⚠️ REGRAS E RESTRIÇÕES DESTA SESSÃO

1. **NENHUM código de software deve ser escrito** — apenas documentação, planejamento e arquitetura
2. **Não pule fases** — execute em ordem sequencial: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8
3. **Seja extremamente detalhado** — cada documento deve ser completo o suficiente para que um time de desenvolvimento inicie o trabalho sem fazer perguntas
4. **Priorize qualidade visual no PDF** — este é o documento que será apresentado ao cliente
5. **Mantenha consistência** — as tecnologias, nomes e conceitos devem ser consistentes em todos os documentos
6. **Use português do Brasil** em toda a documentação voltada ao cliente; use inglês para documentação técnica e código de configuração
7. **Ao criar o PDF**, utilize as melhores práticas de diagramação: espaço em branco, hierarquia visual clara, paleta de cores coesa, tipografia consistente
8. **Considere escalabilidade** — o sistema deve ser planejado para crescer de 5 para 500 usuários sem refatoração estrutural
9. **Inclua custos reais** — pesquise preços atuais de APIs (OpenAI, Anthropic, AWS/VPS) para estimativas realistas
10. **Pense no App Android** com funcionalidades que agreguem valor real: notificações push de status do projeto, aprovação de entregas, visualização de dashboard, chat integrado com a equipe

---

## 💬 MENSAGEM FINAL AO CLAUDE CODE

Este é um projeto que vai transformar completamente a operação da Somos Produtora, uma empresa com décadas de história no mercado audiovisual brasileiro. A dor é real, o impacto é imenso, e o sistema a ser planejado precisa estar à altura dessa responsabilidade.

Pense como um CTO sênior com experiência em startups SaaS e empresas de mídia. Pense em escalabilidade, em experiência do usuário, em automação inteligente. Pense em como os agentes de IA vão agir como um time dedicado de funcionários digitais que nunca dormem e nunca param de buscar oportunidades para a Somos.

Ao final deste planejamento, a equipe de desenvolvimento da Kameda Technology terá em mãos um blueprint completo e detalhado o suficiente para iniciar o desenvolvimento imediatamente, sabendo exatamente o que construir, por que, como e em que ordem.

**Execute com excelência. O sucesso deste projeto começa aqui.**

---

*Prompt criado por: Zhuhai Kameda Technology Co., Ltd.*
*Projeto: Somos Produtora — Sistema Integrado de Gestão Inteligente*
*Versão: 1.0 | Data: Fevereiro 2026*
