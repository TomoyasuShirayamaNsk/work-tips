# SQL

## トランザクションテーブル(プレフィックスがt_で始まるテーブル)のDELETE文を一括発行

```sql
SELECT CONCAT('DELETE FROM ', table_name, ';') AS del_stmt
FROM information_schema.tables
WHERE table_schema = DATABASE()
  AND table_name LIKE 't\_%';
```
