# EPIC-008: Tutoria e Metricas

> [Voltar ao indice](index.md) | PRD: [docs/prd/index.md](../prd/index.md)

**Valor de Negocio:** Suporte pedagogico efetivo e visibilidade sobre desempenho por meio de metricas.
**Segmentos de Usuario:** Tutor, Professor, Admin
**Metricas de Sucesso:**
- 100% dos tutores com acesso restrito aos cursos vinculados
- Dashboards de metricas carregados em < 3 segundos

**Requisitos Relacionados:** FR-030, FR-031, FR-032, FR-033, FR-035

---

## STORY-034: Atribuicao de tutor a curso pelo administrador

> Como um **Admin**, eu quero vincular tutores a cursos especificos (RN-018).

**Criterios de Aceitacao:**
1. **Given** Admin seleciona usuario com papel "tutor", **When** vincula ao curso, **Then** tutor acessa progresso e notas dos alunos.
2. **Given** usuario sem papel "tutor", **When** tenta vincular, **Then** sistema impede.
3. **Given** Tutor vinculado a 2 cursos, **When** acessa plataforma, **Then** ve dados apenas desses 2 cursos.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-035: Remocao de tutor de curso com efeito imediato

> Como um **Admin**, eu quero desvincular um tutor com efeito imediato (RN-024).

**Criterios de Aceitacao:**
1. **Given** Tutor vinculado, **When** Admin desvincula, **Then** acesso revogado imediatamente.
2. **Given** Tutor desvinculado, **When** tenta acessar dados do curso, **Then** acesso negado.
3. **Given** Tutor vinculado a A e B, **When** desvinculado de A, **Then** mantem acesso a B.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-036: Tutor reabre tentativa de quiz bloqueada

> Como um **Tutor**, eu quero reabrir tentativas de quiz para alunos que esgotaram o limite (RN-005).

**Criterios de Aceitacao:**
1. **Given** Aluno esgotou tentativas em curso vinculado, **When** Tutor reabre, **Then** Aluno pode tentar novamente.
2. **Given** Tutor tenta reabrir em curso nao vinculado (RN-018), **When** executa acao, **Then** acesso negado.
3. **Given** Aluno ainda tem tentativas, **When** Tutor tenta reabrir, **Then** sistema informa que nao e necessario.

**Prioridade:** MUST | **Story Points:** 3

---

## STORY-037: Dashboard de metricas do professor

> Como um **Professor**, eu quero visualizar metricas de desempenho dos meus cursos (RN-016).

**Criterios de Aceitacao:**
1. **Given** Professor acessa metricas de seu curso, **When** seleciona curso, **Then** ve taxa de conclusao, evasao por modulo e nota media.
2. **Given** Professor com multiplos cursos, **When** acessa metricas, **Then** ve apenas seus cursos (RN-017).
3. **Given** curso sem alunos, **When** acessa metricas, **Then** exibe zero/sem dados.

**Prioridade:** SHOULD | **Story Points:** 5

---

## STORY-038: Dashboard de metricas globais da plataforma

> Como um **Admin**, eu quero visualizar metricas globais para decisoes estrategicas.

**Criterios de Aceitacao:**
1. **Given** Admin acessa metricas globais, **When** pagina carrega, **Then** total de usuarios por papel, cursos, matriculas, taxa de conclusao e nota media global.
2. **Given** Admin filtra por curso, **When** seleciona, **Then** metricas detalhadas de qualquer curso.
3. **Given** Admin filtra por trilha, **When** seleciona, **Then** taxa de conclusao da trilha e certificados emitidos.

**Prioridade:** SHOULD | **Story Points:** 8

---

## STORY-039: Visualizacao de progresso e notas dos alunos pelo tutor

> Como um **Tutor**, eu quero visualizar progresso e notas dos alunos dos cursos vinculados (RN-018).

**Criterios de Aceitacao:**
1. **Given** Tutor vinculado a curso, **When** acessa painel, **Then** lista alunos com progresso, nota, modulo atual e tentativas.
2. **Given** Tutor seleciona aluno, **When** acessa detalhes, **Then** aulas concluidas, notas de cada quiz, tentativas e datas.
3. **Given** Tutor tenta acessar curso nao vinculado, **When** navega ou URL direta, **Then** acesso negado.

**Prioridade:** MUST | **Story Points:** 5
