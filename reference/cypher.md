# Cypher - Ladybug Extensions

This document covers Cypher features in Ladybug that differ from or extend Kuzu.

## Native JSON Type (v0.15.0+)

Starting from v0.15.0, JSON is natively supported as a core data type. Use `JSON` instead of `STRING` for JSON properties.

```cypher
CREATE NODE TABLE User (id INT64, data JSON, PRIMARY KEY(id));

CREATE (u:User {id: 1, data: {"name": "Alice", "tags": ["admin", "user"]}});
```

```cypher
MATCH (u:User) WHERE u.data.name = 'Alice' RETURN u.data.tags;
```

## Open Type Graph

Create graphs without a predefined schema using `ANY`:

```cypher
CREATE GRAPH my_graph ANY;
USE my_graph;

CREATE (u:User {name: 'Alice'});
CREATE (p:Product {id: 1, price: 99.99});
CREATE (u)-[:BOUGHT {qty: 2}]->(p);
```

Without `ANY`, you must define the schema first:

```cypher
CREATE GRAPH my_graph STRICT;
USE my_graph;
CREATE NODE TABLE User (name STRING, PRIMARY KEY(name));
CREATE NODE TABLE Product (id INT64, price DOUBLE, PRIMARY KEY(id));
CREATE REL TABLE BOUGHT (FROM User TO Product, qty INT64);
```

## Query Parquet Files On Disk

Create node/rel tables that reference Parquet files directly:

```cypher
CREATE NODE TABLE Employee (id INT64, name STRING, PRIMARY KEY(id))
WITH (storage='path/to/employee.parquet');

CREATE REL TABLE WorksIn (from Person, to Company, since INT32)
WITH (storage='path/to/works_in.parquet');

MATCH (e:Employee)-[w:WorksIn]->(c:Company) RETURN e.name, c.name;
```

## Query Arrow Memory

Query Arrow memory directly by registering it with an `arrow://` storage prefix:

```cypher
CREATE NODE TABLE MyData (id INT64, value STRING, PRIMARY KEY(id))
WITH (storage='arrow://my_arrow_table_id');
```

In Python, you register Arrow memory and get the ID:

```python
import real_ladybug as lb
import pyarrow as pa

db = lb.Database(":memory:")
conn = lb.Connection(db)

table = pa.table({'id': [1, 2], 'value': ['a', 'b']})
arrow_id = conn.register_arrow("my_arrow_table", table)

conn.execute(f"CREATE NODE TABLE MyData (id INT64, value STRING, PRIMARY KEY(id)) WITH (storage='arrow://{arrow_id}')")
```
