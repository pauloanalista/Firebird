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
## Retorna conteudo do campo separado por vírgulas
```sql
select list(g.guia) from tra_guia_acesso g
```
## Obtém aproximadamente a quantidade de registro das tabelas
```sql
SELECT RDB$RELATIONS.RDB$RELATION_NAME,
CASE
WHEN RDB$INDICES.RDB$STATISTICS = 0 THEN 0
ELSE CAST(1 / RDB$INDICES.RDB$STATISTICS AS INTEGER)
END
FROM RDB$RELATIONS
LEFT JOIN RDB$RELATION_CONSTRAINTS
ON RDB$RELATIONS.RDB$RELATION_NAME = RDB$RELATION_CONSTRAINTS.RDB$RELATION_NAME
AND RDB$CONSTRAINT_TYPE = 'PRIMARY KEY'
LEFT JOIN RDB$INDICES
ON RDB$RELATION_CONSTRAINTS.RDB$INDEX_NAME = RDB$INDICES.RDB$INDEX_NAME
WHERE RDB$VIEW_BLR IS NULL AND RDB$RELATION_ID >= 128
ORDER BY 2 DESC;
```

## Tabelas sem PK - Chave Primaria
```sql
SELECT RDB$RELATION_NAME AS Tabela
FROM RDB$RELATIONS
WHERE RDB$RELATION_TYPE IN (0, 4, 5)
AND (RDB$SYSTEM_FLAG = 0 OR RDB$SYSTEM_FLAG IS NULL)
AND RDB$RELATION_NAME NOT IN
(SELECT RDB$RELATION_NAME FROM RDB$RELATION_CONSTRAINTS
WHERE RDB$CONSTRAINT_TYPE = 'PRIMARY KEY')
ORDER BY RDB$RELATION_NAME;

```

