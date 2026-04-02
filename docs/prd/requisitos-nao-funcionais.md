# Requisitos Nao Funcionais

> Documento principal: [PRD](index.md)

---

## Desempenho (Performance)

### NFR-001: Tempo de Resposta de API para Operacoes Padrao [MUST]

**Descricao:** As chamadas de API para operacoes de leitura e escrita padrao (listagem de cursos, carregamento de aulas de texto, submissao de quiz, registro de progresso) devem responder dentro de limites aceitaveis sob carga normal.

**Criterios de Aceitacao:** P95 do tempo de resposta <= 300 ms para operacoes de leitura; P95 <= 500 ms para operacoes de escrita (ex.: submissao de quiz, matricula).

**Metodo de Medicao:** Monitoramento de APM em producao com calculo de percentis sobre janelas de 5 minutos. Testes de carga com ferramenta (k6, JMeter) simulando 500 usuarios concorrentes.

---

### NFR-002: Upload de Arquivos de Midia [MUST]

**Descricao:** O sistema deve suportar upload direto de arquivos de midia (video/audio) de ate 1 GB sem timeout ou falha, conforme definido na entidade Lesson.

**Criterios de Aceitacao:** Upload de arquivo de 1 GB deve concluir com sucesso em ate 5 minutos em conexao de 100 Mbps. Taxa de falha de upload < 1% para arquivos ate 1 GB. O sistema deve suportar upload em partes (multipart/chunked) com retomada em caso de interrupcao.

**Metodo de Medicao:** Testes de upload automatizados com arquivos de 100 MB, 500 MB e 1 GB. Monitoramento da taxa de erro de uploads em producao via logs e metricas de objeto storage.

---

### NFR-003: Tempo de Carregamento de Pagina [MUST]

**Descricao:** As paginas da plataforma devem carregar rapidamente para garantir boa experiencia ao aluno durante o consumo de conteudo.

**Criterios de Aceitacao:** Largest Contentful Paint (LCP) <= 2,5 s. First Input Delay (FID) <= 100 ms. Cumulative Layout Shift (CLS) <= 0,1. Metricas medidas em conexao 4G simulada (1,6 Mbps).

**Metodo de Medicao:** Google Lighthouse em modo CI com scores coletados a cada deploy. Monitoramento de Web Vitals reais (RUM) em producao.

---

### NFR-004: Usuarios Concorrentes e Streaming de Midia [SHOULD]

**Descricao:** A plataforma deve suportar picos de usuarios concorrentes, especialmente em horarios de aula, mantendo estabilidade no streaming de video/audio.

**Criterios de Aceitacao:** O sistema deve suportar ao menos 2.000 usuarios concorrentes sem degradacao superior a 20% nos tempos de resposta de API. Streaming de midia deve iniciar reproducao (time to first byte) em <= 3 s para 95% das requisicoes. Taxa de buffering durante reproducao < 2% do tempo total.

**Metodo de Medicao:** Testes de carga com 2.000 usuarios virtuais concorrentes simulando navegacao, streaming e submissao de quizzes.

---

## Seguranca (Security)

### NFR-005: Autenticacao e Politica de Senhas [MUST]

**Descricao:** O sistema deve implementar autenticacao segura seguindo a politica de senhas definida na entidade User (minimo 8 caracteres, 1 maiuscula, 1 minuscula, 1 numero, 1 simbolo).

**Criterios de Aceitacao:** Senhas armazenadas com hashing bcrypt (ou equivalente) com custo minimo de 12. Bloqueio temporario de conta apos 5 tentativas de login falhas consecutivas (lockout de 15 minutos). Tokens de autenticacao (JWT ou equivalente) com expiracao maxima de 1 hora; refresh tokens com expiracao de 7 dias.

**Metodo de Medicao:** Testes automatizados validando rejeicao de senhas fracas. Auditoria de seguranca semestral. SAST integrada ao pipeline de CI.

---

### NFR-006: Autorizacao Contextual [MUST]

**Descricao:** O sistema deve garantir isolamento de dados conforme as regras de autorizacao contextual: professor so gerencia seus cursos (RN-017), tutor so acessa cursos vinculados (RN-018), aluno so acessa seus dados (RN-019). Desvinculacao de tutor remove acesso imediatamente (RN-024).

**Criterios de Aceitacao:** 100% das rotas protegidas devem validar autorizacao contextual. Zero ocorrencias de acesso nao autorizado em testes de penetracao. Latencia adicionada pela camada de autorizacao <= 20 ms no P99.

**Metodo de Medicao:** Suite de testes automatizados cobrindo 100% das combinacoes papel x recurso x operacao. Teste de penetracao anual.

---

### NFR-007: Protecao de Dados e Gestao de Sessao [MUST]

**Descricao:** O sistema deve proteger dados em transito e em repouso, preservar dados historicos conforme RN-022 (soft-delete) e gerenciar sessoes de forma segura.

**Criterios de Aceitacao:** Toda comunicacao via HTTPS/TLS 1.2 ou superior. Dados sensiveis (senhas, tokens) nunca expostos em logs ou respostas de API. Registros marcados como despublicados devem permanecer integros e auditaveis por tempo indeterminado. Sessoes invalidadas em ate 1 segundo apos bloqueio de usuario ou alteracao de papel.

**Metodo de Medicao:** Varredura automatica de vulnerabilidades (DAST) no pipeline de CI/CD. Auditoria de logs para deteccao de vazamento de dados sensiveis.

---

## Escalabilidade (Scalability)

### NFR-008: Crescimento de Usuarios [SHOULD]

**Descricao:** A arquitetura deve suportar crescimento horizontal da base de usuarios sem necessidade de re-arquitetura.

**Criterios de Aceitacao:** O sistema deve manter os SLAs de desempenho com ate 50.000 usuarios cadastrados e 5.000 usuarios ativos diarios. A adicao de capacidade (scale-out) deve ser possivel sem downtime, em ate 10 minutos.

**Metodo de Medicao:** Testes de carga trimestrais simulando crescimento projetado (10.000, 25.000, 50.000 usuarios).

---

### NFR-009: Escalabilidade de Armazenamento de Midia [MUST]

**Descricao:** O armazenamento de arquivos de midia deve escalar de forma independente, considerando arquivos de ate 1 GB cada e a natureza soft-delete do sistema (RN-022).

**Criterios de Aceitacao:** O sistema de armazenamento (object storage) deve suportar ate 10 TB de midia sem degradacao de performance. Tempo de recuperacao de arquivo (TTFB) <= 500 ms no P95. Custo otimizavel via politicas de tiering para midias de cursos despublicados.

**Metodo de Medicao:** Monitoramento de latencia de leitura do object storage. Relatorios mensais de volume armazenado e custos.

---

### NFR-010: Escalabilidade de Banco de Dados [SHOULD]

**Descricao:** O banco de dados deve manter performance aceitavel com o crescimento do volume de dados, considerando que registros nunca sao excluidos (soft-delete, RN-022).

**Criterios de Aceitacao:** Queries de listagem paginada em <= 100 ms no P95 com ate 10 milhoes de registros. Queries de calculo de progresso e notas (RN-006, RN-007, RN-014) em <= 200 ms no P95.

**Metodo de Medicao:** Monitoramento de slow queries (> 200 ms) em producao. Testes de performance com datasets sinteticos de 10M registros.

---

## Confiabilidade (Reliability)

### NFR-011: Disponibilidade e SLA [MUST]

**Descricao:** A plataforma deve manter alta disponibilidade, especialmente durante horarios de pico de uso educacional (18h-23h, dias uteis).

**Criterios de Aceitacao:** Disponibilidade geral >= 99,5% mensal (downtime maximo de ~3,6 horas/mes). Disponibilidade em horario de pico (18h-23h, seg-sex) >= 99,9%. RTO <= 1 hora. RPO <= 15 minutos.

**Metodo de Medicao:** Monitoramento de uptime com verificacoes a cada 1 minuto via servico externo. Relatorio mensal de disponibilidade.

---

### NFR-012: Backup, Recuperacao e Tolerancia a Falhas [MUST]

**Descricao:** O sistema deve garantir integridade e recuperabilidade dos dados, com tolerancia a falhas parciais. Especialmente critico dado que dados historicos devem ser preservados indefinidamente (RN-022).

**Criterios de Aceitacao:** Backups automaticos do banco de dados a cada 6 horas, retencao de 30 dias. Backup completo semanal com retencao de 12 meses. Restauracao em <= 30 minutos. Calculos automaticos (expiracao, progresso, notas, certificados) com retry de no minimo 3 tentativas.

**Metodo de Medicao:** Testes de restauracao mensais. Monitoramento de jobs de backup. Testes de caos trimestrais.

---

## Usabilidade (Usability)

### NFR-013: Acessibilidade [SHOULD]

**Descricao:** A plataforma deve ser acessivel a usuarios com deficiencias, seguindo padroes internacionais de acessibilidade.

**Criterios de Aceitacao:** Conformidade com WCAG 2.1 nivel AA em paginas publicas e areas do aluno. Score de acessibilidade >= 90 no Google Lighthouse. Todos os elementos interativos acessiveis via teclado.

**Metodo de Medicao:** Auditoria automatizada (axe-core) integrada ao CI. Auditoria manual de acessibilidade semestral.

---

### NFR-014: Responsividade e Internacionalizacao [MUST]

**Descricao:** A plataforma deve funcionar adequadamente em dispositivos moveis e ter Portugues Brasileiro como idioma primario, com arquitetura preparada para internacionalizacao futura.

**Criterios de Aceitacao:** Layout responsivo funcional de 320px a 2560px. Todas funcionalidades do aluno acessiveis em dispositivos moveis. 100% das strings de interface em pt-BR. Estrutura de i18n implementada.

**Metodo de Medicao:** Testes automatizados de layout responsivo em breakpoints: 320px, 768px, 1024px, 1440px, 2560px.

---

## Manutenibilidade (Maintainability)

### NFR-015: Qualidade de Codigo, Testes e Observabilidade [MUST]

**Descricao:** O codigo deve manter padroes de qualidade que facilitem manutencao, evolucao e diagnostico de problemas em producao.

**Criterios de Aceitacao:** Cobertura de testes >= 80% em modulos de regras de negocio (notas, progresso, expiracao, certificados). Cobertura geral >= 60%. Zero vulnerabilidades criticas ou altas em SAST no deploy. Logs estruturados (JSON) com request_id, user_id, papel, recurso, acao, status HTTP e duracao. Dashboards de observabilidade cobrindo taxa de erro, latencia por percentil, taxa de sucesso de calculos automaticos. Alertas para disponibilidade < SLA, taxa de erro > 1%, latencia P95 > 2x limite.

**Metodo de Medicao:** Relatorio de cobertura no CI. SAST a cada pull request. Revisao mensal de dashboards.

---

## Matriz Resumo

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
