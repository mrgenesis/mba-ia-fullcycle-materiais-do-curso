# Requisitos Funcionais: Certificacao (Certification)

> Detalhamento dos requisitos FR-028 a FR-029.
> Documento principal: [PRD](../index.md)

---

## FR-028: Emissao de Certificado de Curso [MUST]

**Descricao:** O sistema deve emitir automaticamente um certificado de curso quando o aluno conclui 100% das aulas e aprova todos os quizzes do curso.

**Criterios de Aceitacao:**
1. O certificado de curso e emitido automaticamente no momento em que a matricula e marcada como "concluida".
2. O certificado registra o tipo "curso", a data de emissao (automatica) e identifica o aluno e o curso.
3. O aluno consegue visualizar e acessar o certificado emitido a partir do seu perfil ou da pagina do curso.
4. Nao e possivel emitir certificado duplicado para a mesma combinacao aluno/curso/matricula.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-014
**Epico Relacionado:** EPIC-007

---

## FR-029: Emissao de Certificado de Trilha [MUST]

**Descricao:** O sistema deve emitir automaticamente um certificado de trilha quando o aluno conclui todos os cursos que compoem a trilha.

**Criterios de Aceitacao:**
1. O certificado de trilha e emitido automaticamente ao concluir o ultimo curso pendente da trilha.
2. O certificado registra o tipo "trilha", a data de emissao (automatica) e identifica o aluno e a trilha.
3. O certificado so e emitido quando todos os cursos da trilha possuem matricula com situacao "concluida".
4. O aluno consegue visualizar e acessar o certificado de trilha a partir do seu perfil.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-008
**Epico Relacionado:** EPIC-007
