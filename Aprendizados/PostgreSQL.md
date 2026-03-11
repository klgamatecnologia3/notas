Segue um **resumo ultra-sintético** dos conceitos discutidos.

---

# Conceitos básicos de PostgreSQL (resumo rápido)

## Arquitetura

```
Servidor PostgreSQL (instância)
 ├─ Banco de dados
 │   ├─ Schema
 │   │   ├─ Tabelas
 │   │   ├─ Views
 │   │   └─ Indexes
```

* **Instância** → processo do servidor PostgreSQL rodando.
* **Banco (database)** → conjunto isolado de schemas.
* **Schema** → organização lógica de objetos.
* **Tabelas** → dados.

---

## Conexão cliente → banco

Fluxo:

```
Cliente (DBeaver / psql)
↓
socket TCP
↓
porta 5432
↓
PostgreSQL autentica usuário
↓
sessão criada
↓
cliente envia SQL
↓
PostgreSQL executa
↓
resultado retorna
```

---

## Termos importantes

**Host**
IP ou domínio do servidor.

**Porta**
Número do serviço (ex: `5432`).

**Socket**
Canal de comunicação (IP + porta + protocolo).

**Sessão**
Contexto da conexão dentro do PostgreSQL.

**Conexão**
Ligação TCP entre cliente e servidor.

---

## Estados de conexão

```
active                → executando query
idle                  → conectado sem executar nada
idle in transaction   → transação aberta sem finalizar
```

Consulta:

```sql
SELECT * FROM pg_stat_activity;
```

---

## Transações

```
BEGIN
↓
queries
↓
COMMIT  (confirma)
ou
ROLLBACK (desfaz)
```

`idle in transaction` ocorre quando **BEGIN foi executado mas a transação não terminou**.

---

## MVCC e VACUUM

PostgreSQL usa **MVCC**.

```
UPDATE / DELETE
↓
linhas antigas ficam "mortas"
↓
VACUUM remove e libera espaço
```

Comparação:

```
Garbage collector → memória
VACUUM → linhas antigas no disco
```

---

## Conexões

* Um servidor pode ter **várias conexões simultâneas**.
* Cada conexão cria **um processo no PostgreSQL**.
* Parâmetro principal:

```
max_connections
```

---

## Connection Pool

Ferramentas como **PgBouncer**:

```
aplicações
↓
pool de conexões
↓
PostgreSQL
```

Reutilizam conexões e **reduzem consumo de memória**.

---

## CLI PostgreSQL

Conectar:

```
psql -U usuario -d database
```

Exemplo:

```
psql -U postgres -d postgres
```

---

## Ideia mental final

```
Cliente → TCP → PostgreSQL → Sessão → SQL → Resultado
```
-----------------------------------------------------------------------------
O PostgreSQL usa o **PostgreSQL Wire Protocol** (também chamado **Frontend/Backend Protocol**).

Características:

* roda **sobre TCP/IP**
* protocolo **binário próprio do PostgreSQL**
* usado para comunicação entre **cliente (psql, DBeaver, apps)** e **servidor PostgreSQL**

Fluxo simplificado:

```text
cliente
↓
PostgreSQL wire protocol
↓
TCP
↓
porta 5432
↓
PostgreSQL server
```

Exemplo simplificado de troca:

```text
cliente → StartupMessage (user, database)
servidor → AuthenticationRequest
cliente → PasswordMessage
servidor → AuthenticationOK
cliente → Query("SELECT * FROM clientes")
servidor → DataRow / CommandComplete
```
