# 12 — Estimativa de Custos

> **Somos Produtora** — Sistema Integrado de Gestão Comercial e Operacional com IA
> Projeção financeira completa: desenvolvimento, infraestrutura, operação e ROI

---

## Resumo Executivo

| Categoria | Valor |
|-----------|-------|
| **Desenvolvimento (único)** | R$ 45.000 — R$ 65.000 |
| **Infraestrutura mensal** | R$ 280 — R$ 430/mês |
| **APIs de IA mensal** | R$ 1.750 — R$ 3.250/mês |
| **Ferramentas e serviços mensal** | R$ 100 — R$ 150/mês |
| **Total mensal operacional** | R$ 2.130 — R$ 3.830/mês |
| **Payback estimado** | 2 — 3 meses |

---

## 1. Custo de Desenvolvimento

### Escopo do Projeto

| Item | Detalhes |
|------|----------|
| **Empresa** | Zhuhai Kameda Technology |
| **Duração** | 10 dias de desenvolvimento intensivo |
| **Equipe** | 1 Arquiteto/Lead Developer + Claude Code AI Assistant |
| **Metodologia** | Sprints diários com entregáveis definidos |
| **Entrega** | Sistema completo, documentado e pronto para produção |

### Composição do Valor

| Componente | Horas Est. | Valor (R$) |
|-----------|-----------|------------|
| Arquitetura e planejamento | 16h | 5.000 — 7.000 |
| Backend (FastAPI + 15 módulos) | 40h | 12.000 — 18.000 |
| Frontend (Next.js 15 + ShadcnUI) | 30h | 9.000 — 13.000 |
| Agentes IA (CrewAI + LangChain) | 24h | 8.000 — 11.000 |
| App Android (React Native) | 10h | 3.000 — 5.000 |
| Infraestrutura (Docker, CI/CD, deploy) | 10h | 3.000 — 4.000 |
| Testes e integração | 10h | 3.000 — 4.000 |
| Documentação técnica | 8h | 2.000 — 3.000 |
| **Total** | **~148h** | **R$ 45.000 — R$ 65.000** |

### O que está incluso

- Desenvolvimento completo de todos os 15 módulos
- Configuração de infraestrutura Docker pronta para deploy
- Pipeline CI/CD (GitHub Actions)
- Documentação técnica completa (API, arquitetura, deploy)
- 5 agentes de IA configurados e otimizados
- App Android com funcionalidades essenciais
- 1 semana de suporte pós-entrega para ajustes

### O que NÃO está incluso

- Hospedagem e domínio (custo mensal contínuo)
- Créditos de APIs de IA (custo mensal contínuo)
- Treinamento presencial de equipe
- Customizações adicionais após período de suporte
- Manutenção e evolução contínua (negociável à parte)

---

## 2. Infraestrutura Mensal

### Servidor Principal (VPS)

| Provedor | Configuração | Preço Mensal (R$) | Observações |
|----------|-------------|-------------------|-------------|
| **Hetzner (recomendado)** | CPX41: 8 vCPU AMD, 16GB RAM, 240GB SSD | ~R$ 150 — 200 | Melhor custo-benefício, datacenter na Europa |
| **Hetzner** | CCX33: 8 vCPU dedicado, 32GB RAM, 240GB SSD | ~R$ 300 — 400 | Recomendado para produção com volume alto |
| Contabo | VPS M: 8 vCPU, 30GB RAM, 400GB SSD | ~R$ 180 — 250 | Alternativa econômica, boa para início |
| DigitalOcean | Premium: 8 vCPU, 32GB RAM, 320GB SSD | ~R$ 500 — 700 | Mais caro, melhor suporte |

**Recomendação:** Hetzner CCX33 para produção — R$ 300-400/mês

### Configuração do Servidor

| Serviço | Recurso Alocado | Porta |
|---------|----------------|-------|
| PostgreSQL 16 | 4GB RAM, 100GB disco | 5432 |
| Redis 7 | 1GB RAM | 6379 |
| Qdrant | 2GB RAM, 20GB disco | 6333 |
| MinIO | 1GB RAM, 200GB disco | 9000 |
| FastAPI (backend) | 2GB RAM | 8000 |
| Next.js (frontend) | 2GB RAM | 3000 |
| Nginx/Traefik | 512MB RAM | 80/443 |
| Celery Workers (x4) | 2GB RAM | — |
| Evolution API | 1GB RAM | 8080 |
| **Total necessário** | **~16GB RAM** | — |

### Outros Custos de Infraestrutura

| Item | Custo | Frequência |
|------|-------|-----------|
| Domínio `.com.br` | R$ 40 — 50 | Anual |
| Certificado SSL | Gratuito (Let's Encrypt) | Auto-renovação |
| Backup externo (Backblaze B2) | R$ 20 — 30 | Mensal |
| Monitoramento (Uptime Robot Pro) | Gratuito (tier free) | — |

### Resumo — Infraestrutura Mensal

| Item | Mínimo (R$) | Máximo (R$) |
|------|-------------|-------------|
| VPS (Hetzner) | 250 | 400 |
| Backup externo | 20 | 30 |
| Domínio (proporcional mensal) | ~4 | ~4 |
| **Total Infraestrutura** | **~R$ 274** | **~R$ 434** |

---

## 3. APIs de Inteligência Artificial — Custo Mensal

### Estimativa por Volume Médio de Uso

Considerando uma operação com ~500 leads/mês, ~10 clientes gerenciados e ~200 peças de conteúdo/mês:

#### Anthropic (Claude API)

| Uso | Modelo | Tokens/mês (est.) | Custo (R$) |
|-----|--------|-------------------|------------|
| SDR — Personalização de mensagens | Claude 3.5 Sonnet | ~5M input + 2M output | 250 — 400 |
| SDR — Qualificação de respostas | Claude 3.5 Sonnet | ~2M input + 500K output | 100 — 150 |
| Content Studio — Geração de textos | Claude 3.5 Sonnet | ~8M input + 4M output | 400 — 600 |
| Orçamento Inteligente | Claude 3.5 Sonnet | ~1M input + 500K output | 50 — 100 |
| Assistente Comercial | Claude 3.5 Sonnet | ~3M input + 1M output | 150 — 250 |
| **Subtotal Anthropic** | | | **R$ 950 — R$ 1.500** |

#### OpenAI (GPT-4o + DALL-E)

| Uso | Modelo | Volume/mês (est.) | Custo (R$) |
|-----|--------|-------------------|------------|
| Análise de dados/relatórios | GPT-4o | ~3M input + 1M output | 200 — 350 |
| Fallback para SDR/Content | GPT-4o | ~2M input + 1M output | 150 — 250 |
| Geração de imagens | DALL-E 3 | ~300-500 imagens | 150 — 400 |
| **Subtotal OpenAI** | | | **R$ 500 — R$ 1.000** |

#### Google AI (Gemini)

| Uso | Modelo | Volume/mês (est.) | Custo (R$) |
|-----|--------|-------------------|------------|
| Intelligence Feed — Análise de notícias | Gemini 1.5 Pro | ~5M input + 1M output | 100 — 250 |
| Processamento de documentos longos | Gemini 1.5 Pro | ~3M input + 500K output | 50 — 150 |
| Fallback geral | Gemini 1.5 Flash | ~2M input + 500K output | 20 — 50 |
| **Subtotal Google AI** | | | **R$ 170 — R$ 450** |

#### SerpAPI (Buscas Google)

| Uso | Plano | Buscas/mês | Custo (R$) |
|-----|-------|-----------|------------|
| Hunter Intelligence — Prospecção | Business | 5.000 buscas | ~R$ 250 |
| **Subtotal SerpAPI** | | | **R$ 250** |

### Resumo — APIs de IA Mensal

| Provedor | Mínimo (R$) | Máximo (R$) |
|----------|-------------|-------------|
| Anthropic (Claude) | 950 | 1.500 |
| OpenAI (GPT-4o + DALL-E) | 500 | 1.000 |
| Google AI (Gemini) | 170 | 450 |
| SerpAPI | 250 | 250 |
| **Total APIs de IA** | **R$ 1.870** | **R$ 3.200** |

### Estratégias de Otimização de Custos de IA

| Estratégia | Economia Estimada | Detalhes |
|-----------|-------------------|---------|
| Cache inteligente de respostas | 15-25% | Armazenar respostas similares para evitar chamadas repetidas |
| Roteamento de modelos | 10-20% | Usar modelos menores (Flash/Haiku) para tarefas simples, reservar Pro/Sonnet para tarefas complexas |
| Batching de requisições | 5-10% | Agrupar múltiplas análises em uma única chamada |
| Embeddings locais | 10-15% | Usar modelos de embedding locais para busca semântica, reduzindo chamadas à API |
| Templates otimizados | 5-10% | Prompts enxutos e bem-estruturados que consomem menos tokens |

**Com todas as otimizações aplicadas, o custo pode cair 30-50%, chegando a R$ 1.000 — R$ 1.800/mês.**

---

## 4. Ferramentas e Serviços — Custo Mensal

| Ferramenta | Finalidade | Custo (R$) | Observações |
|-----------|-----------|------------|-------------|
| Evolution API | WhatsApp Business (automação) | Gratuito | Self-hosted, código aberto |
| Resend | Envio de e-mails transacionais e marketing | ~R$ 100 | Plano Pro: 50.000 emails/mês |
| Firebase | Push notifications (Android app) | Gratuito | Tier free cobre o volume |
| GitHub | Repositório + Actions (CI/CD) | Gratuito | Tier free para repos privados |
| Let's Encrypt | Certificados SSL | Gratuito | Auto-renovação via Certbot/Traefik |
| Uptime Robot | Monitoramento de disponibilidade | Gratuito | Tier free: 50 monitores |
| **Total Ferramentas** | | **~R$ 100** | |

---

## 5. Consolidação de Custos

### Investimento Inicial (Único)

| Item | Valor (R$) |
|------|------------|
| Desenvolvimento do sistema | 45.000 — 65.000 |
| Setup de infraestrutura (incluso no dev) | 0 |
| Domínio (primeiro ano) | 40 — 50 |
| **Total Inicial** | **R$ 45.040 — R$ 65.050** |

### Custo Operacional Mensal

| Categoria | Mínimo (R$) | Máximo (R$) |
|-----------|-------------|-------------|
| Infraestrutura (VPS + backup) | 274 | 434 |
| APIs de IA | 1.870 | 3.200 |
| Ferramentas e serviços | 100 | 150 |
| **Total Mensal** | **R$ 2.244** | **R$ 3.784** |

### Custo no Primeiro Ano

| Item | Mínimo (R$) | Máximo (R$) |
|------|-------------|-------------|
| Desenvolvimento | 45.000 | 65.000 |
| Operacional (12 meses) | 26.928 | 45.408 |
| **Total Primeiro Ano** | **R$ 71.928** | **R$ 110.408** |

### Custo Anual a partir do Segundo Ano

| Item | Mínimo (R$) | Máximo (R$) |
|------|-------------|-------------|
| Operacional (12 meses) | 26.928 | 45.408 |
| Manutenção/evolução (estimativa) | 10.000 | 20.000 |
| **Total Anual (a partir do ano 2)** | **R$ 36.928** | **R$ 65.408** |

---

## 6. Análise de ROI (Retorno sobre Investimento)

### Economia Direta — Substituição de Mão de Obra

O sistema substitui ou reduz significativamente a necessidade das seguintes funções:

| Função Substituída | Salário Mensal (R$) | Funções do Sistema |
|-------------------|--------------------|--------------------|
| SDR (1-2 pessoas) | 3.000 — 5.000 cada | Hunter Intelligence + SDR Automation |
| Assistente Comercial | 3.500 — 5.000 | CRM Pipeline + Assistente IA |
| Analista Financeiro (parcial) | 2.000 — 3.000 | Financeiro ERP + Analytics |
| Social Media Manager (parcial) | 2.000 — 3.500 | Content Studio + Social Scheduler |
| Assistente Administrativo | 2.500 — 3.500 | Contratos + Orçamentos + Inventário |
| **Total Economia** | **R$ 13.000 — R$ 20.000/mês** | |

Considerando encargos trabalhistas (~70% sobre salários no regime CLT):

| Cenário | Salários (R$) | Encargos (R$) | Total (R$) |
|---------|--------------|---------------|------------|
| Conservador (3 funções) | 8.500 | 5.950 | **14.450/mês** |
| Moderado (4 funções) | 13.000 | 9.100 | **22.100/mês** |
| Otimista (5 funções) | 20.000 | 14.000 | **34.000/mês** |

### Ganho Indireto — Aumento de Receita

| Métrica | Sem Sistema | Com Sistema | Impacto |
|---------|------------|------------|---------|
| Leads gerados/mês | 30-50 (manual) | 500+ (automatizado) | +900% |
| Leads qualificados/mês | 5-10 | 75-125 | +1.150% |
| Reuniões agendadas/mês | 3-5 | 22-37 | +640% |
| Propostas enviadas/mês | 2-4 | 15-25 | +525% |
| Contratos fechados/mês | 1-2 | 4-7 | +250% |
| Ticket médio por contrato | R$ 8.000 | R$ 10.000* | +25% |

*O orçamento inteligente com IA tende a otimizar a precificação, aumentando o ticket médio.

### Receita Incremental Estimada

| Cenário | Contratos Novos/mês | Ticket Médio (R$) | Receita Adicional/mês (R$) |
|---------|---------------------|--------------------|---------------------------|
| Conservador | +2 | 8.000 | 16.000 |
| Moderado | +4 | 10.000 | 40.000 |
| Otimista | +6 | 12.000 | 72.000 |

### Cálculo de Payback

#### Cenário Conservador

| Mês | Investimento Acumulado (R$) | Economia + Receita Acumulada (R$) | Saldo (R$) |
|-----|---------------------------|----------------------------------|------------|
| 0 | -55.000 (desenvolvimento) | 0 | -55.000 |
| 1 | -58.000 (+ operacional) | +14.450 (economia) | -43.550 |
| 2 | -61.000 | +44.900 | -16.100 |
| 3 | -64.000 | +75.350 | **+11.350** |

**Payback conservador: ~3 meses**

#### Cenário Moderado (com receita incremental)

| Mês | Investimento Acumulado (R$) | Retorno Acumulado (R$) | Saldo (R$) |
|-----|---------------------------|----------------------|------------|
| 0 | -55.000 | 0 | -55.000 |
| 1 | -58.000 | +32.100 (economia + receita) | -25.900 |
| 2 | -61.000 | +64.200 | **+3.200** |

**Payback moderado: ~2 meses**

### ROI em 12 Meses

| Cenário | Investimento Total (R$) | Retorno Total (R$) | ROI |
|---------|------------------------|--------------------|----|
| Conservador | 91.000 | 173.400 | **90%** |
| Moderado | 91.000 | 385.200 | **323%** |
| Otimista | 91.000 | 661.200 | **627%** |

---

## 7. Comparativo com Alternativas

### Opção A: Ferramentas SaaS Separadas

| Ferramenta | Custo Mensal (R$) |
|-----------|-------------------|
| HubSpot CRM (Professional) | ~R$ 4.500 |
| Pipedrive (Professional) | ~R$ 500 |
| RD Station Marketing | ~R$ 1.500 |
| Monday.com (Standard) | ~R$ 600 |
| Conta Azul (Pro) | ~R$ 400 |
| Hootsuite (Professional) | ~R$ 500 |
| Salesforce (Essentials) | ~R$ 1.200 |
| **Total SaaS** | **~R$ 9.200/mês** |

**Comparativo:** Sistema próprio custa R$ 2.244-3.784/mês vs R$ 9.200/mês em SaaS (economia de 59-76%). Além disso, ferramentas SaaS não incluem agentes IA de prospecção e não são integradas entre si.

### Opção B: Desenvolvimento Tradicional (Agência)

| Item | Estimativa (R$) |
|------|----------------|
| Desenvolvimento (6-12 meses, equipe de 4-6) | 300.000 — 800.000 |
| Prazo | 6 — 12 meses |
| Manutenção mensal | 5.000 — 15.000 |

**Comparativo:** Desenvolvimento com IA (Kameda + Claude Code) entrega em 10 dias por R$ 45-65K vs 6-12 meses por R$ 300-800K de uma agência tradicional. Redução de 85-92% no custo e 95% no tempo.

---

## 8. Cenários de Escala

### Crescimento do Volume de Uso

| Métrica | Fase Inicial | 6 Meses | 12 Meses |
|---------|-------------|---------|----------|
| Leads processados/mês | 500 | 2.000 | 5.000 |
| Clientes gerenciados | 3-5 | 10-15 | 20-30 |
| Posts gerados/mês | 200 | 500 | 1.000 |
| Custo IA estimado/mês | R$ 2.000 | R$ 4.500 | R$ 8.000 |
| Custo infra estimado/mês | R$ 350 | R$ 500 | R$ 800 |

### Quando Escalar Infraestrutura

| Trigger | Ação | Custo Adicional (R$) |
|---------|------|---------------------|
| RAM > 80% consistente | Upgrade VPS para 64GB | +R$ 200-300/mês |
| Disco > 70% | Adicionar volume de storage | +R$ 50-100/mês |
| API response > 500ms p95 | Adicionar segundo servidor + load balancer | +R$ 300-400/mês |
| Agentes IA em fila > 5 min | Adicionar worker dedicado para Celery | +R$ 200/mês |

---

## 9. Recomendações de Otimização Financeira

### Curto Prazo (Meses 1-3)

1. **Começar com Hetzner CPX41** (R$ 150-200/mês) e escalar conforme necessidade
2. **Usar tier free** do Firebase, Uptime Robot e GitHub ao máximo
3. **Implementar cache agressivo** para reduzir chamadas de IA em 30%
4. **Monitorar custos de API** semanalmente para identificar desperdícios

### Médio Prazo (Meses 4-6)

1. **Negociar contratos** com Anthropic/OpenAI para volume (descontos de 10-20%)
2. **Implementar modelos locais** para tarefas simples (classificação, embedding)
3. **Otimizar prompts** para reduzir consumo de tokens
4. **Automatizar scaling** com alertas de threshold

### Longo Prazo (Meses 7-12)

1. **Considerar fine-tuning** de modelos menores para tarefas repetitivas
2. **Avaliar GPU dedicada** para geração de imagens local (elimina custo DALL-E)
3. **Implementar CDN** para assets estáticos se o tráfego crescer
4. **Explorar modelos open-source** (Llama, Mistral) para reduzir dependência de APIs

---

## Notas e Considerações

1. **Valores em Reais (BRL):** Custos de APIs internacionais foram convertidos com base na cotação de ~R$ 5,80/USD. Variações cambiais podem impactar os custos de IA e infraestrutura.

2. **Estimativas conservadoras:** Os custos de IA foram estimados com margem de segurança. O uso real pode ser menor nos primeiros meses durante o ramp-up.

3. **Sem custos ocultos:** O sistema é self-hosted e open-source, sem licenciamento proprietário. O cliente é dono do código-fonte.

4. **Flexibilidade:** Cada componente pode ser substituído por alternativa mais barata. Por exemplo, Evolution API (gratuito) no lugar de Twilio (pago) para WhatsApp.

5. **Impostos:** Os valores de desenvolvimento não incluem impostos. Consultar contador para regime tributário aplicável.

---

> **Última atualização:** Fevereiro 2026
> **Elaborado por:** Zhuhai Kameda Technology
> **Validade da proposta:** 30 dias
> **Status:** Estimativa aprovada para planejamento
