# 11 — Roadmap de Implementação

> **Somos Produtora** — Sistema Integrado de Gestão Comercial e Operacional com IA
> Plano de execução em 10 dias com sprints diários detalhados

---

## Visão Geral do Cronograma

| Fase | Dias | Foco | Prioridade |
|------|------|------|------------|
| Setup & Infraestrutura | Dia 0 | Fundação técnica | Crítica |
| Core do Sistema | Dias 1-3 | Módulos essenciais de negócio | Crítica |
| AI & Comercial | Dias 4-6 | Agentes IA e integração comercial | Alta |
| Operações | Dias 7-8 | Módulos operacionais e financeiros | Alta |
| Conteúdo & Mobile | Dias 9-10 | Criação de conteúdo, app e finalização | Média-Alta |

**Metodologia:** Cada dia funciona como um sprint independente com entregáveis definidos. O desenvolvimento segue a abordagem "vertical slice" — cada módulo é entregue funcional de ponta a ponta (backend + frontend + testes) antes de avançar para o próximo.

**Equipe:** 1 Arquiteto/Lead Developer + Claude Code AI Assistant (Zhuhai Kameda Technology)

**Horário de trabalho:** Sprints intensivos de 10-14 horas/dia

---

## Dia 0 — Setup & Infraestrutura

**Objetivo:** Estabelecer toda a fundação técnica para que o desenvolvimento dos módulos possa começar sem impedimentos no Dia 1.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 0.1 | Repositório e estrutura | Monorepo configurado com estrutura de pastas para `backend/`, `frontend/`, `mobile/`, `agents/`, linters (Ruff, ESLint, Prettier), pre-commit hooks | 1h |
| 0.2 | Docker Compose | Stack completa: PostgreSQL 16, Redis 7, Qdrant, MinIO, Nginx/Traefik, FastAPI, Next.js — tudo orquestrado e com health checks | 2h |
| 0.3 | CI/CD Pipeline | GitHub Actions: lint, test, build, deploy automático para staging. Dockerfile otimizado com multi-stage build | 1.5h |
| 0.4 | Banco de Dados | PostgreSQL inicializado com schema base, Alembic configurado para migrations, Redis para cache/sessões, Qdrant para embeddings, MinIO para uploads | 2h |
| 0.5 | Autenticação | Sistema JWT completo (access + refresh tokens), RBAC com roles (admin, gerente, comercial, operacional, visualizador), middleware de autorização por módulo | 3h |
| 0.6 | API Base | FastAPI scaffold: estrutura modular, CORS, rate limiting, error handling padronizado, logging estruturado, Swagger/ReDoc configurados | 2h |
| 0.7 | Frontend Scaffold | Next.js 15 com App Router, ShadcnUI instalado e configurado, TailwindCSS, layout base (sidebar, header, auth pages), dark mode, tema Somos Produtora | 2.5h |

### Dependências

```
0.1 Repositório ──→ 0.2 Docker ──→ 0.4 Banco de Dados
                          │
                          ├──→ 0.3 CI/CD
                          │
                          └──→ 0.6 API Base ──→ 0.5 Autenticação
                                                        │
0.7 Frontend Scaffold ──────────────────────────────────┘
```

### Milestone do Dia 0

- [ ] `docker-compose up` sobe toda a stack sem erros
- [ ] API responde em `/docs` com Swagger funcional
- [ ] Login/logout funcional com JWT
- [ ] Frontend renderiza com layout base e navegação
- [ ] Pipeline CI/CD executa com sucesso no push
- [ ] Migrations rodam sem erros

### Critérios de "Done"

> O Dia 0 está completo quando um desenvolvedor pode clonar o repo, rodar `docker-compose up` e ter o sistema base funcional com autenticação em menos de 5 minutos.

---

## Dia 1 — CRM Pipeline

**Objetivo:** Entregar o módulo CRM com gestão completa de deals, contatos, empresas e visualização Kanban.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 1.1 | Models & Migrations | Tabelas: `contacts`, `companies`, `deals`, `deal_stages`, `deal_activities`, `tags` com relacionamentos completos | 2h |
| 1.2 | API — Contatos | CRUD completo, busca/filtro, importação em massa (CSV), merge de duplicatas, timeline de interações | 2h |
| 1.3 | API — Empresas | CRUD, vinculação com contatos, dados fiscais (CNPJ), segmentação por setor | 1.5h |
| 1.4 | API — Deals | CRUD, movimentação entre stages, valor/probabilidade, previsão de fechamento, histórico de mudanças | 2h |
| 1.5 | Frontend — Kanban Board | Board drag-and-drop com colunas configuráveis, cards com resumo do deal, filtros por valor/responsável/data | 3h |
| 1.6 | Frontend — Páginas | Listagem de contatos/empresas com DataTable, formulários de criação/edição, página de detalhe com timeline | 2.5h |
| 1.7 | Testes | Testes unitários dos services, testes de integração dos endpoints, testes E2E do fluxo Kanban | 1h |

### Dependências

- **Depende de:** Dia 0 (infraestrutura completa)
- **Bloqueia:** Dia 6 (integração CRM + IA)

### Milestone do Dia 1

- [ ] CRUD funcional para contatos, empresas e deals via API e frontend
- [ ] Board Kanban renderiza com drag-and-drop funcional
- [ ] Filtros e busca operacionais
- [ ] Timeline de atividades registrando interações

---

## Dia 2 — Gestão de Usuários & Dashboard

**Objetivo:** Sistema completo de gestão de usuários com roles granulares e dashboard principal com widgets configuráveis.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 2.1 | Gestão de Usuários | CRUD de usuários, atribuição de roles, permissões por módulo, convite por e-mail, avatar, preferências | 2.5h |
| 2.2 | Sistema de Permissões | Middleware granular: permissões por módulo (ler, criar, editar, deletar), hierarquia de roles, audit log de acessos | 2h |
| 2.3 | Dashboard Principal | Layout de widgets: resumo de pipeline (valor total por stage), leads do dia, tarefas pendentes, atividades recentes | 3h |
| 2.4 | Widgets Configuráveis | Gráficos: funil de vendas, receita por período, leads por fonte, conversão por estágio. Usando Recharts/Chart.js | 2.5h |
| 2.5 | Notificações | Sistema de notificações in-app (bell icon), marcação lida/não-lida, configuração de preferências de notificação por usuário | 2h |
| 2.6 | Perfil do Usuário | Página de perfil com edição de dados pessoais, troca de senha, configuração de tema e preferências de notificação | 1h |
| 2.7 | Testes | Testes de autorização (role-based), testes de dashboard, testes de notificação | 1h |

### Dependências

- **Depende de:** Dia 0 (autenticação) + Dia 1 (dados de CRM para popular dashboard)
- **Bloqueia:** Todos os módulos subsequentes (sistema de permissões)

### Milestone do Dia 2

- [ ] Usuários podem ser criados e gerenciados com roles diferentes
- [ ] Permissões bloqueiam corretamente acesso a módulos não autorizados
- [ ] Dashboard exibe dados reais do CRM com gráficos interativos
- [ ] Notificações aparecem em tempo real

---

## Dia 3 — Contratos & Financeiro Base

**Objetivo:** Módulo de gestão de contratos com geração automática de PDF e base do módulo financeiro.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 3.1 | Models — Contratos | Tabelas: `contracts`, `contract_templates`, `contract_versions`, `contract_signatures`, vinculação com deals e empresas | 1.5h |
| 3.2 | Templates de Contrato | Sistema de templates com variáveis dinâmicas (`{{empresa}}`, `{{valor}}`, `{{escopo}}`), editor visual de templates | 2h |
| 3.3 | Geração de PDF | Motor de geração PDF (WeasyPrint/ReportLab): inserção de dados dinâmicos, logo, formatação profissional, marca d'água para rascunho | 2h |
| 3.4 | Ciclo de Vida | Fluxo: rascunho → revisão → aprovação → enviado → assinado → vigente → encerrado. Notificações automáticas em cada transição | 1.5h |
| 3.5 | Financeiro Base | Models: `invoices`, `payments`, `accounts`, `cost_centers`. CRUD de contas a pagar e receber, categorização | 2.5h |
| 3.6 | Frontend — Contratos | Listagem com status visual, formulário com preview em tempo real, visualizador de PDF integrado, assinatura digital básica | 2.5h |
| 3.7 | Frontend — Financeiro | Dashboard financeiro: fluxo de caixa, gráfico de receitas vs. despesas, tabela de contas com filtros | 2h |
| 3.8 | Testes | Testes de geração PDF, testes de fluxo de contrato, testes de cálculos financeiros | 1h |

### Dependências

- **Depende de:** Dia 1 (deals/empresas para vincular contratos)
- **Bloqueia:** Dia 7 (financeiro avançado)

### Milestone do Dia 3

- [ ] Contratos podem ser criados a partir de templates com dados dinâmicos
- [ ] PDF gerado com formatação profissional
- [ ] Fluxo de aprovação funcional com notificações
- [ ] Dashboard financeiro mostra dados de contas a pagar/receber

---

## Dia 4 — Hunter Intelligence

**Objetivo:** Desenvolver os agentes de prospecção ativa que vasculham múltiplas fontes para identificar leads potenciais.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 4.1 | Infraestrutura de Agentes | Setup CrewAI/LangChain, configuração de modelos (Claude/GPT-4o), sistema de tools, logging de execução, rate limiting | 2h |
| 4.2 | Scraper — LinkedIn | Agente que busca perfis e empresas no LinkedIn por critérios (setor, cargo, localização, tamanho da empresa), extrai dados públicos | 2.5h |
| 4.3 | Scraper — Google | Agente que realiza buscas avançadas no Google (site:, intitle:, inurl:) via SerpAPI para encontrar empresas e contatos-alvo | 2h |
| 4.4 | Scraper — Redes Sociais | Agente que monitora Instagram, Facebook e Twitter/X para identificar empresas que podem precisar de serviços de produção audiovisual | 2h |
| 4.5 | Enriquecimento de Dados | Pipeline que cruza dados de múltiplas fontes, normaliza informações, remove duplicatas e enriquece com dados adicionais (CNPJ, porte, faturamento estimado) | 2h |
| 4.6 | Scoring de Leads | Algoritmo de classificação (0-100) baseado em: fit com ICP, sinais de intenção de compra, tamanho da empresa, engajamento social, orçamento estimado | 1.5h |
| 4.7 | Frontend — Hunter | Dashboard de prospecção: leads descobertos, score visual, fonte de origem, ações rápidas (adicionar ao CRM, iniciar outreach) | 2h |
| 4.8 | Testes | Testes dos agentes com mocks, testes de scoring, testes de integração com CRM | 1h |

### Dependências

- **Depende de:** Dia 0 (infraestrutura) + Dia 1 (CRM para receber leads)
- **Bloqueia:** Dia 5 (SDR precisa de leads qualificados)

### Milestone do Dia 4

- [ ] Agentes executam buscas em LinkedIn, Google e redes sociais
- [ ] Leads são enriquecidos e pontuados automaticamente
- [ ] Dashboard mostra leads descobertos com score e ações
- [ ] Leads podem ser importados para o CRM com um clique

---

## Dia 5 — SDR Automation

**Objetivo:** Construir o sistema de abordagem comercial automatizada multicanal.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 5.1 | Integration — WhatsApp | Conexão com Evolution API: envio/recebimento de mensagens, templates aprovados, gestão de sessão, webhook de respostas | 2.5h |
| 5.2 | Integration — LinkedIn | Automação de conexões, envio de mensagens InMail, sequências de follow-up, detecção de resposta | 2h |
| 5.3 | Integration — Email | Setup Resend: templates HTML responsivos, envio programado, tracking de abertura/clique, gestão de bounces | 1.5h |
| 5.4 | Cadências de Outreach | Motor de cadências multicanal: dia 1 (LinkedIn connect) → dia 2 (email intro) → dia 4 (WhatsApp follow-up) → dia 7 (email valor). Configurável por campanha | 2.5h |
| 5.5 | Agente SDR | Agente IA que personaliza mensagens com base no perfil do lead, empresa, setor e sinais de compra. Adapta tom e conteúdo por canal | 2h |
| 5.6 | Qualificação Automática | Classificação de respostas (interessado, não agora, sem interesse, reunião agendada) com IA. Encaminhamento automático para etapa adequada do pipeline | 1.5h |
| 5.7 | Frontend — SDR | Dashboard de campanhas: leads em outreach, taxa de resposta, conversões, timeline de interações por lead, configurador de cadências | 2h |
| 5.8 | Testes | Testes de envio (com mocks), testes de cadência, testes de classificação de resposta | 1h |

### Dependências

- **Depende de:** Dia 4 (leads qualificados para abordar)
- **Bloqueia:** Dia 6 (integração completa)

### Milestone do Dia 5

- [ ] Mensagens enviadas via WhatsApp, LinkedIn e e-mail
- [ ] Cadências executam automaticamente com delays configurados
- [ ] Respostas classificadas automaticamente por IA
- [ ] Dashboard mostra métricas de cada campanha

---

## Dia 6 — Integração CRM + IA

**Objetivo:** Conectar os agentes Hunter e SDR ao CRM Pipeline, criando um fluxo completo de prospecção→qualificação→oportunidade.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 6.1 | Pipeline Automatizado | Fluxo: Hunter descobre lead → score > threshold → cria contato no CRM → SDR inicia cadência → resposta positiva → cria deal no pipeline | 3h |
| 6.2 | Qualification Scoring v2 | Scoring avançado combinando dados do Hunter (fit) + sinais do SDR (engajamento, respostas) para score dinâmico que atualiza conforme interações | 2h |
| 6.3 | Automações de Pipeline | Triggers: deal muda de stage → notifica responsável; deal parado > 3 dias → alerta; deal qualificado → sugere próxima ação com IA | 2h |
| 6.4 | Assistente Comercial | Agente IA que sugere: melhor horário para contato, canal preferido do lead, argumentos de venda personalizados, objeções prováveis e respostas sugeridas | 2h |
| 6.5 | Relatórios Comerciais | Funil de conversão (lead → qualificado → reunião → proposta → fechamento), velocidade do pipeline, forecast de receita, performance por canal/campanha | 2h |
| 6.6 | Frontend — Integrações | Painel de status dos agentes (executando, parado, erro), logs de atividade, configuração de thresholds e regras de automação | 2h |
| 6.7 | Testes E2E | Teste completo do fluxo: discovery → outreach → qualificação → deal criado → movimentação no pipeline | 1.5h |

### Dependências

- **Depende de:** Dias 1, 4 e 5 (CRM + Hunter + SDR)
- **Bloqueia:** Nenhum (módulo de integração)

### Milestone do Dia 6

- [ ] Fluxo completo Hunter → SDR → CRM funcionando end-to-end
- [ ] Scoring dinâmico atualizando conforme interações
- [ ] Automações de pipeline disparando corretamente
- [ ] Relatórios comerciais com dados reais

---

## Dia 7 — Financeiro ERP & Inventário

**Objetivo:** Expandir o módulo financeiro para ERP completo e implementar controle de inventário de equipamentos.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 7.1 | Notas Fiscais | Integração com API de NF-e (NFe.io ou similar): emissão, cancelamento, consulta, PDF da nota. Campos fiscais completos (CNAE, NCM, alíquotas) | 3h |
| 7.2 | Pagamentos | Registro de pagamentos parciais/totais, vínculo com invoices, comprovantes (upload), conciliação manual, alertas de atraso | 2h |
| 7.3 | Fluxo de Caixa | Visão consolidada: entradas vs. saídas por período, projeção futura baseada em contas a receber, categorização por centro de custo | 1.5h |
| 7.4 | DRE Simplificado | Demonstrativo de Resultados: receita bruta, deduções, receita líquida, custos, despesas operacionais, lucro/prejuízo por período | 1.5h |
| 7.5 | Inventário — Models | Tabelas: `equipment`, `equipment_categories`, `equipment_bookings`, `maintenance_records`. Campos: nome, modelo, número de série, status, localização, valor | 1.5h |
| 7.6 | Inventário — Funcionalidades | CRUD de equipamentos, reserva/agendamento para projetos, histórico de uso, alertas de manutenção, QR code para identificação rápida | 2h |
| 7.7 | Frontend — Financeiro | Dashboard ERP: DRE visual, fluxo de caixa interativo, listagem de NFs com status, página de pagamentos | 2h |
| 7.8 | Frontend — Inventário | Catálogo visual de equipamentos com fotos, calendário de disponibilidade, formulário de reserva, scanner QR | 1.5h |

### Dependências

- **Depende de:** Dia 3 (financeiro base)
- **Bloqueia:** Dia 8 (orçamento precisa de dados de inventário e custos)

### Milestone do Dia 7

- [ ] NF-e pode ser emitida e consultada via sistema
- [ ] Fluxo de caixa e DRE exibem dados consolidados
- [ ] Equipamentos cadastrados com status e disponibilidade
- [ ] Reservas de equipamento funcionais com calendário

---

## Dia 8 — Fornecedores, Orçamento & Projetos

**Objetivo:** Implementar base de fornecedores, gerador inteligente de orçamentos e sistema de gestão de projetos de produção.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 8.1 | Fornecedores — CRUD | Cadastro completo: dados fiscais, categorias de serviço, portfólio, região de atuação, avaliação (1-5 estrelas), histórico de contratações | 2h |
| 8.2 | Fornecedores — Comparativo | Tabela comparativa de fornecedores por categoria, ranking por preço/qualidade/pontualidade, sugestão automática por tipo de projeto | 1h |
| 8.3 | Orçamento Inteligente | Agente IA que recebe briefing do projeto e gera orçamento detalhado: equipe necessária, equipamentos, locações, pós-produção, com valores baseados em histórico e fornecedores cadastrados | 3h |
| 8.4 | Templates de Orçamento | Modelos pré-configurados por tipo de produção (vídeo institucional, evento, social media, documentário), exportação para PDF profissional | 1.5h |
| 8.5 | Gestão de Projetos — Models | Tabelas: `projects`, `project_tasks`, `project_milestones`, `project_team`, `project_deliverables`. Vinculação com deals, contratos e orçamentos | 2h |
| 8.6 | Gestão de Projetos — Board | Board de tarefas estilo Kanban (a fazer, em andamento, revisão, concluído), atribuição de responsáveis, prazos, checklists, comentários | 2h |
| 8.7 | Frontend — Orçamento | Wizard de criação: briefing → IA sugere → usuário ajusta → preview PDF → envio ao cliente | 1.5h |
| 8.8 | Testes | Testes do gerador de orçamento, testes de projeto, testes de fornecedores | 1h |

### Dependências

- **Depende de:** Dia 7 (inventário e dados financeiros para orçamento)
- **Bloqueia:** Dia 10 (analytics precisa de dados de projetos)

### Milestone do Dia 8

- [ ] Fornecedores cadastrados com comparativo funcional
- [ ] Orçamento gerado por IA a partir de briefing
- [ ] PDF de orçamento profissional exportado
- [ ] Board de projetos funcional com tarefas e timeline

---

## Dia 9 — Content Studio & Social Scheduler

**Objetivo:** Plataforma de criação de conteúdo com IA e sistema de agendamento para redes sociais.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 9.1 | Content Studio — Texto | Agente IA para geração de: posts para redes sociais, roteiros de vídeo, legendas, hashtags, copies para ads. Múltiplos tons e formatos | 2.5h |
| 9.2 | Content Studio — Imagem | Integração DALL-E/Stable Diffusion: geração de imagens a partir de prompts, variações, edição (inpainting), templates visuais | 2h |
| 9.3 | Editor de Conteúdo | Interface rica: editor de texto com formatação, preview por rede social (Instagram quadrado, Stories vertical, LinkedIn horizontal), banco de assets | 2h |
| 9.4 | Social Scheduler — Setup | Integração com APIs: Instagram (Graph API), Facebook (Pages API), LinkedIn (Marketing API), agendamento com fila (Celery/Redis) | 2.5h |
| 9.5 | Calendário Editorial | Visão mensal/semanal do calendário, drag-and-drop para reagendar, cores por rede social, status (rascunho, aprovado, publicado) | 2h |
| 9.6 | Publicação Automática | Motor de publicação: horário programado → posta automaticamente → registra resultado → notifica usuário. Retry automático em caso de falha | 1.5h |
| 9.7 | Frontend — Content | Workspace criativo: gerador IA na lateral, preview central, banco de assets abaixo, calendário à direita | 2h |
| 9.8 | Testes | Testes de geração de conteúdo, testes de agendamento, testes de publicação (com mocks das APIs) | 1h |

### Dependências

- **Depende de:** Dia 0 (infraestrutura de agentes IA)
- **Bloqueia:** Nenhum

### Milestone do Dia 9

- [ ] Textos e imagens gerados por IA com qualidade profissional
- [ ] Editor com preview por rede social funcional
- [ ] Calendário editorial com agendamento drag-and-drop
- [ ] Publicação automática funcionando (pelo menos Instagram e LinkedIn)

---

## Dia 10 — App Android, Analytics & Integração Final

**Objetivo:** Entregar o app mobile, dashboards de analytics e realizar integração e testes finais de todo o sistema.

### Entregáveis

| # | Entregável | Detalhes | Tempo Est. |
|---|-----------|----------|------------|
| 10.1 | App Android — Setup | React Native (Expo) configurado, autenticação JWT, navegação principal (tab bar), tema Somos Produtora | 2h |
| 10.2 | App — Funcionalidades | Telas: dashboard resumido, pipeline (visualização), notificações push (Firebase), aprovações rápidas, câmera para documentação de produção | 3h |
| 10.3 | Analytics — Dashboard | Dashboard central: métricas de vendas (funil, receita, conversão), operações (projetos ativos, entregas), marketing (alcance, engajamento) | 2.5h |
| 10.4 | Analytics — Relatórios | Geração de relatórios em PDF: performance mensal, ROI de campanhas, produtividade por módulo. Agendamento de envio automático | 2h |
| 10.5 | Integração Final | Teste end-to-end de todos os fluxos: lead discovery → outreach → CRM → contrato → projeto → entrega → faturamento → analytics | 2h |
| 10.6 | Performance & Otimização | Cache strategies, lazy loading, query optimization, bundle size reduction, lighthouse audit, load testing | 1.5h |
| 10.7 | Documentação Final | Swagger completo, README atualizado, variáveis de ambiente documentadas, guia de deploy, guia do usuário básico | 1.5h |
| 10.8 | Smoke Tests | Testes de fumaça em todos os módulos, verificação de fluxos críticos, checklist de entrega | 1h |

### Dependências

- **Depende de:** Todos os dias anteriores
- **Bloqueia:** Nenhum (entrega final)

### Milestone do Dia 10

- [ ] App Android funcional com features essenciais
- [ ] Dashboard de analytics consolidando dados de todos os módulos
- [ ] Relatórios PDF gerados automaticamente
- [ ] Todos os fluxos end-to-end testados e validados
- [ ] Sistema pronto para produção

---

## Resumo de Dependências entre Dias

```
Dia 0 (Infra) ─────┬──→ Dia 1 (CRM) ──→ Dia 2 (Users/Dashboard) ──→ Dia 3 (Contratos/Fin)
                    │         │                                              │
                    │         ├──→ Dia 4 (Hunter) ──→ Dia 5 (SDR) ──→ Dia 6 (CRM+IA)
                    │         │
                    │         └──────────────────────────────── Dia 7 (ERP/Inventário)
                    │                                                │
                    │                                          Dia 8 (Fornec/Orç/Proj)
                    │                                                │
                    ├──→ Dia 9 (Content/Social) ────────────────────┤
                    │                                                │
                    └──→ Dia 10 (Mobile/Analytics/Integração) ◄─────┘
```

---

## Checklist de Entrega Final

### Funcionalidades Core
- [ ] Autenticação e autorização (JWT + RBAC)
- [ ] CRM Pipeline com Kanban
- [ ] Gestão de contatos e empresas
- [ ] Gestão de contratos com PDF
- [ ] Módulo financeiro / ERP
- [ ] Inventário de equipamentos
- [ ] Base de fornecedores
- [ ] Orçamento inteligente com IA
- [ ] Gestão de projetos

### Agentes IA
- [ ] Hunter Intelligence (3 fontes)
- [ ] SDR Automation (3 canais)
- [ ] Integração CRM + IA
- [ ] Content Studio (texto + imagem)
- [ ] Assistente Comercial

### Plataformas
- [ ] Frontend Web (Next.js 15)
- [ ] App Android (React Native)
- [ ] API REST (FastAPI)
- [ ] Social Scheduler

### Infraestrutura
- [ ] Docker Compose funcional
- [ ] CI/CD Pipeline
- [ ] Monitoramento básico
- [ ] Backup automatizado
- [ ] SSL/HTTPS configurado

---

## Riscos e Mitigações

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| APIs de terceiros instáveis (LinkedIn, redes sociais) | Alta | Médio | Implementar fallbacks, retry com backoff, modo degradado |
| Rate limits de APIs de IA | Média | Alto | Cache agressivo, batching de requests, fallback entre providers (Claude ↔ GPT-4o ↔ Gemini) |
| Complexidade do módulo fiscal (NF-e) | Alta | Médio | Usar serviço intermediário (NFe.io), implementar versão simplificada primeiro |
| Tempo insuficiente para app mobile | Média | Baixo | Priorizar PWA como fallback, app com features mínimas |
| Performance com muitos agentes simultâneos | Média | Médio | Queue management com Celery, concurrency limits, auto-scaling |

---

## Notas Importantes

1. **Priorização:** Se houver atraso, os dias 9-10 podem ser comprimidos priorizando funcionalidades essenciais
2. **Testes:** Cada dia inclui tempo para testes. Não pular esta etapa
3. **Code Review:** Claude Code AI faz review contínuo, mas revisão humana no final de cada dia é obrigatória
4. **Documentação:** Swagger/OpenAPI é gerado automaticamente. Documentação de usuário é mínima nesta fase
5. **Deploy:** O sistema roda em Docker, facilitando deploy em qualquer VPS com `docker-compose up`

---

> **Última atualização:** Fevereiro 2026
> **Responsável:** Zhuhai Kameda Technology
> **Metodologia:** Sprint diário com entregáveis definidos
> **Status:** Planejamento concluído — pronto para execução
