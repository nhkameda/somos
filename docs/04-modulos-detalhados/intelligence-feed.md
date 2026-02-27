# Intelligence Feed

## Proposito e Visao

O Intelligence Feed e o modulo de inteligencia de mercado da Somos Produtora, funcionando como um radar estrategico que opera 24 horas por dia, 7 dias por semana. Agentes de IA monitoram continuamente o ecossistema de tendencias e oportunidades nos setores de audiovisual, agencia 360, comunicacao visual e marketing digital, fornecendo insights acionaveis para a tomada de decisao estrategica da empresa.

O modulo escaneia uma ampla variedade de fontes: redes sociais (Instagram, TikTok, YouTube, LinkedIn), plataformas de streaming (Netflix, Amazon Prime, Disney+), ferramentas de tendencias (Google Trends, Pinterest Trends), portais de noticias do setor, blogs especializados, foruns de profissionais e relatorios de mercado. Cada sinal identificado e processado, categorizado e transformado em relatorios de oportunidades e alertas de tendencias.

A visao e posicionar a Somos Produtora sempre a frente do mercado, antecipando tendencias de formato (Reels, Shorts, carrosseis interativos), novas tecnologias de producao (IA generativa, volumes virtuais, drones FPV), mudancas no comportamento do consumidor e oportunidades de negocio antes dos concorrentes.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Diretor Geral | Consome relatorios estrategicos, toma decisoes baseadas em inteligencia | Acesso total, configuracao de prioridades |
| Diretor Comercial | Identifica oportunidades de vendas a partir de tendencias | Visualizacao, criacao de oportunidades no CRM |
| Diretor de Criacao | Acompanha tendencias visuais e de formato de conteudo | Visualizacao, configuracao de fontes criativas |
| Gerente de Estrategia | Analisa relatorios e traduz em acoes taticas | Visualizacao, edicao de relatorios, distribuicao |
| Social Media Manager | Identifica tendencias de conteudo para aplicar nos clientes | Visualizacao, exportacao para Content Studio |
| Agente IA Scanner | Monitora fontes e identifica sinais de tendencia | Leitura de fontes externas, classificacao |
| Agente IA Analista | Sintetiza sinais em relatorios e oportunidades | Analise, geracao de relatorios |
| Agente IA Alertador | Detecta sinais de alta urgencia e emite alertas | Monitoramento, envio de notificacoes |

## Funcionalidades Principais

### Monitoramento Continuo de Fontes
- Escaneamento automatico de fontes configuradas em intervalos definidos
- Fontes monitoradas:
  - **Redes Sociais**: Instagram (trending reels, hashtags), TikTok (trending sounds, formats), YouTube (trending videos, temas), LinkedIn (artigos de thought leaders)
  - **Streaming**: Netflix (novos lancamentos, generos em alta), Amazon Prime, Disney+ (tendencias de producao)
  - **Tendencias**: Google Trends (buscas em alta), Pinterest Trends (tendencias visuais), Semrush Trends
  - **Noticias do Setor**: Meio & Mensagem, B9, Adnews, ProNews, Tela Viva, Canaltech
  - **Internacional**: Adweek, Campaign, The Drum, Variety, Deadline
  - **Foruns e Comunidades**: Reddit (r/videography, r/filmmaking), grupos Facebook, comunidades Discord
- Extracao de dados estruturados de cada fonte (titulo, resumo, data, metricas de popularidade)
- Deduplicacao automatica de noticias similares de fontes diferentes

### Classificacao e Pontuacao de Sinais
- Cada sinal recebe um score de relevancia (0-100) baseado em:
  - **Relevancia para o setor** (0-30): Alinhamento com audiovisual, agencia 360, comunicacao visual
  - **Urgencia temporal** (0-20): Quao recente e quao rapido esta crescendo
  - **Potencial de oportunidade** (0-25): Possibilidade de gerar negocios para a Somos
  - **Originalidade** (0-15): Se e algo genuinamente novo ou repetitivo
  - **Impacto potencial** (0-10): Magnitude da tendencia no mercado
- Categorizacao automatica por tema:
  - Tendencias de formato de conteudo
  - Novas tecnologias de producao
  - Mudancas no comportamento do consumidor
  - Movimentacoes de concorrentes
  - Oportunidades de mercado
  - Regulamentacoes e politicas

### Geracao de Relatorios de Oportunidades
- Relatorios automaticos com periodicidade configuravel (diario, semanal, mensal)
- Estrutura do relatorio:
  - Resumo executivo (3-5 pontos principais)
  - Tendencias identificadas com evidencias e fontes
  - Oportunidades de negocio sugeridas
  - Analise competitiva (o que concorrentes estao fazendo)
  - Recomendacoes de acao com prioridade
- Relatorios personalizados por area (Comercial, Criacao, Producao)
- Versao executiva resumida para o Diretor Geral
- Exportacao em PDF e envio automatico por email

### Alertas de Tendencia em Tempo Real
- Alertas instantaneos quando um sinal atinge score acima de 80
- Alertas de emergencia quando detectada movimentacao significativa de concorrente
- Alertas de oportunidade temporal (eventos, datas, sazonalidade)
- Canais de alerta: push notification (app), email, Slack/Teams, dashboard
- Configuracao de preferencias de alerta por usuario (frequencia, temas, score minimo)

### Painel de Descobertas
- Dashboard interativo com os sinais mais recentes e relevantes
- Mapa de calor de tendencias por categoria e periodo
- Timeline visual mostrando evolucao de tendencias ao longo do tempo
- Comparativo "Somos vs Mercado": o que a empresa ja esta fazendo vs tendencias do mercado
- Bookmark de sinais para acompanhamento futuro

## Fluxo de Dados

```
[Fontes Externas]
+--------+--------+--------+--------+--------+
| Redes  |Streaming| Google | Portais| Foruns |
| Sociais|         | Trends | Noticias|       |
+--------+--------+--------+--------+--------+
         |         |        |        |
         +----+----+--------+--------+
              |
              v
    [Agente IA Scanner]
    (Extracao e Deduplicacao)
              |
              v
    [Classificacao e Scoring]
    (Relevancia, Urgencia, Potencial)
              |
              v
    [Banco de Sinais de Mercado]
              |
    +---------+---------+
    |         |         |
    v         v         v
[Score>80] [Relatorio] [Painel de
 [Alerta    Periodico]  Descobertas]
  Imediato]     |
    |           v
    |     [Agente IA Analista]
    |     (Sintese e Recomendacoes)
    |           |
    |           v
    |     [Relatorio de Oportunidades]
    |           |
    +-----------+
                |
                v
    [Distribuicao por Area]
    +--------+--------+--------+
    |        |        |        |
    v        v        v        v
[Diretor [Comercial][Criacao] [Social
 Geral]                       Media]
```

## Entidades e Modelo de Dados

### TrendReport (Relatorio de Tendencias)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| title | String | Titulo do relatorio |
| type | Enum | DIARIO, SEMANAL, MENSAL, ESPECIAL |
| target_audience | Enum | DIRETORIA, COMERCIAL, CRIACAO, PRODUCAO, TODOS |
| executive_summary | Text | Resumo executivo |
| body | Text | Corpo completo do relatorio (markdown) |
| key_findings | JSON[] | Principais descobertas estruturadas |
| recommendations | JSON[] | Recomendacoes de acao com prioridade |
| signals_count | Integer | Numero de sinais analisados |
| period_start | Date | Inicio do periodo analisado |
| period_end | Date | Fim do periodo analisado |
| generated_by | UUID (FK) | Agente IA que gerou |
| reviewed_by | UUID (FK) | Humano que revisou (se aplicavel) |
| status | Enum | GERADO, REVISADO, DISTRIBUIDO |
| created_at | DateTime | Data de geracao |

### Opportunity (Oportunidade)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| trend_report_id | UUID (FK) | Relatorio de origem |
| title | String | Titulo da oportunidade |
| description | Text | Descricao detalhada |
| category | Enum | NOVO_SERVICO, NOVO_CLIENTE, TENDENCIA_CONTEUDO, TECNOLOGIA, PARCERIA |
| priority | Enum | BAIXA, MEDIA, ALTA, URGENTE |
| potential_revenue | Decimal | Estimativa de receita potencial |
| time_sensitivity | Enum | IMEDIATO, CURTO_PRAZO, MEDIO_PRAZO, LONGO_PRAZO |
| status | Enum | IDENTIFICADA, EM_AVALIACAO, APROVADA, EM_EXECUCAO, DESCARTADA |
| assigned_to | UUID (FK) | Responsavel por avaliar |
| source_signals | UUID[] | Sinais que geraram esta oportunidade |
| action_items | JSON[] | Acoes sugeridas |
| created_at | DateTime | Data de identificacao |

### Source (Fonte de Dados)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome da fonte |
| type | Enum | RSS, API, SCRAPING, MANUAL |
| url | String | URL da fonte |
| category | Enum | REDE_SOCIAL, STREAMING, TENDENCIA, NOTICIA, FORUM, INTERNACIONAL |
| scan_interval_minutes | Integer | Intervalo de escaneamento em minutos |
| is_active | Boolean | Se esta ativa |
| reliability_score | Integer | Score de confiabilidade da fonte (0-100) |
| last_scanned_at | DateTime | Ultima vez que foi escaneada |
| total_signals_generated | Integer | Total de sinais gerados desta fonte |

### Alert (Alerta)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| signal_id | UUID (FK) | Sinal que gerou o alerta |
| type | Enum | TENDENCIA, OPORTUNIDADE, CONCORRENTE, EMERGENCIA |
| severity | Enum | INFO, ATENCAO, URGENTE, CRITICO |
| title | String | Titulo do alerta |
| message | Text | Mensagem detalhada |
| recipients | UUID[] | Usuarios que devem receber |
| channels | Enum[] | PUSH, EMAIL, SLACK, DASHBOARD |
| is_read | Boolean | Se foi lido |
| read_at | DateTime | Data/hora da leitura |
| created_at | DateTime | Data de criacao |

### MarketSignal (Sinal de Mercado)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| source_id | UUID (FK) | Fonte de origem |
| title | String | Titulo do sinal |
| summary | Text | Resumo gerado pela IA |
| original_url | String | URL do conteudo original |
| original_content | Text | Conteudo extraido |
| category | Enum | FORMATO, TECNOLOGIA, COMPORTAMENTO, CONCORRENCIA, MERCADO, REGULACAO |
| relevance_score | Integer | Score total de relevancia (0-100) |
| sector_score | Integer | Score de relevancia para o setor (0-30) |
| urgency_score | Integer | Score de urgencia temporal (0-20) |
| opportunity_score | Integer | Score de potencial de oportunidade (0-25) |
| originality_score | Integer | Score de originalidade (0-15) |
| impact_score | Integer | Score de impacto potencial (0-10) |
| tags | String[] | Tags descritivas |
| is_duplicate | Boolean | Se e duplicata de outro sinal |
| duplicate_of | UUID (FK) | Sinal original se for duplicata |
| bookmarked_by | UUID[] | Usuarios que salvaram |
| created_at | DateTime | Data da captura |

## Interface e UX

### Dashboard Principal (Intelligence Hub)
- Feed tipo timeline com sinais mais recentes e relevantes
- Filtros rapidos: por categoria, score minimo, fonte, periodo
- Cards de sinal com titulo, resumo, score (badge colorido), fonte e data
- Acao rapida: bookmark, criar oportunidade, compartilhar
- Widget lateral com alertas nao lidos

### Mapa de Tendencias
- Visualizacao em bubble chart com tendencias por categoria
- Tamanho do bubble proporcional ao score de relevancia
- Cor do bubble indica urgencia (verde, amarelo, vermelho)
- Clique no bubble para expandir detalhes e sinais relacionados
- Timeline horizontal mostrando como a tendencia evoluiu ao longo do tempo

### Painel de Oportunidades
- Kanban board com colunas: Identificada > Em Avaliacao > Aprovada > Em Execucao
- Cards de oportunidade com titulo, categoria, prioridade e receita potencial
- Drag-and-drop para mover entre etapas
- Filtros por categoria, prioridade e responsavel

### Central de Relatorios
- Lista de relatorios gerados com filtros por tipo, periodo e audiencia
- Preview do relatorio com opcao de edicao antes da distribuicao
- Botao de distribuicao com selecao de destinatarios e canais
- Historico de distribuicao e confirmacoes de leitura

### Configuracao de Fontes
- Lista de fontes ativas e inativas com metricas de performance
- Formulario para adicionar nova fonte (URL, tipo, intervalo, categoria)
- Teste de conectividade em tempo real
- Metricas por fonte: sinais gerados, score medio, confiabilidade

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| CRM | Escrita | Cria oportunidades de venda a partir de tendencias |
| Content Studio | Escrita | Alimenta curadoria com tendencias de conteudo |
| Analytics/Reports | Escrita | Envia metricas de inteligencia para dashboards executivos |
| Google Trends API | Leitura | Dados de tendencias de busca |
| YouTube Data API | Leitura | Videos trending e tendencias de formato |
| Instagram Graph API | Leitura | Hashtags trending, formatos populares |
| Pinterest API | Leitura | Tendencias visuais e de estilo |
| RSS Feeds | Leitura | Noticias de portais especializados |
| Slack/Teams | Escrita | Envio de alertas e relatorios |

## Agentes de IA

### Agente Scanner de Fontes
- **Funcao**: Monitora todas as fontes configuradas e extrai sinais brutos
- **Modelo**: Gemini Pro (processamento de grande volume de dados)
- **Frequencia**: Variavel por fonte (15 min a 4 horas)
- **Processo**: Acessa fonte > Extrai conteudo novo > Deduplicacao > Armazena sinal bruto
- **Volume**: Processa centenas de artigos/posts por dia

### Agente Classificador de Sinais
- **Funcao**: Analisa sinais brutos e atribui scores de relevancia multicritrio
- **Modelo**: Claude Sonnet (classificacao rapida e precisa)
- **Entrada**: Sinal bruto com titulo, resumo e conteudo original
- **Saida**: Scores por criterio, categoria, tags e indicacao de duplicata
- **Frequencia**: Em tempo real, para cada novo sinal

### Agente Analista de Tendencias
- **Funcao**: Sintetiza multiplos sinais em relatorios de tendencias e oportunidades
- **Modelo**: Claude Opus (analise profunda e sintese estrategica)
- **Entrada**: Conjunto de sinais do periodo + contexto do mercado + historico
- **Saida**: Relatorio estruturado com resumo executivo, analises e recomendacoes
- **Frequencia**: Diario (resumo), semanal (completo), mensal (estrategico)

### Agente Alertador
- **Funcao**: Monitora sinais em tempo real e emite alertas quando criterios sao atendidos
- **Modelo**: Regras + Claude Haiku (classificacao rapida de urgencia)
- **Criterios de Alerta**: Score acima de 80, mencao a concorrentes chave, mudancas regulatorias
- **Saida**: Alerta formatado com contexto e acao sugerida
- **Canais**: Push, email, Slack (conforme configuracao do usuario)

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Analise de Texto (volume) | Gemini Pro API | Processamento eficiente de grandes volumes |
| Sintese e Relatorios | Claude API (Anthropic) | Qualidade superior em analise e redacao |
| Scraping Web | Puppeteer + Cheerio | Extracao de conteudo de paginas dinamicas |
| RSS Parsing | rss-parser (Node.js) | Leitura de feeds RSS/Atom |
| Fila de Processamento | BullMQ + Redis | Escaneamento periodico e processamento assincrono |
| Busca de Sinais | Elasticsearch | Busca textual rapida em grande volume de sinais |
| Visualizacoes | D3.js / Recharts | Graficos customizados (bubble chart, timeline) |
| Notificacoes | Firebase Cloud Messaging + Nodemailer | Push e email |
| Agendamento | node-cron | Cron jobs para escaneamento periodico |
| Armazenamento | PostgreSQL (Supabase) | Dados estruturados com busca full-text |
| Cache | Redis | Cache de resultados de APIs externas |

## Dependencias

### Dependencias de Modulo
- **CRM**: Recomendada - oportunidades identificadas podem virar leads/oportunidades de venda
- **Content Studio**: Recomendada - tendencias de conteudo alimentam a curadoria
- **Analytics/Reports**: Recomendada - metricas de inteligencia no dashboard executivo

### Dependencias Tecnicas
- APIs de IA configuradas (Gemini Pro, Claude)
- Credenciais de APIs das plataformas monitoradas (Google Trends, YouTube, etc.)
- Elasticsearch configurado para busca de sinais (opcional, pode usar pg full-text search)
- Redis para filas e cache
- Fontes iniciais configuradas com URLs e categorias

### Dependencias Externas
- Acesso as APIs das plataformas monitoradas
- Conformidade com termos de uso de cada plataforma para scraping
- Quotas de API suficientes para volume de escaneamento

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Sinais capturados por dia | > 100 | Contagem diaria |
| Score medio de relevancia dos sinais | > 40 | Media diaria |
| Oportunidades identificadas por mes | > 10 | Contagem mensal |
| Taxa de conversao de oportunidade em negocio | > 15% | Oportunidades convertidas / total identificadas |
| Tempo de deteccao de tendencia emergente | < 4 horas | Primeira publicacao da tendencia vs captura |
| Relatorios gerados no prazo | 100% | Relatorios entregues / relatorios programados |
| Taxa de leitura dos relatorios | > 80% | Relatorios lidos / relatorios distribuidos |
| Precisao das previsoes de tendencia | > 60% | Tendencias que se confirmaram / total previstas |
| Satisfacao dos stakeholders | > 4.0/5.0 | Pesquisa trimestral |
| Receita gerada a partir de oportunidades | Crescimento de 20% a/a | Soma de contratos originados de oportunidades |
