# Roadmap de Releases

> PRD: [docs/prd/index.md](prd/index.md) | Epicos: [docs/epicos/index.md](epicos/index.md)

---

## Fase 1 - MVP (Produto Minimo Viavel)

**Objetivo:** Validar o fluxo completo de criacao de conteudo, matricula e consumo de cursos.
**Duracao estimada:** 8-10 semanas

| Epico | Descricao | Regras de Negocio |
|-------|-----------|-------------------|
| EPIC-001 | Gestao de Usuarios | RN-017, RN-019, RN-023 |
| EPIC-002 | CRUD de Cursos, Modulos e Aulas | RN-015, RN-017, RN-022 |
| EPIC-003 | Matricula Basica | RN-013 |
| EPIC-004 | Progresso e Desbloqueio Sequencial | RN-002, RN-003, RN-014 |
| EPIC-005 | Sistema Basico de Quizzes | RN-004, RN-005 |

**Criterios de saida:**
- Aluno: cadastra, matricula, consome aulas, faz quizzes, ve progresso
- Professor: cria e publica cursos com modulos, aulas e quizzes
- Admin: cria usuarios e atribui papeis

---

## Fase 2 - Aprimoramento

**Objetivo:** Expandir funcionalidades pedagogicas e de gestao.
**Duracao estimada:** 6-8 semanas

| Epico | Descricao | Regras de Negocio |
|-------|-----------|-------------------|
| EPIC-006 | Trilhas de Aprendizado | RN-007, RN-008, RN-009 |
| EPIC-007 | Certificados (Curso e Trilha) | RN-008, RN-014 |
| EPIC-008 | Funcionalidades de Tutoria | RN-005, RN-018, RN-024 |
| EPIC-009 | Pre-requisitos entre Cursos | RN-001 |
| EPIC-010 | Metricas do Professor | RN-016 |
| EPIC-011 | Expiracao de Acesso e Periodo de Carencia | RN-010, RN-011, RN-020 |

**Criterios de saida:**
- Trilhas funcionais com certificado automatico
- Tutores vinculados com reabertura de quiz
- Pre-requisitos com deteccao de ciclos
- Metricas basicas para professores
- Sistema de expiracao e carencia operacional

---

## Fase 3 - Otimizacao

**Objetivo:** Refinar experiencia, performance e capacidades avancadas.
**Duracao estimada:** 4-6 semanas

| Epico | Descricao | Regras de Negocio |
|-------|-----------|-------------------|
| EPIC-012 | Dashboards Avancados de Metricas | RN-016 |
| EPIC-013 | Otimizacao de Performance (cache, CDN, queries) | - |
| EPIC-014 | Gestao Avancada de Midia (transcoding, thumbnails) | - |
| EPIC-015 | Renovacao de Matricula | RN-012 |
| EPIC-016 | Gestao de Professor Bloqueado e Conteudo Despublicado | RN-020, RN-021 |

**Criterios de saida:**
- Dashboards com metricas de conclusao, evasao e notas medias
- Tempo de carregamento de midias otimizado via CDN
- Fluxo de renovacao de matricula completo
- Regras de professor bloqueado e despublicacao implementadas
