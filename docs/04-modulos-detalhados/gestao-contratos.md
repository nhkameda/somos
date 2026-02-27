# Gestao de Contratos

## Proposito e Visao

O modulo de Gestao de Contratos automatiza todo o ciclo de vida dos contratos da Somos Produtora, desde a geracao automatica a partir de deals ganhos no CRM ate a assinatura digital, versionamento e arquivo. Ele elimina o trabalho manual e repetitivo de redigir contratos, garantindo padronizacao, conformidade juridica e agilidade no fechamento de negocios.

A visao e que, ao ganhar um deal no CRM Pipeline, o sistema gere automaticamente um contrato completo e personalizado com base nos dados do deal (empresa, valores, servicos, prazos), utilizando templates previamente aprovados pelo departamento juridico. O responsavel comercial revisa, ajusta se necessario, e envia para assinatura digital sem precisar sair da plataforma.

O modulo tambem gerencia versoes de contratos, permitindo rastrear alteracoes, aditivos e renovacoes ao longo do tempo, criando um historico completo e auditavel de cada relacionamento contratual.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Gerente Comercial | Solicitante | Inicia a geracao do contrato a partir do deal, revisa e envia para assinatura |
| Assessoria Juridica | Validador | Cria e aprova templates, revisa clausulas customizadas, valida termos |
| Diretor/CEO | Aprovador | Aprova contratos acima de determinado valor, assina em nome da Somos |
| Cliente | Signatario | Recebe o contrato, revisa, solicita ajustes e assina digitalmente |
| Financeiro | Observer | Acompanha valores contratuais, condicoes de pagamento e vigencia |
| Administrador | Configurador | Gerencia templates, variaveis e regras de geracao automatica |

## Funcionalidades Principais

### Geracao Automatica de Contratos

- Criacao de contrato a partir de um deal ganho no CRM com um clique
- Preenchimento automatico de variaveis: razao social, CNPJ, endereco, nome do contato, valor total, parcelas, servicos contratados, prazo de vigencia, prazo de entrega
- Selecao automatica de template conforme o tipo de servico (producao audiovisual, gestao de redes sociais, consultoria, curso online, evento)
- Preview do contrato gerado antes da finalizacao
- Edicao manual de qualquer secao antes de enviar para assinatura

### Biblioteca de Templates

- Repositorio de templates de contrato organizados por tipo de servico
- Editor de templates com suporte a variaveis dinamicas (sintaxe tipo {{empresa.razao_social}})
- Versionamento de templates com historico de alteracoes
- Status de template: Rascunho, Em Revisao Juridica, Aprovado, Depreciado
- Clausulas modulares reutilizaveis entre templates (confidencialidade, propriedade intelectual, rescisao, etc.)

### Clausulas Modulares

- Biblioteca de clausulas padrao aprovadas pelo juridico
- Clausulas obrigatorias por tipo de contrato (ex: LGPD sempre presente)
- Clausulas opcionais que podem ser adicionadas conforme a negociacao
- Cada clausula com versao, data de aprovacao e responsavel pela aprovacao
- Busca e filtro de clausulas por categoria: pagamento, entrega, confidencialidade, propriedade intelectual, SLA, penalidades

### Assinatura Digital

- Integracao com plataformas de assinatura digital (DocuSign, Clicksign, ou equivalente nacional)
- Fluxo de assinatura configuravel: apenas cliente, cliente + diretor, multiplos signatarios
- Notificacao automatica para signatarios pendentes
- Acompanhamento em tempo real do status de assinatura
- Armazenamento seguro do documento assinado com certificado digital
- Validade juridica conforme legislacao brasileira (MP 2.200-2/2001 e Lei 14.063/2020)

### Controle de Versoes e Aditivos

- Historico completo de todas as versoes de cada contrato
- Comparacao visual entre versoes (diff) com destaque de alteracoes
- Criacao de aditivos vinculados ao contrato original
- Registro de quem alterou, quando e qual a justificativa
- Status de versao: Rascunho, Em Revisao, Aprovado, Assinado, Cancelado

### Geracao de PDF com Identidade Visual

- Renderizacao do contrato em PDF com header e footer da Somos Produtora
- Logo, cores e tipografia da marca aplicados automaticamente
- Numeracao de paginas, marca d'agua para rascunhos
- QR Code no documento para verificacao de autenticidade
- Opcao de gerar em formato A4 ou formato digital otimizado para tela

## Fluxo de Dados

```
[Deal marcado como "Ganho" no CRM Pipeline]
        |
        v
[Sistema identifica tipo de servico e seleciona template]
        |
        v
[Preenchimento automatico de variaveis com dados do deal]
        |
        v
[Selecao de clausulas modulares aplicaveis]
        |
        v
[Geracao do rascunho do contrato]
        |
        v
[Revisao pelo Gerente Comercial]
        |
        +---> [Ajustes necessarios] ---> [Edicao manual] ---> [Nova revisao]
        |
        v
[Aprovacao pelo Juridico (se necessario)]
        |
        v
[Geracao do PDF final com identidade visual]
        |
        v
[Envio para assinatura digital]
        |
        v
[Cliente recebe e assina]
        |
        v
[Diretor/CEO assina (se aplicavel)]
        |
        v
[Contrato assinado armazenado e vinculado ao deal/projeto]
        |
        v
[Notificacao para Financeiro e Gestao de Projetos]
```

## Entidades e Modelo de Dados

### Contract

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico do contrato |
| numero | String | Numero sequencial do contrato (ex: SOMOS-2026-0042) |
| deal_id | FK(Deal) | Deal de origem no CRM |
| empresa_id | FK(Company) | Empresa contratante |
| template_id | FK(Template) | Template utilizado |
| tipo_servico | Enum | ProducaoAudiovisual, GestaoRedes, CursoOnline, Evento, Consultoria |
| valor_total | Decimal | Valor total do contrato |
| condicao_pagamento | JSON | Parcelas, datas de vencimento, forma de pagamento |
| data_inicio | Date | Inicio da vigencia |
| data_fim | Date | Fim da vigencia |
| status | Enum | Rascunho, EmRevisao, Aprovado, EnviadoParaAssinatura, Assinado, Cancelado, Expirado |
| conteudo_html | Text | Conteudo completo do contrato em HTML |
| pdf_url | String | URL do PDF gerado |
| assinatura_url | String | URL para assinatura digital |
| clausulas | Array[FK(Clause)] | Clausulas incluidas |
| versao_atual | Integer | Numero da versao atual |
| criado_por | FK(User) | Responsavel pela criacao |
| criado_em | DateTime | Data de criacao |
| assinado_em | DateTime | Data de assinatura completa |

### Template

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do template |
| tipo_servico | Enum | Tipo de servico que o template atende |
| conteudo_html | Text | Conteudo com variaveis dinamicas |
| variaveis | Array[String] | Lista de variaveis utilizadas |
| status | Enum | Rascunho, EmRevisao, Aprovado, Depreciado |
| versao | Integer | Numero da versao |
| aprovado_por | FK(User) | Quem aprovou o template |
| aprovado_em | DateTime | Data de aprovacao |
| criado_em | DateTime | Data de criacao |

### Clause

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| titulo | String | Titulo da clausula |
| conteudo | Text | Texto completo da clausula |
| categoria | Enum | Pagamento, Entrega, Confidencialidade, PropriedadeIntelectual, SLA, Penalidades, LGPD, Rescisao, ForoJuridico |
| obrigatoria | Boolean | Se e obrigatoria em todos os contratos |
| tipos_servico | Array[Enum] | Tipos de servico onde se aplica |
| versao | Integer | Numero da versao |
| aprovada_por | FK(User) | Quem aprovou |
| ativa | Boolean | Se esta ativa para uso |

### Signature

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| contrato_id | FK(Contract) | Contrato associado |
| signatario_nome | String | Nome do signatario |
| signatario_email | String | Email do signatario |
| signatario_tipo | Enum | Cliente, DiretorSomos, Testemunha |
| status | Enum | Pendente, Assinado, Recusado, Expirado |
| assinado_em | DateTime | Data e hora da assinatura |
| ip_address | String | IP do signatario no momento da assinatura |
| certificado_id | String | ID do certificado digital |
| ordem | Integer | Ordem de assinatura |

### ContractVersion

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| contrato_id | FK(Contract) | Contrato associado |
| numero_versao | Integer | Numero sequencial da versao |
| conteudo_html | Text | Conteudo da versao |
| alteracoes | Text | Descricao das alteracoes |
| tipo | Enum | Original, Revisao, Aditivo, Renovacao |
| criado_por | FK(User) | Autor da versao |
| criado_em | DateTime | Data de criacao |

## Interface e UX

### Lista de Contratos

- Tabela com todos os contratos, filtravel por status, tipo de servico, empresa, periodo, responsavel
- Indicadores visuais de status com cores distintas (rascunho em cinza, em assinatura em amarelo, assinado em verde, cancelado em vermelho)
- Busca textual por numero do contrato, empresa ou contato
- Exportacao de lista para CSV e Excel

### Editor de Contrato

- Editor WYSIWYG com barra de ferramentas para formatacao
- Painel lateral com variaveis disponiveis para insercao (drag-and-drop ou clique)
- Painel de clausulas modulares para adicionar/remover
- Preview em tempo real do PDF resultante
- Modo de comparacao para visualizar diferencas entre versoes
- Historico de edicoes com possibilidade de reverter

### Fluxo de Assinatura

- Tela de configuracao do fluxo: definir signatarios, ordem e prazo
- Acompanhamento visual com timeline mostrando status de cada signatario
- Botao para reenviar convite de assinatura
- Visualizacao do documento assinado com certificados

### Editor de Templates

- Interface similar ao editor de contrato, porem com foco em templates
- Insersor de variaveis com autocomplete
- Preview com dados ficticio para testar renderizacao
- Fluxo de aprovacao integrado (enviar para juridico, aprovar, publicar)

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| Modulo CRM Pipeline | Recepcao de dados do deal para geracao de contrato | Interno (evento) |
| Modulo Gestao de Projetos | Notificacao de contrato assinado para inicio de projeto | Interno (evento) |
| Modulo Financeiro/ERP | Envio de condicoes de pagamento e valores | Interno (evento) |
| DocuSign ou Clicksign | Assinatura digital de contratos | API REST |
| Receita Federal (CNPJ) | Validacao de dados da empresa contratante | API REST |
| Google Drive / S3 | Armazenamento seguro de PDFs | API |
| Servico de Email | Notificacoes de status de contrato | API (SendGrid/SES) |

## Agentes de IA

### Agente de Geracao de Contrato

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Preencher variaveis do template com dados do deal, adaptar linguagem ao contexto do servico e sugerir clausulas relevantes
- **Comportamento**: Recebe dados do deal e template selecionado. Preenche todas as variaveis. Sugere clausulas opcionais baseado no tipo de servico e valor do contrato. Gera preview para revisao.

### Agente de Revisao Juridica

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Revisar o contrato gerado, identificar inconsistencias, sugerir melhorias e verificar conformidade com templates aprovados
- **Comportamento**: Analisa o contrato completo, compara com o template original, destaca clausulas faltantes ou modificadas, verifica se valores e prazos sao coerentes.

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Editor WYSIWYG | TipTap ou Plate | Editores modernos com suporte a variaveis e plugins |
| Geracao de PDF | Puppeteer ou React-PDF | Renderizacao fiel de HTML para PDF com estilos |
| Armazenamento de PDF | AWS S3 ou Google Cloud Storage | Armazenamento escalavel e seguro |
| Assinatura Digital | Clicksign API | Plataforma brasileira com validade juridica |
| Backend | Node.js com TypeScript | Consistencia com stack do projeto |
| Banco de Dados | PostgreSQL | Armazenamento relacional com suporte a JSON |
| Diff de Texto | diff-match-patch | Comparacao visual entre versoes de contrato |
| Template Engine | Handlebars ou Mustache | Renderizacao de variaveis nos templates |

## Dependencias

- **Modulo CRM Pipeline**: Fonte de dados dos deals para geracao de contratos
- **Modulo Gestao de Projetos**: Destino da notificacao de contrato assinado
- **Modulo Financeiro/ERP**: Recepcao de dados de pagamento e faturamento
- **Servico de Assinatura Digital**: Conta ativa em plataforma de assinatura (Clicksign ou equivalente)
- **Servico de Armazenamento**: Bucket configurado para armazenar PDFs
- **Assessoria Juridica**: Templates aprovados e clausulas validadas antes do go-live

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Tempo de Geracao | Tempo entre deal ganho e contrato gerado | <= 5 minutos |
| Tempo de Assinatura | Tempo entre envio e assinatura completa | <= 48 horas |
| Taxa de Aprovacao Sem Ajuste | Contratos aprovados na primeira versao | >= 70% |
| Contratos Gerados por Mes | Volume mensal de contratos | Acompanhamento |
| Taxa de Assinatura | Contratos enviados que foram efetivamente assinados | >= 95% |
| Erros de Preenchimento | Contratos com erros de variaveis ou dados | <= 2% |
| Satisfacao do Cliente | Feedback sobre experiencia de assinatura digital | >= 9/10 |
| Cobertura de Templates | Porcentagem de servicos com template aprovado | 100% |
