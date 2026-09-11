# Etapa 2 — Modelagem Conceitual: ER e Normalização (1NF-3NF)
 
## 1. Diagrama entidade-relacionamento (ER)
 
- **Entidade**: uma "coisa" do domínio que vai virar tabela (Usuário, Livro, Pedido).
- **Atributo**: uma característica da entidade que vai virar coluna (nome, data de nascimento).
- **Relacionamento**: como duas entidades se conectam (Usuário *empresta* Livro).
- **Cardinalidade**: quantas instâncias de uma entidade se relacionam com quantas da outra:
  - **1:1** — um registro de A se relaciona com no máximo um de B (ex: Usuário e Perfil, se Perfil for uma tabela separada por organização).
  - **1:N** — um registro de A se relaciona com vários de B, mas cada B só com um A (ex: um Autor escreve vários Livros, mas cada Livro na sua modelagem simplificada tem um Autor só).
  - **N:N** — vários registros de A se relacionam com vários de B (ex: Livro e Autor de verdade — um livro pode ter vários autores, um autor pode ter vários livros).

  ---

  ### Por que N:N não pode ser resolvido com FK direta
 
Numa relação 1:N, a FK fica na tabela "N" (ex: `livro.autor_id`). Numa relação N:N, não existe onde colocar essa FK — nenhum dos dois lados aguenta referenciar "vários" com uma coluna só. A solução é criar uma **tabela associativa** (também chamada tabela de junção): uma terceira tabela que tem só duas FKs, uma pra cada lado, e a combinação das duas costuma ser a própria PK composta dessa tabela.
 
Exemplo: `livro_autor` com colunas `livro_id` e `autor_id`, ambas FK, PK composta pelas duas.
 