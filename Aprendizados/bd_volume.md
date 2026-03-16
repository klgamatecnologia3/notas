**Docker + Banco de Dados — resumo curto**

* **Container**: roda o PostgreSQL (processo).
* **Volume**: guarda os **dados do banco**.
* **Container pode ser apagado; volume permanece.**

Sem volume:

* dados ficam **dentro do container**
* remover container → **banco perdido**

Com volume:

* dados ficam **fora do container**
* recriar container → **banco continua existindo**

Regra prática:

> **Container roda o banco. Volume guarda o banco.**
