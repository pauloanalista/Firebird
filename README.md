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

## Consultar campos de uma tabela
```sql
SELECT * FROM RDB$RELATION_FIELDS A
INNER JOIN RDB$FIELDS B ON (A.RDB$FIELD_SOURCE = B.RDB$FIELD_NAME)
WHERE RDB$RELATION_NAME ='tabela'
```

## Obtém tabelas que possui um campo especifico
```sql
SELECT A.RDB$RELATION_NAME AS TABELA FROM RDB$RELATION_FIELDS A
INNER JOIN RDB$FIELDS B ON (A.RDB$FIELD_SOURCE = B.RDB$FIELD_NAME)
WHERE A.RDB$FIELD_NAME =:PCAMPO
```

