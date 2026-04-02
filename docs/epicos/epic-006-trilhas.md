# EPIC-006: Trilhas de Aprendizado

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Organizar cursos em percursos estruturados com certificacao ao final.
**Segmentos de Usuario:** Admin, Aluno
**Metricas de Sucesso:**
- Progresso da trilha calculado corretamente como media dos cursos
- 100% dos certificados de trilha emitidos automaticamente

**Requisitos Relacionados:** FR-025, FR-026, FR-027

---

## STORY-026: Criacao de trilhas com cursos ordenados

> Como um **Admin**, eu quero criar trilhas de aprendizado agrupando cursos em ordem recomendada.

**Criterios de Aceitacao:**
1. **Given** Admin autenticado, **When** cria trilha com titulo, descricao e cursos ordenados, **Then** trilha criada e visivel para alunos.
2. **Given** Admin reordena cursos, **When** salva, **Then** ordens ajustadas (1, 2, 3...).
3. **Given** curso com pre-requisitos na trilha, **When** aluno tenta matricular, **Then** pre-requisitos respeitados independente da ordem na trilha (RN-009).

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-027: Navegacao flexivel na trilha com respeito a pre-requisitos

> Como um **Aluno**, eu quero visualizar a ordem recomendada sabendo que posso iniciar cursos fora de ordem (RN-009).

**Criterios de Aceitacao:**
1. **Given** trilha sem pre-requisitos, **When** acessa, **Then** todos cursos disponiveis para matricula em qualquer ordem.
2. **Given** Curso B requer Curso A, **When** tenta matricular em B sem concluir A, **Then** impedido.
3. **Given** Curso C sem pre-requisitos, **When** tenta matricular sem fazer outros, **Then** permitido.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-028: Calculo e visualizacao do progresso na trilha

> Como um **Aluno**, eu quero visualizar meu progresso geral na trilha (RN-007).

**Criterios de Aceitacao:**
1. **Given** trilha com 4 cursos, completou 100% no C1, 50% no C2, nao iniciou C3 e C4, **When** acessa, **Then** progresso = (100+50+0+0)/4 = 37,5%.
2. **Given** nao matriculado em nenhum curso, **When** acessa, **Then** progresso = 0%.
3. **Given** todos cursos concluidos (100%), **When** acessa, **Then** progresso = 100%.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-029: Emissao automatica de certificado da trilha

> Como um **Aluno**, eu quero receber o certificado da trilha ao concluir todos os cursos (RN-008).

**Criterios de Aceitacao:**
1. **Given** Aluno concluiu todos exceto ultimo, **When** conclui ultimo, **Then** certificado "trilha" emitido automaticamente.
2. **Given** 3 de 4 cursos concluidos, **When** acessa trilha, **Then** certificado nao disponivel, indica quais faltam.
3. **Given** certificado emitido, **When** acessa area de certificados, **Then** disponivel com data e nome da trilha.

**Prioridade:** MUST | **Story Points:** 5
