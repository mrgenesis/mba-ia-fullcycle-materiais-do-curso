# EPIC-005: Matricula e Progresso

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Permitir matriculas com controle de validade e acompanhamento de progresso em tempo real.
**Segmentos de Usuario:** Aluno, Admin
**Metricas de Sucesso:**
- 100% das datas de expiracao calculadas corretamente
- Zero matriculas ativas duplicadas

**Requisitos Relacionados:** FR-020, FR-021, FR-022, FR-023, FR-024, FR-038

---

## STORY-021: Matricula do aluno em curso publicado

> Como um **Aluno**, eu quero me matricular em cursos publicados (RN-013).

**Criterios de Aceitacao:**
1. **Given** curso publicado, sem matricula ativa e pre-requisitos atendidos, **When** matricula, **Then** criada "ativa" com expiracao = data + periodo de acesso (RN-010).
2. **Given** matricula ativa para mesmo curso, **When** tenta novamente, **Then** impedido.
3. **Given** matricula anterior expirada/cancelada, **When** rematricula, **Then** permitida com nova expiracao.
4. **Given** curso nao publicado, **When** tenta matricular, **Then** impedido.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-022: Visualizacao de progresso em tempo real

> Como um **Aluno**, eu quero acompanhar meu progresso no curso (RN-014).

**Criterios de Aceitacao:**
1. **Given** Aluno matriculado, **When** acessa painel, **Then** progresso = (aulas completadas / total) * 100.
2. **Given** 100% progresso + todos quizzes aprovados, **When** detecta conclusao, **Then** matricula "concluida" + certificado emitido.
3. **Given** 100% aulas mas quiz pendente, **When** acessa painel, **Then** 100% progresso mas matricula "ativa".

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-023: Controle de expiracao e carencia

> Como um **Aluno**, eu quero visualizar conteudo durante carencia apos expiracao (RN-010, RN-011).

**Criterios de Aceitacao:**
1. **Given** expirou mas carencia vigente, **When** acessa, **Then** visualiza conteudo sem avancar progresso/quizzes.
2. **Given** expirou e carencia tambem, **When** acessa, **Then** acesso bloqueado, matricula "expirada".
3. **Given** em carencia, **When** tenta submeter quiz, **Then** impedido com mensagem de renovacao.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-024: Renovacao de matricula

> Como um **Aluno**, eu quero renovar minha matricula mantendo progresso e notas (RN-012).

**Criterios de Aceitacao:**
1. **Given** matricula expirada ou em carencia, **When** renova, **Then** expiracao = data atual + periodo de acesso, situacao "ativa", progresso e notas mantidos.
2. **Given** renovacao realizada, **When** acessa curso, **Then** tudo preservado, continua de onde parou.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-025: Calculo automatico da data de expiracao

> Como um **Aluno**, eu quero que a data de expiracao seja calculada automaticamente (RN-010).

**Criterios de Aceitacao:**
1. **Given** curso com 180 dias de acesso, **When** matricula criada, **Then** expiracao = data + 180 dias.
2. **Given** 90 dias de acesso e 30 de carencia, **When** consulta detalhes, **Then** exibe data de expiracao ativa e data final de carencia.

**Prioridade:** MUST | **Story Points:** 2
