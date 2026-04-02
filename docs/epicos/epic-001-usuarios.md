# EPIC-001: Gestao de Usuarios e Autenticacao

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Garantir acesso seguro a plataforma com controle granular de papeis, permitindo que cada usuario tenha permissoes adequadas ao seu contexto de atuacao.
**Segmentos de Usuario:** Admin, Aluno, Professor, Tutor
**Metricas de Sucesso:**
- 100% dos usuarios criados com ao menos um papel valido atribuido
- Zero acessos nao autorizados a funcionalidades fora do papel do usuario

**Requisitos Relacionados:** FR-001, FR-002, FR-003, FR-004, FR-034, FR-036, FR-037

---

## STORY-001: Cadastro de novo usuario na plataforma

> Como um **Admin**, eu quero cadastrar novos usuarios com nome, email, senha e papeis, para que eles possam acessar a plataforma com as permissoes adequadas.

**Criterios de Aceitacao:**
1. **Given** o Admin esta autenticado, **When** ele preenche nome (ate 100 caracteres), email (unico, formato valido), senha (conforme politica) e seleciona ao menos um papel, **Then** o usuario e criado com situacao "ativo" e papeis atribuidos (RN-023).
2. **Given** o email ja esta em uso, **When** o Admin tenta cadastrar, **Then** o sistema rejeita com mensagem de erro especifica.
3. **Given** a senha nao atende aos criterios, **When** o Admin tenta cadastrar, **Then** o sistema exibe quais criterios nao foram atendidos.
4. **Given** nenhum papel e selecionado, **When** o Admin tenta cadastrar, **Then** o sistema impede o cadastro.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-002: Gestao de multiplos papeis simultaneos

> Como um **Admin**, eu quero atribuir ou remover multiplos papeis de um usuario existente, para que ele possa acumular funcoes na plataforma (RN-023).

**Criterios de Aceitacao:**
1. **Given** o Admin edita um usuario ativo, **When** ele adiciona "professor" a quem ja e "aluno", **Then** o usuario passa a ter ambos os papeis.
2. **Given** o Admin tenta remover o unico papel restante, **When** ele confirma, **Then** o sistema impede a remocao.
3. **Given** o Admin atribui "tutor", **When** o usuario faz login, **Then** ele ve opcoes de menu correspondentes aos seus papeis.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-003: Bloqueio e desbloqueio de contas de usuario

> Como um **Admin**, eu quero bloquear e desbloquear usuarios, para restringir o acesso sem perder dados historicos.

**Criterios de Aceitacao:**
1. **Given** o Admin seleciona um usuario ativo, **When** ele bloqueia, **Then** o usuario nao consegue mais fazer login.
2. **Given** um Professor esta bloqueado (RN-021), **When** um Aluno acessa um curso publicado desse Professor, **Then** o curso continua acessivel.
3. **Given** um Professor esta bloqueado (RN-021), **When** ele tenta editar ou criar cursos, **Then** o sistema impede.
4. **Given** o Admin desbloqueia um usuario, **When** o usuario tenta login, **Then** o acesso e restaurado.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-004: Autenticacao de usuario na plataforma

> Como um **Usuario** (qualquer papel), eu quero fazer login com email e senha, para acessar as funcionalidades de acordo com meus papeis.

**Criterios de Aceitacao:**
1. **Given** conta ativa, **When** credenciais corretas, **Then** autentica e direciona ao painel.
2. **Given** conta bloqueada, **When** credenciais corretas, **Then** exibe mensagem de conta bloqueada.
3. **Given** credenciais invalidas, **When** tenta login, **Then** exibe mensagem generica sem revelar se email existe.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-005: Isolamento de dados por papel e contexto

> Como um **Aluno**, eu quero que meus dados de matricula e progresso sejam visiveis apenas para mim (RN-019).

**Criterios de Aceitacao:**
1. **Given** Aluno autenticado, **When** acessa matriculas, **Then** ve apenas suas proprias.
2. **Given** Aluno autenticado, **When** tenta acessar via URL matricula de outro, **Then** erro de acesso negado.
3. **Given** Aluno autenticado, **When** navega, **Then** ve apenas cursos publicados.

**Prioridade:** MUST | **Story Points:** 3
