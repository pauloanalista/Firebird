# Firebird - Rotinas de Apoio
Conjunto de scrips úteis em nosso dia a dia.
### Exibir Consultas que estão rodando no momento
```sql
SELECT  
      MS.MON$SQL_TEXT AS CONSULTA, 
      ATT.MON$REMOTE_ADDRESS AS IP, 
      ATT.MON$REMOTE_PROTOCOL AS PROTOCOLO, 
      ATT.MON$REMOTE_PROCESS AS PROCESSO 
  FROM MON$STATEMENTS MS
INNER JOIN MON$ATTACHMENTS ATT 
        ON (ATT.MON$ATTACHMENT_ID = MS.MON$ATTACHMENT_ID)
```

### Consultar Triggers
```sql
SELECT * FROM RDB$TRIGGERS  P1 WHERE  P1.RDB$TRIGGER_SOURCE LIKE UPPER('%tcb_escala_exportada%')
```
## Consultar Procedures
```sql
SELECT * FROM RDB$PROCEDURES  P2 WHERE  P2.RDB$PROCEDURE_SOURCE LIKE UPPER('%tcb_escala_exportada%')
```

## Exibir campos de uma tabela
```sql
SELECT * FROM RDB$RELATION_FIELDS A
INNER JOIN RDB$FIELDS B ON (A.RDB$FIELD_SOURCE = B.RDB$FIELD_NAME)
WHERE RDB$RELATION_NAME ='tabela'
```

## Pesquisar campo em várias tabelas
```sql
SELECT A.RDB$RELATION_NAME, A.RDB$FIELD_NAME, A.RDB$FIELD_SOURCE,
      A.RDB$FIELD_POSITION, B.RDB$FIELD_TYPE, B.RDB$FIELD_LENGTH FROM RDB$RELATION_FIELDS A
INNER JOIN RDB$FIELDS B ON (A.RDB$FIELD_SOURCE = B.RDB$FIELD_NAME)      
WHERE A.RDB$FIELD_NAME LIKE '%CONTA%'
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

## Exibe triggers de uma tabela
```sql
SELECT * FROM rdb$triggers
where upper(RDB$RELATION_NAME) LIKE '%TABELA%' AND RDB$TRIGGER_SOURCE IS NOT NULL

```

## Analisar consumo do firebird
```sql
SELECT a.mon$attachment_id as "Attachment ID",
        a.mon$server_pid as "Server PID",
        a.mon$state as "State",
        a.mon$attachment_name as "Attachment Name",
        a.mon$user as "User",
        a.mon$role as "Role",
        a.mon$remote_protocol as "Remote Protocol",
        a.mon$remote_address as "Remote Address",
        a.mon$remote_pid as "Remote PID",
        cs.rdb$character_set_name as "Character Set",
        a.mon$timestamp as "Established At",
        a.mon$garbage_collection as "Garbage Collection",
        a.mon$remote_process as "Remote Process",
        a.mon$stat_id as "Statistics ID",
        r.mon$record_seq_reads as "Non-indexed Reads",
        r.mon$record_idx_reads as "Indexed Reads",
        r.mon$record_inserts as "Records Inserted",
        r.mon$record_updates as "Records Updated",
        r.mon$record_deletes as "Records Deleted",
        r.mon$record_backouts as "Records Backed Out",
        r.mon$record_purges as "Records Purged",
        r.mon$record_expunges as "Records Expunged",
        io.mon$page_reads as "Page Reads",
        io.mon$page_writes as "Page Writes",
        io.mon$page_fetches as "Page Fetches",
        io.mon$page_marks as "Page Marks",
        cast(round((MEM.MON$MEMORY_USED / 1024.00 / 1024.00),2) as
 numeric(18,2)) as MEMORY_USED,
        cast(round((MEM.MON$MEMORY_ALLOCATED / 1024.00 / 1024.00),2) as
 numeric(18,2)) as MEMORY_ALLOCATED,
        cast(round((MEM.MON$MAX_MEMORY_USED / 1024.00 / 1024.00),2) as
 numeric(18,2)) as MAX_MEMORY_USED,
        cast(round((MEM.MON$MAX_MEMORY_ALLOCATED / 1024.00 / 1024.00),2) as
 numeric(18,2)) as MAX_MEMORY_ALLOCATED
 from mon$attachments a
 join rdb$character_sets cs on (a.mon$character_set_id =
 cs.rdb$character_set_id)
 left join mon$record_stats r on (a.mon$stat_id = r.mon$stat_id)
 left join mon$io_stats io on (a.mon$stat_id = io.mon$stat_id)
 left join MON$MEMORY_USAGE MEM on (a.mon$stat_id = mem.mon$stat_id)
--  where a.mon$remote_process like '%Trafego_Urbano.exe%'
 order by a.mon$timestamp, a.mon$remote_process
```

## Manutenção Firebird
### Verificação de corrupção da base
```sh
gfix -v -f caminho_da_base -user sysdba -pass masterkey
```
### Correção da corrupção da base
```sh
gfix -m -i caminho_da_base -user sysdba -pass masterkey
```
### Realização de backup da base
```sh
gbak -g -b -z -l -v caminho_da_base caminho_do_arquivo_fbk -user sysdba -pass masterkey
```
### Restauração de backup da base
```sh
gbak -g -c -z -v caminho_do_arquivo_fbk caminho_da_base -user sysdba -pass masterkey
```
Comandos adicionais
### Cancelar todas as transações abertas
```sh
gfix -rollback all nome_base -user sysdba -pass masterkey
```
### Colocar a base em shutdown
```sh
gfix -shut -force 0 nome_base -user sysdba -pass masterkey
```
### Colocar a base em online
```sh
gfix -online nome_base -user sysdba -pass masterkey
```
### Executar o garbage collection na base
```sh
gfix -s nome_base -user sysdba -pass masterkey
```

# VEJA TAMBÉM
## Grupo de Estudo no Telegram
- [Participe gratuitamente do grupo de estudo](https://t.me/blogilovecode)

## Cursos baratos!
- [Meus cursos](https://olha.la/udemy)

## Fique ligado, acesse!
- [Blog ILoveCode](https://ilovecode.com.br)

## Novidades, cupons de descontos e cursos gratuitos
https://olha.la/ilovecode-receber-cupons-novidades
