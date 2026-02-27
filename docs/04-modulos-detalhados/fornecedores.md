# Fornecedores

## Proposito e Visao

O modulo de Fornecedores e o diretorio inteligente da Somos Produtora para gestao de toda a rede de parceiros externos: freelancers especializados, fornecedores de equipamentos, locadoras, servicos de pos-producao terceirizados, locacoes, catering, transporte e demais servicos necessarios para a operacao de uma produtora audiovisual e agencia 360.

A visao e criar uma base de dados rica e qualificada que permita encontrar rapidamente o profissional ou fornecedor ideal para cada necessidade, com informacoes atualizadas sobre precos, disponibilidade, especializacoes e avaliacoes de trabalhos anteriores. Essa base alimenta diretamente os modulos de Gestao de Projetos (alocacao de freelancers), Financeiro (contas a pagar) e Inventario (aluguel de equipamentos).

Em uma produtora, a rede de fornecedores e um dos ativos mais valiosos. Saber quem e o melhor colorista da cidade, qual estudio tem o melhor custo-beneficio para locacao, ou qual freelancer de drone esta disponivel na proxima semana e informacao critica que precisa estar organizada e acessivel, nao dispersa em contatos de WhatsApp e anotacoes pessoais.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Produtor Executivo | Consumidor Principal | Busca fornecedores para compor equipes e orcamentos de producao |
| Gerente de Operacoes | Gestor | Cadastra, atualiza e qualifica fornecedores, negocia condicoes |
| Financeiro | Pagador | Processa pagamentos, registra contratos e tabelas de preco |
| Diretor Criativo | Consultor | Recomenda profissionais criativos baseado em qualidade de portfolio |
| Fornecedor/Freelancer | Cadastrado | Mantem seus dados atualizados, envia propostas, recebe avaliacoes |
| Administrador | Configurador | Gerencia categorias, campos customizados e regras de avaliacao |

## Funcionalidades Principais

### Cadastro Completo de Fornecedores

- Registro com dados pessoais/empresariais: nome, razao social, CPF/CNPJ, endereco, telefone, email, redes sociais
- Classificacao por tipo: Freelancer (pessoa fisica), Empresa de Servicos, Locadora de Equipamentos, Estudio, Locacao, Alimentacao, Transporte, Grafica, Outros
- Categorias de especialidade por tipo de fornecedor:
  - **Freelancers criativos**: Diretor de fotografia, Editor de video, Colorista, Sound designer, Motion designer, Animator 3D, Roteirista, Diretor
  - **Freelancers tecnicos**: Operador de camera, Operador de drone, Tecnico de audio, Eletricista de set, Maquiador, Figurinista, Cenografo
  - **Servicos de pos-producao**: Color grading, Finalizacao, Masterizacao de audio, Efeitos visuais, Legendagem, Traducao
  - **Infraestrutura**: Estuidos para filmagem, Ilhas de edicao, Estuidos de audio, Locacoes externas
  - **Logistica**: Catering, Transporte de equipe, Transporte de equipamento, Seguro de producao
- Portfolio/links de trabalhos anteriores
- Documentos: contrato social, certificados, seguro de equipamento, MEI/CNPJ ativo
- Status: Ativo, Inativo, Bloqueado, Em Avaliacao

### Tabelas de Preco

- Cada fornecedor pode ter uma ou mais tabelas de preco por tipo de servico
- Valores por diaria, por hora, por projeto ou por entrega
- Valores diferenciados por tipo de producao (comercial, institucional, evento, redes sociais)
- Historico de precos com datas de vigencia
- Comparativo de precos entre fornecedores para o mesmo servico
- Negociacao de pacotes e descontos por volume registrados no sistema
- Tabela de referencia de mercado para validacao de precos

### Gestao de Disponibilidade

- Calendario de disponibilidade por fornecedor (atualizado pelo proprio ou pelo gestor)
- Marcacao de periodos indisponiveis (ferias, outros compromissos)
- Consulta rapida: "Quais coloristas estao disponiveis na semana de 15/03?"
- Reserva de disponibilidade vinculada a projeto (soft booking)
- Confirmacao de booking com notificacao automatica ao fornecedor
- Integracao com modulo de Gestao de Projetos para alocacao

### Sistema de Avaliacoes e Ratings

- Avaliacao apos cada projeto com nota de 1 a 5 em multiplos criterios
- Criterios de avaliacao: Qualidade da entrega, Pontualidade, Comunicacao, Custo-beneficio, Proatividade
- Media ponderada geral visivel no perfil do fornecedor
- Comentarios textuais vinculados a cada avaliacao
- Historico de avaliacoes visivel para tomada de decisao
- Ranking de fornecedores por categoria e nota
- Flag de fornecedores "preferidos" pela equipe

### Contratos com Fornecedores

- Registro de contratos ativos com cada fornecedor (termos, valores, vigencia)
- Modelos de contrato: por projeto, contrato mensal, acordo de confidencialidade (NDA)
- Alertas de vencimento de contrato para renovacao
- Vinculacao de pagamentos ao contrato vigente
- Historico de todos os contratos com o fornecedor

### Busca e Filtros Avancados

- Busca por especialidade, disponibilidade, faixa de preco, avaliacao minima, localizacao
- Filtros combinados: "Editores de video com nota >= 4, disponivel em marco, diaria ate R$ 800"
- Sugestoes inteligentes baseadas no tipo de projeto e historico de contratacoes
- Busca por proximidade geografica para producoes em locacao
- Resultados ordenados por relevancia (combina nota, preco e disponibilidade)

## Fluxo de Dados

```
[Novo fornecedor identificado (indicacao, prospeccao, auto-cadastro)]
        |
        v
[Cadastro no sistema com dados completos]
        |
        v
[Verificacao de documentacao e status fiscal]
        |
        v
[Cadastro de tabela de precos]
        |
        v
[Fornecedor disponivel para consultas]
        |
        v
[Produtor busca fornecedor para projeto]
        |
        v
[Selecao e reserva de disponibilidade]
        |
        v
[Alocacao confirmada no modulo Gestao de Projetos]
        |
        v
[Execucao do servico durante o projeto]
        |
        v
[Registro de custo no modulo Financeiro]
        |
        v
[Avaliacao pos-projeto]
        |
        v
[Atualizacao do rating e historico do fornecedor]
```

## Entidades e Modelo de Dados

### Supplier (Fornecedor)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome completo ou razao social |
| nome_artistico | String | Nome artistico ou nome fantasia |
| tipo_pessoa | Enum | PessoaFisica, PessoaJuridica |
| cpf_cnpj | String | CPF ou CNPJ |
| email | String | Email de contato |
| telefone | String | Telefone principal |
| whatsapp | String | Numero de WhatsApp |
| endereco | JSON | Endereco completo com CEP |
| cidade | String | Cidade (para busca por proximidade) |
| estado | String | Estado |
| tipo_fornecedor | Enum | Freelancer, EmpresaServicos, LocadoraEquipamentos, Estudio, Locacao, Alimentacao, Transporte, Grafica, Outros |
| especialidades | Array[String] | Lista de especialidades |
| portfolio_urls | Array[String] | Links para portfolio, Vimeo, Behance, etc. |
| redes_sociais | JSON | Links de Instagram, LinkedIn, site pessoal |
| documentos | Array[JSON] | Documentos com tipo, URL e validade |
| banco_dados | JSON | Dados bancarios para pagamento |
| rating_medio | Float | Media das avaliacoes (1-5) |
| total_projetos | Integer | Total de projetos realizados com a Somos |
| status | Enum | Ativo, Inativo, Bloqueado, EmAvaliacao |
| preferido | Boolean | Marcado como fornecedor preferido |
| notas | Text | Observacoes internas |
| criado_em | DateTime | Data de cadastro |
| atualizado_em | DateTime | Ultima atualizacao |

### Service (Servico Oferecido)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| fornecedor_id | FK(Supplier) | Fornecedor que oferece |
| nome | String | Nome do servico |
| descricao | Text | Descricao detalhada |
| categoria | String | Categoria do servico |
| tempo_medio_entrega | String | Prazo tipico de entrega |
| requisitos | Text | Requisitos ou pre-condicoes |
| ativo | Boolean | Se o servico esta sendo oferecido |

### PriceTable (Tabela de Precos)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| fornecedor_id | FK(Supplier) | Fornecedor |
| servico_id | FK(Service) | Servico precificado |
| tipo_cobranca | Enum | Diaria, Hora, Projeto, Entrega, Mensal |
| valor | Decimal | Valor base |
| moeda | Enum | BRL, USD |
| condicoes | Text | Condicoes especiais (minimo de diarias, desconto por volume, etc.) |
| valido_de | Date | Inicio da vigencia |
| valido_ate | Date | Fim da vigencia |
| tipo_producao | Enum | Comercial, Institucional, Evento, RedesSociais, CursoOnline, Todos |
| negociavel | Boolean | Se o valor e negociavel |

### Rating (Avaliacao)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| fornecedor_id | FK(Supplier) | Fornecedor avaliado |
| projeto_id | FK(Project) | Projeto no qual prestou servico |
| avaliador_id | FK(User) | Quem avaliou |
| nota_qualidade | Integer | Qualidade da entrega (1-5) |
| nota_pontualidade | Integer | Cumprimento de prazos (1-5) |
| nota_comunicacao | Integer | Comunicacao e profissionalismo (1-5) |
| nota_custo_beneficio | Integer | Relacao custo-beneficio (1-5) |
| nota_proatividade | Integer | Proatividade e iniciativa (1-5) |
| nota_media | Float | Media calculada |
| comentario | Text | Feedback textual |
| recomenda | Boolean | Se recomendaria para futuros projetos |
| data_avaliacao | DateTime | Data da avaliacao |

### Contract (Contrato com Fornecedor)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| fornecedor_id | FK(Supplier) | Fornecedor contratado |
| tipo | Enum | PorProjeto, Mensal, NDA, Acordo |
| descricao | Text | Descricao dos termos |
| valor | Decimal | Valor do contrato (se aplicavel) |
| data_inicio | Date | Inicio da vigencia |
| data_fim | Date | Fim da vigencia |
| status | Enum | Ativo, Expirado, Cancelado, EmRenovacao |
| documento_url | String | URL do documento do contrato |
| renovacao_automatica | Boolean | Se renova automaticamente |
| alerta_vencimento_dias | Integer | Dias antes do vencimento para alertar |

## Interface e UX

### Diretorio de Fornecedores

- Grid de cards com foto/logo, nome, especialidades principais, rating (estrelas), cidade e status
- Filtros laterais: tipo de fornecedor, especialidade, faixa de preco, rating minimo, disponibilidade, cidade
- Barra de busca com autocomplete por nome, especialidade ou servico
- Ordenacao por: rating, preco (menor/maior), projetos realizados, nome
- Badge de "Preferido" em fornecedores marcados
- Toggle entre visualizacao em grid e lista

### Perfil do Fornecedor

- Header com foto, nome, especialidades e rating geral
- Abas: Informacoes, Servicos e Precos, Portfolio, Avaliacoes, Historico, Documentos
- Aba Informacoes: dados de contato, endereco, documentos, dados bancarios
- Aba Servicos e Precos: tabelas de preco ativas com comparativo de mercado
- Aba Portfolio: galeria de trabalhos com player de video embutido
- Aba Avaliacoes: lista cronologica de avaliacoes com notas e comentarios
- Aba Historico: projetos realizados, valores pagos, frequencia de contratacao
- Aba Documentos: contratos, NDAs, certidoes com status de validade
- Botoes de acao: Contratar para Projeto, Enviar Mensagem, Editar Cadastro

### Comparador de Fornecedores

- Selecao de 2 a 4 fornecedores para comparacao lado a lado
- Comparacao por: preco, rating, disponibilidade, numero de projetos, especialidades
- Tabela comparativa com destaque visual para melhor opcao em cada criterio
- Botao de acao para selecionar o fornecedor vencedor e alocar no projeto

### Tela de Busca para Orcamento

- Interface otimizada para montagem de orcamento de producao
- Selecao do tipo de producao e servicos necessarios
- Sistema sugere fornecedores com precos pre-carregados
- Montagem do orcamento diretamente na tela, somando custos de cada fornecedor selecionado
- Exportacao do orcamento para o modulo Financeiro

### Calendario de Disponibilidade

- Visao mensal com todos os fornecedores de uma categoria
- Marcacoes visuais: disponivel (verde), parcialmente disponivel (amarelo), indisponivel (vermelho), reservado pela Somos (azul)
- Filtros por especialidade e cidade
- Click para reservar disponibilidade diretamente

### Dashboard de Fornecedores

- Total de fornecedores ativos por categoria
- Ranking dos fornecedores mais contratados
- Distribuicao de gastos com fornecedores por categoria
- Fornecedores com contratos proximos do vencimento
- Fornecedores recentemente cadastrados aguardando avaliacao
- Media de rating por categoria

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| Modulo Gestao de Projetos | Alocacao de freelancers e servicos em producoes | Interno (bidirecional) |
| Modulo Financeiro/ERP | Registro de pagamentos e custos de fornecedores | Interno (evento) |
| Modulo Inventario | Consulta de locadoras para aluguel de equipamentos | Interno (consulta) |
| Modulo Gestao de Contratos | Geracao de contratos com fornecedores | Interno (acao) |
| WhatsApp Business API | Comunicacao direta com fornecedores via plataforma | API REST |
| Receita Federal (CPF/CNPJ) | Validacao de situacao cadastral do fornecedor | API REST |
| Google Maps | Busca por proximidade geografica e calculo de distancia | API REST |
| Vimeo / YouTube | Embed de portfolio de video no perfil | API / Embed |

## Agentes de IA

### Agente de Recomendacao de Fornecedor

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Sugerir os fornecedores mais adequados para um projeto com base no briefing, tipo de producao, orcamento e historico
- **Comportamento**: Recebe descricao do projeto e necessidades. Analisa base de fornecedores considerando especialidade, disponibilidade, preco, rating e historico de trabalhos com a Somos. Gera lista rankeada com justificativa para cada sugestao.

### Agente de Analise de Precos

- **Modelo**: GPT-4o
- **Funcao**: Analisar tabelas de preco de fornecedores e comparar com referencias de mercado
- **Comportamento**: Recebe precos de um fornecedor ou orcamento de producao. Compara com historico de contratacoes similares e faixas de preco de mercado. Identifica oportunidades de negociacao e alerta sobre precos fora do padrao.

### Agente de Montagem de Orcamento

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Auxiliar na montagem de orcamentos de producao selecionando automaticamente fornecedores e calculando custos
- **Comportamento**: Recebe briefing do projeto. Identifica todos os servicos e profissionais necessarios. Sugere fornecedores para cada posicao com custo estimado. Gera orcamento preliminar completo para revisao do produtor.

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Frontend | React com TypeScript | Interface rica com grid, filtros e comparadores |
| Busca | Elasticsearch | Busca full-text em fornecedores, servicos e especialidades |
| Geolocalizacao | Google Maps API / PostGIS | Busca por proximidade e calculo de distancia |
| Backend | Node.js com TypeScript | Consistencia com stack do projeto |
| Banco de Dados | PostgreSQL com extensao PostGIS | Queries geoespaciais e relacionais |
| Cache | Redis | Cache de resultados de busca frequentes |
| Player de Video | Plyr ou Video.js | Visualizacao de portfolio no perfil |
| Notificacoes | Email + WhatsApp | Comunicacao com fornecedores sobre reservas e pagamentos |

## Dependencias

- **Modulo Gestao de Projetos**: Consumidor principal para alocacao de fornecedores
- **Modulo Financeiro/ERP**: Registro de custos e processamento de pagamentos
- **Modulo Inventario**: Consulta de fornecedores para aluguel de equipamentos
- **Modulo Gestao de Contratos**: Geracao de contratos formais com fornecedores
- **Servico de Autenticacao**: Controle de acesso (quem pode ver dados bancarios, quem pode editar)
- **Validacao de CPF/CNPJ**: Servico externo para verificacao de regularidade fiscal

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Base de Fornecedores Ativa | Total de fornecedores com status ativo e dados completos | >= 200 |
| Cobertura de Especialidades | Porcentagem de especialidades com pelo menos 3 fornecedores cadastrados | >= 90% |
| Rating Medio da Base | Media geral de avaliacao dos fornecedores | >= 4.0/5.0 |
| Tempo de Busca | Tempo medio para encontrar fornecedor adequado para uma necessidade | <= 5 minutos |
| Taxa de Avaliacoes | Porcentagem de projetos com avaliacao de fornecedor registrada | >= 80% |
| Tabelas de Preco Atualizadas | Fornecedores com tabela de preco vigente | >= 85% |
| Economia em Negociacoes | Reducao de custo obtida por comparacao e negociacao | >= 10% |
| Satisfacao com Fornecedores | Porcentagem de projetos onde fornecedor recebeu nota >= 4 | >= 85% |
| Tempo de Resposta | Tempo medio entre solicitacao e confirmacao do fornecedor | <= 24 horas |
| Recontratacao | Porcentagem de fornecedores contratados mais de uma vez | >= 60% |
