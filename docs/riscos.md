# Riscos e Mitigacoes

> PRD: [docs/prd/index.md](prd/index.md)

---

| ID | Risco | Impacto | Prob. | Estrategia de Mitigacao | Responsavel |
|----|-------|---------|-------|------------------------|-------------|
| RSK-001 | Custos de armazenamento de midia escalarem | Alto | Alta | Compressao/transcoding automatico; limites por professor; monitorar custos | DevOps |
| RSK-002 | Falha no upload de arquivos grandes (ate 1 GB) | Alto | Alta | Upload multipart com retomada; retry automatico | Backend |
| RSK-003 | Deteccao de pre-requisitos circulares degradar performance | Medio | Media | DFS/topological sort otimizado; cache de grafo | Backend |
| RSK-004 | Mudancas de estado em cascata gerarem inconsistencias | Alto | Media | Transacoes atomicas; eventos assincronos com garantia de entrega | Backend |
| RSK-005 | Complexidade de periodo de carencia e transicoes de matricula | Medio | Media | Maquina de estados para matricula; job agendado idempotente | Backend |
| RSK-006 | Submissoes concorrentes de quiz gerarem duplicidade | Alto | Baixa | Lock otimista por matricula+quiz; fila de processamento | Backend |
| RSK-007 | Integridade de calculos automaticos em cenarios de borda | Alto | Media | Testes unitarios e integracao robustos; property-based testing | QA |
| RSK-008 | Performance de queries em dashboards com grande volume | Medio | Media | Materialized views; paginacao; cache com invalidacao | Backend |
| RSK-009 | Reordenacao causar inconsistencia na sequencia contigua | Medio | Baixa | Validacao server-side; reordenacao atomica; testes de regressao | Backend |
| RSK-010 | Atraso na definicao de infra de midia impactar Fase 1 | Alto | Media | POC de upload/storage na sprint 0 | Tech Lead |
