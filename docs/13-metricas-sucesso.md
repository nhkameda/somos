# 13 — Métricas de Sucesso e KPIs

> **Somos Produtora** — Sistema Integrado de Gestão Comercial e Operacional com IA
> Framework completo de métricas para avaliação objetiva do sistema

---

## Visão Geral

Este documento define os **Key Performance Indicators (KPIs)** que serão utilizados para medir o sucesso do sistema Somos Produtora. As métricas estão organizadas em três categorias — negócio, produtividade e técnicas — com metas progressivas para 30, 60 e 90 dias.

**Princípio fundamental:** Se não pode ser medido, não pode ser melhorado. Cada módulo do sistema deve contribuir para pelo menos um KPI mensurável.

---

## 1. Métricas de Negócio

### 1.1 Funil de Vendas

| Métrica | Definição | Fórmula | Meta | Frequência |
|---------|-----------|---------|------|-----------|
| **Leads gerados/mês** | Total de leads descobertos pelo Hunter Intelligence | Contagem de novos leads no período | 500+ no primeiro mês | Semanal |
| **Taxa de qualificação** | Percentual de leads que atingem score mínimo de qualificação | (Leads qualificados / Leads totais) x 100 | 15-25% | Semanal |
| **Taxa de conversão qualificado → reunião** | Leads qualificados que resultam em reunião agendada | (Reuniões agendadas / Leads qualificados) x 100 | 30% | Semanal |
| **Taxa de conversão reunião → proposta** | Reuniões que resultam em envio de proposta/orçamento | (Propostas enviadas / Reuniões realizadas) x 100 | 60% | Quinzenal |
| **Taxa de fechamento** | Propostas que resultam em contrato assinado | (Contratos fechados / Propostas enviadas) x 100 | 20% | Mensal |
| **Velocidade do pipeline** | Tempo médio da primeira interação até o fechamento | Média de dias (data fechamento - data primeiro contato) | < 30 dias | Mensal |

### Funil de Conversão Esperado (Meta Mensal em Regime)

```
Leads Gerados:        500     (100%)
    ↓ Taxa qualificação: 20%
Leads Qualificados:   100     (20%)
    ↓ Taxa reunião: 30%
Reuniões Agendadas:    30     (6%)
    ↓ Taxa proposta: 60%
Propostas Enviadas:    18     (3.6%)
    ↓ Taxa fechamento: 20%
Contratos Fechados:   3-4     (0.7%)
```

### 1.2 Receita e Financeiro

| Métrica | Definição | Fórmula | Meta | Frequência |
|---------|-----------|---------|------|-----------|
| **Receita gerada por leads do sistema** | Valor total de contratos originados via sistema | Soma do valor de contratos cuja origem é lead do sistema | R$ 30.000+/mês | Mensal |
| **Ticket médio** | Valor médio por contrato fechado | Receita total / Número de contratos | R$ 8.000-12.000 | Mensal |
| **MRR (Monthly Recurring Revenue)** | Receita recorrente mensal de clientes gerenciados | Soma de contratos mensais ativos | Crescente mês a mês | Mensal |
| **Custo por Lead (CPL)** | Custo total do sistema dividido por leads gerados | (Custo IA + Infra) / Leads gerados | < R$ 8,00 | Mensal |
| **Custo de Aquisição de Cliente (CAC)** | Custo total para adquirir um novo cliente | Custo total operacional / Novos clientes fechados | < R$ 1.500 | Mensal |
| **LTV/CAC** | Relação entre valor vitalício do cliente e custo de aquisição | LTV estimado / CAC | > 5x | Trimestral |
| **ROI do sistema** | Retorno sobre o investimento no sistema | (Ganho - Custo) / Custo x 100 | > 200% em 12 meses | Trimestral |

### 1.3 Métricas Comerciais Detalhadas

| Métrica | Definição | Meta |
|---------|-----------|------|
| **Leads por fonte** | Distribuição de leads por canal de origem (LinkedIn, Google, Social) | Diversificação: nenhuma fonte > 50% |
| **Score médio de qualificação** | Média do score de IA para leads gerados | > 65/100 |
| **Taxa de resposta (outreach)** | Percentual de leads que respondem ao primeiro contato | > 15% |
| **Taxa de resposta positiva** | Respostas classificadas como "interessado" ou "agendar reunião" | > 5% do total |
| **Tempo médio de primeira resposta** | Tempo entre envio da mensagem e resposta do lead | Registro para análise |
| **Canal mais efetivo** | Canal com maior taxa de conversão (WhatsApp vs Email vs LinkedIn) | Identificar em 30 dias |
| **Churn de pipeline** | Deals perdidos ou abandonados por mês | < 40% |

---

## 2. Métricas de Produtividade

### 2.1 Eficiência Operacional

| Métrica | Definição | Antes do Sistema | Meta com Sistema | Redução |
|---------|-----------|-----------------|-----------------|---------|
| **Tempo de geração de orçamento** | Do briefing ao PDF final | 2-4 horas | < 15 minutos | 93% |
| **Tempo de geração de contrato** | Da aprovação ao PDF pronto | 1-2 horas | < 5 minutos | 95% |
| **Tempo de qualificação de lead** | Da descoberta à classificação | 30-60 minutos | < 2 minutos | 97% |
| **Tempo de criação de post** | Da ideia ao conteúdo final | 1-2 horas | < 10 minutos | 90% |
| **Tempo de envio de proposta** | Da reunião ao envio da proposta | 2-5 dias | < 1 dia | 75% |
| **Redução de trabalho manual geral** | Horas de trabalho manual por semana | 40h/semana | 12h/semana | **70%** |

### 2.2 Produtividade por Módulo

| Módulo | Métrica | Meta | Frequência |
|--------|---------|------|-----------|
| **Hunter Intelligence** | Leads descobertos por execução do agente | > 50 leads/execução | Diária |
| **SDR Automation** | Mensagens enviadas por dia | > 100 mensagens/dia | Diária |
| **SDR Automation** | Cadências ativas simultaneamente | > 5 campanhas | Semanal |
| **CRM Pipeline** | Deals movimentados por semana | > 20 movimentações | Semanal |
| **Gestão de Contratos** | Contratos gerados por mês | > 10 contratos | Mensal |
| **Financeiro ERP** | Notas fiscais emitidas por mês | > 10 NFs | Mensal |
| **Inventário** | Equipamentos rastreados | 100% do acervo | Contínuo |
| **Orçamento Inteligente** | Orçamentos gerados por mês | > 15 orçamentos | Mensal |
| **Content Studio** | Posts gerados por semana por cliente | > 10 posts/semana/cliente | Semanal |
| **Social Scheduler** | Posts publicados por semana | > 30 posts/semana (total) | Semanal |
| **Gestão de Projetos** | Projetos gerenciados simultaneamente | > 5 projetos | Contínuo |
| **Analytics** | Relatórios gerados automaticamente | > 4 relatórios/mês | Mensal |

### 2.3 Qualidade do Conteúdo IA

| Métrica | Definição | Meta |
|---------|-----------|------|
| **Taxa de aprovação sem edição** | Posts gerados por IA aprovados sem modificação | > 60% |
| **Taxa de aprovação com edição mínima** | Posts aprovados com menos de 20% de alteração | > 85% |
| **Taxa de rejeição** | Posts descartados completamente | < 5% |
| **Engajamento médio de posts IA** | Likes + comentários + compartilhamentos por post | Igual ou superior a posts manuais |
| **Precisão de orçamentos IA** | Diferença entre orçamento gerado e custo real | Margem < 15% |

---

## 3. Métricas Técnicas

### 3.1 Disponibilidade e Performance

| Métrica | Definição | Meta | Alerta |
|---------|-----------|------|--------|
| **Uptime do sistema** | Percentual de tempo que o sistema está disponível | 99.5% | < 99% |
| **Tempo de resposta da API (p50)** | Latência mediana das requisições | < 100ms | > 200ms |
| **Tempo de resposta da API (p95)** | Latência do percentil 95 | < 200ms | > 500ms |
| **Tempo de resposta da API (p99)** | Latência do percentil 99 | < 500ms | > 1.000ms |
| **Taxa de erro (5xx)** | Percentual de requisições com erro de servidor | < 0.1% | > 0.5% |
| **Taxa de erro (4xx)** | Percentual de requisições com erro de cliente | < 2% | > 5% |
| **Tempo de carregamento do frontend** | First Contentful Paint (FCP) | < 1.5s | > 3s |
| **Largest Contentful Paint (LCP)** | Tempo até o maior elemento visível carregar | < 2.5s | > 4s |

### 3.2 Infraestrutura

| Métrica | Definição | Meta | Alerta |
|---------|-----------|------|--------|
| **Uso de CPU** | Utilização média do processador | < 60% | > 80% |
| **Uso de RAM** | Utilização média de memória | < 70% | > 85% |
| **Uso de disco** | Espaço utilizado no armazenamento | < 60% | > 80% |
| **Conexões ao banco de dados** | Pool de conexões ativas do PostgreSQL | < 70% do pool | > 85% do pool |
| **Tamanho do banco de dados** | Crescimento mensal do PostgreSQL | Monitorar tendência | Crescimento > 20%/mês |
| **Fila de tarefas (Celery)** | Tarefas aguardando execução | < 50 tarefas | > 200 tarefas |
| **Tempo de execução de backup** | Duração do backup diário | < 15 minutos | > 30 minutos |

### 3.3 Performance dos Agentes IA

| Métrica | Definição | Meta | Alerta |
|---------|-----------|------|--------|
| **Score de performance (0-100)** | Avaliação consolidada de cada agente | > 80/100 | < 60/100 |
| **Tempo de execução do agente** | Duração de cada run do agente | < 60s (simples), < 300s (complexo) | > 2x da média |
| **Taxa de sucesso** | Percentual de execuções sem erro | > 95% | < 90% |
| **Taxa de fallback** | Frequência de uso do modelo alternativo | < 10% | > 25% |
| **Custo por execução** | Custo médio em tokens/dinheiro por run | Monitorar tendência | Aumento > 30%/mês |
| **Qualidade do output** | Avaliação por sampling (manual ou IA) | > 4/5 | < 3/5 |

### Composição do Score de Performance do Agente (0-100)

| Componente | Peso | Critério para 100% |
|-----------|------|---------------------|
| Taxa de sucesso | 30% | 100% das execuções sem erro |
| Tempo de execução | 20% | Abaixo de 50% do timeout |
| Qualidade do output | 25% | Avaliação 5/5 |
| Custo-eficiência | 15% | Abaixo do orçamento por execução |
| Uso de fallback | 10% | 0% de fallback |

### 3.4 Segurança

| Métrica | Definição | Meta | Alerta |
|---------|-----------|------|--------|
| **Tentativas de acesso não autorizado** | Requisições bloqueadas pelo sistema de auth | < 100/dia | > 500/dia (possível ataque) |
| **Tokens expirados reutilizados** | Tentativas de uso de JWT expirado | Registro apenas | Picos anômalos |
| **Tempo de resposta a incidentes** | Tempo entre detecção e primeira ação | < 1 hora | > 4 horas |
| **Vulnerabilidades conhecidas** | Dependências com CVE conhecido | 0 críticas | Qualquer crítica |
| **Cobertura de audit log** | Percentual de ações sensíveis com log | 100% | < 95% |

---

## 4. Metas por Período

### 4.1 Meta de 30 Dias — "Sistema Core Operacional"

**Objetivo:** Sistema base funcionando com fluxo comercial automatizado.

| Categoria | Meta | KPI de Validação |
|-----------|------|-----------------|
| **Funcionalidades** | CRM + Hunter + SDR operacionais | Todos os 3 módulos em produção |
| **Leads** | 100+ leads gerados por semana | Contagem semanal no dashboard |
| **Outreach** | 500+ mensagens enviadas no mês | Contador no módulo SDR |
| **Pipeline** | 20+ deals ativos no CRM | Contagem de deals em stages ativos |
| **Técnico** | Uptime > 99% | Monitoramento Uptime Robot |
| **Técnico** | API response < 200ms (p95) | Métricas do Prometheus |
| **Financeiro** | Custo operacional dentro do orçamento | < R$ 4.000/mês |

**Checklist de validação (30 dias):**

- [ ] Hunter Intelligence executando diariamente e gerando leads
- [ ] SDR enviando mensagens por pelo menos 2 canais
- [ ] CRM Pipeline com board Kanban funcional
- [ ] Dashboard exibindo métricas reais
- [ ] Pelo menos 1 reunião agendada via sistema
- [ ] Zero downtime não planejado
- [ ] Backup funcionando diariamente

### 4.2 Meta de 60 Dias — "Todos os Módulos Ativos"

**Objetivo:** Todos os 15 módulos em produção com pelo menos 3 clientes sendo gerenciados.

| Categoria | Meta | KPI de Validação |
|-----------|------|-----------------|
| **Funcionalidades** | 15/15 módulos em produção | Checklist de módulos ativos |
| **Clientes** | 3+ clientes gerenciados simultaneamente | Projetos ativos por cliente |
| **Conteúdo** | 100+ posts gerados por mês | Contagem no Content Studio |
| **Contratos** | 5+ contratos gerados via sistema | Contagem no módulo de contratos |
| **Financeiro** | NF-e emitidas via sistema | Pelo menos 3 NFs emitidas |
| **Mobile** | App Android funcional e em uso | Downloads + DAU |
| **Equipe** | 2+ usuários ativos no sistema | Login diário de 2+ pessoas |

**Checklist de validação (60 dias):**

- [ ] Todos os módulos acessíveis e funcionais
- [ ] Pelo menos 3 clientes com projetos ativos
- [ ] Content Studio gerando conteúdo para 2+ clientes
- [ ] Social Scheduler publicando automaticamente
- [ ] Orçamentos gerados por IA para pelo menos 5 projetos
- [ ] App Android instalado e em uso
- [ ] Relatório mensal gerado automaticamente
- [ ] Inventário de equipamentos 100% cadastrado

### 4.3 Meta de 90 Dias — "ROI Positivo"

**Objetivo:** Sistema pagando-se e gerando retorno mensurável.

| Categoria | Meta | KPI de Validação |
|-----------|------|-----------------|
| **Leads** | 500+ leads gerados por mês | Dashboard de analytics |
| **Conversão** | 3+ contratos fechados originados do sistema | Atribuição de origem no CRM |
| **Receita** | R$ 30.000+ em receita atribuída ao sistema | Relatório financeiro |
| **Clientes** | 10+ clientes gerenciados | Projetos + contratos ativos |
| **ROI** | Receita > Custo operacional | Cálculo de ROI no dashboard |
| **Produtividade** | 70% de redução de trabalho manual | Comparação de horas antes/depois |
| **Satisfação** | NPS > 7 (feedback dos usuários internos) | Pesquisa interna |

**Checklist de validação (90 dias):**

- [ ] 500+ leads gerados no último mês
- [ ] Taxa de qualificação > 15%
- [ ] Pelo menos 3 contratos fechados via sistema no mês
- [ ] ROI mensal positivo (receita gerada > custo operacional)
- [ ] 10+ clientes com projetos ativos ou em prospecção
- [ ] Redução comprovada de 70% no trabalho manual
- [ ] Todos os KPIs técnicos dentro das metas
- [ ] Zero incidentes críticos de segurança
- [ ] Equipe interna usando o sistema como ferramenta principal

---

## 5. Dashboard de Métricas

### Layout do Dashboard Principal

O dashboard de analytics deve consolidar as métricas mais importantes em uma visualização única:

```
┌─────────────────────────────────────────────────────────────────┐
│                    DASHBOARD — SOMOS PRODUTORA                   │
├──────────────┬──────────────┬──────────────┬───────────────────│
│  Leads/mês   │  Qualificados│  Reuniões    │  Contratos        │
│    523        │     98       │     31       │     4             │
│   ↑ 12%      │   ↑ 8%       │   ↑ 15%      │   ↑ 33%          │
├──────────────┴──────────────┴──────────────┴───────────────────│
│                                                                 │
│  [===== Funil de Conversão =====]   [=== Receita Mensal ===]  │
│  Leads:      ████████████  523      Jan: R$ 32.000             │
│  Qualif:     ████████      98       Fev: R$ 38.000             │
│  Reuniões:   █████         31       Mar: R$ 45.000             │
│  Propostas:  ███           19                                   │
│  Fechados:   ██            4                                    │
│                                                                 │
├──────────────────────────────────┬──────────────────────────────│
│     Agentes IA — Status          │     Performance Técnica      │
│  Hunter:   ● Online  Score: 87   │  Uptime:     99.7%          │
│  SDR:      ● Online  Score: 82   │  API (p95):  145ms          │
│  Content:  ● Online  Score: 91   │  Erros:      0.04%          │
│  Budget:   ● Online  Score: 88   │  CPU:        42%            │
│  Analyst:  ● Idle    Score: 85   │  RAM:        61%            │
├──────────────────────────────────┴──────────────────────────────│
│     Atividades Recentes                                         │
│  10:32  Hunter descobriu 47 novos leads (LinkedIn)             │
│  10:15  SDR enviou 23 mensagens (WhatsApp)                     │
│  09:45  Contrato #089 assinado — Cliente: ABC Produções        │
│  09:30  Orçamento #145 gerado — Valor: R$ 12.500               │
│  09:00  Backup diário concluído (2.3GB, 8min)                  │
└─────────────────────────────────────────────────────────────────┘
```

### Frequência de Monitoramento

| Tipo de Métrica | Coleta | Visualização | Alerta |
|----------------|--------|-------------|--------|
| Métricas técnicas (uptime, latência, erros) | Real-time | Dashboard ao vivo | Imediato (Slack/WhatsApp) |
| Métricas de agentes IA (execuções, custos) | A cada execução | Dashboard ao vivo | Se score < 60 |
| Métricas de pipeline (leads, deals) | Evento-driven | Atualização contínua | Diário (resumo) |
| Métricas financeiras (receita, custos) | Diário | Consolidado mensal | Semanal |
| Métricas de produtividade | Semanal | Relatório semanal | Se abaixo da meta |
| ROI e metas estratégicas | Mensal | Relatório executivo | Trimestral |

---

## 6. Processo de Revisão de Métricas

### Revisão Semanal (15 minutos)

| Item | Responsável |
|------|------------|
| Verificar KPIs de negócio vs metas | Gerente Comercial |
| Verificar performance dos agentes IA | Lead Developer |
| Identificar gargalos no pipeline | Equipe |
| Ajustar thresholds de qualificação se necessário | Equipe |

### Revisão Mensal (1 hora)

| Item | Responsável |
|------|------------|
| Análise de funil completo com taxa de conversão por estágio | Gerente Comercial |
| Custos de IA vs orçamento | Financeiro |
| Performance técnica e incidentes | Lead Developer |
| Qualidade de conteúdo gerado (sampling) | Marketing |
| ROI e comparação com metas do período | Diretoria |
| Definição de ajustes para o próximo mês | Equipe |

### Revisão Trimestral (2 horas)

| Item | Responsável |
|------|------------|
| Avaliação de metas estratégicas (30/60/90 dias) | Diretoria |
| ROI acumulado e projeção futura | Financeiro |
| Roadmap de evolução do sistema | Lead Developer |
| Feedback de usuários e NPS | Equipe |
| Decisão de investimentos adicionais | Diretoria |

---

## 7. Glossário de Métricas

| Termo | Definição |
|-------|-----------|
| **CPL** | Custo por Lead — custo total operacional dividido pelo número de leads gerados |
| **CAC** | Custo de Aquisição de Cliente — custo total para converter um lead em cliente pagante |
| **LTV** | Lifetime Value — receita total esperada de um cliente durante todo o relacionamento |
| **MRR** | Monthly Recurring Revenue — receita recorrente mensal de contratos ativos |
| **NPS** | Net Promoter Score — índice de satisfação e lealdade (escala -100 a +100) |
| **p50/p95/p99** | Percentis de latência — p95 significa que 95% das requisições são mais rápidas que o valor indicado |
| **ICP** | Ideal Customer Profile — perfil do cliente ideal para a Somos Produtora |
| **Churn** | Taxa de perda/abandono — deals que saem do pipeline sem fechamento |
| **Score de qualificação** | Pontuação de 0-100 atribuída por IA indicando a probabilidade de conversão de um lead |
| **Fallback** | Uso de modelo/serviço alternativo quando o principal falha ou está indisponível |

---

> **Última atualização:** Fevereiro 2026
> **Elaborado por:** Zhuhai Kameda Technology
> **Revisão:** Mensal (primeira revisão após 30 dias de operação)
> **Status:** Framework definido — métricas serão coletadas após deploy em produção
