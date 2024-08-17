# Essencials Skils
En esta sección, aprenderás algunas habilidades ampliamente aplicables que te ayudarán a aplicar lo que has aprendido de nuestros laboratorios a otros objetivos vivos. Cubrimos una gama de consejos y trucos generales, así como cómo utilizar algunas de las características menos conocidas de Burp para optimizar su flujo de trabajo. También hemos proporcionado algunos laboratorios adicionales, para que puedas probar esto por ti mismo.

## Obuscar ataques mediante codificación
Simplemente copiar los ataques de nuestras soluciones de laboratorio e intentarlos en sitios reales sólo te llevará hasta ahora. Los sitios web que usted probará a menudo ya serán auditados por otros usuarios y se les aplicaron una serie de parches. Para llevar tus habilidades más lejos, necesitarás adaptar las técnicas que has aprendido a superar estos obstáculos adicionales, desenterrando vulnerabilidades que otros probadores pueden haber pasado por alto.

En esta sección, le proporcionaremos algunas sugerencias sobre cómo puede ofuscar cargas útiles dañinas para evadir filtros de entrada y otras defensas defectuladas. Especízco, aprenderás a usar codificaciones estándar para aprovechar las configuraciones erróneas y las discrepancias de manejo entre sistemas conectados.

La info de ofuscar
https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings
El laboratorio de xml de ofuscación
https://portswigger.net/web-security/sql-injection/lab-sql-injection-with-filter-bypass-via-xml-encoding

## Uso de Burp Scanner durante pruebas manuales

Las pruebas para ciertos tipos de vulnerabilidad pueden ser bastante tediosas, especialmente las que implican probar numerosas técnicas de inyección en cada entrada controlable. Hacer esto manualmente es a menudo poco práctico debido a las limitaciones de tiempo de la vida real, lo que puede llevar a que no se pierdan vulnerabilidades críticas.

Hay una serie de maneras de optimizar su flujo de trabajo mediante el uso de Burp Scanner para complementar su propio conocimiento e intuición. Esto no sólo reduce la posibilidad de que usted pasa por alto las cosas, sino que puede ahorrar tiempo valioso al ayudarle a identificar rápidamente los vectores de ataque potenciales. Esto significa que puede concentrar su tiempo y esfuerzo en cosas que no se pueden automatizar fácilmente, como determinar cómo explotar el comportamiento vulnerable o encadenarlo con sus otros hallazgos.
El laboratorio

https://portswigger.net/web-security/essential-skills/using-burp-scanner-during-manual-testing

### Burp Suite scanner
Uso de Burp Scanner durante pruebas manuales
En esta sección, le mostraremos una serie de maneras en que puede optimizar su flujo de trabajo de pruebas manual mediante el uso de Burp Scanner para complementar su propio conocimiento e intuición. Esto no sólo le ayudará a cubrir más terreno, usted será capaz de pasar su tiempo donde importa en lugar de en el trabajo preliminar tedioso.

#### Escaneando una petición específica
Cuando se encuentra con una función o comportamiento interesante, su primer instinto puede ser enviar las solicitudes relevantes a Repeater o Intruso e investigar más. Pero a menudo es beneficioso entregar la petición a Burp Scanner también. Puede llegar a trabajar en los aspectos más repetitivos de las pruebas mientras pones tus habilidades a un mejor uso en otro lugar.

Si hace clic derecho en una solicitud y seleccione Do escáner activo, Burp Scanner usará su configuración predeterminada para auditar sólo esta solicitud.

Do active Scan
Puede que esto no alcance hasta la última vulnerabilidad, pero potencialmente podría marcar cosas en segundos que de otra manera podrían haber tomado horas para encontrar. También puede ayudarle a descartar ciertos ataques casi inmediatamente. Todavía puede realizar pruebas más específicas usando las herramientas manuales de Burp, pero podrá concentrar sus esfuerzos en entradas específicas y un rango más estrecho de vulnerabilidades potenciales.

Incluso si ya usa Burp Scanner para ejecutar un gateo general y la auditoría de nuevos objetivos, cambiar a este enfoque más específico de la auditoría puede reducir masivamente su tiempo de escaneo general.

##### EL laboratio
