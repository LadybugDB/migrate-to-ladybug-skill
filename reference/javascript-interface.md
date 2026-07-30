# JavaScript/Node.js Interface

## Installation

```bash
npm install @ladybugdb/core
```

## Quick Start

```javascript
import { Database, Connection } from '@ladybugdb/core';

// Create an in-memory database
const db = new Database(':memory:');
const conn = new Connection(db);

// Create a node table
conn.execute("CREATE NODE TABLE Person (id INT64, name STRING, age INT64, PRIMARY KEY (id))");

// Insert data
conn.execute("CREATE (p:Person {id: 1, name: 'Alice', age: 30})");
conn.execute("CREATE (p:Person {id: 2, name: 'Bob', age: 25})");

// Query
const result = conn.execute("MATCH (p:Person) WHERE p.age > 20 RETURN p.name, p.age");
console.log(result.toArray());
// [ { 'p.name': 'Alice', 'p.age': 30 }, { 'p.name': 'Bob', 'p.age': 25 } ]
```

## Result Sets

Results can be converted to arrays or iterated:

```javascript
// As array of objects
const rows = result.toArray();
rows.forEach(row => console.log(row['p.name'], row['p.age']));

// Iterator
for (const row of result) {
  console.log(row.get('p.name'), row.get('p.age'));
}
```

## Shell Commands in JavaScript

Meta-commands available in the shell work as CALL procedures:

```javascript
// List tables
const tables = conn.execute("CALL show_tables()");
console.log(tables.toArray());

// List indexes
conn.execute("CALL show_indexes()");

// Table info
conn.execute("CALL table_info('Person')");

// List functions
conn.execute("CALL show_functions()");

// List macros
conn.execute("CALL show_macros()");
```

## Remote Connection

```javascript
import { Database } from '@ladybugdb/core';

const db = new Database('http://localhost:8123');
const conn = new Connection(db);
```

## Further Reading

- [lbug Shell](../SKILL.md) — shell meta-commands and attach operations
- [Python Interface](python-interface.md)
- [Rust Interface](rust-interface.md)
- [Cypher Reference](cypher.md)
