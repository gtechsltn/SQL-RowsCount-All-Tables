# SQL-RowsCount-All-Tables

```
SELECT
    SCHEMA_NAME(t.schema_id) AS SchemaName,
    t.[name] AS TableName,
    SUM(p.rows) AS [RowCount]
FROM
    sys.tables t
INNER JOIN
    sys.partitions p ON t.object_id = p.object_id
WHERE 1 = 1
	AND t.[name] not like 'index%'
	AND t.[name] not like 'rm_%'
	AND t.[name] not like 'sche%'
	AND t.[name] not like 'solr%'
	AND t.[name] not like 'temp%'
	AND t.[name] not like '%log%'
    AND p.index_id IN (0, 1) -- 0 for heaps, 1 for clustered indexes (main data storage)
    AND t.is_ms_shipped = 0 -- Exclude system tables
GROUP BY
    t.schema_id,
    t.name
HAVING
    SUM(p.rows) <> 0
ORDER BY
    SUM(p.rows) DESC,
    SchemaName,
    TableName;
```
