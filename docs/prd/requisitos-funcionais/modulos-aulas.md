# Requisitos Funcionais: Gestao de Modulos e Aulas (Module & Lesson)

> Detalhamento dos requisitos FR-010 a FR-014.
> Documento principal: [PRD](../index.md)

---

## FR-010: Criacao e Ordenacao de Modulos [MUST]

**Descricao:** O Professor criador (ou Admin) deve poder criar modulos dentro de um curso, mantendo ordenacao contigua e sequencial.

**Criterios de Aceitacao:**
1. O professor criador consegue adicionar modulos ao seu curso com titulo (ate 150 caracteres) e ordem sequencial.
2. A ordem dos modulos e automaticamente ajustada para manter sequencia contigua (1, 2, 3...) ao adicionar ou reordenar.
3. O sistema rejeita a criacao de modulo com titulo vazio ou com mais de 150 caracteres.
4. Apenas o professor criador do curso e o Admin conseguem criar modulos no curso.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-017, Validacoes Transversais (ordenacao)
**Epico Relacionado:** EPIC-003

---

## FR-011: Criacao e Gestao de Aulas [MUST]

**Descricao:** O Professor criador (ou Admin) deve poder criar aulas dentro de modulos, suportando conteudo em texto, video ou audio.

**Criterios de Aceitacao:**
1. O professor consegue criar uma aula do tipo "texto" com conteudo de ate 50.000 caracteres.
2. O professor consegue criar uma aula do tipo "video" ou "audio" com upload de arquivo de ate 1 GB.
3. O sistema rejeita a criacao de aula do tipo "texto" sem conteudo textual, e de aula do tipo "video"/"audio" sem arquivo de midia.
4. A duracao estimada em minutos e opcional; quando informada, deve ser um inteiro positivo.
5. O titulo da aula e obrigatorio e limitado a 150 caracteres.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-017, RN-003
**Epico Relacionado:** EPIC-003

---

## FR-012: Desbloqueio Sequencial de Modulos [MUST]

**Descricao:** O sistema deve liberar modulos sequencialmente: o proximo modulo so e desbloqueado quando o anterior tiver 100% de progresso e quiz aprovado (se houver quiz).

**Criterios de Aceitacao:**
1. O primeiro modulo de um curso esta sempre desbloqueado para o aluno matriculado.
2. O modulo N+1 e desbloqueado somente quando o modulo N possui 100% de aulas completas e quiz aprovado (se existir quiz no modulo N).
3. Para modulos sem quiz, o desbloqueio do modulo seguinte exige apenas 100% de progresso nas aulas.
4. O aluno recebe indicacao visual clara de quais modulos estao bloqueados e desbloqueados.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-002
**Epico Relacionado:** EPIC-003

---

## FR-013: Marcacao de Conclusao de Aula [MUST]

**Descricao:** O Aluno deve poder marcar uma aula como concluida, respeitando a regra de tempo minimo para aulas com duracao estimada.

**Criterios de Aceitacao:**
1. O aluno consegue marcar uma aula como concluida apos permanecer por pelo menos 70% da duracao estimada da aula.
2. Aulas sem duracao estimada definida podem ser marcadas como concluidas imediatamente.
3. O sistema rejeita a tentativa de marcar uma aula como concluida antes de atingir 70% da duracao estimada.
4. O progresso do aluno no modulo e atualizado automaticamente apos a marcacao.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-003
**Epico Relacionado:** EPIC-003

---

## FR-014: Despublicacao de Modulo e Aula [SHOULD]

**Descricao:** O Professor criador (ou Admin) deve poder despublicar modulos e aulas individualmente, sem afetar alunos ja matriculados.

**Criterios de Aceitacao:**
1. O professor criador consegue despublicar um modulo ou aula especifica.
2. Alunos ja matriculados continuam tendo acesso ao conteudo despublicado.
3. Novos alunos matriculados apos a despublicacao nao veem o conteudo despublicado.
4. Aulas despublicadas sao removidas do calculo de progresso para novos alunos que se matricularem apos a despublicacao.

**Prioridade:** SHOULD
**Regras de Negocio Relacionadas:** RN-015, RN-022
**Epico Relacionado:** EPIC-003
