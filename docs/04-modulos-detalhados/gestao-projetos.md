# Gestao de Projetos

## Proposito e Visao

O modulo de Gestao de Projetos e o centro operacional da Somos Produtora. Ele organiza e acompanha a execucao de todos os projetos contratados, desde videos institucionais e campanhas de redes sociais ate cursos online e eventos corporativos. O modulo suporta multiplas metodologias de visualizacao e gestao, adaptando-se a natureza de cada tipo de projeto.

A visao e oferecer uma plataforma que permita gerenciar a complexidade inerente a producao audiovisual, onde cada projeto envolve etapas criativas, logisticas e tecnicas, com equipes hibridas compostas por profissionais internos, freelancers e agentes de IA. O gestor deve ter visibilidade completa do progresso, prazos, entregaveis e alocacao de recursos em tempo real.

O diferencial deste modulo e sua compreensao das particularidades da producao audiovisual: etapas de pre-producao (briefing, roteiro, storyboard), producao (captacao, direcao) e pos-producao (edicao, color grading, sound design, motion graphics) sao nativas do sistema, com templates de projeto especificos para cada tipo de entrega.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Produtor Executivo | Gestor Principal | Cria projetos, define escopos, aloca equipe, monitora progresso |
| Diretor Criativo | Lider Criativo | Aprova entregas criativas, define briefings, dirige producoes |
| Editor de Video | Executor | Executa tarefas de edicao, entrega cortes para aprovacao |
| Designer / Motion Designer | Executor | Cria artes, animacoes, graficos para os projetos |
| Social Media Manager | Executor | Gerencia entregas de conteudo para redes sociais |
| Freelancer | Executor Externo | Executa tarefas especificas conforme contratacao |
| Agente de IA | Executor Automatizado | Executa tarefas automatizaveis (transcricao, legendas, pesquisa) |
| Cliente | Aprovador | Revisa e aprova entregas, fornece feedback |
| Gerente de Conta | Intermediario | Ponte entre cliente e equipe de producao |

## Funcionalidades Principais

### Criacao e Configuracao de Projetos

- Criacao automatica de projeto a partir de contrato assinado no modulo de Gestao de Contratos
- Templates de projeto por tipo: Video Institucional, Comercial de TV, Social Media Mensal, Curso Online, Cobertura de Evento, Campanha Publicitaria, Documentario
- Cada template inclui estrutura predefinida de tarefas, etapas e entregaveis tipicos
- Campos personalizaveis: cliente, responsavel, data inicio, data entrega, orcamento, descricao, briefing
- Vinculacao com deal/contrato de origem para rastreabilidade completa

### Gestao de Tarefas e Sprints

- Criacao de tarefas com titulo, descricao, responsavel, prazo, prioridade, estimativa de horas
- Organizacao de tarefas em sprints semanais ou quinzenais
- Subtarefas para quebrar trabalho complexo em etapas menores
- Checklist dentro de cada tarefa para itens especificos
- Labels customizaveis: Pre-producao, Producao, Pos-producao, Aprovacao, Entrega
- Dependencias entre tarefas (tarefa B so pode iniciar quando tarefa A estiver concluida)
- Atribuicao multipla: humanos e agentes de IA na mesma tarefa

### Visualizacoes Multiplas

- **Kanban Board**: Colunas de status (A Fazer, Em Andamento, Em Revisao, Aprovado, Concluido) com cards arrastavel
- **Calendario**: Visualizacao mensal/semanal com tarefas posicionadas por prazo
- **Timeline (Gantt)**: Barras horizontais mostrando duracao de cada tarefa com dependencias visuais
- **Lista**: Visao tabular com ordenacao e filtros avancados
- **Dashboard do Projeto**: Visao consolidada com progresso, metricas e alertas

### Entregaveis e Aprovacao

- Definicao de entregaveis por projeto com descricao, formato, prazo e responsavel
- Upload de arquivos vinculados ao entregavel (videos, imagens, documentos)
- Fluxo de aprovacao configuravel: revisao interna → revisao do cliente → aprovacao final
- Sistema de comentarios e anotacoes diretamente nos entregaveis
- Versionamento de entregaveis (V1, V2, V3...) com historico de feedback
- Notificacao automatica para o proximo aprovador da fila

### Alocacao de Equipe

- Visao de disponibilidade da equipe por periodo
- Alocacao de horas por membro em cada projeto
- Identificacao de sobrecarga (membro com mais horas alocadas do que disponiveis)
- Mix de equipe interna, freelancers e agentes de IA
- Registro de especialidades por membro para sugestao inteligente de alocacao

### Status Reports Automaticos

- Geracao automatica de relatorio de status por projeto com frequencia configuravel
- Conteudo do relatorio: progresso geral, tarefas concluidas no periodo, tarefas atrasadas, proximos marcos, riscos identificados
- Envio automatico por email para stakeholders configurados
- Versao visual (dashboard) e versao PDF exportavel

## Fluxo de Dados

```
[Contrato assinado no modulo Gestao de Contratos]
        |
        v
[Criacao automatica do projeto com template adequado]
        |
        v
[Produtor Executivo refina escopo, tarefas e equipe]
        |
        v
[Sprint planejado com tarefas atribuidas]
        |
        v
[Equipe executa tarefas, atualiza status]
        |
        v
[Entregaveis produzidos e enviados para revisao]
        |
        v
[Revisao interna pelo Diretor Criativo]
        |
        +---> [Ajustes necessarios] ---> [Retorna para equipe com feedback]
        |
        v
[Envio para aprovacao do cliente]
        |
        +---> [Cliente solicita alteracoes] ---> [Nova iteracao]
        |
        v
[Entregavel aprovado e finalizado]
        |
        v
[Projeto concluido, Status Report final gerado]
        |
        v
[Notificacao para Financeiro (faturamento final)]
```

## Entidades e Modelo de Dados

### Project

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do projeto |
| descricao | Text | Descricao detalhada |
| cliente_id | FK(Company) | Empresa cliente |
| contrato_id | FK(Contract) | Contrato de origem |
| deal_id | FK(Deal) | Deal de origem no CRM |
| tipo | Enum | VideoInstitucional, ComercialTV, SocialMedia, CursoOnline, Evento, Campanha, Documentario |
| status | Enum | Planejamento, EmAndamento, Pausado, EmRevisao, Concluido, Cancelado |
| responsavel_id | FK(User) | Produtor executivo responsavel |
| data_inicio | Date | Data de inicio |
| data_entrega | Date | Data prevista de entrega |
| orcamento | Decimal | Orcamento aprovado |
| custo_atual | Decimal | Custo acumulado ate o momento |
| progresso | Integer | Porcentagem de conclusao (0-100) |
| briefing | Text | Briefing do cliente |
| criado_em | DateTime | Data de criacao |

### Task

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| projeto_id | FK(Project) | Projeto ao qual pertence |
| sprint_id | FK(Sprint) | Sprint associado (opcional) |
| titulo | String | Titulo da tarefa |
| descricao | Text | Descricao detalhada |
| responsavel_id | FK(TeamMember) | Responsavel pela execucao |
| status | Enum | AFazer, EmAndamento, EmRevisao, Aprovado, Concluido, Bloqueado |
| prioridade | Enum | Critica, Alta, Media, Baixa |
| etapa_producao | Enum | PreProducao, Producao, PosProducao, Entrega |
| data_inicio | Date | Data de inicio prevista |
| data_prazo | Date | Data limite |
| horas_estimadas | Float | Estimativa de horas |
| horas_realizadas | Float | Horas efetivamente gastas |
| dependencias | Array[FK(Task)] | Tarefas que devem ser concluidas antes |
| subtarefas | Array[JSON] | Lista de subtarefas com status |
| labels | Array[String] | Tags de classificacao |
| anexos | Array[String] | URLs de arquivos anexados |
| ordem | Integer | Ordem de exibicao |

### Sprint

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| projeto_id | FK(Project) | Projeto associado |
| nome | String | Nome do sprint (ex: "Sprint 3 - Edicao") |
| data_inicio | Date | Inicio do sprint |
| data_fim | Date | Fim do sprint |
| objetivo | Text | Objetivo do sprint |
| status | Enum | Planejado, Ativo, Concluido |
| velocidade | Float | Pontos/horas concluidos |

### Milestone

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| projeto_id | FK(Project) | Projeto associado |
| nome | String | Nome do marco |
| descricao | Text | Descricao do marco |
| data_prevista | Date | Data prevista para atingir |
| data_atingida | Date | Data efetivamente atingida |
| status | Enum | Pendente, Atingido, Atrasado |

### Deliverable

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| projeto_id | FK(Project) | Projeto associado |
| nome | String | Nome do entregavel |
| descricao | Text | Descricao do que deve ser entregue |
| formato | String | Formato do arquivo (MP4, MOV, PSD, PDF, etc.) |
| versao_atual | Integer | Numero da versao atual |
| arquivo_url | String | URL do arquivo mais recente |
| status | Enum | EmProducao, EmRevisaoInterna, EnviadoCliente, AlteracoesSolicitadas, Aprovado, Entregue |
| aprovado_por | FK(User) | Quem aprovou |
| aprovado_em | DateTime | Data de aprovacao |
| historico_versoes | JSON | Historico de versoes com URLs e feedback |

### TeamMember

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do membro |
| tipo | Enum | Interno, Freelancer, AgenteIA |
| funcao | String | Funcao (Editor, Designer, Diretor, etc.) |
| especialidades | Array[String] | Lista de especialidades |
| horas_disponiveis_semana | Float | Capacidade semanal |
| custo_hora | Decimal | Custo por hora (para calculo de margem) |
| usuario_id | FK(User) | Vinculo com usuario do sistema (se interno) |
| fornecedor_id | FK(Supplier) | Vinculo com fornecedor (se freelancer) |
| ativo | Boolean | Status de disponibilidade |

## Interface e UX

### Dashboard do Projeto

- Barra de progresso geral com porcentagem de conclusao
- Cards com KPIs: tarefas concluidas/total, dias restantes, orcamento utilizado/total, horas gastas
- Mini-timeline com marcos e proximas entregas
- Lista de tarefas atrasadas com destaque em vermelho
- Feed de atividades recentes (quem fez o que, quando)

### Kanban Board

- Colunas de status com cards de tarefa arrastavel
- Filtros por responsavel, etapa de producao, prioridade e sprint
- Quick-add de tarefa diretamente na coluna
- Avatar do responsavel no card, badge de prioridade, indicador de prazo

### Calendario

- Visualizacao mensal com tarefas e marcos posicionados na data de prazo
- Cores por projeto (quando visualizando multiplos projetos)
- Click no dia para criar nova tarefa com data pre-preenchida
- Indicador de carga de trabalho por dia (leve, moderada, pesada)

### Timeline (Gantt)

- Barras horizontais por tarefa mostrando inicio e fim
- Linhas de dependencia conectando tarefas relacionadas
- Linha vermelha indicando data atual (hoje)
- Ajuste de datas por drag nas extremidades das barras
- Agrupamento por sprint ou por etapa de producao

### Tela de Entregavel

- Visualizacao do arquivo (player de video embutido, viewer de imagem, preview de PDF)
- Painel lateral com historico de versoes e feedback
- Area de comentarios com opcao de marcar posicao especifica (timestamp no video, area na imagem)
- Botoes de acao: Aprovar, Solicitar Alteracoes, Rejeitar
- Indicador de fluxo de aprovacao (quem ja aprovou, quem falta)

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| Modulo Gestao de Contratos | Criacao automatica de projeto apos assinatura | Interno (evento) |
| Modulo CRM Pipeline | Referencia ao deal e cliente | Interno (consulta) |
| Modulo Financeiro/ERP | Acompanhamento de orcamento e custos | Interno (bidirecional) |
| Modulo Inventario | Reserva de equipamentos para producoes | Interno (acao) |
| Modulo Fornecedores | Alocacao de freelancers e servicos terceirizados | Interno (consulta) |
| Google Drive | Armazenamento e compartilhamento de arquivos | OAuth 2.0 + API |
| Frame.io / Vimeo Review | Revisao colaborativa de videos | API REST |
| Slack / Discord | Notificacoes de equipe e comunicacao | Webhook |
| Google Calendar | Sincronizacao de prazos e eventos de producao | OAuth 2.0 + API |

## Agentes de IA

### Agente de Geracao de Status Report

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Analisar o estado atual do projeto e gerar relatorio de status claro e informativo para stakeholders
- **Comportamento**: Coleta dados de tarefas, progresso, marcos e atividades recentes. Gera relatorio em linguagem profissional destacando conquistas, atrasos, riscos e proximos passos.

### Agente de Alocacao Inteligente

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Sugerir alocacao otima de equipe para novas tarefas com base em disponibilidade, especialidade e carga atual
- **Comportamento**: Analisa a tarefa, identifica especialidades necessarias, verifica disponibilidade dos membros e sugere o profissional mais adequado.

### Agente de Estimativa de Prazo

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Estimar prazos realistas para tarefas com base em historico de projetos similares
- **Comportamento**: Compara a tarefa com tarefas similares em projetos anteriores, considera complexidade e disponibilidade do responsavel, sugere prazo com intervalo de confianca.

### Agentes de Execucao Automatizada

- **Modelos**: Whisper (transcricao), modelos de legendagem, GPT-4o (pesquisa e redacao)
- **Funcao**: Executar tarefas automatizaveis dentro do projeto
- **Comportamento**: Transcricao automatica de entrevistas gravadas, geracao de legendas, pesquisa de referencias, redacao de roteiros base, criacao de thumbnails.

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Frontend | React com TypeScript | Componentes interativos para board, timeline e calendario |
| Kanban | dnd-kit | Drag-and-drop performatico e acessivel |
| Gantt/Timeline | dhtmlxGantt ou custom com D3.js | Visualizacao de timeline com dependencias |
| Calendario | FullCalendar | Componente de calendario robusto e customizavel |
| Player de Video | Video.js ou Plyr | Player customizavel para preview de entregaveis |
| Backend | Node.js com TypeScript | Consistencia com stack do projeto |
| Real-time | WebSockets (Socket.io) | Atualizacao em tempo real do board e atividades |
| Armazenamento | AWS S3 + CloudFront | Arquivos grandes (videos) com CDN para entrega |
| Banco de Dados | PostgreSQL | Relacionamentos complexos entre entidades |

## Dependencias

- **Modulo Gestao de Contratos**: Trigger de criacao de projeto
- **Modulo CRM Pipeline**: Dados do cliente e deal
- **Modulo Financeiro/ERP**: Orcamento aprovado e tracking de custos
- **Modulo Inventario**: Disponibilidade de equipamentos
- **Modulo Fornecedores**: Base de freelancers e servicos
- **Servico de Armazenamento**: Para upload de entregaveis (videos, imagens)
- **Servico de Autenticacao**: Controle de acesso por role e projeto

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| On-Time Delivery | Porcentagem de projetos entregues no prazo | >= 85% |
| On-Budget | Porcentagem de projetos dentro do orcamento | >= 90% |
| Ciclos de Revisao | Numero medio de revisoes por entregavel | <= 3 |
| Satisfacao do Cliente | NPS ou rating de satisfacao pos-projeto | >= 9/10 |
| Utilizacao da Equipe | Horas produtivas / horas disponiveis | >= 75% |
| Tarefas Concluidas por Sprint | Volume de tarefas finalizadas por sprint | Acompanhamento |
| Atraso Medio | Dias de atraso medio nas entregas | <= 2 dias |
| Adocao da Plataforma | Porcentagem da equipe usando ativamente o sistema | >= 95% |
