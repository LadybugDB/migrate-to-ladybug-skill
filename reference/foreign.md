# Foreign Data Wrappers

LadybugDB supports attaching external DuckDB databases for querying nodes and relationships directly.

## Attach DuckDB Database

```cypher
LOAD_DYNAMIC_EXTENSION duckdb;

ATTACH '${LBUG_ROOT_DIRECTORY}/dataset/databases/duckdb_database/tinysnb.db' AS ts 
    (dbtype duckdb, skip_unsupported_table = true);
```

## Query DuckDB Node Tables

Once attached, query the external tables using the database alias as prefix:

```cypher
-- Count all persons
MATCH (a:ts.person) RETURN count(*);

-- Filter by age
MATCH (a:ts.person) WHERE a.age > 30 RETURN count(*);

-- Order and limit
MATCH (a:ts.person) RETURN a.ID ORDER BY a.ID LIMIT 3;
MATCH (a:ts.person) RETURN a.ID ORDER BY a.ID DESC LIMIT 3;

-- Complex filter with ordering
MATCH (a:ts.person) WHERE a.age > 20 RETURN a.ID ORDER BY a.age DESC LIMIT 2;
```

## Query DuckDB Relationship Tables

Create a relationship table that references the DuckDB table:

```cypher
-- Create rel table pointing to DuckDB knows table
CREATE REL TABLE knows_rel (FROM ts.person TO ts.person) 
    WITH (storage = 'ts.knows');

-- Query the relationship
MATCH (a:ts.person)-[b:knows_rel]->(c:ts.person) RETURN count(*);
```

## Detach Database

```cypher
DETACH ts;
```

## Notes

- Use `LOAD_DYNAMIC_EXTENSION duckdb` before attaching
- The `skip_unsupported_table = true` option ignores unsupported DuckDB table types
- Query external tables using the alias prefix (e.g., `ts.person`)
- Cypher to SQL pushdown is supported for DuckDB tables
