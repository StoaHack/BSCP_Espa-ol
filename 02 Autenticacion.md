# Que es la autenticación

La autenticación es el proceso de verificación de la identidad de un usuario o cliente. Los sitios web están potencialmente expuestos a cualquier persona que esté conectada a Internet. Esto hace que los mecanismos de autenticación robustos sean parte integrante de una seguridad web efectiva.

Hay tres tipos principales de autenticación:
- **Algo que conoces**, como una contraseña o la respuesta a una pregunta de seguridad. Estos a veces se llaman **"factores de conocimiento"**.
- **Algo que tienes**, Este es un objeto físico como un teléfono móvil o una ficha de seguridad. Estos a veces se llaman **"factores de posesión".**
- **Algo que seas o hagas**. Por ejemplo, sus biométricas o patrones de comportamiento. Estos a veces se llaman **"factores de la herencia".**

Los mecanismos de autenticación se basan en una gama de tecnologías para verificar uno o más de estos factores.

# Cuál es la diferencia entre autenticación y autorización?

- La autenticación es **el proceso de verificación** de que un usuario es quien dice ser. 
- La autorización implica **verificar** si se permite a un usuario **hacer algo**.

> Por ejemplo, la autenticación determina si alguien intenta acceder a un sitio web con el nombre de usuario Carlos123Realmente es la misma persona que creó la cuenta. <br> <br>
> Una vez Carlos123está autenticado, sus permisos determinan lo que están autorizados a hacer. Por ejemplo, pueden estar autorizados a acceder a información personal sobre otros usuarios, o realizar acciones como eliminar la cuenta de otro usuario.

# Cómo surgen vulnerabilidades de autenticación?
La mayoría de las vulnerabilidades en los mecanismos de autenticación ocurren de dos maneras:
- Los **mecanismos de autenticación son débiles** porque no protegen adecuadamente contra los ataques de la fuerza bruta.
- Los **defectos lógicos o la codificación deficiente en la implementación** permiten que los mecanismos de autenticación sean eludidos por completo por un atacante. Esto a veces se llama "autenticación rota".

En muchas áreas de desarrollo web, fallas lógicas hacen que el sitio web se comporte de una manera inesperada, lo que puede o no ser un problema de seguridad. Sin embargo, como la autenticación es tan crítica a la seguridad, es muy probable que la lógica de autenticación defectuosa exponga el sitio web a cuestiones de seguridad.

# Cuál es el impacto de la autenticación vulnerable?
El **impacto de las vulnerabilidades de autenticación puede ser severo.** Si un atacante evita la autenticación o fuerza bruta su entrada en la cuenta de otro usuario, tiene acceso a todos los datos y funcionalidades que tiene la cuenta comprometida. Si son capaces de comprometer una cuenta de alto valor, como un administrador del sistema, podrían tomar el control total de toda la aplicación y potencialmente tener acceso a la infraestructura interna.
<br><br>
Incluso comprometer una cuenta de baja privilegiada todavía podría conceder a un atacante el acceso a los datos que de otra manera no deberían tener, como información comercialmente sensible. Incluso si la cuenta no tiene acceso a ningún dato sensible, todavía podría permitir al atacante acceder a páginas adicionales, que proporcionan una nueva superficie de ataque. A menudo, los ataques de alta gravedad no son posibles desde páginas de acceso público,** pero pueden ser posibles desde una página interna.**

# Vulnerabilidad en el inicio de sesión basado en contraseña
Para sitios web que adopten un proceso de inicio de sesión basado en contraseña, los usuarios se registran por sí mismos o se les asigna una cuenta por un administrador. Esta cuenta se asocia con un nombre de usuario único y una contraseña secreta, que el usuario introduce en un formulario de inicio de sesión para autenticarse.
<br>
En este escenario, el hecho de que conozcan la contraseña secreta se toma como prueba suficiente de la identidad del usuario. Esto significa que la seguridad del sitio web se ve comprometida si un atacante es capaz de obtener o adivinar las credenciales de inicio de sesión de otro usuario.
<br>
Esto se puede lograr de varias maneras. Las siguientes secciones muestran cómo un atacante puede usar ataques de fuerza bruta, y algunos de los defectos en la protección de la fuerza bruta. También aprenderá sobre las vulnerabilidades en la autenticación básica HTTP.
## Ataques de fuerza bruta
Un ataque de fuerza bruta es cuando un atacante utiliza un sistema de prueba y error para adivinar credenciales válidas de los usuarios. Estos ataques suelen automatizarlos usando listas de palabras de nombres de usuario y contraseñas. Automatizar este proceso, especialmente utilizando herramientas específicas, permite potencialmente a un atacante hacer un gran número de intentos de inicio de sesión a alta velocidad.
<br>
El forzamiento de la fuerza bruta no siempre es sólo un caso de hacer conjeturas completamente aleatorias en nombres de usuario y contraseñas. Utilizando también la lógica básica o el conocimiento disponible públicamente, los atacantes pueden afinar ataques de fuerza bruta para hacer conjeturas mucho más educadas. Esto aumenta considerablemente la eficiencia de esos ataques. Sitios web que se basan en el inicio de sesión basado en contraseñas como su único método de autenticación de los usuarios puede ser muy vulnerable si no implementan suficiente protección de la fuerza bruta.

### Brute-forcing usernames
Los Usernames son especialmente fáciles de adivinar si se ajustan a un patrón reconocible, como una dirección de correo electrónico. Por ejemplo, es muy común ver los inicios de sesión de las empresas en el formato firstname.lastname@somecompany.com. Sin embargo, incluso si no hay un patrón obvio, a veces incluso las cuentas de alto precio se crean utilizando nombres de usuario predecibles, tales como admino o administrator.

Durante la auditoría, compruebe si el sitio web revela públicamente posibles nombres de usuario. Por ejemplo, ¿puede acceder a los perfiles de los usuarios sin iniciar sesión? Incluso si el contenido real de los perfiles está oculto, el nombre utilizado en el perfil es a veces el mismo que el nombre de usuario de inicio de sesión. También deberías comprobar las respuestas HTTP para ver si se revela alguna dirección de correo electrónico. Ocasionalmente, las respuestas contienen direcciones de correo electrónico de usuarios con privilegios elevados, como administradores o soporte de TI. 

### Brute-forcing passwords
 Las contraseñas también pueden ser forzadas, y la dificultad varía en función de la fuerza de la contraseña. Muchos sitios web adoptan algún tipo de política de contraseñas, que obliga a los usuarios a crear contraseñas de alta entropía que son, al menos teóricamente, más difíciles de descifrar utilizando únicamente la fuerza bruta. Esto suele implicar la imposición de contraseñas con:

- Un número mínimo de caracteres
- Una mezcla de minúsculas y mayúsculas
- Al menos un carácter especial

Sin embargo, si bien las contraseñas de alta en introducción son difíciles de romper para las computadoras por sí solas, podemos utilizar un conocimiento básico del comportamiento humano para explotar las vulnerabilidades que los usuarios introducen involuntariamente a este sistema. En lugar de crear una contraseña fuerte con una combinación aleatoria de caracteres, los usuarios a menudo toman una contraseña que pueden recordar y tratar de palancarla en la instalación de la política de contraseñas. Por ejemplo, si mypasswordno está permitido, los usuarios pueden probar algo como Mypassword1!o o Myp4$$w0rdEn vez de eso.

En los casos en que la política requiere que los usuarios cambien sus contraseñas de forma regular, también es común que los usuarios hagan cambios menores y predecibles en su contraseña preferida. Por ejemplo, Mypassword1!se convierte Mypassword1?o o Mypassword2!.

Este conocimiento de credenciales probables y patrones predecibles significa que los ataques de fuerza bruta a menudo pueden ser mucho más sofisticados, y por lo tanto eficaces, que simplemente iterando a través de cada combinación posible de caracteres.

### Enumeración del usuario
La enumeración de nombre de usuario es cuando un atacante es capaz de observar cambios en el comportamiento del sitio web con el fin de identificar si un nombre de usuario dado es válido.

La enumeración de nombre de usuario típicamente ocurre en la página de inicio de sesión, por ejemplo, cuando introduce un nombre de usuario válido pero una contraseña incorrecta, o en los formularios de registro cuando se introduce un nombre de usuario que ya está tomado. Esto reduce en gran medida el tiempo y el esfuerzo requeridos para brute-for-force un login porque el atacante es capaz de generar rápidamente una lista de nombres de usuario válidos.


Mientras intenta hacer una página de inicio de sesión de fuerza bruta, debe prestar especial atención a cualquier diferencia en:

> Códigos de estado : Durante un ataque de fuerza bruta, el código de estado HTTP devuelto es probable que sea el mismo para la gran mayoría de las conjeturas porque la mayoría de ellos estarán equivocados. Si una conjetura devuelve un código de estado diferente, esto es una fuerte indicación de que el nombre de usuario era correcto. Es la mejor práctica que los sitios web devuelva siempre el mismo código de estado independientemente del resultado, pero esta práctica no siempre se sigue.
<br><br>
> Mensajes de error : A veces el mensaje de error devuelto es diferente dependiendo de si tanto el nombre de usuario Y contraseña son incorrectos o sólo la contraseña era incorrecta. Es la mejor práctica que los sitios web utilicen mensajes idénticos y genéricos en ambos casos, pero pequeños errores de mecía a veces se arrastran. Sólo un personaje fuera de lugar hace que los dos mensajes sean distintos, incluso en los casos en que el personaje no es visible en la página render.
<br><br>
> Tiempos de respuesta : Si la mayoría de las solicitudes se manejaron con un tiempo de respuesta similar, cualquiera que se desviara de esto sugiere que algo diferente estaba sucediendo entre bastidores. Esta es otra indicación de que el nombre de usuario adivinado podría ser correcto. Por ejemplo, un sitio web sólo podría comprobar si la contraseña es correcta si el nombre de usuario es válido. Este paso adicional podría causar un ligero aumento en el tiempo de respuesta. Esto puede ser sutil, pero un atacante puede hacer este retraso más obvio al introducir una contraseña excesivamente larga que el sitio web tarda notablemente más tiempo en manejar.

#### Laboratorio Enumeró el nombre de usuario a través del mesnaje de respuesta

Resumen

Este laboratorio es vulnerable a la enumeración de nombres de usuario y a los ataques de la fuerza bruta de la contraseña. Tiene una cuenta con un nombre de usuario y contraseña predecibles, que se puede encontrar en las siguientes listas de palabras:

    Nombres de usuario candidatos
    Contraseñas de candidatos

Para resolver el laboratorio, enumera un nombre de usuario válido, la contraseña de este usuario, luego accede a la página de su cuenta.

```
1.- Se va a la pagina de login
2.- Se autentica con un usuario comun como admin => da como resultado usuario no valido
3.- Atraves de intruder se carga la lista de usuarios de burpsuite y se busca la respuesta con menor tiempo o con un tamaño diferente, que da como resultado un error diferente a Invalid user, en mi caso el usuario es: autodiscover
4.- Ahora se estable el usuario y se cambia el parametro de contraña por la lista de contraseña de burp suite en intruder y lo mismo, en este caso envio un estado diferente al 200, envio un 302 que significa que la respuesta se direcciona al mismo lugar por lo que da un error

Respuesta oficial
Es lo mismo pero con otras palabras jajaja
```
#### Laboratorio Enumeró el nombre de usuario a través de un cambio sutil
Este laboratorio es sutilmente vulnerable a la enumeración de nombres de usuario y a los ataques de la fuerza bruta de contraseña. Tiene una cuenta con un nombre de usuario y contraseña predecibles, que se puede encontrar en las siguientes listas de palabras:

    Nombres de usuario candidatos
    Contraseñas de candidatos

Para resolver el laboratorio, enumera un nombre de usuario válido, la contraseña de este usuario, luego accede a la página de su cuenta.
```
La diferencia radica el punto 
Invalid username or password.
Invalid username or password

El response es dinamico hasta aleartorio o con ciertos rangos
al igual que el tiempo de respuesta

Pero la diferencia radica en el mensaje

De ahi en fuera se hace igual que el anterior solo que con el filtro inversa

Solución oficial

Es diferente
1.-Con Proxy Captura la consulta
2.- La envia a intruder y configuras sniper/lista simple y cargas la contraseñas
3.- [Lo importante] en la pestaña de settings de intruder en la sección de** Grep-Extract > Add > fecth response (hace la consulta y muestra el response) > seleccionas con el mouse la entrada y todos los parametros del add se llenan, das clicl en ok**
4.- Ahora clic en el ataque y notaras que tienes una nueva columna con lo que seleccionaste
5.- Hacer lo mismo con el parametro de contraseña (lo de grep extract aqui es opcional ya que el mensaje de error sera el mismo y el codigo de estado revelará cual es la contraseña correcta)

Agenda
football
```
#### Laboratorio Enumeró el nombre de usuario a través del momento de la respuesta
Este laboratorio es vulnerable a la enumeración de nombre de usuario usando sus tiempos de respuesta. Para resolver el laboratorio, enumera un nombre de usuario válido, la contraseña de este usuario, luego accede a la página de su cuenta.

    Sus credenciales: wiener:peter
    Nombres de usuario candidatos
    Contraseñas de candidatos

> X-Forwarded-For: 203.0.113.111  este header ayuda para evadir la portección de ataques de fuerza bruta a traves de una IP
```
Aqui si necesite leer la documentación

1.- Estaba viendo el comportamiento y cuanto tardaba la consulta si le cambiaba el nombre del usuario hasta que aparecio que estaba bloqueado por multiples intentos de login y tuve que investigar, además de leer el hint el cual menciona que hay un registro de IP, que usualmente se hace con el header X-Forwarded-For:IP
2.- Despues el deber ser (porque lei) es ver el comportamiento cambiando la longitud de la contraseña, con el usuario real vs con un usuario falso, si el usuario es verdadero y la contraseña es incorrecta con una gran longitud el tiempo de respuesta es bastante grande, el tiempo de repsuesta es proporcional al tamaño de la contraseña
3.- Sabiendo lo anterior ya se puede proceder al ataque con intruder pero ahora se utilizará Pithcfork, que permite que cada entrada tenga un valor diferente



    Solicitud uno:
        Posición 1 = Primera carga útil del Conjunto 1.
        Posición 2 = Primera carga útil del Conjunto 2.

    Solicitud dos:
        Posición 1 = Segunda carga útil del Conjunto 1.
        Posición 2 = Segunda carga útil del Conjunto 2.

    Solicitud tres:
        Posición 1 = Tercera carga útil del Conjunto 1.
        Posición 2 = Tercera carga útil del Conjunto 2.
```
**Más info sobre los ataques de intruder: https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/attack-types**

### Protección de fuerza bruta defectuosa
Es muy probable que un ataque de fuerza bruta implique muchas conjeturas fallidas antes de que el atacante comprometa con éxito una cuenta. Lógicamente, la protección de la fuerza bruta gira en torno a tratar de hacerlo lo más complicado posible para automatizar el proceso y ralentizar el ritmo al que un atacante puede intentar iniciar sesión. Las dos formas más comunes de prevenir los ataques con fuerza bruta son:

- Bloquear la cuenta que el usuario remoto está tratando de acceder si hace demasiados intentos de inicio de sesión fallidos
- Bloquear la dirección IP del usuario remoto si hace demasiados intentos de inicio de sesión en rápida sucesión

Ambos enfoques ofrecen diversos grados de protección, pero ninguno de los dos es invulnerable, especialmente si se implementa usando lógica defectiva.

Por ejemplo, a veces puede encontrar que su IP está bloqueada si no entra en sesión demasiadas veces. En algunas implementaciones, el contador para el número de intentos fallidos se restablece si el propietario IP inicia sesión con éxito. Esto significa que un atacante simplemente tendría que iniciar sesión en su propia cuenta cada pocos intentos para evitar que este límite se alcance alguna vez.

En este caso, simplemente incluir sus propias credenciales de inicio de sesión a intervalos regulares a través de la lista de palabras es suficiente para hacer esta defensa prácticamente inútil.

> En resumen, la protección de fuerza bruta bloqueando por IP puede tener el defecto si se inicia sesión de vez en cuando ya que podria resetear el contador
>
#### Laboratorio: Protección contra la fuerza bruta rota, bloque de IP
 Este laboratorio es vulnerable debido a un fallo lógico en su protección contra la fuerza bruta de contraseñas. Para resolver el problema, fuerza bruta la contraseña de la víctima, luego inicia sesión y accede a la página de su cuenta.

    Tus credenciales: wiener:peter
    Nombre de usuario de la víctima: carlos
    Contraseñas candidatas
 
```
Es posible que los usuarios avanzados quieran resolver esta práctica de laboratorio utilizando una macro o la extensión Turbo Intruder. Sin embargo, es posible resolver la práctica de laboratorio sin utilizar estas funciones avanzadas.

wiener:peter => pasa

wiener:petesadasdr > incorrect password

3 intentos y bloquea

La idea la tenia es decir con pitchfork

la lista de usuarios seria con wiener y carlos solo esos usuarios con la misma cantidad de contraseñas de tal manera que considan la contraseña de wiener con peter
Aquí el ejemplo
wiener => peter
carlos => pass1
wiener => peter
carlos => pass2

EL problema que hace consultas simultaneas por lo que se bloquea de manera inmediata (no sabia que se podia limitar), se limita con

BURP SUITE > INTRUDER > PESTAÑA - RESOURCE POOL > create new resouce pool > le pones que maximo 1

Listo ya no bloquea, para encontrar la contraseña rapido, busqueda inversa de wiener y estatus 300 y listo aparece

```
### Bloqueo de usuarios / Account locking

Una de las acciones que los sitios web utilizan para prevenir los actaques de fuerza brutas es bloquear el usuario si ciertos criterios se cimplen, usualmente un numero establecido de intentos fallidos. 
AL igual que con los errores de inicio de sesión, las respuesta del servidor indican que una cuenta está bloqueada tambien pueden ayudar a un atacante a enumerar usuarios

Bloquear usuarios ofrece un cierta protección en contra de los ataques de fuerza bruta a usuarios objetivo. sin mebago, esta prevención falla de manera adecuada en prevenir los ataques de fuerza bruta
en caso de un que un atacante solo es intentado de ganar acceso a cualquier cuenta aleatoria  que quiera.


1.- Establecer una lista de nombres de usuario candidatos que probablemente sean válidos. Esto puede hacerse mediante una enumeración de nombres de usuario o simplemente basándose en una lista de nombres de usuario comunes.
2.- Decide una lista muy reducida de contraseñas que creas que al menos un usuario puede tener. Lo más importante es que el número de contraseñas que elijas no supere el número de intentos de inicio de sesión permitidos. Por ejemplo, si has calculado que el límite es de 3 intentos, tienes que elegir un máximo de 3 contraseñas.
3.- Utilizando una herramienta como Burp Intruder, pruebe cada una de las contraseñas seleccionadas con cada uno de los nombres de usuario candidatos. De esta forma, podrá intentar la fuerza bruta en todas las cuentas sin activar el bloqueo de cuentas. Sólo es necesario que un único usuario utilice una de las tres contraseñas para comprometer una cuenta.

El bloqueo de cuentas tampoco protege contra los ataques de relleno de credenciales. Esto implica el uso de un diccionario masivo de pares nombre de usuario:contraseña
 compuestos por credenciales de acceso auténticas robadas en filtraciones de datos. 
El relleno de credenciales se basa en el hecho de que mucha gente reutiliza el mismo nombre de usuario y contraseña en varios sitios web y, por tanto, existe la de que algunas de las credenciales comprometidas en el diccionario sean también válidas en el sitio web objetivo. 
también sean válidas en el sitio web objetivo. El bloqueo de cuentas no protege contra el relleno de credenciales porque cada nombre de usuario sólo se una sola vez. El relleno de credenciales es particularmente peligroso porque  a veces puede resultar en que el atacante comprometa muchas cuentas 
cuentas diferentes con un solo ataque automatizado.

> Es decir que en un brecha de seguridad el usuario no haya actualizado sus contraseñas por lo que es vulnerable su cuenta

#### Laboratorio Listado de nombre de usuario vía bloqueo de cuenta

Este laboratorio es vulnerable a la enumeración de nombres de usuario. Utiliza bloqueo de cuentas, pero esto contiene un fallo lógico. Para resolver el laboratorio, enumera un nombre de usuario válido, la contraseña de este usuario, luego accede a la página de su cuenta.

    Nombres de usuario candidatos
    Contraseñas de candidatos
```
La teoria poner 4 contraseeñas asi lo bloqueara por lo que se puede conocer cual es el usuario bueno
si ponemos 3 no lo bloquea pero como sabemos que accede > si accede envia otro mensaje
 hay un fallo logico

Hacemos el deber ser aunque no lo proceso del todo o no me gusta es decir,
probamos todos los nombres con las primeras 3 contraseñas de la lista si alguna cuenta tiene una respuesta diferente al del error ya la hicimos

No funciono
Mejor lo hice como lo habia planeado de 4 contraseñas asi lo habre bloqeuado jejeje
SI lo encontre así con el user / password
am / qwerty

SELECT ID FROM USERS WHERE USERNAME= 'USER' AND PASSWORD = 'PASS'
qwerty' AND 1=1--
SELECT ID FROM USERS WHERE USERNAME= 'am' AND PASSWORD = 'qwerty' AND 1=1--''

SELECT ID FROM USERS WHERE USERNAME= 'USER' AND PASSWORD = 'PASS' AND TRYS<=3

CREATE STORE PROCEDURE LOGIN (USER, PASS)
COUNT INT 0
IF EXISTS SELECT ID WHERE USERNAME = USER
 IF EXISTS SELECT ID WHERE USERNAME = USER  AND FLAG=FALSE
  IF EXISTS SELECT ID WHERE USERNAME = USER AND PASSWORD=PASS AND COUNT<=3
  RETURN PASS
 ELSE
  COUNT++
  IF COUNT >= 3
  FLAG = TRUE
ELSE
RETURN 0

la falla logica es que apesar de que tuviera el "bloqueo" te iba a dejar pasar siempre y cuando tuvieras la contraseña correcta

el oficial hace en el cluster bomb un tipo de ataque null payload en el paramtro de la contraseña

```
