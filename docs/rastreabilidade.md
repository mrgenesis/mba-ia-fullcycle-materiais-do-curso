# Matriz de Rastreabilidade

> PRD: [docs/prd/index.md](prd/index.md) | Epicos: [docs/epicos/index.md](epicos/index.md)

---

## Mapeamento: Regra de Negocio -> Descricao -> Fase

| Regra de Negocio | Descricao Resumida | Fase |
|------------------|--------------------|------|
| RN-001 | Pre-requisitos entre cursos; prevencao de ciclos | 2 |
| RN-002 | Desbloqueio sequencial de modulos | 1 |
| RN-003 | Aula marcada como completa apos 70% da duracao | 1 |
| RN-004 | Quiz com nota minima para aprovacao | 1 |
| RN-005 | Maximo de tentativas de quiz; tutor pode reabrir | 1, 2 |
| RN-006 | Nota final = media ponderada das melhores notas | 1 |
| RN-007 | Progresso da trilha = media dos cursos | 2 |
| RN-008 | Certificado da trilha ao concluir todos os cursos | 2 |
| RN-009 | Trilha define ordem recomendada, nao obrigatoria | 2 |
| RN-010 | Matricula tem validade (data + periodo de acesso) | 2 |
| RN-011 | Periodo de carencia apos expiracao | 2 |
| RN-012 | Renovacao estende expiracao | 3 |
| RN-013 | Matricula so em cursos publicados; sem duplicata ativa | 1 |
| RN-014 | Progresso = aulas completadas / total; conclusao automatica | 1, 2 |
| RN-015 | Despublicar modulo/aula nao afeta matriculados | 1 |
| RN-016 | Metricas do professor | 2, 3 |
| RN-017 | Professor so gerencia seus proprios cursos | 1 |
| RN-018 | Tutor so acessa cursos vinculados | 2 |
| RN-019 | Aluno so acessa seus proprios dados | 1 |
| RN-020 | Curso despublicado: 30 dias de carencia | 3 |
| RN-021 | Professor bloqueado: cursos acessiveis, edicao impedida | 3 |
| RN-022 | Conteudo nao pode ser excluido, apenas despublicado | 1 |
| RN-023 | Usuario pode ter multiplos papeis | 1 |
| RN-024 | Desvinculacao de tutor remove acesso imediatamente | 2 |

**Cobertura:** 100% - todas as 24 regras de negocio estao mapeadas.
