# [![My Skills](https://skillicons.dev/icons?i=postgres)](https://skillicons.dev) Roadmap de Banco de Dados / SQL
 
## Objetivo
 
Este documento registra a jornada de estudo estruturado e self-directed de Banco de Dados/SQL, do modelo relacional básico até otimização de queries em nível prático. Serve como referência de progresso, registro de conceitos cobertos por etapa, e histórico de exercícios/checkpoints cumpridos.

## Regras do método
 
1. A partir da **Etapa 3**, todo bloco de conteúdo ganha pelo menos 1 exercício **[DEBUG]** — código/schema já pronto e quebrado de propósito, sem revelar quantos bugs existem nem onde estão.
2. Checkpoints de consolidação a cada 3 etapas — volta-se a um exercício antigo e usa-se com o conteúdo novo. Etapa sem checkpoint cumprido não conta como concluída.
3. A partir da **Etapa 11** (Constraints Avançadas), testes de banco (via `pgTAP` ou scripts de asserção) entram como habilidade contínua — não é tópico isolado no fim do roadmap, aparece de novo em toda etapa relevante a partir daí (marcado como **[TESTE]**).
4. A partir do **Checkpoint 3** (após a Etapa 9), leitura de schema/queries reais de repositório open source entra como exercício de consolidação, mantendo-se até o fim do roadmap.
5. Critério de avanço: não é "li o conteúdo", é "consigo explicar pra alguém leigo E cometo menos erros óbvios quando aplico por conta própria" — validado via perguntas de verificação e exercício executado de verdade, nunca por afirmação própria de que "entendi".

## Estrutura
 
### Etapa 1 - Modelo Relacional e Fundamentos

- Banco relacional vs arquivo/planilha vs banco não-relacional
- Tabela, linha, coluna, chave primária, chave estrangeira, domínio de dados
- Anomalias de dados duplicados sem relação declarada
- Noção informal de seleção, projeção e junção
- Exercícios: setup de ambiente (Docker + PostgreSQL); exploração inicial via `psql`

---
 
### Etapa 2 - Modelagem Conceitual: ER e Normalização (1NF-3NF)

- Diagrama ER: entidade, atributo, relacionamento, cardinalidade (1:1, 1:N, N:N)
- Tabela associativa para relacionamento N:N
- 1NF, 2NF, 3NF
- Desnormalização deliberada
- Exercícios: modelagem de domínio próprio com N:N e decisão de 3NF

---
 
### Etapa 3 - DDL: Criar e Alterar Estrutura

- `CREATE TABLE`, tipos de dados
- `ALTER TABLE`
- `DROP` vs `TRUNCATE`
- `PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `DEFAULT` na criação
- Exercícios: implementação do modelo da Etapa 2 em SQL real; alteração de tabela existente
- **[DEBUG]** schema com FK mal declarada / tipo de coluna incompatível com dado esperado

## Checkpoint 1 (obrigatório antes de seguir pra Etapa 4)

Revisitar o modelo de domínio criado na Etapa 2 e o ambiente montado na Etapa 1, e confirmar que o schema real implementado na Etapa 3 bate 100% com a modelagem conceitual — inclusive cardinalidade e decisões de 3NF já tomadas.
 
---
 
### Etapa 4 - DML Básico: INSERT, UPDATE, DELETE, SELECT Simples

- `INSERT INTO` (com/sem lista de colunas, múltiplas linhas)
- `UPDATE` com `WHERE`
- `DELETE` com `WHERE`
- `SELECT` simples, alias (`AS`)
- Exercícios: popular tabelas; UPDATE/DELETE condicionais
- **[DEBUG]** script de seed com risco de `UPDATE`/`DELETE` sem `WHERE`

---
 
### Etapa 5 - Filtros e Operadores

- Operadores de comparação, precedência `AND`/`OR`/`NOT`
- `BETWEEN`, `IN`, `LIKE`/`ILIKE`
- `NULL` e lógica de três valores
- Exercícios: queries com filtros compostos
- **[DEBUG]** query com `NOT`/`!=` que descarta linhas por causa de `NULL`

---
 
### Etapa 6 - Ordenação e Agregação

- `ORDER BY`, `LIMIT`/`OFFSET`
- `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`
- `GROUP BY`, `HAVING` vs `WHERE`
- Exercícios: paginação; agregação com `HAVING`
- **[DEBUG]** query que usa `WHERE` no lugar de `HAVING` (ou vice-versa) e produz resultado plausível mas errado

## Checkpoint 2 (obrigatório antes de seguir pra Etapa 7)

Revisitar os dados populados na Etapa 4 e os filtros da Etapa 5, agora produzindo um relatório agregado e filtrado (Etapa 6) sobre os mesmos dados — sem reescrever o schema do zero.
 
---
 
### Etapa 7 - JOINs

- `INNER`, `LEFT`, `RIGHT`, `FULL OUTER`, `CROSS JOIN`
- Self join
- Múltiplos JOINs na mesma query
- Exercícios: query com 3+ tabelas joinadas
- **[DEBUG]** JOIN que gera produto cartesiano acidental / duplicação de linhas por condição de junção incompleta

---
 
### Etapa 8 - Subqueries

- Subquery escalar, correlacionada vs não-correlacionada
- `EXISTS`/`NOT EXISTS` vs `IN`/`NOT IN`
- Quando reescrever subquery como JOIN
- Exercícios: mesma pergunta de negócio resolvida por 3 abordagens diferentes
- **[DEBUG]** `NOT IN` que retorna vazio por causa de `NULL` na subquery

---
 
### Etapa 9 - Set Operations

- `UNION` vs `UNION ALL`
- `INTERSECT`, `EXCEPT`
- Compatibilidade de colunas entre queries combinadas
- Exercícios: combinação de queries com os três operadores
- **[DEBUG]** uso indevido de `UNION` escondendo duplicata que deveria aparecer

## Checkpoint 3 (obrigatório antes de seguir pra Etapa 10)

Revisitar o exercício de JOIN (Etapa 7) e o de subquery (Etapa 8), reescrevendo partes com set operations onde fizer sentido. **A partir daqui, leitura de código real**: analisar um schema/conjunto de queries de um repositório open source real e explicar as decisões de modelagem encontradas.
 
---
 
### Etapa 10 - Views

- `CREATE VIEW`, `CREATE OR REPLACE VIEW`, `DROP VIEW`
- Materialized view vs view normal
- Exercícios: encapsular a query do Checkpoint 3 numa view
- **[DEBUG]** view que esconde um problema estrutural de N+1/performance

---
 
### Etapa 11 - Constraints Avançadas

- `UNIQUE`, `CHECK`
- `FOREIGN KEY` com `ON DELETE CASCADE / SET NULL / RESTRICT / NO ACTION`
- Exercícios: decidir e justificar política de `ON DELETE` por relação do próprio modelo
- **[TESTE]** primeiros testes de banco (via `pgTAP` ou script de asserção) provando que `CHECK`/`CASCADE` se comportam como esperado
- **[DEBUG]** constraint `CHECK` mal escrita que permite dado inválido passar

---
 
### Etapa 12 - Transações e ACID

- `BEGIN`/`COMMIT`/`ROLLBACK`
- ACID
- Isolation levels, dirty read, non-repeatable read, phantom read
- Deadlock
- Exercícios: teste com duas sessões `psql` simultâneas
- **[TESTE]** validar comportamento sob isolation level específico
- **[DEBUG]** transação que não trata erro e deixa lock preso

## Checkpoint 4 (obrigatório antes de seguir pra Etapa 13)

Revisitar as constraints da Etapa 11 dentro de uma transação que tenta violar `CHECK`/FK de propósito, observando o `ROLLBACK` acontecer. Os testes escritos na Etapa 11 devem ser reexecutados dentro desse cenário transacional.
 
---
 
### Etapa 13 - Índices e Leitura de Plano de Execução

- Conceito de B-tree (nível de uso, não implementação interna)
- `CREATE INDEX`, índice composto
- `EXPLAIN`/`EXPLAIN ANALYZE`
- Quando índice não ajuda
- Exercícios: comparar plano de execução antes/depois de criar índice
- **[DEBUG]** índice criado que o planner ignora — investigar o motivo real

---
 
### Etapa 14 - Funções, Stored Procedures e Triggers

- Function vs procedure
- PL/pgSQL básico
- `CREATE TRIGGER` (`BEFORE`/`AFTER`, `INSERT`/`UPDATE`/`DELETE`)
- Lógica no banco vs lógica na aplicação
- Exercícios: trigger de auditoria
- **[TESTE]** testar comportamento da trigger via `pgTAP`
- **[DEBUG]** trigger com efeito colateral não intencional (duplicação de log, loop de trigger)

---
 
### Etapa 15 - Window Functions

- `OVER()`, `PARTITION BY`
- `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`
- `LAG()`, `LEAD()`
- Agregação como window function (running total)
- Exercícios: ranking e total acumulado
- **[DEBUG]** uso de `RANK()` onde o correto seria `ROW_NUMBER()`, gerando contagem errada em caso de empate

## Checkpoint 5 (obrigatório antes de seguir pra Etapa 16)

Revisitar a view da Etapa 10, reconstruindo-a com uma window function por dentro, com os testes das Etapas 11/14 cobrindo o novo comportamento.
 
---
 
### Etapa 16 - CTEs e Queries Recursivas

- `WITH` (não recursiva)
- `WITH RECURSIVE`: caso base + caso recursivo
- Hierarquia (ex: categoria pai/filho)
- Exercícios: árvore hierárquica completa via CTE recursiva
- **[DEBUG]** CTE recursiva sem condição de parada correta (risco de loop)

---
 
### Etapa 17 - Segurança e Controle de Acesso

- Roles, `GRANT`/`REVOKE`, princípio do menor privilégio
- SQL Injection e parametrização
- `pg_dump`/`pg_restore`
- Exercícios: usuário com privilégio mínimo; backup e restore reais
- **[DEBUG]** trecho de código de aplicação vulnerável a SQL injection, a identificar e corrigir

---
 
### Etapa 18 - Otimização de Queries

- Nested loop, hash join, merge join — quando o planner escolhe cada um
- `ANALYZE` e estatísticas desatualizadas
- Ordem de colunas em índice composto
- Anti-padrões: `SELECT *`, N+1, função aplicada à coluna em `WHERE`
- Exercícios: diagnosticar e corrigir uma query lenta real (ou sintética)
- **[DEBUG]** query lenta plantada de propósito, causa não revelada

## Checkpoint 6 (obrigatório antes da Etapa 19)

Revisitar qualquer exercício anterior que tenha ficado sem índice adequado e justificar, com `EXPLAIN ANALYZE` real, se ele precisa de correção agora que otimização foi formalizada.
 
---
 
### Etapa 19 - Projeto Consolidado
 
Aplicar tudo no schema real de um projeto próprio (ex: o schema do projeto de hotel). Critério de conclusão — não é "features implementadas":
 
- Testes (`pgTAP` ou equivalente) cobrindo as constraints e triggers relevantes do schema.
- Capacidade de explicar cada decisão de modelagem e de índice sem consultar nada.
- Resiliência: alguém de fora tentando inserir dado malicioso/inconsistente (SQL injection, violação de constraint, transação concorrente) não deve conseguir corromper o schema.

## Fora de escopo (decisão deliberada)
 
- Administração de cluster, replicação, alta disponibilidade
- Tuning de storage engine / configuração de servidor em produção
- NoSQL, data warehousing/OLAP, ETL