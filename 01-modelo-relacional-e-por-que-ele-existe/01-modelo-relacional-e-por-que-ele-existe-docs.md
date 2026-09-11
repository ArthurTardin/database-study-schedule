# Etapa 1 — Modelo Relacional e Por Que Ele Existe
 
## Ambiente
 
- SO: Windows
- Motor: PostgreSQL (imagem oficial `postgres:latest`, via Docker)
- Setup: container Docker (`docker run --name pg-estudo ...`), sem instalação nativa
## Conteúdo estudado
 
- Banco relacional vs arquivo/planilha vs banco não-relacional (conceito)
- Tabela, linha, coluna, chave primária, chave estrangeira, domínio de dados
- Anomalias de dados duplicados sem relação declarada (inserção, atualização, remoção)
- Ideia geral do que uma query faz (seleção, projeção, junção) sem terminologia formal ainda

## Exercício prático
 
Setup do ambiente via Docker:
 
```
docker run --name pg-estudo -e POSTGRES_PASSWORD=estudo123 -p 5432:5432 -d postgres
```
 
Conexão via `psql` dentro do container:
 
```
docker exec -it pg-estudo psql -U postgres
```
 
Verificação de estado do banco (esperado: ambiente limpo, só os 3 bancos padrão, nenhuma tabela/relação criada):
 
```sql
\l
\dt
\d
```
 
## Perguntas de verificação
 
**1. Por que armazenar o mesmo dado em duas tabelas diferentes sem ligação é um problema prático, não só teórico?**
 
Gera **anomalia de atualização**: se o mesmo dado (ex: nome de cliente) existe em duas tabelas sem relação declarada via FK, ao atualizar uma e esquecer a outra, o banco não reclama — porque para ele são duas colunas de texto quaisquer, sem qualquer relação declarada explicitamente. Não há mecanismo que force consistência entre elas.
 
**2. O que diferencia uma chave primária de uma coluna qualquer que também tem valores únicos (ex: CPF)?**
 
- Só pode existir **uma** PK por tabela; podem existir **várias** colunas `UNIQUE`.
- PK é automaticamente `NOT NULL`; `UNIQUE` sozinho permite múltiplos `NULL` na maioria dos bancos.
- FK de outras tabelas referencia a PK — é o alvo oficial de relacionamento. Uma coluna `UNIQUE` comum não se torna destino de FK só por ser única.