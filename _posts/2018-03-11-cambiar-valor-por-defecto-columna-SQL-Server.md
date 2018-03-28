---
published: true
layout: post
title: Cambiar valor por defecto a columna en SQL Server
---
En SQL Server a diferencia de otras bases de datos al crear un valor por defecto a un campo, este queda como un constraint, dentro de la tabla, lo que ocasiona que al querer cambiar el valor por defecto o eliminar la columna de la base de datos, aparezca la restricción. En ambientes controlados, es sencillo, encontrar el nombre del constraint y eliminarlo, pero, al tener un producto instalado en varias bases de datos, el valor de este constraint puede ser diferente en cada base de datos, por lo que se debe hacer de la siguiente manera:

```sql
DECLARE @ConstraintName nvarchar(200)
SELECT @ConstraintName = Name 
FROM SYS.DEFAULT_CONSTRAINTS
WHERE PARENT_OBJECT_ID = OBJECT_ID('__TableName__')
AND PARENT_COLUMN_ID = 
    (SELECT column_id 
     FROM sys.columns
     WHERE NAME = N'__ColumnName__'
     AND object_id = OBJECT_ID(N'__TableName__'))
IF @ConstraintName IS NOT NULL
EXEC('ALTER TABLE __TableName__ DROP CONSTRAINT ' + @ConstraintName);
```