# EPIC-003: Gestao de Modulos e Aulas

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Estruturar conteudo educacional em modulos sequenciais com aulas de diferentes formatos.
**Segmentos de Usuario:** Professor, Aluno, Admin
**Metricas de Sucesso:**
- 100% dos uploads de midia ate 1 GB processados com sucesso
- Zero modulos desbloqueados indevidamente

**Requisitos Relacionados:** FR-010, FR-011, FR-012, FR-013, FR-014

---

## STORY-011: Criacao de modulos com ordem sequencial

> Como um **Professor**, eu quero criar modulos dentro do meu curso com titulos e ordem sequencial.

**Criterios de Aceitacao:**
1. **Given** Professor editando curso, **When** adiciona modulo com titulo valido, **Then** modulo criado com proxima ordem disponivel.
2. **Given** curso com modulos 1, 2, 3, **When** Professor reordena modulo 3 para posicao 1, **Then** ordens ajustadas automaticamente.
3. **Given** titulo > 150 caracteres, **When** tenta criar, **Then** sistema rejeita.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-012: Criacao de aulas com texto, video ou audio

> Como um **Professor**, eu quero criar aulas com conteudo em texto, video ou audio.

**Criterios de Aceitacao:**
1. **Given** tipo "texto", **When** cria com conteudo ate 50.000 chars, **Then** aula criada.
2. **Given** tipo "video"/"audio", **When** upload ate 1 GB, **Then** sistema aceita e armazena.
3. **Given** arquivo > 1 GB, **When** tenta upload, **Then** sistema rejeita com limite informado.
4. **Given** tipo "texto" sem conteudo, **When** tenta criar, **Then** validacao obriga preenchimento.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-013: Progressao sequencial entre modulos

> Como um **Aluno**, eu quero que modulos sejam desbloqueados sequencialmente conforme meu progresso (RN-002).

**Criterios de Aceitacao:**
1. **Given** Aluno matriculado, **When** acessa curso, **Then** apenas primeiro modulo desbloqueado.
2. **Given** 100% aulas completas + quiz aprovado, **When** acessa proximo modulo, **Then** desbloqueado.
3. **Given** modulo sem quiz e 100% aulas, **When** acessa proximo, **Then** desbloqueado.
4. **Given** 100% aulas mas quiz nao aprovado, **When** tenta acessar proximo, **Then** bloqueado.

**Prioridade:** MUST | **Story Points:** 8

---

## STORY-014: Registro de conclusao de aula pelo aluno

> Como um **Aluno**, eu quero marcar uma aula como concluida apos consumi-la (RN-003).

**Criterios de Aceitacao:**
1. **Given** aula com duracao estimada e < 70% transcorrido, **When** tenta marcar, **Then** impedido.
2. **Given** aula com duracao estimada e >= 70% transcorrido, **When** marca, **Then** concluida e progresso atualizado.
3. **Given** aula sem duracao estimada, **When** marca, **Then** permitido imediatamente.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-015: Reordenacao de aulas dentro de um modulo

> Como um **Professor**, eu quero reorganizar a ordem das aulas dentro de um modulo.

**Criterios de Aceitacao:**
1. **Given** modulo com aulas 1, 2, 3, **When** Professor move aula 3 para posicao 1, **Then** ordens recalculadas mantendo sequencia contigua.
2. **Given** Professor despublica aula, **When** para novos alunos, **Then** ordens ajustadas.

**Prioridade:** SHOULD | **Story Points:** 3
