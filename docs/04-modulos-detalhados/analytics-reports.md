# Analytics e Reports

## Proposito e Visao

O modulo de Analytics e Reports e o centro de inteligencia operacional da Somos Produtora, consolidando dados de todos os outros modulos em dashboards executivos e relatorios acionaveis. Ele fornece visibilidade completa sobre a performance da empresa por meio de KPIs (Indicadores-Chave de Performance), graficos visuais interativos e relatorios automatizados que permitem a tomada de decisao baseada em dados.

O modulo apresenta metricas de todas as areas: leads gerados e taxas de conversao (CRM), receita e fluxo de caixa (Financeiro), status de projetos (Producao), performance de conteudo (Content Studio e Social Scheduler), saude dos agentes de IA (RH), tendencias de mercado (Intelligence Feed) e eficiencia operacional. Dashboards sao atualizados em tempo real via WebSocket, garantindo que gestores sempre tenham a informacao mais recente.

A visao e criar um painel de controle completo da empresa, onde qualquer gestor consiga entender em segundos a saude do negocio, identificar gargalos e tomar decisoes fundamentadas. Relatorios automatizados semanais e mensais eliminam o trabalho manual de compilacao de dados e garantem que as informacoes certas cheguem as pessoas certas no momento certo.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Diretor Geral | Consume dashboards executivos, analisa KPIs estrategicos | Acesso total a todos os dashboards |
| Diretor Comercial | Acompanha metricas de vendas, conversao e pipeline | Dashboard comercial, relatorios de vendas |
| Diretor Financeiro | Monitora receita, despesas, fluxo de caixa, lucratividade | Dashboard financeiro, relatorios financeiros |
| Diretor de Criacao | Acompanha metricas de producao e conteudo | Dashboard de producao e conteudo |
| Gerente de Projetos | Visualiza status de projetos e alocacao de recursos | Dashboard de projetos |
| Gerente de Conta | Acessa relatorios especificos de seus clientes | Relatorios por cliente |
| Agente IA Analista | Gera insights automaticos a partir dos dados | Leitura de todos os dados, geracao de insights |
| Agente IA Reporter | Compila e distribui relatorios automatizados | Compilacao, formatacao, distribuicao |

## Funcionalidades Principais

### Dashboards Executivos

#### Dashboard Geral (CEO View)
- Visao panoramica com KPIs principais em cards de destaque
- Receita mensal vs meta, margem de lucro, projetos ativos
- Grafico de receita ao longo dos ultimos 12 meses (linha)
- Pipeline de vendas resumido (funil)
- Saude dos agentes de IA (semaforo)
- Alertas criticos que requerem atencao imediata

#### Dashboard Comercial
- Leads gerados no periodo (total e por fonte)
- Taxa de conversao por etapa do funil
- Ticket medio de vendas
- Orcamentos pendentes e taxa de aprovacao
- Ranking de vendedores/gerentes de conta
- Pipeline de vendas com valor projetado

#### Dashboard Financeiro
- Receita realizada vs prevista
- Despesas por categoria
- Fluxo de caixa projetado (30, 60, 90 dias)
- Contas a receber e inadimplencia
- Lucratividade por projeto e por cliente
- DRE simplificado mensal

#### Dashboard de Producao
- Projetos por status (nao iniciado, em andamento, concluido, atrasado)
- Timeline de projetos (Gantt simplificado)
- Utilizacao de equipe (% de alocacao)
- Projetos atrasados com dias de atraso
- Equipamentos em uso vs disponiveis

#### Dashboard de Conteudo e Social
- Posts publicados no periodo (por plataforma)
- Engajamento medio e evolucao
- Melhores posts do periodo
- Conteudos em fila de producao
- Crescimento de seguidores dos clientes

#### Dashboard de Agentes de IA
- Score de saude de cada agente (gauge chart)
- Taxa de sucesso geral e por agente
- Erros recentes classificados por severidade
- Agentes em observacao ou desativados
- Versoes e evolucao de performance

### Graficos e Visualizacoes

#### Tipos de Graficos Suportados
- **Pizza (Pie Chart)**: Distribuicao de receita por tipo de servico, leads por fonte
- **Barras (Bar Chart)**: Comparativos mensais, ranking de clientes, despesas por categoria
- **Linhas (Line Chart)**: Evolucao temporal de receita, engajamento, leads
- **Area (Area Chart)**: Fluxo de caixa ao longo do tempo, crescimento de seguidores
- **Timeline (Gantt)**: Cronograma de projetos, marcos e entregas
- **Funil (Funnel Chart)**: Pipeline de vendas, funil de conversao
- **Gauge (Medidor)**: Score de saude de agentes, metas atingidas
- **Mapa de Calor (Heatmap)**: Horarios de melhor engajamento, carga de trabalho por dia
- **Bubble Chart**: Oportunidades por potencial e urgencia
- **KPI Cards**: Valores destaque com variacao percentual e indicador de tendencia

#### Interatividade
- Zoom in/out em graficos temporais
- Drill-down: clicar em um ponto para ver detalhes (ex: clicar na barra de um mes para ver por semana)
- Tooltips com informacoes detalhadas ao passar o mouse
- Filtros globais que afetam todos os graficos do dashboard simultaneamente
- Comparacao de periodos (este mes vs anterior, este trimestre vs anterior)

### Construtor de Relatorios Customizados
- Interface drag-and-drop para montar relatorios personalizados
- Selecao de fontes de dados (modulos) e metricas especificas
- Escolha de tipo de grafico para cada metrica
- Filtros e agrupamentos configurados pelo usuario
- Salvar como template reutilizavel
- Compartilhamento com outros usuarios

### Relatorios Automatizados
- **Relatorio Semanal**: Resumo da semana com KPIs principais, destaques e alertas
  - Distribuido toda segunda-feira as 8h para gestores
- **Relatorio Mensal**: Analise completa do mes com comparativos, tendencias e recomendacoes
  - Distribuido no primeiro dia util do mes seguinte
- **Relatorio de Cliente**: Performance das redes sociais, status de projetos e entregas
  - Distribuido conforme periodicidade acordada com cada cliente
- **Relatorio Financeiro**: DRE, balanco simplificado, fluxo de caixa, inadimplencia
  - Distribuido quinzenalmente para Diretoria Financeira
- Formatos: PDF, Excel, Google Sheets (link compartilhavel)
- Envio automatico por email com resumo inline

### Atualizacao em Tempo Real
- Conexao WebSocket para atualizacao de dados sem refresh da pagina
- Indicador visual de ultima atualizacao em cada dashboard
- Notificacoes em tempo real quando KPIs atingem limites criticos
- Opcao de modo TV para exibicao em telas de escritorio (rotacao entre dashboards)

## Fluxo de Dados

```
[Modulos Fonte de Dados]
+------+------+------+------+------+------+------+
| CRM  |Finan.|Prod. |Content|Social|RH/   |Intel.|
|      |ceiro |      |Studio|Sched.|Agentes|Feed  |
+------+------+------+------+------+------+------+
       |       |      |      |       |       |
       +---+---+------+------+-------+-------+
           |
           v
    [Data Pipeline]
    (ETL / Transformacao)
           |
           v
    [Data Warehouse]
    (PostgreSQL / Views Materializadas)
           |
    +------+------+
    |      |      |
    v      v      v
[Dashboards] [Relatorios] [Alertas]
(WebSocket)  (Automaticos) (Tempo Real)
    |           |            |
    v           v            v
[Frontend]  [PDF/Excel]  [Push/Email]
(Charts)    (Distribuicao) (Notificacoes)
```

## Entidades e Modelo de Dados

### Dashboard (Painel)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome do dashboard |
| type | Enum | EXECUTIVO, COMERCIAL, FINANCEIRO, PRODUCAO, CONTEUDO, AGENTES, CUSTOMIZADO |
| owner_id | UUID (FK) | Proprietario/criador |
| is_public | Boolean | Se e visivel para todos os usuarios com permissao |
| layout | JSON | Configuracao de layout (grid, posicao dos widgets) |
| filters | JSON | Filtros padrao aplicados |
| refresh_interval_seconds | Integer | Intervalo de atualizacao automatica |
| is_tv_mode_enabled | Boolean | Se pode ser exibido em modo TV |
| created_at | DateTime | Data de criacao |
| updated_at | DateTime | Ultima atualizacao |

### Report (Relatorio)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome do relatorio |
| type | Enum | SEMANAL, MENSAL, CLIENTE, FINANCEIRO, CUSTOMIZADO |
| template_id | UUID (FK) | Template utilizado |
| period_start | Date | Inicio do periodo |
| period_end | Date | Fim do periodo |
| data_snapshot | JSON | Snapshot dos dados no momento da geracao |
| generated_file_url | String | URL do arquivo gerado (PDF/Excel) |
| format | Enum | PDF, EXCEL, GOOGLE_SHEETS |
| status | Enum | GERANDO, GERADO, DISTRIBUIDO, ERRO |
| distributed_to | UUID[] | Usuarios que receberam |
| distributed_at | DateTime | Data de distribuicao |
| generated_at | DateTime | Data de geracao |
| generated_by | Enum | AUTOMATICO, MANUAL |

### KPI (Indicador-Chave de Performance)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome do KPI (ex: "Receita Mensal") |
| module | Enum | CRM, FINANCEIRO, PRODUCAO, CONTEUDO, SOCIAL, RH, INTELIGENCIA |
| calculation_query | Text | Query SQL ou formula de calculo |
| current_value | Decimal | Valor atual |
| target_value | Decimal | Meta |
| previous_value | Decimal | Valor do periodo anterior |
| unit | String | Unidade (R$, %, unidades, dias) |
| trend | Enum | SUBINDO, ESTAVEL, DESCENDO |
| alert_threshold_warning | Decimal | Limite para alerta amarelo |
| alert_threshold_critical | Decimal | Limite para alerta vermelho |
| last_calculated_at | DateTime | Ultima vez que foi calculado |

### Chart (Grafico)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| dashboard_id | UUID (FK) | Dashboard onde esta exibido |
| title | String | Titulo do grafico |
| type | Enum | PIE, BAR, LINE, AREA, TIMELINE, FUNNEL, GAUGE, HEATMAP, BUBBLE |
| data_source_id | UUID (FK) | Fonte de dados |
| query | Text | Query de dados |
| config | JSON | Configuracoes visuais (cores, legendas, eixos) |
| position | JSON | Posicao no grid do dashboard (x, y, width, height) |
| drill_down_config | JSON | Configuracao de drill-down |
| is_interactive | Boolean | Se suporta zoom, tooltip, clique |

### DataSource (Fonte de Dados)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome descritivo |
| module | Enum | CRM, FINANCEIRO, PRODUCAO, CONTEUDO, SOCIAL, RH, INTELIGENCIA |
| type | Enum | TABLE, VIEW, API, AGGREGATION |
| connection_config | JSON | Configuracao de conexao |
| refresh_strategy | Enum | REAL_TIME, PERIODIC, ON_DEMAND |
| refresh_interval_seconds | Integer | Intervalo de atualizacao (se periodico) |
| last_refreshed_at | DateTime | Ultima atualizacao |
| cache_ttl_seconds | Integer | Tempo de vida do cache |

## Interface e UX

### Tela Principal - Seletor de Dashboards
- Grid de cards com thumbnails dos dashboards disponiveis
- Favoritos do usuario no topo
- Filtro por tipo (Executivo, Comercial, Financeiro, etc.)
- Botao "Criar Novo Dashboard" para dashboards customizados
- Indicador de ultima atualizacao em cada card

### Dashboard Interativo
- Layout responsivo em grid com widgets redimensionaveis
- Barra de filtros globais no topo (periodo, cliente, departamento)
- Cada widget e um grafico ou KPI card independente
- Menu de contexto em cada widget: expandir, editar, exportar, remover
- Modo fullscreen para apresentacoes
- Modo TV com rotacao automatica entre dashboards

### Construtor de Relatorios
- Interface visual com tres paineis:
  - Esquerda: catalogo de metricas e graficos disponiveis
  - Centro: area de composicao do relatorio (drag-and-drop)
  - Direita: configuracoes do item selecionado
- Preview em tempo real do relatorio sendo construido
- Opcao de configurar distribuicao automatica (destinatarios, periodicidade, formato)
- Salvar como template para reutilizacao

### Central de Alertas
- Lista de alertas ativos e historicos
- Configuracao de regras de alerta por KPI
- Indicadores visuais: verde (normal), amarelo (atencao), vermelho (critico)
- Acao rapida: ir para o dashboard relacionado, marcar como resolvido

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| CRM | Leitura | Dados de leads, conversoes, pipeline, ticket medio |
| Financeiro | Leitura | Receita, despesas, fluxo de caixa, inadimplencia |
| Producao/Projetos | Leitura | Status de projetos, alocacao, prazos |
| Content Studio | Leitura | Volume de conteudo gerado, aprovacoes |
| Social Scheduler | Leitura | Posts publicados, engajamento, metricas sociais |
| RH/Agentes | Leitura | Performance de agentes, dados de equipe |
| Intelligence Feed | Leitura | Tendencias identificadas, oportunidades |
| Google Sheets | Escrita | Exportacao de relatorios para Google Sheets |
| Email (SMTP) | Escrita | Distribuicao de relatorios por email |
| Slack/Teams | Escrita | Alertas e resumos em canais de comunicacao |

## Agentes de IA

### Agente Analista de Dados
- **Funcao**: Analisa padroes nos dados e gera insights automaticos
- **Modelo**: Claude (analise estatistica e interpretacao)
- **Entrada**: Dados de todos os modulos agregados no data warehouse
- **Saida**: Insights como "Receita caiu 15% esta semana, principal causa: 3 projetos adiados"
- **Frequencia**: Diario (insights rapidos), semanal (analise aprofundada)

### Agente Compilador de Relatorios
- **Funcao**: Compila dados, gera graficos e formata relatorios automatizados
- **Modelo**: Claude (narrativa) + Puppeteer (geracao de PDF)
- **Entrada**: Template do relatorio + dados do periodo
- **Saida**: Relatorio formatado em PDF e/ou Excel pronto para distribuicao
- **Frequencia**: Conforme calendario de relatorios (semanal, mensal)

### Agente de Alertas Inteligentes
- **Funcao**: Monitora KPIs em tempo real e emite alertas quando limites sao atingidos
- **Modelo**: Regras configuradas + Claude (contextualizacao do alerta)
- **Monitoramento**: Continuo via WebSocket
- **Saida**: Alerta contextualizado com causa provavel e acao sugerida
- **Exemplo**: "Taxa de conversao caiu para 12% (meta: 20%). Possivel causa: queda no volume de leads qualificados esta semana."

### Agente Narrador de Dashboard
- **Funcao**: Gera uma narrativa textual resumindo o estado do dashboard
- **Modelo**: Claude (sintese narrativa)
- **Entrada**: Todos os KPIs e graficos de um dashboard
- **Saida**: Texto de 3-5 paragrafos resumindo: "Esta semana a Somos..."
- **Uso**: Exibido no topo do dashboard como resumo executivo dinamico

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Graficos | Recharts (React) | Biblioteca declarativa e customizavel para React |
| Graficos Avancados | Chart.js | Alternativa leve para graficos mais simples |
| Graficos Complexos | D3.js | Para visualizacoes customizadas (bubble, heatmap) |
| Atualizacao em Tempo Real | Socket.io (WebSocket) | Atualizacao de dados sem refresh |
| Geracao de PDF | Puppeteer + React-PDF | Relatorios em PDF de alta qualidade |
| Geracao de Excel | ExcelJS | Relatorios em formato Excel |
| Data Pipeline | Views Materializadas (PostgreSQL) | Aggregacoes pre-calculadas para performance |
| Cache | Redis | Cache de dados de dashboard para acesso rapido |
| Agendamento | node-cron / BullMQ | Geracao programada de relatorios |
| Frontend Grid | react-grid-layout | Layout de dashboard com drag-and-drop e resize |
| Backend | Node.js + Fastify | API para consultas de dados e WebSocket |
| Banco de Dados | PostgreSQL (Supabase) | Consultas analiticas com CTEs e window functions |

## Dependencias

### Dependencias de Modulo
- **Todos os modulos**: Obrigatoria - o Analytics agrega dados de todos os modulos
- Cada modulo deve expor APIs ou views de dados para consumo pelo Analytics

### Dependencias Tecnicas
- PostgreSQL com views materializadas configuradas para cada modulo
- Redis para cache de dashboards e dados frequentemente acessados
- WebSocket server configurado para atualizacoes em tempo real
- Templates de relatorios configurados para cada tipo (semanal, mensal, cliente)
- Cron jobs configurados para geracao automatica de relatorios
- SMTP configurado para distribuicao por email

### Dependencias Externas
- Google Sheets API para exportacao (opcional)
- SMTP server para envio de emails com relatorios

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Tempo de carregamento do dashboard | < 2 segundos | Performance monitoring |
| Atualizacao em tempo real | Latencia < 5 segundos | Timestamp do evento vs exibicao |
| Relatorios automaticos entregues no prazo | 100% | Entregues / programados |
| Taxa de uso dos dashboards | > 80% dos gestores acessam diariamente | Analytics de uso |
| Precisao dos dados exibidos | 100% | Auditoria comparativa com dados fonte |
| Dashboards customizados criados | > 5 por departamento | Contagem |
| Tempo de criacao de relatorio customizado | < 10 minutos | Medicao de uso |
| Satisfacao dos usuarios com dashboards | > 4.5/5.0 | Pesquisa trimestral |
| Reducao de tempo gasto em compilacao manual | > 80% | Comparativo com processo anterior |
| Alertas criticos detectados a tempo | 100% | Alertas emitidos antes de impacto |
