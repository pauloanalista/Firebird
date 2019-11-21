# Firebird - Rotinas de Apoio
Conjunto de scrips úteis em nosso dia a dia.

### Consultar Triggers
```sql
SELECT * FROM RDB$TRIGGERS  P1 WHERE  P1.RDB$TRIGGER_SOURCE LIKE UPPER('%tcb_escala_exportada%')
```
## Consultar Procedures
```sql
SELECT * FROM RDB$PROCEDURES  P2 WHERE  P2.RDB$PROCEDURE_SOURCE LIKE UPPER('%tcb_escala_exportada%')
```
