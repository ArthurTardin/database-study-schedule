# [![My Skills](https://skillicons.dev/icons?i=postgres)](https://skillicons.dev) Roadmap de Banco de Dados / SQL — Do Zero ao Avançado
 
---

## Etapa 1 — Modelo relacional e por que ele existe
 
**Conteúdo:**
- O que é um banco relacional vs arquivo/planilha vs banco não-relacional (conceito, não implementação)
- Tabela, linha, coluna, chave primária, chave estrangeira, domínio de dados
- Por que dados duplicados são um problema (anomalias de inserção, atualização, remoção)
- Álgebra relacional na prática: o que uma query "realmente" faz (seleção, projeção, junção — sem termo formal ainda, só o conceito)
**Exercício obrigatório:**
Instalar PostgreSQL local (ou via Docker) e conectar via `psql`. Criar um banco vazio. Rodar `\l`, `\dt`, `\d` e mandar os outputs reais.
 
**Perguntas de verificação:**
1. Por que armazenar o mesmo dado em duas tabelas diferentes sem ligação é um problema prático, não só teórico?
2. O que diferencia uma chave primária de uma coluna qualquer que também tem valores únicos?

---

## Etapa 2 — Modelagem conceitual: ER e normalização (1NF-3NF)
 
**Conteúdo:**
- Diagrama entidade-relacionamento: entidade, atributo, relacionamento, cardinalidade (1:1, 1:N, N:N)
- Primeira, segunda e terceira forma normal — o que cada uma resolve, não só a definição
- Quando modelar N:N exige tabela associativa
**Exercício obrigatório:**
Modelar (papel ou ferramenta tipo dbdiagram.io) um mini sistema de biblioteca: livros, autores, empréstimos, usuários. Deve ter pelo menos um N:N. Mandar o diagrama ou a descrição textual completa das tabelas com PKs/FKs.
 
**Perguntas de verificação:**
1. Por que "autor" e "livro" precisam de tabela associativa e não de uma FK direta em um dos dois?
2. Que anomalia concreta aparece se você deixar sua modelagem em 1NF sem ir pra 2NF?
3. Dê um caso onde desnormalizar de propósito seria defensável (mesmo sem ter estudado performance ainda).

---

## Etapa 3 — DDL: criar e alterar estrutura
 
**Conteúdo:**
- `CREATE TABLE`, tipos de dados (INTEGER, VARCHAR, TEXT, NUMERIC, BOOLEAN, DATE/TIMESTAMP)
- `ALTER TABLE` (add/drop column, rename, alterar tipo)
- `DROP TABLE`, `TRUNCATE` — diferença real entre os dois
- `PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `DEFAULT` na criação da tabela
**Exercício obrigatório:**
Implementar em SQL real (rodando no seu Postgres) o modelo da Etapa 2. Depois, alterar uma tabela já criada (adicionar coluna, mudar tipo) sem dropar nada. Mandar os `CREATE TABLE` e `ALTER TABLE` reais executados.
 
**Perguntas de verificação:**
1. Por que `TRUNCATE` pode falhar numa tabela com FK apontando pra ela, e `DELETE` não tem esse problema do mesmo jeito?
2. O que acontece de fato quando você tenta `ALTER TABLE ... ALTER COLUMN` mudando tipo incompatível com dados já existentes?

---

## Etapa 4 — DML básico: INSERT, UPDATE, DELETE, SELECT simples
 
**Conteúdo:**
- `INSERT INTO` (com e sem lista de colunas, múltiplas linhas)
- `UPDATE` com `WHERE` — e o risco de esquecer o `WHERE`
- `DELETE` com `WHERE` — mesmo risco
- `SELECT` simples, `SELECT *` vs colunas nomeadas, `AS` (alias)
**Exercício obrigatório:**
Popular as tabelas da Etapa 3 com pelo menos 10 linhas por tabela via `INSERT`. Fazer um `UPDATE` e um `DELETE` condicionais. Mandar os comandos reais e o resultado de um `SELECT` antes/depois pra provar que funcionou.
 
**Perguntas de verificação:**
1. Por que `SELECT *` é considerado má prática em código de produção, mesmo funcionando igual a listar as colunas?
2. Se você rodar `UPDATE tabela SET coluna = valor` sem `WHERE`, o que exatamente acontece — e por quê o banco não te impede por padrão?

---

## Etapa 5 — Filtros e operadores
 
**Conteúdo:**
- Operadores de comparação, `AND`/`OR`/`NOT`, precedência entre eles
- `BETWEEN`, `IN`, `LIKE`/`ILIKE` (com `%` e `_`)
- `NULL`: por que `= NULL` não funciona, `IS NULL` / `IS NOT NULL`, comportamento de NULL em comparações e em `AND`/`OR` (lógica de três valores)
**Exercício obrigatório:**
Escrever 5 queries `SELECT` com filtros compostos (mínimo 2 condições cada, misturando `AND`/`OR`) contra os dados da Etapa 4, incluindo pelo menos uma que trate `NULL` explicitamente.
 
**Perguntas de verificação:**
1. Por que `WHERE coluna != 'x'` pode "sumir" com linhas que você esperava ver, se `coluna` tiver NULL nelas?
2. Explique por que `NOT (A AND B)` não é sempre igual a `NOT A AND NOT B` em SQL, dado um exemplo com dados reais seus.

---

## Etapa 6 — Ordenação e agregação
 
**Conteúdo:**
- `ORDER BY` (múltiplas colunas, ASC/DESC, NULLS FIRST/LAST)
- `LIMIT`/`OFFSET` (paginação)
- Funções de agregação: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`
- `GROUP BY` e a regra de ouro: toda coluna no `SELECT` que não é agregada precisa estar no `GROUP BY`
- `HAVING` vs `WHERE` — em que momento cada um filtra
**Exercício obrigatório:**
Query que agrupa empréstimos por usuário, conta quantos cada um tem, e só mostra usuários com mais de N empréstimos (usando `HAVING`). Mandar comando real + resultado.
 
**Perguntas de verificação:**
1. Por que você não pode usar `HAVING` no lugar de `WHERE` pra filtrar linhas individuais antes do agrupamento, mesmo quando o resultado final parece igual em um caso específico?
2. Se você faz `GROUP BY` por uma coluna mas seleciona outra coluna não-agregada que não está no `GROUP BY`, por que o Postgres recusa e outros bancos (MySQL antigo) aceitavam com resultado arbitrário?

---

## Etapa 7 — JOINs
 
**Conteúdo:**
- `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN` — o que cada um preserva
- `CROSS JOIN` e quando ele aparece "por acidente" (esquecer condição de join)
- Self join (ex: hierarquia funcionário-gerente)
- Múltiplos JOINs em uma query só
**Exercício obrigatório (debug primeiro):**
Vou te mandar uma query com JOIN quebrado (resultado errado, não erro de sintaxe) numa próxima interação — você vai ter que identificar por que o resultado está errado antes de eu explicar. Enquanto isso: escreva uma query com 3 tabelas joinadas (livro, autor, empréstimo) usando `LEFT JOIN` pra listar todos os livros mesmo sem empréstimo.
 
**Perguntas de verificação:**
1. Por que um `INNER JOIN` pode fazer você "perder" linhas que existiam nas tabelas originais, mesmo sem nenhum erro na query?
2. Num `LEFT JOIN`, o que aparece nas colunas da tabela da direita quando não há correspondência — e por que isso quebra silenciosamente uma agregação `COUNT(coluna_da_direita)` se você não souber disso?

---

## Etapa 8 — Subqueries
 
**Conteúdo:**
- Subquery escalar (retorna um valor), em `WHERE`, `SELECT`, `FROM`
- Subquery correlacionada (referencia a query externa) vs não-correlacionada
- `EXISTS`/`NOT EXISTS` vs `IN`/`NOT IN` — diferença de comportamento com NULL e de performance conceitual
- Quando uma subquery pode e deve ser reescrita como JOIN
**Exercício obrigatório:**
Escrever a mesma pergunta de negócio ("usuários que nunca fizeram empréstimo") usando três abordagens: `NOT IN`, `NOT EXISTS`, e `LEFT JOIN ... WHERE IS NULL`. Mandar as três queries reais e confirmar se dão o mesmo resultado.
 
**Perguntas de verificação:**
1. Por que `NOT IN` pode dar resultado errado (vazio) se a subquery retornar algum NULL, enquanto `NOT EXISTS` não tem esse problema?
2. Em que situação concreta uma subquery correlacionada é inevitável e não pode ser trivialmente reescrita como JOIN?

---

## Etapa 9 — Set operations
 
**Conteúdo:**
- `UNION` vs `UNION ALL` — custo e comportamento de duplicatas
- `INTERSECT`, `EXCEPT`
- Regra de compatibilidade de colunas entre as queries combinadas
**Exercício obrigatório:**
Duas queries que retornam listas de usuários por critérios diferentes, combinadas com `UNION` e depois com `INTERSECT`, mostrando resultado diferente de cada uma.
 
**Perguntas de verificação:**
1. Por que `UNION ALL` é sempre mais rápido que `UNION`, em termos do que o banco precisa fazer internamente?
2. Dê um cenário de negócio real onde usar `UNION` no lugar de `UNION ALL` causaria um bug silencioso.

---

## Checkpoint de integração 1 (Etapas 1-9)
 
Antes de seguir pra Etapa 10, você vai resolver um exercício que obriga a usar tudo isso junto: modelagem própria (não a de biblioteca), populada, com uma query final que combina JOIN + agregação + subquery + filtro de NULL. Sem isso você não passa pra Etapa 10, mesmo que tenha "entendido" cada etapa isoladamente.

---

## Etapa 10 — Views
 
**Conteúdo:**
- `CREATE VIEW` — o que é (query salva, não dado duplicado)
- Quando uma view ajuda (abstração, segurança de coluna) e quando ela só esconde complexidade sem resolver nada
- `CREATE OR REPLACE VIEW`, `DROP VIEW`
- Materialized view (conceito — Postgres tem `MATERIALIZED VIEW`) e a diferença de trade-off com view normal
**Exercício obrigatório:**
Criar uma view que encapsula a query do Checkpoint 1. Consultar a view como se fosse tabela. Mandar comando de criação e uma consulta na view.
 
**Perguntas de verificação:**
1. Por que uma view não resolve problema de performance de uma query lenta, mesmo parecendo "mais rápida" de usar?
2. Em que caso uma materialized view seria escolha melhor que view normal — e qual o preço que você paga por isso?

---

## Etapa 11 — Constraints avançadas
 
**Conteúdo:**
- `UNIQUE`, `CHECK`, `NOT NULL` além da PK
- `FOREIGN KEY` com `ON DELETE CASCADE / SET NULL / RESTRICT / NO ACTION` — diferença real de comportamento
- Constraint nomeada vs anônima (por que nomear importa pra debug)
**Exercício obrigatório (debug):**
Você vai receber uma tabela com FK sem `ON DELETE` definido, e vai ter que decidir e justificar qual política aplicar pra cada relação do seu próprio modelo (não aceito "CASCADE em tudo" sem justificativa por relação).
 
**Perguntas de verificação:**
1. Por que `ON DELETE CASCADE` pode ser uma bomba-relógio numa relação que parece inofensiva à primeira vista?
2. Qual a diferença prática entre `RESTRICT` e `NO ACTION` — e por que a maioria dos devs nunca percebe a diferença?

---

## Etapa 12 — Transações e ACID
 
**Conteúdo:**
- `BEGIN`, `COMMIT`, `ROLLBACK`
- ACID: atomicidade, consistência, isolamento, durabilidade — o que cada letra garante de fato, com exemplo de falha se não existisse
- Isolation levels (`READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`) e os fenômenos que cada um evita/permite: dirty read, non-repeatable read, phantom read
- Deadlock — o que é, por que acontece
**Exercício obrigatório:**
Abrir duas sessões `psql` simultâneas. Numa, iniciar transação, fazer `UPDATE` sem commitar. Na outra, tentar ler/alterar a mesma linha. Documentar o que aconteceu em cada isolation level testado (mínimo 2 níveis).
 
**Perguntas de verificação:**
1. Por que "atomicidade" não significa "rápido", e sim algo completamente diferente — explique com um exemplo do seu próprio teste.
2. No teste que você fez, o que teria acontecido diferente em `SERIALIZABLE` comparado a `READ COMMITTED`?
3. Descreva um deadlock hipotético entre duas transações no seu modelo de dados — duas queries específicas que se travam mutuamente.

---

## Etapa 13 — Índices e leitura de plano de execução
 
**Conteúdo:**
- O que um índice realmente é (B-tree, na prática — não implementação interna, mas o conceito de busca ordenada vs full scan)
- `CREATE INDEX`, índice composto, índice único
- `EXPLAIN` e `EXPLAIN ANALYZE` — como ler: seq scan vs index scan, custo estimado vs tempo real
- Quando um índice não ajuda (cardinalidade baixa, tabela pequena, coluna nunca filtrada)
**Exercício obrigatório:**
Popular uma tabela sua com pelo menos 50.000 linhas (script simples de geração, pode pedir ajuda pra gerar dados fake). Rodar `EXPLAIN ANALYZE` numa query de filtro sem índice, depois criar o índice e rodar de novo. Mandar os dois planos reais.
 
**Perguntas de verificação:**
1. No seu teste, o que mudou entre os dois planos além do tempo — o que o Postgres decidiu fazer diferente estruturalmente?
2. Por que criar índice em toda coluna "só por garantia" é uma péssima ideia mesmo sabendo que índice acelera leitura?

---

## Etapa 14 — Funções, stored procedures e triggers
 
**Conteúdo:**
- Diferença entre function e procedure no Postgres (retorno de valor vs execução de efeito)
- `CREATE FUNCTION` em PL/pgSQL básico (variável, `IF`, loop simples)
- `CREATE TRIGGER` — `BEFORE`/`AFTER`, `INSERT`/`UPDATE`/`DELETE`
- Quando lógica pertence ao banco (integridade, auditoria) e quando pertence à aplicação (regra de negócio que muda com frequência) — essa decisão importa mais que a sintaxe
**Exercício obrigatório:**
Criar uma trigger que registra automaticamente em uma tabela de log toda vez que um `UPDATE` acontece numa tabela do seu modelo. Testar fazendo o `UPDATE` e mostrando o log gerado.
 
**Perguntas de verificação:**
1. Por que colocar regra de negócio complexa numa trigger é geralmente considerado antipadrão em times que usam ORM/aplicação como camada principal — dado que você já trabalha com EF Core, o que isso significa pro seu projeto do hotel?
2. Que tipo de lógica você defenderia manter no banco mesmo tendo EF Core disponível, e por quê?

---

## Etapa 15 — Window functions
 
**Conteúdo:**
- `OVER()`, `PARTITION BY`, diferença fundamental entre window function e `GROUP BY` (não colapsa linhas)
- `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()` — diferença entre os três com dado empatado
- `LAG()`, `LEAD()` — comparar linha com a anterior/próxima
- Funções de agregação usadas como window function (`SUM() OVER (...)`, running total)
**Exercício obrigatório:**
Query que rankeia usuários por número de empréstimos usando `RANK()`, e outra que calcula total acumulado de empréstimos por mês usando `SUM() OVER`. Mandar as duas reais com resultado.
 
**Perguntas de verificação:**
1. Por que `RANK()` e `DENSE_RANK()` dão resultados diferentes especificamente quando há empate — mostre com um exemplo dos seus dados.
2. Qual a diferença estrutural entre usar `GROUP BY` e usar `PARTITION BY` para "a mesma" pergunta de negócio — o que cada resultado final se parece?

---

## Etapa 16 — CTEs e queries recursivas
 
**Conteúdo:**
- `WITH` (CTE não recursiva) — quando ela é só legibilidade e quando muda comportamento (materialização em versões antigas do Postgres)
- CTE recursiva (`WITH RECURSIVE`) — caso base + caso recursivo
- Caso clássico: hierarquia (funcionário → gerente → gerente do gerente)
**Exercício obrigatório:**
Modelar uma hierarquia simples (pode ser categorias de produto pai/filho) e escrever uma CTE recursiva que retorna a árvore completa a partir da raiz.
 
**Perguntas de verificação:**
1. O que exatamente para a recursão numa CTE recursiva — o que acontece se o caso base estiver errado?
2. Por que uma CTE recursiva mal escrita pode entrar em loop infinito, e como o Postgres se protege (ou não) disso por padrão?

---

## Checkpoint de integração 2 (Etapas 10-16)
 
Exercício que obriga: view sobre uma window function, com CTE, rodando `EXPLAIN ANALYZE` pra justificar se precisa de índice novo. Se você não conseguir justificar cada peça (por que view, por que window function e não GROUP BY, por que essa CTE), não avança.

---

## Etapa 17 — Segurança e controle de acesso
 
**Conteúdo:**
- Roles, `GRANT`/`REVOKE`, permissão por tabela/coluna/schema
- Princípio do menor privilégio aplicado a usuário de aplicação vs usuário admin
- SQL Injection: por que acontece (concatenação de string em query), como prepared statement/parametrização resolve — e por que EF Core já te protege disso por padrão (e onde ele PARA de te proteger, ex: SQL raw)
- Backup básico: `pg_dump`/`pg_restore` — o que cada flag importante faz
**Exercício obrigatório:**
Criar um usuário de banco com permissão só de `SELECT` numa tabela específica (não superuser). Tentar um `INSERT` com esse usuário e mostrar o erro real. Fazer um `pg_dump` do seu banco e restaurar em um banco novo vazio.
 
**Perguntas de verificação:**
1. Por que dar permissão de superuser pro usuário que a aplicação usa em produção é considerado grave, mesmo se "funcionar" perfeitamente no dia a dia?
2. Escreva um exemplo de query C#/EF Core (pode ser pseudocódigo) que seria vulnerável a SQL injection se alguém usasse SQL raw sem parametrizar — e a versão corrigida.

---

## Etapa 18 — Otimização de queries (nível prático, não DBA)
 
**Conteúdo:**
- Leitura avançada de `EXPLAIN ANALYZE`: nested loop vs hash join vs merge join — quando o planner escolhe cada um
- Estatísticas da tabela (`ANALYZE`) e por que planner erra quando estão desatualizadas
- Índice composto: ordem das colunas importa — por quê
- Anti-padrões comuns: `SELECT *` em produção, N+1 query (conceito que vai bater direto com EF Core/lazy loading), função em coluna dentro de `WHERE` que invalida índice
**Exercício obrigatório:**
Pegar uma query lenta de verdade (pode ser sintética, criada de propósito com JOIN mal planejado em tabela grande), rodar `EXPLAIN ANALYZE`, identificar o problema, corrigir (índice, reescrita, ou `ANALYZE`), rodar de novo e comparar custo/tempo real.
 
**Perguntas de verificação:**
1. Explique, com um exemplo seu, por que uma função aplicada à coluna no `WHERE` (ex: `WHERE UPPER(nome) = 'X'`) impede o uso de índice normal naquela coluna.
2. O que é N+1 query, por que EF Core pode gerar isso sem você perceber, e como isso se relaciona com tudo que você aprendeu sobre JOIN nas Etapas 7-9?

---

## Checkpoint final (Etapa 18 + tudo)
 
Você vai pegar o schema real do seu projeto de hotel (o que já existe da modelagem MoSCoW/EF Core) e:
1. Rodar `EXPLAIN ANALYZE` nas queries mais prováveis de uso real (buscar reservas por período, disponibilidade de quarto).
2. Justificar quais índices esse schema precisa e por quê, com base no que os planos mostraram — não no "achismo".
3. Apontar pelo menos um risco de N+1 que o EF Core poderia gerar nesse projeto especificamente.
Sem esse checkpoint fechado, o roadmap de SQL não está "concluído" — teoria sem aplicação no seu próprio projeto não conta como aprendido.

---

## Fora de escopo deste roadmap (decisão deliberada, não esquecimento)
 
- Administração de cluster, replicação, alta disponibilidade
- Tuning de storage engine / configuração de servidor em produção
- NoSQL (Mongo, Redis, etc.) — tópico separado, quando e se fizer sentido pro seu objetivo de carreira
- Data warehousing / OLAP, ETL