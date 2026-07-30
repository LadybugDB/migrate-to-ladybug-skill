# Python Interface

## Installation

```bash
pip install ladybug
```

## Quick Start

```python
import ladybug as lb

# Create an in-memory database
db = lb.Database(":memory:")
conn = lb.Connection(db)

# Create a node table
conn.execute("CREATE NODE TABLE Person (id INT64, name STRING, age INT64, PRIMARY KEY (id))")

# Insert data
conn.execute("CREATE (p:Person {id: 1, name: 'Alice', age: 30})")
conn.execute("CREATE (p:Person {id: 2, name: 'Bob', age: 25})")

# Query
result = conn.execute("MATCH (p:Person) WHERE p.age > 20 RETURN p.name, p.age")
print(result.get_as_df())
#    p.name  p.age
# 0   Alice     30
# 1     Bob     25
```

## Result Sets

Query results can be accessed as a Pandas DataFrame:

```python
result = conn.execute("MATCH (p:Person) RETURN p.name, p.age")
df = result.get_as_df()
```

Or iterate row by row:

```python
result = conn.execute("MATCH (p:Person) RETURN p.name, p.age")
while result.has_next():
    row = result.get_next()
    print(row[0], row[1])
```

## Arrow Integration

LadybugDB supports direct Arrow memory integration for zero-copy data exchange:

```python
import pyarrow as pa

# Create and register an Arrow table
table = pa.table({
    "city": ["New York", "Los Angeles", "Chicago"],
    "population": [8419000, 3980000, 2716000],
})
conn.create_arrow_table("cities", table)

# Query Arrow-backed data
result = conn.execute(
    "MATCH (n:cities) WHERE n.population > 3000000 RETURN n.city ORDER BY n.population"
)
```

You can also register memory manually and get an Arrow ID:

```python
table = pa.table({'id': [1, 2], 'value': ['a', 'b']})
arrow_id = conn.register_arrow("my_arrow_table", table)

conn.execute(
    "CREATE NODE TABLE MyData (id INT64, value STRING, PRIMARY KEY(id)) "
    f"WITH (storage='arrow://{arrow_id}')"
)
```

## Shell Commands in Python

The meta-commands available in the shell work as Cypher statements or CALL procedures:

```python
# List tables
result = conn.execute("CALL show_tables()")
print(result.get_as_df())

# List indexes
result = conn.execute("CALL show_indexes()")
print(result.get_as_df())

# Table info
result = conn.execute("CALL table_info('Person')")
print(result.get_as_df())

# List functions
result = conn.execute("CALL show_functions()")
print(result.get_as_df())

# List macros
result = conn.execute("CALL show_macros()")
print(result.get_as_df())
```

## Remote Connection

```python
db = lb.Database("http://localhost:8123")
conn = lb.Connection(db)
```

## Further Reading

- [lbug Shell](../SKILL.md) — shell meta-commands and attach operations
- [JavaScript Interface](javascript-interface.md)
- [Rust Interface](rust-interface.md)
- [Cypher Reference](cypher.md)
- [Foreign Data Wrappers](foreign.md)
- [Data Science](data-science.md)
