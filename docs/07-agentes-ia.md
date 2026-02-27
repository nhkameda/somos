# 07 - Agentes de Inteligencia Artificial

> Somos Produtora - Catalogo Completo de Agentes IA
> Versao: 1.0 | Ultima atualizacao: 2026-02-28

---

## Indice

1. [Visao Geral](#visao-geral)
2. [Arquitetura Multi-Agente](#arquitetura-multi-agente)
3. [Catalogo de Agentes](#catalogo-de-agentes)
4. [Ciclo de Vida dos Agentes](#ciclo-de-vida-dos-agentes)
5. [Estrategia de Memoria](#estrategia-de-memoria)
6. [Estimativa de Custos com Tokens](#estimativa-de-custos-com-tokens)
7. [Monitoramento e KPIs](#monitoramento-e-kpis)
8. [Seguranca e Limites](#seguranca-e-limites)

---

## 1. Visao Geral

O sistema da Somos Produtora emprega **15 agentes de inteligencia artificial** organizados em 5 equipes (Crews) que trabalham de forma coordenada para automatizar prospeccao, vendas, criacao de conteudo, analise de mercado e operacoes internas.

### Tecnologias Base

| Componente         | Tecnologia                                      |
|--------------------|------------------------------------------------|
| **Orquestracao**   | CrewAI (coordenacao multi-agente)              |
| **Ferramentas**    | LangChain Tools + ferramentas customizadas     |
| **Modelos LLM**    | Claude 3.5 Sonnet, GPT-4o, GPT-4o-mini, Gemini Pro |
| **Geracao Imagem** | DALL-E 3, Flux (via Replicate)                 |
| **Embeddings**     | text-embedding-3-small (OpenAI)                |
| **Memoria Curta**  | Redis (contexto de sessao, ultimas interacoes) |
| **Memoria Longa**  | Qdrant (busca semantica de historico)           |
| **Execucao**       | Celery Workers (async, filas dedicadas)         |

### Principios de Design dos Agentes

1. **Persona definida**: Cada agente tem nome, personalidade e estilo de comunicacao
2. **Objetivo unico**: Cada agente foca em uma tarefa especifica
3. **Ferramentas minimas**: Cada agente recebe apenas as ferramentas necessarias
4. **Fallback definido**: Se o modelo primario falhar, ha um modelo alternativo
5. **Limites claros**: Cada agente tem limites de acoes, gastos e autonomia
6. **Supervisao humana**: Acoes criticas requerem aprovacao de um humano

---

## 2. Arquitetura Multi-Agente

### Organizacao em Crews (Equipes)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        ORQUESTRADOR CENTRAL                            │
│                     (CrewAI Manager Agent)                              │
└────────┬──────────┬──────────┬──────────┬──────────┬───────────────────┘
         │          │          │          │          │
    ┌────▼────┐ ┌───▼───┐ ┌───▼───┐ ┌───▼───┐ ┌───▼───┐
    │ CREW 1  │ │CREW 2 │ │CREW 3 │ │CREW 4 │ │CREW 5 │
    │Prospeccao│ │Vendas │ │Conteudo│ │Analise│ │Operac.│
    └────┬────┘ └───┬───┘ └───┬───┘ └───┬───┘ └───┬───┘
         │          │         │         │          │
    ┌────┴────┐  ┌──┴──┐  ┌──┴──┐  ┌──┴────┐  ┌──┴──────┐
    │Hunter   │  │SDR  │  │Writer│  │News   │  │Budget   │
    │LinkedIn │  │Whats│  │      │  │Curator│  │Calc     │
    │         │  │     │  │Image │  │       │  │         │
    │Hunter   │  │SDR  │  │Creator│ │Trend  │  │Contract │
    │Google   │  │Linkd│  │      │  │Analyz.│  │Drafter  │
    │         │  │     │  │      │  │       │  │         │
    │Hunter   │  │SDR  │  │      │  │       │  │Perf.   │
    │Social   │  │Email│  │      │  │       │  │Evaluat.│
    │         │  │     │  │      │  │       │  │         │
    │         │  │Qual.│  │      │  │       │  │Report  │
    │         │  │     │  │      │  │       │  │Generat.│
    └─────────┘  └─────┘  └─────┘  └───────┘  └────────┘
```

### Fluxo de Trabalho Tipico (Prospeccao -> Venda)

```mermaid
sequenceDiagram
    participant HC as Hunter LinkedIn
    participant HG as Hunter Google
    participant HS as Hunter Social
    participant Q as Qualificador
    participant SW as SDR WhatsApp
    participant SL as SDR LinkedIn
    participant SE as SDR Email
    participant H as Humano (Comercial)

    Note over HC,HG,HS: FASE 1: Prospeccao (Paralelo)
    HC->>HC: Busca leads no LinkedIn por segmento
    HG->>HG: Busca empresas no Google/SerpAPI
    HS->>HS: Busca leads em Instagram/Facebook/YouTube

    HC->>Q: Envia leads encontrados
    HG->>Q: Envia leads encontrados
    HS->>Q: Envia leads encontrados

    Note over Q: FASE 2: Qualificacao
    Q->>Q: Pontua leads (scoring 0-100)
    Q->>Q: Classifica: Quente/Morno/Frio

    Note over SW,SL,SE: FASE 3: Outreach (Paralelo por canal)
    Q->>SW: Leads com WhatsApp (score >= 60)
    Q->>SL: Leads com LinkedIn (score >= 50)
    Q->>SE: Todos os leads (email)

    SW->>SW: Envia mensagem personalizada
    SL->>SL: Envia convite + mensagem
    SE->>SE: Envia cold email personalizado

    Note over H: FASE 4: Handoff Humano
    SW->>H: Lead respondeu positivamente
    SL->>H: Lead aceitou conexao e respondeu
    SE->>H: Lead abriu email e respondeu

    H->>H: Assume negociacao direta
```

---

## 3. Catalogo de Agentes

---

### CREW 1: PROSPECCAO

---

#### Agente 01: Hunter LinkedIn

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Hunter LinkedIn                                                   |
| **Persona**          | Pesquisador corporativo meticuloso e analitico. Fala de forma objetiva e organizada. Especialista em encontrar tomadores de decisao em empresas-alvo. |
| **Objetivo**         | Encontrar leads qualificados no LinkedIn filtrando por segmento de mercado, cargo, localizacao e tamanho da empresa |
| **Ferramentas**      | `linkedin_search_tool` (busca de perfis), `linkedin_company_tool` (info de empresas), `enrichment_tool` (enriquecimento de dados), `database_tool` (salvar no CRM) |
| **Modelo IA**        | GPT-4o (primario) / Gemini Pro (fallback)                         |
| **Memoria**          | Redis: ultimos 50 perfis analisados. Qdrant: historico de todos os leads encontrados, perfis de ICP (Ideal Customer Profile) |
| **Limite de Acoes**  | Maximo 200 buscas/dia, 50 perfis detalhados/dia                  |
| **Aprovacao Humana** | Nao necessaria para busca. Necessaria para adicionar lead ao pipeline ativo |
| **Custo Estimado**   | ~$30-50/mes em tokens                                             |

**Prompt de Sistema (resumo)**:
> Voce e um pesquisador corporativo da Somos Produtora, uma empresa de producao audiovisual. Sua missao e encontrar empresas e profissionais que possam precisar de servicos de producao de video, fotografia, live streaming ou conteudo digital. Foque em: (1) Diretores de marketing, (2) Gerentes de comunicacao, (3) Donos de agencias, (4) Startups em fase de crescimento. Priorize empresas na regiao de [REGIAO]. Sempre registre: nome, cargo, empresa, LinkedIn URL, email (se disponivel), telefone (se disponivel).

---

#### Agente 02: Hunter Google

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Hunter Google                                                     |
| **Persona**          | Detetive digital curioso e persistente. Especialista em OSINT (Open Source Intelligence) e pesquisa avancada na web. |
| **Objetivo**         | Encontrar empresas-alvo atraves de buscas no Google, analise de websites e diretamente em portais de negocios |
| **Ferramentas**      | `serpapi_search_tool` (busca Google), `website_scraper_tool` (extrair dados de sites), `google_maps_tool` (busca local), `whois_tool` (dados de dominio), `database_tool` |
| **Modelo IA**        | GPT-4o-mini (primario) / Gemini Flash (fallback)                  |
| **Memoria**          | Redis: ultimas 100 buscas. Qdrant: historico de empresas encontradas e websites analisados |
| **Limite de Acoes**  | Maximo 500 buscas/dia no SerpAPI, 200 scrapes de website/dia     |
| **Aprovacao Humana** | Nao necessaria                                                    |
| **Custo Estimado**   | ~$20-35/mes em tokens + $50/mes SerpAPI                          |

**Queries de Busca Tipicas**:
- `"produtora de video" OR "producao audiovisual" [cidade]`
- `"precisamos de video institucional" site:linkedin.com`
- `"agencia de marketing" [regiao] -site:instagram.com`
- `"evento corporativo" [segmento] [ano]`

---

#### Agente 03: Hunter Social

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Hunter Social                                                     |
| **Persona**          | Social media analyst perspicaz. Entende algoritmos e comportamento em redes sociais. Identifica oportunidades de negocio em postagens e interacoes. |
| **Objetivo**         | Monitorar Instagram, Facebook e YouTube para encontrar empresas e influenciadores que possam precisar de servicos de producao audiovisual |
| **Ferramentas**      | `instagram_search_tool` (busca perfis/hashtags), `facebook_search_tool` (busca paginas/grupos), `youtube_search_tool` (busca canais), `social_enrichment_tool`, `database_tool` |
| **Modelo IA**        | GPT-4o-mini (primario) / Gemini Flash (fallback)                  |
| **Memoria**          | Redis: hashtags e perfis monitorados. Qdrant: historico de leads sociais |
| **Limite de Acoes**  | Respeitando rate limits de cada API. Maximo 300 analises/dia      |
| **Aprovacao Humana** | Nao necessaria para monitoramento. Necessaria para interacao direta |
| **Custo Estimado**   | ~$15-25/mes em tokens                                             |

**Sinais de Oportunidade Monitorados**:
- Empresa postando conteudo de baixa qualidade visual
- Empresa anunciando lancamento de produto/servico
- Empresa contratando para area de marketing
- Influenciador buscando produtora
- Hashtags como #procuroprodutora, #videoprofissional

---

### CREW 2: VENDAS (SDR)

---

#### Agente 04: SDR WhatsApp

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | SDR WhatsApp                                                      |
| **Persona**          | Consultor comercial amigavel e profissional. Comunica-se de forma pessoal mas nao informal demais. Usa tom consultivo, nunca agressivo. Respeita horarios comerciais (8h-18h). |
| **Objetivo**         | Enviar mensagens personalizadas via WhatsApp para leads qualificados, iniciar conversas e agendar reunioes |
| **Ferramentas**      | `whatsapp_send_tool` (Evolution API), `whatsapp_template_tool` (templates aprovados), `calendar_tool` (agendar reuniao), `lead_info_tool` (dados do lead), `database_tool` |
| **Modelo IA**        | Claude 3.5 Sonnet (primario) / GPT-4o (fallback)                 |
| **Memoria**          | Redis: historico da conversa atual. Qdrant: historico de todas as conversas do lead, templates de sucesso |
| **Limite de Acoes**  | Maximo 50 novas conversas/dia, 200 mensagens/dia. Apenas em horario comercial (8h-18h, seg-sex) |
| **Aprovacao Humana** | Primeira mensagem: nao. Follow-up apos 3 mensagens sem resposta: sim. Envio de proposta: sim |
| **Custo Estimado**   | ~$40-70/mes em tokens                                             |

**Regras de Engajamento**:
1. Nunca enviar mensagem antes das 8h ou depois das 18h
2. Nunca enviar no domingo
3. Maximo 3 follow-ups sem resposta (depois, marcar como "frio")
4. Intervalo minimo de 48h entre follow-ups
5. Sempre personalizar com nome, empresa e motivo do contato
6. Se o lead pedir para parar, marcar como "opt-out" imediatamente

---

#### Agente 05: SDR LinkedIn

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | SDR LinkedIn                                                      |
| **Persona**          | Networker profissional e estrategico. Comunica-se com tom corporativo elegante. Cria conexoes genuinas antes de fazer ofertas. |
| **Objetivo**         | Enviar convites de conexao personalizados e sequencias de mensagens no LinkedIn para gerar interesse e agendar reunioes |
| **Ferramentas**      | `linkedin_connect_tool` (enviar convite), `linkedin_message_tool` (enviar mensagem), `linkedin_profile_tool` (analisar perfil), `lead_info_tool`, `database_tool` |
| **Modelo IA**        | Claude 3.5 Sonnet (primario) / GPT-4o (fallback)                 |
| **Memoria**          | Redis: status de conexao e ultimas mensagens. Qdrant: historico de interacoes LinkedIn, mensagens com melhor taxa de aceite |
| **Limite de Acoes**  | Maximo 25 convites/dia, 50 mensagens/dia (respeitando limites do LinkedIn) |
| **Aprovacao Humana** | Convites: nao. Mensagens de follow-up: nao. Proposta formal: sim |
| **Custo Estimado**   | ~$35-60/mes em tokens                                             |

**Sequencia Padrao de Abordagem**:
1. **Dia 0**: Enviar convite de conexao com nota personalizada (300 chars)
2. **Dia 2** (apos aceite): Mensagem de agradecimento + pergunta consultiva
3. **Dia 5**: Compartilhar case relevante da Somos Produtora
4. **Dia 10**: Sugerir call de 15 minutos para trocar ideias
5. **Dia 20**: Ultimo follow-up com oferta de valor especifica

---

#### Agente 06: SDR Email

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | SDR Email                                                         |
| **Persona**          | Copywriter comercial direto e persuasivo. Escreve emails curtos, claros e com CTAs (Call to Action) fortes. Domina tecnicas de cold email. |
| **Objetivo**         | Enviar sequencias de cold email personalizadas para leads, nutrir interesse e gerar agendamentos |
| **Ferramentas**      | `email_send_tool` (Resend API), `email_template_tool` (templates), `email_tracking_tool` (aberturas/cliques), `lead_info_tool`, `database_tool` |
| **Modelo IA**        | Claude 3.5 Sonnet (primario) / GPT-4o (fallback)                 |
| **Memoria**          | Redis: status de cada sequencia de email. Qdrant: historico de emails enviados, assuntos com melhor taxa de abertura |
| **Limite de Acoes**  | Maximo 100 emails/dia (aquecimento progressivo de dominio). Envio distribuido ao longo do dia |
| **Aprovacao Humana** | Primeiro email da sequencia: nao. Templates novos: sim. Email com desconto/proposta: sim |
| **Custo Estimado**   | ~$25-40/mes em tokens + $20/mes Resend                           |

**Estrutura da Sequencia de Emails**:
1. **Email 1 (Dia 0)**: Introducao + problema identificado + CTA simples
2. **Email 2 (Dia 3)**: Case study relevante + resultado numerico
3. **Email 3 (Dia 7)**: Valor adicional (artigo/video) + CTA para reuniao
4. **Email 4 (Dia 14)**: Break-up email ("Nao quero incomodar, mas...")

---

#### Agente 07: Qualificador

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Qualificador                                                      |
| **Persona**          | Analista comercial rigoroso e imparcial. Avalia leads com base em dados objetivos, sem vieses. Usa frameworks de qualificacao (BANT, MEDDIC). |
| **Objetivo**         | Pontuar e classificar leads com base em dados coletados e interacoes, priorizando os de maior potencial de conversao |
| **Ferramentas**      | `lead_scoring_tool` (calcular score), `lead_enrichment_tool` (buscar dados adicionais), `crm_update_tool` (atualizar status), `notification_tool` (alertar equipe), `database_tool` |
| **Modelo IA**        | GPT-4o (primario) / Claude 3.5 Sonnet (fallback)                 |
| **Memoria**          | Redis: scores recentes. Qdrant: historico de leads qualificados e padroes de conversao |
| **Limite de Acoes**  | Sem limite (processo automatico). Reavaliacao a cada nova interacao |
| **Aprovacao Humana** | Nao necessaria para scoring. Necessaria para mover lead para "Proposta" |
| **Custo Estimado**   | ~$20-35/mes em tokens                                             |

**Criterios de Scoring (0-100)**:

| Criterio                        | Peso | Pontuacao                               |
|---------------------------------|------|-----------------------------------------|
| Cargo (decisor/influenciador)   | 25%  | Diretor: 100, Gerente: 70, Analista: 30 |
| Tamanho da empresa              | 20%  | Grande: 100, Media: 70, Pequena: 40     |
| Segmento aderente               | 20%  | Audiovisual: 100, Marketing: 80, Outro: 40 |
| Engajamento (respostas)         | 15%  | Respondeu: 100, Abriu: 50, Ignorou: 0   |
| Orcamento indicado              | 10%  | Alto: 100, Medio: 60, Baixo: 20        |
| Urgencia                        | 10%  | Imediata: 100, Trimestre: 60, Indefinida: 20 |

**Classificacao**:
- **Quente** (80-100): Transferir imediatamente para equipe comercial
- **Morno** (50-79): Continuar nurturing com SDRs
- **Frio** (0-49): Manter em lista de email marketing

---

### CREW 3: CONTEUDO

---

#### Agente 08: Content Writer

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Content Writer                                                    |
| **Persona**          | Redator criativo e versatil, com profundo conhecimento do setor audiovisual brasileiro. Escreve em portugues brasileiro impecavel, adaptando tom e estilo conforme a plataforma (LinkedIn formal, Instagram descontraido, blog informativo). |
| **Objetivo**         | Gerar conteudo textual para redes sociais, blog, email marketing e materiais comerciais da Somos Produtora |
| **Ferramentas**      | `content_generator_tool` (gerar texto), `seo_tool` (otimizacao SEO), `brand_guidelines_tool` (consultar identidade visual/verbal), `content_calendar_tool` (calendario editorial), `plagiarism_check_tool`, `database_tool` |
| **Modelo IA**        | Claude 3.5 Sonnet (primario) / GPT-4o (fallback)                 |
| **Memoria**          | Redis: conteudos recentes gerados. Qdrant: todo o historico de conteudo publicado, brand voice da Somos, personas de audiencia |
| **Limite de Acoes**  | Maximo 30 pecas de conteudo/dia                                   |
| **Aprovacao Humana** | Sempre. Todo conteudo passa por revisao antes de publicacao       |
| **Custo Estimado**   | ~$50-80/mes em tokens                                             |

**Tipos de Conteudo Gerados**:

| Tipo                  | Plataforma      | Frequencia Sugerida | Tamanho Medio       |
|-----------------------|-----------------|---------------------|---------------------|
| Post carrossel        | Instagram       | 3x/semana           | 10 slides, 150 chars cada |
| Post com imagem       | Instagram       | 5x/semana           | 200-300 chars       |
| Stories roteiro       | Instagram       | Diario              | 5-10 frames         |
| Post profissional     | LinkedIn        | 3x/semana           | 500-1500 chars      |
| Artigo longo          | LinkedIn/Blog   | 1x/semana           | 1500-3000 palavras  |
| Email newsletter      | Email           | 1x/semana           | 500-800 palavras    |
| Script de video       | YouTube         | 1x/semana           | 800-1500 palavras   |
| Caption de video      | Redes sociais   | Sob demanda         | 100-300 chars       |

---

#### Agente 09: Image Creator

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Image Creator                                                     |
| **Persona**          | Diretor de arte digital com olho apurado para composicao, cores e branding. Traduz briefings textuais em prompts visuais precisos. |
| **Objetivo**         | Gerar imagens para redes sociais, apresentacoes e materiais de marketing seguindo as diretrizes visuais da marca |
| **Ferramentas**      | `dalle3_tool` (geracao DALL-E 3), `flux_tool` (geracao Flux via Replicate), `image_edit_tool` (ajustes basicos), `brand_colors_tool` (paleta de cores), `canva_template_tool`, `storage_tool` (salvar no MinIO), `database_tool` |
| **Modelo IA**        | GPT-4o para prompts + DALL-E 3 / Flux para geracao               |
| **Memoria**          | Redis: prompts recentes. Qdrant: historico de imagens geradas, style guide da marca, prompts que geraram bons resultados |
| **Limite de Acoes**  | Maximo 50 imagens/dia (DALL-E 3: 30, Flux: 20)                   |
| **Aprovacao Humana** | Sempre para publicacao. Geracoes internas de teste: nao           |
| **Custo Estimado**   | ~$30-50/mes em tokens LLM + $50/mes em geracao de imagens         |

**Fluxo de Geracao de Imagem**:
1. Recebe briefing textual (descricao + contexto + plataforma)
2. Consulta brand guidelines (cores, fontes, estilo)
3. Gera prompt otimizado para o modelo de imagem
4. Gera 3-4 variacoes
5. Apresenta opcoes para aprovacao humana
6. Armazena versao aprovada no MinIO com metadados

---

### CREW 4: ANALISE

---

#### Agente 10: News Curator

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | News Curator                                                      |
| **Persona**          | Jornalista especializado em mercado audiovisual, tecnologia e marketing digital. Atento as tendencias e capaz de identificar o que e relevante para a Somos Produtora. |
| **Objetivo**         | Monitorar noticias do setor audiovisual, marketing digital e tecnologia, filtrando o que e relevante para a estrategia da empresa |
| **Ferramentas**      | `rss_reader_tool` (feeds RSS), `serpapi_news_tool` (busca de noticias), `social_trends_tool` (trending topics), `summary_tool` (resumir artigos), `notification_tool`, `database_tool` |
| **Modelo IA**        | GPT-4o-mini (primario) / Gemini Flash (fallback)                  |
| **Memoria**          | Redis: noticias das ultimas 24h. Qdrant: historico de noticias curadas, topicos de interesse |
| **Limite de Acoes**  | Monitoramento continuo. Maximo 500 artigos analisados/dia         |
| **Aprovacao Humana** | Nao. Curadoria automatica com digest diario enviado a equipe      |
| **Custo Estimado**   | ~$10-20/mes em tokens                                             |

**Fontes Monitoradas**:
- Portais: Meio & Mensagem, B9, AdNews, ProNews, Tecmundo
- RSS feeds de blogs de producao audiovisual
- Google News para termos-chave do setor
- Trending topics Twitter/X relacionados a audiovisual
- Lancamentos de equipamentos e softwares

---

#### Agente 11: Trend Analyzer

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Trend Analyzer                                                    |
| **Persona**          | Estrategista de mercado analitico e visionario. Identifica padroes em dados e transforma em insights acionaveis. Pensa em termos de oportunidades de negocio. |
| **Objetivo**         | Analisar tendencias de mercado, identificar oportunidades de negocio e recomendar acoes estrategicas para a Somos Produtora |
| **Ferramentas**      | `market_data_tool` (dados de mercado), `google_trends_tool` (Google Trends), `competitor_analysis_tool` (analise de concorrentes), `report_generator_tool`, `database_tool` |
| **Modelo IA**        | Gemini Pro (primario) / Claude 3.5 Sonnet (fallback)              |
| **Memoria**          | Redis: analises recentes. Qdrant: historico de tendencias, dados de mercado, relatorios anteriores |
| **Limite de Acoes**  | 5 analises profundas/dia, 50 analises rapidas/dia                 |
| **Aprovacao Humana** | Nao para analise. Sim para recomendacoes que envolvam investimento |
| **Custo Estimado**   | ~$25-40/mes em tokens                                             |

**Tipos de Analise**:
- Tendencias de busca por servicos audiovisuais na regiao
- Analise de concorrentes (precos, servicos, posicionamento)
- Sazonalidade de demanda (eventos corporativos, festas, etc.)
- Novas tecnologias e seu impacto no setor (IA generativa, realidade aumentada)
- Oportunidades de nicho identificadas por dados

---

### CREW 5: OPERACOES

---

#### Agente 12: Budget Calculator

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Budget Calculator                                                 |
| **Persona**          | Contador/orcamentista meticuloso com profundo conhecimento de custos de producao audiovisual. Preciso em calculos e transparente nas premissas. |
| **Objetivo**         | Auxiliar na criacao de orcamentos para projetos de producao, considerando custos de equipe, equipamento, locacao, pos-producao e margem de lucro |
| **Ferramentas**      | `cost_database_tool` (base de custos historicos), `equipment_catalog_tool` (catalogo de equipamentos e diarias), `supplier_pricing_tool` (precos de fornecedores), `margin_calculator_tool`, `pdf_generator_tool`, `database_tool` |
| **Modelo IA**        | GPT-4o (primario) / Claude 3.5 Sonnet (fallback)                 |
| **Memoria**          | Redis: orcamento atual em edicao. Qdrant: historico de todos os orcamentos, custos medios por categoria |
| **Limite de Acoes**  | Sem limite. Ferramenta de suporte interno                         |
| **Aprovacao Humana** | Sempre. Orcamento e sugestao, nunca enviado automaticamente       |
| **Custo Estimado**   | ~$15-25/mes em tokens                                             |

**Categorias de Custo Padrao**:

| Categoria           | Itens Tipicos                                             |
|---------------------|-----------------------------------------------------------|
| Pre-producao        | Roteiro, storyboard, planejamento, casting                |
| Equipe              | Diretor, camera, som, iluminacao, produtor, assistentes   |
| Equipamento         | Cameras, lentes, iluminacao, audio, drones, estabilizadores |
| Locacao             | Estudio, locacoes externas, autorizacoes                  |
| Transporte          | Deslocamento equipe, frete equipamento                    |
| Alimentacao         | Catering equipe, coffee break                             |
| Pos-producao        | Edicao, color grading, motion graphics, mixagem de audio  |
| Licenciamento       | Musica, imagens, fontes, softwares                        |
| Margem              | 20-40% sobre custo total                                  |

---

#### Agente 13: Contract Drafter

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Contract Drafter                                                  |
| **Persona**          | Assessor juridico especializado em contratos de prestacao de servicos audiovisuais. Linguagem formal e precisa. Conhece a legislacao brasileira de direitos autorais e contratos. |
| **Objetivo**         | Gerar minutas de contratos de prestacao de servicos, termos de uso de imagem, contratos de licenciamento e outros documentos juridicos padrao |
| **Ferramentas**      | `contract_template_tool` (templates base), `clause_library_tool` (biblioteca de clausulas), `legal_review_tool` (checklist juridico), `pdf_generator_tool`, `database_tool` |
| **Modelo IA**        | Claude 3.5 Sonnet (primario) / GPT-4o (fallback)                 |
| **Memoria**          | Redis: contrato atual em edicao. Qdrant: historico de todos os contratos, clausulas aprovadas por advogado, jurisprudencia relevante |
| **Limite de Acoes**  | Maximo 10 contratos/dia                                           |
| **Aprovacao Humana** | **OBRIGATORIA**. Todo contrato deve ser revisado por advogado antes de envio |
| **Custo Estimado**   | ~$20-35/mes em tokens                                             |

**Tipos de Contrato**:
1. Prestacao de servicos audiovisuais
2. Termo de uso de imagem e voz
3. Contrato de licenciamento de conteudo
4. NDA (Acordo de confidencialidade)
5. Contrato de parceria/permuta
6. Termos de servico para clientes

---

#### Agente 14: Performance Evaluator

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Performance Evaluator                                             |
| **Persona**          | Auditor de qualidade imparcial e data-driven. Monitora KPIs dos demais agentes e identifica anomalias e oportunidades de melhoria. |
| **Objetivo**         | Monitorar o desempenho de todos os agentes IA, identificar falhas, sugerir melhorias nos prompts e escalar problemas para a equipe humana |
| **Ferramentas**      | `agent_metrics_tool` (metricas de cada agente), `log_analyzer_tool` (analisar logs de execucao), `cost_tracker_tool` (custos de tokens), `alert_tool` (enviar alertas), `report_tool`, `database_tool` |
| **Modelo IA**        | GPT-4o (primario) / Gemini Pro (fallback)                         |
| **Memoria**          | Redis: metricas das ultimas 24h. Qdrant: historico completo de performance, benchmarks |
| **Limite de Acoes**  | Monitoramento continuo. Avaliacao profunda 1x/dia                 |
| **Aprovacao Humana** | Nao para monitoramento. Sim para desativar/reconfigurar agente    |
| **Custo Estimado**   | ~$15-25/mes em tokens                                             |

**KPIs Monitorados por Agente**:

| KPI                          | Descricao                                              | Alerta Se        |
|------------------------------|--------------------------------------------------------|------------------|
| Taxa de sucesso              | % de tarefas concluidas com sucesso                    | < 80%            |
| Tempo medio de execucao      | Tempo para concluir tarefa                             | > 2x da media    |
| Custo por tarefa             | Tokens consumidos por tarefa                           | > 3x da media    |
| Taxa de erro                 | % de tarefas que falharam                              | > 10%            |
| Qualidade de output          | Score de qualidade (avaliacao humana periodica)        | < 7/10           |
| Taxa de aprovacao humana     | % de outputs aprovados sem revisao                     | < 60%            |
| ROI estimado                 | Valor gerado vs custo de operacao                      | < 3x             |

---

#### Agente 15: Report Generator

| Atributo             | Detalhe                                                           |
|----------------------|-------------------------------------------------------------------|
| **Nome**             | Report Generator                                                  |
| **Persona**          | Analista de BI (Business Intelligence) preciso e visual. Transforma dados brutos em relatorios claros com graficos, tabelas e insights acionaveis. |
| **Objetivo**         | Gerar relatorios automatizados diarios, semanais e mensais sobre vendas, pipeline, financeiro, conteudo e performance dos agentes |
| **Ferramentas**      | `data_query_tool` (consultar PostgreSQL), `chart_generator_tool` (gerar graficos), `pdf_generator_tool`, `email_send_tool`, `dashboard_update_tool`, `database_tool` |
| **Modelo IA**        | GPT-4o (primario) / Gemini Pro (fallback)                         |
| **Memoria**          | Redis: dados do relatorio atual. Qdrant: historico de relatorios, templates de relatorio, benchmarks |
| **Limite de Acoes**  | Maximo 20 relatorios/dia                                          |
| **Aprovacao Humana** | Nao para relatorios automaticos periodicos. Sim para relatorios ad-hoc |
| **Custo Estimado**   | ~$20-30/mes em tokens                                             |

**Relatorios Automaticos**:

| Relatorio                  | Frequencia | Destinatario        | Conteudo Principal                       |
|----------------------------|------------|---------------------|------------------------------------------|
| Daily Pipeline             | Diario 8h  | Comercial           | Novos leads, deals movidos, tarefas      |
| Weekly Sales               | Seg 9h     | Diretoria           | Vendas, conversoes, pipeline, previsao   |
| Monthly Financial          | Dia 1, 10h | Diretoria/Financeiro| Receita, despesas, lucro, inadimplencia  |
| Agent Performance          | Diario 7h  | Equipe Tecnica      | KPIs de cada agente, custos, alertas     |
| Content Performance        | Seg 9h     | Marketing           | Engajamento, alcance, melhores posts     |
| Lead Source Analysis       | Mensal     | Comercial           | Origem dos leads, taxa por canal         |

---

## 4. Ciclo de Vida dos Agentes

### Fases do Ciclo de Vida

```
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│  DESIGN  │──▶│  BUILD   │──▶│  TEST    │──▶│  DEPLOY  │──▶│ MONITOR  │
│          │   │          │   │          │   │          │   │          │
│- Persona │   │- Prompt  │   │- Unit    │   │- Staging │   │- KPIs    │
│- Objetivo│   │- Tools   │   │- Integr. │   │- Producao│   │- Custos  │
│- Tools   │   │- Memory  │   │- Human   │   │- Gradual │   │- Qualid. │
│- Modelo  │   │- Limits  │   │  eval    │   │  rollout │   │- Alertas │
└──────────┘   └──────────┘   └──────────┘   └──────────┘   └────┬─────┘
                                                                  │
                                                                  ▼
                                                          ┌──────────────┐
                                                          │   IMPROVE    │
                                                          │              │
                                                          │- Refinar     │
                                                          │  prompts     │
                                                          │- Ajustar     │
                                                          │  parametros  │
                                                          │- Trocar      │
                                                          │  modelo      │
                                                          │- Substituir  │
                                                          │  se falhar   │
                                                          └──────────────┘
```

### 4.1 Design

- Definir persona, objetivo e escopo do agente
- Mapear ferramentas necessarias (minimas)
- Escolher modelo de IA (custo vs qualidade)
- Definir limites de autonomia e pontos de aprovacao humana
- Documentar no catalogo de agentes

### 4.2 Build

- Implementar agente usando CrewAI/LangChain
- Criar ferramentas customizadas (LangChain Tools)
- Configurar memoria (Redis + Qdrant)
- Escrever prompt de sistema detalhado
- Implementar fallbacks e tratamento de erros

### 4.3 Test

- **Testes unitarios**: Verificar cada ferramenta isoladamente
- **Testes de integracao**: Verificar fluxo completo do agente
- **Avaliacao humana**: Equipe avalia qualidade dos outputs em 20+ cenarios
- **Teste de custo**: Estimar consumo de tokens em uso real
- **Teste de seguranca**: Verificar que o agente respeita limites

### 4.4 Deploy

- Deploy em staging primeiro (1 semana)
- Deploy gradual em producao (10% -> 50% -> 100%)
- Monitoramento intensivo nas primeiras 72 horas
- Rollback automatico se taxa de erro > 20%

### 4.5 Monitor

- Metricas coletadas em tempo real pelo Performance Evaluator
- Dashboards no Grafana para cada agente
- Alertas automaticos para anomalias
- Review semanal de qualidade pela equipe

### 4.6 Improve / Replace

- **Melhoria continua**: Refinar prompts com base em feedback e metricas
- **A/B testing**: Testar variacoes de prompts e modelos
- **Substituicao de modelo**: Se novo modelo oferece melhor custo-beneficio
- **Aposentadoria**: Se agente nao gera ROI positivo apos 3 meses de operacao, avaliar descontinuacao

---

## 5. Estrategia de Memoria

### Memoria de Curto Prazo (Redis)

```python
# Exemplo: Armazenar contexto de conversa do SDR WhatsApp
import redis
import json

redis_client = redis.Redis(host='redis', port=6379, db=0)

def save_conversation_context(lead_id: str, messages: list):
    key = f"agent:sdr_whatsapp:conversation:{lead_id}"
    redis_client.setex(
        key,
        timedelta(hours=24),  # TTL: 24 horas
        json.dumps(messages)
    )

def get_conversation_context(lead_id: str) -> list:
    key = f"agent:sdr_whatsapp:conversation:{lead_id}"
    data = redis_client.get(key)
    return json.loads(data) if data else []
```

### Memoria de Longo Prazo (Qdrant)

```python
# Exemplo: Busca semantica de leads similares
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams, PointStruct

qdrant = QdrantClient(host="qdrant", port=6333)

# Criar colecao de leads
qdrant.create_collection(
    collection_name="leads",
    vectors_config=VectorParams(size=1536, distance=Distance.COSINE)
)

# Buscar leads similares ao ICP
def find_similar_leads(icp_embedding: list, limit: int = 10):
    results = qdrant.search(
        collection_name="leads",
        query_vector=icp_embedding,
        limit=limit,
        with_payload=True
    )
    return results
```

### Colecoes Qdrant

| Colecao            | Descricao                                        | Tamanho Estimado |
|--------------------|--------------------------------------------------|------------------|
| `leads`            | Embeddings de perfis de leads                    | 50K vetores      |
| `content`          | Embeddings de conteudo gerado                    | 10K vetores      |
| `contracts`        | Embeddings de clausulas contratuais              | 5K vetores       |
| `conversations`    | Embeddings de conversas com leads                | 100K vetores     |
| `knowledge_base`   | Documentos internos, FAQs, processos             | 5K vetores       |
| `brand_guidelines` | Identidade visual/verbal da marca                | 500 vetores      |

---

## 6. Estimativa de Custos com Tokens

### Precos de Referencia (fevereiro 2026)

| Modelo                    | Input (por 1K tokens) | Output (por 1K tokens) | Contexto |
|---------------------------|-----------------------|------------------------|----------|
| Claude 3.5 Sonnet         | $0.003                | $0.015                 | 200K     |
| GPT-4o                    | $0.005                | $0.015                 | 128K     |
| GPT-4o-mini               | $0.00015              | $0.0006                | 128K     |
| Gemini Pro                | $0.00125              | $0.005                 | 1M       |
| Gemini Flash              | $0.000075             | $0.0003                | 1M       |
| text-embedding-3-small    | $0.00002              | N/A                    | 8K       |
| DALL-E 3 (1024x1024)      | $0.040/imagem         | N/A                    | N/A      |
| Flux (via Replicate)       | $0.003/imagem         | N/A                    | N/A      |

### Estimativa Mensal por Agente

| Agente                  | Modelo Principal  | Tokens Input/mes | Tokens Output/mes | Custo LLM/mes | Outros Custos | Total/mes |
|-------------------------|-------------------|-------------------|--------------------|----------------|---------------|-----------|
| Hunter LinkedIn         | GPT-4o            | 5M                | 1M                 | $40             | -             | $40       |
| Hunter Google           | GPT-4o-mini       | 10M               | 2M                 | $2.70           | $50 SerpAPI   | $53       |
| Hunter Social           | GPT-4o-mini       | 8M                | 1.5M               | $2.10           | -             | $2        |
| SDR WhatsApp            | Claude 3.5 Sonnet | 8M                | 3M                 | $69             | -             | $69       |
| SDR LinkedIn            | Claude 3.5 Sonnet | 6M                | 2M                 | $48             | -             | $48       |
| SDR Email               | Claude 3.5 Sonnet | 5M                | 2M                 | $45             | $20 Resend    | $65       |
| Qualificador            | GPT-4o            | 4M                | 1M                 | $35             | -             | $35       |
| Content Writer          | Claude 3.5 Sonnet | 10M               | 5M                 | $105            | -             | $105      |
| Image Creator           | GPT-4o + DALL-E   | 3M                | 0.5M               | $22.50          | $60 imagens   | $83       |
| News Curator            | GPT-4o-mini       | 15M               | 1M                 | $2.85           | -             | $3        |
| Trend Analyzer          | Gemini Pro        | 8M                | 2M                 | $20             | -             | $20       |
| Budget Calculator       | GPT-4o            | 3M                | 1M                 | $30             | -             | $30       |
| Contract Drafter        | Claude 3.5 Sonnet | 4M                | 2M                 | $42             | -             | $42       |
| Performance Evaluator   | GPT-4o            | 5M                | 1M                 | $40             | -             | $40       |
| Report Generator        | GPT-4o            | 4M                | 1.5M               | $42.50          | -             | $43       |
| Embeddings              | embedding-3-small | 20M               | N/A                | $0.40           | -             | $0.40     |
| **TOTAL**               |                   |                   |                    | **~$547**       | **$130**      | **~$678** |

### Distribuicao de Custos por Provedor

| Provedor      | Custo Mensal Estimado | Percentual |
|---------------|----------------------|------------|
| Anthropic     | $200-350             | 37-52%     |
| OpenAI        | $150-300             | 22-44%     |
| Google AI     | $20-50               | 3-7%       |
| Replicate     | $40-60               | 6-9%       |
| **Total IA**  | **$400-700**         | **100%**   |

> **Nota**: Estes valores sao estimativas baseadas em uso moderado. O custo real depende do volume de leads, conteudo gerado e frequencia de analises. Recomenda-se monitorar diariamente e ajustar modelos (usar mini/flash para tarefas simples).

---

## 7. Monitoramento e KPIs

### Dashboard de Agentes IA (Grafana)

Metricas exibidas em tempo real:

- Tokens consumidos por agente (grafico de barras)
- Custo acumulado no mes (gauge com meta)
- Taxa de sucesso por agente (grafico de pizza)
- Tarefas na fila por agente (grafico de linha)
- Tempo medio de execucao (heatmap)
- Erros por tipo (tabela)

### Alertas Automaticos

| Situacao                                  | Acao                                          |
|-------------------------------------------|-----------------------------------------------|
| Agente com taxa de erro > 20%             | Pausa automatica + alerta para equipe tecnica |
| Custo diario de um agente > 2x da media  | Alerta para equipe tecnica                    |
| Fila de tarefas > 500 itens              | Escalar workers temporariamente               |
| Agente sem executar tarefas por 2+ horas | Verificar saude do worker + alerta            |
| Taxa de aprovacao humana < 50%           | Revisar prompt do agente                      |

---

## 8. Seguranca e Limites

### Limites por Agente

| Limite                        | Valor                                         |
|-------------------------------|-----------------------------------------------|
| Maximo de tokens/dia          | Configuravel por agente (padrao: 500K)        |
| Maximo de gastos/dia          | $50 por agente (hard limit)                   |
| Maximo de gastos/mes          | $200 por agente (hard limit)                  |
| Maximo de acoes automaticas   | Configuravel (ver catalogo acima)             |
| Timeout por tarefa            | 5 minutos (padrao), 15 minutos (maximo)       |
| Retries por tarefa            | 3 tentativas com backoff exponencial           |

### Acoes que SEMPRE Requerem Aprovacao Humana

1. Enviar proposta comercial a um cliente
2. Gerar contrato final (apos minuta)
3. Publicar conteudo em redes sociais
4. Desativar ou reconfigurar outro agente
5. Acessar dados financeiros sensiveis
6. Enviar email para mais de 50 destinatarios de uma vez
7. Excluir dados do banco de dados
8. Alterar configuracoes do sistema

### Sandboxing

Cada agente roda em um contexto isolado:
- Nao pode acessar ferramentas de outros agentes
- Nao pode alterar seu proprio prompt ou configuracao
- Nao pode acessar credenciais de APIs que nao usa
- Logs de todas as acoes sao imutaveis

---

*Documento mantido pela equipe de IA da Somos Produtora.*
