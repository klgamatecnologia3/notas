**Coluna (ordem “mais completa”):**

```sql
coluna_nome tipo [ COLLATE collation ]
  [ GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY ( ... ) | GENERATED ALWAYS AS (expr) STORED ]
  [ DEFAULT expr ]
  [ NOT NULL | NULL ]
  [ CONSTRAINT nome_col CHECK (expr) ]
  [ UNIQUE ]
  [ PRIMARY KEY ]
  [ REFERENCES tabela(col) [ MATCH ... ] [ ON UPDATE ... ] [ ON DELETE ... ] ]
  [ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY IMMEDIATE ]
```

**Tabela (constraints, ordem típica):**

```sql
CREATE TABLE t (
  colunas...,
  [ CONSTRAINT pk PRIMARY KEY (c1, c2) ],
  [ CONSTRAINT uq UNIQUE (c1) ],
  [ CONSTRAINT ck CHECK (expr) ],
  [ CONSTRAINT fk FOREIGN KEY (c1) REFERENCES outra(c2) ON DELETE ... ON UPDATE ... ]
);
```
