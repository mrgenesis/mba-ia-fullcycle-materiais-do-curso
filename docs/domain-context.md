---
**Nome do Projeto:** CodeLearn Platform

**Descrição:** Plataforma de cursos online que permite a criação, gestão e consumo de conteúdo educacional organizado em cursos, módulos, aulas, quizzes e trilhas de aprendizado.
---

**Personas:**

**Aluno (Student):** Consumidor principal. Busca cursos, se matricula, assiste aulas, realiza quizzes, acompanha progresso e obtém certificados. Só vê cursos publicados. Não acessa dados de outros alunos.

**Professor (Teacher):** Criador de conteúdo. Cria e edita seus próprios cursos, módulos, aulas e quizzes. Publica e despublica conteúdo. Vê métricas dos seus cursos (conclusão, evasão, notas). Não acessa cursos de outros professores. Não gerencia usuários nem trilhas.

**Tutor:** Suporte pedagógico vinculado a cursos específicos. Vê progresso e notas dos alunos dos cursos que tutora. Pode reabrir tentativas de quiz. Não edita conteúdo. Não acessa cursos aos quais não está vinculado.

**Admin:** Gestão global. Cria e gerencia usuários (criar, bloquear, alterar papéis). Cria e gerencia trilhas de aprendizado. Vincula tutores a cursos. Visualiza e edita qualquer recurso. Acessa métricas globais.

---

**Entidades:**

- **User** — Representa qualquer usuário da plataforma
  - nome (obrigatório, até 100 caracteres)
  - email (obrigatório, único, até 255 caracteres)
  - senha (obrigatório, mínimo 8 caracteres contendo ao menos 1 maiúscula, 1 minúscula, 1 número e 1 símbolo)
  - papéis (obrigatório, um ou mais de: aluno / professor / tutor / admin)
  - situação (obrigatório: ativo ou bloqueado)

- **Course** — Representa um curso. Todo curso é criado por um professor
  - título (obrigatório, até 150 caracteres)
  - descrição (obrigatória, até 2000 caracteres)
  - situação (obrigatório: rascunho ou publicado)
  - período de acesso em dias (obrigatório, número inteiro)
  - período de carência em dias (obrigatório, número inteiro)
  - professor criador (obrigatório)

- **Module** — Representa um módulo dentro de um curso
  - título (obrigatório, até 150 caracteres)
  - ordem no curso (obrigatória, número inteiro, contígua e sequencial)

- **Lesson** — Representa uma aula dentro de um módulo
  - título (obrigatório, até 150 caracteres)
  - tipo de conteúdo (obrigatório: texto, vídeo ou áudio)
  - conteúdo texto (obrigatório se tipo = texto, até 50.000 caracteres)
  - arquivo de mídia (obrigatório se tipo = vídeo ou áudio, upload direto na plataforma, até 1 GB)
  - duração estimada em minutos (opcional, número inteiro)

- **Quiz** — Avaliação vinculada a um módulo. Cada módulo pode ter no máximo um quiz
  - nota mínima para aprovação (obrigatória, número de 0 a 100)
  - máximo de tentativas (opcional — ilimitado quando não definido)
  - deve ter ao menos 1 questão

- **Question** — Pergunta dentro de um quiz
  - enunciado (obrigatório, até 2000 caracteres)
  - ordem no quiz (obrigatória, contígua e sequencial)
  - deve ter ao menos 4 opções, exatamente 1 correta

- **Option** — Alternativa de resposta dentro de uma questão
  - texto (obrigatório, até 500 caracteres)
  - indica se é a correta (obrigatório, sim ou não)

- **Enrollment** — Representa a matrícula de um aluno em um curso
  - situação (obrigatória: ativa, concluída, expirada ou cancelada)
  - data de expiração (obrigatória, calculada automaticamente a partir da data da matrícula + período de acesso do curso)
  - data de conclusão (preenchida automaticamente ao concluir o curso)

- **Progress** — Registra o avanço de um aluno em cada aula
  - data de início (obrigatória, preenchida automaticamente ao iniciar a aula)
  - data de conclusão (preenchida automaticamente ao completar a aula)

- **QuizAttempt** — Registra cada tentativa de um aluno em um quiz
  - nota obtida (obrigatória, calculada automaticamente)
  - respostas selecionadas (obrigatório)
  - data da tentativa (obrigatória, preenchida automaticamente)

- **CourseTutor** — Vincula tutores aos cursos que acompanham. Um tutor pode acompanhar vários cursos e um curso pode ter vários tutores

- **CoursePrerequisite** — Define quais cursos precisam ser concluídos antes de permitir matrícula em outro curso

- **LearningPath** — Agrupa cursos em uma trilha de aprendizado
  - título (obrigatório, até 150 caracteres)
  - descrição (obrigatória, até 2000 caracteres)

- **LearningPathCourse** — Define a ordem de cada curso dentro de uma trilha
  - ordem na trilha (obrigatória, contígua e sequencial)

- **Certificate** — Emitido ao aluno ao concluir um curso ou uma trilha
  - tipo (obrigatório: curso ou trilha)
  - data de emissão (obrigatória, preenchida automaticamente)

---

**Regras de Negócio:**

**Pré-requisitos e Desbloqueio:**
- RN-001: Curso pode exigir conclusão de outros cursos como pré-requisito para matrícula. Pré-requisitos circulares devem ser impedidos.
- RN-002: Módulo só desbloqueia quando módulo anterior tem 100% de progresso E quiz aprovado. Primeiro módulo sempre desbloqueado. Módulo sem quiz só exige 100% de progresso.
- RN-003: Aula só pode ser marcada como completa após 70% da duração estimada. Aulas sem duração definida podem ser marcadas imediatamente.

**Avaliação:**
- RN-004: Quiz define nota mínima para aprovação. Nota = (corretas / total) * 100. Aluno só avança se aprovado.
- RN-005: Quiz define máximo de tentativas. Esgotadas, aluno fica bloqueado. Tutor vinculado pode reabrir. Quando o limite não é definido, as tentativas são ilimitadas.
- RN-006: Nota final do curso = média ponderada das melhores notas de cada quiz (peso = número de questões). Módulos sem quiz não entram no cálculo. Curso sem nenhum quiz não possui nota.

**Trilhas:**
- RN-007: Progresso da trilha = média dos progressos dos cursos da trilha. Cursos não iniciados contam como 0%.
- RN-008: Certificado da trilha só emitido quando o aluno concluiu todos os cursos da trilha. Emissão automática ao completar o último.
- RN-009: Trilha define ordem recomendada mas não obriga sequência (a menos que cursos tenham pré-requisitos via RN-001).

**Acesso e Expiração:**
- RN-010: Matrícula tem validade. A data de expiração é calculada a partir da data da matrícula somada ao período de acesso definido no curso.
- RN-011: Após expiração, inicia-se o período de carência definido no curso. Durante esse período, o aluno pode ver o conteúdo mas não avança progresso nem faz quiz. Após o período de carência, o acesso é bloqueado e a matrícula é marcada como expirada.
- RN-012: Renovação estende a data de expiração a partir da data atual. Progresso e notas mantidos.

**Matrícula e Progresso:**
- RN-013: Matrícula só em cursos publicados. Não é permitido ter duas matrículas ativas para o mesmo aluno no mesmo curso. Rematrícula é permitida se a matrícula anterior estiver expirada ou cancelada.
- RN-014: Progresso do curso = (aulas completadas / total de aulas) * 100. Quando o progresso atinge 100% e todos os quizzes estão aprovados, a matrícula é marcada como concluída e o certificado é emitido automaticamente.

**Professor e Conteúdo:**
- RN-015: Despublicar módulo ou aula não afeta alunos já matriculados (mantêm acesso). Novos alunos não veem conteúdo despublicado. Aulas despublicadas saem do cálculo de progresso para novos alunos. Esta regra cobre módulos e aulas individuais — para despublicação do curso inteiro, ver RN-020.
- RN-016: Métricas do professor: taxa de conclusão por curso, evasão por módulo (% que parou ali), nota média dos concluintes.

**Autorização Contextual:**
- RN-017: Professor só pode gerenciar cursos que ele próprio criou.
- RN-018: Tutor só acessa cursos aos quais foi vinculado.
- RN-019: Aluno só acessa seus próprios dados e matrículas.

**Gestão de Conteúdo e Usuários:**
- RN-020: Quando um curso inteiro é despublicado, alunos matriculados mantêm acesso por até 30 dias após a despublicação. Após esse prazo, o acesso é bloqueado. Novas matrículas são impedidas imediatamente.
- RN-021: Quando um professor é bloqueado, seus cursos publicados continuam acessíveis aos alunos. O professor não pode editar nem criar conteúdo enquanto estiver bloqueado.
- RN-022: Conteúdo (cursos, módulos, aulas, quizzes) não pode ser excluído, apenas despublicado. Dados históricos são sempre preservados.
- RN-023: Usuário pode ter múltiplos papéis simultaneamente, sem restrição de combinação. Admin gerencia a atribuição de papéis.
- RN-024: Desvinculação de tutor de um curso remove seu acesso imediatamente.

---

**Validações Transversais:**

- Email deve ter formato válido.
- Toda ordenação (módulos no curso, questões no quiz, cursos na trilha) deve ser contígua e sequencial (1, 2, 3...).

---