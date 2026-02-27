# Orcamento Inteligente

## Proposito e Visao

O modulo de Orcamento Inteligente e o motor financeiro comercial da Somos Produtora. Ele automatiza a criacao de orcamentos profissionais para projetos audiovisuais, integrando-se diretamente com o banco de dados de equipamentos (inventario), banco de dados de servicos e fornecedores, e tabelas de precos pre-configuradas. O objetivo e eliminar planilhas manuais, reduzir erros de calculo e garantir que cada orcamento reflita custos reais, margens de lucro adequadas e impostos aplicaveis.

Quando uma venda e iniciada, o colaborador humano monta o orcamento selecionando cameras, equipe tecnica, servicos terceirizados e horas de trabalho diretamente do banco de dados. O sistema calcula automaticamente o total considerando impostos (ISS, PIS, COFINS, IRPJ, CSLL), margens de lucro configuradas por tipo de servico, custos de freelancers e fornecedores externos. Ao final, gera um PDF profissional a partir de um layout pre-definido com a identidade visual da Somos.

A visao de longo prazo inclui sugestoes inteligentes baseadas em historico de projetos similares, alertas de desvio orcamentario e integracao completa com o modulo financeiro para acompanhamento de rentabilidade por projeto.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Diretor Comercial | Aprova orcamentos acima de determinado valor, define margens minimas | Aprovacao final, configuracao de margens |
| Produtor Executivo | Monta orcamentos para projetos audiovisuais, seleciona recursos | Criacao, edicao, envio ao cliente |
| Gerente de Projetos | Consulta orcamentos para planejamento de producao | Visualizacao, comentarios |
| Assistente Financeiro | Valida impostos e condicoes de pagamento | Revisao financeira, ajuste de impostos |
| Agente IA de Orcamentos | Sugere itens baseados em projetos similares, valida consistencia | Sugestao automatica, validacao |
| Cliente | Recebe o orcamento em PDF, aprova ou solicita ajustes | Visualizacao externa, aprovacao |
| Diretor Geral | Supervisao geral e aprovacao de orcamentos estrategicos | Acesso total |

## Funcionalidades Principais

### Montagem Interativa de Orcamento
- Interface drag-and-drop para adicionar itens ao orcamento
- Busca inteligente no banco de dados de equipamentos com filtros por categoria (cameras, lentes, iluminacao, audio, grip)
- Busca no banco de dados de servicos e fornecedores com precos atualizados
- Selecao de profissionais (equipe interna e freelancers) com diarias/horas configuradas
- Duplicacao rapida de orcamentos anteriores como base para novos projetos

### Calculo Automatico
- Soma de todos os itens com quantidades e periodos (diarias, horas, unidades)
- Aplicacao automatica de impostos conforme regime tributario (Simples Nacional, Lucro Presumido, MEI)
- Calculo de margem de lucro por categoria de servico (producao, pos-producao, criacao, midia)
- Inclusao automatica de custos de freelancers com encargos trabalhistas quando aplicavel
- Taxa de administracao configuravel por tipo de projeto
- Desconto condicional com aprovacao do Diretor Comercial

### Tabelas de Precos
- Tabelas de precos versionadas com historico de alteracoes
- Precos diferenciados por tipo de cliente (recorrente, novo, parceiro)
- Atualizacao em lote de precos com base em indices de inflacao (IPCA, IGPM)
- Precos de custo versus precos de venda separados para analise de margem

### Geracao de PDF Profissional
- Layout pre-definido com identidade visual da Somos Produtora
- Cabecalho com logo, dados da empresa, dados do cliente
- Tabela detalhada de itens com descricao, quantidade, valor unitario e subtotal
- Secao de condicoes comerciais e forma de pagamento
- Validade do orcamento com contagem regressiva automatica
- Assinatura digital opcional
- Versoes do orcamento (v1, v2, v3) com rastreio de alteracoes

### Fluxo de Aprovacao
- Orcamentos abaixo de R$ 5.000: aprovacao automatica pelo Produtor Executivo
- Orcamentos entre R$ 5.000 e R$ 50.000: aprovacao do Diretor Comercial
- Orcamentos acima de R$ 50.000: aprovacao do Diretor Geral
- Notificacao automatica para aprovadores via push e email
- Historico de aprovacoes com timestamp e comentarios

## Fluxo de Dados

```
[Solicitacao Comercial] --> [Selecao de Itens do BD]
                                    |
                    +---------------+---------------+
                    |               |               |
            [Equipamentos]   [Servicos]    [Profissionais]
            (Inventario)   (Fornecedores)   (RH/Freelancers)
                    |               |               |
                    +-------+-------+---------------+
                            |
                    [Motor de Calculo]
                            |
              +-------------+-------------+
              |             |             |
        [Impostos]    [Margens]    [Encargos]
              |             |             |
              +------+------+-------------+
                     |
              [Orcamento Montado]
                     |
              [Fluxo de Aprovacao]
                     |
              [Geracao de PDF]
                     |
              [Envio ao Cliente]
                     |
           +--------+---------+
           |                  |
    [Aprovado]          [Ajuste Solicitado]
           |                  |
    [Gera Contrato]    [Nova Versao]
```

## Entidades e Modelo de Dados

### Budget (Orcamento)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| code | String | Codigo sequencial (ORC-2026-0001) |
| client_id | UUID (FK) | Referencia ao cliente |
| project_name | String | Nome do projeto |
| version | Integer | Versao do orcamento (1, 2, 3...) |
| status | Enum | RASCUNHO, AGUARDANDO_APROVACAO, APROVADO, REJEITADO, EXPIRADO |
| subtotal | Decimal | Soma dos itens antes de impostos |
| tax_total | Decimal | Total de impostos calculados |
| margin_total | Decimal | Total de margem aplicada |
| grand_total | Decimal | Valor final do orcamento |
| valid_until | Date | Data de validade |
| payment_terms | Text | Condicoes de pagamento |
| notes | Text | Observacoes internas |
| approved_by | UUID (FK) | Quem aprovou |
| approved_at | DateTime | Data/hora da aprovacao |
| created_by | UUID (FK) | Quem criou |
| created_at | DateTime | Data de criacao |
| updated_at | DateTime | Ultima atualizacao |

### BudgetItem (Item do Orcamento)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| budget_id | UUID (FK) | Referencia ao orcamento |
| item_type | Enum | EQUIPAMENTO, SERVICO, PROFISSIONAL, OUTRO |
| reference_id | UUID (FK) | Referencia ao item no BD de origem |
| description | String | Descricao do item |
| category | String | Categoria (producao, pos-producao, criacao) |
| quantity | Decimal | Quantidade |
| unit | Enum | DIARIA, HORA, UNIDADE, PACOTE |
| unit_cost | Decimal | Custo unitario |
| unit_price | Decimal | Preco unitario de venda |
| subtotal | Decimal | Quantidade x Preco unitario |
| sort_order | Integer | Ordem de exibicao |

### PriceTable (Tabela de Precos)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| name | String | Nome da tabela |
| version | Integer | Versao da tabela |
| valid_from | Date | Inicio da vigencia |
| valid_until | Date | Fim da vigencia |
| client_tier | Enum | PADRAO, RECORRENTE, PARCEIRO, VIP |
| is_active | Boolean | Se esta ativa |

### ServiceItem (Item de Servico)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| price_table_id | UUID (FK) | Referencia a tabela de precos |
| name | String | Nome do servico |
| category | String | Categoria |
| cost_price | Decimal | Preco de custo |
| sell_price | Decimal | Preco de venda |
| unit | Enum | DIARIA, HORA, UNIDADE, PACOTE |
| supplier_id | UUID (FK) | Fornecedor associado |

### EquipmentItem (Item de Equipamento)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| price_table_id | UUID (FK) | Referencia a tabela de precos |
| equipment_id | UUID (FK) | Referencia ao inventario |
| rental_daily_rate | Decimal | Valor de diaria para locacao |
| rental_hourly_rate | Decimal | Valor por hora para locacao |
| replacement_cost | Decimal | Custo de reposicao (para seguro) |

## Interface e UX

### Tela Principal - Montagem de Orcamento
- Layout em duas colunas: catalogo de itens a esquerda, orcamento sendo montado a direita
- Barra de busca com filtros rapidos por categoria no catalogo
- Itens adicionados via clique ou drag-and-drop
- Resumo financeiro fixo no rodape (subtotal, impostos, margem, total)
- Botoes de acao: Salvar Rascunho, Enviar para Aprovacao, Gerar PDF, Duplicar

### Tela de Aprovacao
- Lista de orcamentos pendentes com indicadores visuais de urgencia
- Visualizacao rapida do orcamento com detalhes expandiveis
- Botoes de Aprovar / Solicitar Ajustes com campo de comentario obrigatorio
- Historico de versoes com diff visual entre versoes

### Visualizacao do PDF
- Preview em tempo real do PDF enquanto o orcamento e montado
- Opcao de personalizar layout (margens, cores, posicao do logo)
- Download em PDF e envio direto por email ao cliente

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| Inventario | Leitura | Consulta equipamentos disponiveis e precos de locacao |
| Fornecedores | Leitura | Consulta servicos terceirizados e precos |
| Financeiro | Escrita | Registra orcamento aprovado para faturamento |
| Contratos | Escrita | Gera contrato automaticamente apos aprovacao |
| CRM | Leitura/Escrita | Vincula orcamento a oportunidade de venda |
| RH/Agentes | Leitura | Consulta disponibilidade e custos de profissionais |

## Agentes de IA

### Agente Assistente de Orcamento
- **Funcao**: Sugere itens com base em projetos similares anteriores
- **Modelo**: Claude (analise de contexto e sugestao)
- **Gatilhos**: Quando o usuario inicia um novo orcamento, o agente analisa o tipo de projeto e sugere uma lista inicial de itens
- **Saida**: Lista de itens sugeridos com justificativa

### Agente Validador de Consistencia
- **Funcao**: Verifica se o orcamento esta completo e consistente
- **Modelo**: Claude (validacao logica)
- **Verificacoes**: Itens obrigatorios faltando (ex: seguro para equipamentos caros), margens abaixo do minimo, precos desatualizados
- **Saida**: Lista de alertas e recomendacoes antes do envio

### Agente de Analise de Rentabilidade
- **Funcao**: Compara orcamento com historico para prever rentabilidade
- **Modelo**: Claude (analise preditiva)
- **Saida**: Score de rentabilidade e comparativo com projetos similares

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Frontend | Next.js + React | Interface dinamica com preview em tempo real |
| Geracao de PDF | Puppeteer + Handlebars | Templates HTML convertidos em PDF de alta qualidade |
| Calculo de Impostos | Biblioteca customizada | Regras tributarias brasileiras especificas |
| Drag-and-Drop | dnd-kit (React) | Biblioteca performatica para reordenacao de itens |
| Cache de Precos | Redis | Acesso rapido a tabelas de precos frequentemente consultadas |
| Armazenamento de PDFs | AWS S3 / Supabase Storage | PDFs gerados armazenados com versionamento |
| Backend | Node.js + Fastify | API rapida para calculos em tempo real |
| Banco de Dados | PostgreSQL (Supabase) | Consultas complexas com joins entre multiplas tabelas |

## Dependencias

### Dependencias de Modulo
- **Inventario**: Obrigatoria - sem inventario nao ha equipamentos para selecionar
- **Fornecedores/Servicos**: Obrigatoria - servicos terceirizados compoem grande parte dos orcamentos
- **CRM**: Recomendada - vinculacao com oportunidades de venda
- **Financeiro**: Obrigatoria - orcamentos aprovados alimentam o modulo financeiro
- **Contratos**: Recomendada - geracao automatica de contrato apos aprovacao

### Dependencias Tecnicas
- Supabase configurado com tabelas de inventario e servicos populadas
- Templates de PDF configurados com identidade visual da Somos
- Regras tributarias configuradas conforme regime da empresa
- Tabelas de precos iniciais cadastradas

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Tempo medio de criacao de orcamento | < 15 minutos | Timestamp criacao vs envio |
| Taxa de aprovacao no primeiro envio | > 70% | Orcamentos aprovados na v1 / total |
| Precisao dos calculos de impostos | 100% | Auditoria mensal comparativa |
| Margem media por projeto | > 30% | (Venda - Custo) / Venda |
| Tempo de aprovacao interna | < 24 horas | Timestamp envio vs aprovacao |
| Orcamentos gerados por mes | Crescimento de 10% m/m | Contagem mensal |
| Taxa de conversao orcamento-contrato | > 40% | Contratos gerados / orcamentos enviados |
| Satisfacao do cliente com orcamento | > 4.5/5 | Pesquisa pos-envio |
