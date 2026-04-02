# Requisitos Funcionais: Trilhas de Aprendizado (Learning Paths)

> Detalhamento dos requisitos FR-025 a FR-027.
> Documento principal: [PRD](../index.md)

---

## FR-025: Criacao e Gestao de Trilhas de Aprendizado [MUST]

**Descricao:** O Admin deve poder criar trilhas de aprendizado com titulo, descricao e uma sequencia ordenada de cursos.

**Criterios de Aceitacao:**
1. O Admin consegue criar uma trilha com titulo (ate 150 caracteres) e descricao (ate 2000 caracteres).
2. O Admin consegue adicionar cursos a trilha com ordem contigua e sequencial (1, 2, 3...).
3. O Admin consegue reordenar ou remover cursos da trilha, mantendo a sequencia contigua.
4. Apenas usuarios com papel de Admin conseguem criar e gerenciar trilhas.
5. A trilha e visivel para alunos quando contem ao menos um curso publicado.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-009, Validacoes Transversais (ordenacao)
**Epico Relacionado:** EPIC-006

---

## FR-026: Progresso em Trilha de Aprendizado [MUST]

**Descricao:** O sistema deve calcular e exibir o progresso do aluno na trilha como media dos progressos individuais dos cursos que compoem a trilha.

**Criterios de Aceitacao:**
1. O progresso da trilha e calculado como media aritmetica dos progressos individuais dos cursos da trilha.
2. Cursos da trilha nos quais o aluno nao esta matriculado contam como 0% no calculo.
3. O progresso da trilha e atualizado automaticamente a cada alteracao de progresso em qualquer curso da trilha.
4. O aluno consegue visualizar o progresso geral da trilha e o progresso individual de cada curso componente.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-007
**Epico Relacionado:** EPIC-006

---

## FR-027: Ordem Recomendada na Trilha [COULD]

**Descricao:** A trilha deve exibir a ordem recomendada dos cursos, sem obrigar o aluno a seguir a sequencia (exceto quando houver pre-requisitos entre cursos).

**Criterios de Aceitacao:**
1. A trilha exibe os cursos na ordem definida pelo Admin com indicacao visual de ordem recomendada.
2. O aluno consegue se matricular em qualquer curso da trilha fora da ordem recomendada, desde que atenda aos pre-requisitos.
3. Cursos com pre-requisitos nao concluidos permanecem bloqueados para matricula, independentemente da trilha.

**Prioridade:** COULD
**Regras de Negocio Relacionadas:** RN-009, RN-001
**Epico Relacionado:** EPIC-006
