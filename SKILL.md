# Migrate from Kuzu to LadybugDB

LadybugDB is the continuation of Kuzu. The migration involves updating package imports and connection code.

## Package Changes

| Language | Old Package | New Package |
|----------|-------------|-------------|
| Python | `kuzu` | `real_ladybug` |
| Node.js | `kuzu` | `@ladbugdb/core` |
| Go | `github.com/kuzudb/go-kuzu` | `github.com/lbugdb/go-ladybug` |
| Rust | `kuzu` | `lbug` |

## Python Migration

### Installation

```bash
# Old
pip install kuzu

# New
pip install real_ladybug
```

### Code Changes

```python
# Old (kuzu)
import kuzu

db = kuzu.Database("myDB")
conn = kuzu.Connection(db)
result = conn.execute("MATCH (n) RETURN n LIMIT 5")
```

```python
# New (ladybug)
import real_ladybug as lb

db = lb.Database("myDB")
conn = lb.Connection(db)
result = conn.execute("MATCH (n) RETURN n LIMIT 5")
```

## Database Compatibility

LadybugDB is built on top of Kuzu, so existing `.kuzu` database files are generally compatible. If needed, use the EXPORT/IMPORT commands:

```cypher
-- Export from Kuzu
EXPORT DATABASE '/path/to/export';

-- Import to Ladybug
IMPORT DATABASE '/path/to/export';
```

## Connection String Changes

```python
# Embedded (unchanged)
db = lb.Database("myDB")

# In-memory (unchanged)
db = lb.Database(":memory:")

# Remote API server
db = lb.Database("http://localhost:8123")
```

## Notes

- KuzuDB is now archived; LadybugDB continues development
- Some extension names may differ between versions
- Check LadybugDB documentation for new features like object storage support

## Reference

- [Cypher Queries](reference/cypher.md)
- [DDL](reference/ddl.md)
- [Data Import/Export](reference/foreign.md)
- [Data Science](reference/data-science.md)
