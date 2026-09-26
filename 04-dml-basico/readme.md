# Etapa 4 - DML Básico

## 1. `INSERT INTO`

```sql
   -- Com lista de colunas explícita (recomendado sempre)
   INSERT INTO tipo_ingresso (categoria, preco_base, beneficicios)
   VALUES ('VIP', 500.00, 'Acesso a todas as palestras + coffee break exclusivo');

   -- Múltiplas linhas num INSERT só
   INSERT INTO tipo_ingresso (categoria, preco_base, beneficios)
   VALUES
        ('Padrão', 150.00, 'Acesso às palestras'),
        ('Estudante', 80.00, 'Acesso às palestras, com comprovação');
```

**Armadilha comum:** `INSERT INTO tabela VALUES (...)` sem listar as colunas explicitamente funciona, mas depende da ordem exata das colunas na tabela, se alguém alterar a ordem/adicionar coluna no meio (via `ALTER TABLE`), esse `INSERT` passa a inserir dado na coluna errada sem erro nenhum. É a mesma categoria de risco do `SELECT *`: funciona até a estrutura da tabela mudar.

## 2. `UPDATE` com `WHERE`

```sql
   UPDATE tipo_ingresso
   SET preco_base = 550.00
   WHERE categoria = 'VIP'; 
```

**O que acontece sem `WHERE`**:

```sql
   UPDATE tipo_ingresso SET preco_base = 550.00
   -- Isso aplica 550.00 em TODAS as linhas da tabela, sem exceção. 
```

O Postgres não te impede por padrão, sintaticamente, `UPDATE ... SET ...` sem `WHERE` é um comando 100% válido, porque "atualizar tudo" é uma operação legítima em alguns cenários raros (ex: reindexar um status geral). O banco não sabe distinguir sua intenção da de alguém que realmente queria atualizar tudo.

## 3. `DELETE` com `WHERE`

```sql
   DELETE FROM participante WHERE id_participante = 3; 
```

Mesmo risco do `UPDATE`: `DELETE FROM participante;` sem `WHERE` apaga todas as linhas. Diferente de `TRUNCATE` (Etapa 3), `DELETE` sem `WHERE` ainda passa por log linha a linha (mais lento), mas o resultado final, tabela vazia, é o mesmo.
 
**Prática de segurança real, não teórica:** antes de rodar `UPDATE`/`DELETE` com condição, rode o mesmo `WHERE` dentro de um `SELECT` primeiro, pra confirmar visualmente quais linhas serão afetadas, antes de aplicar a operação destrutiva.

```sql
   -- Primeiro confirma:
   SELECT * FROM participante WHERE id_tipo_ingresso = 2;
   -- Só deppis confirmar que são as linhas certas:
   DELETE FROM participante WHERE id_tipo_ingresso = 2; 
```

`SELECT *` retorna todas as colunas, funciona, mas tem os mesmos riscos do `INSERT` sem lista de colunas: se a estrutura da tabela mudar (nova coluna adicionada), o resultado muda sem você ter pedido explicitamente, e código de aplicação que espera um número fixo de colunas pode quebrar.
 
**Armadilha comum:** usar `SELECT *` "porque é mais rápido de escrever" em algo que vai virar código de produção, isso não é economia real, é dívida técnica que aparece na primeira migração de schema.

## Checklist antes de ir pros exercícios
 
- Eu sei explicar exatamente o que acontece, mecanicamente, se eu rodar `DELETE FROM tabela;` sem `WHERE`, não "apaga tudo", mas por que o banco permite isso sem confirmação?
- Eu sei dizer por que `INSERT INTO tabela VALUES (...)` sem listar colunas é arriscado, mesmo funcionando corretamente hoje?
- Eu sei explicar a prática de rodar `SELECT` com o mesmo `WHERE` antes de um `UPDATE`/`DELETE`, o que exatamente isso previne?
- Eu sei por que `SELECT *` é considerado má prática em código de produção, além de "não ser explícito"?

## Exercícios
 
### Bloco 1 — Popular as tabelas

1. Inserir pelo menos 5 linhas em cada uma das 6 tabelas do seu modelo (respeitando a ordem de dependência de FK — não dá pra inserir em `participante` antes de existir a linha correspondente em `tipo_ingresso`).

### Bloco 2 — UPDATE e DELETE condicionais

2. Fazer um `UPDATE` que altera pelo menos uma linha específica via `WHERE`, confirmando antes com `SELECT` a linha que será afetada.
3. Fazer um `DELETE` que remove pelo menos uma linha específica via `WHERE`, com a mesma confirmação prévia.
