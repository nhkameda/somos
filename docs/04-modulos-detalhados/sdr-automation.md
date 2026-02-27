# SDR Automation

## Proposito e Visao

O modulo SDR Automation e o braco de abordagem comercial automatizada da Somos Produtora. Ele recebe leads qualificados do modulo Hunter Intelligence e conduz o primeiro contato e as sequencias de follow-up de forma inteligente, personalizada e multicanal, utilizando agentes de inteligencia artificial.

A visao e eliminar o gargalo humano na etapa de prospeccao ativa, permitindo que a equipe comercial se concentre em reunioes e fechamento de negocios, enquanto os agentes de IA realizam o trabalho de volume: abordagem inicial, follow-ups, qualificacao por conversa e agendamento de reunioes.

Cada mensagem enviada e personalizada com base no perfil do lead, seu segmento, sua presenca digital atual e os servicos da Somos Produtora mais relevantes para o contexto. O objetivo nao e enviar mensagens genericas em massa, mas sim criar abordagens que simulem o cuidado de um SDR humano experiente.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Gerente Comercial | Estrategista | Define tom de voz, aprova templates, configura sequencias de abordagem |
| SDR Humano | Supervisor | Monitora conversas, assume quando necessario, valida qualificacoes |
| Agente SDR (IA) | Executor | Envia mensagens, acompanha respostas, qualifica leads por conversa |
| Diretor Criativo | Consultor | Auxilia na criacao de mensagens que refletem o posicionamento da marca |
| Lead/Prospect | Destinatario | Recebe as abordagens e interage com os agentes |

## Funcionalidades Principais

### Gestao de Campanhas de Outreach

- Criacao de campanhas segmentadas por tipo de servico, segmento de mercado ou perfil de lead
- Configuracao de canais por campanha: WhatsApp, LinkedIn, Instagram DM, email ou combinacao multicanal
- Definicao de horarios otimos de envio por canal e segmento
- Limites diarios de envio por canal para evitar bloqueios e manter naturalidade
- A/B testing de mensagens para otimizacao continua de taxas de resposta

### Sequencias de Follow-up Automatizadas

- Cadencias predefinidas com intervalos configuraveis entre cada tentativa de contato
- Sequencia padrao: Dia 1 (primeiro contato) → Dia 3 (follow-up 1) → Dia 7 (follow-up 2) → Dia 14 (ultimo follow-up)
- Escalonamento automatico de canal: se nao responde no LinkedIn, tenta WhatsApp; se nao responde no WhatsApp, tenta email
- Interrupcao automatica da sequencia quando o lead responde
- Reativacao de leads frios apos periodo de cooldown configuravel

### Personalizacao de Mensagens por IA

- Geracao de mensagens unicas para cada lead com base em seu perfil, empresa, presenca digital e segmento
- Referencia especifica a trabalhos recentes da empresa do lead (ex: "Vi que a [empresa] lancou uma nova linha de produtos...")
- Apresentacao contextualizada dos servicos da Somos mais relevantes para cada lead
- Adaptacao de tom de voz conforme o canal (mais formal no LinkedIn, mais casual no Instagram)
- Inclusao de portfolio relevante da Somos (links para cases do mesmo segmento)

### Tracking de Interacoes e Respostas

- Registro de cada mensagem enviada com timestamp, canal, conteudo e status de entrega
- Deteccao de leitura (quando disponivel no canal)
- Classificacao automatica de respostas: Positiva, Negativa, Neutra, Pedido de Informacao, Pedido de Reuniao
- Analise de sentimento das respostas para ajustar a abordagem
- Alerta imediato para SDR humano quando resposta e classificada como "Pedido de Reuniao"

### Qualificacao por Conversa

- Framework de qualificacao BANT adaptado (Budget, Authority, Need, Timeline) aplicado durante a conversa
- Perguntas estrategicas inseridas naturalmente no fluxo de mensagens
- Score de qualificacao atualizado dinamicamente conforme as respostas do lead
- Classificacao final: SQL (Sales Qualified Lead), MQL (Marketing Qualified Lead) ou Desqualificado

## Fluxo de Dados

```
[Lead qualificado recebido do Hunter Intelligence]
        |
        v
[Selecao da campanha e sequencia adequada]
        |
        v
[Geracao de mensagem personalizada (Claude 3.5 Sonnet)]
        |
        v
[Envio pelo canal prioritario]
        |
        v
[Monitoramento de entrega e leitura]
        |
        +---> [Sem resposta] ---> [Aguardar intervalo] ---> [Proximo follow-up]
        |
        +---> [Resposta recebida]
                    |
                    v
            [Analise de sentimento e classificacao]
                    |
                    +---> [Positiva/Reuniao] ---> [Alerta SDR humano + Criar deal no CRM]
                    |
                    +---> [Neutra/Duvida] ---> [Resposta automatica contextual]
                    |
                    +---> [Negativa] ---> [Registrar motivo + Encerrar sequencia]
```

## Entidades e Modelo de Dados

### OutreachCampaign

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico da campanha |
| nome | String | Nome da campanha |
| descricao | Text | Descricao e objetivo da campanha |
| segmento_id | FK(Segment) | Segmento alvo |
| canais | Array[Enum] | WhatsApp, LinkedIn, Instagram, Email |
| sequencia_followup | JSON | Definicao de cadencia e intervalos |
| template_base | Text | Template base para personalizacao |
| horarios_envio | JSON | Janelas de horario por canal |
| limite_diario | Integer | Maximo de envios por dia |
| status | Enum | Rascunho, Ativa, Pausada, Encerrada |
| taxa_abertura | Float | Metrica de abertura/leitura |
| taxa_resposta | Float | Metrica de respostas |
| criada_em | DateTime | Data de criacao |

### Message

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| campanha_id | FK(OutreachCampaign) | Campanha associada |
| lead_id | FK(Lead) | Lead destinatario |
| canal | Enum | WhatsApp, LinkedIn, Instagram, Email |
| conteudo | Text | Texto da mensagem enviada |
| tipo | Enum | PrimeiroContato, FollowUp1, FollowUp2, FollowUp3, Resposta |
| status_envio | Enum | Agendada, Enviada, Entregue, Lida, Falha |
| enviada_em | DateTime | Timestamp de envio |
| lida_em | DateTime | Timestamp de leitura |

### ContactAttempt

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| lead_id | FK(Lead) | Lead contatado |
| campanha_id | FK(OutreachCampaign) | Campanha associada |
| numero_tentativa | Integer | Numero sequencial da tentativa |
| canal | Enum | Canal utilizado |
| resultado | Enum | Entregue, Lido, Respondido, Falha, Bloqueado |
| resposta_lead | Text | Texto da resposta (se houver) |
| classificacao_resposta | Enum | Positiva, Negativa, Neutra, PedidoInfo, PedidoReuniao |
| sentimento | Float | Score de sentimento (-1 a 1) |
| data | DateTime | Data da tentativa |

### Qualification

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| lead_id | FK(Lead) | Lead qualificado |
| budget_score | Integer | Pontuacao de orcamento (0-25) |
| authority_score | Integer | Pontuacao de autoridade decisoria (0-25) |
| need_score | Integer | Pontuacao de necessidade (0-25) |
| timeline_score | Integer | Pontuacao de urgencia (0-25) |
| score_total | Integer | Soma BANT (0-100) |
| classificacao | Enum | SQL, MQL, Desqualificado |
| evidencias | JSON | Trechos de conversa que sustentam a qualificacao |
| qualificado_em | DateTime | Data da qualificacao |

## Interface e UX

### Painel de Campanhas

- Lista de campanhas ativas com metricas resumidas (enviados, abertos, respondidos, reunioes)
- Indicadores visuais de performance relativa entre campanhas
- Botoes de acao rapida: pausar, retomar, duplicar, editar campanha
- Filtros por segmento, canal, status e periodo

### Monitor de Conversas

- Inbox unificado mostrando todas as conversas ativas com leads
- Organizacao por prioridade: respostas pendentes de classificacao no topo
- Visualizacao de thread completa por lead com historico de todos os canais
- Botao de "assumir conversa" para que SDR humano tome controle
- Indicadores visuais de sentimento em cada mensagem

### Construtor de Sequencias

- Interface visual drag-and-drop para montar cadencias de follow-up
- Timeline visual mostrando intervalos entre cada etapa
- Preview de mensagem personalizada com dados de um lead exemplo
- Opcao de testar envio para conta interna antes de ativar

### Dashboard de Metricas

- Funil de conversao: Enviados → Entregues → Lidos → Respondidos → Reunioes
- Graficos comparativos de performance por canal
- Heatmap de melhores horarios de envio
- Ranking de templates/mensagens com maior taxa de resposta

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| WhatsApp Business API | Envio e recebimento de mensagens via WhatsApp | API REST + Webhooks |
| LinkedIn Messaging API | Envio de InMails e mensagens de conexao | OAuth 2.0 + API REST |
| Instagram Direct API (Meta) | Envio e recebimento de DMs | API REST + Webhooks |
| Servico de Email (SendGrid/AWS SES) | Envio de emails com tracking | API REST |
| Modulo Hunter Intelligence | Recebimento de leads qualificados | Interno (fila de eventos) |
| Modulo CRM Pipeline | Criacao de deals quando lead e qualificado como SQL | Interno (evento) |
| Google Calendar | Agendamento de reunioes quando lead aceita | OAuth 2.0 + API REST |

## Agentes de IA

### Agente de Personalizacao de Mensagens

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Gerar mensagens unicas e personalizadas para cada lead com base em seu perfil, contexto e campanha
- **Comportamento**: Recebe template base, dados do lead e portfolio relevante da Somos. Gera mensagem que parece escrita por um humano, com referencias especificas ao contexto do lead. Adapta tom conforme o canal.

### Agente de Analise de Respostas

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Classificar respostas recebidas, analisar sentimento, identificar intencao e determinar proxima acao
- **Comportamento**: Le a resposta do lead no contexto da conversa completa. Classifica (positiva, negativa, neutra, etc.). Sugere ou executa proxima acao (responder, escalar para humano, encerrar sequencia).

### Agente de Qualificacao BANT

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Avaliar leads com base nas informacoes coletadas durante a conversa segundo o framework BANT
- **Comportamento**: Analisa historico completo de interacoes do lead. Extrai evidencias de budget, authority, need e timeline. Gera score e classificacao final com justificativa.

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Backend | Node.js com TypeScript | Excelente para operacoes I/O intensivas e webhooks |
| Fila de Mensagens | BullMQ com Redis | Agendamento preciso de envios e retries |
| Banco de Dados | PostgreSQL | Armazenamento relacional de conversas e metricas |
| Cache de Sessao | Redis | Manter contexto de conversas ativas |
| Webhooks | Express.js | Recebimento de callbacks das APIs de mensageria |
| Orquestrador de IA | LangChain | Gestao de prompts, chains e memoria de conversa |
| Tracking de Email | SendGrid | Open tracking, click tracking, bounce handling |
| Monitoramento | Sentry + Grafana | Deteccao de erros e dashboards de performance |

## Dependencias

- **Modulo Hunter Intelligence**: Fonte primaria de leads para abordagem
- **Modulo CRM Pipeline**: Destino dos leads qualificados como SQL
- **APIs de Mensageria**: Contas ativas e verificadas em WhatsApp Business, LinkedIn, Instagram, email
- **Modulo de Autenticacao**: Controle de acesso por role (gerente, SDR, viewer)
- **Infraestrutura de Filas**: Redis para agendamento de envios e processamento de webhooks

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Taxa de Entrega | Porcentagem de mensagens entregues com sucesso | >= 95% |
| Taxa de Abertura/Leitura | Porcentagem de mensagens lidas pelo destinatario | >= 60% |
| Taxa de Resposta | Porcentagem de leads que responderam | >= 15% |
| Taxa de Resposta Positiva | Porcentagem de respostas classificadas como positivas | >= 40% das respostas |
| Reunioes Agendadas por Semana | Volume de reunioes marcadas via automacao | >= 20/semana |
| Tempo Medio de Resposta | Tempo entre envio e resposta do lead | Metrica de monitoramento |
| Taxa de Conversao para SQL | Porcentagem de leads que se tornam SQL | >= 10% |
| Custo por Reuniao Agendada | Custo operacional por reuniao conseguida | <= R$ 15,00 |
| Taxa de Bloqueio | Porcentagem de contas bloqueadas por plataforma | <= 1% |
| NPS do SDR Humano | Satisfacao da equipe comercial com a qualidade dos leads | >= 8/10 |
