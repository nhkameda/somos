# RH e Gestao de Agentes

## Proposito e Visao

O modulo de RH e Gestao de Agentes e um sistema hibrido unico que gerencia tanto colaboradores humanos quanto agentes de inteligencia artificial da Somos Produtora. Esta abordagem unificada reflete a filosofia da empresa de tratar agentes de IA como membros efetivos da equipe, com responsabilidades, metas e avaliacoes de desempenho analogas as dos funcionarios humanos.

Para agentes de IA, o modulo implementa um ciclo de vida completo: criacao, atribuicao de tarefas, monitoramento de performance, classificacao de erros (leve, medio, grave) e, quando um agente acumula falhas excessivas, sua desativacao seguida da criacao de um novo agente com melhorias baseadas nos erros do antecessor. Este processo evolutivo garante que a qualidade dos agentes melhore continuamente.

Para colaboradores humanos, o modulo oferece funcoes classicas de RH: controle de ponto, gestao de ferias, avaliacao de desempenho, acompanhamento de metas, integracao com folha de pagamento e gestao de freelancers. O Diretor Geral (humano) e a autoridade final em decisoes criticas como substituicao de agentes e desligamento de colaboradores.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Diretor Geral | Autoridade final em substituicao de agentes e decisoes de RH estrategicas | Aprovacao final, acesso total |
| Gerente de RH | Gestao operacional de colaboradores humanos, folha de pagamento, ferias | Gestao completa de humanos |
| Lider Tecnico de IA | Monitora performance dos agentes, configura parametros | Gestao completa de agentes |
| Gerente de Projetos | Atribui tarefas a humanos e agentes, acompanha entregas | Atribuicao de tarefas, visualizacao |
| Colaborador Humano | Registra ponto, visualiza metas, solicita ferias | Autoatendimento |
| Agente de IA (avaliado) | Executa tarefas e reporta status automaticamente | Execucao, reporte de status |
| Agente IA de Monitoramento | Monitora performance dos demais agentes e classifica erros | Leitura de logs, classificacao |

## Funcionalidades Principais

### Gestao de Agentes de IA

#### Ciclo de Vida do Agente
- **Criacao**: Novo agente e criado com configuracao de modelo, prompt base, ferramentas e permissoes
- **Ativacao**: Agente entra em operacao e comeca a receber tarefas
- **Monitoramento**: Performance e rastreada em tempo real (taxa de sucesso, tempo de resposta, qualidade)
- **Avaliacao**: Avaliacoes periodicas automaticas e manuais
- **Evolucao**: Ajustes de prompt, modelo ou parametros com base em feedback
- **Desativacao**: Quando acumula falhas excessivas, e desativado
- **Substituicao**: Novo agente e criado incorporando licoes aprendidas do antecessor

#### Classificacao de Erros de Agentes
- **Erro Leve** (1 ponto): Resposta imprecisa mas funcional, formatacao incorreta, atraso leve
  - Acao: Log registrado, nenhuma acao imediata
  - Exemplo: Agente de conteudo gera texto com tom levemente inadequado
- **Erro Medio** (3 pontos): Tarefa incompleta, dados incorretos, falha de integracao recuperavel
  - Acao: Alerta ao Lider Tecnico, tarefa reenviada
  - Exemplo: Agente de orcamento calcula imposto incorretamente
- **Erro Grave** (10 pontos): Tarefa falhada completamente, dados corrompidos, violacao de regras de negocio
  - Acao: Agente suspenso temporariamente, notificacao ao Diretor Geral
  - Exemplo: Agente envia conteudo nao aprovado para publicacao

#### Sistema de Pontuacao e Desativacao
- Cada agente tem uma pontuacao de saude iniciando em 100
- Erros subtraem pontos conforme classificacao (1, 3 ou 10 pontos)
- Tarefas bem-sucedidas adicionam 1 ponto (maximo 100)
- Quando a pontuacao cai abaixo de 50: agente entra em "observacao" (tarefas reduzidas)
- Quando a pontuacao cai abaixo de 25: agente e desativado automaticamente
- Diretor Geral recebe relatorio e decide sobre substituicao
- Novo agente e criado com prompt melhorado baseado em analise dos erros do antecessor

#### Versionamento de Agentes
- Cada agente tem um historico de versoes (v1.0, v1.1, v2.0)
- Versoes menores (1.x): ajustes de prompt e parametros
- Versoes maiores (x.0): mudanca de modelo ou reestruturacao completa
- Historico de performance por versao para comparacao
- Rollback para versao anterior se nova versao performar pior

### Gestao de Colaboradores Humanos

#### Controle de Ponto
- Registro de entrada e saida via app mobile ou desktop
- Geolocalizacao para trabalho em campo (set de filmagem, locacoes)
- Calculo automatico de horas trabalhadas, extras e banco de horas
- Integracao com calendario de projetos para alocacao de horas

#### Gestao de Ferias e Afastamentos
- Solicitacao de ferias com workflow de aprovacao
- Calculo automatico de saldo de ferias conforme CLT
- Gestao de afastamentos (medico, licenca, etc.)
- Calendario de equipe mostrando disponibilidade

#### Avaliacao de Desempenho
- Avaliacoes trimestrais com criterios configurados por cargo
- Auto-avaliacao + avaliacao do gestor + avaliacao de pares (360 graus)
- Metas SMART com acompanhamento de progresso
- Plano de desenvolvimento individual (PDI)
- Historico de avaliacoes com graficos de evolucao

#### Gestao de Freelancers
- Cadastro de freelancers com especialidades e portfolio
- Historico de projetos e avaliacoes
- Valores de diaria/hora por tipo de servico
- Controle de pagamentos e notas fiscais

#### Integracao com Folha de Pagamento
- Exportacao de dados para sistemas de folha (horas, extras, beneficios)
- Controle de adiantamentos e vales
- Gestao de beneficios (VR, VT, plano de saude)
- Dashboard de custos de pessoal por projeto e departamento

## Fluxo de Dados

```
+==========================================+
|        GESTAO DE AGENTES DE IA           |
+==========================================+
                    |
[Criacao do Agente] --> [Configuracao]
                            |
                    [Ativacao e Operacao]
                            |
                    [Execucao de Tarefas]
                            |
                    [Monitoramento Continuo]
                            |
                +----------+-----------+
                |          |           |
         [Sucesso]   [Erro Leve]  [Erro Medio]  [Erro Grave]
         (+1 ponto)  (-1 ponto)  (-3 pontos)   (-10 pontos)
                |          |           |              |
                +----------+-----------+--------------+
                           |
                    [Score Atualizado]
                           |
              +------------+------------+
              |            |            |
        [Score > 50]  [Score 25-50]  [Score < 25]
        [Normal]      [Observacao]   [Desativado]
                                          |
                                   [Relatorio ao DG]
                                          |
                                   [Decisao Humana]
                                          |
                                   [Novo Agente v+1]
                                   (com melhorias)

+==========================================+
|      GESTAO DE HUMANOS                   |
+==========================================+
                    |
[Admissao] --> [Onboarding]
                    |
            [Operacao Diaria]
            (Ponto, Tarefas, Metas)
                    |
            [Avaliacao Trimestral]
                    |
        +-----------+-----------+
        |           |           |
  [Excelente]  [Satisfatorio] [Insatisfatorio]
  [Bonus/       [Metas         [PDI /
   Promocao]    Ajustadas]      Acompanhamento]
```

## Entidades e Modelo de Dados

### Employee (Colaborador Humano)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome completo |
| email | String | Email corporativo |
| role | String | Cargo |
| department | Enum | PRODUCAO, CRIACAO, COMERCIAL, FINANCEIRO, TI, DIRETORIA |
| employment_type | Enum | CLT, PJ, FREELANCER, ESTAGIARIO |
| hire_date | Date | Data de admissao |
| salary | Decimal (encrypted) | Salario base |
| manager_id | UUID (FK) | Gestor direto |
| is_active | Boolean | Se esta ativo |
| vacation_balance | Integer | Saldo de ferias em dias |

### Agent (Agente de IA)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome do agente (ex: "Curador v2.1") |
| type | Enum | CURADOR, REDATOR, VISUAL, ORCAMENTO, MONITOR, ANALYTICS |
| model | String | Modelo de IA utilizado (claude-opus, gemini-pro) |
| version | String | Versao do agente (ex: "2.1") |
| base_prompt | Text | Prompt base de configuracao |
| tools | JSON | Ferramentas e APIs que o agente pode usar |
| health_score | Integer | Pontuacao de saude (0-100) |
| status | Enum | ATIVO, OBSERVACAO, DESATIVADO, SUBSTITUIDO |
| total_tasks | Integer | Total de tarefas executadas |
| successful_tasks | Integer | Tarefas completadas com sucesso |
| parent_agent_id | UUID (FK) | Agente antecessor (se for evolucao) |
| deactivation_reason | Text | Motivo da desativacao |
| created_at | DateTime | Data de criacao |
| deactivated_at | DateTime | Data de desativacao |

### PerformanceReview (Avaliacao de Desempenho)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| reviewee_type | Enum | HUMAN, AGENT |
| reviewee_id | UUID | ID do avaliado (humano ou agente) |
| reviewer_id | UUID (FK) | ID do avaliador |
| period | String | Periodo da avaliacao (ex: "2026-Q1") |
| overall_score | Decimal | Nota geral (0-10) |
| criteria_scores | JSON | Notas por criterio |
| strengths | Text | Pontos fortes identificados |
| improvements | Text | Pontos de melhoria |
| action_plan | Text | Plano de acao |
| status | Enum | RASCUNHO, EM_ANDAMENTO, FINALIZADA |
| created_at | DateTime | Data da avaliacao |

### Goal (Meta)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| assignee_type | Enum | HUMAN, AGENT |
| assignee_id | UUID | ID do responsavel |
| title | String | Titulo da meta |
| description | Text | Descricao detalhada (SMART) |
| target_value | Decimal | Valor alvo |
| current_value | Decimal | Valor atual |
| unit | String | Unidade de medida |
| deadline | Date | Prazo |
| status | Enum | NAO_INICIADA, EM_ANDAMENTO, CONCLUIDA, ATRASADA |
| weight | Decimal | Peso na avaliacao geral (%) |

### Violation (Violacao / Erro)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| agent_id | UUID (FK) | Agente que cometeu o erro |
| task_id | UUID (FK) | Tarefa onde ocorreu o erro |
| severity | Enum | LEVE, MEDIO, GRAVE |
| points_deducted | Integer | Pontos subtraidos (1, 3 ou 10) |
| description | Text | Descricao do erro |
| context | JSON | Contexto completo (input, output, esperado) |
| resolution | Text | Como foi resolvido |
| created_at | DateTime | Data/hora do erro |

### AgentVersion (Versao do Agente)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| agent_id | UUID (FK) | Agente associado |
| version | String | Numero da versao |
| changes | Text | Descricao das mudancas |
| prompt_diff | Text | Diferenca no prompt em relacao a versao anterior |
| model_changed | Boolean | Se o modelo de IA mudou |
| performance_before | JSON | Metricas antes da mudanca |
| performance_after | JSON | Metricas apos a mudanca (preenchido depois) |
| created_by | UUID (FK) | Quem criou a versao |
| created_at | DateTime | Data da criacao |

## Interface e UX

### Dashboard Principal de RH
- Dois paineis lado a lado: "Equipe Humana" e "Agentes de IA"
- Cards resumidos com total de ativos, em avaliacao, inativos
- Alertas recentes (erros graves de agentes, avaliacoes pendentes)
- Grafico de distribuicao de equipe por departamento

### Painel de Agentes
- Lista de agentes com indicador visual de saude (barra de progresso colorida)
- Filtros por tipo, status e versao
- Clique no agente para ver dashboard individual:
  - Grafico de saude ao longo do tempo
  - Lista de erros recentes com classificacao
  - Historico de versoes com timeline visual
  - Metricas de performance (taxa de sucesso, tempo medio)
- Botao "Criar Nova Versao" com formulario de ajustes

### Painel de Colaboradores
- Lista de colaboradores com filtros por departamento e tipo de contrato
- Perfil individual com historico de avaliacoes, metas, banco de horas
- Formulario de avaliacao de desempenho com criterios por cargo
- Calendario de ferias e afastamentos da equipe

### Tela de Decisao do Diretor Geral
- Fila de decisoes pendentes (substituicao de agentes, aprovacoes de RH)
- Para cada decisao: contexto completo, dados de performance e recomendacao da IA
- Botoes de acao: Aprovar, Rejeitar, Solicitar mais informacoes
- Historico de decisoes com resultados

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| Todos os Modulos | Leitura | Coleta de dados de performance dos agentes em cada modulo |
| Financeiro | Leitura/Escrita | Dados de folha de pagamento e custos de pessoal |
| Projetos | Leitura | Alocacao de recursos humanos e agentes por projeto |
| App Android | Escrita | Notificacoes e registro de ponto mobile |
| Sistema de Folha (externo) | Escrita | Exportacao de dados para processamento de folha |
| Analytics/Reports | Escrita | Metricas de RH para dashboards executivos |

## Agentes de IA

### Agente Monitor de Performance de Agentes
- **Funcao**: Monitora continuamente a performance de todos os outros agentes
- **Modelo**: Claude (analise de logs e classificacao)
- **Monitoramento**: Analisa cada tarefa completada pelos agentes, classifica erros e atualiza scores
- **Saida**: Classificacao de erros, atualizacao de score, alertas quando score atinge limiares
- **Frequencia**: Em tempo real para cada tarefa completada

### Agente Analista de RH
- **Funcao**: Gera insights sobre equipe humana e tendencias
- **Modelo**: Claude (analise de dados de RH)
- **Analises**: Turnover, satisfacao, produtividade por departamento, custos de pessoal
- **Saida**: Relatorios mensais com recomendacoes estrategicas
- **Frequencia**: Mensal com atualizacoes semanais

### Agente Construtor de Agentes
- **Funcao**: Quando um agente e desativado, analisa seus erros e cria configuracao melhorada
- **Modelo**: Claude Opus (analise profunda e geracao de prompt)
- **Entrada**: Historico completo do agente desativado (erros, contextos, padroes)
- **Saida**: Configuracao do novo agente (prompt melhorado, parametros ajustados)
- **Gatilho**: Quando Diretor Geral aprova substituicao de agente

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Frontend | Next.js + React | Dashboards interativos e formularios complexos |
| Graficos | Recharts / Chart.js | Visualizacao de metricas de performance |
| Controle de Ponto | Geolocation API + PWA | Registro de ponto com localizacao |
| Fila de Monitoramento | BullMQ + Redis | Processamento continuo de logs de agentes |
| Banco de Dados | PostgreSQL (Supabase) | Consultas complexas com historico extenso |
| Criptografia | AES-256 | Dados salariais e informacoes sensiveis |
| Logs de Agentes | Elasticsearch (opcional) | Busca e analise de logs em grande volume |
| Notificacoes | Firebase Cloud Messaging | Alertas em tempo real |
| Backend | Node.js + Fastify | APIs para gestao e monitoramento |

## Dependencias

### Dependencias de Modulo
- **Todos os modulos com agentes**: Obrigatoria - cada modulo reporta performance dos seus agentes
- **Financeiro**: Recomendada - custos de pessoal e folha de pagamento
- **Projetos**: Recomendada - alocacao de recursos
- **App Android**: Recomendada - registro de ponto e notificacoes mobile

### Dependencias Tecnicas
- Sistema de logs centralizado para captura de performance dos agentes
- APIs de IA configuradas para os agentes monitores
- Sistema de criptografia para dados sensiveis de RH
- Regras de classificacao de erros configuradas por tipo de agente

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Score medio de saude dos agentes | > 75 | Media dos scores de todos os agentes ativos |
| Taxa de sucesso dos agentes | > 95% | Tarefas bem-sucedidas / total de tarefas |
| Tempo medio de deteccao de erro grave | < 2 minutos | Timestamp do erro vs classificacao |
| Tempo de substituicao de agente | < 24 horas | Desativacao vs ativacao do substituto |
| Performance do agente substituto vs antecessor | Melhoria > 20% | Comparacao de taxa de sucesso |
| Turnover de colaboradores humanos | < 15% anual | Desligamentos / total de colaboradores |
| Indice de satisfacao dos colaboradores | > 4.0/5.0 | Pesquisa trimestral |
| Avaliacoes de desempenho realizadas no prazo | 100% | Avaliacoes feitas / avaliacoes previstas |
| Precisao da classificacao de erros | > 90% | Auditoria manual amostral |
