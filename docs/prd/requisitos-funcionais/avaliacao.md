# Requisitos Funcionais: Avaliacao (Assessment)

> Detalhamento dos requisitos FR-015 a FR-019.
> Documento principal: [PRD](../index.md)

---

## FR-015: Criacao e Configuracao de Quiz [MUST]

**Descricao:** O Professor criador (ou Admin) deve poder criar quizzes vinculados a modulos, definindo nota minima de aprovacao e maximo de tentativas.

**Criterios de Aceitacao:**
1. O professor consegue criar um quiz com nota minima de aprovacao entre 0 e 100.
2. O professor consegue definir um numero maximo de tentativas (inteiro positivo) ou deixar como ilimitado (campo nao preenchido).
3. O quiz deve possuir ao menos 1 questao para ser considerado valido.
4. Apenas o professor criador do curso (ou Admin) consegue criar e configurar quizzes.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-004, RN-005, RN-017
**Epico Relacionado:** EPIC-004

---

## FR-016: Criacao de Questoes e Opcoes [MUST]

**Descricao:** O Professor criador (ou Admin) deve poder criar questoes com enunciado, ordem e opcoes de resposta (minimo 4 opcoes, exatamente 1 correta).

**Criterios de Aceitacao:**
1. O professor consegue criar questoes com enunciado de ate 2000 caracteres e ordem sequencial contigua.
2. Cada questao deve ter ao menos 4 opcoes de resposta com texto de ate 500 caracteres cada.
3. O sistema rejeita a criacao de questao que nao tenha exatamente 1 opcao marcada como correta.
4. A ordem das questoes no quiz e mantida contigua e sequencial (1, 2, 3...).

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-004, Validacoes Transversais (ordenacao)
**Epico Relacionado:** EPIC-004

---

## FR-017: Realizacao de Tentativa de Quiz [MUST]

**Descricao:** O Aluno deve poder realizar tentativas de quiz, selecionando respostas para cada questao, com calculo automatico de nota.

**Criterios de Aceitacao:**
1. O aluno consegue iniciar uma tentativa de quiz e selecionar uma resposta para cada questao.
2. A nota e calculada automaticamente como (respostas corretas / total de questoes) * 100 ao submeter a tentativa.
3. O aluno e informado se foi aprovado ou reprovado com base na nota minima configurada no quiz.
4. A data da tentativa e registrada automaticamente.
5. O aluno nao consegue iniciar nova tentativa quando ja esgotou o maximo de tentativas permitidas.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-004, RN-005
**Epico Relacionado:** EPIC-004

---

## FR-018: Bloqueio e Reabertura de Tentativas de Quiz [MUST]

**Descricao:** O sistema deve bloquear o aluno que esgotou o maximo de tentativas. O Tutor vinculado ao curso deve poder reabrir tentativas para o aluno.

**Criterios de Aceitacao:**
1. O aluno que esgotou todas as tentativas de um quiz nao consegue iniciar nova tentativa e recebe mensagem informativa.
2. O Tutor vinculado ao curso consegue reabrir tentativas para um aluno bloqueado em um quiz especifico.
3. Apos a reabertura, o aluno consegue realizar novas tentativas ate o limite configurado.
4. Um Tutor nao vinculado ao curso nao consegue reabrir tentativas para alunos desse curso.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-005, RN-018, RN-024
**Epico Relacionado:** EPIC-004

---

## FR-019: Calculo de Nota Final do Curso [MUST]

**Descricao:** O sistema deve calcular a nota final do curso como media ponderada das melhores notas de cada quiz, usando o numero de questoes como peso.

**Criterios de Aceitacao:**
1. A nota final do curso considera a melhor nota obtida pelo aluno em cada quiz do curso.
2. O peso de cada quiz no calculo e igual ao numero de questoes do quiz.
3. Modulos sem quiz nao entram no calculo da nota final.
4. Cursos sem nenhum quiz nao possuem nota final (campo nulo ou nao aplicavel).

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-006
**Epico Relacionado:** EPIC-004
