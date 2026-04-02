### HLD: CodeLearn Platform

Versão: 1.0
Data: 2026-03-26
Responsável: Luiz Carlos

---

### Objetivo técnico

Construir do zero (greenfield) uma plataforma de cursos online (CodeLearn Platform) que suporte criação, gestão e consumo de conteúdo educacional. O sistema deve resolver os seguintes desafios técnicos:

- Upload de mídia pesada (vídeo/áudio até 1 GB) com streaming eficiente
- Escalabilidade para grande volume de alunos simultâneos
- Controle de acesso granular por papel (aluno, professor, tutor, admin) com autorização contextual
- Cálculo em tempo real de progresso, notas e desbloqueio de módulos

Dependências com outros sistemas
- AWS S3 para armazenamento de mídia em produção

---

### Arquitetura geral

Monolito modular com organização interna seguindo arquitetura hexagonal (ports & adapters). APIs REST como interface de comunicação. Deploy inicial em Docker Compose local, com migração para cloud planejada para o futuro.

Ambiente de implantação
- Docker Compose local (fase inicial)
- Migração para cloud a definir

Tecnologias principais
- Express.js com TypeScript (backend)
- PostgreSQL (banco de dados relacional)
- AWS S3 (armazenamento de mídia em produção)
- OpenTelemetry (tracing distribuído)

Padrões adotados
- REST para APIs externas
- Arquitetura hexagonal (ports & adapters) para organização interna dos módulos

---

### Componentes e responsabilidades

| Componente | Responsabilidades | Dependências |
| ----------- | ----------------- | ------------ |
| User | Cadastro, autenticação (JWT com sessão no banco), autorização (RBAC + ABAC + ACL), gestão de papéis e bloqueio | Nenhuma (módulo raiz) |
| Course | CRUD de cursos, módulos e aulas; publicação e despublicação de conteúdo; validação de pré-requisitos | User (professor criador) |
| Media | Upload, validação e streaming de arquivos de vídeo e áudio; integração com disco local (dev) e S3 (produção) | Course (aulas com mídia), AWS S3 |
| Quiz | CRUD de quizzes, questões e opções; correção automática; controle de tentativas e reabertura por tutor | Course (vinculado a módulo) |
| Enrollment | Matrícula, expiração, renovação, carência; validação de pré-requisitos e duplicidade | User (aluno), Course (curso publicado) |
| Progress | Tracking de progresso por aula, módulo e curso; desbloqueio de módulos; marcação de conclusão | Enrollment (matrícula ativa), Course (aulas/módulos), Quiz (aprovação) |
| LearningPath | CRUD de trilhas de aprendizado; ordenação de cursos; cálculo de progresso da trilha | Course (cursos agrupados), Enrollment (verificação de conclusão) |
| Certificate | Emissão automática de certificados de curso e trilha | Enrollment (conclusão), Progress (100%), Quiz (aprovação), LearningPath (conclusão) |
| Metrics | Métricas do professor (taxa de conclusão, evasão por módulo, nota média) e métricas globais do admin | Enrollment, Progress, Quiz |

---

### Fluxo de requisições e de dados

**Fluxo de requisição (ex: aluno acessa uma aula)**
1. Cliente envia request HTTP para Express.js
2. Middleware de autenticação valida token JWT com verificação server-side (sessão no banco)
3. Middleware de autorização verifica papel (RBAC), atributos contextuais (ABAC) e permissões específicas (ACL)
4. Router direciona para o controller do módulo correto
5. Controller chama o use case (camada de aplicação hexagonal)
6. Use case aplica regras de negócio (verifica desbloqueio do módulo, matrícula ativa, período de carência)
7. Use case chama o repositório (port/adapter) para persistir ou consultar no PostgreSQL
8. Resposta retorna pelo mesmo caminho com HTTP caching headers (Cache-Control, ETag)

**Fluxo de dados (upload de mídia)**
1. Cliente envia arquivo via multipart/form-data
2. Middleware valida tamanho (até 1 GB) e tipo (vídeo/áudio)
3. Módulo Media faz upload para disco local (dev) ou S3 (produção)
4. Referência do arquivo é salva no PostgreSQL vinculada à aula
5. Streaming é servido via endpoint dedicado (dev) ou URL pré-assinada do S3 (produção)

---

### Modelo de dados (alto nível)

Entidades principais
- User (nome, email, senha, papéis, situação)
- Course (título, descrição, situação, período de acesso, período de carência, professor criador)
- Module (título, ordem no curso)
- Lesson (título, tipo de conteúdo, conteúdo texto, arquivo de mídia, duração estimada)
- Quiz (nota mínima, máximo de tentativas)
- Question (enunciado, ordem no quiz)
- Option (texto, indicador de correta)
- Enrollment (situação, data de expiração, data de conclusão)
- Progress (data de início, data de conclusão por aula)
- QuizAttempt (nota obtida, respostas selecionadas, data da tentativa)
- CourseTutor (vínculo tutor-curso)
- CoursePrerequisite (pré-requisitos entre cursos)
- LearningPath (título, descrição)
- LearningPathCourse (ordem do curso na trilha)
- Certificate (tipo: curso ou trilha, data de emissão)

Relações
- User (professor) 1:N Course
- Course 1:N Module (ordenados)
- Module 1:N Lesson
- Module 0..1 Quiz
- Quiz 1:N Question (ordenadas)
- Question 1:N Option (mínimo 4, exatamente 1 correta)
- User (aluno) N:N Course via Enrollment
- Enrollment 1:N Progress (por aula)
- Quiz 1:N QuizAttempt (por aluno)
- User (tutor) N:N Course via CourseTutor
- Course N:N Course via CoursePrerequisite
- LearningPath N:N Course via LearningPathCourse (ordenados)
- Certificate N:1 Enrollment ou LearningPath

Fonte de verdade
- PostgreSQL como fonte única de verdade para todos os dados
- Soft delete: dados nunca excluídos, apenas despublicados (preservação de histórico)

---

### Interfaces públicas

| Nome | Tipo | Protocolo | Exposição | SLAs/Limites |
| ---- | ---- | ---------- | --------- | ------------- |
| Auth API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| Users API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| Courses API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| Media API | API | REST | Externa | Timeout 10 min (upload), 1 GB max |
| Quizzes API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| Enrollments API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| Progress API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| LearningPaths API | API | REST | Externa | p95 < 200ms leitura, < 500ms escrita |
| Certificates API | API | REST | Externa | p95 < 200ms leitura |
| Metrics API | API | REST | Externa | p95 < 200ms leitura |

Rate limiting global: 100 req/s por usuário autenticado, 20 req/s para não autenticados. Payload máximo: 1 GB (mídia), 1 MB (demais endpoints).

---

### Considerações de escalabilidade e disponibilidade

Abordagem geral
- Monolito modular com boundaries claros permite evolução gradual
- Docker Compose local na fase inicial, preparado para scaling horizontal em cloud

Técnicas aplicadas
- Scaling horizontal da aplicação (múltiplas instâncias atrás de load balancer, planejado)
- Connection pooling no PostgreSQL via PgBouncer
- Rate limiting na camada de aplicação (100 req/s autenticado, 20 req/s não autenticado)
- HTTP caching (Cache-Control, ETag) como estratégia inicial de cache

Meta de disponibilidade
- 99.5% uptime mensal (~43h de downtime por ano) na fase inicial

---

### Segurança

Autenticação
- JWT com validação server-side (sessão armazenada no banco de dados)
- Access token + refresh token emitidos pela própria aplicação

Autorização
- RBAC: validação de papéis (aluno, professor, tutor, admin)
- ABAC: verificação de atributos contextuais (ex: professor só gerencia seus cursos, tutor só acessa cursos vinculados)
- ACL: controle de acesso a recursos específicos (ex: aluno só acessa seus dados e matrículas)

Proteção de dados
- TLS em trânsito (HTTPS) para todas as comunicações
- Sem criptografia em repouso nesta fase
- Senhas com hash bcrypt/argon2
- PII (nome, email) sem anonimização nesta fase

Gestão de segredos
- Variáveis de ambiente (.env) na fase inicial
- Migração para vault (ex: HashiCorp Vault, AWS Secrets Manager) planejada

---

### Observabilidade

Logs
- Logs estruturados em JSON com campos padronizados: request ID, user ID, módulo, ação, timestamp
- Correlação de logs via request ID propagado em toda a cadeia

Métricas
- Métricas de aplicação: latência por endpoint, taxa de erro, uso de memória e CPU
- Métricas de negócio: matrículas por dia, uploads por dia, quizzes realizados

Tracing
- Tracing distribuído com OpenTelemetry desde o início
- Spans por camada: middleware, controller, use case, repository

Dashboards e alertas
- Sem dashboards ou alertas configurados nesta fase, apenas logs e traces

---

### Riscos arquiteturais e mitigação

#### Upload de arquivos grandes sobrecarregando o servidor
- **Probabilidade:** Alta
- **Impacto:** Degradação de performance para todos os usuários durante uploads simultâneos
- **Mitigação:**
  - Limitar uploads concorrentes por usuário
  - Migração para S3 com upload direto do cliente (presigned URL) em produção
- **Plano de contingência:** Rate limiting agressivo no endpoint de upload

#### Acoplamento crescente entre módulos do monolito
- **Probabilidade:** Média
- **Impacto:** Dificuldade de manutenção e evolução a médio prazo
- **Mitigação:**
  - Arquitetura hexagonal com boundaries claros entre módulos
  - Code reviews focados em dependências entre módulos
- **Plano de contingência:** Extração de módulos críticos para microsserviços

#### PostgreSQL como ponto único de falha
- **Probabilidade:** Média
- **Impacto:** Indisponibilidade total do sistema
- **Mitigação:**
  - Backups automatizados e periódicos
  - Connection pooling com PgBouncer
- **Plano de contingência:** Restore de backup; futura adoção de réplica de leitura

#### Cálculos de progresso e notas sob alta carga
- **Probabilidade:** Média
- **Impacto:** Latência elevada em endpoints de progresso e desbloqueio de módulos
- **Mitigação:**
  - Queries otimizadas com índices adequados
  - HTTP caching para respostas de leitura frequente
- **Plano de contingência:** Introdução de cache (Redis) ou materialização de progresso em tabela dedicada

---

### ADRs e próximos passos

ADRs associados
- ADR-001: Adoção de monolito modular com arquitetura hexagonal
- ADR-002: Express.js + TypeScript como stack backend
- ADR-003: PostgreSQL como banco de dados único
- ADR-004: JWT com validação server-side para autenticação
- ADR-005: Combinação de RBAC + ABAC + ACL para autorização
- ADR-006: AWS S3 para armazenamento de mídia em produção
- ADR-007: OpenTelemetry para tracing distribuído
- ADR-008: HTTP caching como estratégia inicial de cache

Decisões pendentes
- Estratégia de upload em chunks (ainda não definida)
- Escolha de biblioteca para OpenTelemetry no Express.js
- Definição de infraestrutura cloud (provedor e serviços)
- Definição de política de backups do PostgreSQL

Próximos passos
- Elaborar FDD/LLD por módulo
- Definir schema detalhado do banco de dados
- Implementar POC do módulo de autenticação/autorização
- Definir contrato de API (OpenAPI/Swagger)
