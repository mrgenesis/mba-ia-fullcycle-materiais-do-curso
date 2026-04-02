# Requisitos Funcionais: Autorizacao e Controle de Acesso (Authorization)

> Detalhamento dos requisitos FR-034 a FR-038.
> Documento principal: [PRD](../index.md)

---

## FR-034: Controle de Acesso Contextual do Professor [MUST]

**Descricao:** O sistema deve garantir que o Professor so consiga gerenciar (criar, editar, publicar, despublicar) cursos, modulos, aulas e quizzes que ele mesmo criou.

**Criterios de Aceitacao:**
1. O professor consegue listar, visualizar e editar apenas os cursos nos quais e o criador.
2. O professor nao consegue acessar ou modificar cursos, modulos, aulas ou quizzes de outros professores.
3. O sistema retorna erro de autorizacao quando o professor tenta acessar recursos de outro professor.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-017
**Epico Relacionado:** EPIC-001

---

## FR-035: Controle de Acesso Contextual do Tutor [MUST]

**Descricao:** O sistema deve garantir que o Tutor so consiga visualizar dados de alunos e realizar acoes de suporte nos cursos aos quais esta vinculado.

**Criterios de Aceitacao:**
1. O tutor consegue visualizar dados de alunos apenas nos cursos aos quais esta vinculado.
2. O tutor consegue reabrir tentativas de quiz apenas para alunos de cursos aos quais esta vinculado.
3. O sistema retorna erro de autorizacao quando o tutor tenta acessar dados de cursos nao vinculados.
4. Apos desvinculacao, o acesso do tutor e removido imediatamente.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-018, RN-024
**Epico Relacionado:** EPIC-008

---

## FR-036: Controle de Acesso do Aluno [MUST]

**Descricao:** O sistema deve garantir que o Aluno so consiga acessar seus proprios dados, matriculas e progresso, e so visualize cursos publicados.

**Criterios de Aceitacao:**
1. O aluno consegue acessar apenas seus proprios dados pessoais, matriculas, progresso e certificados.
2. O aluno nao consegue acessar dados de outros alunos.
3. O aluno so visualiza cursos com situacao "publicado" na listagem e busca.
4. O sistema retorna erro de autorizacao quando o aluno tenta acessar dados de outro usuario.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-019
**Epico Relacionado:** EPIC-001

---

## FR-037: Acesso Irrestrito do Admin [MUST]

**Descricao:** O sistema deve garantir que o Admin consiga visualizar e editar qualquer recurso da plataforma sem restricoes de contexto.

**Criterios de Aceitacao:**
1. O Admin consegue visualizar e editar qualquer curso, modulo, aula, quiz, matricula e usuario da plataforma.
2. O Admin consegue acessar metricas de qualquer curso ou professor.
3. O Admin consegue realizar qualquer acao que outros papeis podem realizar (criar cursos, vincular tutores, reabrir tentativas etc.).

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-017, RN-018, RN-023
**Epico Relacionado:** EPIC-001

---

## FR-038: Listagem e Busca de Cursos pelo Aluno [SHOULD]

**Descricao:** O Aluno deve poder listar e buscar cursos publicados disponiveis para matricula.

**Criterios de Aceitacao:**
1. O aluno visualiza apenas cursos com situacao "publicado" na listagem.
2. O aluno consegue buscar cursos por titulo ou descricao.
3. Os resultados exibem informacoes basicas do curso (titulo, descricao, periodo de acesso).
4. Cursos nos quais o aluno ja possui matricula ativa sao identificados claramente na listagem.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-013, RN-019
**Epico Relacionado:** EPIC-005

---

## Matriz de Rastreabilidade: Regras de Negocio x Requisitos Funcionais

| Regra de Negocio | Requisitos Funcionais Relacionados |
|---|---|
| RN-001 | FR-008, FR-020, FR-027 |
| RN-002 | FR-012 |
| RN-003 | FR-011, FR-013 |
| RN-004 | FR-015, FR-016, FR-017 |
| RN-005 | FR-015, FR-017, FR-018 |
| RN-006 | FR-019 |
| RN-007 | FR-026 |
| RN-008 | FR-029 |
| RN-009 | FR-025, FR-027 |
| RN-010 | FR-020, FR-023 |
| RN-011 | FR-023 |
| RN-012 | FR-024 |
| RN-013 | FR-020, FR-022, FR-038 |
| RN-014 | FR-021, FR-028 |
| RN-015 | FR-014 |
| RN-016 | FR-032, FR-033 |
| RN-017 | FR-005, FR-006, FR-010, FR-015, FR-032, FR-034, FR-037 |
| RN-018 | FR-018, FR-030, FR-031, FR-035, FR-037 |
| RN-019 | FR-002, FR-036, FR-038 |
| RN-020 | FR-007 |
| RN-021 | FR-002, FR-004, FR-006 |
| RN-022 | FR-007, FR-009, FR-014, FR-022 |
| RN-023 | FR-001, FR-003, FR-004, FR-037 |
| RN-024 | FR-018, FR-030, FR-035 |
| Validacoes Transversais | FR-001, FR-010, FR-016, FR-025 |
