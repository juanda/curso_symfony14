Unidad 1. Conceptos básicos para el seguimiento del curso.
==========================================================

Aplicaciones web
----------------

La *World Wide Web* es un sistema de documentos de *hipertexto* o *hipermedios* 
enlazados y distribuidos a través de *Internet*. En sus comienzos, la 
interacción entre los usuarios de la *WWW* y sus  servidores era muy reducida: 
a través de un software cliente denominado navegador *web*, el usuario se 
limitaba a solicitar documentos a los servidores y estos respondían a aquellos
con el envío del documento solicitado. A estos documentos que circulaban por el
“espacio *web*” se les denominó *páginas web*. Cada recurso, conocido como 
página *web*, se localiza en este espacio mediante una dirección única que 
describe tanto al servidor como al recurso: la *URL*. Cada página *web* puede 
incorporar las *URL's* de otras páginas como parte de su contenido. De esta 
manera se enlazan unas con otras.

Han pasado casi 20 años desde la aparición de la *Word Wide Web* y, aunque en 
esencia su funcionamiento, basado en el protocolo *HTTP*, sigue siendo el mismo,
la capacidad de interacción entre usuarios y servidores se ha enriquecido 
sustancialmente. De la *página web* hemos pasado a la *aplicación web*; un tipo
de aplicación informática que adopta, de manera natural,  la arquitectura 
cliente-servidor de la *web*. De manera que en las peticiones  al servidor, el
usuario no sólo solicita un recurso, si no que además puede enviar datos. El 
servidor los procesa y elabora la respuesta que corresponda en función de ellos.
Es decir, el servidor construye dinámicamente la página *web* que le envía al 
cliente. Todo el peso de la aplicación reside en el servidor, mientras que el 
cliente, esto es, el navegador *web*, se limita a presentar el contenido que 
recibe mostrándolo al usuario.

Esta evolución comenzó con la aparición de los *CGI's*1, que son aplicaciones 
escritas en cualquier lenguaje de programación y que pueden ser accedidas por 
el servidor *web* a petición del cliente, y ha madurado gracias a la aparición
de los lenguajes de programación del lado del servidor, como *PHP, Java* o 
*Python*, gracias a los cuales los servidores *web* (*apache* como ejemplo más
conocido y usado) han ampliado su funcionalidades; ya no sólo son capaces de 
buscar, encontrar y enviar documentos a petición del cliente, si no que también
pueden procesar peticiones (acceder a base de datos, realizar peticiones a otros
servidores, ejecutar algoritmos, …) y construir los documentos que finalmente
serán enviados al cliente en función de los datos que este les ha proporcionado.

También es relevante en esta evolución de la *web* la incorporación de 
procesamiento en los navegadores *web* mediante lenguajes de *“scripting”* como
*javascript*, que permiten la ejecución de ciertos procesos (casi todos 
relacionados con la manipulación de la interfaz gráfica) en el lado del cliente.
De hecho, en la actualidad existen aplicaciones que delegan gran parte de sus 
procesos al lado del cliente, aunque de todas formas, todo el código es 
proporcionado desde la parte servidora la primera vez que se solicita el recurso.

Todo esto ha sido bautizado con el omnipresente y manido término de *Web 2.0*, 
que en realidad es una manera de referirse a este aumento de la capacidad de 
interacción con el usuario, y que  ha permitido el desarrollo y explosión de 
las redes sociales y la *blogosfera* entre otros muchos fenómenos de la reducida
pero incesante historia de la *World Wide Web*.

El panorama actual se resume en un interés creciente por las aplicaciones *web*, 
hasta el punto de que, en muchos casos, han desplazado a la madura aplicación de
escritorio. Son varias las razones que justifican este hecho y, aunque se trata
de un tema que por su amplitud no abordaremos en detalle, si que señalaremos 
algunas:

* Se mejora la  mantenibilidad de las aplicaciones gracias a su centralización. 
  Al residir la aplicación en el servidor, desaparece el problema de la 
  distribución de las mismas. Por ejemplo, los cambios en la interfaz de usuario 
  son realizado una sola vez en el servidor y tienen efecto inmediatamente en 
  todos los clientes.

* Se aumenta la capacidad de interacción y comunicación entre los usuarios de la
  misma, así como de su gestión.

* Al ser *HTTP* un protocolo de comunicación ligero y “sin conexión” 
  (*conectionless*) se evita  mantener conexiones abiertas con todos y cada uno 
  de sus clientes, mejorando la eficiencia de los servidores.

* Para utilizar la aplicación, los usuarios tan solo necesitan tener instalado 
  un software denominado navegador *web* (*browser*). Esto reduce drásticamente 
  los problemas de portabilidad y distribución. También permite que terminales 
  ligeras, con poca capacidad de proceso, puedan utilizar “grandes” aplicaciones 
  ya que su función se limita a mostrar mediante el navegador los datos que le han 
  sido enviado. 

* El desarrollo de dispositivos móviles con conectividad a redes expande el 
  dominio de uso de las aplicaciones *web* y abre nuevos mercados.

* Se puede acceder a la aplicación desde cualquier punto con acceso a la red 
  donde preste servicio la aplicación. Si se trata de Internet, desde cualquier 
  parte del mundo, si se trata de una intranet desde cualquier parte del mundo con 
  acceso a la misma. Todo ello sin necesidad de instalar nada más que el navegador 
  en la computadora cliente (punto anterior).

* Los lenguajes utilizados para construir las aplicaciones *web* son relativamente
  fáciles de aprender. Además algunos de los más utilizados, como *PHP* y *Python*,
  se distribuyen con licencias libres y existe una gran cantidad de documentación 
  de calidad disponible en la propia red *Internet*.

* Recientemente  han aparecido en escena varias plataformas y *frameworks* de 
  desarrollo (por ejemplo *symfony*) que facilitan la construcción de las 
  aplicaciones *web*, reduciendo el tiempo de desarrollo y mejorando la calidad.

Obviamente no todo son ventajas; incluso algunas de las ventajas que hemos 
señalado pueden convertirse en desventajas desde otros puntos de vista. Por 
ejemplo:

* el hecho de que los cambios realizados en una aplicación *web* sean efectivos
  inmediatamente en todos los clientes que la usan, puede dejar sin servicio a un
  gran número de usuarios si este cambio provoca un fallo (intencionado si se 
  trata de un ataque, o no intencionado si se trata de una modificación que no ha
  sido debidamente probada). Esto repercute en la necesidad de aumentar las 
  precauciones y la seguridad por parte de los responsables técnicos que mantienen
  la aplicación.

* La disponibilidad de la aplicación es completamente dependiente de la 
  disponibilidad de la red. Así la aplicación *web* será útil en entornos donde 
  se garantice la estabilidad de la red.

* Los programadores necesitan dominar las distintas tecnologías y conceptos que,
  en estrecha colaboración, conforman la aplicación (*HTTP, HTML, XML, CSS, 
  javascript*, lenguajes de *scripting* del lado del servidor como *PHP, Java* o
  *Python*, …)

* La triste realidad de las incompatibilidades entre navegadores.

No obstante, la realidad demuestra que el interés por las aplicaciones *web* es
un hecho consumado, lo cual seduce a los programadores de todo el mundo a formarse
en las tecnologías y estrategias que permiten desarrollarlas. Este curso tiene 
como objetivo presentar una de las más exitosas: *el desarrollo de aplicaciones 
web mediante el uso del framework symfony*. 


Desarrollo rápido y de calidad de  aplicaciones web
---------------------------------------------------------

La experiencia adquirida tras muchos años de construcción de aplicaciones 
informáticas de escritorio, dio lugar a la aparición de entornos y *frameworks* 
de desarrollo que no solo hacían posible construir rápidamente las aplicaciones,
si no que además cuidaban la calidad de las mismas. Es lo que técnicamente se 
conoce como *Desarrollo Rápido de Aplicaciones*. 

Sin embargo, el desarrollo de aplicaciones *web* es muy reciente, por lo que 
estas herramientas de desarrollo rápido y de calidad no han aparecido en el mundo
de la *web* hasta hace bien poco. De hecho la construcción de una aplicación *web*
de calidad no ha estado exenta de dificultades debido a esta carencia. 
Afortunadamente contamos desde hace unos pocos años con *frameworks* de desarrollo
de aplicaciones *web* que facilitan el desarrollo de las mismas y están haciendo 
que el concepto de *Desarrollo Rápido de Aplicaciones* en este campo sea una 
realidad. *Symfony* representa una de las herramientas de más éxito para la 
construcción de aplicaciones *web* de calidad con *PHP*.

Desarrollar con *symfony* hace más sencillo la construcción de aplicaciones *web*
que satisfagan las siguientes características, deseables en cualquier tipo de 
aplicación informática profesional al margen de sus requisitos específicos.

* **Fiabilidad**. La aplicación debe responder de forma que sus resultados sean
  correctos y podamos **fiarnos** de ellos. También implica que los datos que 
  introducimos como entrada sean debidamente validados para asegurar un 
  comportamiento correcto. 

* **Seguridad**. La aplicación debe garantizar la confidencialidad y el acceso 
  a la misma a usuarios debidamente autentificados y autorizados. En el caso de 
  las aplicaciones *web* esto es especialmente importante puesto que residen en 
  computadores que, al pertenecer a una red, son accesibles a una gran cantidad 
  de personas. Lo que significa que inevitablemente están expuestas a ser atacadas
  con fines maliciosos. Por ello deben incorporar mecanismos de protección ante 
  conocidas técnicas de ataque *web* como puede ser el *Cross Site Scripting (XSS)*.

* **Disponibilidad**. La aplicación debe prestar servicio cuando se le solicite.
  Es importante, por tanto, que los cambios requeridos por operaciones 
  relacionadas con el mantenimiento (actualizaciones, migraciones de datos, 
  migraciones de la aplicación a otros servidores, etcétera) sean sencillos de 
  controlar. De esa manera se evitarán largas temporadas de inactividad. La
  disponibilidad es una de las características más valoradas en las aplicaciones 
  *web*, ya que el funcionamiento de la misma no depende, por lo general, de sus 
  usuarios si no de los responsables técnicos del sistema donde se encuentre 
  alojada. Hay que pensar en ellos y ponérselo fácil cuando necesiten realizar 
  este tipo de tarea. También es importante que los errores de funcionamiento
  debidos a errores de programación (*bugs*) sean rápidamente diagnosticados y 
  resueltos para mejorar tanto la disponibilidad como la fiabilidad de la 
  aplicación.

* **Mantenibilidad**. A medida que se usa una aplicación, aparecen nuevos 
  requisitos y funcionalidades que se desean ofrecer. Un sistema mantenible 
  permite ser extendido sin que ello suponga un coste muy alto, minimizando la
  probabilidad de introducir errores en los aspectos que ya estaban funcionando 
  antes de emprender la implementación de nuevas características.

* **Escalabilidad**, es decir, que la aplicación pueda ampliarse sin perder 
  calidad en los servicios ofrecidos, lo cual se consigue diseñándola de manera 
  que sea flexible y modular.


Presentación del curso
----------------------

A quién va dirigido
^^^^^^^^^^^^^^^^^^^

Este curso va dirigido a personas que ya cuenten con cierta experiencia en la
programación de aplicaciones *web*. A pesar de que *symfony* está construido 
sobre *PHP*, no es tan importante conocer dicho lenguaje como estar familiarizado
con las tecnologías de la *web* y con el paradigma de la programación orientada
a objetos. En la confección del curso hemos supuesto que el estudiante comprende
los fundamentos de las tecnologías que componen las aplicaciones *web* y las 
relaciones que existen entre ellas:

* El protocolo *HTTP* y los servidores *web*, 

* Los lenguajes de marcado *HTML* y *XML*,

* Las hojas de estilo *CSS's*

* *Javascript* como lenguaje de *script* del lado del cliente

* Los lenguajes de *script* del lado del servidor (*PHP* fundamentalmente)

* Los fundamentos de la programación orientada a objetos (mejor con *PHP*)

* Los fundamentos de las bases de datos relacionales y los sistemas gestores de
  base de datos.

Obviamente, para seguir el curso, no hay que ser un experto en cada uno de estas
tecnologías, pero sí es importante conocerlas hasta el punto de saber cual es el
papel que desempeña cada una y como se relacionan entre sí. Cualquier persona que
haya desarrollado alguna aplicación *web* mediante el archiconocido entorno 
*LAMP* o *WAMP* (*Linux/Windows – Apache – MySQL – PHP*), debería tener los 
conocimientos necesarios para seguir con provecho este curso.

Objetivos del curso
^^^^^^^^^^^^^^^^^^^

Cuando finalices el curso habrás adquirido suficiente conocimiento para 
desarrollar aplicaciones *web* mediante el empleo del *framework* de desarrollo 
en *PHP symfony*. Ello significa a grandes rasgos que serás capaz de construir 
aplicaciones *web* que:

* Son altamente modulares, extensibles y escalables.

* Separan claramente la lógica de negocio de la presentación, permitiendo que 
  el trabajo de programación y de diseño puedan realizarse independientemente.

* Incorporaran un sistema sencillo, flexible y robusto garantizar la seguridad 
  a los niveles de autentificación y autorización.

* Acceden a las bases de datos a través de una capa de abstracción que permite
  cambiar de sistema gestor de base de datos sin más que cambiar un parámetro de
  configuración. No es necesario tocar ni una sola línea de código para ello.

* Cuentan con un flexible sistema de configuración mediante el que se puede 
  cambiar gran parte del comportamiento de la aplicación sin tocar nada de código.
  Esto permite, entre otras cosas, que se puedan ejecutar en distintos entornos: 
  de producción, de desarrollo y de pruebas, según la fase en la que se encuentre
  la aplicación.

* Pueden ofrecer el resultado final en varios formatos distintos (*HTML, XML,
  JSON, RSS, txt, …*) gracias al potente sistema de generación de vistas,

* Cuentan con un potente sistema de gestión de errores y excepciones, 
  especialmente útil en el entorno de desarrollo.

* Implementan un sistema de caché que disminuye los tiempos de ejecución de los
  *scripts*.

* Incorpora por defecto mecanismos de seguridad contra ataques *XSS* y *CSRF*.

* Pueden ser internacionalizadas con facilidad, aunque la aplicación no se haya
  desarrollado con la internacionalización como requisito.

* Incorporan un sistema de enrutamiento que proporciona *URL's* limpias, 
  compuestas exclusivamente por rutas que ocultan detalles sobre la estructura de
  la aplicación.

El curso cubre una porción suficientemente completa sobre las múltiples 
posibilidades que ofrece *symfony* para desarrollar aplicaciones *web*, 
incidiendo en sus características y herramientas más fundamentales. El objetivo
es que, al final del curso, te sientas cómodo usando *symfony* y te resulte 
sencillo y estimulante continuar profundizando en el *framework* a medida que tu
trabajo lo sugiera. 

Cuando emprendas el estudio de este curso, debes tener en cuenta que el 
aprendizaje de cualquier *framework* de desarrollo de aplicaciones, y *symfony* 
no es una excepción, es bastante duro en los inicios. Sin embargo merece la pena,
pues a medida que se van asimilando los conceptos y procedimientos propios del 
*framework*, la productividad crece de una manera abismal.

Plan del curso
^^^^^^^^^^^^^^

Para conseguir los objetivos que nos hemos propuesto hemos optado por un 
planteamiento completamente práctico en el que se está “picando código” funcional
desde el principio del curso.

En la unidad 2, sin utilizar *symfony* para nada, desarrollamos una sencilla 
aplicación *web* en *PHP*. El objetivo de esta unidad es mostrar como se puede 
organizar el código para que siga los planteamientos del patrón de diseño Modelo
– Vista – Controlador (*MVC*), gracias al cual separamos completamente la lógica
de negocio de la presentación de la información. Es importante comprender los 
fundamentos de esta organización ya que *symfony*, como se verá más adelante, 
la utiliza en su implementación. Además en esta unidad se introducen los conceptos
de controlador frontal, acción, plantilla y *layout*, ampliamente usados en el 
resto del curso.

En la unidad 3 hacemos una presentación panorámica de *symfony*, exponiendo los 
conceptos fundamentales. En esta unidad volveremos a escribir, esta vez utilizando
*symfony*, la aplicación de la unidad 2. Dicho ejercicio nos ayudará a realizar 
la presentación del *framework* a la vez que servirá como referencia de los 
conceptos de base. Avisamos: esta unidad es bastante densa.

En la unidad 4 planteamos el análisis de una aplicación, que aún siendo concebida
con criterios pedagógicos, es suficientemente amplia como para ser considerada
una aplicación profesional. Se trata de un gestor documental y su desarrollo nos
servirá como vehículo para penetrar al interior de *symfony* durante el resto del
curso.

En las siguientes unidades se construyen progresivamente las distintas 
funcionalidades de la aplicación analizada en la unidad 4. Cada unidad incide 
sobre algún aspecto fundamental de *symfony*.

En la unidad 5 profundizamos en el concepto de *modelo* y de capa de abstracción 
de acceso a datos (*ORM*). Aquí se tratan con bastante profundidad los aspectos
relacionados con el acceso a bases datos.

En la unidad 6 se describe el mecanismo para el manejo de la sesión de usuario 
en el servidor que proporciona *symfony*, mediante el cual se trata la seguridad
a los niveles de autentificación y autorización. En esta unidad se construye un
procedimiento para que los usuarios inicien sesión en la aplicación que puede 
ser reutilizado en cualquier otra aplicación *web* construida con *symfony*.

En la unidad 7 se profundiza en la arquitectura *MVC* de *symfony*. Esta unidad
ofrece un buen número de detalles, tanto del controlador como de la vista, 
mediante los cuales se enriquecen las aplicaciones *web* desarrolladas con
*symfony*. 

En la unidad 8 se presenta el fabuloso *framework* de formularios de *symfony*. 
Este sistema, por sí solo, constituye una poderosa herramienta para la definición
de formularios y la validación de los datos que son enviados en las peticiones 
*HTTP* al servidor *web*. También se estudian los formularios que son generados
automáticamente por *symfony* para la gestión de cada una de las tablas de la 
base de datos.

En la unidad 9 se construye completamente y de manera automática la parte de 
administración de la aplicación analizada en el tema 4. Además se construye un
*plugin* con el fin de reutilizar código.

Por último, la unidad 10 presenta los conceptos de internacionalización y 
enrutamiento y la manera en que *symfony* los trata.


Sobre symfony
-------------

*Symfony* es uno de los *frameworks* para el desarrollo de aplicaciones *web*
en *PHP* más conocidos y utilizados. Prueba de ello es la amplia y activa 
comunidad que lo desarrolla y que lo utiliza. El número de aplicaciones 
registradas en el *trac*5 del proyecto también da una idea de la confianza que
muchos desarrolladores depositan en *symfony*. Al margen de estos parámetros
basados en la amplitud de una comunidad, la fiabilidad del *framework* está 
ampliamente asegurada gracias a los más de 8500 test unitarios y funcionales que 
componen la batería de test del producto y que constituyen una garantía de 
calidad.

Desde la primera versión de *symfony* hace 5 años se han lanzado 5 versiones 
estables, siendo la última de ellas la versión 1.4, sobre la cual hemos 
desarrollado este curso. No obstante gran parte de los conceptos que se tratan 
en el curso son independientes de la versión. Incluso muchos de ellos son comunes
a otros *frameworks* de desarrollo. Esto es así por que los creadores de 
*symfony*6 han partido del famoso principio consistente en “no reinventar la 
rueda” y han utilizado muchas ideas y patrones que han tenido éxito en el mundo 
del desarrollo de *software* en general, más allá del *PHP*. De hecho muchas 
ideas provienen del exitoso *framework* de desarrollo de aplicaciones *web Ruby
On Rails*.

La documentación de symfony
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Posiblemente una de las características más apreciadas de *symfony* es la 
cantidad y la calidad de documentación oficial que existe. Ello proporciona la 
tranquilidad de saber que prácticamente cualquier problema que se le presente 
al programador estará resuelto, o al menos estará resuelto algo parecido, en 
alguno de los muchos documentos que sobre *symfony* se han escrito. A medida que
avanzas en el curso comprobarás numerosas referencias a documentos donde se trata
con más detalle el concepto que en ese momento se esté trabajando. 

Teniendo en cuenta este hecho, hemos desarrollado el texto de este curso desde
una perspectiva más pedagógica que técnica, ya que es en aquel aspecto donde la
documentación oficial de *symfony* es más endeble. Este curso supone un apoyo 
pedagógico para aprender a desarrollar aplicaciones *web* con *symfony*, y no 
pretende ni sustituir ni desdeñar la documentación oficial. Muy al contrario 
creemos que dicha documentación es muy valiosa y debe formar parte del equipo 
de recursos necesarios para desarrollar con *symfony*. Por ello realizaremos a 
continuación una relación de dicha documentación:

+-----------------------------------+-------------------------------------------+
| Documento                         |   Descripción                             |
+===================================+===========================================+
| *A Gentle Introduction to symfony*|En este manual se tratan todos los aspectos|
|                                   |fundamentales y no tan fundamentales sobre |
|                                   |*symfony*. A nuestro modo de ver es más que|
|                                   |una introducción.                          |
+-----------------------------------+-------------------------------------------+
|*Practical symfony*                |Son 24 tutoriales de, supuestamente, 1 hora|
|                                   |cada uno. En ellos se desarrolla desde el  |
|                                   |principio hasta el final una aplicación    |
|                                   |completa consistente en un sitio para el   |
|                                   |registro y difusión de ofertas de trabajo. |
|                                   |Trata prácticamente todos los aspectos de  |
|                                   |*symfony*.                                 |
+-----------------------------------+-------------------------------------------+
|*The symfony Reference Book*       |Es el manual de referencia de *symfony*.   |
|                                   |Muy apropiado cuando uno ya se siente      |
|                                   |cómodo con el *framework*.                 |
+-----------------------------------+-------------------------------------------+
|*More with symfony*                |Es un libro solo apto para para “symfony   |
|                                   |masters”. En él se explican recovecos de   |
|                                   |*symfony* muy interesantes.                |
+-----------------------------------+-------------------------------------------+
|*La API*                           |Como es de esperar, representa la          |
|                                   |documentación más árida pero más útil en   |
|                                   |ocasiones en las que hay que explorar qué  |
|                                   |posibilidades ofrece un objeto, o como     |
|                                   |deben ser los argumentos de una función.   |
+-----------------------------------+-------------------------------------------+

Todos estos documentos se encuentran en el sitio web oficial de *symfony*:

http://www.symfony-project.org/doc/1_4/

Además existen traducciones de muchos de ellos en el mismo sitio de *symfony*. 
También son interesantes las traducciones sobre *symfony* realizadas en 
*librosweb*: 

http://www.librosweb.es/symfony/index.html


Instalación de symfony
----------------------

La instalación de *symfony* está perfectamente explicada en la siguiente dirección
*web*:

http://www.symfony-project.org/getting-started/1_4/en/03-Symfony-Installation

No obstante hemos creado la siguiente guía de instalación por si tuvieses 
problemas con el inglés.

Instalación en Windows de XAMPP + symfony
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Descargar xampp para Windows (versión autoextraible exe):

.. code-block:: bash
   
   http://www.apachefriends.org/download.php?xampp-win32-1.7.3.zip
   
* Ejecutar el archivo y se instala solito. A las preguntas se les responde con
  la opción que ofrecen por defecto.

* Abrir el control panel e iniciar los servicios apache y mysql. Asegúrate que
  las casillas Svc de estos servicios están desactivadas.

* Prueba el web server. Abre un navegador y solicita la URL ``http://localhost``.
  Debe responderte el servicio recién instalado. Elige el idioma y verás la página
  de inicio de *XAMPP*. Ahora selecciona la herramienta *phpMyAdmin* para 
  comprobar que el servidor de base de datos funciona correctamente.

* Descargar symfony (archivo zip: 
  ``http://www.symfony-project.org/get/symfony-1.4.6.zip``)

* Extraer el contenido en la carpeta ``C:\xampp\php``

* Añadir a la variable de entorno ``Path`` la ruta siguiente: 
  ``C:\xampp\php\symfony-1.4.6\data\bin``. Para ello haz click con el botón derecho
  del ratón en Mi PC y selecciona ``Propiedades → Opciones Avanzadas → Variables de
  entorno``, selecciona la variable ``Path``, la editas y añades la ruta anterior.

* Ya puedes usar el comando *symfony* en una terminal desde cualquier directorio.


Instalación de symfony en Ubuntu
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Instala mediante *synaptic* el servidor *web apache* con *php* y el servidor 
  *mysql* (paquetes *apache2, mysql-server* y *phpmyadmin*)

* Prueba los servicios instalados. Para ello abre un navegador y accede a la 
  URL ``http://localhost`` y ``http://localhos/phpmyadmin``

* Descargar symfony (archivo zip: 
  ``http://www.symfony-project.org/get/symfony-1.4.6.zip)``

* Mover el archivo anterior al directorio */usr/share/php/*:
  ``sudo mv symfony-1.4.6.zip /usr/share/php``

* Descomprimirlo:

* ``sudo unzip symfony-1.4.6.zip``

* Hacer un enlace simbólico en el directorio */usr/bin* a la herramienta de 
  línea de comandos de *symfony*:
  ``cd /usr/bin``
  ``sudo ln -s /usr/share/php/symfony-1.4.6/data/bin/symfony``

* Ya puedes usar el comando ``symfony`` en una terminal desde cualquier 
  directorio.

.. raw:: html

   <div style="background-color: rgb(242, 242, 242); text-align: center; margin: 20px; padding: 10px;">
   <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/"><img alt="Licencia Creative Commons" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a>
   <br />
   <span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Desarrollo de Aplicaciones web con symfony 1.4</span> por <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Juan David Rodríguez García (juandavid.rodriguez@ite.educacion.es)</span>
   <br/>
   se encuentra bajo una Licencia <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Reconocimiento-NoComercial-CompartirIgual 3.0 Unported</a>.
   </style>
   </div>

