# Etapa 1 - Modelo Relacional e Fundamentos
 
### 1. Banco relacional vs alternativas
 
Um banco relacional organiza dado em tabelas com estrutura fixa (colunas tipadas, linhas como registros), e a relação entre tabelas é declarada explicitamente, não é você que precisa "lembrar" que duas tabelas se conectam, é o banco que sabe disso via chave estrangeira.
 
Compare com um arquivo/planilha: nada impede você de digitar texto onde devia ser número, nada garante que "cliente_id = 5" na aba de pedidos corresponde a um cliente que existe de fato na aba de clientes. O banco relacional resolve isso com **constraints**, regras que o próprio motor aplica, e você vai ver isso na prática já na Etapa 3.
 
### 2. Tabela, linha, coluna, chave primária, chave estrangeira, domínio
 
- **Tabela**: conjunto de registros do mesmo tipo (ex: `cliente`).
- **Linha (registro/tupla)**: uma instância, um cliente específico.
- **Coluna (atributo/campo)**: uma característica de cada registro — nome, email.
- **Domínio**: o conjunto de valores válidos pra uma coluna (ex: domínio de "idade" não aceita texto nem número negativo).
- **Chave primária (PK)**: identifica cada linha de forma única dentro da tabela. Só existe uma por tabela.
- **Chave estrangeira (FK)**: coluna que referencia a PK de outra tabela, formalizando o relacionamento.
```sql
-- Exemplo ilustrativo (você não precisa rodar isso ainda, a sintaxe formal vem na Etapa 3)
-- cliente.id é PK
-- pedido.cliente_id é FK, aponta pra cliente.id
```
 
**Armadilha comum:** achar que qualquer coluna com valores únicos (ex: CPF) é "praticamente uma PK". Não é, a diferença mecânica entre PK e uma coluna `UNIQUE` comum (unicidade só, `NOT NULL` automático ou não, ser alvo de FK) é formalizada só na Etapa 11, mas já vale ter isso no radar agora.
 
### 3. Anomalias de dado duplicado sem relação declarada
 
Se o mesmo dado existe em duas tabelas sem FK conectando-as, o banco não tem meio de saber que elas "deveriam" estar sincronizadas. Isso gera **anomalia de atualização**: você atualiza um lugar, esquece o outro, e nada no banco reclama — porque pra ele são só duas colunas de texto quaisquer, sem relação declarada explicitamente.
 
**Armadilha comum:** confundir esse problema (anomalia de atualização, dado duplicado desnecessariamente) com o problema de **integridade referencial** (registro "solto", sem ligação nenhuma a nada). São dois problemas diferentes, o primeiro é sobre dado redundante sem sincronia; o segundo é sobre ausência de relação onde deveria existir uma.
 
### 4. Noção informal de seleção, projeção e junção
 
Sem nomenclatura formal ainda (isso vem com SELECT/WHERE/JOIN nas próximas etapas), mas já dá pra intuir:
- **Selecionar linhas** = filtrar quais registros você quer ver.
- **Projetar colunas** = escolher quais campos aparecem no resultado.
- **Juntar tabelas** = combinar dados de tabelas relacionadas numa consulta só.
## Checklist antes de ir pros exercícios
 
- Eu sei explicar por que um banco relacional impede (ou dificulta) um tipo de inconsistência que uma planilha permite sem esforço?
- Eu sei dizer, sem citar definição de livro, o que uma chave estrangeira formaliza que uma coluna comum não formaliza?
- Eu sei nomear a diferença entre "anomalia de atualização" e "problema de integridade referencial" com um exemplo próprio pra cada?
- Eu sei dizer por que "ver a versão do Docker na interface gráfica" não é a mesma prova que "rodar `docker --version` no terminal e ver o output"?
Se a resposta for "não tenho certeza" em algum item, relê só o bloco específico, não o documento inteiro.
 
## Exercícios
 
1. Instalar Docker Desktop no Windows (WSL2 como backend) e confirmar via terminal (`docker --version`, `docker ps`) que o daemon está acessível via linha de comando, não só que o app abriu.
2. Subir um container PostgreSQL (`docker run ...`) e confirmar via `docker ps` que o status está `Up`.
3. Conectar ao banco via `psql` dentro do container e rodar `\l`, `\dt`, `\d`, confirmando que o ambiente está limpo (só os bancos padrão, nenhuma tabela).
Não há exercício [DEBUG] nesta etapa — esse tipo de exercício começa na Etapa 3, quando já existe schema real pra quebrar de propósito.