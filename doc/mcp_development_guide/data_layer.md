# Data Layer & Database

## Table of Contents
- [Database Adapter Strategy](#database-adapter-strategy)
- [SQLite Initialization](#sqlite-initialization)
- [FTS & Health Checks](#fts--health-checks)

## Database Adapter Strategy
The database adapter tries native `better-sqlite3` first and falls back to `sql.js`.

```ts
try {
  const adapter = await createBetterSQLiteAdapter(dbPath);
  return adapter;
} catch {
  const adapter = await createSQLJSAdapter(dbPath);
  return adapter;
}
```

## SQLite Initialization
The MCP server locates the database file and initializes repositories and services.

```ts
const possiblePaths = [
  path.join(process.cwd(), 'data', 'nodes.db'),
  path.join(__dirname, '../../data', 'nodes.db'),
  './data/nodes.db'
];

this.db = await createDatabaseAdapter(dbPath);
this.repository = new NodeRepository(this.db);
this.templateService = new TemplateService(this.db);
```

## FTS & Health Checks
Database health checks ensure the main nodes table and FTS index are present.

```ts
const nodeCount = this.db.prepare('SELECT COUNT(*) as count FROM nodes').get() as { count: number };

const ftsExists = this.db.prepare(`
  SELECT name FROM sqlite_master
  WHERE type='table' AND name='nodes_fts'
`).get();
```
