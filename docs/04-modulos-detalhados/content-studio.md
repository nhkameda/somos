# Content Studio

## Proposito e Visao

O Content Studio e o nucleo criativo inteligente da Somos Produtora, projetado para automatizar e escalar a producao de conteudo digital para os clientes da agencia 360. Este modulo combina curadoria de noticias impulsionada por IA, geracao de texto, criacao de imagens e montagem de pecas graficas para redes sociais, tudo seguindo os padroes de marca de cada cliente.

O fluxo comeca com agentes de IA que monitoram constantemente foruns, blogs, portais de noticias e sites especializados do setor de cada cliente. O conteudo curado recebe uma pontuacao de relevancia e, a partir dele, o sistema gera automaticamente de 5 a 20 slides de carrossel com ganchos (hooks), dados factuais, chamadas para acao (CTAs) e imagens geradas ou adaptadas seguindo os padroes visuais da marca.

O Content Studio gera pecas otimizadas para Instagram (carrosseis), LinkedIn (artigos e posts), Facebook (posts e stories), alem de PDFs para apresentacoes, propostas comerciais e cronogramas de projeto. A visao e transformar a Somos em uma fabrica de conteudo que opera 24/7 com supervisao humana estrategica.

## Atores Envolvidos

| Ator | Papel | Permissoes |
|------|-------|------------|
| Diretor de Criacao | Define padroes visuais, aprova conteudos estrategicos | Aprovacao final, configuracao de brand guidelines |
| Social Media Manager | Revisa e ajusta conteudos gerados, programa publicacoes | Edicao, aprovacao de posts, agendamento |
| Designer Grafico | Refina templates e imagens geradas pela IA | Edicao de templates, upload de assets |
| Redator | Revisa e refina textos gerados pela IA | Edicao de texto, ajuste de tom de voz |
| Gerente de Conta | Solicita conteudo para clientes especificos | Solicitacao, visualizacao, aprovacao do cliente |
| Agente IA Curador | Monitora fontes de noticias e atribui pontuacao de relevancia | Leitura de fontes externas, escrita de curadoria |
| Agente IA Redator | Gera textos para posts e artigos | Geracao de texto |
| Agente IA Visual | Gera e adapta imagens seguindo brand guidelines | Geracao de imagens |
| Cliente | Aprova ou solicita ajustes no conteudo | Visualizacao, aprovacao externa |

## Funcionalidades Principais

### Curadoria Inteligente de Noticias
- Monitoramento continuo de fontes configuradas por cliente e setor
- Fontes incluem: portais de noticias, blogs especializados, foruns (Reddit, Quora), RSS feeds, newsletters
- Cada noticia curada recebe um score de relevancia de 0 a 100 baseado em:
  - Alinhamento com o setor do cliente
  - Potencial de engajamento (topico trending)
  - Originalidade (nao duplicado de conteudo ja publicado)
  - Atualidade (noticias mais recentes pontuam mais)
- Painel de curadoria com filtros por cliente, setor, score e data
- Opcao de curadoria manual pelo Social Media Manager

### Geracao de Conteudo Textual
- A partir de uma noticia curada, gera automaticamente:
  - 5 a 20 slides de carrossel com texto otimizado para cada slide
  - Hooks (ganchos de atencao) para o primeiro slide
  - Dados factuais e insights nos slides intermediarios
  - CTAs (chamadas para acao) no ultimo slide
  - Legenda do post com hashtags relevantes
- Geracao de artigos longos para LinkedIn (800-1500 palavras)
- Geracao de posts curtos para Facebook e Instagram (feed e stories)
- Ajuste automatico de tom de voz conforme brand guidelines do cliente
- Opcao de regenerar com parametros diferentes (mais formal, mais descontraido, mais tecnico)

### Geracao e Adaptacao de Imagens
- Geracao de imagens via DALL-E 3 ou Flux seguindo paleta de cores e estilo do cliente
- Adaptacao de templates do Figma/Canva com conteudo gerado
- Redimensionamento automatico para diferentes plataformas:
  - Instagram Feed: 1080x1080px, 1080x1350px
  - Instagram Stories: 1080x1920px
  - LinkedIn: 1200x627px
  - Facebook: 1200x630px
  - YouTube Thumbnail: 1280x720px
- Aplicacao automatica de logo, marca d'agua e elementos visuais do cliente
- Banco de imagens stock integrado (Unsplash, Pexels) como alternativa

### Geracao de PDFs
- Apresentacoes comerciais com slides formatados
- Propostas de projeto com cronograma visual
- Relatorios de desempenho de redes sociais
- Cronogramas de producao de conteudo mensal
- Templates personalizados por tipo de documento

### Sistema de Aprovacao
- Workflow de aprovacao em etapas: Geracao IA > Revisao Interna > Aprovacao do Cliente
- Preview interativo de como o post ficara em cada plataforma
- Comentarios em linha para ajustes pontuais
- Versionamento de conteudo com historico de alteracoes
- Aprovacao do cliente via link externo (sem necessidade de login)

## Fluxo de Dados

```
[Fontes Externas]
(Blogs, Foruns, Noticias, RSS)
        |
        v
[Agente Curador de IA]
        |
        v
[Curadoria com Score de Relevancia]
        |
        v
[Selecao de Conteudo] <--- [Social Media Manager (humano)]
        |
        v
+-------+-------+
|               |
v               v
[Agente IA      [Agente IA
 Redator]        Visual]
|               |
v               v
[Textos         [Imagens
 Gerados]        Geradas]
|               |
+-------+-------+
        |
        v
[Montagem da Peca Final]
(Carrossel, Post, Artigo, PDF)
        |
        v
[Revisao Interna]
        |
        v
[Aprovacao do Cliente]
        |
        v
[Social Scheduler] --> [Publicacao]
```

## Entidades e Modelo de Dados

### ContentPiece (Peca de Conteudo)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| client_id | UUID (FK) | Cliente para quem o conteudo e criado |
| brand_id | UUID (FK) | Marca/brand guidelines aplicadas |
| type | Enum | CAROUSEL, POST, ARTICLE, STORY, REEL_SCRIPT, PDF |
| platform | Enum | INSTAGRAM, LINKEDIN, FACEBOOK, YOUTUBE, MULTI |
| title | String | Titulo interno da peca |
| body_text | Text | Texto principal / legenda |
| hashtags | String[] | Lista de hashtags |
| cta_text | String | Texto da chamada para acao |
| source_article_id | UUID (FK) | Noticia de origem (curadoria) |
| status | Enum | RASCUNHO, REVISAO_INTERNA, AGUARDANDO_CLIENTE, APROVADO, PUBLICADO |
| relevance_score | Integer | Score de relevancia herdado da curadoria |
| created_at | DateTime | Data de criacao |
| approved_at | DateTime | Data de aprovacao |

### Template (Template Visual)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| brand_id | UUID (FK) | Marca associada |
| name | String | Nome do template |
| type | Enum | CAROUSEL_SLIDE, FEED_POST, STORY, ARTICLE_HEADER, PDF_PAGE |
| platform | Enum | INSTAGRAM, LINKEDIN, FACEBOOK, UNIVERSAL |
| figma_url | String | Link para o template no Figma |
| canva_url | String | Link para o template no Canva |
| html_template | Text | Template HTML para geracao programatica |
| dimensions | JSON | Largura e altura em pixels |
| placeholders | JSON | Campos editaveis (titulo, subtitulo, imagem, logo) |
| is_active | Boolean | Se o template esta ativo |

### Brand (Marca / Brand Guidelines)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| client_id | UUID (FK) | Cliente proprietario |
| name | String | Nome da marca |
| primary_color | String | Cor primaria (hex) |
| secondary_color | String | Cor secundaria (hex) |
| accent_color | String | Cor de destaque (hex) |
| font_primary | String | Fonte primaria |
| font_secondary | String | Fonte secundaria |
| tone_of_voice | Text | Descricao do tom de voz |
| logo_url | String | URL do logo principal |
| logo_variations | JSON | Variacoes do logo (branco, preto, compacto) |
| visual_style | Text | Descricao do estilo visual |
| prohibited_terms | String[] | Termos proibidos para a marca |

### ImageAsset (Ativo de Imagem)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| content_piece_id | UUID (FK) | Peca de conteudo associada |
| type | Enum | AI_GENERATED, STOCK, UPLOADED, TEMPLATE_RENDER |
| url | String | URL do arquivo |
| width | Integer | Largura em pixels |
| height | Integer | Altura em pixels |
| alt_text | String | Texto alternativo para acessibilidade |
| generation_prompt | Text | Prompt usado para gerar a imagem (se IA) |
| model_used | String | Modelo de IA usado (DALL-E 3, Flux) |

### Article (Artigo Curado)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| source_url | String | URL original da noticia |
| source_name | String | Nome da fonte |
| title | String | Titulo da noticia |
| summary | Text | Resumo gerado pela IA |
| full_text | Text | Texto completo extraido |
| sector | String | Setor do conteudo |
| relevance_score | Integer | Score de relevancia (0-100) |
| curated_at | DateTime | Data da curadoria |
| used_in_content | Boolean | Se ja foi usado para gerar conteudo |

### Carousel (Carrossel)
| Campo | Tipo | Descricao |
|-------|------|-----------|
| id | UUID | Identificador unico |
| content_piece_id | UUID (FK) | Peca de conteudo pai |
| slides | JSON[] | Array de slides com texto, imagem e ordem |
| slide_count | Integer | Numero total de slides |
| hook_slide | Text | Texto do slide de gancho (primeiro) |
| cta_slide | Text | Texto do slide CTA (ultimo) |

## Interface e UX

### Painel de Curadoria
- Feed de noticias curadas com cards mostrando titulo, fonte, score e resumo
- Filtros por cliente, setor, score minimo e periodo
- Acao rapida: "Gerar Conteudo a partir disto" em cada card
- Indicador visual de score (verde > 70, amarelo 40-70, vermelho < 40)

### Editor de Conteudo
- Editor visual WYSIWYG para textos
- Preview lado a lado: editor a esquerda, preview da plataforma a direita
- Seletor de template visual com thumbnails
- Botao "Regenerar com IA" para solicitar nova versao do texto ou imagem
- Painel de comentarios para revisao interna

### Galeria de Templates
- Grid de templates organizados por marca e tipo
- Filtros por plataforma e formato
- Preview interativo com dados de exemplo
- Opcao de criar novo template a partir de existente

### Calendario de Conteudo
- Visao mensal e semanal de conteudos por cliente
- Status visual de cada conteudo (rascunho, em revisao, aprovado, publicado)
- Drag-and-drop para reorganizar datas de publicacao

## Integracoes

| Modulo/Sistema | Tipo de Integracao | Descricao |
|----------------|-------------------|-----------|
| Social Scheduler | Escrita | Envia conteudo aprovado para agendamento e publicacao |
| CRM | Leitura | Consulta dados do cliente e historico de conteudo |
| Figma API | Leitura | Importa templates e componentes visuais |
| Canva API | Leitura/Escrita | Importa e exporta designs |
| Unsplash/Pexels API | Leitura | Busca imagens stock gratuitas |
| OpenAI API (DALL-E 3) | Escrita | Gera imagens a partir de prompts |
| Flux API | Escrita | Gera imagens alternativas com outro modelo |
| RSS Feeds | Leitura | Fontes de noticias para curadoria |
| Analytics/Reports | Leitura | Metricas de desempenho de conteudos publicados |

## Agentes de IA

### Agente Curador de Noticias
- **Funcao**: Monitora fontes de noticias continuamente (a cada 30 minutos) e atribui scores de relevancia
- **Modelo**: Gemini Pro para processamento de grandes volumes de texto + Claude para analise de relevancia
- **Fontes Monitoradas**: RSS feeds, Google News, blogs especializados, foruns Reddit/Quora
- **Saida**: Noticias curadas com score, resumo e tags de setor
- **Frequencia**: A cada 30 minutos para fontes prioritarias, a cada 2 horas para demais

### Agente Redator de Conteudo
- **Funcao**: Gera textos para posts, carrosseis e artigos
- **Modelo**: Claude (Opus para artigos longos, Sonnet para posts curtos)
- **Entrada**: Noticia curada + brand guidelines do cliente + tipo de conteudo desejado
- **Saida**: Texto formatado por slide (carrossel) ou texto unico (post/artigo)
- **Personalizacao**: Tom de voz, nivel de formalidade, uso de emojis, estilo narrativo

### Agente Visual
- **Funcao**: Gera imagens e monta pecas graficas
- **Modelo**: DALL-E 3 para geracao de imagens, Flux como alternativa
- **Entrada**: Descricao do conteudo + paleta de cores + estilo visual da marca
- **Saida**: Imagens geradas em resolucoes adequadas para cada plataforma
- **Restricoes**: Segue rigorosamente a paleta de cores e estilo definido no Brand

### Agente Otimizador de Hashtags
- **Funcao**: Seleciona hashtags otimizadas para alcance e relevancia
- **Modelo**: Claude (analise de tendencias e relevancia)
- **Entrada**: Texto do post + setor do cliente + plataforma
- **Saida**: Lista de 15-30 hashtags ordenadas por relevancia

## Tecnologias Recomendadas

| Componente | Tecnologia | Justificativa |
|------------|-----------|---------------|
| Geracao de Texto | Claude API (Anthropic) | Qualidade superior em portugues, tom de voz consistente |
| Geracao de Imagens | OpenAI DALL-E 3 API | Alta qualidade, controle de estilo |
| Geracao de Imagens (Alt) | Flux API | Alternativa open-source, menor custo |
| Scraping de Noticias | Puppeteer + Cheerio | Extracao de conteudo de paginas web |
| RSS Parsing | rss-parser (Node.js) | Leitura de feeds RSS/Atom |
| Geracao de PDF | React-PDF / Puppeteer | PDFs profissionais a partir de templates React |
| Processamento de Imagem | Sharp (Node.js) | Redimensionamento e otimizacao de imagens |
| Editor de Texto | TipTap (React) | Editor WYSIWYG extensivel e moderno |
| Armazenamento | Supabase Storage / AWS S3 | Imagens e PDFs com CDN |
| Fila de Processamento | BullMQ + Redis | Geracao assincrona de conteudo (imagens demoram) |

## Dependencias

### Dependencias de Modulo
- **Social Scheduler**: Recomendada - publicacao automatica de conteudo aprovado
- **CRM**: Recomendada - dados do cliente e historico
- **Analytics/Reports**: Recomendada - metricas para otimizar conteudo futuro
- **Brand Guidelines (interno)**: Obrigatoria - sem brand guidelines nao ha padrao visual

### Dependencias Tecnicas
- APIs de IA configuradas (Claude, DALL-E 3 / Flux)
- Templates visuais cadastrados por marca/cliente
- Brand guidelines de cada cliente documentadas no sistema
- Fontes de noticias configuradas por setor/cliente
- Fila de processamento (Redis + BullMQ) para geracao assincrona

## Metricas de Sucesso

| Metrica | Meta | Forma de Medicao |
|---------|------|-------------------|
| Conteudos gerados por mes | > 200 por cliente ativo | Contagem mensal |
| Taxa de aprovacao na primeira revisao | > 60% | Aprovados na v1 / total gerado |
| Tempo medio de geracao (texto + imagem) | < 5 minutos | Timestamp inicio vs conclusao |
| Score medio de relevancia da curadoria | > 65 | Media dos scores dos ultimos 30 dias |
| Engajamento medio dos posts publicados | Crescimento de 15% m/m | Dados das APIs das redes sociais |
| Reducao de tempo do Social Media Manager | > 50% | Comparativo com processo manual anterior |
| Precisao do tom de voz | > 85% satisfacao | Avaliacao do redator humano |
| Volume de artigos curados por dia | > 50 por setor | Contagem diaria |
