# Requisitos Funcionais: Matricula e Progresso (Enrollment & Progress)

> Detalhamento dos requisitos FR-020 a FR-024.
> Documento principal: [PRD](../index.md)

---

## FR-020: Matricula em Curso [MUST]

**Descricao:** O Aluno deve poder se matricular em cursos publicados, respeitando pre-requisitos e impedindo matriculas duplicadas ativas.

**Criterios de Aceitacao:**
1. O aluno consegue se matricular em um curso com situacao "publicado" e a matricula e criada com situacao "ativa".
2. O sistema rejeita a matricula quando o aluno ja possui uma matricula ativa para o mesmo curso.
3. O sistema rejeita a matricula quando o aluno nao concluiu todos os cursos pre-requisitos.
4. O aluno consegue se rematricular em um curso cuja matricula anterior esta "expirada" ou "cancelada".
5. A data de expiracao e calculada automaticamente como data da matricula + periodo de acesso do curso.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-001, RN-010, RN-013
**Epico Relacionado:** EPIC-005

---

## FR-021: Acompanhamento de Progresso do Curso [MUST]

**Descricao:** O sistema deve calcular e exibir o progresso do aluno no curso como percentual de aulas completadas sobre o total de aulas.

**Criterios de Aceitacao:**
1. O progresso do curso e calculado como (aulas completadas / total de aulas) * 100 e exibido ao aluno.
2. O progresso e atualizado automaticamente a cada aula marcada como concluida.
3. Quando o progresso atinge 100% e todos os quizzes estao aprovados, a matricula e marcada como "concluida" automaticamente.
4. A data de inicio do progresso e registrada automaticamente na primeira interacao do aluno com o conteudo.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-014
**Epico Relacionado:** EPIC-005

---

## FR-022: Cancelamento de Matricula [SHOULD]

**Descricao:** O Aluno (ou Admin) deve poder cancelar uma matricula ativa, alterando sua situacao para "cancelada".

**Criterios de Aceitacao:**
1. O aluno consegue cancelar sua propria matricula ativa, e a situacao muda para "cancelada".
2. O Admin consegue cancelar a matricula de qualquer aluno.
3. Matriculas ja concluidas ou expiradas nao podem ser canceladas.
4. O progresso e notas do aluno sao preservados mesmo apos o cancelamento.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-013, RN-022
**Epico Relacionado:** EPIC-005

---

## FR-023: Controle de Expiracao de Matricula [MUST]

**Descricao:** O sistema deve controlar a validade da matricula, aplicando periodo de carencia apos a expiracao e bloqueando acesso ao final da carencia.

**Criterios de Aceitacao:**
1. A matricula expira automaticamente quando a data atual ultrapassa a data de expiracao (data da matricula + periodo de acesso).
2. Durante o periodo de carencia, o aluno consegue visualizar o conteudo mas nao consegue avancar progresso nem realizar quizzes.
3. Apos o termino do periodo de carencia, o acesso do aluno ao curso e bloqueado completamente e a matricula e marcada como "expirada".
4. O aluno recebe indicacao clara de que esta em periodo de carencia e da data limite de acesso.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-010, RN-011
**Epico Relacionado:** EPIC-005

---

## FR-024: Renovacao de Matricula [SHOULD]

**Descricao:** O Aluno deve poder renovar uma matricula, estendendo a data de expiracao a partir da data atual e mantendo todo o progresso acumulado.

**Criterios de Aceitacao:**
1. O aluno consegue renovar uma matricula ativa ou em periodo de carencia.
2. A nova data de expiracao e calculada como data atual + periodo de acesso do curso.
3. Todo o progresso, notas e tentativas de quiz do aluno sao preservados integralmente apos a renovacao.
4. A situacao da matricula retorna para "ativa" caso estivesse em carencia.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-012
**Epico Relacionado:** EPIC-005
