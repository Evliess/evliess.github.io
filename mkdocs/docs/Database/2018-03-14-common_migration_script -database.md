---
layout: index
title: "Migration Scripts Sample"
category: database
date: 2018-06-25 15:17:55
---

> To check that a table exists

```sql
-- does a particular table exist
IF EXISTS ( SELECT  1
        FROM    information_schema.Tables
        WHERE   table_schema = 'MySchema'
                AND TABLE_NAME = 'MyTableName' )
PRINT 'the table exists' 
ELSE
PRINT 'The table isn''t there'
```

> To check that a function exists

```sql
-- does a particular function exist
IF EXISTS ( SELECT  1
        FROM    information_schema.Routines
        WHERE   ROUTINE_NAME = 'MyFunctionName'
                AND ROUTINE_TYPE = 'FUNCTION'
                AND ROUTINE_SCHEMA = 'MySchema' )
PRINT 'the function exists' 
ELSE
PRINT 'The function isn''t there'
```

> To check that a procedure exists

```sql
-- does a particular procedure  exist
IF EXISTS ( SELECT  1
        FROM    information_schema.Routines
        WHERE   ROUTINE_NAME = 'MyProcedureName'
                AND ROUTINE_TYPE = 'PROCEDURE'
                AND ROUTINE_SCHEMA = 'MySchema' )
PRINT 'the procedure exists' 
ELSE
PRINT 'The procedure isn''t there'
```

> To check that a particular column exists in a table exists

```sql
-- does a particular column exist in a table
IF EXISTS ( SELECT  1
        FROM    information_schema.COLUMNS
        WHERE   table_schema = 'MySchema'
                AND TABLE_NAME = 'MyTableName'
                AND column_Name = 'MyColumnName' )
PRINT 'the colmun exists' 
ELSE
PRINT 'The column isn''t there'
```

> To check whether a column has any sort of constraint

```sql
IF EXISTS ( SELECT  1
        FROM    information_schema.CONSTRAINT_column_USAGE
        WHERE   table_schema = 'MySchema'
                AND TABLE_NAME = 'MyTableName'
                AND column_Name = 'MyColumnName' )
PRINT 'there is a constraint on the column' 
ELSE
PRINT 'no constraint'
```

> To check that a column has a check constraint

```sql
IF EXISTS ( SELECT  1
        FROM    information_schema.CONSTRAINT_column_USAGE CCU
                INNER JOIN information_schema.CHECK_CONSTRAINTS CC ON CC.constraint_name = CCU.constraint_NAME
        WHERE   table_schema = 'MySchema'
                AND TABLE_NAME = 'MyTableNames'
                AND column_Name = 'MyColumnName' )
PRINT 'there is a check constraint on the column' 
ELSE
PRINT 'no check constraint'
```

> To check that a column has a foreign key constraint

```sql
--is there a foreign key constraint on the column	
IF EXISTS ( SELECT  1
    FROM    information_schema.CONSTRAINT_column_USAGE CCU
            INNER JOIN information_schema.REFERENTIAL_CONSTRAINTS CC ON CC.constraint_name = CCU.constraint_NAME
    WHERE   table_schema = 'MySchema'
            AND TABLE_NAME = 'MyTableName'
            AND column_Name = 'MyColumnName' )
PRINT 'there is a referential constraint on the column' 
ELSE
PRINT 'no referential constraint'
```

> To check that a column participates in a primary key

```sql
--is the column (part of ) a primary key?	
    IF EXISTS ( SELECT  1
        FROM    information_schema.CONSTRAINT_column_USAGE CCU
                INNER JOIN information_schema.TABLE_CONSTRAINTS CC ON CC.constraint_name = CCU.constraint_NAME
        WHERE   CCU.table_schema = 'MySchema'
                AND CCU.TABLE_NAME = 'MyTableName'
                AND column_Name = 'MyColumnName'
                AND constraint_Type = 'PRIMARY KEY' )
PRINT 'the column is involved in a primary key' 
ELSE
PRINT 'not involved in a primary key'
```

> Reference Link  

- https://www.red-gate.com/simple-talk/sql/database-administration/using-migration-scripts-in-database-deployments/