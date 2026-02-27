# 10 - Seguranca

> Somos Produtora - Estrategia Completa de Seguranca
> Versao: 1.0 | Ultima atualizacao: 2026-02-28

---

## Indice

1. [Visao Geral](#visao-geral)
2. [Autenticacao](#autenticacao)
3. [Autorizacao (RBAC)](#autorizacao-rbac)
4. [Matriz de Permissoes](#matriz-de-permissoes)
5. [Criptografia de Dados](#criptografia-de-dados)
6. [Conformidade LGPD](#conformidade-lgpd)
7. [Auditoria e Logging](#auditoria-e-logging)
8. [Seguranca da API](#seguranca-da-api)
9. [Seguranca dos Agentes IA](#seguranca-dos-agentes-ia)
10. [Testes de Seguranca](#testes-de-seguranca)
11. [Plano de Resposta a Incidentes](#plano-de-resposta-a-incidentes)
12. [Checklist de Seguranca](#checklist-de-seguranca)

---

## 1. Visao Geral

A estrategia de seguranca da Somos Produtora segue o principio de **defesa em profundidade** (defense in depth), aplicando multiplas camadas de protecao em cada nivel da arquitetura.

### Camadas de Seguranca

```
┌─────────────────────────────────────────────────────────────────────┐
│  CAMADA 1: REDE                                                     │
│  Cloudflare DDoS + UFW Firewall + SSH Key-Only + Fail2ban          │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 2: TRANSPORTE                                               │
│  TLS 1.3 + HSTS + Certificate Pinning                              │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 3: APLICACAO                                                │
│  Rate Limiting + CORS + Input Validation + SQL Injection Prevention │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 4: AUTENTICACAO                                             │
│  JWT + Refresh Tokens + bcrypt + MFA (futuro)                       │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 5: AUTORIZACAO                                              │
│  RBAC (5 roles) + Permissoes Granulares + Row-Level Security        │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 6: DADOS                                                    │
│  AES-256 at Rest + Criptografia de campos sensiveis + Backups      │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 7: AGENTES IA                                               │
│  Action Limits + Human Approval + Spending Caps + Sandboxing        │
├─────────────────────────────────────────────────────────────────────┤
│  CAMADA 8: AUDITORIA                                                │
│  Audit Logging + Monitoramento + Alertas + SIEM                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Autenticacao

### JWT (JSON Web Tokens) com Refresh Tokens

O sistema utiliza autenticacao baseada em JWT com um esquema de **access token + refresh token** para balancear seguranca e usabilidade.

#### Configuracao dos Tokens

| Parametro               | Access Token          | Refresh Token          |
|--------------------------|-----------------------|------------------------|
| **Algoritmo**            | HS256 (HMAC-SHA256)   | HS256 (HMAC-SHA256)    |
| **Validade**             | 15 minutos            | 7 dias                 |
| **Armazenamento**        | Memoria (JS) / Cookie | HttpOnly Cookie + Redis|
| **Rotacao**              | A cada 15 min         | A cada uso (rotation)  |
| **Revogacao**            | Via blacklist Redis    | Deletar do Redis       |

#### Implementacao

```python
# backend/app/core/security.py

from datetime import datetime, timedelta, timezone
from jose import JWTError, jwt
from passlib.context import CryptContext
import secrets

# Configuracoes
SECRET_KEY = settings.SECRET_KEY  # Variavel de ambiente, minimo 64 chars
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 15
REFRESH_TOKEN_EXPIRE_DAYS = 7

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")


def hash_password(password: str) -> str:
    """Hash de senha com bcrypt (custo 12, ~250ms)."""
    return pwd_context.hash(password)


def verify_password(plain_password: str, hashed_password: str) -> bool:
    """Verifica senha contra hash bcrypt."""
    return pwd_context.verify(plain_password, hashed_password)


def create_access_token(data: dict, expires_delta: timedelta = None) -> str:
    """Cria access token JWT."""
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + (expires_delta or timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES))
    to_encode.update({
        "exp": expire,
        "iat": datetime.now(timezone.utc),
        "type": "access",
        "jti": secrets.token_hex(16)  # JWT ID unico
    })
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)


def create_refresh_token(user_id: str) -> str:
    """Cria refresh token e armazena no Redis."""
    token_id = secrets.token_hex(32)
    expire = datetime.now(timezone.utc) + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)
    payload = {
        "sub": user_id,
        "exp": expire,
        "type": "refresh",
        "jti": token_id
    }
    token = jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

    # Armazenar no Redis com TTL
    redis_client.setex(
        f"refresh_token:{token_id}",
        REFRESH_TOKEN_EXPIRE_DAYS * 86400,
        user_id
    )
    return token


def verify_access_token(token: str) -> dict:
    """Verifica e decodifica access token."""
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        if payload.get("type") != "access":
            raise JWTError("Token type invalid")

        # Verificar se token nao esta na blacklist
        jti = payload.get("jti")
        if redis_client.exists(f"blacklist:{jti}"):
            raise JWTError("Token has been revoked")

        return payload
    except JWTError:
        raise


def revoke_token(jti: str, expires_in: int = 900):
    """Adiciona token a blacklist no Redis."""
    redis_client.setex(f"blacklist:{jti}", expires_in, "revoked")
```

#### Fluxo de Autenticacao

```
┌──────────┐                          ┌──────────┐                    ┌──────┐
│  Cliente  │                          │  Backend │                    │Redis │
└─────┬────┘                          └────┬─────┘                    └──┬───┘
      │                                    │                             │
      │  POST /api/v1/auth/login           │                             │
      │  {email, password}                 │                             │
      │───────────────────────────────────▶│                             │
      │                                    │  Verificar bcrypt hash      │
      │                                    │─────────────────────────────│
      │                                    │  Armazenar refresh token    │
      │                                    │────────────────────────────▶│
      │  {access_token, refresh_token}     │                             │
      │◀───────────────────────────────────│                             │
      │                                    │                             │
      │  GET /api/v1/leads                 │                             │
      │  Authorization: Bearer {access}    │                             │
      │───────────────────────────────────▶│                             │
      │                                    │  Verificar JWT              │
      │                                    │  Verificar blacklist        │
      │                                    │────────────────────────────▶│
      │  {data: [...]}                     │                             │
      │◀───────────────────────────────────│                             │
      │                                    │                             │
      │  [Access token expirou - 15 min]   │                             │
      │                                    │                             │
      │  POST /api/v1/auth/refresh         │                             │
      │  {refresh_token}                   │                             │
      │───────────────────────────────────▶│                             │
      │                                    │  Verificar refresh token    │
      │                                    │────────────────────────────▶│
      │                                    │  Rotacionar refresh token   │
      │                                    │────────────────────────────▶│
      │  {new_access, new_refresh}         │                             │
      │◀───────────────────────────────────│                             │
      │                                    │                             │
      │  POST /api/v1/auth/logout          │                             │
      │───────────────────────────────────▶│                             │
      │                                    │  Blacklist access token     │
      │                                    │  Deletar refresh token      │
      │                                    │────────────────────────────▶│
      │  {message: "Logged out"}           │                             │
      │◀───────────────────────────────────│                             │
```

### Politica de Senhas

| Requisito                          | Valor                              |
|------------------------------------|------------------------------------|
| Comprimento minimo                 | 8 caracteres                       |
| Letras maiusculas                  | Pelo menos 1                       |
| Letras minusculas                  | Pelo menos 1                       |
| Numeros                            | Pelo menos 1                       |
| Caracteres especiais               | Pelo menos 1                       |
| Historico de senhas                | Ultimas 5 (nao repetir)           |
| Bloqueio apos tentativas falhas    | 5 tentativas, bloqueio de 15 min   |
| Hashing                            | bcrypt com custo 12                |

```python
# Validacao de senha
import re

def validate_password(password: str) -> bool:
    """Valida politica de senha."""
    if len(password) < 8:
        return False
    if not re.search(r'[A-Z]', password):
        return False
    if not re.search(r'[a-z]', password):
        return False
    if not re.search(r'[0-9]', password):
        return False
    if not re.search(r'[!@#$%^&*()_+\-=\[\]{};:\'",.<>?/\\|`~]', password):
        return False
    return True
```

---

## 3. Autorizacao (RBAC)

### Papeis do Sistema

| Role          | Descricao                                                 | Nivel de Acesso |
|---------------|-----------------------------------------------------------|-----------------|
| **Admin**     | Administrador do sistema com acesso total                 | Total           |
| **Diretor**   | Diretor da produtora, acesso a relatorios e financeiro    | Alto            |
| **Comercial** | Equipe de vendas, acesso a leads, deals e conteudo        | Medio           |
| **Agente_IA** | Conta de servico para agentes de IA                       | Restrito        |
| **Cliente**   | Cliente externo, visualiza apenas seus projetos e faturas | Minimo          |

### Implementacao RBAC

```python
# backend/app/core/permissions.py

from enum import Enum
from functools import wraps
from fastapi import HTTPException, Depends

class Permission(str, Enum):
    # Usuarios
    USERS_READ = "users:read"
    USERS_WRITE = "users:write"
    USERS_DELETE = "users:delete"

    # Leads
    LEADS_READ = "leads:read"
    LEADS_WRITE = "leads:write"
    LEADS_DELETE = "leads:delete"

    # Deals
    DEALS_READ = "deals:read"
    DEALS_WRITE = "deals:write"
    DEALS_DELETE = "deals:delete"

    # Projetos
    PROJECTS_READ = "projects:read"
    PROJECTS_WRITE = "projects:write"
    PROJECTS_DELETE = "projects:delete"

    # Financeiro
    FINANCE_READ = "finance:read"
    FINANCE_WRITE = "finance:write"
    FINANCE_DELETE = "finance:delete"

    # Conteudo
    CONTENT_READ = "content:read"
    CONTENT_WRITE = "content:write"
    CONTENT_PUBLISH = "content:publish"

    # Agentes
    AGENTS_READ = "agents:read"
    AGENTS_WRITE = "agents:write"
    AGENTS_EXECUTE = "agents:execute"

    # Relatorios
    REPORTS_READ = "reports:read"
    REPORTS_GENERATE = "reports:generate"

    # Configuracoes
    SETTINGS_READ = "settings:read"
    SETTINGS_WRITE = "settings:write"

    # Audit
    AUDIT_READ = "audit:read"


# Mapeamento Role -> Permissoes
ROLE_PERMISSIONS = {
    "admin": [p.value for p in Permission],  # Todas as permissoes

    "diretor": [
        Permission.USERS_READ,
        Permission.LEADS_READ, Permission.LEADS_WRITE,
        Permission.DEALS_READ, Permission.DEALS_WRITE,
        Permission.PROJECTS_READ, Permission.PROJECTS_WRITE,
        Permission.FINANCE_READ, Permission.FINANCE_WRITE,
        Permission.CONTENT_READ, Permission.CONTENT_WRITE, Permission.CONTENT_PUBLISH,
        Permission.AGENTS_READ, Permission.AGENTS_WRITE,
        Permission.REPORTS_READ, Permission.REPORTS_GENERATE,
        Permission.SETTINGS_READ,
        Permission.AUDIT_READ,
    ],

    "comercial": [
        Permission.LEADS_READ, Permission.LEADS_WRITE,
        Permission.DEALS_READ, Permission.DEALS_WRITE,
        Permission.PROJECTS_READ,
        Permission.CONTENT_READ,
        Permission.REPORTS_READ,
    ],

    "agente_ia": [
        Permission.LEADS_READ, Permission.LEADS_WRITE,
        Permission.DEALS_READ,
        Permission.CONTENT_READ, Permission.CONTENT_WRITE,
        Permission.AGENTS_READ, Permission.AGENTS_EXECUTE,
    ],

    "cliente": [
        Permission.PROJECTS_READ,
        Permission.FINANCE_READ,  # Apenas suas proprias faturas
    ],
}


def require_permission(permission: Permission):
    """Decorator para verificar permissao no endpoint."""
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, current_user=Depends(get_current_user), **kwargs):
            user_permissions = get_user_permissions(current_user)
            if permission.value not in user_permissions:
                raise HTTPException(
                    status_code=403,
                    detail=f"Permissao insuficiente: {permission.value} necessaria"
                )
            return await func(*args, current_user=current_user, **kwargs)
        return wrapper
    return decorator
```

---

## 4. Matriz de Permissoes

### Tabela de Permissoes por Role e Modulo

| Modulo / Acao                | Admin | Diretor | Comercial | Agente_IA | Cliente |
|------------------------------|-------|---------|-----------|-----------|---------|
| **USUARIOS**                 |       |         |           |           |         |
| Listar usuarios              | SIM   | SIM     | NAO       | NAO       | NAO     |
| Criar/editar usuarios        | SIM   | NAO     | NAO       | NAO       | NAO     |
| Deletar usuarios             | SIM   | NAO     | NAO       | NAO       | NAO     |
| **LEADS**                    |       |         |           |           |         |
| Listar leads                 | SIM   | SIM     | SIM       | SIM       | NAO     |
| Criar leads                  | SIM   | SIM     | SIM       | SIM       | NAO     |
| Editar leads                 | SIM   | SIM     | SIM       | NAO       | NAO     |
| Deletar leads                | SIM   | NAO     | NAO       | NAO       | NAO     |
| **DEALS**                    |       |         |           |           |         |
| Listar deals                 | SIM   | SIM     | SIM       | SIM*      | NAO     |
| Criar/editar deals           | SIM   | SIM     | SIM       | NAO       | NAO     |
| Mover no pipeline            | SIM   | SIM     | SIM       | NAO       | NAO     |
| Deletar deals                | SIM   | NAO     | NAO       | NAO       | NAO     |
| **PROJETOS**                 |       |         |           |           |         |
| Listar projetos              | SIM   | SIM     | SIM*      | NAO       | SIM**   |
| Criar/editar projetos        | SIM   | SIM     | NAO       | NAO       | NAO     |
| Gerenciar tarefas            | SIM   | SIM     | NAO       | NAO       | NAO     |
| Deletar projetos             | SIM   | NAO     | NAO       | NAO       | NAO     |
| **FINANCEIRO**               |       |         |           |           |         |
| Ver faturas                  | SIM   | SIM     | NAO       | NAO       | SIM**   |
| Criar faturas                | SIM   | SIM     | NAO       | NAO       | NAO     |
| Registrar pagamentos         | SIM   | SIM     | NAO       | NAO       | NAO     |
| Ver despesas                 | SIM   | SIM     | NAO       | NAO       | NAO     |
| Aprovar despesas             | SIM   | SIM     | NAO       | NAO       | NAO     |
| Emitir NF-e                  | SIM   | SIM     | NAO       | NAO       | NAO     |
| **CONTEUDO**                 |       |         |           |           |         |
| Listar conteudo              | SIM   | SIM     | SIM       | SIM       | NAO     |
| Criar conteudo               | SIM   | SIM     | NAO       | SIM       | NAO     |
| Aprovar/publicar conteudo    | SIM   | SIM     | NAO       | NAO       | NAO     |
| **AGENTES IA**               |       |         |           |           |         |
| Ver status dos agentes       | SIM   | SIM     | NAO       | SIM       | NAO     |
| Configurar agentes           | SIM   | SIM     | NAO       | NAO       | NAO     |
| Executar agentes             | SIM   | SIM     | NAO       | SIM       | NAO     |
| Pausar/desativar agentes     | SIM   | SIM     | NAO       | NAO       | NAO     |
| **RELATORIOS**               |       |         |           |           |         |
| Ver relatorios               | SIM   | SIM     | SIM*      | NAO       | NAO     |
| Gerar relatorios             | SIM   | SIM     | NAO       | NAO       | NAO     |
| **CONFIGURACOES**            |       |         |           |           |         |
| Ver configuracoes            | SIM   | SIM     | NAO       | NAO       | NAO     |
| Alterar configuracoes        | SIM   | NAO     | NAO       | NAO       | NAO     |
| **AUDITORIA**                |       |         |           |           |         |
| Ver logs de auditoria        | SIM   | SIM     | NAO       | NAO       | NAO     |

**Legenda**:
- `*` = Acesso restrito (apenas seus proprios registros ou registros do seu departamento)
- `**` = Acesso restrito (apenas registros vinculados ao seu perfil de cliente)

---

## 5. Criptografia de Dados

### Em Repouso (At Rest)

| Dado                       | Metodo de Criptografia              | Onde                     |
|----------------------------|-------------------------------------|--------------------------|
| Senhas de usuarios         | bcrypt (custo 12)                   | Campo `password_hash`    |
| API Keys de integracoes    | AES-256-GCM (Fernet)               | Tabela `settings`        |
| Dados bancarios fornecedores| AES-256-GCM (Fernet)              | Campo `bank_info` (JSONB)|
| Certificados digitais      | AES-256-GCM (Fernet)               | Storage criptografado    |
| Backups do banco           | GPG (RSA-4096 + AES-256)           | Arquivos .sql.gz.gpg     |
| Disco do servidor          | LUKS (Linux Unified Key Setup)     | Volume inteiro           |

### Implementacao de Criptografia de Campos

```python
# backend/app/core/encryption.py

from cryptography.fernet import Fernet
import base64
import os

class FieldEncryption:
    """Criptografia simetrica para campos sensiveis no banco de dados."""

    def __init__(self):
        key = os.environ.get("ENCRYPTION_KEY")
        if not key:
            raise ValueError("ENCRYPTION_KEY nao configurada")
        self.fernet = Fernet(key.encode())

    def encrypt(self, plaintext: str) -> str:
        """Criptografa texto e retorna string base64."""
        return self.fernet.encrypt(plaintext.encode()).decode()

    def decrypt(self, ciphertext: str) -> str:
        """Descriptografa string base64 e retorna texto."""
        return self.fernet.decrypt(ciphertext.encode()).decode()


# Uso no modelo SQLAlchemy
from sqlalchemy import TypeDecorator, String

class EncryptedString(TypeDecorator):
    """Tipo customizado que criptografa/descriptografa automaticamente."""
    impl = String
    cache_ok = True

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.encryptor = FieldEncryption()

    def process_bind_param(self, value, dialect):
        if value is not None:
            return self.encryptor.encrypt(value)
        return value

    def process_result_value(self, value, dialect):
        if value is not None:
            return self.encryptor.decrypt(value)
        return value


# Uso no modelo
class IntegrationSettings(Base):
    __tablename__ = "integration_settings"

    id = Column(UUID, primary_key=True, default=uuid4)
    service_name = Column(String(100), nullable=False)
    api_key = Column(EncryptedString(500))  # Criptografado automaticamente
    api_secret = Column(EncryptedString(500))
    config = Column(JSONB, default={})
```

### Em Transito (In Transit)

| Conexao                    | Protocolo        | Configuracao                          |
|----------------------------|------------------|---------------------------------------|
| Cliente <-> Nginx          | TLS 1.3          | Let's Encrypt, HSTS enabled           |
| Nginx <-> Backend          | HTTP (interno)   | Rede Docker isolada, nao exposta      |
| Backend <-> PostgreSQL     | TLS (opcional)   | `sslmode=require` em producao         |
| Backend <-> Redis          | AUTH + TLS       | Password + rede interna               |
| Backend <-> APIs externas  | TLS 1.2+         | Certificados verificados              |
| SSH                        | SSH + Ed25519    | Chave Ed25519, sem senha              |

---

## 6. Conformidade LGPD

### Lei Geral de Protecao de Dados (Lei 13.709/2018)

A Somos Produtora processa dados pessoais de leads, contatos e clientes. A conformidade com a LGPD e obrigatoria.

### Bases Legais para Tratamento de Dados

| Dado                        | Base Legal                    | Justificativa                          |
|-----------------------------|-------------------------------|----------------------------------------|
| Dados de leads (prospeccao) | Legitimo interesse (Art. 7, IX)| Necessario para atividade comercial   |
| Dados de clientes           | Execucao de contrato (Art. 7, V)| Necessario para prestar servico      |
| Dados de contato (WhatsApp) | Consentimento (Art. 7, I)     | Requer opt-in explicito               |
| Dados financeiros           | Obrigacao legal (Art. 7, II)  | Obrigacoes fiscais e contratuais      |
| Dados de navegacao          | Consentimento (Art. 7, I)     | Cookie banner com opt-in              |

### Direitos do Titular (LGPD Art. 18)

Implementacao tecnica de cada direito:

| Direito                        | Endpoint                            | Descricao                            |
|--------------------------------|-------------------------------------|--------------------------------------|
| Confirmacao de tratamento      | `GET /api/v1/lgpd/data-exists`      | Verifica se existem dados do titular |
| Acesso aos dados               | `GET /api/v1/lgpd/my-data`          | Exporta todos os dados em JSON       |
| Correcao de dados              | `PATCH /api/v1/lgpd/my-data`        | Permite atualizar dados pessoais     |
| Anonimizacao                   | `POST /api/v1/lgpd/anonymize`       | Anonimiza dados pessoais             |
| Eliminacao                     | `DELETE /api/v1/lgpd/my-data`       | Exclui dados pessoais (soft/hard)    |
| Portabilidade                  | `GET /api/v1/lgpd/export`           | Exporta dados em formato padrao (CSV/JSON) |
| Revogacao de consentimento     | `POST /api/v1/lgpd/revoke-consent`  | Revoga consentimento especifico      |

### Implementacao de Exportacao de Dados (Portabilidade)

```python
# backend/app/api/v1/lgpd.py

@router.get("/lgpd/export")
@require_permission(Permission.LGPD_SELF)
async def export_my_data(
    current_user: User = Depends(get_current_user),
    format: str = Query("json", enum=["json", "csv"])
):
    """
    LGPD Art. 18, V - Portabilidade de dados.
    Exporta todos os dados pessoais do titular.
    """
    data = {
        "usuario": {
            "nome": current_user.full_name,
            "email": current_user.email,
            "telefone": current_user.phone,
            "criado_em": current_user.created_at.isoformat(),
        },
        "leads": await get_leads_by_contact_email(current_user.email),
        "interacoes": await get_interactions_by_user(current_user.id),
        "consentimentos": await get_consents_by_user(current_user.id),
    }

    if format == "csv":
        return generate_csv_export(data)
    return data
```

### Implementacao de Anonimizacao

```python
async def anonymize_user_data(user_id: str):
    """
    Anonimiza dados pessoais substituindo por valores genericos.
    Mantem estrutura para integridade referencial.
    """
    # Anonimizar usuario
    await db.execute(
        update(User)
        .where(User.id == user_id)
        .values(
            full_name="[ANONIMIZADO]",
            email=f"anonimizado_{secrets.token_hex(8)}@removed.local",
            phone=None,
            avatar_url=None,
            is_active=False
        )
    )

    # Anonimizar contatos vinculados
    await db.execute(
        update(Contact)
        .where(Contact.created_by == user_id)
        .values(
            full_name="[ANONIMIZADO]",
            email=None,
            phone=None,
            whatsapp=None,
            linkedin_url=None
        )
    )

    # Registrar acao no audit log
    await create_audit_log(
        user_id=user_id,
        action="lgpd_anonymize",
        resource_type="user",
        resource_id=user_id
    )
```

### DPO (Data Protection Officer)

| Atributo              | Detalhe                                          |
|-----------------------|--------------------------------------------------|
| **Designacao**        | Obrigatoria (LGPD Art. 41)                       |
| **Responsavel**       | Definir pessoa ou empresa terceirizada            |
| **Canal de contato**  | dpo@somosprodutora.com.br                        |
| **Publicacao**        | Informacoes do DPO no site da empresa             |
| **Responsabilidades** | Receber reclamacoes ANPD, orientar equipe, auditar compliance |

### Consentimento

```python
# Tabela de consentimentos
class Consent(Base):
    __tablename__ = "consents"

    id = Column(UUID, primary_key=True, default=uuid4)
    user_id = Column(UUID, ForeignKey("users.id"), nullable=True)
    contact_id = Column(UUID, ForeignKey("contacts.id"), nullable=True)
    purpose = Column(String(100), nullable=False)  # "marketing", "whatsapp", "analytics"
    granted = Column(Boolean, nullable=False)
    granted_at = Column(DateTime(timezone=True))
    revoked_at = Column(DateTime(timezone=True))
    ip_address = Column(INET)
    method = Column(String(50))  # "web_form", "whatsapp_opt_in", "verbal"
    evidence = Column(Text)  # Registro do consentimento
    created_at = Column(DateTime(timezone=True), default=func.now())
```

---

## 7. Auditoria e Logging

### O que e Registrado no Audit Log

Todas as acoes sensiveis sao registradas com as seguintes informacoes:

| Campo              | Descricao                                        |
|--------------------|--------------------------------------------------|
| `user_id`          | ID do usuario que realizou a acao                |
| `agent_id`         | ID do agente IA (se acao automatizada)           |
| `action`           | Tipo de acao (create, update, delete, login, etc)|
| `resource_type`    | Tipo de recurso afetado (lead, deal, user, etc.) |
| `resource_id`      | ID do recurso afetado                            |
| `old_values`       | Valores anteriores (para update/delete)          |
| `new_values`       | Novos valores (para create/update)               |
| `ip_address`       | IP do solicitante                                |
| `user_agent`       | Browser/client do solicitante                    |
| `session_id`       | ID da sessao ativa                               |
| `created_at`       | Timestamp preciso (microsegundos)                |

### Acoes que Geram Audit Log

| Categoria          | Acoes                                             |
|--------------------|---------------------------------------------------|
| **Autenticacao**   | Login, logout, falha de login, troca de senha, refresh token |
| **Usuarios**       | Criar, editar, deletar, alterar role, bloquear    |
| **Leads/Deals**    | Criar, editar, mover no pipeline, deletar         |
| **Financeiro**     | Criar fatura, registrar pagamento, emitir NF, aprovar despesa |
| **Contratos**      | Criar, enviar, assinar, cancelar                  |
| **Conteudo**       | Publicar, agendar, remover                        |
| **Agentes IA**     | Ativar, pausar, configurar, executar acao critica |
| **Configuracoes**  | Alterar qualquer configuracao do sistema          |
| **LGPD**           | Exportar dados, anonimizar, revogar consentimento |

### Implementacao do Middleware de Auditoria

```python
# backend/app/core/audit.py

from fastapi import Request
import structlog

logger = structlog.get_logger("audit")

async def create_audit_log(
    user_id: str = None,
    agent_id: str = None,
    action: str = "",
    resource_type: str = "",
    resource_id: str = None,
    old_values: dict = None,
    new_values: dict = None,
    request: Request = None
):
    """Cria registro de auditoria imutavel."""
    log_entry = AuditLog(
        user_id=user_id,
        agent_id=agent_id,
        action=action,
        resource_type=resource_type,
        resource_id=resource_id,
        old_values=old_values,
        new_values=new_values,
        ip_address=request.client.host if request else None,
        user_agent=request.headers.get("user-agent") if request else None,
        session_id=request.state.session_id if request and hasattr(request.state, "session_id") else None,
    )
    db.add(log_entry)
    await db.commit()

    # Log estruturado para Loki/ELK
    logger.info(
        "audit_event",
        user_id=user_id,
        action=action,
        resource_type=resource_type,
        resource_id=resource_id,
    )
```

### Retencao de Logs de Auditoria

| Tipo de Log         | Retencao Minima | Retencao Recomendada | Motivo                    |
|---------------------|-----------------|----------------------|---------------------------|
| Autenticacao        | 12 meses        | 36 meses             | Seguranca                 |
| Financeiro          | 60 meses        | 60 meses             | Obrigacao fiscal (5 anos) |
| LGPD (consentimento)| 60 meses        | 60 meses             | Comprovacao legal         |
| Operacional         | 6 meses         | 12 meses             | Troubleshooting           |
| Agentes IA          | 12 meses        | 24 meses             | Analise e melhoria        |

---

## 8. Seguranca da API

### Rate Limiting

```python
# backend/app/core/middleware.py

from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(
    key_func=get_remote_address,
    storage_uri="redis://redis:6379/2"
)

# Limites por endpoint
RATE_LIMITS = {
    "login":        "5/minute",     # 5 tentativas por minuto
    "register":     "3/minute",     # 3 registros por minuto
    "password_reset": "3/hour",     # 3 resets por hora
    "api_general":  "100/minute",   # 100 requests por minuto (geral)
    "api_write":    "30/minute",    # 30 escritas por minuto
    "webhook":      "200/minute",   # 200 webhooks por minuto
    "file_upload":  "10/minute",    # 10 uploads por minuto
}
```

### CORS (Cross-Origin Resource Sharing)

```python
# backend/app/main.py

from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "https://app.somosprodutora.com.br",
        "https://admin.somosprodutora.com.br",
        "http://localhost:3000",  # Apenas em desenvolvimento
    ],
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "PATCH", "DELETE"],
    allow_headers=["Authorization", "Content-Type", "X-Request-ID"],
    max_age=3600,
)
```

### Validacao de Input

```python
# Exemplo de schema Pydantic com validacao rigorosa
from pydantic import BaseModel, EmailStr, Field, validator
import bleach

class CreateLeadSchema(BaseModel):
    contact_name: str = Field(..., min_length=2, max_length=255)
    email: EmailStr
    phone: str = Field(None, pattern=r'^\+?[0-9]{10,15}$')
    company_name: str = Field(..., min_length=2, max_length=255)
    source: str = Field(..., pattern=r'^(linkedin|google|instagram|facebook|referral|other)$')
    notes: str = Field(None, max_length=5000)

    @validator('contact_name', 'company_name', 'notes')
    def sanitize_text(cls, v):
        """Remove tags HTML e scripts potencialmente maliciosos."""
        if v:
            return bleach.clean(v, tags=[], strip=True)
        return v
```

### Prevencao de SQL Injection

O SQLAlchemy ORM parametriza todas as queries automaticamente. Adicionalmente:

```python
# NUNCA fazer isso:
# query = f"SELECT * FROM users WHERE email = '{email}'"  # VULNERAVEL

# SEMPRE usar parametros:
result = await db.execute(
    select(User).where(User.email == email)  # Parametrizado automaticamente
)

# Para queries raw (quando necessario):
result = await db.execute(
    text("SELECT * FROM users WHERE email = :email"),
    {"email": email}  # Parametro seguro
)
```

### Headers de Seguranca

```python
# Middleware para headers de seguranca
@app.middleware("http")
async def add_security_headers(request: Request, call_next):
    response = await call_next(request)
    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    response.headers["X-XSS-Protection"] = "1; mode=block"
    response.headers["Referrer-Policy"] = "strict-origin-when-cross-origin"
    response.headers["Permissions-Policy"] = "camera=(), microphone=(), geolocation=()"
    response.headers["X-Request-ID"] = request.state.request_id
    return response
```

---

## 9. Seguranca dos Agentes IA

### Principios de Seguranca para Agentes

1. **Principio do menor privilegio**: Cada agente acessa apenas o que precisa
2. **Limites explicitos**: Gastos, acoes e tokens limitados
3. **Supervisao humana**: Acoes criticas requerem aprovacao
4. **Isolamento**: Agentes nao podem acessar recursos de outros agentes
5. **Auditoria total**: Todas as acoes sao registradas
6. **Idempotencia**: Acoes devem ser seguras para repetir

### Limites por Agente

```python
# backend/app/agents/security.py

class AgentSecurityConfig:
    """Configuracao de seguranca por agente."""

    def __init__(self, agent_id: str):
        self.agent = get_agent_config(agent_id)

    @property
    def limits(self):
        return {
            "max_tokens_per_request": 50_000,
            "max_tokens_per_day": self.agent.daily_token_limit,
            "max_cost_per_day": self.agent.daily_cost_limit,
            "max_cost_per_month": self.agent.monthly_cost_limit,
            "max_actions_per_day": self.agent.daily_action_limit,
            "max_execution_time_seconds": 300,
            "max_retries_per_task": 3,
        }

    async def check_limits(self, action: str, estimated_tokens: int = 0):
        """Verifica se o agente ainda esta dentro dos limites."""
        today = date.today()

        # Verificar custo diario
        daily_cost = await get_agent_daily_cost(self.agent.id, today)
        if daily_cost >= self.limits["max_cost_per_day"]:
            raise AgentLimitExceeded(
                f"Agente {self.agent.name}: limite diario de custo atingido "
                f"({daily_cost:.2f} >= {self.limits['max_cost_per_day']:.2f})"
            )

        # Verificar custo mensal
        monthly_cost = await get_agent_monthly_cost(self.agent.id, today)
        if monthly_cost >= self.limits["max_cost_per_month"]:
            raise AgentLimitExceeded(
                f"Agente {self.agent.name}: limite mensal de custo atingido"
            )

        # Verificar acoes diarias
        daily_actions = await get_agent_daily_actions(self.agent.id, today)
        if daily_actions >= self.limits["max_actions_per_day"]:
            raise AgentLimitExceeded(
                f"Agente {self.agent.name}: limite diario de acoes atingido"
            )

        return True
```

### Acoes que Requerem Aprovacao Humana

```python
# Definicao de acoes criticas que precisam de aprovacao
ACTIONS_REQUIRING_APPROVAL = {
    "send_proposal":        {"roles": ["comercial", "diretor", "admin"]},
    "send_contract":        {"roles": ["diretor", "admin"]},
    "publish_content":      {"roles": ["diretor", "admin"]},
    "send_mass_email":      {"roles": ["diretor", "admin"], "threshold": 50},
    "modify_agent_config":  {"roles": ["admin"]},
    "delete_data":          {"roles": ["admin"]},
    "approve_expense":      {"roles": ["diretor", "admin"], "threshold": 1000},
    "emit_nfe":             {"roles": ["diretor", "admin"]},
}

async def request_human_approval(
    agent_id: str,
    action: str,
    context: dict,
    urgency: str = "normal"
):
    """Solicita aprovacao humana para acao critica do agente."""
    approval_request = AgentApprovalRequest(
        agent_id=agent_id,
        action=action,
        context=context,
        urgency=urgency,
        status="pending",
        required_roles=ACTIONS_REQUIRING_APPROVAL[action]["roles"]
    )
    await db.add(approval_request)
    await db.commit()

    # Notificar usuarios com role adequado
    await notify_approvers(approval_request)

    # Aguardar aprovacao (com timeout)
    # O agente entra em espera ate receber aprovacao ou rejeicao
    return approval_request.id
```

### Sandboxing de Agentes

```python
# Cada agente so pode usar suas proprias ferramentas
class AgentSandbox:
    """Sandbox que restringe o que cada agente pode fazer."""

    AGENT_TOOLS = {
        "hunter_linkedin": ["linkedin_search", "enrichment", "database_write"],
        "sdr_whatsapp": ["whatsapp_send", "calendar", "lead_read", "database_write"],
        "content_writer": ["content_generate", "seo_check", "brand_read", "database_write"],
        # ...
    }

    def __init__(self, agent_slug: str):
        self.allowed_tools = self.AGENT_TOOLS.get(agent_slug, [])

    def validate_tool_access(self, tool_name: str):
        if tool_name not in self.allowed_tools:
            raise AgentSecurityViolation(
                f"Agente tentou acessar ferramenta nao autorizada: {tool_name}"
            )
```

---

## 10. Testes de Seguranca

### Cronograma de Testes

| Tipo de Teste                    | Frequencia  | Responsavel              | Ferramenta              |
|----------------------------------|-------------|--------------------------|-------------------------|
| Analise estatica de codigo (SAST)| Cada commit | CI/CD automatico         | Bandit (Python), ESLint |
| Analise de dependencias          | Diario      | CI/CD automatico         | Dependabot, Snyk        |
| Testes de seguranca da API       | Semanal     | Equipe de desenvolvimento| OWASP ZAP, Postman      |
| Scan de vulnerabilidades         | Mensal      | DevOps                   | Trivy (containers)      |
| Teste de penetracao              | Trimestral  | Empresa terceirizada     | Manual + ferramentas    |
| Revisao de permissoes            | Mensal      | Admin                    | Script automatizado     |
| Simulacao de phishing            | Semestral   | RH + TI                  | GoPhish                 |
| Auditoria LGPD                   | Anual       | DPO + Juridico           | Checklist ANPD          |

### Pipeline de Seguranca no CI/CD

```yaml
# .github/workflows/security.yml

name: Security Checks

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * 1'  # Toda segunda as 6h

jobs:
  sast:
    name: Static Analysis
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Bandit (Python SAST)
        run: |
          pip install bandit
          bandit -r backend/app/ -f json -o bandit-report.json || true
      - name: Run Semgrep
        uses: returntocorp/semgrep-action@v1
        with:
          config: auto

  dependency-check:
    name: Dependency Vulnerabilities
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Safety (Python deps)
        run: |
          pip install safety
          safety check -r backend/requirements/base.txt
      - name: Run npm audit (JS deps)
        working-directory: ./frontend
        run: npm audit --audit-level=high

  container-scan:
    name: Container Security
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build images
        run: docker compose build
      - name: Run Trivy
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: somos-backend:latest
          format: 'table'
          severity: 'CRITICAL,HIGH'

  secret-scan:
    name: Secret Detection
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Run Gitleaks
        uses: gitleaks/gitleaks-action@v2
```

### Checklist de Teste de Penetracao

| Area                    | Verificacao                                            | Prioridade |
|-------------------------|--------------------------------------------------------|------------|
| Autenticacao            | Brute force no login                                   | Critica    |
| Autenticacao            | Bypass de JWT (algoritmo none, chave fraca)            | Critica    |
| Autenticacao            | Roubo de sessao/refresh token                          | Critica    |
| Autorizacao             | Escalacao de privilegios (horizontal e vertical)       | Critica    |
| Autorizacao             | IDOR (Insecure Direct Object References)               | Alta       |
| Input                   | SQL Injection em todos os endpoints                    | Critica    |
| Input                   | XSS (Stored, Reflected, DOM-based)                     | Alta       |
| Input                   | Command Injection                                      | Critica    |
| Input                   | Path Traversal                                         | Alta       |
| API                     | Rate limiting efetivo                                  | Media      |
| API                     | Mass assignment                                        | Alta       |
| API                     | Exposicao de dados sensiveis em responses              | Alta       |
| File Upload             | Upload de arquivos maliciosos                          | Alta       |
| File Upload             | Tamanho e tipo de arquivo nao validados                | Media      |
| Infra                   | Portas abertas desnecessariamente                      | Media      |
| Infra                   | Headers de seguranca ausentes                          | Media      |
| Agentes IA              | Prompt injection via dados de leads                    | Alta       |
| Agentes IA              | Bypass de limites de acoes                             | Alta       |
| Agentes IA              | Exfiltracao de dados via tool calls                    | Alta       |

---

## 11. Plano de Resposta a Incidentes

### Classificacao de Incidentes de Seguranca

| Nivel    | Descricao                                        | Exemplos                              | SLA Resposta |
|----------|--------------------------------------------------|---------------------------------------|--------------|
| **S1**   | Brecha critica com exposicao de dados            | Vazamento de banco, ransomware        | 15 min       |
| **S2**   | Vulnerabilidade explorada sem vazamento confirmado| SQL injection detectado em producao   | 30 min       |
| **S3**   | Tentativa de ataque bloqueada                    | Brute force detectado, DDoS mitigado  | 2 horas      |
| **S4**   | Vulnerabilidade identificada (nao explorada)     | Dependencia com CVE, config insegura  | 24 horas     |

### Fluxo de Resposta a Incidentes

```
┌───────────────┐   ┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│  1. DETECTAR  │──▶│ 2. CONTER     │──▶│ 3. ERRADICAR  │──▶│ 4. RECUPERAR  │
│               │   │               │   │               │   │               │
│ - Monitoramento│  │ - Isolar      │   │ - Patching    │   │ - Restaurar   │
│ - Alerta      │   │   sistema     │   │ - Remover     │   │   servicos    │
│ - Triagem     │   │ - Bloquear    │   │   malware     │   │ - Verificar   │
│               │   │   acessos     │   │ - Corrigir    │   │   integridade │
└───────────────┘   └───────────────┘   │   vulnerab.   │   └───────┬───────┘
                                        └───────────────┘           │
                                                              ┌─────▼───────┐
                                                              │ 5. APRENDER │
                                                              │             │
                                                              │ - Post-     │
                                                              │   mortem    │
                                                              │ - Atualizar │
                                                              │   controles │
                                                              │ - Treinar   │
                                                              │   equipe    │
                                                              └─────────────┘
```

### Procedimentos por Tipo de Incidente

#### Vazamento de Dados (S1)

1. **Conter**: Desconectar servidor afetado da rede
2. **Preservar evidencias**: Snapshot dos logs antes de qualquer acao
3. **Avaliar escopo**: Quais dados foram expostos? Quantos titulares afetados?
4. **Notificar**: ANPD em ate 2 dias uteis (LGPD Art. 48)
5. **Notificar titulares**: Se risco relevante (LGPD Art. 48, par. 1)
6. **Remediar**: Corrigir vulnerabilidade, trocar credenciais comprometidas
7. **Post-mortem**: Documentar causa raiz e acoes preventivas

#### Comprometimento de Credenciais (S2)

1. **Revogar** todos os tokens do usuario afetado
2. **Forcar** troca de senha
3. **Verificar** atividades suspeitas nos audit logs
4. **Notificar** o usuario
5. **Investigar** como as credenciais foram comprometidas

### Contatos de Emergencia

| Papel                | Responsavel          | Canal                    |
|----------------------|----------------------|--------------------------|
| Responsavel Tecnico  | [Nome]               | Telefone + Telegram      |
| DPO                  | [Nome]               | Email + Telefone         |
| Diretor              | [Nome]               | Telefone                 |
| Advogado             | [Escritorio]         | Telefone                 |
| ANPD                 | Autoridade Nacional  | www.gov.br/anpd          |

---

## 12. Checklist de Seguranca

### Antes do Lancamento

- [ ] Todas as senhas padrao alteradas
- [ ] Variaveis de ambiente nao expostas no codigo
- [ ] `.env` no `.gitignore`
- [ ] Gitleaks rodando no CI/CD
- [ ] SSL/TLS configurado e funcionando
- [ ] HSTS habilitado
- [ ] Headers de seguranca configurados no Nginx
- [ ] Rate limiting ativo em todos os endpoints
- [ ] CORS configurado com dominios especificos
- [ ] Firewall UFW ativo com regras minimas
- [ ] SSH com chave (senha desativada)
- [ ] Fail2ban ativo
- [ ] Bcrypt para todas as senhas (custo >= 12)
- [ ] JWT com chave forte (>= 64 chars)
- [ ] Refresh token rotation implementada
- [ ] RBAC implementado e testado
- [ ] Validacao de input em todos os endpoints (Pydantic)
- [ ] Sanitizacao de HTML (bleach)
- [ ] SQLAlchemy ORM usado (sem queries raw concatenadas)
- [ ] Audit log implementado para acoes sensiveis
- [ ] Backup automatico configurado e testado
- [ ] Politica de retencao de dados definida (LGPD)
- [ ] Termos de uso e politica de privacidade publicados
- [ ] DPO designado e publicado
- [ ] Teste de penetracao basico executado
- [ ] Scan de dependencias sem vulnerabilidades criticas
- [ ] Containers escaneados com Trivy
- [ ] Documentacao de seguranca revisada

### Revisoes Periodicas

| Frequencia   | Acao                                                  |
|--------------|-------------------------------------------------------|
| **Diaria**   | Verificar alertas de seguranca do Sentry/Grafana      |
| **Semanal**  | Revisar logs de autenticacao (falhas, padroes)        |
| **Mensal**   | Atualizar dependencias, revisar permissoes de acesso  |
| **Trimestral** | Teste de penetracao, revisao de configuracoes       |
| **Semestral** | Simulacao de phishing, treinamento da equipe         |
| **Anual**    | Auditoria LGPD completa, revisao do plano de incidentes |

---

*Documento mantido pela equipe de seguranca da Somos Produtora.*
*Classificacao: CONFIDENCIAL - Uso interno apenas.*
