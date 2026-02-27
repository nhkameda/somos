# App Android (Mobile)

## Proposito e Visao

O App Android e a extensao mobile do ecossistema Somos Produtora, desenvolvido em React Native para atender tanto clientes quanto membros da equipe. O aplicativo oferece acesso em tempo real as funcionalidades mais criticas do sistema, projetado especialmente para cenarios de mobilidade: producoes em campo, sets de filmagem, reunioes externas e acompanhamento remoto de projetos.

Para os clientes da Somos, o app funciona como um portal de transparencia e colaboracao, permitindo acompanhar o status de seus projetos, aprovar entregas (videos, conteudos, orcamentos), receber notificacoes sobre marcos importantes e manter comunicacao direta com a equipe responsavel.

Para os membros da equipe Somos, o app e uma ferramenta de produtividade operacional: gestao de tarefas em campo, dashboard de metricas acessivel de qualquer lugar, chat interno entre equipe, registro de ponto com geolocalizacao e acesso a documentos e briefings de projetos. A funcionalidade offline e fundamental para trabalho em locacoes de filmagem onde a conectividade pode ser limitada.

A visao e garantir que nenhuma informacao critica fique inacessivel por falta de acesso ao desktop, mantendo a equipe e os clientes conectados e informados 24/7.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Cliente | Acompanha projetos, aprova entregas, recebe notificacoes | Visualizacao de seus projetos, aprovacao, chat com equipe |
| Produtor Executivo | Gerencia projetos em campo, atualiza status, comunica com equipe | Gestao de projetos, tarefas, chat, dashboard |
| Diretor de Fotografia / Cinegrafista | Consulta briefings, reporta status de filmagem | Visualizacao de projetos, checklist de filmagem, chat |
| Editor / Pos-Produtor | Recebe notificacoes de aprovacao, envia previews | Notificacoes, upload de preview, chat |
| Gerente de Conta | Comunica com clientes, acompanha entregas | Chat com clientes, status de projetos, dashboard |
| Diretor Geral | Acessa dashboards executivos, aprova decisoes criticas | Dashboard completo, aprovacoes, notificacoes |
| Social Media Manager | Visualiza calendario de posts, recebe alertas de publicacao | Calendario, notificacoes de publicacao |

## Funcionalidades Principais

### Funcionalidades para Clientes

#### Acompanhamento de Projetos
- Lista de projetos ativos e concluidos do cliente
- Timeline visual com etapas do projeto e progresso percentual
- Marcos do projeto com datas previstas e realizadas
- Detalhes de cada etapa com descricao e responsavel
- Historico de atualizacoes com timeline cronologica

#### Aprovacao de Entregas
- Lista de entregas pendentes de aprovacao
- Visualizacao de videos, imagens e documentos inline no app
- Player de video com opcao de comentar em timecodes especificos
- Botoes de Aprovar / Solicitar Ajustes com campo de comentario
- Historico de aprovacoes e revisoes
- Notificacao push quando nova entrega esta disponivel para revisao

#### Comunicacao com Equipe
- Chat direto com o Gerente de Conta responsavel
- Envio de mensagens de texto, imagens, documentos e audio
- Indicador de mensagem lida / nao lida
- Historico de conversas preservado

#### Notificacoes do Cliente
- Novo marco do projeto atingido
- Entrega disponivel para aprovacao
- Orcamento enviado para aprovacao
- Resposta da equipe no chat
- Projeto concluido com link para arquivos finais

### Funcionalidades para Equipe

#### Gestao de Tarefas em Campo
- Lista de tarefas atribuidas ao usuario, organizadas por projeto e prioridade
- Atualizacao de status de tarefa (nao iniciada, em andamento, concluida)
- Checklist de itens por tarefa (ex: checklist de equipamentos para filmagem)
- Timer de tarefa para registro de horas trabalhadas
- Criacao de tarefas rapidas e atribuicao a colegas
- Comentarios e anexos em tarefas

#### Dashboard de Metricas
- Dashboard resumido com KPIs mais relevantes para o papel do usuario
- Versao compacta dos dashboards do modulo Analytics/Reports
- Graficos adaptados para tela mobile (swipe entre graficos)
- Dados atualizados em tempo real quando online
- Dados em cache quando offline (ultima sincronizacao)

#### Chat Interno da Equipe
- Canais de chat por projeto (todos os membros do projeto)
- Canais por departamento (Producao, Criacao, Comercial)
- Chat direto (1:1) entre membros da equipe
- Envio de texto, imagens, videos, documentos e localizacao
- Mencoes (@usuario) com notificacao especifica
- Busca no historico de mensagens
- Mensagens fixadas (pinned) para informacoes importantes

#### Push Notifications
- Notificacoes categorizadas por tipo:
  - **Tarefas**: Nova tarefa atribuida, prazo se aproximando, tarefa concluida por dependente
  - **Projetos**: Mudanca de status, novo marco, entrega aprovada/rejeitada pelo cliente
  - **Chat**: Nova mensagem, mencao em canal, mensagem do cliente
  - **Alertas**: Erro de agente de IA, publicacao falhada, KPI critico
  - **RH**: Aprovacao de ferias, lembrete de ponto, avaliacao pendente
- Centro de notificacoes com historico e filtros
- Configuracao granular de preferencias (quais tipos receber, horarios silenciosos)
- Badge no icone do app com contagem de notificacoes nao lidas

#### Registro de Ponto Mobile
- Botao de check-in / check-out na tela principal
- Captura de geolocalizacao no momento do registro
- Compativel com registro em locacoes de filmagem fora do escritorio
- Historico de registros com mapa mostrando localizacoes
- Integracao com modulo RH para calculo de horas

#### Acesso a Documentos
- Visualizacao de briefings de projeto
- Acesso a orcamentos e contratos
- Download de scripts e roteiros
- Storyboards e referencias visuais
- Cache local de documentos frequentemente acessados

### Funcionalidades Offline

#### Modo Offline Completo
- Tarefas e checklists disponiveis offline com sincronizacao posterior
- Mensagens de chat redigidas offline sao enviadas quando a conexao retorna
- Dashboard com dados em cache da ultima sincronizacao
- Documentos baixados previamente acessiveis offline
- Registro de ponto offline com timestamp local e sincronizacao posterior
- Indicador visual claro de modo offline (banner no topo)

#### Sincronizacao Inteligente
- Sincronizacao automatica quando a conexao e restabelecida
- Resolucao de conflitos: ultima edicao prevalece com notificacao
- Fila de acoes pendentes visivel ao usuario
- Sincronizacao seletiva: o usuario escolhe quais projetos manter offline
- Compressao de dados para economia de dados moveis

## Fluxo de Dados

```
+==========================================+
|          FLUXO DO CLIENTE                |
+==========================================+

[Notificacao Push] --> [App Cliente]
                           |
              +------------+------------+
              |            |            |
      [Meus Projetos] [Aprovacoes]  [Chat]
              |            |            |
       [Status e      [Revisar     [Mensagem
        Timeline]      Entrega]     p/ Equipe]
              |            |            |
              +------+-----+            |
                     |                  |
              [Backend API] <-----------+
              (Supabase / Fastify)

+==========================================+
|          FLUXO DA EQUIPE                 |
+==========================================+

[Notificacao Push] --> [App Equipe]
                           |
    +------+------+--------+--------+------+
    |      |      |        |        |      |
[Tarefas][Dash  [Chat   [Ponto  [Docs  [Notif.
         board] Equipe]  Mobile] Access] Center]
    |      |      |        |        |      |
    +------+------+--------+--------+------+
                   |
            [Cache Local]
            (SQLite / MMKV)
                   |
            [Sync Manager]
                   |
            [Backend API]
            (Supabase / Fastify)
                   |
        +----------+----------+
        |          |          |
  [Supabase   [Redis     [Firebase
   Realtime]   Cache]     FCM]
```

## Entidades e Modelo de Dados

### MobileUser (Usuario Mobile)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| user_id | UUID (FK) | Referencia ao usuario no sistema principal |
| user_type | Enum | CLIENTE, EQUIPE |
| device_token | String | Token FCM para push notifications |
| device_os | String | Versao do SO (Android 14, etc.) |
| app_version | String | Versao do app instalada |
| last_sync_at | DateTime | Ultima sincronizacao com servidor |
| offline_projects | UUID[] | Projetos marcados para acesso offline |
| notification_preferences | JSON | Preferencias de notificacao por tipo |
| is_active | Boolean | Se a sessao mobile esta ativa |
| created_at | DateTime | Primeira instalacao |

### Notification (Notificacao)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| user_id | UUID (FK) | Destinatario |
| type | Enum | TAREFA, PROJETO, CHAT, ALERTA, RH, APROVACAO, PUBLICACAO |
| title | String | Titulo da notificacao |
| body | Text | Corpo da notificacao |
| data | JSON | Dados adicionais (IDs para deep linking) |
| deep_link | String | URL de deep link para tela especifica do app |
| priority | Enum | LOW, NORMAL, HIGH, URGENT |
| is_read | Boolean | Se foi lida |
| read_at | DateTime | Data/hora da leitura |
| is_pushed | Boolean | Se foi enviada via push |
| pushed_at | DateTime | Data/hora do envio push |
| created_at | DateTime | Data de criacao |

### ChatMessage (Mensagem de Chat)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| channel_id | UUID (FK) | Canal de chat |
| sender_id | UUID (FK) | Remetente |
| message_type | Enum | TEXT, IMAGE, VIDEO, DOCUMENT, AUDIO, LOCATION |
| content | Text | Conteudo textual da mensagem |
| media_url | String | URL da midia anexada |
| media_thumbnail_url | String | URL do thumbnail (para imagens/videos) |
| reply_to_id | UUID (FK) | Mensagem a qual esta respondendo |
| mentions | UUID[] | Usuarios mencionados |
| is_pinned | Boolean | Se esta fixada no canal |
| is_edited | Boolean | Se foi editada |
| edited_at | DateTime | Data/hora da edicao |
| is_deleted | Boolean | Se foi deletada (soft delete) |
| created_at | DateTime | Data/hora de envio |
| synced | Boolean | Se foi sincronizada com servidor (offline support) |

### ProjectView (Visao de Projeto no Mobile)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| project_id | UUID (FK) | Projeto no sistema principal |
| client_id | UUID (FK) | Cliente associado |
| name | String | Nome do projeto |
| status | Enum | NAO_INICIADO, EM_ANDAMENTO, PAUSADO, CONCLUIDO, CANCELADO |
| progress_percentage | Integer | Progresso geral (0-100) |
| current_phase | String | Fase atual (Pre-producao, Producao, Pos-producao) |
| milestones | JSON[] | Marcos com nome, data prevista, data real, status |
| team_members | JSON[] | Membros da equipe com nome, papel e foto |
| pending_approvals | Integer | Numero de aprovacoes pendentes do cliente |
| next_deadline | DateTime | Proximo prazo importante |
| thumbnail_url | String | Imagem representativa do projeto |
| cached_at | DateTime | Data do ultimo cache local |

### ChatChannel (Canal de Chat)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| type | Enum | PROJECT, DEPARTMENT, DIRECT, CLIENT |
| name | String | Nome do canal |
| description | Text | Descricao do canal |
| project_id | UUID (FK) | Projeto associado (se tipo PROJECT) |
| participants | UUID[] | Participantes do canal |
| is_archived | Boolean | Se esta arquivado |
| last_message_at | DateTime | Data/hora da ultima mensagem |
| unread_count | JSON | Contagem de nao lidas por usuario |
| created_at | DateTime | Data de criacao |

## Interface e UX

### Tela Inicial (Home) - Cliente
- Card principal com projeto mais recente e progresso
- Lista de notificacoes recentes (ultimas 5)
- Badge de aprovacoes pendentes com numero
- Acesso rapido ao chat com equipe
- Bottom navigation: Home, Projetos, Aprovacoes, Chat, Perfil

### Tela Inicial (Home) - Equipe
- Cards com KPIs resumidos do dia (tarefas pendentes, projetos ativos)
- Lista de tarefas prioritarias para hoje
- Botao flutuante de check-in/check-out (registro de ponto)
- Notificacoes recentes
- Bottom navigation: Home, Tarefas, Chat, Dashboard, Menu

### Tela de Projeto (Cliente)
- Header com nome do projeto e progresso (barra visual)
- Timeline vertical com marcos e etapas
- Aba "Entregas" com itens para aprovacao
- Aba "Comunicacao" com chat do projeto
- Aba "Documentos" com arquivos do projeto

### Tela de Tarefas (Equipe)
- Lista de tarefas com filtros: Hoje, Esta Semana, Atrasadas, Por Projeto
- Card de tarefa com titulo, projeto, prioridade (badge colorido) e prazo
- Swipe para direita: marcar como concluida
- Swipe para esquerda: adiar tarefa
- Tela de detalhe com descricao, checklist, comentarios e timer

### Tela de Chat
- Lista de canais e conversas ordenados por ultima mensagem
- Indicador de mensagens nao lidas por canal
- Tela de conversa com bolhas de mensagem (estilo WhatsApp)
- Barra de composicao com opcoes de anexo (camera, galeria, documento, localizacao)
- Pull-to-refresh para carregar mensagens mais antigas

### Tela de Dashboard (Equipe)
- Cards de KPI com swipe horizontal para navegar entre metricas
- Graficos simplificados adaptados para mobile
- Tap em grafico para expandir em fullscreen
- Seletor de periodo (hoje, esta semana, este mes)

### Design System Mobile
- Seguir Material Design 3 (Android nativo)
- Cores primarias da marca Somos Produtora
- Tipografia consistente com hierarquia clara
- Suporte a tema escuro (dark mode)
- Acessibilidade: fontes ajustaveis, contraste adequado, labels para screen readers
- Animacoes suaves para transicoes entre telas

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| Backend API (Fastify) | Leitura/Escrita | Todas as operacoes CRUD |
| Supabase Realtime | Leitura | Atualizacoes em tempo real de projetos, chat, notificacoes |
| Firebase Cloud Messaging | Escrita | Envio de push notifications |
| Firebase Analytics | Escrita | Analytics de uso do app |
| Firebase Crashlytics | Escrita | Monitoramento de crashes e erros |
| Google Maps API | Leitura | Mapa para registro de ponto com geolocalizacao |
| Supabase Storage | Leitura/Escrita | Upload e download de midias e documentos |
| Deep Linking | Leitura | Navegacao direta para telas especificas via links |

## Agentes de IA

### Agente de Notificacoes Inteligentes
- **Funcao**: Decide quais notificacoes enviar, para quem e quando, evitando spam
- **Modelo**: Claude Haiku (decisao rapida e leve)
- **Logica**: Agrupa notificacoes similares, prioriza urgentes, respeita horarios silenciosos
- **Exemplo**: Em vez de 5 notificacoes "nova mensagem no chat", envia 1 "5 novas mensagens no projeto X"
- **Frequencia**: Em tempo real, para cada evento gerado

### Agente Resumidor de Atualizacoes
- **Funcao**: Gera resumos diarios de atividades para clientes e equipe
- **Modelo**: Claude Sonnet (sintese de informacoes)
- **Entrada**: Todas as atualizacoes do dia para o usuario
- **Saida**: Resumo em 3-5 linhas do que aconteceu nos projetos do usuario
- **Entrega**: Push notification matinal ("Bom dia! Veja o que aconteceu ontem nos seus projetos")

### Agente de Sincronizacao Offline
- **Funcao**: Otimiza a sincronizacao de dados offline priorizando conteudo mais relevante
- **Modelo**: Regras + heuristicas (nao requer LLM)
- **Logica**: Prioriza projetos ativos, tarefas do dia, mensagens recentes
- **Saida**: Fila de sincronizacao otimizada para minimizar uso de dados

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Framework Mobile | React Native | Codebase unica para Android (e futuro iOS) |
| Navegacao | React Navigation v7 | Navegacao nativa com deep linking |
| Estado Global | Zustand + React Query | Estado leve com cache inteligente de API |
| Armazenamento Local | MMKV (Shopify) | Storage local ultrarapido para cache e offline |
| Banco Local | WatermelonDB | Banco SQLite reativo para dados offline complexos |
| Push Notifications | Firebase Cloud Messaging | Push confiavel com entrega garantida |
| Chat em Tempo Real | Supabase Realtime | Mensagens em tempo real via WebSocket |
| Mapas | react-native-maps (Google Maps) | Mapa para geolocalizacao de ponto |
| Player de Video | react-native-video | Reproducao de videos de entrega para aprovacao |
| Graficos Mobile | Victory Native | Graficos otimizados para React Native |
| Crash Reporting | Firebase Crashlytics | Monitoramento de estabilidade |
| Analytics de Uso | Firebase Analytics | Metricas de uso e engajamento do app |
| CI/CD | Fastlane + GitHub Actions | Build e distribuicao automatizada |
| Distribuicao | Google Play Store + Firebase App Distribution | Producao e beta testing |

## Dependencias

### Dependencias de Modulo
- **Backend API (todos os modulos)**: Obrigatoria - o app e um frontend mobile para o backend
- **RH/Agentes**: Recomendada - registro de ponto e dados de equipe
- **Analytics/Reports**: Recomendada - dashboards mobile
- **Social Scheduler**: Recomendada - calendario e notificacoes de publicacao
- **Content Studio**: Recomendada - aprovacoes de conteudo

### Dependencias Tecnicas
- Backend API publicado e acessivel via HTTPS
- Firebase Project configurado (FCM, Analytics, Crashlytics)
- Supabase Realtime habilitado para chat e atualizacoes
- Google Play Developer Account para publicacao
- Certificados de assinatura do app configurados
- Ambiente de CI/CD configurado para builds automaticos

### Dependencias Externas
- Google Play Store para distribuicao
- Firebase para push notifications e analytics
- Google Maps API key para geolocalizacao
- Conexao com internet para sincronizacao (funciona offline com limitacoes)

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Downloads e instalacoes ativas | > 90% da equipe + 70% dos clientes ativos | Google Play Console + Firebase |
| DAU (Usuarios Ativos Diarios) - Equipe | > 80% da equipe | Firebase Analytics |
| DAU (Usuarios Ativos Diarios) - Clientes | > 30% dos clientes ativos | Firebase Analytics |
| Crash rate | < 0.5% | Firebase Crashlytics |
| Tempo medio de abertura do app | < 2 segundos | Firebase Performance |
| Taxa de entrega de push notifications | > 95% | Firebase Cloud Messaging stats |
| Taxa de leitura de notificacoes | > 60% | Push abertos / push enviados |
| Aprovacoes de entregas feitas via app | > 50% do total | Comparativo app vs web |
| Mensagens de chat enviadas via app | > 40% do total | Contagem por plataforma |
| NPS do app (clientes) | > 50 | Pesquisa in-app trimestral |
| NPS do app (equipe) | > 60 | Pesquisa in-app trimestral |
| Sincronizacao offline bem-sucedida | > 99% | Logs de sincronizacao |
| Tamanho do app | < 50 MB | Google Play Console |
