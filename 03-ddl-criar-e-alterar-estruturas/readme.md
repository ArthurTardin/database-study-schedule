# Etapa 3 - DDL: Criar e Alterar Estrutura

# 1. `CREATE TABLE` e tipos de dados

```sql
    CREATE TABLE tipo_ingresso (
        id_tipo_ingresso SERIAL PRIMARY KEY,
        categoria VARCHAR(50) NOT NULL,
        preco_base NUMERIC (10, 2) NOT NULL,
        beneficios TEXT
    );
```

- `SERIAL` gera um inteiro autoincrementado, é o jeito clássico do Postgres de fazer PK numérica sem você controlar o próximo valor manualmente.
- `VARCHAR(50)` tem limite de tamanho; `TEXT` não tem limite prático. Usar `VARCHAR` sem necessidade real de limitar não traz benefício de performance no Postgres (diferente de outros bancos), é decisão de modelagem/validação, não de performance.
- `NUMERIC(10,2)` é tipo exato para dinheiro, nunca use `FLOAT`/`REAL` para valor monetário, porque eles têm erro de arredondamento por representação binária.

**Armadilha comum:** usar `FLOAT` para preço porque "é número com casa decimal" sem saber que isso é semanticamente diferente de `NUMERIC`. Isso não dá erro na criação da tabela, só te morde quando um cálculo de soma de preços não fecha exatamente.

## 2. `ALTER TABLE`

```sql
   ALTER TABLE participante ADD COLUMN telefone VARCHAR(20);
   ALTER TABLE participante ALTER COLUMN nome TYPE VARCHAR(150);
   ALTER TABLE participante RENAME COLUMN documento TO cpf; 
```

**Armadilha comum:** `ALTER COLUMN ... TYPE` pode falhar (ou truncar dado silenciosamente, depois do tipo) se já existir dado incompatível com o novo tipo, ex: tentar reduzir `VARCHAR(150)` pra `VARCHAR(10)` numa coluna que já tem string maior que 10 caracteres.

## 3. `DROP` vs `TRUNCATE`

- `DROP TABLE`: remove a tabela inteira (estrutura + dados). Falha se outra tabela tiver FK apontando para ela, a menos que você `CASCADE`.
-  `TRUNCATE`: remove todas as linhas, mantém a estrutura. Mais rápido que `DELETE` sem `WHERE` porque não gera log linha a linha, mas também falha (por padrão) se houver FK referenciando a tabela.

Isso conecta direto com a Etapa 2: a ordem de dependência das suas FKs (`InscricaoPalestra` dpende de `Participante` e `Palestra`) é a mesma ordem que determina em que sequência você consegue dropar/truncar tabelas sem usar `CASCADE`.

**Armadilha comum:** achar que `TRUNCATE` é "igual a `DELETE` sem `WHERE`, só mais rápido" sem saber que ele reseta sequência de `SERIAL` por padrão (a menos que você use `TRUNCATE ... RESTART IDENTITY` explicitamento pro comportamento contrário, o padrão vari, e vcoê precisa testar e confirmar, não assumir).
 
## 4. `PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `DEFAULT` na criação

```sql
    CREATE TABLE palestra (
        id_palestra SERIAL PRIMARY KEY,
        titulo VARCHAR(200) NOT NULL,
        horario_inicio TIMESTAMP NOT NULL,
        horario_termino TIMESTAMP NOT NULL,
        id_sala INTEGER NOT NULL REFERENCES sala(id_sala),
        id_palestrante INTEGER NOT NULL REFERENCES palestrante(id_palestrante),
        status VARCHAR(20) DEFAULT 'agendada'
    );
```

- `REFERENCES` dentro da própria definição da coluna é FK inline, funciona igual a declarar `FOREIGN KEY (...) REFERENCES ...` separado, só que mais direto para FK simples (uma coluna só).
- `DEFAULT` só se aplica quando você não informa valor no `INSERT`, não sobrescreve valor que você mandou explicitamente, mesmo que seja o mesmo valor do default.

## Checklist antes de ir pros exercícios
 
- Eu sei explicar por que usar `FLOAT` pra dinheiro é um erro, mesmo que a tabela seja criada sem nenhum erro de sintaxe?
- Eu sei dizer o que acontece, na prática, se eu tentar `DROP TABLE` numa tabela referenciada por FK sem usar `CASCADE`?
- Eu sei explicar por que uma FK sem `NOT NULL` pode estar tecnicamente correta e ainda assim estar errada em relação à minha própria modelagem da Etapa 2?
- Eu sei dizer quando `DEFAULT` é aplicado e quando ele é ignorado?
Se a resposta for "não tenho certeza" em algum item, relê só o bloco específico.

## Exercícios
 
### Bloco 1 - Implementação real do modelo da Etapa 2
1. Implementar, em SQL real rodado no seu Postgres, todas as 6 tabelas do modelo de eventos (TipoIngresso, Sala, Palestrante, Participante, Palestra, InscricaoPalestra), respeitando PK, FK, `NOT NULL` onde a cardinalidade mínima da Etapa 2 exige, e tipos de dados coerentes com cada atributo.
2. Depois de criado, alterar pelo menos uma tabela já existente (`ALTER TABLE`) sem dropar nada, ex: adicionar uma coluna nova ou mudar tipo de uma existente.

### Bloco 2 - `DROP` vs `TRUNCATE`

3. Tentar `DROP TABLE` em uma tabela referenciada por FK (ex: `TipoIngresso`, referenciada por `Participante`) sem `CASCADE`, e registrar o erro real que aparece. Depois, tentar de novo resolvendo o problema sem simplesmente usar `CASCADE`, pense em por que `CASCADE` ali seria perigoso antes de escolher a solução.

### [DEBUG]

4. Vou te passar, na próxima mensagem, um schema pronto (`CREATE TABLE`s) com pelo menos um problema plantado — pode ser tipo de dado incoerente, `NOT NULL` faltando onde a modelagem exige, FK apontando errado, ou algo que rode sem erro mas esteja semanticamente quebrado. Você vai ter que identificar o(s) problema(s) e corrigir, sem eu revelar antes quantos existem.