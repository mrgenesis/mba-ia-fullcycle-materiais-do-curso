# Requisitos Funcionais: Gestao de Usuarios (User Management)

> Detalhamento dos requisitos FR-001 a FR-004.
> Documento principal: [PRD](../index.md)

---

## FR-001: Cadastro de Usuario [MUST]

**Descricao:** O sistema deve permitir o cadastro de novos usuarios com nome, email e senha, validando todas as restricoes de formato e unicidade.

**Criterios de Aceitacao:**
1. O cadastro e concluido com sucesso quando nome (ate 100 caracteres), email (ate 255 caracteres, formato valido, unico) e senha (minimo 8 caracteres com 1 maiuscula, 1 minuscula, 1 numero e 1 simbolo) sao fornecidos corretamente.
2. O sistema rejeita o cadastro e retorna mensagem de erro especifica quando o email ja esta em uso.
3. O sistema rejeita o cadastro e retorna mensagem de erro especifica quando a senha nao atende a politica de complexidade.
4. O usuario cadastrado recebe o papel padrao de "aluno" e situacao "ativo".

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-023, Validacoes Transversais (formato de email)
**Epico Relacionado:** EPIC-001

---

## FR-002: Autenticacao de Usuario [MUST]

**Descricao:** O sistema deve permitir que usuarios ativos realizem login com email e senha e obtenham uma sessao autenticada.

**Criterios de Aceitacao:**
1. O login e concluido com sucesso quando email e senha correspondem a um usuario com situacao "ativo".
2. O sistema rejeita o login e retorna mensagem de erro quando as credenciais sao invalidas.
3. O sistema rejeita o login e retorna mensagem de erro quando o usuario esta com situacao "bloqueado".
4. A sessao autenticada identifica o usuario e seus papeis para controle de autorizacao.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-019, RN-021
**Epico Relacionado:** EPIC-001

---

## FR-003: Gestao de Papeis de Usuario [MUST]

**Descricao:** O Admin deve poder atribuir e remover papeis (aluno, professor, tutor, admin) a qualquer usuario, permitindo multiplos papeis simultaneos.

**Criterios de Aceitacao:**
1. O Admin consegue adicionar um ou mais papeis a um usuario existente, e os novos papeis ficam imediatamente disponiveis.
2. O Admin consegue remover papeis de um usuario, desde que o usuario mantenha ao menos um papel.
3. Um usuario com multiplos papeis consegue acessar as funcionalidades correspondentes a cada papel atribuido.
4. Usuarios sem papel de Admin nao conseguem alterar papeis de nenhum usuario.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-023
**Epico Relacionado:** EPIC-001

---

## FR-004: Bloqueio e Desbloqueio de Usuario [MUST]

**Descricao:** O Admin deve poder bloquear e desbloquear usuarios. Um professor bloqueado nao pode editar ou criar conteudo, mas seus cursos publicados continuam acessiveis.

**Criterios de Aceitacao:**
1. O Admin consegue alterar a situacao de um usuario de "ativo" para "bloqueado", e o usuario perde acesso imediatamente ao fazer nova requisicao.
2. O Admin consegue alterar a situacao de um usuario de "bloqueado" para "ativo", restaurando o acesso.
3. Um professor bloqueado nao consegue criar, editar ou publicar cursos enquanto estiver bloqueado.
4. Os cursos publicados de um professor bloqueado permanecem acessiveis para alunos matriculados.
5. Usuarios sem papel de Admin nao conseguem bloquear ou desbloquear outros usuarios.

**Prioridade:** MUST
**Regras de Negocio Relacionadas:** RN-021, RN-023
**Epico Relacionado:** EPIC-001
