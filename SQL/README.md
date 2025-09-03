# SQL

## トランザクションテーブル(プレフィックスがt_で始まるテーブル)のDELETE文を一括発行

```sql
SELECT CONCAT('DELETE FROM ', table_name, ';') AS del_stmt
FROM information_schema.tables
WHERE table_schema = DATABASE()
  AND table_name LIKE 't\_%';
```

## トランザクションテーブル(プレフィックスがt_で始まるテーブル)の件数カウントを一括発行

```sql
SELECT CONCAT(
    'SELECT \'', table_name, '\' AS tbl, COUNT(*) AS cnt FROM ', table_name, ';'
) AS cnt_stmt
FROM information_schema.tables
WHERE table_schema = DATABASE()
  AND table_name LIKE 't\_%';
```
