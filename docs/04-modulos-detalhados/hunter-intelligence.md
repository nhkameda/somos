# Hunter Intelligence

## Proposito e Visao

O modulo Hunter Intelligence e o motor de prospeccao ativa da Somos Produtora. Ele utiliza agentes de inteligencia artificial que operam de forma autonoma e continua, varrendo multiplas plataformas digitais em busca de leads qualificados para servicos de producao audiovisual, comunicacao 360 e agencia digital.

A visao central e transformar a prospeccao comercial de um processo manual e limitado em uma operacao escalavel, inteligente e ininterrupta. Cada agente hunter e especializado em um segmento de mercado e atua como um pesquisador digital incansavel, identificando sinais de intencao de compra, necessidades de comunicacao e oportunidades de negocio em tempo real.

O diferencial esta na capacidade de correlacionar dados de multiplas fontes para gerar um perfil completo do lead, incluindo porte da empresa, presenca digital atual, lacunas de comunicacao e potencial de investimento em servicos audiovisuais.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Diretor Comercial | Estrategista | Define segmentos prioritarios, aprova configuracoes de hunters, analisa resultados macro |
| Gerente de Novos Negocios | Supervisor | Monitora performance dos agentes, ajusta parametros de qualificacao, valida leads |
| Agente Hunter (IA) | Executor | Varre plataformas, coleta dados, qualifica leads, gera relatorios |
| Analista de Dados | Suporte | Configura fontes de dados, otimiza queries, monitora taxas de conversao |
| Equipe de SDR | Consumidor | Recebe leads qualificados para iniciar abordagem comercial |

## Funcionalidades Principais

### Configuracao de Agentes Hunter

- Criacao de multiplos agentes hunter, cada um especializado em um segmento de mercado
- Segmentos predefinidos incluem: farmaceutico, automotivo, moda, influenciadores digitais, educacao, tecnologia, saude, alimentos e bebidas, imobiliario, eventos corporativos
- Configuracao de palavras-chave, filtros geograficos, porte de empresa e indicadores de intencao
- Agendamento de frequencia de varredura (horaria, diaria, semanal)
- Definicao de limites de leads por ciclo para evitar sobrecarga da equipe comercial

### Varredura Multiplataforma

- **LinkedIn**: Busca por decisores (CMOs, diretores de marketing, gerentes de comunicacao), empresas que postaram vagas de marketing, empresas sem presenca audiovisual forte
- **Google**: Monitoramento de empresas buscando servicos de producao de video, agencias de comunicacao, produtoras audiovisuais na regiao
- **Instagram**: Identificacao de marcas com conteudo visual fraco, empresas lancando produtos sem video, influenciadores buscando producao profissional
- **Facebook**: Empresas com paginas desatualizadas, anuncios sem video, grupos de discussao sobre marketing digital
- **YouTube**: Canais corporativos inativos, empresas sem presenca em video, analise de concorrentes dos leads
- **Pinterest**: Marcas de lifestyle, moda e decoracao sem conteudo em video, tendencias visuais emergentes

### Qualificacao Automatica de Leads

- Sistema de pontuacao (Lead Score) de 0 a 100 baseado em multiplos criterios
- Criterios de pontuacao incluem: porte da empresa, presenca digital atual, sinais de intencao, orcamento estimado, acessibilidade do decisor, fit com servicos da Somos
- Classificacao automatica em categorias: Quente (80-100), Morno (50-79), Frio (20-49), Descartado (0-19)
- Enriquecimento automatico de dados com informacoes complementares de outras fontes

### Painel de Monitoramento

- Dashboard em tempo real mostrando leads descobertos por agente, por segmento e por plataforma
- Graficos de tendencia de volume de leads ao longo do tempo
- Mapa de calor geografico dos leads identificados
- Alertas automaticos quando leads de alta pontuacao sao descobertos

## Fluxo de Dados

```
[Configuracao do Hunter]
        |
        v
[Agente IA inicia varredura]
        |
        v
[Coleta de dados brutos das plataformas]
        |
        v
[Deduplicacao e normalizacao de dados]
        |
        v
[Analise e qualificacao por IA (Claude 3.5 Sonnet)]
        |
        v
[Calculo do Lead Score]
        |
        v
[Enriquecimento de dados complementares]
        |
        v
[Armazenamento no banco de dados de leads]
        |
        v
[Notificacao para equipe comercial (leads quentes)]
        |
        v
[Encaminhamento para modulo SDR Automation]
```

O fluxo opera em ciclos continuos. Cada agente executa sua varredura conforme o agendamento configurado, e os dados coletados passam por um pipeline de processamento que inclui deduplicacao (evitando leads duplicados entre agentes e plataformas), normalizacao (padronizacao de campos como telefone, CNPJ, razao social) e enriquecimento (busca de dados complementares em fontes secundarias).

## Entidades e Modelo de Dados

### Lead

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico do lead |
| nome_contato | String | Nome do contato principal |
| cargo | String | Cargo do contato na empresa |
| empresa | String | Nome da empresa |
| cnpj | String | CNPJ da empresa (quando disponivel) |
| email | String | Email do contato |
| telefone | String | Telefone do contato |
| linkedin_url | String | Perfil do LinkedIn |
| instagram_url | String | Perfil do Instagram |
| website | String | Site da empresa |
| segmento | FK(Segment) | Segmento de mercado |
| lead_score | Integer | Pontuacao de qualificacao (0-100) |
| classificacao | Enum | Quente, Morno, Frio, Descartado |
| fonte_primaria | FK(SearchSource) | Plataforma onde foi encontrado |
| hunter_agent_id | FK(HunterAgent) | Agente que descobriu o lead |
| dados_brutos | JSON | Dados coletados antes do processamento |
| dados_enriquecidos | JSON | Dados complementares pos-enriquecimento |
| status | Enum | Novo, Em Analise, Qualificado, Encaminhado, Descartado |
| criado_em | DateTime | Data de descoberta |
| atualizado_em | DateTime | Ultima atualizacao |

### HunterAgent

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico do agente |
| nome | String | Nome descritivo do agente |
| segmento_id | FK(Segment) | Segmento de atuacao |
| plataformas | Array[String] | Plataformas que o agente varre |
| palavras_chave | Array[String] | Termos de busca configurados |
| filtros_geo | JSON | Filtros geograficos (cidade, estado, pais) |
| filtros_porte | JSON | Filtros de porte da empresa |
| frequencia | Enum | Horaria, Diaria, Semanal |
| limite_leads_ciclo | Integer | Maximo de leads por ciclo |
| ativo | Boolean | Status de ativacao do agente |
| modelo_ia | String | Modelo de IA utilizado |
| ultima_execucao | DateTime | Data da ultima varredura |
| total_leads_gerados | Integer | Contador acumulado de leads |

### SearchSource

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome da plataforma |
| tipo | Enum | LinkedIn, Google, Instagram, Facebook, YouTube, Pinterest |
| api_config | JSON | Configuracoes de acesso a API |
| rate_limit | Integer | Limite de requisicoes por minuto |
| ativo | Boolean | Status da fonte |

### Segment

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do segmento |
| descricao | Text | Descricao detalhada do segmento |
| palavras_chave_padrao | Array[String] | Palavras-chave sugeridas |
| criterios_qualificacao | JSON | Regras especificas de pontuacao |

### LeadScore

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| lead_id | FK(Lead) | Lead avaliado |
| pontuacao_total | Integer | Score final (0-100) |
| criterios | JSON | Detalhamento dos criterios e pesos |
| justificativa | Text | Explicacao gerada pela IA |
| calculado_em | DateTime | Data do calculo |

## Interface e UX

### Tela Principal - Painel de Hunters

- Visao geral com cards de cada agente hunter ativo, mostrando nome, segmento, status (ativo/pausado), ultimo ciclo executado e total de leads gerados
- Indicadores visuais de performance com cores (verde para alta performance, amarelo para media, vermelho para baixa)
- Botoes rapidos para pausar, retomar ou configurar cada agente

### Tela de Leads Descobertos

- Tabela paginada e filtravel com todos os leads coletados
- Filtros por segmento, plataforma de origem, faixa de score, data de descoberta, classificacao
- Visualizacao rapida (hover) com resumo do lead e justificativa do score
- Acoes em lote: encaminhar para SDR, descartar, reclassificar

### Tela de Configuracao de Agente

- Formulario passo a passo para criacao/edicao de agentes
- Selecao de segmento com sugestoes de palavras-chave
- Configuracao visual de plataformas com checkboxes
- Preview de busca para testar configuracoes antes de ativar
- Editor de regras de pontuacao com interface drag-and-drop

### Dashboard Analitico

- Graficos de leads por dia/semana/mes
- Funil de qualificacao (total descoberto, qualificado, encaminhado, convertido)
- Ranking de agentes por performance
- Mapa geografico interativo dos leads

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| SerpAPI | Buscas no Google, Google Maps, Google News | API REST |
| LinkedIn API | Busca de perfis, empresas, vagas | OAuth 2.0 + API REST |
| Google Custom Search | Buscas web personalizadas por segmento | API REST com chave |
| Meta Graph API | Busca em Instagram e Facebook | OAuth 2.0 + API REST |
| YouTube Data API | Busca de canais e videos corporativos | API REST com chave |
| Pinterest API | Busca de pins e boards de marcas | OAuth 2.0 + API REST |
| Modulo SDR Automation | Encaminhamento de leads qualificados | Interno (evento/fila) |
| Modulo CRM Pipeline | Criacao de deals a partir de leads | Interno (evento/fila) |

## Agentes de IA

### Agente de Varredura e Coleta

- **Modelo**: GPT-4o
- **Funcao**: Executar buscas estruturadas nas plataformas, interpretar resultados, extrair dados relevantes de paginas e perfis
- **Comportamento**: Opera em ciclos conforme agendamento, respeita rate limits, gera logs detalhados de cada varredura

### Agente de Analise e Qualificacao

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Analisar o perfil completo do lead, avaliar fit com servicos da Somos Produtora, gerar pontuacao justificada, redigir resumo executivo
- **Comportamento**: Recebe dados brutos coletados, aplica criterios de qualificacao do segmento, gera output estruturado com score e justificativa

### Agente de Enriquecimento

- **Modelo**: GPT-4o
- **Funcao**: Buscar dados complementares em fontes secundarias, cruzar informacoes entre plataformas, validar dados coletados
- **Comportamento**: Acionado apos coleta inicial, busca CNPJ na Receita Federal, verifica redes sociais adicionais, estima porte e faturamento

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Backend | Node.js com TypeScript | Alta performance para operacoes assincronas de scraping |
| Fila de Tarefas | BullMQ com Redis | Gerenciamento robusto de jobs agendados e retry |
| Banco de Dados | PostgreSQL | Suporte a JSON, queries complexas, integridade relacional |
| Cache | Redis | Cache de resultados de busca para evitar requisicoes duplicadas |
| ORM | Prisma | Type-safety, migrations, boa integracao com TypeScript |
| Orquestrador de IA | LangChain | Encadeamento de chamadas a LLMs, gestao de prompts |
| Monitoramento | Grafana + Prometheus | Dashboards de performance dos agentes e metricas de sistema |

## Dependencias

- **Modulo SDR Automation**: Destino dos leads qualificados para abordagem automatica
- **Modulo CRM Pipeline**: Destino final dos leads que avancam no funil comercial
- **Infraestrutura de Filas**: Redis/BullMQ para agendamento e execucao dos agentes
- **Servicos de API Externos**: Credenciais e limites de uso das APIs de cada plataforma
- **Modulo de Autenticacao**: Controle de acesso para configuracao de agentes e visualizacao de leads

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Leads Gerados por Dia | Total de leads descobertos por todos os agentes em 24h | >= 50 leads/dia |
| Taxa de Qualificacao | Porcentagem de leads que recebem score >= 50 | >= 30% |
| Precisao do Score | Correlacao entre score atribuido e conversao real | >= 75% |
| Cobertura de Plataformas | Numero de plataformas ativamente varridas | >= 5 |
| Tempo de Processamento | Tempo medio entre descoberta e qualificacao do lead | <= 15 minutos |
| Taxa de Duplicatas | Porcentagem de leads duplicados detectados e eliminados | <= 5% |
| Custo por Lead | Custo operacional (APIs + infra) por lead qualificado | <= R$ 2,50 |
| Leads Encaminhados para SDR | Volume de leads quentes enviados ao modulo SDR por semana | >= 100/semana |
| Uptime dos Agentes | Disponibilidade dos agentes hunter | >= 99,5% |
