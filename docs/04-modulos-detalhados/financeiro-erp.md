# Financeiro e ERP

## Proposito e Visao

O modulo Financeiro e ERP e a espinha dorsal administrativa da Somos Produtora. Ele centraliza toda a gestao financeira da empresa: contas a pagar e a receber, emissao de notas fiscais (NF-e), controle de margens de lucro, comissoes de vendas, orcamentos por projeto, impostos e fluxo de caixa. A visao e eliminar planilhas dispersas e trazer visibilidade financeira total em tempo real.

Este modulo foi projetado para atender as necessidades especificas de uma produtora audiovisual e agencia 360, onde cada projeto tem seu proprio orcamento, custos de equipe, equipamentos, fornecedores terceirizados e margens que precisam ser calculadas individualmente. Alem disso, a natureza dos servicos (projetos pontuais combinados com contratos recorrentes de gestao de redes sociais) exige flexibilidade no modelo de cobranca e faturamento.

O objetivo e que qualquer gestor da Somos possa, em poucos cliques, visualizar a saude financeira da empresa, a rentabilidade de cada projeto, o fluxo de caixa projetado e as obrigacoes fiscais pendentes.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Diretor Financeiro / CEO | Estrategista | Define politicas financeiras, aprova orcamentos, analisa rentabilidade |
| Analista Financeiro | Executor | Registra lancamentos, concilia pagamentos, emite notas fiscais |
| Contador (externo ou interno) | Consultor | Orienta sobre tributacao, valida demonstracoes, fecha balancetes |
| Produtor Executivo | Informante | Fornece dados de custos de producao, valida orcamentos de projeto |
| Gerente Comercial | Informante | Informa comissoes, valores de contratos, condicoes de pagamento |
| Fornecedores | Destinatario | Recebem pagamentos conforme contas a pagar |
| Clientes | Pagador | Efetuam pagamentos conforme contas a receber |

## Funcionalidades Principais

### Contas a Receber

- Registro de todas as receitas esperadas vinculadas a contratos e projetos
- Parcelamento automatico conforme condicoes de pagamento do contrato
- Acompanhamento de status: A Vencer, Vencido, Pago, Parcialmente Pago, Cancelado
- Alertas de vencimento proximo (3 dias antes) e inadimplencia (1 dia apos)
- Baixa automatica via integracao bancaria ou manual com comprovante
- Historico de pagamentos por cliente com analise de pontualidade

### Contas a Pagar

- Registro de todas as despesas: fornecedores, freelancers, aluguel, equipamentos, licencas de software
- Vinculacao de despesas ao projeto correspondente (rateio quando aplicavel)
- Aprovacao de pagamentos com alcada por valor (ate R$ 5.000 gerente, acima CEO)
- Calendario de pagamentos com visao mensal
- Registro de comprovantes de pagamento
- Conciliacao bancaria para verificar pagamentos efetuados

### Emissao de Notas Fiscais (NF-e)

- Geracao de NF-e de servicos vinculada a conta a receber
- Preenchimento automatico de dados: tomador (CNPJ, razao social), servicos prestados, valores, impostos
- Calculo automatico de impostos: ISS, PIS, COFINS, IRPJ, CSLL (conforme regime tributario da Somos)
- Envio para prefeitura via API do sistema de NF-e municipal
- Armazenamento do XML e PDF da nota fiscal
- Cancelamento e substituicao de notas com justificativa

### Orcamento por Projeto

- Criacao de orcamento detalhado por projeto com categorias de custo
- Categorias padrao: Equipe Interna, Freelancers, Equipamentos (aluguel/uso), Locacoes, Alimentacao, Transporte, Pos-producao, Licencas, Outros
- Comparacao orcado vs realizado em tempo real
- Alertas quando custo realizado ultrapassa determinado percentual do orcado (70%, 90%, 100%)
- Margem de lucro calculada automaticamente: (Receita - Custos) / Receita
- Historico de orcamentos para referencia em projetos similares futuros

### Comissoes de Vendas

- Regras de comissao configuraveis por tipo de servico, valor do deal ou responsavel
- Calculo automatico de comissao ao fechar deal no CRM
- Acompanhamento de comissoes a pagar por periodo
- Relatorio de comissoes por vendedor com detalhamento por deal
- Vinculacao com folha de pagamento ou pagamento avulso

### Analise e Impostos

- Dashboard de obrigacoes fiscais por periodo
- Calculo de impostos devidos com base nas notas emitidas
- Guias de recolhimento geradas para ISS, DAS (se Simples), DARF (se Lucro Presumido)
- Relatorio de faturamento por competencia para prestacao de contas ao contador
- Exportacao de dados no formato exigido pela contabilidade

### Fluxo de Caixa

- Projecao de fluxo de caixa com base em contas a receber e a pagar
- Visao diaria, semanal e mensal com grafico de linha
- Cenarios: otimista (todos pagam em dia), realista (historico de atrasos), pessimista (inadimplencia)
- Saldo projetado por periodo com alertas de saldo negativo
- DRE (Demonstracao de Resultado) simplificada por periodo

## Fluxo de Dados

```
[Contrato assinado / Projeto criado]
        |
        v
[Geracao de contas a receber (parcelas)]
        |
        v
[Emissao de NF-e por parcela faturada]
        |
        v
[Acompanhamento de pagamentos recebidos]
        |
        v
[Custos do projeto registrados como contas a pagar]
        |
        v
[Aprovacao e execucao de pagamentos]
        |
        v
[Calculo de margem: Receita - Custos = Lucro do Projeto]
        |
        v
[Calculo de comissoes sobre deals fechados]
        |
        v
[Consolidacao em fluxo de caixa e DRE]
        |
        v
[Dashboard financeiro atualizado em tempo real]
```

## Entidades e Modelo de Dados

### Invoice (Nota Fiscal)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| numero_nf | String | Numero da nota fiscal |
| contrato_id | FK(Contract) | Contrato associado |
| projeto_id | FK(Project) | Projeto associado |
| empresa_cliente_id | FK(Company) | Tomador do servico |
| valor_bruto | Decimal | Valor antes dos impostos |
| impostos | JSON | Detalhamento de impostos (ISS, PIS, COFINS, etc.) |
| valor_liquido | Decimal | Valor apos impostos |
| descricao_servico | Text | Descricao do servico na NF |
| competencia | Date | Mes de competencia |
| data_emissao | Date | Data de emissao |
| status | Enum | Emitida, Cancelada, Substituida |
| xml_url | String | URL do XML da NF-e |
| pdf_url | String | URL do PDF da NF-e |
| protocolo_prefeitura | String | Numero de protocolo na prefeitura |

### Payment (Conta a Receber / Pagar)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| tipo | Enum | Receber, Pagar |
| descricao | String | Descricao do lancamento |
| contrato_id | FK(Contract) | Contrato associado (se receber) |
| projeto_id | FK(Project) | Projeto associado |
| fornecedor_id | FK(Supplier) | Fornecedor (se pagar) |
| valor | Decimal | Valor da parcela |
| data_vencimento | Date | Data de vencimento |
| data_pagamento | Date | Data efetiva do pagamento |
| status | Enum | AVencer, Vencido, Pago, ParcialmentePago, Cancelado |
| forma_pagamento | Enum | Boleto, PIX, Transferencia, CartaoCredito, Dinheiro |
| comprovante_url | String | URL do comprovante |
| parcela | String | Indicacao de parcela (ex: "3/6") |
| nota_fiscal_id | FK(Invoice) | NF vinculada (se aplicavel) |
| aprovado_por | FK(User) | Quem aprovou o pagamento |
| categoria | String | Categoria de despesa/receita |

### Budget (Orcamento)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| projeto_id | FK(Project) | Projeto associado |
| valor_total_orcado | Decimal | Orcamento total aprovado |
| valor_total_realizado | Decimal | Custo acumulado real |
| margem_prevista | Float | Margem de lucro prevista (%) |
| margem_real | Float | Margem de lucro real (%) |
| categorias | JSON | Orcado vs realizado por categoria |
| status | Enum | Rascunho, Aprovado, EmExecucao, Encerrado |
| aprovado_por | FK(User) | Quem aprovou |
| criado_em | DateTime | Data de criacao |

### Expense (Despesa Detalhada)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| budget_id | FK(Budget) | Orcamento associado |
| projeto_id | FK(Project) | Projeto associado |
| categoria | Enum | EquipeInterna, Freelancers, Equipamentos, Locacoes, Alimentacao, Transporte, PosProducao, Licencas, Outros |
| descricao | String | Descricao da despesa |
| valor | Decimal | Valor da despesa |
| fornecedor_id | FK(Supplier) | Fornecedor (se aplicavel) |
| data | Date | Data da despesa |
| comprovante_url | String | URL do comprovante |
| rateio | JSON | Rateio entre projetos (quando aplicavel) |

### Commission (Comissao)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| deal_id | FK(Deal) | Deal de origem |
| vendedor_id | FK(User) | Vendedor comissionado |
| valor_deal | Decimal | Valor do deal fechado |
| percentual_comissao | Float | Percentual de comissao |
| valor_comissao | Decimal | Valor calculado da comissao |
| status | Enum | Calculada, Aprovada, Paga |
| data_pagamento | Date | Data de pagamento da comissao |

### TaxRecord (Registro Fiscal)

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| tipo_imposto | Enum | ISS, PIS, COFINS, IRPJ, CSLL, DAS |
| competencia | Date | Mes de competencia |
| base_calculo | Decimal | Base de calculo |
| aliquota | Float | Aliquota aplicada |
| valor | Decimal | Valor do imposto |
| status | Enum | Calculado, Pago, Atrasado |
| guia_url | String | URL da guia de recolhimento |
| data_vencimento | Date | Data limite de pagamento |

## Interface e UX

### Dashboard Financeiro Principal

- Cards no topo com KPIs: Receita do Mes, Despesas do Mes, Lucro Liquido, Saldo em Caixa, Inadimplencia
- Grafico de pizza: distribuicao de receita por tipo de servico
- Grafico de barras: receita vs despesa por mes (ultimos 12 meses)
- Grafico de linha: fluxo de caixa projetado para os proximos 90 dias
- Lista de contas vencidas com acao rapida de cobranca

### Tela de Contas a Receber/Pagar

- Tabela filtravel com todas as contas do periodo selecionado
- Filtros por status, tipo, cliente/fornecedor, projeto, periodo
- Agrupamento por vencimento (hoje, esta semana, este mes, atrasados)
- Acao rapida: registrar pagamento, emitir NF, enviar cobranca
- Totalizadores: total a vencer, total vencido, total pago no periodo

### Tela de Orcamento do Projeto

- Comparativo visual orcado vs realizado por categoria (grafico de barras horizontal)
- Semaforo de saude: verde (< 70% do orcamento), amarelo (70-90%), vermelho (> 90%)
- Lista de despesas detalhadas com filtro por categoria
- Indicador de margem de lucro prevista e real com destaque

### Calendario de Pagamentos

- Visualizacao mensal com marcacoes de pagamentos (verde = receber, vermelho = pagar)
- Saldo projetado dia a dia
- Click no dia para ver detalhes dos lancamentos
- Alertas de dias com saldo negativo projetado

### Relatorios Financeiros

- DRE simplificada por periodo
- Balancete por projeto
- Relatorio de comissoes
- Relatorio de faturamento por cliente
- Exportacao em PDF, Excel e CSV

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| Modulo CRM Pipeline | Dados de deals para calculo de comissoes e previsao | Interno (consulta) |
| Modulo Gestao de Contratos | Valores e condicoes de pagamento dos contratos | Interno (evento) |
| Modulo Gestao de Projetos | Orcamento e custos de projeto | Interno (bidirecional) |
| Modulo Fornecedores | Dados de fornecedores para contas a pagar | Interno (consulta) |
| Modulo Inventario | Custos de equipamentos alocados | Interno (consulta) |
| Prefeitura (NF-e) | Emissao e cancelamento de notas fiscais | API REST (webservice) |
| Banco (Open Banking) | Conciliacao bancaria, extrato automatico | API / OFX |
| Contabilidade (Dominio, Sage) | Exportacao de dados fiscais e contabeis | Arquivo CSV/XML |
| PIX / Boleto | Geracao de boletos e chaves PIX para cobranca | API bancaria |

## Agentes de IA

### Agente de Analise Financeira

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Analisar dados financeiros e gerar insights sobre rentabilidade, tendencias de fluxo de caixa e oportunidades de otimizacao
- **Comportamento**: Analisa historico financeiro, identifica padroes de inadimplencia, projeta cenarios e sugere acoes preventivas

### Agente de Classificacao de Despesas

- **Modelo**: GPT-4o
- **Funcao**: Classificar automaticamente despesas em categorias corretas com base na descricao e historico
- **Comportamento**: Recebe descricao da despesa e fornecedor, classifica na categoria correta, sugere rateio entre projetos quando aplicavel

### Agente de Cobranca Inteligente

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Gerar mensagens de cobranca personalizadas para clientes inadimplentes, respeitando o relacionamento comercial
- **Comportamento**: Analisa historico do cliente, tempo de atraso e valor devido. Gera mensagem adequada ao contexto: lembrete gentil, segunda via, cobranca formal.

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Backend | Node.js com TypeScript | Consistencia com stack, bom para calculos financeiros |
| Banco de Dados | PostgreSQL | Integridade transacional para dados financeiros |
| Graficos | Recharts ou Chart.js | Graficos interativos para dashboards financeiros |
| Geracao de PDF | Puppeteer | Relatorios e boletos em PDF |
| NF-e | Biblioteca SEFAZ ou API de terceiro (eNotas, NFe.io) | Emissao de notas fiscais |
| Calculos Fiscais | Biblioteca customizada | Regras fiscais brasileiras (ISS, PIS, COFINS, etc.) |
| Calendario | FullCalendar | Calendario de pagamentos |
| Exportacao | ExcelJS, PDFKit | Exportacao de relatorios |

## Dependencias

- **Modulo CRM Pipeline**: Dados de deals para comissoes e previsao de receita
- **Modulo Gestao de Contratos**: Valores contratuais e condicoes de pagamento
- **Modulo Gestao de Projetos**: Orcamentos e custos de projeto
- **Modulo Fornecedores**: Cadastro de fornecedores para contas a pagar
- **Modulo Inventario**: Custos de uso de equipamentos
- **Servico de NF-e**: Integracao com prefeitura para emissao de notas
- **Conta Bancaria**: Credenciais para conciliacao e emissao de boletos
- **Assessoria Contabil**: Parametros fiscais e regime tributario configurados

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Margem de Lucro Media | Margem media de todos os projetos | >= 35% |
| Inadimplencia | Porcentagem de receitas nao recebidas apos 30 dias | <= 5% |
| Tempo de Emissao de NF | Tempo entre faturamento e emissao da NF-e | <= 24 horas |
| Precisao do Orcamento | Desvio medio entre orcado e realizado | <= 15% |
| Tempo de Conciliacao | Tempo para conciliar pagamentos recebidos | <= 1 dia util |
| Fluxo de Caixa Positivo | Meses com saldo positivo no ano | >= 11 de 12 |
| Comissoes Processadas | Tempo entre fechamento do deal e calculo da comissao | <= 24 horas |
| Adimplencia de Pagamentos | Pagamentos a fornecedores feitos em dia | >= 98% |
