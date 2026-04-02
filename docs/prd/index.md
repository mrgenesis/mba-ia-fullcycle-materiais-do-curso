# Product Requirements Document (PRD)

**Nome do Projeto:** CodeLearn Platform
**Versao do Documento:** 1.0
**Data:** 2026-03-26
**Status:** Rascunho

---

## Controle do Documento

| Versao | Data | Autor | Alteracoes |
|---------|------|--------|---------|
| 1.0 | 2026-03-26 | Product Manager (AI-assisted) | Versao inicial |

---

## Sumario Executivo

**Problema:**
Instituicoes de ensino e criadores de conteudo carecem de uma plataforma unificada que permita criar, organizar e distribuir cursos online com controle pedagogico rigoroso — incluindo progressao sequencial, avaliacoes automaticas, trilhas de aprendizado e certificacao — sem depender de multiplas ferramentas fragmentadas.

**Solucao Proposta:**
A CodeLearn Platform e uma plataforma web de cursos online que permite a criacao, gestao e consumo de conteudo educacional organizado em cursos, modulos, aulas, quizzes e trilhas de aprendizado, com emissao automatica de certificados e controle granular de acesso por papeis.

**Valor de Negocio:**
- Plataforma autossuficiente para ensino online com modelo pedagogico sequencial
- Automacao completa de avaliacoes, progresso e certificacao
- Controle contextual de acesso garantindo seguranca e privacidade dos dados

**Metricas de Sucesso:**
- Taxa de conclusao de cursos superior a 40% entre alunos matriculados
- 100% dos certificados emitidos automaticamente sem intervencao manual
- Zero acessos nao autorizados a recursos protegidos por autorizacao contextual

---

## 1. Visao Geral do Projeto

### Contexto

A CodeLearn Platform atende a necessidade de uma plataforma completa de ensino online com foco em progressao pedagogica estruturada. O sistema permite que professores criem cursos com modulos sequenciais, aulas em multiplos formatos (texto, video, audio) e quizzes com correcao automatica, enquanto alunos acompanham seu progresso e obtem certificados ao concluir cursos e trilhas de aprendizado.

### Stakeholders

| Stakeholder | Papel | Interesse | Influencia |
|-------------|------|----------|-----------|
| Aluno | Consumidor de conteudo | Experiencia de aprendizado fluida e certificacao | Alta |
| Professor | Criador de conteudo | Ferramentas de criacao e metricas de desempenho | Alta |
| Tutor | Suporte pedagogico | Acompanhamento de alunos e intervencao quando necessario | Media |
| Admin | Gestao da plataforma | Controle total sobre usuarios, trilhas e metricas globais | Alta |

---

## 2. Objetivos

### Objetivos de Negocio
1. Fornecer uma plataforma de cursos online autossuficiente com modelo pedagogico sequencial
2. Automatizar avaliacoes, calculo de notas, progresso e emissao de certificados
3. Garantir controle de acesso contextual por papel (professor, tutor, aluno, admin)

### Objetivos do Usuario
1. **Aluno:** Consumir conteudo educacional de forma organizada, acompanhar progresso e obter certificados
2. **Professor:** Criar e gerenciar cursos com modulos, aulas e quizzes de forma autonoma
3. **Tutor:** Acompanhar alunos de cursos vinculados e oferecer suporte pedagogico
4. **Admin:** Gerenciar usuarios, trilhas e ter visibilidade global da plataforma

---

## 3. Personas

### Aluno (Student)
**Perfil:** Consumidor principal da plataforma educacional.
**Objetivos:** Buscar e se matricular em cursos publicados; consumir aulas em diferentes formatos; realizar quizzes e acompanhar progresso; obter certificados ao concluir cursos e trilhas.
**Restricoes de Acesso:** So visualiza cursos publicados; so acessa seus proprios dados, matriculas e progresso (RN-019).

### Professor (Teacher)
**Perfil:** Criador de conteudo educacional.
**Objetivos:** Criar e editar cursos, modulos, aulas e quizzes; publicar e despublicar conteudo; acompanhar metricas de desempenho dos seus cursos.
**Restricoes de Acesso:** So gerencia cursos que ele criou (RN-017); nao gerencia usuarios nem trilhas; nao pode editar/criar conteudo quando bloqueado (RN-021).

### Tutor
**Perfil:** Suporte pedagogico vinculado a cursos especificos.
**Objetivos:** Acompanhar progresso e notas dos alunos; reabrir tentativas de quiz bloqueadas (RN-005).
**Restricoes de Acesso:** So acessa cursos vinculados (RN-018); nao edita conteudo; acesso removido imediatamente ao ser desvinculado (RN-024).

### Admin
**Perfil:** Gestao global da plataforma.
**Objetivos:** Criar e gerenciar usuarios; criar e gerenciar trilhas de aprendizado; vincular tutores a cursos; acessar metricas globais.

---

## 4. Requisitos Funcionais

**Legenda de prioridade MoSCoW:**
- **MUST** -- Obrigatorio para o lancamento. Sem ele o produto nao funciona.
- **SHOULD** -- Importante, esperado no lancamento, mas contornavel temporariamente.
- **COULD** -- Desejavel, agrega valor, pode ser adiado sem impacto critico.
- **WONT** -- Fora do escopo desta versao; registrado para referencia futura.

| ID | Nome | Prioridade | Dominio |
|----|------|-----------|---------|
| FR-001 | Cadastro de Usuario | MUST | [Gestao de Usuarios](requisitos-funcionais/gestao-usuarios.md) |
| FR-002 | Autenticacao de Usuario | MUST | [Gestao de Usuarios](requisitos-funcionais/gestao-usuarios.md) |
| FR-003 | Gestao de Papeis de Usuario | MUST | [Gestao de Usuarios](requisitos-funcionais/gestao-usuarios.md) |
| FR-004 | Bloqueio e Desbloqueio de Usuario | MUST | [Gestao de Usuarios](requisitos-funcionais/gestao-usuarios.md) |
| FR-005 | Criacao de Curso | MUST | [Gestao de Cursos](requisitos-funcionais/gestao-cursos.md) |
| FR-006 | Edicao de Curso | MUST | [Gestao de Cursos](requisitos-funcionais/gestao-cursos.md) |
| FR-007 | Publicacao e Despublicacao de Curso | MUST | [Gestao de Cursos](requisitos-funcionais/gestao-cursos.md) |
| FR-008 | Definicao de Pre-requisitos de Curso | SHOULD | [Gestao de Cursos](requisitos-funcionais/gestao-cursos.md) |
| FR-009 | Exclusao Logica de Curso | MUST | [Gestao de Cursos](requisitos-funcionais/gestao-cursos.md) |
| FR-010 | Criacao e Ordenacao de Modulos | MUST | [Modulos e Aulas](requisitos-funcionais/modulos-aulas.md) |
| FR-011 | Criacao e Gestao de Aulas | MUST | [Modulos e Aulas](requisitos-funcionais/modulos-aulas.md) |
| FR-012 | Desbloqueio Sequencial de Modulos | MUST | [Modulos e Aulas](requisitos-funcionais/modulos-aulas.md) |
| FR-013 | Marcacao de Conclusao de Aula | MUST | [Modulos e Aulas](requisitos-funcionais/modulos-aulas.md) |
| FR-014 | Despublicacao de Modulo e Aula | SHOULD | [Modulos e Aulas](requisitos-funcionais/modulos-aulas.md) |
| FR-015 | Criacao e Configuracao de Quiz | MUST | [Avaliacao](requisitos-funcionais/avaliacao.md) |
| FR-016 | Criacao de Questoes e Opcoes | MUST | [Avaliacao](requisitos-funcionais/avaliacao.md) |
| FR-017 | Realizacao de Tentativa de Quiz | MUST | [Avaliacao](requisitos-funcionais/avaliacao.md) |
| FR-018 | Bloqueio e Reabertura de Tentativas de Quiz | MUST | [Avaliacao](requisitos-funcionais/avaliacao.md) |
| FR-019 | Calculo de Nota Final do Curso | MUST | [Avaliacao](requisitos-funcionais/avaliacao.md) |
| FR-020 | Matricula em Curso | MUST | [Matricula e Progresso](requisitos-funcionais/matricula-progresso.md) |
| FR-021 | Acompanhamento de Progresso do Curso | MUST | [Matricula e Progresso](requisitos-funcionais/matricula-progresso.md) |
| FR-022 | Cancelamento de Matricula | SHOULD | [Matricula e Progresso](requisitos-funcionais/matricula-progresso.md) |
| FR-023 | Controle de Expiracao de Matricula | MUST | [Matricula e Progresso](requisitos-funcionais/matricula-progresso.md) |
| FR-024 | Renovacao de Matricula | SHOULD | [Matricula e Progresso](requisitos-funcionais/matricula-progresso.md) |
| FR-025 | Criacao e Gestao de Trilhas de Aprendizado | MUST | [Trilhas](requisitos-funcionais/trilhas.md) |
| FR-026 | Progresso em Trilha de Aprendizado | MUST | [Trilhas](requisitos-funcionais/trilhas.md) |
| FR-027 | Ordem Recomendada na Trilha | COULD | [Trilhas](requisitos-funcionais/trilhas.md) |
| FR-028 | Emissao de Certificado de Curso | MUST | [Certificacao](requisitos-funcionais/certificacao.md) |
| FR-029 | Emissao de Certificado de Trilha | MUST | [Certificacao](requisitos-funcionais/certificacao.md) |
| FR-030 | Vinculacao de Tutor a Curso | MUST | [Tutoria e Metricas](requisitos-funcionais/tutoria-metricas.md) |
| FR-031 | Acompanhamento de Alunos pelo Tutor | MUST | [Tutoria e Metricas](requisitos-funcionais/tutoria-metricas.md) |
| FR-032 | Metricas do Professor | SHOULD | [Tutoria e Metricas](requisitos-funcionais/tutoria-metricas.md) |
| FR-033 | Metricas Globais do Admin | SHOULD | [Tutoria e Metricas](requisitos-funcionais/tutoria-metricas.md) |
| FR-034 | Controle de Acesso Contextual do Professor | MUST | [Autorizacao](requisitos-funcionais/autorizacao.md) |
| FR-035 | Controle de Acesso Contextual do Tutor | MUST | [Autorizacao](requisitos-funcionais/autorizacao.md) |
| FR-036 | Controle de Acesso do Aluno | MUST | [Autorizacao](requisitos-funcionais/autorizacao.md) |
| FR-037 | Acesso Irrestrito do Admin | MUST | [Autorizacao](requisitos-funcionais/autorizacao.md) |
| FR-038 | Listagem e Busca de Cursos pelo Aluno | SHOULD | [Autorizacao](requisitos-funcionais/autorizacao.md) |

---

## 5. Requisitos Nao Funcionais

Detalhes completos em [requisitos-nao-funcionais.md](requisitos-nao-funcionais.md).

| ID | Categoria | Prioridade | Resumo |
|---|---|---|---|
| NFR-001 | Desempenho | MUST | API <= 300 ms (leitura) / 500 ms (escrita) no P95 |
| NFR-002 | Desempenho | MUST | Upload de midia ate 1 GB com taxa de falha < 1% |
| NFR-003 | Desempenho | MUST | LCP <= 2,5 s, FID <= 100 ms, CLS <= 0,1 |
| NFR-004 | Desempenho | SHOULD | 2.000 usuarios concorrentes; streaming inicia em <= 3 s |
| NFR-005 | Seguranca | MUST | Autenticacao segura com lockout e tokens com expiracao |
| NFR-006 | Seguranca | MUST | Autorizacao contextual com 100% de cobertura |
| NFR-007 | Seguranca | MUST | HTTPS/TLS 1.2+, soft-delete integro |
| NFR-008 | Escalabilidade | SHOULD | Suporte a 50.000 usuarios com scale-out sem downtime |
| NFR-009 | Escalabilidade | MUST | Object storage ate 10 TB com TTFB <= 500 ms |
| NFR-010 | Escalabilidade | SHOULD | Queries <= 100 ms no P95 com 10M registros |
| NFR-011 | Confiabilidade | MUST | Disponibilidade >= 99,5% mensal; >= 99,9% em pico |
| NFR-012 | Confiabilidade | MUST | Backup a cada 6 h, restauracao em <= 30 min |
| NFR-013 | Usabilidade | SHOULD | WCAG 2.1 AA, Lighthouse >= 90 |
| NFR-014 | Usabilidade | MUST | Responsivo 320-2560 px, pt-BR primario |
| NFR-015 | Manutenibilidade | MUST | Cobertura >= 80% (negocio), logs estruturados, alertas |

---

## 6. Metricas de Sucesso

| Metrica | Baseline | Meta | Metodo de Medicao | Frequencia |
|---------|----------|------|-------------------|-----------|
| Taxa de conclusao de cursos | N/A | > 40% | Matriculas concluidas / Total matriculas | Mensal |
| Taxa de evasao por modulo | N/A | < 20% por modulo | Alunos parados no modulo / Iniciaram modulo | Mensal |
| Nota media dos concluintes | N/A | > 70/100 | Media de notas finais de cursos concluidos | Mensal |
| Certificados emitidos automaticamente | N/A | 100% | Certificados / Conclusoes | Continuo |
| Tempo de emissao de certificado | N/A | < 5 s | Tempo entre conclusao e emissao | Continuo |
| Tentativas de acesso nao autorizado | N/A | 0 | Logs de autorizacao | Continuo |

---

## 7. Premissas e Dependencias

### Premissas

| # | Premissa | Justificativa |
|---|----------|---------------|
| P-001 | A plataforma sera exclusivamente web-based (responsiva) | Nao ha requisito para aplicativos nativos mobile |
| P-002 | O deployment sera single-tenant (instancia unica) | Nao ha requisitos de multi-tenancy na versao inicial |
| P-003 | O idioma primario e unico da v1 sera Portugues Brasileiro (pt-BR) | Publico-alvo exclusivamente brasileiro |
| P-004 | O armazenamento de midia suportara arquivos de ate 1 GB por arquivo | Conforme entidade Lesson; exige object storage |
| P-005 | O desbloqueio sequencial de modulos e fundamental ao modelo pedagogico | Conforme RN-002; progressao linear e premissa do design instrucional |
| P-006 | Todos os calculos de regras de negocio serao server-side | Garante integridade e consistencia |
| P-007 | Conteudo nunca sera excluido fisicamente (soft-delete) | Conforme RN-022; dados historicos preservados |
| P-008 | Usuarios podem possuir multiplos papeis simultaneamente | Conforme RN-023 |

### Dependencias

| ID | Dependencia | Tipo | Responsavel | Status | Risco | Mitigacao |
|----|-------------|------|-------------|--------|-------|-----------|
| DEP-001 | Servico de object storage para midia (ate 1 GB) | Infraestrutura | DevOps | Pendente | Alto | Avaliar S3/MinIO/GCS; definir lifecycle e CDN |
| DEP-002 | Servico de e-mail transacional | Infraestrutura | DevOps | Pendente | Medio | SES/SendGrid com fila de retry |
| DEP-003 | Servico de autenticacao e sessao (JWT/OAuth) | Tecnica | Backend | Pendente | Alto | Definir stack na fase de arquitetura |
| DEP-004 | Banco de dados relacional com ACID | Tecnica | Backend | Pendente | Baixo | PostgreSQL |
| DEP-005 | Pipeline de CI/CD | Infraestrutura | DevOps | Pendente | Medio | GitHub Actions/GitLab CI |
| DEP-006 | CDN para distribuicao de midia | Infraestrutura | DevOps | Pendente | Medio | CloudFront/CloudFlare |
| DEP-007 | Servico de geracao de PDF para certificados | Tecnica | Backend | Pendente | Baixo | Puppeteer/WeasyPrint |
| DEP-008 | Ambiente de staging | Processo | DevOps | Pendente | Baixo | Ambiente identico a producao |

---

## 8. Restricoes

### Restricoes Tecnicas

| ID | Restricao | Impacto |
|----|-----------|---------|
| RT-001 | Upload de midia limitado a 1 GB por arquivo | Exige upload multipart/chunked com retomada |
| RT-002 | Senha deve ter min 8 chars com 1 maiuscula, 1 minuscula, 1 numero e 1 simbolo | Validacao frontend + backend |
| RT-003 | Ordenacao contigua e sequencial (1, 2, 3...) | Reordenacao automatica em insercao/remocao |
| RT-004 | Deteccao de pre-requisitos circulares (RN-001) | Algoritmo de deteccao de ciclos em grafo |
| RT-005 | Nota final por media ponderada (RN-006) | Recalculo a cada tentativa de quiz |
| RT-006 | Conclusao de aula apos 70% da duracao (RN-003) | Rastreamento de tempo no frontend + validacao server-side |

### Restricoes de Negocio

| ID | Restricao | Impacto |
|----|-----------|---------|
| RB-001 | Conteudo nao pode ser excluido (RN-022) | Banco cresce continuamente; necessidade de archiving |
| RB-002 | Dados historicos preservados (RN-022) | Registros nunca removidos fisicamente |
| RB-003 | Periodo de carencia de 30 dias para cursos despublicados (RN-020) | Job agendado para verificacao diaria |
| RB-004 | Matricula somente em cursos publicados (RN-013) | Transicao de estado impacta fluxo de matricula |
| RB-005 | Professor bloqueado nao edita mas cursos acessiveis (RN-021) | Separacao acesso conteudo vs permissao de gestao |
| RB-006 | Desvinculacao de tutor remove acesso imediatamente (RN-024) | Invalidacao de sessao/cache em tempo real |

### Restricoes de Recursos

| ID | Restricao | Impacto |
|----|-----------|---------|
| RR-001 | Custos de armazenamento de midia escalam com volume | Monitoramento de custos e compressao/transcoding |
| RR-002 | Time de desenvolvimento limitado | Priorizacao rigorosa via fases de release |
| RR-003 | Banda de rede para streaming | CDN obrigatoria; impacto em custo operacional |

---

## 9. Fora do Escopo

| # | Item Excluido | Justificativa |
|---|---------------|---------------|
| FE-001 | Videoconferencia em tempo real / aulas ao vivo | Complexidade alta; existem solucoes terceiras maduras |
| FE-002 | Sistema de pagamento e cobranca | Requer gateway de pagamento e conformidade fiscal |
| FE-003 | Aplicativos nativos mobile (iOS/Android) | Plataforma sera web responsiva |
| FE-004 | Funcionalidades sociais (foruns, comentarios, chat) | Complexidade de moderacao e infraestrutura |
| FE-005 | Gamificacao alem de certificados | Certificados atendem necessidade inicial |
| FE-006 | Integracao com conteudo de terceiros (SCORM/xAPI/LTI) | Padroes complexos; foco em conteudo proprio |
| FE-007 | Recomendacoes baseadas em IA | Requer volume de dados para treino e infra de ML |
| FE-008 | Suporte multi-idioma | v1 exclusivamente em pt-BR |
| FE-009 | Integracao com LMS externos ou APIs publicas | Foco na plataforma autossuficiente |
| FE-010 | Notificacoes push / in-app avancado | E-mail suficiente para v1 |

---

## 10. Glossario

| Termo | Definicao |
|-------|-----------|
| **Matricula** | Vinculo formal entre aluno e curso, com situacao (ativa/concluida/expirada/cancelada), data de expiracao e progresso. |
| **Trilha de Aprendizado** | Agrupamento ordenado de cursos formando um percurso formativo completo. Ordem recomendada, nao obrigatoria. |
| **Periodo de Carencia** | Intervalo apos expiracao da matricula onde o aluno pode visualizar conteudo sem avancar progresso ou fazer quiz. |
| **Modulo** | Unidade organizacional de um curso. Contem aulas e opcionalmente um quiz. Desbloqueio sequencial. |
| **Aula** | Unidade de conteudo dentro de um modulo. Tipos: texto, video ou audio. |
| **Quiz** | Avaliacao vinculada a modulo com questoes de multipla escolha. Define nota minima e maximo de tentativas. |
| **Tentativa** | Registro de submissao de quiz com respostas, nota calculada e data. |
| **Certificado** | Documento emitido automaticamente apos conclusao de curso (100% + quizzes) ou trilha (todos cursos). |
| **Despublicar** | Alterar situacao de conteudo para invisivel para novos alunos. Nao exclui fisicamente. |
| **Pre-requisito** | Relacao entre cursos onde conclusao de um e obrigatoria para matricula em outro. Ciclos impedidos. |
| **Nota Final** | Media ponderada das melhores notas de cada quiz, com peso = numero de questoes. |
| **Progresso** | Percentual: (aulas completadas / total de aulas) * 100. |
| **Rematricula** | Nova matricula quando anterior expirada/cancelada. Progresso anterior nao preservado. |
| **Renovacao** | Extensao de prazo de matricula ativa/em carencia. Progresso e notas preservados. |
| **Soft-delete** | Exclusao logica: registros marcados como inativos, nunca removidos fisicamente. |
| **Autorizacao Contextual** | Permissoes dependem do contexto (professor->seus cursos, tutor->cursos vinculados, aluno->seus dados). |
| **Media Ponderada** | Cada quiz contribui proporcionalmente ao seu numero de questoes na nota final. |
| **Desbloqueio Sequencial** | Modulo acessivel somente apos completar 100% do anterior e aprovar seu quiz (quando existente). |

---

## Documentos Relacionados

- [Requisitos Nao Funcionais (detalhado)](requisitos-nao-funcionais.md)
- [Epicos e Historias de Usuario](../epicos/index.md)
- [Roadmap de Releases](../roadmap.md)
- [Riscos e Mitigacoes](../riscos.md)
- [Matriz de Rastreabilidade](../rastreabilidade.md)
- [High-Level Design](../hld.md)
- [Domain Context](../domain-context.md)

---

**Fim do Documento**
