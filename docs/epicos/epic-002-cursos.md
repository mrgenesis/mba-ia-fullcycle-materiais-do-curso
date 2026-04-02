# EPIC-002: Gestao de Cursos

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Permitir que professores criem e gerenciem cursos completos com controle de publicacao, pre-requisitos e periodos de acesso.
**Segmentos de Usuario:** Professor, Admin
**Metricas de Sucesso:**
- Zero casos de pre-requisitos circulares persistidos
- 100% dos cursos despublicados mantendo acesso dos alunos por 30 dias

**Requisitos Relacionados:** FR-005, FR-006, FR-007, FR-008, FR-009

---

## STORY-006: Criacao de novo curso em rascunho

> Como um **Professor**, eu quero criar um novo curso com titulo, descricao, periodo de acesso e carencia, para organizar o conteudo antes de publica-lo.

**Criterios de Aceitacao:**
1. **Given** Professor autenticado, **When** preenche dados validos, **Then** curso criado em "rascunho" com Professor como criador (RN-017).
2. **Given** titulo > 150 chars ou descricao > 2000 chars, **When** tenta criar, **Then** sistema rejeita.
3. **Given** Professor acessa lista de cursos, **When** visualiza, **Then** ve apenas seus proprios cursos (RN-017).

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-007: Controle de publicacao de curso

> Como um **Professor**, eu quero publicar e despublicar meus cursos, para controlar quando o conteudo fica disponivel.

**Criterios de Aceitacao:**
1. **Given** curso em rascunho, **When** Professor publica, **Then** curso visivel para matricula.
2. **Given** curso publicado com alunos (RN-020), **When** Professor despublica, **Then** novas matriculas impedidas, matriculados mantem acesso por 30 dias.
3. **Given** curso despublicado ha > 30 dias, **When** aluno tenta acessar, **Then** acesso bloqueado.
4. **Given** conteudo publicado (RN-022), **When** Professor tenta excluir, **Then** sistema impede: so despublicar.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-008: Configuracao de pre-requisitos de curso

> Como um **Professor**, eu quero definir pre-requisitos para meu curso (RN-001).

**Criterios de Aceitacao:**
1. **Given** Professor editando curso, **When** adiciona pre-requisito, **Then** sistema registra dependencia.
2. **Given** pre-requisito criaria ciclo direto (A->B->A), **When** confirma, **Then** sistema detecta e impede (RN-001).
3. **Given** ciclo indireto (A->B->C->A), **When** confirma, **Then** sistema detecta circularidade transitiva.
4. **Given** curso com pre-requisitos, **When** aluno sem conclusao tenta matricular, **Then** sistema impede e lista pendencias.

**Prioridade:** MUST | **Story Points:** 8

---

## STORY-009: Edicao de curso restrita ao professor criador

> Como um **Professor**, eu quero editar meu curso para manter o conteudo atualizado.

**Criterios de Aceitacao:**
1. **Given** Professor e criador (RN-017), **When** altera dados, **Then** alteracoes salvas.
2. **Given** Professor nao e criador, **When** tenta editar, **Then** acesso negado.
3. **Given** Admin autenticado, **When** edita qualquer curso, **Then** permitido.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-010: Despublicacao granular de modulos e aulas

> Como um **Professor**, eu quero despublicar modulos/aulas sem afetar matriculados (RN-015).

**Criterios de Aceitacao:**
1. **Given** Professor despublica aula, **When** Aluno ja matriculado acessa, **Then** aula ainda disponivel.
2. **Given** Professor despublica aula, **When** novo Aluno se matricula, **Then** aula invisivel e fora do calculo de progresso.
3. **Given** Professor despublica modulo inteiro, **When** novos alunos acessam, **Then** modulo nao aparece.

**Prioridade:** SHOULD | **Story Points:** 5
