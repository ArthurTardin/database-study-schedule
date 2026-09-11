
# Etapa 2 - Modelagem Conceitual: ER e Normalização (1NF-3NF)
 
### 1. Diagrama entidade-relacionamento (ER)
 
- **Entidade**: uma "coisa" do domínio que vai virar tabela (Usuário, Livro, Pedido).
- **Atributo**: uma característica da entidade que vai virar coluna.
- **Relacionamento**: como duas entidades se conectam (Usuário *empresta* Livro).
- **Cardinalidade**: quantas instâncias de A se relacionam com quantas de B:
  - **1:1** - no máximo um registro de cada lado se relaciona.
  - **1:N** - um registro de A se relaciona com vários de B, mas cada B só com um A.
  - **N:N** - vários de A com vários de B.
Isso é o mesmo raciocínio de PK/FK da Etapa 1, só que agora formalizado num passo anterior ao SQL: você desenha a relação antes de escrever `CREATE TABLE`.
 
### 2. Por que N:N não cabe em FK direta
 
Numa relação 1:N, a FK fica no lado "N" (ex: `livro.autor_id`, se cada livro tivesse um autor só). Numa relação N:N, nenhum dos dois lados aguenta uma FK só, porque cada lado pode ter múltiplas correspondências do outro lado, não existe onde colocar a FK.
 
A solução é a **tabela associativa**: uma terceira tabela com duas FKs, uma pra cada entidade, e normalmente a combinação das duas vira a PK composta dela.
 
```sql
-- Ilustrativo — sintaxe formal de CREATE TABLE vem na Etapa 3
-- livro_autor(livro_id FK, autor_id FK, PK composta = (livro_id, autor_id))
```
 
**Armadilha comum:** modelar N:N como se fosse 1:N só porque "na prática a maioria dos casos tem um valor só" — isso quebra assim que aparecer o primeiro caso real com múltiplos valores dos dois lados.
 
### 3. Formas normais (1NF, 2NF, 3NF)
 
As três são cumulativas: para estar em 3NF, já precisa estar em 2NF e 1NF.
 
- **1NF**: cada coluna guarda valor atômico (não lista, não "várias coisas separadas por vírgula"); sem colunas repetidas pro mesmo tipo de dado (`telefone1`, `telefone2`, `telefone3` é sinal de violação).
  - *Anomalia que resolve:* impossibilidade de filtrar/agregar direito quando o dado está "amassado" numa célula só.
- **2NF**: já em 1NF, e todo atributo não-chave depende da **chave inteira**, não de parte dela. Só é relevante quando a PK é **composta** (mais de uma coluna).
  - *Anomalia que resolve:* duplicação desnecessária quando parte da informação depende só de uma fração da chave composta, não da chave toda.
  - Exemplo: numa tabela `matricula(aluno_id, curso_id, nome_curso)`, se `nome_curso` depende só de `curso_id` (não da combinação `aluno_id + curso_id`), isso é violação de 2NF, `nome_curso` pertence à tabela `curso`, não à `matricula`.
- **3NF**: já em 2NF, e nenhum atributo não-chave depende de **outro atributo não-chave** (dependência transitiva).
  - *Anomalia que resolve:* a mesma anomalia de atualização que você já viu na Etapa 1 — mas agora com critério formal pra identificar onde ela vai aparecer antes de escrever uma linha de SQL.
  - Exemplo: `pedido(id, cliente_id, cliente_nome, cliente_email)` — se `cliente_nome`/`cliente_email` dependem de `cliente_id` (não da PK do pedido diretamente), isso é violação. Esses dados pertencem a `cliente`, não a `pedido`.
**Armadilha comum, direta pro seu histórico:** 2NF resolve dependência parcial da chave composta; 3NF resolve dependência transitiva entre atributos não-chave. Se você citar a definição errada pra anomalia errada, é o mesmo tipo de erro que você cometeu na Etapa 1 — nomear o problema com o rótulo errado.
 
### 4. Desnormalização deliberada
 
Existem casos em que você quebra a norma de propósito, geralmente por performance (evitar JOIN caro em leitura muito frequente). Só é defensável quando é decisão consciente e documentada — não porque você não sabia modelar direito. Você revisita isso com mais profundidade na Etapa 13 (índices/performance); por agora, só precisa saber que a opção existe e que ela tem preço.
 
## Checklist antes de ir pros exercícios
 
- Eu sei explicar por que uma relação N:N não pode ser resolvida com uma FK direta em nenhum dos dois lados?
- Eu sei dizer, com exemplo próprio (não o da biblioteca), qual anomalia específica a 2NF resolve — e por que ela só é relevante com chave composta?
- Eu sei diferenciar, sem citar a definição de livro, o que a 3NF resolve em relação ao que a 2NF resolve?
- Eu sei justificar um caso hipotético onde desnormalizar de propósito seria defensável, e qual o preço que eu pagaria por isso?
Se a resposta for "não tenho certeza" em algum item, relê só o bloco específico.
 
## Exercícios
 
1. Modelar um domínio próprio (diferente do de biblioteca), com no mínimo 4 entidades.
2. O modelo precisa ter pelo menos um relacionamento N:N resolvido via tabela associativa.
3. Identificar pelo menos um caso, no seu próprio modelo, onde você teve que decidir se um atributo pertence a essa entidade ou devia estar em outra — aplicando raciocínio de 3NF, e justificando a decisão por escrito.
Entrega: diagrama (dbdiagram.io, papel, ASCII) ou descrição textual completa — com todas as tabelas, colunas, qual é PK, qual é FK, e a cardinalidade de cada relacionamento explicitada.
 
Não há exercício [DEBUG] nesta etapa — começa na Etapa 3.
