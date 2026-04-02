# EPIC-004: Sistema de Avaliacao

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Medir o aprendizado dos alunos por meio de quizzes com correcao automatica.
**Segmentos de Usuario:** Professor, Aluno, Tutor
**Metricas de Sucesso:**
- 100% das notas calculadas automaticamente
- Zero tentativas realizadas alem do limite (sem reabertura por tutor)

**Requisitos Relacionados:** FR-015, FR-016, FR-017, FR-018, FR-019

---

## STORY-016: Criacao de quiz associado a um modulo

> Como um **Professor**, eu quero criar quizzes com questoes de multipla escolha para avaliar alunos.

**Criterios de Aceitacao:**
1. **Given** Professor editando modulo, **When** cria quiz com nota minima (0-100) e opcionalmente maximo de tentativas, **Then** quiz associado ao modulo.
2. **Given** questao com enunciado e >= 4 opcoes (exatamente 1 correta), **When** salva, **Then** questao criada com ordem sequencial.
3. **Given** < 4 opcoes ou != 1 correta, **When** tenta salvar, **Then** validacao impede.
4. **Given** quiz sem questoes, **When** tenta publicar, **Then** sistema exige ao menos 1 questao.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-017: Tentativa de quiz com correcao automatica

> Como um **Aluno**, eu quero realizar tentativas de quiz e receber minha nota automaticamente (RN-004).

**Criterios de Aceitacao:**
1. **Given** Aluno acessa quiz desbloqueado, **When** responde e submete, **Then** nota = (corretas / total) * 100.
2. **Given** nota >= minima, **When** correcao finalizada, **Then** quiz aprovado, avanco permitido.
3. **Given** nota < minima, **When** correcao finalizada, **Then** informa nota, minima necessaria e tentativas restantes.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-018: Limite de tentativas de quiz e bloqueio

> Como um **Aluno**, eu quero saber quantas tentativas restam em um quiz (RN-005).

**Criterios de Aceitacao:**
1. **Given** maximo = 3 e Aluno fez 3 tentativas sem aprovacao, **When** tenta nova, **Then** bloqueado com mensagem.
2. **Given** maximo nao definido, **When** Aluno faz multiplas tentativas, **Then** ilimitado.
3. **Given** Aluno bloqueado, **When** Tutor vinculado reabre, **Then** Aluno pode tentar novamente.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-019: Nota final do curso como media ponderada

> Como um **Aluno**, eu quero visualizar minha nota final no curso (RN-006).

**Criterios de Aceitacao:**
1. **Given** Aluno concluiu todos os quizzes, **When** acessa notas, **Then** nota final = media ponderada das melhores notas (peso = numero de questoes).
2. **Given** modulos com e sem quiz, **When** calcula nota, **Then** modulos sem quiz ignorados.
3. **Given** curso sem nenhum quiz, **When** Aluno acessa notas, **Then** informa que curso nao possui nota.

**Prioridade:** MUST | **Story Points:** 5

---

## STORY-020: Ajuste da ordem das questoes em um quiz

> Como um **Professor**, eu quero reordenar as questoes de um quiz.

**Criterios de Aceitacao:**
1. **Given** quiz com questoes 1-4, **When** Professor move questao 4 para posicao 2, **Then** ordens recalculadas.
2. **Given** Professor remove questao, **When** salva, **Then** ordens restantes ajustadas.

**Prioridade:** SHOULD | **Story Points:** 2
