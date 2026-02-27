# Fase 1 — Analise de Escopo

## Projeto: Plataforma Integrada Somos Produtora

**Empresa:** Somos Produtora
**Segmento:** Produtora Audiovisual e Agencia 360
**Tempo de mercado:** ~30 anos
**Data da analise:** 28 de fevereiro de 2026
**Fonte:** Transcricao de briefing com o proprietario (ESCOPO.md)

---

## Sumario Executivo

A Somos Produtora e uma empresa consolidada no mercado audiovisual e de comunicacao, atuando como agencia 360 ha quase 30 anos. Apesar da solidez e experiencia, a empresa enfrenta gargalos operacionais criticos decorrentes da ausencia de um sistema integrado que unifique prospeccao comercial, gestao de clientes, producao de conteudo, controle financeiro e gestao de equipes (humanas e de inteligencia artificial). Este documento apresenta a analise completa do escopo levantado junto ao proprietario, mapeando dores, atores, fluxos, modulos, integracoes e requisitos de IA necessarios para a construcao da plataforma.

---

## 1. Dores Identificadas

### 1.1 Prospeccao Comercial — Gargalo Critico

A dor principal e mais urgente relatada pelo proprietario reside na operacao comercial. O time comercial perde um volume excessivo de horas em atividades manuais e repetitivas que poderiam ser automatizadas ou assistidas por inteligencia artificial.

| Dor | Descricao | Impacto |
|-----|-----------|---------|
| Busca manual de leads | O Diretor Comercial (Hunter) abre manualmente LinkedIn, Instagram, Facebook, Google, YouTube e outros sites para filtrar potenciais clientes | Perda massiva de horas produtivas; fadiga operacional; baixa escalabilidade |
| Prospeccao fragmentada | Mensagens de abordagem sao enviadas por canais distintos (LinkedIn, WhatsApp, Instagram, Facebook) sem unificacao ou rastreamento | Impossibilidade de medir conversao por canal; risco de mensagens duplicadas; falta de historico |
| Qualificacao manual | O processo de qualificar se um lead tem potencial e feito inteiramente por avaliacao humana sem criterios sistematizados | Inconsistencia na qualificacao; leads quentes perdidos; leads frios consumindo tempo |
| Acumulo de funcoes | O Hunter acumula funcoes de busca, filtracao, qualificacao e abordagem simultaneamente | Sobrecarga do profissional; queda na qualidade de cada etapa; impossibilidade de escalar |
| Ausencia de segmentacao | Nao ha separacao sistematica entre nichos (industria farmaceutica, automotiva, moda, influenciadores, medicos, etc.) | Abordagens genericas; perda de oportunidades em nichos especificos |

### 1.2 Gestao Centralizada — Ausencia Total

Nao existe um sistema unico (CRM/ERP) que centralize as informacoes da empresa. Cada area opera de forma isolada, gerando retrabalho e perda de visibilidade.

- **Sem CRM integrado:** Dados de clientes, leads, oportunidades e contratos dispersos em planilhas, e-mails e anotacoes avulsas
- **Sem dashboard unificado:** A direcao nao tem visao consolidada em tempo real do funil comercial, projetos em andamento e saude financeira
- **Sem padronizacao de processos:** Cada projeto segue um fluxo ad hoc, sem templates, checklists ou automacoes que garantam qualidade consistente

### 1.3 Gestao de Projetos e Producao

A empresa atende clientes com necessidades muito diversas — videos institucionais, cursos online, gestao de redes sociais, curadoria de conteudo, producao de campo — e nao dispoe de ferramenta que orquestre essas demandas.

- **Sem cards de tarefas:** Nao ha sistema de quadros Kanban, sprints ou atribuicao formal de tarefas por projeto
- **Sem status reports automatizados:** O acompanhamento de cada etapa (briefing, roteiro, captacao, edicao, revisao, entrega) depende de comunicacao informal
- **Sem calendario de conteudo integrado:** O planejamento de postagens para Instagram, LinkedIn, Facebook e blogs de cada cliente nao esta centralizado
- **Sem controle de etapas de producao:** Desde o briefing inicial ate a entrega final, nao ha fluxo sistematizado com gates de aprovacao

### 1.4 Controle Financeiro

O proprietario expressou necessidade explicita de uma camada financeira robusta que hoje e inexistente dentro de um sistema unificado.

- **Sem controle de orcamentos (budgets):** Cada projeto deveria ter orcamento calculado automaticamente com base em equipamentos, equipe, horas e servicos, porem isso e feito manualmente
- **Sem emissao de notas fiscais integrada:** O processo de emitir NF exige navegar manualmente ate portais externos
- **Sem calculo automatico de impostos:** Margem, comissao de vendedores, impostos e parcelas nao sao calculados dentro de um sistema unico
- **Sem contas a pagar/receber:** Nao ha calendario financeiro com visibilidade de parcelas vincendas, boletos emitidos e fluxo de caixa
- **Sem historico financeiro por projeto:** Nao e possivel consultar rapidamente quanto foi gasto, qual a margem real e quem foi responsavel por cada custo

### 1.5 Inventario e Fornecedores

Como produtora audiovisual, a Somos possui um acervo significativo de equipamentos (cameras, lentes, iluminacao, audio, drones, etc.) que nao esta catalogado digitalmente.

- **Sem inventario de equipamentos:** Nao ha registro sistematizado de quais equipamentos a empresa possui, seu estado de conservacao e disponibilidade
- **Sem controle de locacao:** Quando um equipamento sai para locacao externa ou quando a empresa precisa locar equipamentos de terceiros, nao ha rastreamento
- **Sem banco de fornecedores:** Nao existe base centralizada de fornecedores de equipamentos, locacoes, locais de filmagem e servicos terceirizados
- **Sem banco de freelancers:** Profissionais freelancers (cinegrafistas, editores, produtores, designers) nao estao catalogados com valores, especialidades e historico

### 1.6 Producao de Conteudo

A geracao de conteudo para clientes de gestao de redes sociais e feita manualmente, sem ferramentas de automacao ou assistencia de IA.

- **Sem gerador de posts automatizado:** Textos para Instagram, LinkedIn, Facebook e blogs sao redigidos inteiramente do zero
- **Sem gerador de imagens padronizado:** Imagens para posts nao seguem templates integrados ao sistema; dependem de ferramentas externas (Canva/Figma) sem conexao com a plataforma
- **Sem curadoria automatica de noticias:** O monitoramento de tendencias e noticias por nicho (ex.: ecossistema China, industria farmaceutica, tecnologia) depende de busca manual
- **Sem geracao automatica de PDFs:** Propostas comerciais, cronogramas de projeto e materiais de boas-vindas sao construidos artesanalmente a cada novo cliente

### 1.7 Gestao de Contratos

- **Sem gestao contratual centralizada:** Contratos nao sao gerados, armazenados e acompanhados dentro de um sistema unico
- **Sem templates de contrato:** Cada contrato e redigido individualmente, sem aproveitamento de modelos padronizados
- **Sem vinculacao contrato-projeto:** O contrato nao se conecta automaticamente ao projeto, orcamento e cronograma correspondentes

### 1.8 Recursos Humanos (Agentes e Humanos)

O proprietario manifestou a necessidade de um setor de RH que gerencie tanto funcionarios humanos quanto agentes de IA, incluindo avaliacao de desempenho, metricas e ciclo de vida.

- **Sem avaliacao de desempenho de agentes IA:** Nao ha mecanismo para classificar a qualidade dos resultados dos Hunters e SDRs artificiais (falhas leves, medias e graves)
- **Sem ciclo de vida de agentes:** Quando um agente apresenta desempenho insatisfatorio, nao ha processo formalizado de desvinculacao, analise de erros e criacao de novo agente aprimorado
- **Sem metricas unificadas:** Humanos e agentes nao compartilham o mesmo dashboard de produtividade e metas

---

## 2. Atores do Sistema

### 2.1 Atores Humanos

| Ator | Funcao Principal | Area |
|------|-----------------|------|
| Diretor Geral | Conduzir o time, canalizar informacoes, tomar decisoes estrategicas, supervisao geral de todos os projetos e equipes | Direcao |
| Diretor Comercial (Hunter Humano) | Prospectar clientes de alto valor, conduzir reunioes de vendas, apresentar produtos, fechar contratos | Comercial |
| Time de Vendas / SDR Humano | Apoiar a abordagem ativa, realizar follow-ups, agendar reunioes, alimentar o CRM | Comercial |
| Time de Pos-venda | Dar boas-vindas ao cliente, coletar dados cadastrais (CNPJ, endereco, inscricao municipal), montar dossiê contratual | Comercial / Operacoes |
| Diretor de Conteudo | Pensar narrativas, definir linhas editoriais, supervisionar roteiros e producao intelectual de conteudo | Producao |
| Estrategista | Planejar posicionamento em redes sociais, identificar tendencias, criar planos de marketing por cliente | Marketing / Estrategia |
| Roteirista | Construir roteiros, textos, descricoes de imagens, posicionamento editorial para cada projeto | Producao |
| Editor | Edicao de videos (institucionais, cursos online, reels, conteudos para redes sociais) | Pos-producao |
| Produtor | Organizar captacoes, logistica de filmagem, alocacao de equipamentos e equipe em campo | Producao |
| Revisor | Revisar entregas (textos, videos, imagens) garantindo qualidade e aderencia ao briefing | Qualidade |
| Designers | Criar identidade visual, PDFs, apresentacoes, layouts de posts, materiais graficos | Design |
| RH Humano | Supervisionar desempenho de agentes e humanos, aplicar avaliacoes, gerenciar ciclo de vida de agentes | Recursos Humanos |

### 2.2 Atores de Inteligencia Artificial (Agentes)

| Agente IA | Funcao | Quantidade Estimada |
|-----------|--------|-------------------|
| Hunter por Segmento | Buscar leads em plataformas (LinkedIn, Instagram, Facebook, Google, YouTube) filtrados por nicho especifico (ex.: industria farmaceutica, automotiva, moda, influenciadores, medicos) | Multiplos — 1 agente para cada 1 a 3 segmentos |
| SDR Ativo | Enviar mensagens de abordagem nos canais identificados pelo Hunter, qualificar respostas, classificar interesse | Multiplos — proporcionais ao volume de leads |
| Gerador de Conteudo Textual | Produzir textos para posts de Instagram, artigos de LinkedIn, legendas, blogs e newsletters | 1 ou mais por cluster de clientes |
| Gerador de Imagens | Criar imagens para posts respeitando identidade visual do cliente (baseada em templates Figma/Canva) | 1 ou mais por cluster de clientes |
| Analista de Tendencias | Monitorar fontes de noticias, foruns, blogs, redes sociais e YouTube por nicho, gerar notas de relevancia | 1 por vertical de conteudo |
| Calculador de Orcamentos | Montar orcamentos automaticamente com base em inventario de equipamentos, tabela de precos, horas e servicos | 1 centralizado |
| Avaliador de Qualidade (RH-IA) | Classificar desempenho dos agentes, detectar falhas (leves, medias, graves), recomendar substituicao ou ajustes | 1 centralizado |
| Gerador de PDFs | Montar propostas comerciais, cronogramas, materiais de boas-vindas a partir de templates e dados do CRM | 1 centralizado |

### 2.3 Perfis de Acesso (Roles)

| Role | Descricao | Permissoes Principais |
|------|-----------|----------------------|
| **Admin** | Acesso total ao sistema, configuracoes, gestao de usuarios e agentes, parametros globais | Leitura/escrita em todos os modulos; gestao de roles; configuracoes de integracao |
| **Diretor** | Visao executiva de todos os projetos, financeiro, comercial e equipes | Dashboard completo; aprovacao de orcamentos e contratos; relatorios gerenciais |
| **Comercial** | Acesso ao funil de vendas, CRM, contratos e orcamentos | Gestao de leads e oportunidades; criacao de propostas; visualizacao financeira parcial |
| **Agente (IA)** | Execucao automatizada de tarefas atribuidas (prospeccao, conteudo, analise) | Acesso via API; escrita em modulos especificos conforme funcao; leitura restrita |
| **Operacional** | Equipe de producao, edicao, design e estrategia | Gestao de tarefas e cards; calendario de conteudo; upload de arquivos; producao |
| **Cliente** | Portal de acompanhamento para clientes externos | Visualizacao do andamento do projeto; aprovacao de entregas; acesso a calendario de postagens |

---

## 3. Fluxos de Processo

### 3.1 Funil Comercial Completo

```
[DESCOBERTA]          [QUALIFICACAO]         [CONVERSAO]           [CONTRATACAO]
     |                      |                      |                      |
     v                      v                      v                      v
  Hunters IA           SDR Ativo IA          Humano (Reuniao)      Pos-venda
  buscam leads    -->  enviam mensagens  -->  apresenta         -->  coleta dados
  por segmento         qualificam             produtos/PDFs         monta contrato
  em multiplas         respostas              negocia               cadastra CNPJ
  plataformas          classificam            fecha venda           gera contrato
                       interesse                                    boas-vindas
     |                      |                      |                      |
     v                      v                      v                      v
[DASHBOARD CRM]     [DASHBOARD CRM]        [DASHBOARD CRM]       [DASHBOARD CRM]
  Lead registrado     Lead qualificado       Oportunidade           Cliente ativo
  com fonte e         com score e            convertida em          com contrato
  segmento            historico de           venda fechada          assinado
                      interacoes
```

#### Detalhamento Etapa a Etapa

**Etapa 1 — Descoberta (Hunter IA)**
1. Agentes Hunter segmentados iniciam buscas periodicas em LinkedIn, Instagram, Facebook, Google, YouTube e outras fontes
2. Filtram potenciais clientes por criterios pre-definidos: segmento de atuacao, porte da empresa, presenca digital, localizacao
3. Registram o lead no CRM com as informacoes coletadas: nome, empresa, cargo, canal de origem, contato, segmento
4. Classificam o lead como "Descoberto" e atribuem ao pool de SDR Ativo correspondente

**Etapa 2 — Qualificacao (SDR Ativo IA)**
1. Agente SDR recebe leads classificados como "Descoberto"
2. Envia mensagens personalizadas por canal (WhatsApp, LinkedIn, Instagram, e-mail) apresentando a Somos Produtora
3. Monitora respostas e interacoes
4. Classifica o lead: sem resposta / respondeu sem interesse / respondeu com interesse
5. Para leads com interesse: agenda horario e encaminha para o time humano

**Etapa 3 — Conversao (Humano)**
1. Humano (Diretor Comercial ou vendedor designado) recebe lead qualificado com historico completo
2. Realiza reuniao de apresentacao (presencial ou online)
3. Apresenta portfolio, cases de sucesso e proposta comercial (PDF gerado pelo sistema)
4. Entende as dores e necessidades especificas do cliente
5. Elabora orcamento personalizado utilizando a base de dados de equipamentos, servicos e valores
6. Negocia e fecha a venda
7. Classifica o lead como "Convertido" no CRM

**Etapa 4 — Contratacao (Pos-venda)**
1. Time de pos-venda recebe a oportunidade convertida
2. Coleta dados cadastrais completos: CNPJ, endereco, CEP, inscricao municipal, inscricao de servico
3. Gera contrato com base em template padronizado, preenchido com dados coletados e termos acordados
4. Envia material de boas-vindas (PDF) detalhando as etapas do projeto
5. Registra tudo no sistema para rastreabilidade

### 3.2 Fluxo de Producao (Pos-contratacao)

```
[BRIEFING]  -->  [PLANEJAMENTO]  -->  [PRODUCAO]  -->  [POS-PRODUCAO]  -->  [ENTREGA]
    |                  |                   |                  |                  |
    v                  v                   v                  v                  v
 Diretor Geral    Estrategista        Produtor           Editor             Revisor
 + Diretor de     + Roteirista        + Equipe           + Designer         + Cliente
   Conteudo       + Designer            de campo         + Agentes IA         (aprovacao)
 + Cliente                              (captacao)         (imagens/texto)
```

**Etapa 5 — Briefing**
1. Diretor Geral conduz sessao de briefing com o cliente
2. Diretor de Conteudo participa para captar narrativas e linhas editoriais
3. Informacoes sao registradas no sistema e vinculadas ao projeto/contrato

**Etapa 6 — Planejamento**
1. Estrategista cria plano de marketing e posicionamento em redes sociais
2. Roteirista desenvolve roteiros, textos e descricoes de imagens
3. Designer inicia identidade visual e templates
4. Cronograma e gerado e registrado no calendario do projeto

**Etapa 7 — Producao**
1. Produtor organiza logistica de captacao (equipamentos do inventario, equipe, locacoes)
2. Equipamentos sao reservados no sistema de inventario
3. Filmagens e captacoes sao realizadas
4. Material bruto e carregado no sistema

**Etapa 8 — Pos-producao**
1. Editor realiza edicao de videos
2. Designers criam pecas graficas e posts
3. Agentes IA geram conteudo complementar (textos, imagens, artigos LinkedIn)
4. Curadoria de noticias alimenta o feed de conteudo para posts relevantes ao nicho do cliente

**Etapa 9 — Entrega e Publicacao**
1. Revisor faz checagem de qualidade
2. Cliente aprova via portal
3. Conteudo e agendado no calendario de publicacoes (Instagram, LinkedIn, Facebook, blog)
4. Metricas de desempenho sao monitoradas

### 3.3 Fluxo Financeiro

```
[ORCAMENTO]  -->  [APROVACAO]  -->  [EXECUCAO]  -->  [FATURAMENTO]  -->  [CONCILIACAO]
     |                 |                |                  |                   |
     v                 v                v                  v                   v
 Calculadora IA    Direcao         Registro de        Emissao NF          Dashboard
 + Inventario      aprova          custos reais       + Boleto            financeiro
 + Tabela precos                   por projeto        + Integracao        + Graficos
 + Freelancers                                          contabil          + Margem
```

### 3.4 Fluxo de Gestao de Conteudo para Redes Sociais

```
[MONITORAMENTO]  -->  [CURADORIA]  -->  [GERACAO]  -->  [REVISAO]  -->  [AGENDAMENTO]
      |                    |               |               |                 |
      v                    v               v               v                 v
  Agente de           Nota de          Agente gera     Estrategista      Calendario
  Tendencias          relevancia       5-20 cards      + Revisor         de postagens
  busca fontes        atribuida        com texto       aprovam           por cliente
  por nicho           pelo agente      + imagem                          por rede social
```

**Detalhamento:**
1. Agente de Tendencias monitora foruns, blogs, sites de noticias, redes sociais e YouTube por nicho do cliente
2. Informacoes recebem nota de relevancia automatica
3. Conteudo aprovado e expandido em cards (5 a 20 slides/posts) contendo: gancho (hook), informativo, fato curioso e call-to-action (CTA)
4. Agente gerador de imagens cria visuais respeitando identidade visual do cliente (templates do Figma/Canva)
5. Imagens sao geradas nos formatos adequados para cada plataforma (Instagram, LinkedIn, Facebook)
6. Agente gerador de texto produz artigos para LinkedIn, legendas para Instagram e textos para blog
7. Estrategista e Revisor humanos validam o conteudo
8. Conteudo aprovado e agendado no calendario de publicacoes

---

## 4. Modulos Funcionais

### 4.1 Visao Geral dos Modulos

```
+------------------------------------------------------------------+
|                     PLATAFORMA SOMOS PRODUTORA                    |
+------------------------------------------------------------------+
|                                                                    |
|  +------------+  +------------+  +------------+  +------------+   |
|  |    CRM     |  |    ERP     |  | INVENTARIO |  | FORNECEDORES|  |
|  | Leads      |  | Financeiro |  | Equipamentos|  | Freelancers|  |
|  | Funil      |  | NF / Boleto|  | Locacao    |  | Servicos   |  |
|  | Historico  |  | Margem     |  | Rastreio   |  | Valores    |  |
|  +------------+  +------------+  +------------+  +------------+   |
|                                                                    |
|  +------------+  +------------+  +------------+  +------------+   |
|  | ORCAMENTOS |  |  CONTENT   |  | AGENDADOR  |  | CONTRATOS  |  |
|  | Calculadora|  |  STUDIO    |  | Posts      |  | Templates  |  |
|  | Templates  |  | Texto/Img  |  | Calendario |  | Assinatura |  |
|  | PDF Auto   |  | PDF / PPT  |  | Multi-rede |  | Historico  |  |
|  +------------+  +------------+  +------------+  +------------+   |
|                                                                    |
|  +------------+  +------------+  +------------+  +------------+   |
|  |  PROJETOS  |  |     RH     |  |   INTEL    |  | ANALYTICS  |  |
|  | Cards      |  | Avaliacoes |  |   FEED     |  | Dashboards |  |
|  | Tarefas    |  | Metricas   |  | Tendencias |  | Graficos   |  |
|  | Sprints    |  | Ciclo vida |  | Noticias   |  | KPIs       |  |
|  +------------+  +------------+  +------------+  +------------+   |
|                                                                    |
+------------------------------------------------------------------+
```

### 4.2 Detalhamento por Modulo

#### Modulo 1 — CRM (Customer Relationship Management)

**Objetivo:** Centralizar todo o ciclo de vida do relacionamento com clientes, desde a descoberta do lead ate o pos-venda.

| Funcionalidade | Descricao |
|----------------|-----------|
| Cadastro de Leads | Registro automatico (via Hunters IA) e manual de potenciais clientes com dados de contato, empresa, segmento, canal de origem |
| Funil de Vendas | Visualizacao em pipeline com estagios: Descoberto > Contactado > Qualificado > Reuniao Agendada > Proposta Enviada > Negociacao > Fechado-Ganho / Fechado-Perdido |
| Historico de Interacoes | Log completo de todas as mensagens enviadas (WhatsApp, LinkedIn, Instagram, e-mail) e respostas recebidas |
| Score de Lead | Classificacao automatica com base em criterios de qualificacao (tamanho da empresa, engajamento, canal de resposta) |
| Segmentacao | Classificacao por nicho: institucional, influenciador, industria farmaceutica, automotiva, moda, educacao, etc. |
| Painel do Vendedor | Visao individual com metas, pipeline pessoal, atividades pendentes e historico de conversoes |

#### Modulo 2 — ERP / Financeiro

**Objetivo:** Controlar integralmente a saude financeira da empresa com visibilidade por projeto e por cliente.

| Funcionalidade | Descricao |
|----------------|-----------|
| Contas a Receber | Calendario de recebimentos com status (pendente, pago, atrasado), vinculado a contratos e projetos |
| Contas a Pagar | Registro de despesas operacionais, fornecedores, freelancers, locacoes, impostos |
| Emissao de NF | Botao integrado que direciona ao portal de nota fiscal ou emite diretamente via API |
| Emissao de Boletos | Geracao de boletos bancarios vinculados a parcelas do contrato |
| Calculo de Impostos | Calculo automatico de tributos incidentes sobre cada nota fiscal emitida |
| Calculo de Margem | Comparacao automatica entre orcamento aprovado, custos reais e receita liquida por projeto |
| Comissoes | Calculo e registro de comissoes de vendedores sobre cada venda fechada |
| Dashboard Visual | Graficos em formato pizza, barras, linhas e tabelas com visao consolidada — fluxo de caixa, faturamento mensal, margens por projeto, custos por categoria |
| Integracao Contabil | Envio automatico de dados para o escritorio de contabilidade ou software contabil |
| Declaracoes | Apoio a geracao de dados para IRPJ, DAS (Simples Nacional) e demais obrigacoes fiscais |

#### Modulo 3 — Inventario

**Objetivo:** Catalogar e rastrear todos os equipamentos da produtora, controlando disponibilidade, locacao e manutencao.

| Funcionalidade | Descricao |
|----------------|-----------|
| Cadastro de Equipamentos | Registro por categoria (cameras, lentes, iluminacao, audio, drones, estabilizadores, computadores, etc.) com marca, modelo, numero de serie, valor de aquisicao |
| Status do Equipamento | Classificacao: Disponivel / Em uso (projeto X) / Locado para terceiro / Em manutencao / Indisponivel |
| Controle de Locacao | Registro de entradas e saidas de equipamentos, com data, destino e responsavel |
| Locacao de Terceiros | Registro de equipamentos locados de fornecedores externos, com custo e periodo |
| Vinculacao a Projetos | Ao reservar equipamentos para um projeto, o custo e automaticamente incorporado ao orcamento |
| Alertas | Notificacoes de equipamento proximo do vencimento de manutencao, seguro ou locacao |

#### Modulo 4 — Banco de Fornecedores e Freelancers

**Objetivo:** Centralizar informacoes de todos os prestadores de servico e fornecedores parceiros.

| Funcionalidade | Descricao |
|----------------|-----------|
| Cadastro de Fornecedores | Nome, CNPJ/CPF, especialidade, contato, endereco, categorias de servico |
| Cadastro de Freelancers | Nome, especialidade (cinegrafista, editor, produtor, designer, etc.), portfolio, valor hora/diaria, disponibilidade |
| Historico de Trabalhos | Registro de todos os projetos em que cada fornecedor/freelancer participou, com avaliacao de desempenho |
| Tabela de Precos | Valores atualizados por tipo de servico e profissional |
| Busca e Filtros | Localizar rapidamente profissionais por especialidade, faixa de preco, avaliacao e disponibilidade |

#### Modulo 5 — Gerador de Orcamentos

**Objetivo:** Construir orcamentos profissionais de forma rapida e precisa, integrando dados de inventario, servicos e fornecedores.

| Funcionalidade | Descricao |
|----------------|-----------|
| Montagem Assistida | Selecao de itens do inventario (equipamentos), servicos da tabela de precos, freelancers, carga horaria e locacoes |
| Calculo Automatico | Somatorio de custos + margem configuravel + impostos = valor final sugerido |
| Templates de Orcamento | Modelos pre-formatados por tipo de projeto (video institucional, gestao de redes, curso online, evento) |
| Exportacao PDF | Geracao automatica de PDF profissional com a identidade visual da Somos |
| Versionamento | Historico de versoes do orcamento (v1, v2, v3...) com registro de alteracoes |
| Vinculacao ao CRM | Orcamento vinculado a oportunidade no funil de vendas |

#### Modulo 6 — Content Studio (Estudio de Conteudo)

**Objetivo:** Centralizar a criacao, edicao e gestao de todo conteudo produzido para clientes.

| Funcionalidade | Descricao |
|----------------|-----------|
| Gerador de Texto | Criacao assistida por IA de legendas, artigos, roteiros, blogs, newsletters |
| Gerador de Imagens | Criacao de visuais para posts respeitando identidade visual do cliente (templates Figma/Canva importados) |
| Gerador de PDF | Montagem automatica de propostas comerciais, cronogramas, materiais de boas-vindas, apresentacoes |
| Curadoria de Noticias | Feed filtrado por nicho do cliente com notas de relevancia atribuidas automaticamente |
| Expansao em Cards | Transformacao de uma noticia/tema em serie de 5 a 20 cards (hook, informativo, fato curioso, CTA) |
| Formatos por Rede | Adaptacao automatica de conteudo para formatos especificos de Instagram, LinkedIn, Facebook, YouTube, Blog |

#### Modulo 7 — Agendador Social (Social Scheduler)

**Objetivo:** Planejar, agendar e publicar conteudo em multiplas redes sociais a partir de um calendario unificado.

| Funcionalidade | Descricao |
|----------------|-----------|
| Calendario Visual | Visao mensal/semanal de todas as postagens planejadas por cliente e por rede social |
| Agendamento Multi-rede | Programacao de publicacao simultanea ou escalonada em Instagram, LinkedIn, Facebook, YouTube, Blog |
| Aprovacao Pre-publicacao | Fluxo de aprovacao por Estrategista e/ou cliente antes da publicacao |
| Historico de Publicacoes | Registro de tudo que foi publicado com data, rede, metricas de engajamento |
| Repostagem Inteligente | Sugestao de repostagem de conteudo com bom desempenho |

#### Modulo 8 — Gestao de Contratos

**Objetivo:** Criar, armazenar, acompanhar e gerenciar todos os contratos da empresa.

| Funcionalidade | Descricao |
|----------------|-----------|
| Templates de Contrato | Modelos padronizados por tipo de servico, pre-preenchidos com dados do CRM |
| Geracao Automatica | Preenchimento automatico com dados cadastrais do cliente (CNPJ, endereco, valores, servicos contratados) |
| Repositorio Seguro | Armazenamento centralizado de todos os contratos com controle de acesso |
| Status Contratual | Acompanhamento: Rascunho > Enviado > Assinado > Vigente > Encerrado > Renovado |
| Alertas de Vencimento | Notificacoes automaticas de contratos proximos do vencimento para renovacao |
| Vinculacao | Contrato ligado ao projeto, orcamento e financeiro correspondentes |

#### Modulo 9 — Gestao de Projetos

**Objetivo:** Gerenciar a execucao de todos os projetos com visibilidade, rastreabilidade e controle de prazos.

| Funcionalidade | Descricao |
|----------------|-----------|
| Quadro Kanban | Cards de tarefas com colunas configuraveis (A Fazer, Em Andamento, Revisao, Concluido) |
| Sprints | Organizacao de entregas em ciclos definidos com metas e datas-limite |
| Atribuicao de Tarefas | Designacao de tarefas para humanos e agentes IA com prazo e prioridade |
| Status Reports | Relatorio automatico de progresso com percentual de conclusao, tarefas atrasadas e bloqueios |
| Dependencias | Definicao de sequenciamento entre tarefas (ex.: edicao so inicia apos captacao concluida) |
| Notas e Comentarios | Espaco colaborativo por tarefa para comunicacao da equipe |
| Visualizacoes | Quadro Kanban, calendario, lista, timeline (Gantt) |

#### Modulo 10 — Recursos Humanos (RH)

**Objetivo:** Gerenciar o desempenho, avaliacao e ciclo de vida de todos os funcionarios — humanos e agentes de IA.

| Funcionalidade | Descricao |
|----------------|-----------|
| Cadastro Unificado | Ficha de cada funcionario (humano) e cada agente (IA) com funcao, area, data de inicio, competencias |
| Avaliacao de Desempenho | Sistema de metas e indicadores por periodo, com avaliacao por lider e pelo sistema (no caso de agentes) |
| Classificacao de Falhas | Para agentes IA: registro de falhas classificadas como leves, medias e graves, com contagem cumulativa |
| Ciclo de Vida de Agentes | Processo de desvinculacao de agente com desempenho insatisfatorio, analise de erros e acertos, criacao de novo agente aprimorado herdando as melhorias |
| Historico | Log completo de todas as acoes, entregas e avaliacoes de cada funcionario/agente |
| Quadro de Time | Visao geral de toda a equipe com status (ativo, em avaliacao, desvinculado) |

#### Modulo 11 — Intelligence Feed (Feed de Inteligencia)

**Objetivo:** Alimentar a empresa com informacoes de mercado, tendencias, oportunidades e insights em tempo real.

| Funcionalidade | Descricao |
|----------------|-----------|
| Monitoramento de Fontes | Rastreamento automatico de sites, blogs, foruns, redes sociais, YouTube, Pinterest por nicho |
| Notas de Relevancia | Classificacao automatica de cada informacao coletada com score de relevancia |
| Alertas de Tendencia | Notificacoes quando um tema emergente e detectado em determinado nicho |
| Oportunidades de Mercado | Identificacao de setores em crescimento que podem gerar novos clientes para a Somos |
| Base de Conhecimento | Repositorio de informacoes categorizadas por segmento, acessivel a toda a equipe |

#### Modulo 12 — Analytics (Paineis Analiticos)

**Objetivo:** Fornecer visao executiva e operacional de todos os indicadores da empresa.

| Funcionalidade | Descricao |
|----------------|-----------|
| Dashboard Comercial | Funil de vendas, taxa de conversao por etapa, tempo medio de ciclo de venda, leads por segmento |
| Dashboard Financeiro | Faturamento, custos, margens, fluxo de caixa, inadimplencia — com graficos em pizza, barras e linhas |
| Dashboard de Producao | Projetos em andamento, tarefas atrasadas, capacidade da equipe, entregas no prazo |
| Dashboard de Conteudo | Posts publicados, engajamento por rede social, calendario de publicacoes, desempenho por cliente |
| Dashboard de Agentes IA | Performance por agente (leads descobertos, mensagens enviadas, taxa de resposta, taxa de qualificacao) |
| Dashboard RH | Headcount, avaliacoes pendentes, agentes em observacao, produtividade por area |
| Relatorios Exportaveis | Geracao de relatorios em PDF e CSV com filtros por periodo, cliente, projeto, area |

---

## 5. Integracoes Externas

### 5.1 Redes Sociais e Plataformas de Comunicacao

| Plataforma | Tipo de Integracao | Funcionalidades |
|------------|-------------------|-----------------|
| **LinkedIn** | API bidirecional | Busca de leads (Hunter); envio de mensagens (SDR); publicacao de artigos e posts; monitoramento de engajamento |
| **WhatsApp** | API Business / Webhook | Envio de mensagens de abordagem (SDR); comunicacao com clientes; notificacoes de projeto |
| **Instagram** | API Graph | Busca de leads; envio de DMs (SDR); publicacao de posts e stories agendados; metricas de engajamento |
| **Facebook** | API Graph | Busca de leads; envio de mensagens; publicacao de posts agendados; metricas de pagina |
| **YouTube** | API Data v3 | Busca de tendencias e oportunidades; monitoramento de canais por nicho; publicacao de videos |
| **Pinterest** | API | Busca de tendencias visuais; monitoramento de pins por nicho; inspiracao para conteudo |

### 5.2 Ferramentas de Busca e Dados

| Plataforma | Tipo de Integracao | Funcionalidades |
|------------|-------------------|-----------------|
| **Google Search** | API Custom Search / Scraping assistido | Busca de empresas, profissionais e tendencias por segmento; alimentacao dos Hunters IA |
| **Google My Business** | API | Coleta de dados comerciais de empresas locais para prospeccao |

### 5.3 Ferramentas de Design e Criacao

| Plataforma | Tipo de Integracao | Funcionalidades |
|------------|-------------------|-----------------|
| **Canva** | API | Importacao de templates; geracao automatizada de posts com identidade visual do cliente |
| **Figma** | API | Importacao de design systems e templates visuais para geracao automatizada de imagens |

### 5.4 Integracoes Financeiras e Fiscais

| Plataforma | Tipo de Integracao | Funcionalidades |
|------------|-------------------|-----------------|
| **Portal NFS-e** | API ou redirecionamento | Emissao de notas fiscais de servico diretamente do sistema |
| **Sistema Contabil** | API / Exportacao | Envio automatico de dados financeiros (NFs, pagamentos, recebimentos) para o escritorio de contabilidade |
| **Bancos / Gateway de Pagamento** | API bancaria | Emissao de boletos, registro de recebimentos, conciliacao bancaria automatica |
| **Receita Federal / SEFAZ** | API ou redirecionamento | Consulta de dados de CNPJ, situacao cadastral; apoio a declaracoes fiscais |

### 5.5 Integracoes de Conteudo e Inteligencia

| Plataforma | Tipo de Integracao | Funcionalidades |
|------------|-------------------|-----------------|
| **RSS Feeds / News APIs** | API | Alimentacao automatica do Intelligence Feed com noticias por nicho |
| **APIs de IA Generativa** | API (OpenAI, Anthropic, Stability, etc.) | Geracao de textos, imagens, resumos, analises de tendencias |
| **Google Analytics** | API | Metricas de performance de sites e blogs dos clientes |

### 5.6 Mapa de Integracoes

```
                            +-------------------+
                            |   PLATAFORMA      |
                            |   SOMOS           |
                            +--------+----------+
                                     |
          +---------+--------+-------+-------+--------+---------+
          |         |        |       |       |        |         |
     +----+---+ +---+---+ +-+----+ ++-+ +---+---+ +--+----+ +--+----+
     |LinkedIn| |Whats  | |Insta | |FB| |YouTube| |Google | |Pinterest|
     +--------+ |App    | |gram  | +--+ +-------+ |Search | +---------+
                +-------+ +------+                 +-------+
          |         |        |       |       |        |         |
     +----+---+ +---+---+ +-+----+ ++-+ +---+---+ +--+----+
     |Canva   | |Figma  | |Portal| |Ban| |Contab | |RSS/   |
     |        | |       | |NFS-e | |cos| |ilidade| |News   |
     +--------+ +-------+ +------+ +--+ +-------+ +-------+
          |         |        |       |       |        |
     +----+---+ +---+---+ +-+----+
     |APIs IA | |Google | |Receita|
     |Generat.| |Analyt.| |Federal|
     +--------+ +-------+ +------+
```

---

## 6. Requisitos de Inteligencia Artificial

### 6.1 Visao Geral da Arquitetura de Agentes

A plataforma emprega uma arquitetura multi-agente onde cada agente de IA opera como um "funcionario digital" com funcao, metas e metricas de desempenho definidas. Os agentes sao gerenciados pelo modulo de RH, podendo ser avaliados, ajustados ou substituidos conforme performance.

### 6.2 Agentes de Prospeccao (Hunters)

**Missao:** Descobrir leads qualificados em multiplas plataformas, segmentados por nicho de mercado.

| Caracteristica | Especificacao |
|---------------|---------------|
| **Quantidade** | Multiplos agentes — recomendado 1 agente para cada 1 a 3 segmentos de mercado |
| **Segmentos-alvo** | Industria farmaceutica, automotiva, moda, educacao, saude (medicos), influenciadores, entretenimento, tecnologia, mineracao, varejo, entre outros |
| **Fontes de busca** | LinkedIn, Instagram, Facebook, Google, YouTube, sites corporativos, diretórios empresariais |
| **Criterios de filtragem** | Porte da empresa, localizacao geografica, presenca digital, aderencia ao portfolio da Somos |
| **Saida esperada** | Lead registrado no CRM com: nome, empresa, cargo, contato(s), canal de origem, segmento, score de aderencia |
| **Frequencia** | Execucao diaria ou em intervalos configuraveis |
| **Metricas** | Leads descobertos/dia, taxa de aderencia (leads aproveitados vs descartados), diversidade de fontes |

### 6.3 Agentes de Abordagem (SDR Ativo)

**Missao:** Realizar outreach automatizado e personalizado para leads descobertos, qualificando respostas.

| Caracteristica | Especificacao |
|---------------|---------------|
| **Quantidade** | Proporcional ao volume de leads — recomendado agrupamento por canal de comunicacao |
| **Canais de atuacao** | WhatsApp, LinkedIn (mensagens e InMails), Instagram (DMs), Facebook (Messenger), E-mail |
| **Personalizacao** | Mensagens adaptadas ao segmento do lead, servicos relevantes da Somos, casos de sucesso pertinentes |
| **Qualificacao** | Classificacao de respostas: sem resposta / negativo / curioso / interessado / pronto para reuniao |
| **Follow-up** | Sequencias automaticas de follow-up com intervalos configuraveis e conteudo progressivo |
| **Saida esperada** | Lead qualificado com historico de interacoes, pronto para handoff ao humano |
| **Metricas** | Mensagens enviadas/dia, taxa de resposta, taxa de qualificacao, tempo medio de qualificacao |

### 6.4 Agentes de Geracao de Conteudo Textual

**Missao:** Produzir textos de alta qualidade para multiplos formatos e plataformas, alinhados a estrategia de cada cliente.

| Caracteristica | Especificacao |
|---------------|---------------|
| **Tipos de conteudo** | Legendas Instagram, artigos LinkedIn, posts Facebook, roteiros de video, blogs, newsletters, scripts de Stories/Reels |
| **Personalizacao** | Tom de voz do cliente, terminologia do nicho, estrategia de posicionamento definida pelo Estrategista |
| **Input** | Briefing do cliente, curadoria de noticias (Intelligence Feed), diretrizes do Diretor de Conteudo |
| **Output** | Texto formatado para a plataforma-alvo, com hashtags, CTAs e adequacao de caracteres |
| **Revisao** | Todo conteudo passa por validacao humana (Estrategista/Revisor) antes da publicacao |
| **Metricas** | Volume de conteudo gerado, taxa de aprovacao sem edicao, engajamento das publicacoes |

### 6.5 Agentes de Geracao de Imagens

**Missao:** Criar visuais profissionais respeitando a identidade visual de cada cliente.

| Caracteristica | Especificacao |
|---------------|---------------|
| **Tipos de imagem** | Cards para Instagram (feed, stories, carrossel), capas de artigo LinkedIn, banners Facebook, thumbnails YouTube |
| **Base visual** | Templates importados do Figma/Canva com identidade visual do cliente (cores, fontes, logo, estilo) |
| **Formatos** | Geracao em multiplos formatos simultaneamente (1080x1080, 1080x1920, 1200x628, etc.) |
| **Fluxo** | Recebe texto do agente de conteudo + template visual = imagem finalizada |
| **Metricas** | Volume de imagens geradas, taxa de aprovacao, aderencia ao brand guide |

### 6.6 Agentes de Monitoramento de Tendencias

**Missao:** Rastrear tendencias, noticias e oportunidades de mercado em tempo real por nicho de atuacao.

| Caracteristica | Especificacao |
|---------------|---------------|
| **Fontes** | Sites de noticias, blogs especializados, foruns, Reddit, YouTube, Google Trends, Pinterest, redes sociais |
| **Cobertura** | Por nicho de cliente (ex.: ecossistema China, industria farmaceutica, tecnologia, audiovisual) |
| **Processamento** | Coleta de informacao > analise de relevancia > atribuicao de nota (score) > classificacao por tema |
| **Saida** | Feed de inteligencia com noticias ranqueadas, resumos e sugestoes de conteudo |
| **Alertas** | Notificacao imediata quando tendencia emergente e detectada em nicho relevante |
| **Metricas** | Volume de informacoes coletadas, precisao da nota de relevancia (avalida por humano), cobertura por nicho |

### 6.7 Agentes de Calculo de Orcamento

**Missao:** Gerar orcamentos automatizados com base em dados internos (inventario, tabela de precos, fornecedores).

| Caracteristica | Especificacao |
|---------------|---------------|
| **Inputs** | Tipo de projeto, servicos solicitados, equipamentos necessarios, carga horaria, numero de profissionais, locacoes |
| **Base de dados** | Inventario de equipamentos, tabela de precos de servicos, banco de freelancers com valores, custos operacionais fixos |
| **Calculos** | Custo total (diretos + indiretos) + margem configuravel + impostos estimados = preco sugerido |
| **Output** | Orcamento detalhado em formato estruturado, pronto para exportacao em PDF |
| **Inteligencia** | Sugestao de alternativas para otimizacao de custo (ex.: trocar locacao por equipamento proprio) |
| **Metricas** | Tempo de geracao, precisao do orcamento vs custo real pos-projeto |

### 6.8 Agentes de Avaliacao de Qualidade (RH-IA)

**Missao:** Monitorar e avaliar o desempenho de todos os agentes de IA, garantindo qualidade e melhoria continua.

| Caracteristica | Especificacao |
|---------------|---------------|
| **Escopo** | Todos os agentes IA ativos na plataforma |
| **Criterios** | Metas atingidas, qualidade dos outputs, taxa de falhas, feedback dos humanos supervisores |
| **Classificacao de Falhas** | Leve (erro menor, corrigivel automaticamente), Media (afetou fluxo, requer intervencao), Grave (impacto no cliente ou na operacao) |
| **Acoes** | Alerta ao lider > Observacao > Ajuste de parametros > Desvinculacao e criacao de novo agente aprimorado |
| **Heranca** | Ao criar novo agente substituto, herdar acertos e corrigir erros do agente anterior |
| **Metricas** | Score de performance por agente, tendencia de evolucao, tempo medio entre falhas |

### 6.3 Resumo: Ecossistema de Agentes

```
+------------------------------------------------------------------+
|                    ECOSSISTEMA DE AGENTES IA                      |
+------------------------------------------------------------------+
|                                                                    |
|  PROSPECCAO                 CONTEUDO                 INTELIGENCIA  |
|  +------------------+       +------------------+     +----------+  |
|  | Hunter Farma     |       | Gerador Texto    |     | Trend    |  |
|  | Hunter Auto      |       | Gerador Imagens  |     | Monitor  |  |
|  | Hunter Moda      |       | Gerador PDF      |     +----------+  |
|  | Hunter Saude     |       +------------------+                   |
|  | Hunter Tech      |                                              |
|  | Hunter Educacao  |       FINANCEIRO             RH              |
|  | ...              |       +------------------+   +----------+    |
|  +------------------+       | Calc. Orcamento  |   | Avaliador|    |
|                             +------------------+   | Qualidade|    |
|  ABORDAGEM                                         +----------+    |
|  +------------------+                                              |
|  | SDR WhatsApp     |                                              |
|  | SDR LinkedIn     |       SUPERVISAO HUMANA                     |
|  | SDR Instagram    |       +-----------------------------+       |
|  | SDR E-mail       |       | Diretor Geral + RH Humano  |       |
|  +------------------+       | avaliam e decidem sobre     |       |
|                             | continuidade dos agentes    |       |
|                             +-----------------------------+       |
+------------------------------------------------------------------+
```

---

## Consideracoes Finais

Esta analise de escopo revela que a Somos Produtora necessita de uma plataforma altamente integrada que funcione simultaneamente como:

1. **CRM** para gestao completa do funil comercial
2. **ERP** para controle financeiro, fiscal e contabil
3. **Sistema de gestao de projetos** com cards, tarefas e sprints
4. **Estudio de conteudo** com geracao assistida por IA (texto, imagem, PDF)
5. **Agendador social** para publicacao em multiplas redes
6. **Sistema de inventario** para equipamentos audiovisuais
7. **Plataforma de gestao de agentes de IA** com ciclo de vida e RH digital

A complexidade do projeto e significativa, porem a modularizacao proposta permite uma implementacao incremental, priorizando os modulos de maior impacto imediato (CRM + Hunters IA + SDR Ativo) e expandindo progressivamente para os demais.

O proximo passo recomendado e a **Fase 2 — Arquitetura Tecnica**, onde serao definidos stack tecnologico, banco de dados, APIs, infraestrutura e cronograma de implementacao.

---

*Documento gerado como parte do planejamento da Plataforma Integrada Somos Produtora.*
*Fase 1 de 4 — Analise de Escopo.*
