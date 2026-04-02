# EPIC-007: Certificacao

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Reconhecer formalmente a conclusao de cursos e trilhas com certificados automaticos.
**Segmentos de Usuario:** Aluno, Admin
**Metricas de Sucesso:**
- 100% dos certificados emitidos automaticamente no momento da conclusao
- Zero certificados emitidos para cursos/trilhas nao concluidos

**Requisitos Relacionados:** FR-028, FR-029

---

## STORY-030: Certificado de curso ao atingir 100% com quizzes aprovados

> Como um **Aluno**, eu quero receber meu certificado ao concluir um curso (RN-014).

**Criterios de Aceitacao:**
1. **Given** 100% progresso + todos quizzes aprovados, **When** sistema detecta, **Then** certificado "curso" emitido, matricula "concluida".
2. **Given** 100% progresso mas quiz pendente, **When** sistema verifica, **Then** nenhum certificado.
3. **Given** curso sem quiz e 100% progresso, **When** sistema verifica, **Then** certificado emitido.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-031: Certificado de trilha ao concluir todos os cursos

> Como um **Aluno**, eu quero que o certificado de trilha seja emitido ao concluir o ultimo curso (RN-008).

**Criterios de Aceitacao:**
1. **Given** todos cursos concluidos exceto um, **When** conclui ultimo e certificado de curso emitido, **Then** certificado "trilha" emitido automaticamente.
2. **Given** certificado de trilha ja emitido, **When** novo curso adicionado a trilha, **Then** certificado existente permanece valido.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-032: Area de certificados do aluno

> Como um **Aluno**, eu quero acessar todos os meus certificados em um unico local.

**Criterios de Aceitacao:**
1. **Given** Aluno com certificados, **When** acessa area, **Then** lista todos com tipo, nome e data.
2. **Given** Aluno sem certificados, **When** acessa area, **Then** mensagem informativa e sugestao de cursos.
3. **Given** Admin acessa, **When** consulta certificados de qualquer aluno, **Then** lista completa exibida.

**Prioridade:** SHOULD | **Story Points:** 3

---

## STORY-033: Validacao e integridade dos certificados

> Como um **Admin**, eu quero garantir que certificados so sejam emitidos quando todos os criterios estao atendidos.

**Criterios de Aceitacao:**
1. **Given** matricula "ativa" e progresso < 100%, **When** verifica emissao, **Then** nenhum certificado.
2. **Given** certificado emitido e curso despublicado, **When** aluno consulta, **Then** certificado permanece valido (RN-022).
3. **Given** certificado emitido, **When** tentam excluir, **Then** sistema impede (registros historicos imutaveis).

**Prioridade:** MUST | **Story Points:** 3
