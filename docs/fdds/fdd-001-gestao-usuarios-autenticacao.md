### FDD: Gestao de Usuarios e Autenticacao

Versao: 1.0
Data: 2026-03-27
Responsavel: A definir

---

### 1. Contexto e motivacao tecnica

O modulo User e o modulo raiz da plataforma CodeLearn, sem dependencias externas a outros modulos. Ele resolve o problema de controle de acesso granular para 4 papeis (student, professor, tutor, admin) com autorizacao contextual.

A autenticacao utiliza JWT com validacao server-side (sessao persistida no banco), conforme ADR-004, permitindo invalidacao imediata ao bloquear um usuario. A autorizacao opera em 3 camadas: RBAC no middleware (verificacao de papel), ABAC e ACL nos use cases (verificacao contextual e por recurso), conforme ADR-005.

**Atores:** Admin (gestao de usuarios, papeis e bloqueio), Usuario de qualquer papel (login), Aluno (isolamento de dados).

**Encaixe no HLD:** O modulo User e listado como componente raiz do qual todos os demais modulos dependem para autenticacao e autorizacao. A stack e Express.js 5 + TypeScript (ESM), PostgreSQL 17, arquitetura hexagonal com InversifyJS para injecao de dependencias.

**Suposicoes:**
- Apenas Admin cria usuarios (sem cadastro self-service)
- Papeis sao fixos (student, professor, tutor, admin), sem criacao dinamica

**Restricoes:**
- JWT com validacao server-side obrigatoria (ADR-004)
- Autorizacao em 3 camadas obrigatoria (ADR-005)
- Soft delete obrigatorio para preservacao de dados historicos (RN-022)

---

### 2. Objetivos tecnicos

- CRUD completo de usuarios com validacao de nome (max 100 chars), email (unico, formato valido), senha (min 8 chars, 1 maiuscula, 1 minuscula, 1 numero, 1 simbolo) e ao menos 1 papel atribuido (RN-023). Invariante: todo usuario possui ao menos 1 papel valido.
- Autenticacao via JWT com sessao server-side emitindo access token (15 min) + refresh token (7 dias), com verificacao de sessao ativa no banco a cada requisicao (ADR-004). Medida: sessao consultada em 100% das requisicoes autenticadas.
- Invalidacao imediata de sessao ao bloquear usuario. Invariante: zero delay entre bloqueio e impossibilidade de uso dos tokens existentes.
- Autorizacao em camadas com RBAC no middleware e ABAC/ACL nos use cases (ADR-005). Medida: zero acessos nao autorizados a funcionalidades fora do papel do usuario.
- Isolamento de dados garantindo que aluno so acesse seus proprios dados/matriculas (RN-019). Medida: tentativa de acesso via URL a recurso de outro aluno retorna 403.
- SLA de performance conforme HLD: p95 < 200ms leitura, < 500ms escrita nos endpoints de Auth e Users.
- Soft delete para dados de usuario (RN-022). Invariante: nenhum registro e fisicamente removido do banco.

---

### 3. Escopo e exclusoes

**Incluido**
- Entidade User (nome, email, senha com hash, papeis, situacao ativo/bloqueado)
- Endpoint de criacao de usuario (Admin)
- Endpoint de edicao de papeis (adicionar/remover, Admin)
- Endpoint de bloqueio e desbloqueio de usuario (Admin)
- Endpoint de login (email + senha) com emissao de access token + refresh token
- Endpoint de refresh token
- Endpoint de logout (invalidacao de sessao)
- Endpoints de consulta de usuario (por ID e listagem com paginacao)
- Tabela de sessoes no PostgreSQL para validacao server-side
- Middleware de autenticacao (validacao JWT + sessao ativa)
- Middleware de autorizacao RBAC (verificacao de papel por endpoint)
- Logica ABAC/ACL nos use cases para isolamento de dados (RN-019)
- Hash de senha com argon2
- Soft delete para usuarios (RN-022)

**Excluido**
- Tela/frontend (apenas API REST)
- Recuperacao de senha / fluxo de "esqueci minha senha"
- Cadastro self-service (apenas Admin cria usuarios)
- Autenticacao via provedores externos (OAuth/SSO)
- Vinculacao de tutor a cursos (pertence ao modulo Course/CourseTutor)
- Verificacao de email (confirmacao por link)
- Rate limiting (tratado como preocupacao transversal, fora deste modulo)

---

### 4. Fluxos detalhados e diagramas

**Fluxo principal: Cadastro de usuario (Admin)**
1. Admin autenticado envia POST /v1/users com nome, email, senha e papeis
2. Middleware de autenticacao valida JWT + sessao ativa no banco
3. Middleware RBAC verifica que o usuario tem papel "admin"
4. Controller repassa para o use case CreateUser
5. Use case valida: nome (max 100 chars), email (formato valido), senha (politica), ao menos 1 papel valido
6. Use case verifica unicidade do email no banco
7. Use case gera hash da senha (argon2)
8. Use case persiste o usuario com situacao "active" e papeis atribuidos
9. Retorna 201 com dados do usuario (sem senha)

**Fluxo: Login (qualquer usuario)**
1. Usuario envia POST /v1/auth/login com email e senha
2. Use case busca usuario por email
3. Se nao encontrado, retorna erro generico (sem revelar se email existe)
4. Se encontrado, verifica hash da senha
5. Se senha incorreta, retorna mesmo erro generico
6. Se conta bloqueada, retorna erro de conta bloqueada
7. Se tudo valido, cria sessao no banco e emite access token + refresh token
8. Retorna 200 com tokens

**Fluxo: Gestao de papeis (Admin)**
1. Admin autenticado envia PATCH /v1/users/:id/roles com papeis
2. Middlewares de autenticacao e RBAC executam
3. Use case valida que ao menos 1 papel restara apos a operacao
4. Use case atualiza papeis do usuario
5. Retorna 200 com dados atualizados

**Fluxo: Bloqueio/desbloqueio (Admin)**
1. Admin autenticado envia PATCH /v1/users/:id/status com nova situacao
2. Middlewares de autenticacao e RBAC executam
3. Use case atualiza situacao do usuario
4. Se bloqueio: use case invalida todas as sessoes ativas do usuario
5. Retorna 200 com dados atualizados

**Fluxo: Isolamento de dados (Aluno)**
1. Aluno autenticado faz GET em recurso proprio (ex: suas matriculas)
2. Middlewares de autenticacao e RBAC executam
3. Use case aplica filtro ACL: so retorna dados do proprio usuario
4. Se tentativa de acesso via URL a recurso de outro aluno, retorna 403

**Fluxos alternativos e excecoes**
- Email duplicado no cadastro: rejeita com 409 Conflict
- Senha fora da politica: rejeita com 422 e lista de criterios nao atendidos
- Nenhum papel selecionado: rejeita com 422
- Tentativa de remover ultimo papel: rejeita com 422
- Token expirado: retorna 401, cliente usa refresh token
- Refresh token invalido/expirado: retorna 401, cliente precisa fazer login novamente
- Sessao invalidada (bloqueio): retorna 401 na proxima requisicao

**Diagramas**

Diagrama 1: Cadastro de usuario (Admin)

```mermaid
sequenceDiagram
    participant Admin
    participant AuthMW as Middleware Auth
    participant RBACMW as Middleware RBAC
    participant Controller
    participant UseCase as CreateUser UC
    participant Repo as UserRepository
    participant DB as PostgreSQL

    Admin->>AuthMW: POST /v1/users (JWT)
    AuthMW->>DB: Verifica sessao ativa
    DB-->>AuthMW: Sessao valida
    AuthMW->>RBACMW: Usuario autenticado
    RBACMW->>RBACMW: Verifica papel "admin"
    RBACMW->>Controller: Autorizado
    Controller->>UseCase: execute(name, email, password, roles)
    UseCase->>UseCase: Valida campos (nome, email, senha, papeis)
    UseCase->>Repo: findByEmail(email)
    Repo->>DB: SELECT WHERE email = ?
    DB-->>Repo: null (nao existe)
    Repo-->>UseCase: null
    UseCase->>UseCase: Hash da senha (argon2)
    UseCase->>Repo: save(user)
    Repo->>DB: INSERT user + roles
    DB-->>Repo: OK
    Repo-->>UseCase: User criado
    UseCase-->>Controller: User (sem senha)
    Controller-->>Admin: 201 Created
```

Diagrama 2: Login

```mermaid
sequenceDiagram
    participant User as Usuario
    participant Controller
    participant UseCase as Login UC
    participant Repo as UserRepository
    participant SessionRepo as SessionRepository
    participant DB as PostgreSQL

    User->>Controller: POST /v1/auth/login (email, senha)
    Controller->>UseCase: execute(email, password)
    UseCase->>Repo: findByEmail(email)
    Repo->>DB: SELECT WHERE email = ?
    DB-->>Repo: User encontrado
    Repo-->>UseCase: User
    UseCase->>UseCase: Verifica hash da senha
    UseCase->>UseCase: Verifica situacao != blocked
    UseCase->>SessionRepo: create(userId)
    SessionRepo->>DB: INSERT session
    DB-->>SessionRepo: OK
    SessionRepo-->>UseCase: Session
    UseCase->>UseCase: Gera access token + refresh token
    UseCase-->>Controller: Tokens
    Controller-->>User: 200 OK (access_token, refresh_token)
```

Diagrama 3: Bloqueio com invalidacao de sessoes

```mermaid
sequenceDiagram
    participant Admin
    participant AuthMW as Middleware Auth
    participant RBACMW as Middleware RBAC
    participant Controller
    participant UseCase as BlockUser UC
    participant UserRepo as UserRepository
    participant SessionRepo as SessionRepository
    participant DB as PostgreSQL

    Admin->>AuthMW: PATCH /v1/users/:id/status (JWT)
    AuthMW->>DB: Verifica sessao ativa
    DB-->>AuthMW: Sessao valida
    AuthMW->>RBACMW: Usuario autenticado
    RBACMW->>RBACMW: Verifica papel "admin"
    RBACMW->>Controller: Autorizado
    Controller->>UseCase: execute(userId, "blocked")
    UseCase->>UserRepo: updateStatus(userId, "blocked")
    UserRepo->>DB: UPDATE user SET status = blocked
    DB-->>UserRepo: OK
    UseCase->>SessionRepo: invalidateAll(userId)
    SessionRepo->>DB: UPDATE sessions SET active = false
    DB-->>SessionRepo: OK
    UseCase-->>Controller: User atualizado
    Controller-->>Admin: 200 OK
```

Diagrama 4: Requisicao autenticada com isolamento (Aluno)

```mermaid
sequenceDiagram
    participant Aluno
    participant AuthMW as Middleware Auth
    participant RBACMW as Middleware RBAC
    participant Controller
    participant UseCase as GetEnrollments UC
    participant Repo as EnrollmentRepository
    participant DB as PostgreSQL

    Aluno->>AuthMW: GET /v1/enrollments (JWT)
    AuthMW->>DB: Verifica sessao ativa
    DB-->>AuthMW: Sessao valida
    AuthMW->>RBACMW: Usuario autenticado (userId)
    RBACMW->>RBACMW: Verifica papel "aluno"
    RBACMW->>Controller: Autorizado
    Controller->>UseCase: execute(userId)
    UseCase->>Repo: findByUserId(userId)
    Repo->>DB: SELECT WHERE user_id = userId
    DB-->>Repo: Matriculas do aluno
    Repo-->>UseCase: Enrollments[]
    UseCase-->>Controller: Enrollments[]
    Controller-->>Aluno: 200 OK
```

---

### 5. Contratos publicos (assinaturas, endpoints, headers, exemplos)

**POST /v1/auth/login**
- Tipo: http_endpoint
- Rota: POST /v1/auth/login
- Metodo: POST
- Autenticacao: Publica (sem autenticacao)
- Semantica de status:
  - 200 OK: login bem-sucedido, tokens emitidos
  - 401 Unauthorized: credenciais invalidas (mensagem generica)
  - 403 Forbidden: conta bloqueada

**Exemplo de requisicao**
```json
{
  "email": "usuario@example.com",
  "password": "Senh@F0rte1"
}
```

**Exemplo de resposta (200)**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "refresh_token": "dGhpcyBpcyBhIHJlZnJl...",
  "expires_in": 900
}
```

---

**POST /v1/auth/refresh**
- Tipo: http_endpoint
- Rota: POST /v1/auth/refresh
- Metodo: POST
- Autenticacao: Publica (usa refresh token no body)
- Semantica de status:
  - 200 OK: novo par de tokens emitido
  - 401 Unauthorized: refresh token invalido ou expirado

**Exemplo de requisicao**
```json
{
  "refresh_token": "dGhpcyBpcyBhIHJlZnJl..."
}
```

**Exemplo de resposta (200)**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "refresh_token": "bmV3IHJlZnJlc2ggdG9r...",
  "expires_in": 900
}
```

---

**POST /v1/auth/logout**
- Tipo: http_endpoint
- Rota: POST /v1/auth/logout
- Metodo: POST
- Autenticacao: Bearer token (qualquer papel)
- Semantica de status:
  - 204 No Content: sessao invalidada com sucesso
  - 401 Unauthorized: token invalido ou sessao ja invalidada
- Semantica de headers:
  - Authorization: Bearer <access_token> (obrigatorio)

**Exemplo de requisicao**
```
POST /v1/auth/logout
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

**Exemplo de resposta**
```
204 No Content
(sem body)
```

---

**POST /v1/users**
- Tipo: http_endpoint
- Rota: POST /v1/users
- Metodo: POST
- Autenticacao: Bearer token, papel: admin
- Semantica de status:
  - 201 Created: usuario criado com sucesso
  - 409 Conflict: email ja esta em uso
  - 422 Unprocessable Entity: erro de validacao (senha, papeis, nome, email)
  - 401 Unauthorized: nao autenticado
  - 403 Forbidden: nao autorizado (nao e admin)

**Exemplo de requisicao**
```json
{
  "name": "Maria Silva",
  "email": "maria@example.com",
  "password": "Senh@F0rte1",
  "roles": ["student", "professor"]
}
```

**Exemplo de resposta (201)**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Maria Silva",
  "email": "maria@example.com",
  "roles": ["student", "professor"],
  "status": "active",
  "created_at": "2026-03-27T10:00:00.000Z"
}
```

**Exemplo de resposta (422)**
```json
{
  "error": "VALIDATION_ERROR",
  "message": "Erro de validacao",
  "details": [
    "Senha deve conter ao menos 1 letra maiuscula",
    "Senha deve conter ao menos 1 simbolo"
  ]
}
```

---

**PATCH /v1/users/:id/roles**
- Tipo: http_endpoint
- Rota: PATCH /v1/users/:id/roles
- Metodo: PATCH
- Autenticacao: Bearer token, papel: admin
- Semantica de status:
  - 200 OK: papeis atualizados com sucesso
  - 404 Not Found: usuario nao encontrado
  - 422 Unprocessable Entity: lista vazia de papeis ou papel invalido
  - 401 Unauthorized: nao autenticado
  - 403 Forbidden: nao autorizado

**Exemplo de requisicao**
```json
{
  "roles": ["student", "professor", "tutor"]
}
```

**Exemplo de resposta (200)**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Maria Silva",
  "email": "maria@example.com",
  "roles": ["student", "professor", "tutor"],
  "status": "active",
  "updated_at": "2026-03-27T11:00:00.000Z"
}
```

---

**PATCH /v1/users/:id/status**
- Tipo: http_endpoint
- Rota: PATCH /v1/users/:id/status
- Metodo: PATCH
- Autenticacao: Bearer token, papel: admin
- Semantica de status:
  - 200 OK: situacao atualizada com sucesso
  - 404 Not Found: usuario nao encontrado
  - 401 Unauthorized: nao autenticado
  - 403 Forbidden: nao autorizado

**Exemplo de requisicao**
```json
{
  "status": "blocked"
}
```

**Exemplo de resposta (200)**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Maria Silva",
  "email": "maria@example.com",
  "roles": ["student", "professor"],
  "status": "blocked",
  "updated_at": "2026-03-27T12:00:00.000Z"
}
```

---

**GET /v1/users/:id**
- Tipo: http_endpoint
- Rota: GET /v1/users/:id
- Metodo: GET
- Autenticacao: Bearer token, papel: admin (qualquer usuario) ou proprio usuario (seus dados)
- Semantica de status:
  - 200 OK: dados do usuario retornados
  - 403 Forbidden: acesso negado (nao e admin e nao e o proprio usuario)
  - 404 Not Found: usuario nao encontrado
  - 401 Unauthorized: nao autenticado

**Exemplo de resposta (200)**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Maria Silva",
  "email": "maria@example.com",
  "roles": ["student", "professor"],
  "status": "active",
  "created_at": "2026-03-27T10:00:00.000Z",
  "updated_at": "2026-03-27T11:00:00.000Z"
}
```

---

**GET /v1/users**
- Tipo: http_endpoint
- Rota: GET /v1/users
- Metodo: GET
- Autenticacao: Bearer token, papel: admin
- Suporta paginacao: query params `page` (default 1) e `per_page` (default 20, max 100)
- Suporta filtros: query params `role`, `status`, `name` (busca parcial)
- Semantica de status:
  - 200 OK: lista de usuarios retornada
  - 401 Unauthorized: nao autenticado
  - 403 Forbidden: nao autorizado

**Exemplo de requisicao**
```
GET /v1/users?page=1&per_page=10&role=professor&status=active
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

**Exemplo de resposta (200)**
```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "name": "Maria Silva",
      "email": "maria@example.com",
      "roles": ["student", "professor"],
      "status": "active",
      "created_at": "2026-03-27T10:00:00.000Z",
      "updated_at": "2026-03-27T11:00:00.000Z"
    }
  ],
  "total": 1,
  "page": 1,
  "per_page": 10
}
```

---

**Headers comuns:**
- `Authorization: Bearer <access_token>` (endpoints autenticados)
- `Content-Type: application/json`
- Respostas incluem `Cache-Control: no-store` (dados sensiveis de usuario, sem cache)

**Limites (conforme HLD):**
- Rate limiting: 100 req/s autenticado, 20 req/s nao autenticado
- Payload maximo: 1 MB
- SLA: p95 < 200ms leitura, < 500ms escrita

---

### 6. Erros, excecoes e fallback

**Matriz de erros previstos e tratamentos**

| Condicao | HTTP Status | Tratamento |
|---|---|---|
| Email duplicado no cadastro | 409 Conflict | Mensagem: "Email ja esta em uso" |
| Senha fora da politica | 422 Unprocessable Entity | Lista de criterios nao atendidos |
| Nenhum papel selecionado | 422 Unprocessable Entity | Mensagem: "Ao menos um papel e obrigatorio" |
| Remocao do ultimo papel | 422 Unprocessable Entity | Mensagem: "Usuario deve ter ao menos um papel" |
| Nome vazio ou acima de 100 chars | 422 Unprocessable Entity | Mensagem de validacao especifica |
| Email formato invalido | 422 Unprocessable Entity | Mensagem de validacao especifica |
| Papel invalido (fora dos 4 aceitos) | 422 Unprocessable Entity | Mensagem: "Papel invalido" |
| Credenciais invalidas (login) | 401 Unauthorized | Mensagem generica (sem revelar se email existe) |
| Conta bloqueada (login) | 403 Forbidden | Mensagem: "Conta bloqueada" |
| Token JWT expirado | 401 Unauthorized | Cliente deve usar refresh token |
| Refresh token invalido/expirado | 401 Unauthorized | Cliente deve fazer login novamente |
| Sessao invalidada (bloqueio) | 401 Unauthorized | Proxima requisicao falha imediatamente |
| Acesso a recurso de outro usuario | 403 Forbidden | Mensagem: "Acesso negado" |
| Usuario nao encontrado | 404 Not Found | Mensagem: "Recurso nao encontrado" |

**Estrategias de resiliencia:**
- Timeout de 5s para queries ao banco (evitar conexoes penduradas)
- Connection pooling via PgBouncer (conforme HLD)
- Sem retry automatico para operacoes de escrita (idempotencia nao garantida)
- Retry com backoff para leitura em caso de falha transitoria do banco (max 2 retries)

**Politica de fallback:**
- Se o banco estiver indisponivel, todos os endpoints autenticados retornam 503 Service Unavailable (dependencia da validacao de sessao)
- Sem fallback de cache para dados de autenticacao (seguranca prioritaria)

**Invariantes:**
- Usuario sempre tem ao menos 1 papel
- Usuario sempre tem situacao (active ou blocked)
- Senha nunca e retornada em responses
- Senha sempre armazenada como hash argon2
- Bloqueio invalida sessoes imediatamente (zero delay)

---

### 7. Observabilidade

**Metricas**
- `auth.login.total` (counter): total de tentativas de login (tags: success/failure)
- `auth.login.blocked` (counter): tentativas de login em contas bloqueadas
- `auth.token.refresh.total` (counter): total de refresh token requests (tags: success/failure)
- `auth.session.active` (gauge): sessoes ativas no banco
- `users.created.total` (counter): usuarios criados
- `users.blocked.total` (counter): usuarios bloqueados
- `users.endpoint.duration` (histogram): latencia por endpoint (tags: method, route, status)

**Logs**
- Formato: JSON estruturado
- Campos padrao: `timestamp`, `level`, `request_id`, `user_id`, `module: "user"`, `action`, `message`
- Login bem-sucedido: `{ action: "login_success", user_id, roles }`
- Login falho: `{ action: "login_failure", reason: "invalid_credentials" | "blocked_account" }` (sem logar email para proteger PII)
- Usuario criado: `{ action: "user_created", target_user_id, roles, created_by }`
- Usuario bloqueado: `{ action: "user_blocked", target_user_id, sessions_invalidated: number, blocked_by }`
- Sessao invalidada: `{ action: "session_invalidated", target_user_id, reason: "block" | "logout" }`
- Acesso negado: `{ action: "access_denied", target_resource, reason }`

**Tracing**
- Spans principais:
  - `auth.middleware`: validacao JWT + consulta sessao
  - `rbac.middleware`: verificacao de papel
  - `usecase.{create_user|login|block_user|update_roles}`: logica de negocio
  - `repository.user.{find|save|update}`: operacoes no banco
  - `repository.session.{create|invalidate|find}`: operacoes de sessao
- Amostragem: 100% em dev, 10% em producao (ajustavel)
- Dados sensiveis: nunca incluir senha ou tokens em spans/logs

**Dashboards e alertas**
- Alerta: taxa de login falho > 50% em janela de 5 min (possivel ataque)
- Alerta: latencia p95 de `/v1/auth/login` > 500ms
- Alerta: sessoes ativas crescendo alem do esperado (possivel vazamento de sessao)

---

### 8. Dependencias e compatibilidade

| Componente | Versao minima | Observacoes |
|---|---|---|
| Node.js | 25.x | Conforme compose.yaml |
| Express.js | 5.x | Conforme CLAUDE.md |
| TypeScript | 6.x | ESM modules, strict mode |
| PostgreSQL | 17.x | Fonte unica de verdade |
| TypeORM | latest | Data Mapper pattern, conforme stack |
| InversifyJS | latest | Dependency injection, conforme stack |
| argon2 (npm) | 0.40+ | Binding nativo, hash de senha |
| jsonwebtoken (npm) | 9.x (hipotese) | Geracao e verificacao de JWT |
| PgBouncer | 1.x | Connection pooling (conforme HLD) |
| OpenTelemetry SDK | 1.x | Tracing distribuido (ADR-007) |

**Garantias de compatibilidade**
- Modulo User nao depende de nenhum outro modulo (modulo raiz)
- Outros modulos dependem do User para autenticacao/autorizacao via middleware compartilhado
- Contratos de middleware (autenticacao e RBAC) sao a interface publica para os demais modulos
- Versionamento de API via prefixo `/v1/`
- Tabelas de User e Session sao exclusivas deste modulo (sem acesso direto por outros modulos)

---

### 9. Criterios de aceite tecnicos

1. Usuario criado com nome, email (unico), senha (hash argon2) e ao menos 1 papel, situacao "active"
2. Tentativa de cadastro com email duplicado retorna 409
3. Tentativa de cadastro com senha fora da politica retorna 422 com lista de criterios nao atendidos
4. Tentativa de cadastro sem papel retorna 422
5. Login com credenciais validas e conta ativa retorna access token (15 min) + refresh token (7 dias)
6. Login com credenciais invalidas retorna 401 com mensagem generica (nao revela existencia do email)
7. Login com conta bloqueada retorna 403
8. Refresh token valido emite novo par de tokens
9. Refresh token expirado/invalido retorna 401
10. Logout invalida sessao no banco, proxima requisicao com mesmo token retorna 401
11. Admin consegue adicionar e remover papeis de um usuario
12. Tentativa de remover o ultimo papel retorna 422
13. Bloqueio de usuario invalida todas as sessoes ativas imediatamente
14. Desbloqueio de usuario restaura capacidade de login
15. Aluno acessando recurso proprio recebe 200; acessando recurso de outro aluno recebe 403
16. Senha nunca aparece em responses, logs ou spans
17. Todos os endpoints de leitura com p95 < 200ms
18. Todos os endpoints de escrita com p95 < 500ms
19. Spans de tracing presentes em todas as camadas (middleware, use case, repository)
20. Logs estruturados em JSON com request_id, user_id, module e action em todas as operacoes

---

### 10. Riscos e mitigacao

### Consulta ao banco a cada requisicao autenticada (validacao de sessao)

- **Probabilidade:** media
- **Impacto:** Aumento de latencia em todos os endpoints autenticados sob alta carga
- **Mitigacao:**
    - Query leve por ID de sessao com indice dedicado
    - Connection pooling via PgBouncer
    - Monitorar latencia do span `auth.middleware` via tracing
- **Plano de contingencia:** Introduzir cache de sessao em memoria (ex: Redis) com TTL curto (30s), aceitando janela de delay no bloqueio

### Ataques de forca bruta no endpoint de login

- **Probabilidade:** alta
- **Impacto:** Comprometimento de contas, sobrecarga no banco (hashing argon2 e CPU-intensivo)
- **Mitigacao:**
    - Rate limiting de 20 req/s para endpoints nao autenticados (conforme HLD)
    - Mensagem generica no login (nao revela se email existe)
    - Logs de tentativas falhas com alerta em taxa > 50% em 5 min
- **Plano de contingencia:** Implementar bloqueio temporario por IP ou por conta apos N tentativas falhas

### Vazamento de sessoes (sessoes orfas no banco)

- **Probabilidade:** media
- **Impacto:** Crescimento da tabela de sessoes, degradacao de queries
- **Mitigacao:**
    - TTL na sessao igual ao refresh token (7 dias)
    - Job periodico para limpar sessoes expiradas
    - Metrica `auth.session.active` com alerta de crescimento anomalo
- **Plano de contingencia:** Truncar sessoes expiradas manualmente; adicionar indice parcial em sessoes ativas

### Binding nativo do argon2 causa problemas em diferentes ambientes

- **Probabilidade:** baixa
- **Impacto:** Falha no build ou deploy em ambientes sem dependencias nativas
- **Mitigacao:**
    - Docker como ambiente padrao (dependencias controladas)
    - Testar build no CI com a mesma imagem Docker de producao
- **Plano de contingencia:** Fallback para bcrypt (puro JS, sem binding nativo)
