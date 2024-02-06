## DBA Tips

Run commands in transaction
```sql
START TRANSACTION
-- Do something
-- It fails
ROLLBACK
-- Try again
-- Seems OK
COMMIT;
```

Use temporary tables
``
```sql
CREATE TABLE new_tbl [AS] SELECT * FROM orig_tbl;
```