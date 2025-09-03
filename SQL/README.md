# SQL

## トランザクションテーブル(プレフィックスがt_で始まるテーブル)のDELETE文を一括発行

```sql
SELECT CONCAT('DELETE FROM ', table_name, ';') AS del_stmt
FROM information_schema.tables
WHERE table_schema = DATABASE()
  AND table_name LIKE 't\_%';
```

## トランザクションテーブル(プレフィックスがt_で始まるテーブル)でデータが存在するテーブルの一覧取得

```sql
SELECT
    TABLE_NAME,
    TABLE_ROWS
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = DATABASE()
  AND TABLE_NAME LIKE 't\_%'
  AND TABLE_ROWS > 0;
```
