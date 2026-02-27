# Social Scheduler

## Proposito e Visao

O Social Scheduler e o modulo responsavel por agendar, publicar e monitorar conteudo nas redes sociais dos clientes da Somos Produtora. Ele funciona como o ultimo elo da cadeia de producao de conteudo, recebendo pecas aprovadas do Content Studio e distribuindo-as nas plataformas corretas nos horarios otimizados para maximo engajamento.

O modulo oferece uma visao de calendario unificada por cliente, mostrando todos os posts agendados em todas as plataformas. Integra-se diretamente com as APIs do Instagram (Graph API), Facebook (Marketing API), LinkedIn (Share API) e YouTube (Data API) para publicacao automatica. Alem da publicacao, o Social Scheduler coleta dados de analytics de cada post publicado, oferecendo insights sobre engajamento, alcance, melhores horarios de postagem e tendencias de crescimento.

A visao e criar um hub centralizado onde o time de social media da Somos consiga gerenciar dezenas de contas de clientes de forma eficiente, com sugestoes inteligentes de horarios e deteccao automatica de conflitos de programacao.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Social Media Manager | Agenda posts, monitora calendario, analisa metricas | Agendamento, publicacao, edicao, analise |
| Diretor de Criacao | Supervisiona calendario editorial, aprova posts estrategicos | Visualizacao geral, aprovacao |
| Gerente de Conta | Visualiza calendario do seu cliente, compartilha relatorios | Visualizacao por cliente, exportacao |
| Cliente | Visualiza posts agendados e publicados via portal externo | Visualizacao externa, aprovacao opcional |
| Agente IA de Horarios | Analisa dados historicos e sugere melhores horarios de postagem | Leitura de analytics, sugestao |
| Agente IA Monitor | Monitora falhas de publicacao e alertas de API | Leitura de status, envio de alertas |

## Funcionalidades Principais

### Agendamento de Publicacoes
- Agendamento de posts para data e hora especificas em qualquer plataforma
- Agendamento em lote: multiplos posts para multiplas plataformas em uma unica acao
- Re-agendamento via drag-and-drop no calendario
- Filas de publicacao: definir intervalos minimos entre posts para evitar saturacao
- Agendamento recorrente para conteudo evergreen (semanal, quinzenal, mensal)
- Preview nativo de como o post aparecera em cada plataforma antes da publicacao

### Calendario Visual Unificado
- Visao mensal, semanal e diaria de todos os posts agendados
- Filtros por cliente, plataforma, status e tipo de conteudo
- Codigo de cores por plataforma (roxo Instagram, azul Facebook, azul escuro LinkedIn, vermelho YouTube)
- Codigo de cores por status (cinza rascunho, amarelo agendado, verde publicado, vermelho erro)
- Indicadores de datas comemorativas e eventos relevantes por setor do cliente
- Deteccao de conflitos: alerta quando dois posts estao agendados muito proximos para o mesmo cliente

### Publicacao Multi-Plataforma
- Publicacao automatica no horario agendado via APIs oficiais
- Suporte a tipos de conteudo por plataforma:
  - **Instagram**: Feed (imagem/carrossel), Stories, Reels (upload de video)
  - **Facebook**: Posts (imagem/video), Stories, eventos
  - **LinkedIn**: Posts, artigos, documentos (PDF)
  - **YouTube**: Videos, Shorts, Community posts
- Tratamento de erros com retry automatico (3 tentativas com intervalo exponencial)
- Notificacao imediata ao Social Media Manager em caso de falha definitiva
- Log detalhado de cada tentativa de publicacao

### Analytics e Insights
- Coleta automatica de metricas apos publicacao (24h, 48h, 7 dias)
- Metricas por post: curtidas, comentarios, compartilhamentos, alcance, impressoes, cliques, salvamentos
- Metricas por conta: crescimento de seguidores, taxa de engajamento media, alcance total
- Analise de melhores horarios de postagem baseada em dados historicos
- Comparativo de performance entre plataformas para o mesmo conteudo
- Relatorios automaticos semanais e mensais por cliente
- Exportacao de relatorios em PDF e CSV

### Sugestao Inteligente de Horarios
- Analise de dados historicos de engajamento por plataforma e cliente
- Mapa de calor mostrando horarios de maior engajamento por dia da semana
- Sugestao automatica do melhor horario ao agendar um novo post
- Consideracao de fuso horario do publico-alvo do cliente

## Fluxo de Dados

```
[Content Studio]
(Conteudo Aprovado)
        |
        v
[Social Scheduler - Entrada]
        |
        v
[Selecao de Plataforma e Horario]
        |
        +---> [Sugestao IA de Horario]
        |
        v
[Calendario - Agendamento]
        |
        v
[Fila de Publicacao (BullMQ)]
        |
        v
[Worker de Publicacao]
        |
        +----------+----------+----------+
        |          |          |          |
        v          v          v          v
[Instagram   [Facebook  [LinkedIn  [YouTube
 Graph API]   API]       API]       Data API]
        |          |          |          |
        +----------+----------+----------+
                   |
                   v
            [Confirmacao / Erro]
                   |
           +-------+-------+
           |               |
    [Publicado com    [Falha]
     Sucesso]              |
           |          [Retry / Alerta]
           v
    [Coleta de Analytics]
    (24h, 48h, 7 dias)
           |
           v
    [Dashboard de Metricas]
```

## Entidades e Modelo de Dados

### ScheduledPost (Post Agendado)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| content_piece_id | UUID (FK) | Referencia ao conteudo no Content Studio |
| social_account_id | UUID (FK) | Conta social de destino |
| client_id | UUID (FK) | Cliente proprietario |
| platform | Enum | INSTAGRAM, FACEBOOK, LINKEDIN, YOUTUBE |
| post_type | Enum | FEED, STORY, REEL, ARTICLE, VIDEO, SHORT, CAROUSEL |
| caption | Text | Legenda/texto do post |
| media_urls | String[] | URLs das midias (imagens, videos) |
| hashtags | String[] | Hashtags do post |
| scheduled_at | DateTime | Data/hora agendada para publicacao |
| published_at | DateTime | Data/hora real da publicacao |
| status | Enum | RASCUNHO, AGENDADO, PUBLICANDO, PUBLICADO, ERRO, CANCELADO |
| platform_post_id | String | ID do post na plataforma apos publicacao |
| error_message | Text | Mensagem de erro se falhou |
| retry_count | Integer | Numero de tentativas de publicacao |
| created_by | UUID (FK) | Quem agendou |
| created_at | DateTime | Data de criacao |

### SocialAccount (Conta Social)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| client_id | UUID (FK) | Cliente proprietario da conta |
| platform | Enum | INSTAGRAM, FACEBOOK, LINKEDIN, YOUTUBE |
| account_name | String | Nome da conta (@handle) |
| account_id | String | ID da conta na plataforma |
| access_token | String (encrypted) | Token de acesso a API |
| refresh_token | String (encrypted) | Token de refresh |
| token_expires_at | DateTime | Data de expiracao do token |
| is_active | Boolean | Se a conta esta ativa |
| followers_count | Integer | Numero de seguidores (atualizado periodicamente) |
| connected_at | DateTime | Data de conexao da conta |

### Calendar (Calendario)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| client_id | UUID (FK) | Cliente associado |
| name | String | Nome do calendario |
| description | Text | Descricao do calendario editorial |
| default_posting_times | JSON | Horarios padrao de postagem por dia da semana |
| min_post_interval_hours | Integer | Intervalo minimo entre posts (em horas) |
| special_dates | JSON[] | Datas comemorativas e eventos customizados |
| is_active | Boolean | Se o calendario esta ativo |

### AnalyticsData (Dados de Analytics)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| scheduled_post_id | UUID (FK) | Post associado |
| social_account_id | UUID (FK) | Conta social |
| collected_at | DateTime | Data/hora da coleta |
| hours_after_post | Integer | Horas apos publicacao (24, 48, 168) |
| likes | Integer | Curtidas |
| comments | Integer | Comentarios |
| shares | Integer | Compartilhamentos |
| saves | Integer | Salvamentos (Instagram) |
| reach | Integer | Alcance |
| impressions | Integer | Impressoes |
| clicks | Integer | Cliques no link |
| engagement_rate | Decimal | Taxa de engajamento calculada |
| video_views | Integer | Visualizacoes de video (se aplicavel) |
| video_watch_time | Integer | Tempo total de visualizacao em segundos |

## Interface e UX

### Calendario Principal
- Visao padrao: semanal com colunas por dia e linhas por horario
- Alternativas: visao mensal compacta e visao diaria detalhada
- Cards de posts no calendario mostrando thumbnail, plataforma e status
- Clique no card para expandir detalhes e preview
- Drag-and-drop para mover posts entre datas e horarios
- Sidebar direita com lista de posts pendentes de agendamento

### Tela de Agendamento
- Formulario com selecao de conta social, tipo de post e data/hora
- Preview nativo da plataforma selecionada (mockup visual)
- Sugestao de horario otimizado com botao "Usar horario sugerido"
- Mapa de calor de engajamento por horario abaixo do seletor de data/hora
- Upload de midia ou selecao do Content Studio
- Opcao de agendamento em lote para multiplas plataformas

### Dashboard de Analytics
- Cards com metricas principais: posts publicados, engajamento medio, alcance total
- Graficos de linha mostrando evolucao ao longo do tempo
- Tabela comparativa de performance por plataforma
- Top 5 posts com maior engajamento do periodo
- Piores 5 posts para analise de melhoria
- Filtros por cliente, plataforma e periodo

### Monitor de Status
- Lista em tempo real de publicacoes em andamento e recentes
- Indicadores visuais: verde (sucesso), amarelo (tentando), vermelho (erro)
- Detalhes de erro com opcao de retry manual
- Alertas de tokens expirando ou contas desconectadas

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| Content Studio | Leitura | Recebe conteudo aprovado para agendamento |
| Instagram Graph API | Escrita/Leitura | Publicacao de posts e coleta de metricas |
| Facebook Marketing API | Escrita/Leitura | Publicacao de posts e coleta de metricas |
| LinkedIn API | Escrita/Leitura | Publicacao de posts e artigos |
| YouTube Data API | Escrita/Leitura | Upload de videos e coleta de metricas |
| Analytics/Reports | Escrita | Envia dados de performance para dashboards executivos |
| CRM | Leitura | Dados do cliente e informacoes de conta |
| Firebase Cloud Messaging | Escrita | Notificacoes push em caso de erro de publicacao |

## Agentes de IA

### Agente Otimizador de Horarios
- **Funcao**: Analisa dados historicos de engajamento e sugere os melhores horarios para cada post
- **Modelo**: Claude (analise estatistica e recomendacao)
- **Entrada**: Historico de analytics da conta, tipo de conteudo, dia da semana
- **Saida**: Top 3 horarios sugeridos com score de confianca
- **Frequencia**: Recalcula semanalmente para cada conta

### Agente Monitor de Saude
- **Funcao**: Monitora status de conexoes com APIs, tokens expirando e falhas de publicacao
- **Modelo**: Regras + Claude para diagnostico de erros complexos
- **Monitoramento**: A cada 15 minutos verifica status de todas as conexoes
- **Saida**: Alertas proativos antes de tokens expirarem, diagnostico de erros recorrentes
- **Acao**: Notifica o Social Media Manager e sugere acoes corretivas

### Agente Analisador de Performance
- **Funcao**: Analisa metricas coletadas e gera insights acionaveis
- **Modelo**: Claude (sintese de dados e recomendacoes)
- **Entrada**: Dados de analytics dos ultimos 30 dias
- **Saida**: Relatorio com padroes identificados, conteudos de melhor performance e recomendacoes
- **Frequencia**: Relatorio semanal automatico por cliente

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Frontend | Next.js + React | Interface reativa com calendario complexo |
| Calendario UI | FullCalendar (React) | Biblioteca robusta para calendario com drag-and-drop |
| Fila de Publicacao | BullMQ + Redis | Agendamento preciso e retry de jobs falhados |
| Instagram API | Instagram Graph API v18+ | API oficial para publicacao e metricas |
| Facebook API | Facebook Marketing API v18+ | API oficial para publicacao e metricas |
| LinkedIn API | LinkedIn Share API v2 | API oficial para publicacao |
| YouTube API | YouTube Data API v3 | API oficial para upload e metricas |
| Armazenamento de Midia | Supabase Storage / AWS S3 | Midias com CDN para upload rapido |
| WebSocket | Socket.io | Atualizacoes em tempo real do status de publicacao |
| Cron Jobs | node-cron / BullMQ Repeatable | Coleta periodica de analytics |
| Backend | Node.js + Fastify | API performatica para operacoes em tempo real |

## Dependencias

### Dependencias de Modulo
- **Content Studio**: Recomendada - principal fonte de conteudo para agendamento
- **Analytics/Reports**: Recomendada - envio de metricas para dashboards executivos
- **CRM**: Recomendada - dados dos clientes e suas contas sociais

### Dependencias Tecnicas
- Contas de desenvolvedor configuradas nas plataformas (Meta, LinkedIn, Google)
- Tokens de acesso OAuth2 configurados e renovaveis automaticamente
- Redis configurado para filas de publicacao e cache de metricas
- Dominio verificado para Instagram Business API
- Pagina do Facebook vinculada a conta do Instagram Business

### Dependencias Externas
- Aprovacao de app pelo Meta (Instagram + Facebook) para publicacao automatica
- Aprovacao de app pelo LinkedIn para publicacao via API
- Quota de API do YouTube suficiente para volume de publicacoes

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Taxa de publicacao com sucesso | > 98% | Posts publicados / posts agendados |
| Tempo medio de atraso na publicacao | < 30 segundos | Diferenca entre horario agendado e real |
| Contas sociais conectadas ativas | 100% | Contas com token valido / total de contas |
| Tempo de deteccao de falha | < 5 minutos | Timestamp do erro vs timestamp do alerta |
| Engajamento medio dos posts | Crescimento de 10% m/m | Media de engagement rate por cliente |
| Posts agendados por mes | > 100 por cliente ativo | Contagem mensal |
| Precisao da sugestao de horario | > 75% | Posts no horario sugerido com engajamento acima da media |
| Disponibilidade do sistema | > 99.5% | Uptime mensal |
