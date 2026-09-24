# SQLGlot

It use when RAG application genrate or manipulate SQL

- NO SQL FLOW

    """
    PDF/Docx
      |
    Chunking => Embeding => VectorDB => UserQuery => Simlilarity Search => Query => answer

    """

SQLGlot sit between LLM and DB

```
    Qwen
      |
  SQL Querry
      |
   SQLGlot----> Danger --> No
      |
  Valid SQL DB
```

```yaml
pip install sqlglot
```

```py
import sqlglot
query = sqlglot.parse_one(sql)
```

## Restriction Operation

- Insert,update,Delete,Drop,Alter,Truncate
- SQLGlot is not a security system.
- SQLGlot is an additional validate/ parse layer

## without SQLGlot

application directly execute

LLM => Delete => Database

> potentially dangerous

eg1. customers table

- database error: table  "customer" does not esist if table name is wrone in query

eg2.

- You need to build more SQL safty validation

## with SQLGlot

LLM => Delete => SQLGlot => Reject

EG1:

we can compare schema

```py
tree = sqlglot.parse_one(sql)
for table in tree.find_all(sqlglot.exp.Table):
    print(table.name)
```

eg2. It specifically designed to understand SQL syntex

> You can build SQL-RAG without SQLGlot but for proper SQL analysis/validation layer we need SQLGlot.
