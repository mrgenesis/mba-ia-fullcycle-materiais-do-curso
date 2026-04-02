# Dev Workflow com IA — CodeLearn Platform

> Aula do **MBA de Engenharia de Software de IA** da [FullCycle](https://fullcycle.com.br)

Aula sobre como usar **agentes de IA** (Claude Code + VS Code Copilot Agent) para conduzir o ciclo completo de engenharia de software — da concepção de requisitos até o plano de implementação task-a-task — sem escrever documentação manualmente.

O projeto utilizado como exemplo é a **CodeLearn Platform**, um sistema de gestão de aprendizagem (LMS) greenfield.

---

## Sobre o Projeto

A **CodeLearn Platform** é um LMS (Learning Management System) construído como uma aplicação **monolítica modular** com **arquitetura hexagonal** (ports & adapters).

- **4 personas**: Aluno, Professor, Tutor, Admin
- **15+ entidades**: User, Course, Module, Lesson, Quiz, Enrollment, Progress, Certificate, etc.
- **24 regras de negócio**: desbloqueio sequencial de módulos, correção automática de quizzes, pré-requisitos de curso, emissão automática de certificados, isolamento de dados por papel, entre outras
- **3 fases de release**: MVP → Enhancement → Optimization

Detalhes completos no [docs/domain-context.md](docs/domain-context.md).

---

## Workflow da Aula com IA

O ponto central da aula é demonstrar como **skills customizados** do Claude Code conduzem cada etapa do ciclo de engenharia de software — desde um documento de contexto de domínio até um plano de implementação detalhado com tasks ordenadas por dependência.

### Fluxo executado

```
Domain Context → PRD → HLD → Épicos → FDD → Plano de Implementação
```

| Sessão | O que foi feito | Skill utilizado |
|--------|----------------|-----------------|
| [01](/__claude-sessions/01-criando-prd.txt) | Criação do PRD a partir do domain context | `/product-manager` |
| [02](/__claude-sessions/02-quebrando-prd-em-varios-arquivod.txt) | Quebra do PRD monolítico em múltiplos arquivos | — (prompt direto) |
| [03](/__claude-sessions/03-criando-hld-document.txt) | Criação do High-Level Design (arquitetura, componentes, fluxos, ADRs) | `/hld-creator` |
| [04](/__claude-sessions/04-fdd-01-gestao-usuarios-auth.txt) | Criação do FDD-001: Gestão de Usuários e Autenticação | `/fdd-creator` |
| [05](/__claude-sessions/05-plano-final-auth.txt) | Plano de implementação com 33 tasks em 7 fases (~16 dias-dev) | `/implementation-plan-creator` |

Cada sessão está salva em `__claude-sessions/` com o log completo da interação.

---

## Skills e Agentes Customizados

Os skills ficam em [.claude/skills/](.claude/skills/) e funcionam como **agentes especializados** que o Claude Code invoca via comando `/nome-do-skill`. Cada um conduz uma entrevista estruturada e gera um artefato padronizado.

| Skill | Descrição | Artefato gerado |
|-------|-----------|-----------------|
| `product-manager` | Gera PRDs e especificações técnicas com requisitos funcionais/não-funcionais, épicos e user stories | PRD em Markdown |
| `hld-creator` | Projeta a arquitetura do sistema via entrevista de 11 etapas (componentes, fluxos, segurança, ADRs) | HLD em Markdown |
| `fdd-creator` | Cria Feature Design Documents focados no "como implementar" — contratos, fluxos, erros, observabilidade | FDD em Markdown |
| `implementation-plan-creator` | Quebra FDDs em tasks concretas ordenadas por dependência técnica com estimativas e critérios de aceite | Plano de implementação |
| `generate-test-guide` | Analisa o tech stack do projeto e gera um guia de testes multi-arquivo como skill permanente | Skill de testes |
| `testing-guide-dev-workflow-test` | Guia de testes permanente do CodeLearn — consultado sempre que se escreve ou revisa testes | Referência contínua |

---

## Regras de Código

As regras em [.claude/rules/](.claude/rules/) são aplicadas automaticamente pelo agente ao gerar ou revisar código. Garantem consistência com as decisões arquiteturais do projeto.

| Regra | Escopo | O que define |
|-------|--------|-------------|
| `coding.md` | `**/*.ts` | Naming conventions (camelCase, PascalCase, kebab-case), estilo TypeScript, imports ESM |
| `controllers.md` | `src/*/controllers/**` | Controllers como funções (não classes), fluxo extract → use case → response, propagação de erros via `next()` |
| `error-handler.md` | `src/shared/middleware/` | Middleware global de erros: mapeamento de classes de erro → HTTP status codes |
| `hexagonal-layers.md` | `src/*/domain/**`, `src/*/services/**`, etc. | Regras de dependência entre camadas: domínio nunca depende de infraestrutura |
| `testing.md` | `**/*.test.ts`, `**/*.spec.ts` | Padrão AAA (Arrange-Act-Assert), posicionamento por camada, cleanup de banco com TRUNCATE |
| `typeorm.md` | `src/*/models/**` | Dual ID (integer + UUID v4), soft delete, Data Mapper, naming de tabelas |

---

## Documentação Gerada

Toda a documentação abaixo foi gerada pelos skills durante a aula e está em `docs/`:

| Documento | Descrição |
|-----------|-----------|
| [PRD — Index](docs/prd/index.md) | Visão geral do produto, personas, escopo, priorização |
| [Requisitos Funcionais](docs/prd/requisitos-funcionais/) | 9 módulos com requisitos detalhados (gestão de usuários, cursos, matrículas, etc.) |
| [Requisitos Não-Funcionais](docs/prd/requisitos-nao-funcionais.md) | Performance, segurança, disponibilidade, observabilidade |
| [HLD](docs/hld.md) | Arquitetura, componentes, fluxos, modelo de dados, ADRs, riscos |
| [Domain Context](docs/domain-context.md) | Personas, entidades, regras de negócio |
| [Roadmap](docs/roadmap.md) | 3 fases de release (MVP, Enhancement, Optimization) |
| [Épicos](docs/epicos/) | 8 épicos com user stories e critérios de aceite |
| [FDD-001](docs/fdds/fdd-001-gestao-usuarios-autenticacao.md) | Feature Design: Gestão de Usuários e Autenticação |
| [Rastreabilidade](docs/rastreabilidade.md) | Mapeamento regras de negócio → features → fases |
| [Riscos](docs/riscos.md) | 10 riscos identificados com estratégias de mitigação |

---

## Tech Stack

| Camada | Tecnologia |
|--------|-----------|
| Runtime | Node.js 25 (ES Modules) |
| Linguagem | TypeScript 6 (strict mode, target ES2022) |
| Framework | Express.js 5 |
| Banco de Dados | PostgreSQL 17 |
| ORM | TypeORM 0.3 (Data Mapper) |
| Testes | Vitest |
| DI Container | InversifyJS |
| Autenticação | JWT + sessão server-side |
| Dev Tooling | tsx (watch mode), Docker Compose |

---

## Como Rodar

### Pré-requisitos

- [Docker](https://docs.docker.com/get-docker/) e Docker Compose

### Subir a infraestrutura

```bash
docker compose up -d
```

Verificar se o banco está pronto:

```bash
docker compose exec db pg_isready -U lms
# Esperado: accepting connections
```

### Comandos disponíveis

```bash
npm run start:dev       # Dev server com hot reload (tsx watch)
npm run build           # Compilar TypeScript → dist/
npm run start:prod      # Rodar build compilado
npm test                # Rodar testes uma vez
npm run test:watch      # Testes em watch mode
```

### Credenciais do PostgreSQL

| Campo | Valor |
|-------|-------|
| Host | `localhost` |
| Porta | `5432` |
| Usuário | `lms` |
| Senha | `lms` |
| Database | `lms` |

---

## Estrutura do Projeto

```
src/
  /<module-name>
    index.ts                    # entry point do módulo
    /domain
      /entities                 # entidades e value objects
      /services                 # domain services (lógica stateless)
      /ports
        /driven                 # ports de entrada (controllers)
        /driving                # ports de saída (repositories)
    /controllers                # Express route handlers
    /models                     # entidades TypeORM
    /repositories               # implementações dos ports (adapters)
    /services                   # use cases (application services)
    /container                  # setup de DI do módulo
  /shared
    /utils                      # utilitários
    /errors                     # classes de erro customizadas
    /middleware                  # middleware Express (auth, error handling)
    /config                     # configuração e variáveis de ambiente
    /migrations                 # migrations do banco
    container.ts                # DI container global
  app.ts                        # setup do Express (middleware, rotas)
  server.ts                     # entry point (listen porta 3000)
docs/                           # documentação gerada pelos skills
.claude/
  /skills                       # skills customizados do Claude Code
  /rules                        # regras de código para o agente
```

---

## Licença

Projeto educacional desenvolvido para o **MBA de Engenharia de Software de IA** — [FullCycle](https://fullcycle.com.br).
