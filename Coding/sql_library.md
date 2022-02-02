## Postgres

Find all Foreign Key relationships

```sql
SELECT
    CASE WHEN rc.delete_rule <> 'NO ACTION' THEN '(' || rc.delete_rule || ' DELETE)' ELSE '('||rc.delete_rule||')' END
        || ' ' || tc.table_schema || '.' || tc.table_name || '.' || kcu.column_name || ' -> ' || ccu.table_schema
        || '.' || ccu.table_name || '.' || ccu.column_name as constraint,
    tc.constraint_name,
    tc.table_schema || '.' || tc.table_name as on_table,
    ccu.table_schema || '.' || ccu.table_name as referenced_table,
    ' ALTER TABLE ' || tc.table_schema || '.' || tc.table_name || E'\n' ||
        ' ADD FOREIGN KEY (' || kcu.column_name || ')' || E'\n' ||
        ' REFERENCES ' || ccu.table_schema || '.' || ccu.table_name || ' (' || ccu.column_name || ')' || E'\n' ||
        CASE WHEN rc.match_option <> 'NONE' THEN
        ' MATCH ' || rc.match_option || E'\n' ELSE '' END ||
        CASE WHEN rc.update_rule <> 'NO ACTION' THEN
        ' ON UPDATE ' || rc.update_rule || E'\n' ELSE '' END ||
        CASE WHEN rc.delete_rule <> 'NO ACTION' THEN
        ' ON DELETE ' || rc.delete_rule ELSE E'\n' END ||
        ';' AS statement
FROM information_schema.table_constraints AS tc
JOIN information_schema.key_column_usage AS kcu
ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
JOIN information_schema.constraint_column_usage AS ccu
ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
JOIN information_schema.referential_constraints AS rc
ON tc.constraint_name=rc.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY'
order by rc.delete_rule, ccu.table_schema, ccu.table_name
```