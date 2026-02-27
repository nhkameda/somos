# CRM Pipeline

## Proposito e Visao

O modulo CRM Pipeline e o coracao da gestao comercial da Somos Produtora. Ele centraliza todas as oportunidades de negocio em um funil visual e intuitivo, permitindo que a equipe comercial acompanhe cada deal desde a descoberta ate o fechamento, com visibilidade completa sobre interacoes, valores, probabilidades e proximos passos.

A visao e oferecer uma experiencia de CRM construida especificamente para o contexto de uma produtora audiovisual e agencia 360, com campos, etapas e automacoes que refletem a realidade do mercado de comunicacao. Diferente de CRMs genericos, este modulo entende que um deal pode envolver producao de video institucional, gestao de redes sociais, criacao de curso online ou organizacao de evento, e adapta o fluxo de informacoes conforme o tipo de servico.

O pipeline visual estilo Kanban permite arrasto de cards entre etapas, com atualizacao em tempo real e dashboards de conversao que auxiliam na tomada de decisao estrategica.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Diretor Comercial | Estrategista | Define metas, analisa pipeline, forecast de receita |
| Gerente de Novos Negocios | Executor | Conduz negociacoes, move deals no funil, agenda reunioes |
| SDR | Alimentador | Encaminha leads qualificados, cria deals iniciais |
| Produtor Executivo | Consultor | Auxilia na precificacao e escopo tecnico das propostas |
| Financeiro | Observer | Acompanha valores e previsao de faturamento |
| CEO | Sponsor | Visao macro do pipeline e saude comercial da empresa |

## Funcionalidades Principais

### Kanban Visual do Pipeline

- Board com colunas representando cada estagio do funil comercial
- Estagios padrao: Descoberto → Contatado → Qualificado → Reuniao Agendada → Apresentado → Negociando → Ganho / Perdido
- Cards de deal com informacoes resumidas: nome da empresa, valor estimado, servico principal, responsavel, dias na etapa
- Drag-and-drop para mover deals entre estagios
- Codificacao por cores conforme prioridade e tempo na etapa (alerta para deals estagnados)
- Soma de valores por coluna com previsao ponderada de receita

### Gestao de Contatos e Empresas

- Cadastro completo de empresas com razao social, nome fantasia, CNPJ, segmento, porte, endereco, website e redes sociais
- Cadastro de contatos vinculados a empresas com nome, cargo, email, telefone, LinkedIn, Instagram
- Historico completo de interacoes por contato (emails, ligacoes, reunioes, mensagens)
- Relacionamento N:N entre contatos e empresas (um contato pode estar em multiplas empresas)
- Importacao de contatos via CSV e integracao com fontes externas

### Historico de Atividades

- Registro automatico de todas as interacoes: emails enviados/recebidos, ligacoes, reunioes, mensagens de WhatsApp
- Notas manuais adicionadas pela equipe com formatacao rica
- Timeline visual por deal mostrando toda a evolucao cronologica
- Anexos de arquivos (propostas, briefings, referencias) vinculados ao deal
- Tags e categorias para facilitar busca e filtragem

### Dashboard em Tempo Real

- Visao macro do pipeline com valor total por estagio
- Taxa de conversao entre estagios (funil de conversao)
- Velocity: tempo medio de permanencia em cada estagio
- Forecast de receita: previsao baseada em probabilidade por estagio
- Ranking de responsaveis por volume e valor de deals
- Comparativo de periodos (mes atual vs anterior, trimestre vs trimestre)

### Automacoes e Alertas

- Alerta quando deal permanece na mesma etapa por mais de X dias
- Notificacao automatica quando lead do Hunter/SDR vira deal
- Lembrete de follow-up configuravel por deal
- Mudanca automatica de estagio com base em eventos (ex: contrato assinado → Ganho)
- Relatorio semanal automatico enviado por email para gestores

## Fluxo de Dados

```
[Lead qualificado (Hunter/SDR)]
        |
        v
[Criacao do Deal no estagio "Descoberto"]
        |
        v
[Primeiro contato realizado] ---> Estagio: "Contatado"
        |
        v
[Qualificacao BANT confirmada] ---> Estagio: "Qualificado"
        |
        v
[Reuniao marcada via calendario] ---> Estagio: "Reuniao Agendada"
        |
        v
[Proposta/Portfolio apresentado] ---> Estagio: "Apresentado"
        |
        v
[Negociacao de valores e escopo] ---> Estagio: "Negociando"
        |
        +---> [Acordo fechado] ---> Estagio: "Ganho" ---> [Criar Contrato + Projeto]
        |
        +---> [Sem acordo] ---> Estagio: "Perdido" ---> [Registrar motivo]
```

## Entidades e Modelo de Dados

### Deal

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico do deal |
| titulo | String | Nome descritivo da oportunidade |
| empresa_id | FK(Company) | Empresa associada |
| contato_principal_id | FK(Contact) | Contato decisor |
| pipeline_id | FK(Pipeline) | Pipeline ao qual pertence |
| estagio_id | FK(Stage) | Estagio atual no funil |
| valor_estimado | Decimal | Valor estimado do deal em reais |
| moeda | Enum | BRL, USD, EUR |
| probabilidade | Integer | Probabilidade de fechamento (0-100%) |
| servicos | Array[String] | Tipos de servico envolvidos |
| responsavel_id | FK(User) | Responsavel comercial |
| fonte | Enum | Hunter, SDR, Indicacao, Inbound, Evento |
| data_previsao_fechamento | Date | Previsao de quando sera fechado |
| motivo_perda | String | Razao do deal perdido (quando aplicavel) |
| notas | Text | Observacoes gerais |
| criado_em | DateTime | Data de criacao |
| atualizado_em | DateTime | Ultima atualizacao |

### Contact

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome completo |
| cargo | String | Cargo na empresa |
| email | String | Email profissional |
| telefone | String | Telefone com DDD |
| linkedin_url | String | Perfil do LinkedIn |
| instagram_url | String | Perfil do Instagram |
| empresa_ids | Array[FK(Company)] | Empresas vinculadas |
| tags | Array[String] | Tags de classificacao |
| avatar_url | String | Foto do contato |
| criado_em | DateTime | Data de cadastro |

### Company

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| razao_social | String | Razao social |
| nome_fantasia | String | Nome fantasia |
| cnpj | String | CNPJ formatado |
| segmento | String | Segmento de mercado |
| porte | Enum | MEI, ME, EPP, Media, Grande |
| website | String | URL do site |
| endereco | JSON | Endereco completo |
| redes_sociais | JSON | Links de redes sociais |
| faturamento_estimado | Decimal | Faturamento anual estimado |
| total_deals | Integer | Contador de deals com a empresa |
| valor_total_deals | Decimal | Soma de valores de deals |

### Pipeline

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do pipeline |
| descricao | Text | Descricao do pipeline |
| estagios | Array[FK(Stage)] | Estagios ordenados |
| ativo | Boolean | Status do pipeline |

### Stage

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do estagio |
| ordem | Integer | Posicao no pipeline |
| pipeline_id | FK(Pipeline) | Pipeline ao qual pertence |
| probabilidade_padrao | Integer | Probabilidade default (%) |
| cor | String | Cor de exibicao (hex) |
| alerta_dias | Integer | Dias para alerta de estagnacao |

### Activity

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| deal_id | FK(Deal) | Deal associado |
| contato_id | FK(Contact) | Contato envolvido |
| tipo | Enum | Email, Ligacao, Reuniao, Mensagem, Nota, Arquivo |
| descricao | Text | Descricao da atividade |
| anexos | Array[String] | URLs de arquivos anexados |
| autor_id | FK(User) | Quem registrou a atividade |
| automatica | Boolean | Se foi registrada automaticamente |
| criada_em | DateTime | Data da atividade |

## Interface e UX

### Board Kanban Principal

- Colunas lado a lado com scroll horizontal, cada coluna representando um estagio
- Cards compactos com: logo da empresa, titulo do deal, valor, responsavel (avatar), dias na etapa
- Badge de alerta em cards estagnados (vermelho) ou proximos do prazo (amarelo)
- Header de cada coluna com soma de valores e contagem de deals
- Filtros globais: por responsavel, por servico, por valor, por periodo

### Detalhe do Deal

- Painel lateral (drawer) que abre ao clicar no card
- Secoes colapsaveis: Informacoes Gerais, Contatos, Timeline, Tarefas, Arquivos
- Edicao inline de campos
- Botoes de acao: mover estagio, agendar atividade, gerar proposta, criar contrato

### Tela de Contatos

- Lista pesquisavel e filtravel de todos os contatos
- Card de contato com foto, dados de contato, empresas vinculadas e deals associados
- Historico de interacoes unificado por contato (todos os deals)

### Dashboard Comercial

- KPIs no topo: valor total do pipeline, deals abertos, taxa de conversao, ticket medio
- Grafico de funil com taxas de conversao entre estagios
- Grafico de barras com receita prevista por mes
- Grafico de linhas com evolucao de deals ganhos vs perdidos ao longo do tempo

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| Modulo Hunter Intelligence | Recepcao de leads para criacao de deals | Interno (evento) |
| Modulo SDR Automation | Recepcao de leads qualificados (SQL) | Interno (evento) |
| Modulo Gestao de Contratos | Geracao de contrato a partir do deal ganho | Interno (acao) |
| Modulo Gestao de Projetos | Criacao de projeto a partir do deal ganho | Interno (acao) |
| Modulo Financeiro/ERP | Previsao de receita e faturamento | Interno (consulta) |
| Google Calendar | Agendamento de reunioes vinculadas ao deal | OAuth 2.0 + API |
| Google Workspace / Outlook | Sincronizacao de emails trocados com contatos | API |
| WhatsApp Business | Historico de mensagens com contatos | Webhook |

## Agentes de IA

### Agente de Resumo de Deal

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Gerar resumos executivos de deals com base no historico de atividades, facilitando handoff entre responsaveis
- **Comportamento**: Ao solicitar, analisa toda a timeline do deal e gera um resumo conciso com status atual, proximos passos sugeridos e riscos identificados

### Agente de Forecast

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Analisar o pipeline completo e gerar previsoes de receita baseadas em padroes historicos e dados atuais
- **Comportamento**: Considera velocity por estagio, sazonalidade, segmento e historico de conversao para gerar forecast com intervalos de confianca

### Agente de Sugestao de Proximos Passos

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Sugerir acoes para avancamento de deals estagnados com base em padroes de sucesso
- **Comportamento**: Analisa deals estagnados, compara com deals que avancaram em contextos similares e sugere acoes especificas

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Frontend | React com TypeScript | Componentizacao ideal para board Kanban complexo |
| State Management | Zustand ou Jotai | Leve e performatico para atualizacoes em tempo real |
| Drag-and-Drop | dnd-kit | Biblioteca moderna e acessivel para drag-and-drop |
| Backend | Node.js com TypeScript | Consistencia com stack do projeto |
| Real-time | WebSockets (Socket.io) | Atualizacao em tempo real do board entre usuarios |
| Banco de Dados | PostgreSQL | Queries complexas para dashboards e relatorios |
| Graficos | Recharts ou Nivo | Graficos interativos e responsivos para dashboards |
| Busca | Elasticsearch (opcional) | Busca full-text em contatos, empresas e atividades |

## Dependencias

- **Modulo Hunter Intelligence**: Fonte de leads para criacao de deals
- **Modulo SDR Automation**: Fonte de leads qualificados (SQL)
- **Modulo Gestao de Contratos**: Geracao automatica de contrato ao ganhar deal
- **Modulo Gestao de Projetos**: Criacao de projeto ao ganhar deal
- **Modulo Financeiro/ERP**: Dados de faturamento e previsao de receita
- **Servico de Autenticacao**: Controle de acesso por role e visibilidade de deals

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Taxa de Conversao Global | Deals ganhos / deals criados | >= 25% |
| Ticket Medio | Valor medio dos deals ganhos | >= R$ 25.000 |
| Velocity do Pipeline | Tempo medio entre criacao e fechamento | <= 30 dias |
| Deals Estagnados | Porcentagem de deals sem atividade ha mais de 7 dias | <= 15% |
| Forecast Accuracy | Precisao da previsao de receita vs realizado | >= 80% |
| Adocao do CRM | Porcentagem de deals com atividades registradas | >= 90% |
| Receita Mensal do Pipeline | Valor total de deals ganhos por mes | Meta variavel |
| Tempo de Resposta | Tempo medio entre contato do lead e primeira acao no CRM | <= 2 horas |
