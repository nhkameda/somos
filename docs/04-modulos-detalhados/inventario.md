# Inventario

## Proposito e Visao

O modulo de Inventario gerencia todo o acervo de equipamentos da Somos Produtora, desde cameras profissionais e lentes ate equipamentos de iluminacao, audio, drones e acessorios. Ele oferece controle completo de disponibilidade, estado de conservacao, manutencao preventiva e alocacao para producoes, garantindo que a equipe sempre saiba o que esta disponivel, onde esta e em que condicoes.

A visao e que o equipamento nunca seja um gargalo operacional. Ao planejar uma producao, o produtor executivo deve poder consultar rapidamente quais equipamentos estao disponiveis na data necessaria, reserva-los e incluir o custo proporcional no orcamento do projeto. Equipamentos alugados de terceiros tambem sao rastreados aqui, permitindo comparacao de custo entre uso de equipamento proprio e aluguel externo.

Alem disso, o modulo implementa controle de manutencao preventiva, com alertas para revisoes periodicas, calibracoes e atualizacoes de firmware, prolongando a vida util dos equipamentos e evitando problemas durante producoes.

## Atores Envolvidos

| Ator | Papel | Responsabilidades |
|------|-------|-------------------|
| Produtor Executivo | Solicitante | Reserva equipamentos para producoes, consulta disponibilidade |
| Tecnico de Equipamentos | Gestor | Cadastra, atualiza status, realiza manutencoes, controla saidas e retornos |
| Diretor de Fotografia | Consultor | Indica equipamentos necessarios para cada tipo de producao |
| Financeiro | Observer | Acompanha custos de manutencao, depreciacao e decisoes aluguel vs compra |
| Freelancer | Usuario | Retira e devolve equipamentos para producoes externas |
| Administrador | Configurador | Gerencia categorias, locais de armazenamento e regras de reserva |

## Funcionalidades Principais

### Cadastro Completo de Equipamentos

- Registro detalhado de cada item: nome, marca, modelo, numero de serie, ano de aquisicao, valor de compra
- Categorias: Cameras, Lentes, Iluminacao, Audio (microfones, gravadores), Drones, Tripes e Estabilizadores, Monitores, Computadores de Edicao, Acessorios, Cenografia
- Fotos do equipamento para identificacao visual
- Especificacoes tecnicas (resolucao, sensor, distancia focal, potencia, etc.)
- Status: Disponivel, Em Uso, Em Manutencao, Reservado, Aposentado, Alugado de Terceiro
- QR Code gerado para cada equipamento para leitura rapida

### Equipamentos Cadastrados (Exemplos do Acervo)

| Categoria | Exemplos |
|-----------|----------|
| Cameras | Blackmagic URSA Mini Pro 12K, Blackmagic Pocket Cinema 6K Pro, Sony FX6, Sony A7S III, Sony FX3 |
| Lentes | Canon CN-E 24mm T1.5, Sigma 18-35mm f/1.8, Sony 24-70mm f/2.8 GM, Rokinon Cine DS Set |
| Iluminacao | Aputure 600d Pro, Aputure 300x, Nanlite Forza 500, Godox SL200II, Painel LED bicolor |
| Audio | Sennheiser MKH 416, Rode NTG5, Zoom F6 Recorder, Wireless Sennheiser EW 112P G4, Boom poles |
| Drones | DJI Inspire 3, DJI Mavic 3 Pro, DJI Mini 4 Pro |
| Estabilizadores | DJI RS 4 Pro, DJI Ronin 4D (integrado), Zhiyun Crane 4 |
| Monitores | SmallHD Cine 7, Atomos Ninja V+, SmallHD Focus Pro |

### Gestao de Disponibilidade e Reservas

- Calendario visual de disponibilidade por equipamento
- Reserva de equipamento vinculada a projeto e periodo
- Verificacao automatica de conflitos de reserva
- Lista de espera quando equipamento nao esta disponivel
- Notificacao automatica quando equipamento reservado fica disponivel
- Visao consolidada de todos os equipamentos reservados para uma producao especifica

### Checkout e Devolucao

- Registro de retirada: quem retirou, quando, para qual producao, condicao na saida
- Checklist de verificacao na retirada (equipamento completo, acessorios, bateria carregada)
- Registro de devolucao: quando, por quem, condicao no retorno
- Checklist de verificacao na devolucao (danos, itens faltantes, limpeza)
- Fotos comparativas: condicao na saida vs condicao no retorno
- Alerta para devolucoes atrasadas

### Manutencao Preventiva e Corretiva

- Agendamento de manutencoes preventivas periodicas por equipamento
- Registro de historico de manutencoes: tipo, data, custo, fornecedor, descricao
- Alertas automaticos para manutencoes proximas (calibracao de lentes, limpeza de sensor, atualizacao de firmware)
- Controle de garantia: data de inicio, data de expiracao, numero da garantia
- Orcamento de manutencao corretiva com aprovacao pelo financeiro

### Proprio vs Aluguel

- Marcacao de equipamentos como proprio ou alugado de terceiro
- Para equipamentos proprios: calculo de depreciacao, custo de uso por dia/producao
- Para equipamentos alugados: fornecedor, valor de locacao, periodo, contrato
- Comparativo de custo: alugar vs comprar para equipamentos frequentemente alugados
- Relatorio de utilizacao que embasa decisoes de investimento em novos equipamentos

## Fluxo de Dados

```
[Produtor Executivo planeja producao no modulo Gestao de Projetos]
        |
        v
[Consulta disponibilidade de equipamentos no Inventario]
        |
        v
[Reserva equipamentos para o periodo da producao]
        |
        +---> [Equipamento proprio disponivel] ---> [Reserva confirmada]
        |
        +---> [Equipamento nao disponivel] ---> [Opcao: alugar de terceiro]
        |                                              |
        |                                              v
        |                                   [Consulta fornecedores no modulo Fornecedores]
        |                                              |
        |                                              v
        |                                   [Registra aluguel no Inventario]
        |
        v
[Dia da producao: Checkout com checklist]
        |
        v
[Equipamento em uso (status: Em Uso)]
        |
        v
[Fim da producao: Devolucao com checklist]
        |
        v
[Verificacao de condicao]
        |
        +---> [OK] ---> [Status: Disponivel]
        |
        +---> [Dano identificado] ---> [Status: Em Manutencao] ---> [Orcamento e reparo]
        |
        v
[Custo de uso registrado no orcamento do projeto (modulo Financeiro)]
```

## Entidades e Modelo de Dados

### Equipment

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do equipamento |
| marca | String | Marca (Blackmagic, Sony, DJI, etc.) |
| modelo | String | Modelo especifico |
| numero_serie | String | Numero de serie |
| categoria_id | FK(Category) | Categoria do equipamento |
| status | Enum | Disponivel, EmUso, EmManutencao, Reservado, Aposentado, AlugadoTerceiro |
| propriedade | Enum | Proprio, AlugadoPermanente |
| data_aquisicao | Date | Data de compra |
| valor_aquisicao | Decimal | Valor de compra |
| valor_depreciado | Decimal | Valor atual com depreciacao |
| custo_dia | Decimal | Custo de uso diario calculado |
| especificacoes | JSON | Especificacoes tecnicas detalhadas |
| fotos | Array[String] | URLs das fotos do equipamento |
| qr_code | String | Codigo QR para identificacao rapida |
| localizacao_id | FK(Location) | Local de armazenamento atual |
| garantia_ate | Date | Data de expiracao da garantia |
| proxima_manutencao | Date | Data da proxima manutencao preventiva |
| notas | Text | Observacoes gerais |
| ativo | Boolean | Se o equipamento esta ativo no acervo |
| criado_em | DateTime | Data de cadastro |

### Category

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome da categoria |
| descricao | Text | Descricao da categoria |
| icone | String | Icone representativo |
| total_itens | Integer | Contador de itens na categoria |

### Checkout

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| equipamento_id | FK(Equipment) | Equipamento retirado |
| projeto_id | FK(Project) | Producao associada |
| retirado_por | FK(User) | Quem retirou |
| data_retirada | DateTime | Data e hora da retirada |
| data_devolucao_prevista | Date | Data prevista de devolucao |
| data_devolucao_real | DateTime | Data efetiva de devolucao |
| condicao_saida | Enum | Excelente, Bom, Regular, Necessita Atencao |
| condicao_retorno | Enum | Excelente, Bom, Regular, Danificado |
| checklist_saida | JSON | Itens verificados na saida |
| checklist_retorno | JSON | Itens verificados na devolucao |
| fotos_saida | Array[String] | Fotos na retirada |
| fotos_retorno | Array[String] | Fotos na devolucao |
| observacoes | Text | Notas sobre o uso |
| status | Enum | EmUso, Devolvido, Atrasado |

### Maintenance

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| equipamento_id | FK(Equipment) | Equipamento em manutencao |
| tipo | Enum | Preventiva, Corretiva, Atualizacao, Calibracao |
| descricao | Text | Descricao do servico realizado |
| fornecedor_id | FK(Supplier) | Prestador do servico |
| custo | Decimal | Custo da manutencao |
| data_entrada | Date | Data de envio para manutencao |
| data_conclusao | Date | Data de retorno |
| status | Enum | Agendada, EmAndamento, Concluida, Cancelada |
| garantia_servico | Date | Garantia do servico realizado |
| notas | Text | Observacoes tecnicas |

### Location

| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| nome | String | Nome do local (ex: "Estudio A", "Almoxarifado", "Sala de Edicao") |
| endereco | String | Endereco fisico |
| descricao | Text | Descricao do espaco |
| capacidade | Integer | Capacidade de armazenamento |
| responsavel_id | FK(User) | Responsavel pelo local |

## Interface e UX

### Catalogo de Equipamentos

- Grid de cards com foto, nome, marca/modelo, status e categoria de cada equipamento
- Filtros por categoria, status, marca, disponibilidade e localizacao
- Busca textual por nome, modelo ou numero de serie
- Leitura de QR Code para acesso rapido ao equipamento (via camera do dispositivo)
- Toggle entre visualizacao em grid e em lista

### Calendario de Disponibilidade

- Visualizacao tipo Gantt com cada equipamento em uma linha e dias no eixo horizontal
- Blocos coloridos indicando reservas (azul), em uso (verde), manutencao (amarelo), indisponivel (vermelho)
- Filtros por categoria e projeto
- Drag para criar reservas diretamente no calendario
- Tooltip com detalhes da reserva ao passar o mouse

### Tela de Checkout/Devolucao

- Formulario guiado passo a passo para retirada e devolucao
- Leitor de QR Code para selecionar equipamento
- Checklist interativo com checkboxes para cada item de verificacao
- Camera integrada para tirar fotos do equipamento no momento
- Assinatura digital do responsavel pela retirada

### Painel de Manutencao

- Lista de manutencoes agendadas e em andamento
- Calendario de manutencoes preventivas com alertas
- Historico completo de manutencoes por equipamento
- Dashboard com custos de manutencao por periodo e por categoria

### Dashboard de Inventario

- Total de equipamentos por categoria e status
- Taxa de utilizacao por equipamento (dias em uso / dias disponiveis)
- Ranking dos equipamentos mais utilizados
- Custo total de manutencao por periodo
- Alerta de equipamentos com garantia expirando
- Comparativo de custo: proprio vs aluguel para itens frequentes

## Integracoes

| Servico | Finalidade | Tipo de Integracao |
|---------|------------|-------------------|
| Modulo Gestao de Projetos | Reserva de equipamentos vinculada a producoes | Interno (bidirecional) |
| Modulo Financeiro/ERP | Custos de equipamento no orcamento do projeto, depreciacao | Interno (evento) |
| Modulo Fornecedores | Consulta de fornecedores para aluguel e manutencao | Interno (consulta) |
| Google Calendar | Sincronizacao de datas de reserva e manutencao | OAuth 2.0 + API |
| Camera do dispositivo | Leitura de QR Code e fotos de checklist | API nativa do navegador |

## Agentes de IA

### Agente de Recomendacao de Equipamento

- **Modelo**: Claude 3.5 Sonnet
- **Funcao**: Sugerir lista de equipamentos necessarios para um tipo de producao com base no briefing do projeto
- **Comportamento**: Recebe descricao do projeto (tipo, local, duracao, estilo). Sugere kit de equipamentos ideal considerando o acervo disponivel. Indica alternativas quando equipamento ideal nao esta disponivel.

### Agente de Previsao de Manutencao

- **Modelo**: GPT-4o
- **Funcao**: Analisar historico de uso e manutencao para prever necessidades futuras
- **Comportamento**: Monitora frequencia de uso, idade do equipamento e historico de problemas. Sugere manutencoes preventivas antes de falhas. Recomenda aposentadoria ou substituicao quando custo de manutencao supera valor do equipamento.

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|------------|---------------|
| Frontend | React com TypeScript | Interface interativa com calendario e grid |
| Calendario/Gantt | dhtmlxScheduler ou FullCalendar | Visualizacao de disponibilidade |
| QR Code | qrcode.react (geracao), html5-qrcode (leitura) | Identificacao rapida de equipamentos |
| Camera | API MediaDevices do navegador | Fotos de checklist e leitura de QR |
| Backend | Node.js com TypeScript | Consistencia com stack |
| Banco de Dados | PostgreSQL | Queries de disponibilidade e historico |
| Armazenamento de Fotos | AWS S3 | Fotos de equipamentos e checklists |
| Notificacoes | Push notifications + Email | Alertas de devolucao e manutencao |

## Dependencias

- **Modulo Gestao de Projetos**: Vinculacao de reservas a producoes
- **Modulo Financeiro/ERP**: Registro de custos e depreciacao
- **Modulo Fornecedores**: Consulta de fornecedores para aluguel e assistencia tecnica
- **Servico de Armazenamento**: Para fotos de equipamentos e checklists
- **Servico de Autenticacao**: Controle de quem pode retirar equipamentos e aprovar manutencoes

## Metricas de Sucesso

| Metrica | Descricao | Meta |
|---------|-----------|------|
| Taxa de Utilizacao | Media de dias em uso por equipamento | >= 60% |
| Disponibilidade | Equipamentos disponiveis quando solicitados | >= 95% |
| Devolucoes no Prazo | Porcentagem de devolucoes feitas na data prevista | >= 90% |
| Incidentes em Producao | Falhas de equipamento durante producoes | <= 2% |
| Custo de Manutencao | Custo medio anual de manutencao por equipamento | Monitoramento |
| Tempo de Checkout | Tempo medio para completar processo de retirada | <= 10 minutos |
| Acuracia do Inventario | Correspondencia entre sistema e inventario fisico | >= 99% |
| Cobertura de Manutencao Preventiva | Porcentagem de manutencoes preventivas realizadas no prazo | >= 95% |
