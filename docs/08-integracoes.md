# 08 - Integracoes

> Somos Produtora - Mapa Completo de Integracoes Externas
> Versao: 1.0 | Ultima atualizacao: 2026-02-28

---

## Indice

1. [Visao Geral](#visao-geral)
2. [Redes Sociais](#redes-sociais)
3. [Mensageria](#mensageria)
4. [Modelos de IA](#modelos-de-ia)
5. [Busca e Pesquisa](#busca-e-pesquisa)
6. [Fiscal e Tributario](#fiscal-e-tributario)
7. [Financeiro e Pagamentos](#financeiro-e-pagamentos)
8. [Design e Midia](#design-e-midia)
9. [Armazenamento](#armazenamento)
10. [Estrategia de Resiliencia](#estrategia-de-resiliencia)
11. [Resumo de Custos](#resumo-de-custos)

---

## 1. Visao Geral

O sistema da Somos Produtora se integra com **mais de 20 servicos externos** para automatizar prospeccao, comunicacao, geracao de conteudo, emissao fiscal e operacoes financeiras. Cada integracao segue padroes de resiliencia: retry com backoff exponencial, fallback para servicos alternativos e monitoramento de disponibilidade.

### Diagrama de Integracoes

```
                                ┌──────────────────┐
                                │   SOMOS BACKEND  │
                                │     (FastAPI)    │
                                └────────┬─────────┘
                                         │
          ┌──────────────────────────────┼──────────────────────────────┐
          │              │               │               │              │
    ┌─────▼─────┐  ┌────▼────┐  ┌───────▼──────┐ ┌─────▼─────┐ ┌─────▼─────┐
    │   REDES   │  │MENSAGEM │  │  MODELOS IA  │ │  BUSCA    │ │ FINANCEIRO│
    │  SOCIAIS  │  │         │  │              │ │           │ │           │
    ├───────────┤  ├─────────┤  ├──────────────┤ ├───────────┤ ├───────────┤
    │ LinkedIn  │  │WhatsApp │  │ Anthropic    │ │ SerpAPI   │ │ Asaas     │
    │ Instagram │  │ (Evol.) │  │ OpenAI       │ │ Google    │ │ PagSeguro │
    │ Facebook  │  │ Resend  │  │ Google AI    │ │ Custom S. │ │ Open Bank │
    │ YouTube   │  │ (Email) │  │ Replicate    │ │           │ │ NF-e      │
    └───────────┘  └─────────┘  └──────────────┘ └───────────┘ └───────────┘
          │              │               │               │              │
    ┌─────▼─────┐  ┌────▼────┐  ┌───────▼──────┐ ┌─────▼─────┐ ┌─────▼─────┐
    │  DESIGN   │  │STORAGE  │  │ MONITORAMENTO│ │ CALENDAR  │ │  WEBHOOK  │
    ├───────────┤  ├─────────┤  ├──────────────┤ ├───────────┤ ├───────────┤
    │ Canva API │  │ MinIO   │  │ Sentry       │ │ Google    │ │ Incoming  │
    │           │  │ Cloudf.R2│ │ Grafana      │ │ Calendar  │ │ Webhooks  │
    └───────────┘  └─────────┘  └──────────────┘ └───────────┘ └───────────┘
```

### Padrao de Integracao

Todas as integracoes seguem o mesmo padrao arquitetural:

```python
# backend/app/integrations/base.py

from abc import ABC, abstractmethod
from tenacity import retry, stop_after_attempt, wait_exponential
import httpx
import structlog

logger = structlog.get_logger()

class BaseIntegration(ABC):
    """Classe base para todas as integracoes externas."""

    def __init__(self, api_key: str, base_url: str, timeout: int = 30):
        self.client = httpx.AsyncClient(
            base_url=base_url,
            timeout=timeout,
            headers=self._get_headers(api_key)
        )

    @abstractmethod
    def _get_headers(self, api_key: str) -> dict:
        pass

    @retry(
        stop=stop_after_attempt(3),
        wait=wait_exponential(multiplier=1, min=2, max=30)
    )
    async def _request(self, method: str, endpoint: str, **kwargs):
        try:
            response = await self.client.request(method, endpoint, **kwargs)
            response.raise_for_status()
            return response.json()
        except httpx.HTTPStatusError as e:
            logger.error("integration_error",
                         service=self.__class__.__name__,
                         status=e.response.status_code,
                         detail=e.response.text)
            raise
        except httpx.TimeoutException:
            logger.error("integration_timeout",
                         service=self.__class__.__name__)
            raise
```

---

## 2. Redes Sociais

### 2.1 LinkedIn API

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Busca de leads, envio de convites/mensagens, publicacao de conteudo no perfil da empresa |
| **Autenticacao**      | OAuth 2.0 (3-legged flow)                                  |
| **Scopes**            | `r_liteprofile`, `r_emailaddress`, `w_member_social`, `r_organization_social`, `rw_organization_admin` |
| **Base URL**          | `https://api.linkedin.com/v2/`                             |
| **Rate Limits**       | 100 requests/dia (Standard), 1000/dia (Marketing Developer Platform) |
| **Fallback**          | SerpAPI para busca de perfis (scraping legal via Google)   |
| **Custo**             | Gratuito (API basica) / LinkedIn Sales Navigator ($99/mes para funcoes avancadas) |
| **Dados Coletados**   | Nome, cargo, empresa, localizacao, setor, headline, URL do perfil |

**Endpoints Utilizados**:

| Endpoint                           | Metodo | Descricao                              |
|------------------------------------|--------|----------------------------------------|
| `/me`                              | GET    | Dados do perfil autenticado            |
| `/organizationalEntityShareStatistics` | GET | Metricas de publicacoes da empresa     |
| `/ugcPosts`                        | POST   | Publicar conteudo no perfil da empresa |
| `/connections`                     | GET    | Listar conexoes                        |
| `/messaging/conversations`         | GET    | Listar conversas                       |
| `/messaging/messages`              | POST   | Enviar mensagem                        |

**Implementacao**:

```python
# backend/app/integrations/linkedin.py

class LinkedInIntegration(BaseIntegration):
    def __init__(self, access_token: str):
        super().__init__(
            api_key=access_token,
            base_url="https://api.linkedin.com/v2"
        )

    def _get_headers(self, api_key: str) -> dict:
        return {
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json",
            "X-Restli-Protocol-Version": "2.0.0"
        }

    async def search_people(self, keywords: str, location: str = None):
        params = {"q": "people", "keywords": keywords}
        if location:
            params["facet.location"] = location
        return await self._request("GET", "/search/people", params=params)

    async def send_connection_request(self, profile_urn: str, message: str):
        payload = {
            "invitee": profile_urn,
            "message": message[:300]  # Limite de 300 caracteres
        }
        return await self._request("POST", "/invitations", json=payload)

    async def publish_post(self, org_id: str, text: str, image_url: str = None):
        payload = {
            "author": f"urn:li:organization:{org_id}",
            "lifecycleState": "PUBLISHED",
            "specificContent": {
                "com.linkedin.ugc.ShareContent": {
                    "shareCommentary": {"text": text},
                    "shareMediaCategory": "NONE"
                }
            },
            "visibility": {"com.linkedin.ugc.MemberNetworkVisibility": "PUBLIC"}
        }
        return await self._request("POST", "/ugcPosts", json=payload)
```

---

### 2.2 Instagram Graph API

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Publicacao de conteudo, monitoramento de metricas, busca de perfis publicos |
| **Autenticacao**      | OAuth 2.0 via Facebook Login                               |
| **Scopes**            | `instagram_basic`, `instagram_content_publish`, `instagram_manage_insights`, `pages_show_list` |
| **Base URL**          | `https://graph.instagram.com/v19.0/`                       |
| **Rate Limits**       | 200 requests/hora por usuario, 25 publicacoes/dia          |
| **Fallback**          | Agendamento manual via buffer interno                      |
| **Custo**             | Gratuito                                                   |
| **Dados Coletados**   | Metricas de posts (alcance, impressoes, engajamento), dados de seguidores |

**Endpoints Utilizados**:

| Endpoint                               | Metodo | Descricao                           |
|----------------------------------------|--------|-------------------------------------|
| `/{ig-user-id}/media`                  | POST   | Criar container de midia            |
| `/{ig-user-id}/media_publish`          | POST   | Publicar midia                      |
| `/{ig-user-id}/insights`              | GET    | Metricas do perfil                  |
| `/{ig-media-id}/insights`             | GET    | Metricas de post especifico         |
| `/{ig-user-id}/stories`               | GET    | Listar stories                      |
| `/ig_hashtag_search`                   | GET    | Buscar hashtag                      |

---

### 2.3 Facebook Graph API

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Gerenciamento de pagina da empresa, publicacao de conteudo, analise de metricas |
| **Autenticacao**      | OAuth 2.0 (Facebook Login)                                 |
| **Scopes**            | `pages_manage_posts`, `pages_read_engagement`, `pages_show_list`, `pages_manage_metadata` |
| **Base URL**          | `https://graph.facebook.com/v19.0/`                        |
| **Rate Limits**       | 200 requests/hora por pagina                               |
| **Fallback**          | Agendamento manual                                         |
| **Custo**             | Gratuito                                                   |

---

### 2.4 YouTube Data API v3

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Upload de videos, gerenciamento de canal, analise de metricas, busca de canais-alvo |
| **Autenticacao**      | API Key (leitura) / OAuth 2.0 (escrita)                    |
| **Base URL**          | `https://www.googleapis.com/youtube/v3/`                   |
| **Rate Limits**       | 10.000 unidades/dia (cota padrao)                          |
| **Fallback**          | Upload manual via interface                                |
| **Custo**             | Gratuito (dentro da cota)                                  |
| **Dados Coletados**   | Visualizacoes, inscritos, engajamento, dados demograficos  |

**Endpoints Utilizados**:

| Endpoint              | Metodo | Custo (unidades) | Descricao                      |
|-----------------------|--------|-------------------|--------------------------------|
| `/search`             | GET    | 100               | Buscar canais/videos           |
| `/channels`           | GET    | 1-3               | Dados do canal                 |
| `/videos`             | GET    | 1-3               | Dados do video                 |
| `/videos/insert`      | POST   | 1600              | Upload de video                |
| `/analytics/reports`  | GET    | Variavel          | Metricas detalhadas            |

---

## 3. Mensageria

### 3.1 WhatsApp Business API (via Evolution API)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Envio e recebimento de mensagens WhatsApp para prospeccao, atendimento e notificacoes |
| **Solucao**           | Evolution API (self-hosted, open-source)                   |
| **Autenticacao**      | API Key interna (gerada na instalacao)                     |
| **Base URL**          | `http://evolution-api:8080/` (container Docker)            |
| **Rate Limits**       | Depende da politica do WhatsApp (1000 msgs/dia para contas novas, escala com qualidade) |
| **Fallback**          | SMS via Twilio ($0.05/msg) ou notificacao por email        |
| **Custo**             | **Gratuito** (self-hosted). Apenas custo do servidor       |

**Por que Evolution API?**

- Open-source e gratuito (vs WhatsApp Cloud API com custos por conversa)
- Self-hosted (dados ficam no nosso servidor)
- Suporta multiplas instancias/numeros
- API REST simples e bem documentada
- Webhooks para mensagens recebidas
- Suporta envio de midia (imagens, videos, documentos)

**Implementacao**:

```python
# backend/app/integrations/whatsapp.py

class WhatsAppIntegration(BaseIntegration):
    def __init__(self, api_key: str, instance_name: str = "somos-principal"):
        super().__init__(
            api_key=api_key,
            base_url="http://evolution-api:8080"
        )
        self.instance = instance_name

    def _get_headers(self, api_key: str) -> dict:
        return {
            "apikey": api_key,
            "Content-Type": "application/json"
        }

    async def send_text_message(self, phone: str, message: str):
        """Envia mensagem de texto via WhatsApp."""
        payload = {
            "number": self._format_phone(phone),
            "text": message
        }
        return await self._request(
            "POST",
            f"/message/sendText/{self.instance}",
            json=payload
        )

    async def send_media_message(self, phone: str, media_url: str, caption: str):
        """Envia mensagem com midia (imagem/video/documento)."""
        payload = {
            "number": self._format_phone(phone),
            "mediaUrl": media_url,
            "caption": caption
        }
        return await self._request(
            "POST",
            f"/message/sendMedia/{self.instance}",
            json=payload
        )

    async def send_template_message(self, phone: str, template_name: str, params: dict):
        """Envia mensagem baseada em template aprovado."""
        payload = {
            "number": self._format_phone(phone),
            "template": template_name,
            "params": params
        }
        return await self._request(
            "POST",
            f"/message/sendTemplate/{self.instance}",
            json=payload
        )

    @staticmethod
    def _format_phone(phone: str) -> str:
        """Formata telefone para padrao internacional (5511999999999)."""
        digits = ''.join(filter(str.isdigit, phone))
        if not digits.startswith('55'):
            digits = '55' + digits
        return digits
```

**Webhook para Recebimento de Mensagens**:

```python
# backend/app/api/v1/webhooks.py

from fastapi import APIRouter, Request

router = APIRouter()

@router.post("/webhook/whatsapp")
async def whatsapp_webhook(request: Request):
    """Recebe notificacoes de mensagens do WhatsApp via Evolution API."""
    payload = await request.json()

    event_type = payload.get("event")
    if event_type == "messages.upsert":
        message = payload["data"]
        sender = message["key"]["remoteJid"]
        text = message.get("message", {}).get("conversation", "")

        # Processar mensagem recebida
        await process_incoming_whatsapp(sender, text, payload)

    return {"status": "ok"}
```

---

### 3.2 Email (Resend)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Envio de cold emails, newsletters, notificacoes do sistema e emails transacionais |
| **Autenticacao**      | API Key                                                    |
| **Base URL**          | `https://api.resend.com/`                                  |
| **Rate Limits**       | 100 emails/dia (free), 50.000/mes (Pro $20)                |
| **Fallback**          | Amazon SES como backup ($0.10/1000 emails)                 |
| **Custo**             | **$20/mes** (plano Pro: 50K emails/mes)                    |

**Implementacao**:

```python
# backend/app/integrations/email.py

import resend

class EmailIntegration:
    def __init__(self, api_key: str):
        resend.api_key = api_key

    async def send_email(
        self,
        to: str | list[str],
        subject: str,
        html: str,
        from_email: str = "contato@somosprodutora.com.br",
        reply_to: str = None,
        tags: list[dict] = None
    ):
        params = {
            "from": from_email,
            "to": to if isinstance(to, list) else [to],
            "subject": subject,
            "html": html,
        }
        if reply_to:
            params["reply_to"] = reply_to
        if tags:
            params["tags"] = tags

        return resend.Emails.send(params)

    async def send_batch(self, emails: list[dict]):
        """Envia multiplos emails em lote."""
        return resend.Batch.send(emails)
```

**Funcionalidades do Resend**:
- Tracking de abertura e cliques
- Dominio customizado (SPF, DKIM, DMARC configurados)
- Templates com React Email
- Webhooks para eventos (delivered, opened, clicked, bounced)
- Supressao automatica de bounces

---

## 4. Modelos de IA

### 4.1 Anthropic API (Claude)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Raciocinio complexo, redacao de conteudo, analise de contratos, conversacao com leads |
| **Autenticacao**      | API Key (`x-api-key` header)                               |
| **Base URL**          | `https://api.anthropic.com/v1/`                            |
| **Modelo Principal**  | Claude 3.5 Sonnet (`claude-3-5-sonnet-20241022`)          |
| **Rate Limits**       | 4000 RPM, 400K tokens/min (Tier 2)                        |
| **Fallback**          | OpenAI GPT-4o                                              |
| **Custo Estimado**    | **$200-500/mes** dependendo do volume                      |

**Precos**:
| Modelo              | Input/1M tokens | Output/1M tokens |
|---------------------|-----------------|------------------|
| Claude 3.5 Sonnet   | $3.00           | $15.00           |
| Claude 3.5 Haiku    | $0.25           | $1.25            |

**Uso no Sistema**:
- SDR WhatsApp: mensagens personalizadas (requer nuance e tom correto)
- SDR LinkedIn: mensagens profissionais
- SDR Email: cold emails persuasivos
- Content Writer: conteudo longo e de qualidade
- Contract Drafter: linguagem juridica precisa

---

### 4.2 OpenAI API

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Tarefas gerais, classificacao, scoring de leads, geracao de imagens |
| **Autenticacao**      | API Key (`Authorization: Bearer`)                          |
| **Base URL**          | `https://api.openai.com/v1/`                               |
| **Modelos**           | GPT-4o, GPT-4o-mini, DALL-E 3, text-embedding-3-small     |
| **Rate Limits**       | 10.000 RPM (Tier 3)                                       |
| **Fallback**          | Anthropic Claude / Google Gemini                           |
| **Custo Estimado**    | **$100-300/mes**                                           |

**Precos**:
| Modelo                  | Input/1M tokens | Output/1M tokens |
|-------------------------|-----------------|------------------|
| GPT-4o                  | $5.00           | $15.00           |
| GPT-4o-mini             | $0.15           | $0.60            |
| text-embedding-3-small  | $0.02           | N/A              |
| DALL-E 3 (1024x1024)    | $0.04/imagem    | N/A              |
| DALL-E 3 (1024x1792)    | $0.08/imagem    | N/A              |

**Uso no Sistema**:
- Hunter LinkedIn: busca e analise de perfis
- Qualificador: scoring estruturado de leads
- Budget Calculator: calculos e formatacao
- Performance Evaluator: analise de metricas
- Report Generator: geracao de relatorios
- Image Creator: geracao de imagens com DALL-E 3
- Embeddings: todas as operacoes de busca semantica

---

### 4.3 Google AI (Gemini)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Analise de dados com contexto longo, tendencias de mercado, fallback barato |
| **Autenticacao**      | API Key                                                    |
| **Base URL**          | `https://generativelanguage.googleapis.com/v1beta/`        |
| **Modelos**           | Gemini Pro, Gemini Flash                                   |
| **Rate Limits**       | 60 RPM (free), 1000 RPM (paid)                             |
| **Fallback**          | OpenAI GPT-4o                                              |
| **Custo Estimado**    | **$50-100/mes**                                            |

**Precos**:
| Modelo          | Input/1M tokens | Output/1M tokens | Contexto |
|-----------------|-----------------|------------------|----------|
| Gemini Pro      | $1.25           | $5.00            | 1M       |
| Gemini Flash    | $0.075          | $0.30            | 1M       |

**Uso no Sistema**:
- Trend Analyzer: analise de grandes volumes de dados (janela de contexto de 1M tokens)
- News Curator fallback: processamento barato de artigos
- Hunter Google/Social fallback: tarefas simples de extracao

---

### 4.4 Replicate (Flux para Imagens)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Geracao de imagens alternativa ao DALL-E 3, especialmente para estilos artisticos |
| **Autenticacao**      | API Token (`Authorization: Bearer`)                        |
| **Base URL**          | `https://api.replicate.com/v1/`                            |
| **Modelo**            | Flux Schnell / Flux Pro (Black Forest Labs)                |
| **Rate Limits**       | Sem limite fixo (baseado em capacidade)                    |
| **Fallback**          | DALL-E 3                                                   |
| **Custo Estimado**    | **$30-60/mes** (~$0.003-0.05 por imagem)                   |

**Uso no Sistema**:
- Image Creator: alternativa/complemento ao DALL-E 3
- Estilos que DALL-E nao atende bem (fotorrealismo extremo, estilos artisticos especificos)

---

## 5. Busca e Pesquisa

### 5.1 SerpAPI

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Buscar resultados do Google programaticamente (leads, empresas, noticias) |
| **Autenticacao**      | API Key (parametro `api_key`)                              |
| **Base URL**          | `https://serpapi.com/search`                               |
| **Rate Limits**       | 100 buscas/mes (free), 5000/mes ($50), 15000/mes ($130)   |
| **Fallback**          | Google Custom Search API (gratuito ate 100 queries/dia)    |
| **Custo Estimado**    | **$50/mes** (plano Developer: 5000 buscas)                 |

**Tipos de Busca Utilizados**:

| Tipo                  | Engine                | Uso                                    |
|-----------------------|-----------------------|----------------------------------------|
| Google Search         | `google`              | Busca geral de empresas e leads        |
| Google Maps           | `google_maps`         | Busca local de empresas por regiao     |
| Google News           | `google_news`         | Monitoramento de noticias do setor     |
| LinkedIn Profiles     | `google` + site:      | Busca de perfis LinkedIn via Google    |

**Implementacao**:

```python
# backend/app/integrations/serpapi.py

class SerpAPIIntegration(BaseIntegration):
    def __init__(self, api_key: str):
        self.api_key = api_key
        super().__init__(
            api_key=api_key,
            base_url="https://serpapi.com"
        )

    def _get_headers(self, api_key: str) -> dict:
        return {"Content-Type": "application/json"}

    async def search_google(self, query: str, location: str = "Brazil", num: int = 20):
        params = {
            "api_key": self.api_key,
            "engine": "google",
            "q": query,
            "location": location,
            "num": num,
            "hl": "pt",
            "gl": "br"
        }
        return await self._request("GET", "/search", params=params)

    async def search_maps(self, query: str, location: str):
        params = {
            "api_key": self.api_key,
            "engine": "google_maps",
            "q": query,
            "ll": location,  # latitude,longitude
            "type": "search"
        }
        return await self._request("GET", "/search", params=params)

    async def search_news(self, query: str):
        params = {
            "api_key": self.api_key,
            "engine": "google_news",
            "q": query,
            "gl": "br",
            "hl": "pt"
        }
        return await self._request("GET", "/search", params=params)
```

---

### 5.2 Google Custom Search API

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Busca customizada em dominios especificos (backup do SerpAPI) |
| **Autenticacao**      | API Key                                                    |
| **Base URL**          | `https://www.googleapis.com/customsearch/v1`               |
| **Rate Limits**       | 100 queries/dia (gratuito), 10.000/dia ($5/1000 queries)  |
| **Custo**             | **Gratuito** (ate 100/dia) ou $5/1000 queries adicionais   |

---

## 6. Fiscal e Tributario

### 6.1 Emissao de NF-e (Nota Fiscal Eletronica de Servico)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Emitir Nota Fiscal de Servico eletronica automaticamente apos confirmacao de pagamento |
| **Solucao**           | API de emissao NFS-e via provedor (eNotas, Nuvem Fiscal ou Focus NFe) |
| **Autenticacao**      | API Key + CNPJ                                             |
| **Fallback**          | Emissao manual via portal da prefeitura                    |
| **Custo Estimado**    | **R$ 50-150/mes** (dependendo do volume e provedor)        |

**Provedores Avaliados**:

| Provedor       | Custo Mensal    | NFs incluidas | Municipios Suportados | API Quality |
|----------------|-----------------|---------------|----------------------|-------------|
| eNotas         | R$ 49-199       | 50-500        | 5000+                | Boa         |
| Nuvem Fiscal   | R$ 97-297       | 100-1000      | 5600+                | Excelente   |
| Focus NFe      | R$ 49-199       | 50-500        | 5000+                | Boa         |

**Dados Necessarios para Emissao**:
- CNPJ do prestador (Somos Produtora)
- CNPJ/CPF do tomador (cliente)
- Descricao do servico (codigo CNAE/LC 116)
- Valor do servico
- Impostos (ISS, PIS, COFINS, CSLL, IR)
- Municipio de prestacao

### 6.2 Integracao SEFAZ

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Consulta de situacao fiscal de clientes (CNPJ ativo, situacao cadastral) |
| **Autenticacao**      | Certificado Digital A1 (e-CNPJ)                            |
| **Uso**               | Validacao de CNPJ antes de emissao de NF                   |
| **Fallback**          | Consulta manual no site da Receita Federal                 |
| **Custo**             | **Gratuito** (exceto custo do certificado digital ~R$150/ano) |

---

## 7. Financeiro e Pagamentos

### 7.1 Asaas (Gateway de Pagamentos)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Geracao de boletos, cobarcas via PIX, cartao de credito, gestao de recebimentos |
| **Autenticacao**      | API Key (`access_token` header)                            |
| **Base URL**          | `https://api.asaas.com/v3/` (producao)                    |
| **Rate Limits**       | 100 requests/minuto                                        |
| **Fallback**          | PagSeguro como gateway alternativo                         |
| **Custo**             | Boleto: R$ 1,99/un | PIX: 0,99% | Cartao: 2,99%           |

**Funcionalidades Utilizadas**:

| Funcionalidade        | Endpoint                    | Descricao                          |
|-----------------------|-----------------------------|------------------------------------|
| Criar cobranca        | `POST /payments`            | Boleto, PIX ou cartao              |
| Cobranca recorrente   | `POST /subscriptions`       | Assinaturas mensais                |
| Estornar pagamento    | `POST /payments/{id}/refund`| Estorno parcial ou total           |
| Webhook de status     | Configuravel                | Notificacao de pagamento/vencimento|
| Link de pagamento     | `POST /paymentLinks`        | Link para cliente pagar            |
| Transferencia         | `POST /transfers`           | Transferir para conta bancaria     |

**Implementacao**:

```python
# backend/app/integrations/asaas.py

class AsaasIntegration(BaseIntegration):
    def __init__(self, api_key: str, sandbox: bool = False):
        base_url = "https://sandbox.asaas.com/api/v3" if sandbox \
                   else "https://api.asaas.com/v3"
        super().__init__(api_key=api_key, base_url=base_url)

    def _get_headers(self, api_key: str) -> dict:
        return {
            "access_token": api_key,
            "Content-Type": "application/json"
        }

    async def create_payment(
        self,
        customer_id: str,
        value: float,
        due_date: str,
        billing_type: str = "BOLETO",
        description: str = ""
    ):
        payload = {
            "customer": customer_id,
            "billingType": billing_type,  # BOLETO, PIX, CREDIT_CARD
            "value": value,
            "dueDate": due_date,
            "description": description
        }
        return await self._request("POST", "/payments", json=payload)

    async def create_customer(self, name: str, cpf_cnpj: str, email: str):
        payload = {
            "name": name,
            "cpfCnpj": cpf_cnpj,
            "email": email
        }
        return await self._request("POST", "/customers", json=payload)

    async def get_payment_status(self, payment_id: str):
        return await self._request("GET", f"/payments/{payment_id}")

    async def create_pix_qrcode(self, payment_id: str):
        return await self._request("GET", f"/payments/{payment_id}/pixQrCode")
```

---

### 7.2 PagSeguro (Alternativa)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Gateway alternativo para redundancia de pagamentos         |
| **Autenticacao**      | OAuth 2.0                                                  |
| **Rate Limits**       | 300 requests/minuto                                        |
| **Custo**             | Boleto: R$ 2,99/un | PIX: Gratuito | Cartao: 3,99%        |
| **Uso**               | Ativado apenas se Asaas estiver indisponivel               |

### 7.3 Open Banking APIs

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Reconciliacao bancaria automatica, consulta de saldos e extratos |
| **Autenticacao**      | OAuth 2.0 (consentimento do usuario)                       |
| **Regulacao**         | Banco Central do Brasil (Open Finance Brasil)              |
| **Uso**               | Importar transacoes bancarias para conciliacao automatica   |
| **Custo**             | **Gratuito** (regulado pelo Banco Central)                 |

---

## 8. Design e Midia

### 8.1 Canva API (Connect API)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Renderizar templates de design com dados dinamicos (posts, apresentacoes, thumbnails) |
| **Autenticacao**      | OAuth 2.0                                                  |
| **Base URL**          | `https://api.canva.com/rest/v1/`                           |
| **Rate Limits**       | Variavel conforme plano                                    |
| **Fallback**          | Templates HTML/CSS renderizados com Playwright             |
| **Custo**             | Canva Pro: **R$ 35/mes** por usuario                       |

**Funcionalidades Utilizadas**:
- Autofill de templates com dados do CRM (nome do cliente, projeto, etc.)
- Export de designs em PNG/PDF
- Acesso a biblioteca de templates da marca

---

## 9. Armazenamento

### 9.1 MinIO (Armazenamento Primario)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Armazenamento de todos os arquivos de midia, documentos, exports |
| **Tipo**              | Self-hosted, S3-compatible object storage                  |
| **Autenticacao**      | Access Key + Secret Key (compativel S3)                    |
| **Base URL**          | `http://minio:9000` (interno)                              |
| **Custo**             | **Gratuito** (self-hosted, custo apenas de disco)          |

**Buckets**:

| Bucket               | Uso                                            | Politica de Acesso       |
|-----------------------|------------------------------------------------|--------------------------|
| `media-uploads`       | Uploads de usuarios (fotos, videos, PDFs)      | Privado, pre-signed URLs |
| `generated-content`   | Conteudo gerado por agentes IA                 | Privado, pre-signed URLs |
| `contracts`           | Contratos e documentos juridicos               | Privado, acesso restrito |
| `invoices`            | Notas fiscais e boletos                        | Privado, acesso restrito |
| `backups`             | Backups de banco de dados                      | Privado, somente admin   |
| `exports`             | Relatorios e exports CSV/PDF                   | Privado, TTL 7 dias      |
| `public-assets`       | Logo, favicon, assets publicos                 | Publico (CDN)            |

### 9.2 Cloudflare R2 (Backup Externo)

| Atributo              | Detalhe                                                    |
|-----------------------|------------------------------------------------------------|
| **Proposito**         | Backup externo de arquivos criticos e midia                |
| **Tipo**              | Cloud object storage S3-compatible                         |
| **Autenticacao**      | Access Key + Secret Key (S3-compatible)                    |
| **Custo**             | **$0.015/GB/mes** armazenamento, **gratuito** para egress  |
| **Vantagem**          | Sem custo de transferencia (diferente do S3 AWS)           |

**Uso**:
- Replicacao assicrona de backups do PostgreSQL
- Backup semanal de midias criticas
- CDN para assets publicos via Cloudflare

---

## 10. Estrategia de Resiliencia

### Padrao de Retry

Todas as integracoes utilizam retry com backoff exponencial:

```python
# Configuracao padrao de retry
@retry(
    stop=stop_after_attempt(3),          # Maximo 3 tentativas
    wait=wait_exponential(
        multiplier=1,                     # Base: 1 segundo
        min=2,                            # Minimo: 2 segundos
        max=30                            # Maximo: 30 segundos
    ),
    retry=retry_if_exception_type(
        (httpx.TimeoutException, httpx.HTTPStatusError)
    ),
    before_sleep=before_sleep_log(logger, logging.WARNING)
)
```

### Tabela de Fallbacks

| Servico Primario  | Fallback 1            | Fallback 2               | Acao Final            |
|-------------------|-----------------------|--------------------------|-----------------------|
| Claude 3.5 Sonnet | GPT-4o                | Gemini Pro               | Fila para retry manual|
| GPT-4o            | Claude 3.5 Sonnet     | Gemini Pro               | Fila para retry manual|
| DALL-E 3          | Flux (Replicate)      | -                        | Notificar usuario     |
| SerpAPI           | Google Custom Search  | Busca manual             | Cache de resultados   |
| WhatsApp (Evol.)  | SMS (Twilio)          | Email (Resend)           | Notificar usuario     |
| Resend (Email)    | Amazon SES            | -                        | Fila para retry       |
| Asaas             | PagSeguro             | -                        | Pagamento manual      |
| MinIO             | Cloudflare R2         | -                        | Upload postergado     |

### Circuit Breaker

```python
# Implementacao de circuit breaker para integracoes
from circuitbreaker import circuit

class IntegrationCircuitBreaker:
    """
    Circuit breaker com 3 estados:
    - CLOSED: Tudo funcionando normalmente
    - OPEN: Servico indisponivel, retorna fallback imediatamente
    - HALF-OPEN: Testando se servico voltou (1 request de teste)
    """

    @circuit(
        failure_threshold=5,          # 5 falhas para abrir
        recovery_timeout=60,          # 60 segundos para testar
        expected_exception=Exception
    )
    async def call_external_service(self, service, method, *args, **kwargs):
        return await getattr(service, method)(*args, **kwargs)
```

### Health Check de Integracoes

```python
# Endpoint de saude das integracoes
@router.get("/health/integrations")
async def check_integrations_health():
    results = {}
    checks = [
        ("whatsapp", check_whatsapp_health),
        ("anthropic", check_anthropic_health),
        ("openai", check_openai_health),
        ("serpapi", check_serpapi_health),
        ("asaas", check_asaas_health),
        ("resend", check_resend_health),
        ("minio", check_minio_health),
    ]
    for name, check_fn in checks:
        try:
            await asyncio.wait_for(check_fn(), timeout=5.0)
            results[name] = {"status": "healthy", "latency_ms": latency}
        except Exception as e:
            results[name] = {"status": "unhealthy", "error": str(e)}

    overall = "healthy" if all(
        r["status"] == "healthy" for r in results.values()
    ) else "degraded"

    return {"status": overall, "services": results}
```

---

## 11. Resumo de Custos

### Custos Mensais Estimados (Todas as Integracoes)

| Categoria          | Servico                    | Custo Mensal       | Notas                          |
|--------------------|----------------------------|--------------------|--------------------------------|
| **IA - LLM**       | Anthropic (Claude)         | $200-500           | Depende do volume de agentes   |
| **IA - LLM**       | OpenAI (GPT-4o + embeddings)| $100-300          | Inclui DALL-E 3                |
| **IA - LLM**       | Google AI (Gemini)         | $50-100            | Analise + fallback             |
| **IA - Imagens**   | Replicate (Flux)           | $30-60             | ~1000 imagens/mes              |
| **Busca**          | SerpAPI                    | $50                | 5000 buscas/mes                |
| **Email**          | Resend                     | $20                | 50.000 emails/mes              |
| **Fiscal**         | eNotas/Nuvem Fiscal        | R$ 99 (~$20)       | 100 NFs/mes                    |
| **Pagamentos**     | Asaas                      | Variavel           | % por transacao                |
| **Design**         | Canva Pro                  | R$ 35 (~$7)        | 1 usuario                      |
| **WhatsApp**       | Evolution API              | **Gratuito**       | Self-hosted                    |
| **Storage**        | MinIO                      | **Gratuito**       | Self-hosted                    |
| **Backup**         | Cloudflare R2              | ~$5                | ~300GB armazenados             |
| **Monitoramento**  | Sentry                     | **Gratuito**       | Tier Developer                 |
|                    |                            |                    |                                |
| **TOTAL ESTIMADO** |                            | **$500-1100/mes**  | ~R$ 2.500-5.500/mes            |

### Otimizacao de Custos

1. **Usar modelos baratos para tarefas simples**: GPT-4o-mini e Gemini Flash para classificacao e extracao
2. **Cache agressivo**: Cachear resultados de buscas SerpAPI por 24h
3. **Batch processing**: Agrupar chamadas de API quando possivel
4. **Monitorar consumo diario**: Alertas quando custo excede 120% do orcamento
5. **Renegociar tiers**: Subir de plano conforme volume aumenta (melhor custo unitario)
6. **Self-hosting quando possivel**: WhatsApp, storage, monitoramento

---

*Documento mantido pela equipe de desenvolvimento da Somos Produtora.*
