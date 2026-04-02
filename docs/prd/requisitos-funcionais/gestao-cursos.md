# Requisitos Funcionais: Gestao de Cursos (Course Management)

> Detalhamento dos requisitos FR-005 a FR-009.
> Documento principal: [PRD](../index.md)

---

## FR-005: Criacao de Curso [MUST]

**Descricao:** O Professor deve poder criar cursos informando titulo, descricao, periodo de acesso e periodo de carencia. O curso e criado em situacao "rascunho".

**Criterios de Aceitacao:**
1. O curso e criado com sucesso quando titulo (ate 150 caracteres), descricao (ate 2000 caracteres), periodo de acesso (inteiro positivo em dias) e periodo de carencia (inteiro positivo em dias) sao fornecidos.
2. O curso criado fica com situacao "rascunho" e com o professor autenticado como criador.
3. O sistema rejeita a criacao quando campos obrigatorios estao ausentes ou fora dos limites estabelecidos.
4. Apenas usuarios com papel de Professor ou Admin conseguem criar cursos.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-017
**Epico Relacionado:** EPIC-002

---

## FR-006: Edicao de Curso [MUST]

**Descricao:** O Professor criador (ou Admin) deve poder editar titulo, descricao, periodo de acesso e periodo de carencia de um curso.

**Criterios de Aceitacao:**
1. O professor criador consegue alterar qualquer campo editavel do curso e as alteracoes sao persistidas.
2. Um professor que nao e criador do curso nao consegue editar o curso.
3. O Admin consegue editar qualquer curso independentemente de quem o criou.
4. Um professor bloqueado nao consegue editar seus cursos.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-017, RN-021
**Epico Relacionado:** EPIC-002

---

## FR-007: Publicacao e Despublicacao de Curso [MUST]

**Descricao:** O Professor criador (ou Admin) deve poder publicar e despublicar cursos. A despublicacao deve respeitar o prazo de acesso garantido para alunos ja matriculados.

**Criterios de Aceitacao:**
1. O professor criador consegue alterar a situacao de um curso de "rascunho" para "publicado", tornando-o visivel para alunos.
2. O professor criador consegue despublicar um curso, impedindo imediatamente novas matriculas.
3. Alunos ja matriculados em um curso despublicado mantem acesso por ate 30 dias apos a despublicacao.
4. Apos 30 dias da despublicacao, o acesso dos alunos matriculados e bloqueado automaticamente.
5. Conteudo despublicado nao aparece em buscas ou listagens para alunos nao matriculados.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-020, RN-022
**Epico Relacionado:** EPIC-002

---

## FR-008: Definicao de Pre-requisitos de Curso [SHOULD]

**Descricao:** O Professor criador (ou Admin) deve poder definir outros cursos como pre-requisito para matricula, impedindo dependencias circulares.

**Criterios de Aceitacao:**
1. O professor criador consegue adicionar um ou mais cursos como pre-requisitos do seu curso.
2. O sistema rejeita a adicao de um pre-requisito que criaria uma dependencia circular (ex.: A exige B, B exige A).
3. O aluno nao consegue se matricular em um curso cujos pre-requisitos nao estejam todos com matricula concluida.
4. A remocao de um pre-requisito nao afeta alunos ja matriculados no curso.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-001
**Epico Relacionado:** EPIC-002

---

## FR-009: Exclusao Logica de Curso [MUST]

**Descricao:** O sistema nao deve permitir exclusao fisica de cursos. Cursos devem ser apenas despublicados, preservando dados historicos.

**Criterios de Aceitacao:**
1. Nenhuma interface ou endpoint permite a exclusao permanente de um curso.
2. Cursos despublicados permanecem acessiveis no painel administrativo para consulta historica.
3. Dados de matriculas, progresso e notas associados a cursos despublicados sao preservados integralmente.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-022
**Epico Relacionado:** EPIC-002
