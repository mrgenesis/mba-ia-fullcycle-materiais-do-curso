# Requisitos Funcionais: Tutoria e Metricas (Tutoring & Metrics)

> Detalhamento dos requisitos FR-030 a FR-033.
> Documento principal: [PRD](../index.md)

---

## FR-030: Vinculacao de Tutor a Curso [MUST]

**Descricao:** O Admin deve poder vincular e desvincular tutores a cursos, controlando o escopo de atuacao de cada tutor.

**Criterios de Aceitacao:**
1. O Admin consegue vincular um usuario com papel de "tutor" a um ou mais cursos.
2. O Admin consegue desvincular um tutor de um curso, removendo o acesso imediatamente.
3. Apenas usuarios com papel de Admin conseguem gerenciar vinculacoes de tutores.
4. A desvinculacao remove o acesso do tutor aos dados dos alunos do curso no ato da desvinculacao.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-018, RN-024
**Epico Relacionado:** EPIC-008

---

## FR-031: Acompanhamento de Alunos pelo Tutor [MUST]

**Descricao:** O Tutor vinculado a um curso deve poder visualizar o progresso e as notas dos alunos matriculados no curso.

**Criterios de Aceitacao:**
1. O tutor vinculado consegue visualizar a lista de alunos matriculados no curso ao qual esta vinculado.
2. O tutor consegue visualizar o progresso (percentual de aulas concluidas) e as notas de quiz de cada aluno.
3. O tutor nao consegue visualizar dados de alunos de cursos aos quais nao esta vinculado.
4. O tutor nao consegue editar conteudo do curso (modulos, aulas, quizzes).

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-018
**Epico Relacionado:** EPIC-008

---

## FR-032: Metricas do Professor [SHOULD]

**Descricao:** O Professor deve poder visualizar metricas dos seus cursos: taxa de conclusao, evasao por modulo e nota media dos concluintes.

**Criterios de Aceitacao:**
1. O professor visualiza a taxa de conclusao (matriculas concluidas / total de matriculas) para cada curso que criou.
2. O professor visualiza a taxa de evasao por modulo (alunos que pararam no modulo / total de alunos que iniciaram o modulo) para cada curso.
3. O professor visualiza a nota media dos alunos que concluiram cada curso.
4. O professor so visualiza metricas dos cursos que ele criou; metricas de cursos de outros professores nao sao acessiveis.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-016, RN-017
**Epico Relacionado:** EPIC-008

---

## FR-033: Metricas Globais do Admin [SHOULD]

**Descricao:** O Admin deve poder visualizar metricas globais da plataforma, incluindo dados de todos os cursos, professores e alunos.

**Criterios de Aceitacao:**
1. O Admin visualiza a taxa de conclusao agregada de todos os cursos da plataforma.
2. O Admin visualiza as metricas detalhadas (conclusao, evasao, nota media) de qualquer curso individual.
3. O Admin visualiza o total de usuarios ativos e bloqueados por papel.
4. Apenas usuarios com papel de Admin conseguem acessar as metricas globais.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-016
**Epico Relacionado:** EPIC-008
