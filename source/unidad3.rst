Unidad 3. Desarrollo de una aplicación con Symfony
==================================================

El objetivo fundamental de este curso es que aprendas a desarrollar aplicaciones
*web* de calidad con *symfony*, un potente *framework* de desarrollo en *PHP*. 
Para conocerlo en profundidad utilizaremos una estrategia de acercamiento en
espiral a nuestro objeto de estudio, es decir, lo rodearemos trazando círculos de 
radio cada vez más pequeños, de forma que cada vuelta que demos nos revelará un
conocimiento más profundo de esta magnífica herramienta de desarrollo. 

Esta estrategia nos proporcionará resultados visibles y prácticos desde el 
principio, desde la primera vuelta, aunque ello será a costa de tratar 
superficialmente algunos conceptos que, progresivamente, irán definiéndose 
con más precisión a medida que avanza el curso.

En esta unidad volveremos a desarrollar la aplicación que construimos en la 
unidad 2. Pero ahora utilizaremos *symfony*. Será nuestra primera vuelta, 
la menos precisa pero la más panorámica, que nos proporcionará una primera imagen
del *framework* aún difusa pero suficiente para mostrar sus características 
fundamentales.

.. note::

   **Recursos de apoyo y ampliación**
   ``http://www.librosweb.es/symfony_1_2/capitulo3/crear_una_aplicacion_web.html``
   ``http://www.librosweb.es/symfony_1_2/capitulo4.html``


Creamos el proyecto.
--------------------

El desarrollo de cualquier aplicación *web* desarrollada con *symfony* comienza 
por la generación del **proyecto** que es la columna vertebral que articula y da
cohesión a todos los elementos que conforman la aplicación: las librerías del 
modelo, el código *PHP* de los módulos, los archivos de configuración, las *CSS's*,
las librerías *Javascript*, las imágenes y otros recursos que pueda requerir la
aplicación, incluido la documentación.

Al igual que hicimos en el ejercicio de la unidad 2, crearemos un directorio en
un lugar accesible por el servidor *web*, donde desplegaremos los ficheros del
proyecto:

.. code-block:: bash

   $ mkdir /opt/lamp/htdocs/unidad3


*Symfony* proporciona una herramienta de desarrollo que nos asiste a lo largo 
del desarrollo de nuestra aplicación mediante las denominadas **tareas**. Dicha
herramienta está desarrollada en *PHP* y se utiliza a través de la linea de 
comandos (*PHP-CLI*). Su nombre, como es de esperar, es **symfony**. Podemos ver 
un listado de todas las tareas que ofrece esta herramienta, así como su modo de 
uso invocándola sin ningún argumento:

.. code-block:: bash

   $ symfony


El primer uso que haremos de la misma será la generación del proyecto, para lo
que nos ubicamos dentro del directorio que acabamos de crear y ejecutamos la 
siguiente instrucción:

.. code-block:: bash

   $ symfony generate:project unidad3 --orm=Propel2


la cual genera un conjunto de ficheros organizados funcionalmente en un árbol 
de directorios que denominaremos: **el proyecto**. A medida que desarrollemos 
la aplicación, ubicaremos los archivos en el directorio que le corresponda según
su función, de la misma manera que ya hicimos en el mini-*framework* de la
unidad 2.

Aunque el número de directorios ha crecido sustancialmente y pueda resultar
abrumador en un principio, verás como al poco de trabajar con *symfony* te
manejarás con bastante soltura por la estructura recién creada, y comprobarás 
que las posibilidades de *symfony* superan extraordinariamente las de 
mini-*framework* de la unidad anterior.

Desde este momento hasta el final del curso, los conceptos *controlador frontal*,
*acción, plantilla* y *layout* introducidos en la unidad 2 y derivados del 
patrón *Modelo – Vista – Controlador*, son utilizados intensivamente. Por ello 
es importante que los tengas bien asimilados antes de continuar.

Vamos a describir la función de cada directorio del proyecto:

+-------------------+----------------------------------------------------------+
|**Directorio**     |**Función**                                               |
+-------------------+----------------------------------------------------------+
|*apps*             |Bajo este directorio se alojarán los ficheros con las     |
|                   |acciones y las plantillas  agrupados en **módulos** que a |
|                   |su vez se agrupan en **aplicaciones**. La mayor parte de  |
|                   |los ficheros desarrollados manualmente por el programador,|
|                   |es decir el código específico de la aplicación, se alojan |
|                   |aquí. Por lo pronto está vacío pues aún no hemos comenzado|
|                   |a programar nada.                                         |
+-------------------+----------------------------------------------------------+
|*lib*              |Aquí se guardarán todas las clases, generadas             |
|                   |automáticamente por *symfony*, mediante las cuales        |
|                   |podremos gestionar las tablas de la base de datos con     |
|                   |independencia del sistema gestor de base de datos que     |
|                   |utilicemos. Estas clases constituyen un *ORM (Object      |
|                   |Relational Mapping)*, es decir una capa de abstracción    |
|                   |entre la base de datos y la aplicación que permite al     | 
|                   |programador utilizar siempre los mismos métodos (funciones|
|                   |de objetos) para manipular los datos sin que éste se tenga|
|                   |que preocupar por la sintaxis propia de la base de datos  |
|                   |que utilice. *Symfony* incorpora dos *ORM's* bien         |
|                   |conocidos: *Propel* y *Doctrine*, en este curso           |
|                   |utilizaremos el primero de ellos. En la terminología de   |
|                   |*symfony* al conjunto formado por todas estas clases se   |
|                   |les denomina **el modelo**.                               |
|                   |Además *symfony* proporciona un *subframework*            |
|                   |independiente para la gestión de formularios *HTML* que   |
|                   |puede ser utilizado en otros proyectos que no sean        |
|                   |*symfony*. Si decidimos utilizarlo en nuestro proyecto, lo|
|                   |cual recomendamos encarecidamente, también se alojarán en |
|                   |este directorio un conjunto de clases que modelan         |
|                   |**formularios** *HTML* para la manipulación de las tablas |
|                   |de la base de datos. Estas clases son generadas           |
|                   |automáticamente a partir de la estructura de la base de   |
|                   |datos.                                                    |
|                   |Por último también se alojarán los **filtros**, que son un|
|                   |tipo especial de formulario que se utiliza para realizar  |
|                   |búsquedas acotadas sobre los datos de las tablas de la    |
|                   |base de datos.                                            |
+-------------------+----------------------------------------------------------+
|*config*           |Aquí se almacenan los ficheros de configuración del       |
|                   |proyecto:                                                 |
|                   |*ProjectConfiguration.class.php*, en él se realiza la     |
|                   |inclusión del núcleo de *symfony* mediante la clase       |
|                   |*sfCoreAutoload* que se encarga de realizar la inclusión  |
|                   |automática de las clases requeridas en nuestras acciones. |
|                   |*databases.yml*, en este archivo se establecen los        |
|                   |parámetros de conexión para cada base de datos que utilice|
|                   |el proyecto: *host*, nombre de usuario, clave y nombre de |
|                   |la base de datos.                                         |
|                   |*schema.yml*, es un archivo en el que se define la        |
|                   |estructura de la base de datos que vamos a utilizar. Por  |
|                   |cada base de datos que requiera el proyecto tendremos un  |
|                   |esquema que se denominará: *basedatos1.schema.yml*,       |
|                   |*basedatos2.schema.yml*, etcétera. Lo más normal es       |
|                   |utilizar sólo una base de datos, se utilizará entonces tan|
|                   |solo un archivo denominado *schema.yml*. Este (estos)     |
|                   |archivo(s) es (son) el punto de partida para la generación|
|                   |tanto de las clases del modelo como de los formularios y  |
|                   |los filtros. Son archivos que sólo se necesitan durante la|
|                   |fase de construcción del software.                        |
|                   |*propel.ini*, es el archivo de configuración de *Propel*, |
|                   |y al igual que el *schema.yml*, únicamente se necesita    |
|                   |durante la fase de construcción de la aplicación.         |
+-------------------+----------------------------------------------------------+
|*web*              |En este directorio residen los recursos que el servidor   |
|                   |*web* puede suministrar como respuestas a las peticiones  |
|                   |*HTTP*. En un entorno de producción debería coincidir con |
|                   |el directorio raíz del servidor *web*. Es importante, por |
|                   |cuestiones de seguridad, que una vez desplegada la        |
|                   |aplicación en el servidor de producción, nos aseguremos   |
|                   |que el servidor *web* únicamente tiene acceso a este      |
|                   |directorio, es decir, que es imposible obtener un archivo |
|                   |del resto de los directorio a través de una *URL*. Para   |
|                   |conseguir esto hay que configurar adecuadamente el        |
|                   |servidor *web*. En esta *URL* del proyecto *symfony* se   |
|                   |explica con detalle como hacerlo:                         |
|                   |``http://www.symfony-project.org/getting-started/1_4/en/05|
|                   |-Web-Server-Configuration``                               |
|                   |No obstante, en los entornos de desarrollo este hecho no  |
|                   |es tan importante, por ello en el desarrollo de nuestro   |
|                   |ejercicio no lo tendremos en cuenta y desplegaremos todos |
|                   |los ejercicios bajo el directorio raíz del servidor *web* |
|                   |que tengamos instalado.                                   |
|                   |Dentro del directorio *web* encontramos:                  |
|                   |Por cada aplicación (en el sentido de agrupación de       |
|                   |módulos que le da *symfony*), tenemos dos archivos        |
|                   |denominados **controladores frontales**, cuya finalidad es|
|                   |la misma que la del controlador frontal del mini-         |
|                   |*framework* de la unidad 2, aunque su lógica es mucho más |
|                   |compleja. Si, por ejemplo tenemos dos aplicaciones        |
|                   |denominadas *aplicacion1* y *aplicacion2*                 |
|                   |respectivamente, a la primera le corresponderán los       |
|                   |controladores frontales                                   |
|                   |* *index.php* y                                           |
|                   |* *aplicacion1_dev.php*                                   |
|                   |Mientras que a la segunda:                                |
|                   |* *aplicacion2.php* y                                     |
|                   |* *aplicacion2_dev.php*                                   |
|                   |Estos archivos son el único punto de entrada a cada       |
|                   |aplicación y ejecutarán la acción del módulo que se le    |
|                   |indique mediante parámetros *GET* en la *URL*. El archivo |
|                   |que no lleva el sufijo ``_dev``, es el controlador de     |
|                   |producción mientras que el que lo lleva es el de          |
|                   |desarrollo. Obviamente, sólo podemos utilizar uno de los  |
|                   |controladores frontales. La diferencia más importante     |
|                   |entre ambos es que el controlador de desarrollo incorpora |
|                   |un sistema de depuración (*debug*) que ofrece al          |
|                   |programador muchísima información sobre la ejecución de   |
|                   |las acciones (variables de sesión, *request*, respuesta,  |
|                   |tiempo de ejecución, *queries SQL* lanzadas, y muchas más |
|                   |cosas). Es por ello ** muy importante que en el servidor  |
|                   |de producción no se pueda acceder a la aplicación a través|
|                   |del controlador frontal de desarrollo.**                  |
|                   |También tenemos un directorio para el almacenamiento de   |
|                   |las *css's* denominado *css*.                             |
|                   |Otro denominado *js* para el almacenamiento de las        |
|                   |librerías de funciones *javascript*.                      |
|                   |Otro denominado *images* para el almacenamiento de las    |
|                   |imágenes.                                                 |
|                   |Otro denominado *uploads* para el almacenamiento de       |
|                   |los ficheros subidos al servidor desde los clientes.      |
+-------------------+----------------------------------------------------------+
|*plugins*          |Las aplicaciones construidas con *symfony* pueden ser     |
|                   |reorganizadas en plugins que tienen la particularidad de  |
|                   |distribuirse mediante canales de *PEAR* y que pueden ser  |
|                   |incorporados fácilmente a nuestras aplicaciones *symfony* |
|                   |ampliando sus funcionalidades.                            |
|                   |Es en este directorio donde se alojarán los *plugins*, si |
|                   |decidimos utilizar alguno. Existe un sitio *web* del      |
|                   |proyecto *symfony* dedicado a los *plugins*. Allí podemos |
|                   |encontrar varios cientos de ellos. Merece la pena destacar|
|                   |el *plugin* de gestión de usuarios *sfGuardPlugin*,       |
|                   |mediante el que podemos incorporar en poco tiempo un      |
|                   |sistema de gestión de usuarios y grupos con               |
|                   |autentificación y autorización.                           |
+-------------------+----------------------------------------------------------+
|*doc*              |Para almacenar la documentación                           |
+-------------------+----------------------------------------------------------+
|*data*             |Para almacenar datos externos a la propia aplicación como |
|                   |pueden ser *scripts* para poblar la base de datos         |
+-------------------+----------------------------------------------------------+
|*test*             |Aquí se generarán de forma semiautomática tests para      |
|                   |realizar pruebas unitarias.                               |
+-------------------+----------------------------------------------------------+
|*cache*            |Es el directorio utilizado por el sistema de caché de     |
|                   |*symfony* para optimizar el tiempo de ejecución.          |
+-------------------+----------------------------------------------------------+
|*log*              |Aquí se alojan los archivos de registro para el           |
|                   |diagnóstico y control de las aplicaciones.                |
+-------------------+----------------------------------------------------------+

Como podemos observar, es “algo” más complejo que el mini-*framework* que
desarrollamos en la unidad 2. Pero el esfuerzo requerido para asimilarlo 
merecerá la pena. Tranquilos, aún no hemos dado la primera vuelta de la espiral.


Generación del esquema, el modelo y los formularios
---------------------------------------------------

En este segundo paso generaremos el resto de la estructura básica que dará 
soporte a la aplicación que vamos a construir: las clases con las que accederemos 
a la base de datos y los formularios que el programador podrá utilizar para 
gestionar las tablas de dicha base de datos. Por ello, para continuar necesitamos
confeccionar previamente una base de datos. El ejercicio que se desarrolla en esta
unidad, como ya hemos advertido al principio, es el mismo que el de la unidad 
anterior, por tanto utilizaremos la misma base de datos: *alimentos*.

En primer lugar definimos los parámetros de conexión a la base de datos para que
la aplicación tenga acceso a la misma. Esto se hace a través del fichero 
*config/databases.yml*:

.. code-block:: yaml

	dev:
	  propel:
		param:
		  classname:  DebugPDO
		  debug:
			realmemoryusage: true
			details:
			  time:       { enabled: true }
			  slow:       { enabled: true, threshold: 0.1 }
			  mem:        { enabled: true }
			  mempeak:    { enabled: true }
			  memdelta:   { enabled: true }
	
	test:
	  propel:
		param:
		  classname:  DebugPDO
	
	all:
	  propel:
		class:        sfPropelDatabase
		param:
		  classname:  PropelPDO
		  dsn:        mysql:dbname=alimentos;host=localhost
		  username:   root
		  password:   root
		  encoding:   utf8
		  persistent: true
		  pooling:    true


.. note::

   Antes de continuar abrimos un pequeño paréntesis para adelantar que la mayor 
   parte de los ficheros de configuración de *symfony* utilizan el formato *YAML*
   para definir los parámetros de configuración. Según dicen en el sitio oficial 
   del proyecto, *“YAML is a human friendly data serialization standard for all 
   programming languages”*, es decir un standard para la serialización de datos 
   fácil de leer por humanos y máquinas. Algo así como el *XML* pero más fácil 
   de interpretar por las personas. Los archivos *YAML* utilizan la identación 
   de los elementos como base para definir  estructuras jerárquicas (árboles). 
   Esto es algo que puedes comprobar directamente echando un vistazo al fichero
   anterior.

   Los ficheros de configuración de *symfony* suelen clasificar los parámetros de
   configuración en varias secciones, cada una de ellas corresponde a lo que se 
   denomina un entorno de ejecución. Son 3 los entornos de ejecución que el 
   *framework* ofrece por defecto, aunque podemos añadir otros nuevos. Se conocen
   como desarrollo (cuya clave es *dev*), producción (cuya clave es *prod*)
   y pruebas (cuya clave es *test*). Esta estrategia permite definir distintos
   valores para el mismo parámetro en cada entorno de ejecución. Si deseamos 
   definir un parámetro de configuración común para todos los entornos utilizamos
   la clave *all*. El archivo anterior muestra este hecho con claridad, aunque
   no especifica parámetros propios para el entorno de producción, con lo que este
   entorno asumiría únicamente los parámetros definidos en las sección de clave 
   *all*. 

   Y ahora te preguntarás, ¿y que cosa es esto de los entornos de ejecución? 
   Podemos decir brevemente que cada entorno de ejecución responde a distintas 
   configuraciones. Por ejemplo, mientras en el entorno de desarrollo tenemos 
   acceso a una gran cantidad de datos para la depuración de la aplicación, en 
   el de producción no. Así mismo podríamos definir una base de datos para el 
   entorno de producción y otra para el de desarrollo. Y la otra pregunta que 
   surge ¿cómo ejecutamos un entrono u otro? La respuesta ya la hemos adelantado 
   cuando explicábamos la existencia de dos controladores frontales por 
   aplicación, uno para desarrollo y otro para producción.En ellos se especifica 
   qué entorno deseamos utilizar en la ejecución del *script*. 

   En el siguiente apartado volveremos a la carga con el tema de los entornos 
   de ejecución. Por lo pronto vamos a cerrar el paréntesis con la intención de 
   no perder demasiado el hilo.


En cada entorno de ejecución se ha definido una conexión denominada *propel*. La
conexión consiste en una serie de parámetros que el *framework* utilizará para 
conectarse a la base de datos. Los más importantes son:

* *dsn*: que define el nombre de la base de datos y el host, lo que se conoce 
  generalmente como *Data Source Name*.

* *username*: el nombre de usuario de la base de datos

* *password*: el *password* de dicho usuario


Si el proyecto va a requerir el acceso a varias bases de datos, es aquí donde 
debemos añadir las conexiones con los parámetros para acceder a las mismas. El
nombre de las conexiones lo decide el programador, aunque por defecto la primera
conexión creada al generarse el proyecto se denomina *propel*, como acabamos
de ver.

A continuación modificamos el fichero de configuración *config/propel.ini*, 
el cual es utilizado por *symfony* para la generación del esquema a partir de 
una base de datos existente. Lo que se denomina una introspección a la base de 
datos. En él indicamos los parámetros de conexión a la base de datos en cuestión:
(lineas 6 a 9 del fichero *config/propel.ini*)

.. code-block:: bash

	...
	propel.database.url        = mysql:dbname=sf_alimentos;host=localhost
	propel.database.creole.url = ${propel.database.url}
	propel.database.user       = root
	propel.database.password   = root
	...

El archivo que acabamos de modificar contiene otros muchas directivas que indican
a *Propel* como debe comportarse. Por lo general podemos dejarlo tal y como está,
únicamente se deben cambiar los parámetros de conexión a la base de datos. Este
fichero es necesario únicamente durante la fase de desarrollo para la generación 
automática del **esquema**. No es necesario para la ejecución de la aplicación 
*web* una vez desarrollada, y se puede obviar una vez que se encuentre en 
producción. 

Comenzamos por generar el esquema:

.. code-block:: bash

   $ symfony propel:build-schema


Si abres y examinas el archivo generado (*config/schema.yml*) comprobarás que su
contenido es una definición de la estructura de la base de datos al estilo de un
*DDL* (*Data Definition Language*). Es decir, en él se definen las tablas, sus 
columnas y sus relaciones. De cara al modelo de datos, este archivo es el más 
fundamental del *framework*; la generación del modelo, los formularios y los 
filtros se basan en él.


.. note::

   Hemos generado automáticamente el archivo *schema.yml* a partir de una
   base de datos existente, es decir, realizando ingeniería inversa o una 
   introspección a la base de datos. Pero también se puede proceder al revés:
   en primer lugar confeccionar manualmente fichero *schema.yml*, y a partir
   de él generar las sentencias *SQL* para construir la base de datos mediante la
   instrucción:
   
   .. code-block:: bash

      ``$ symfony propel:build-sql``

   En este caso se puede prescindir del archivo de configuración *propel.ini*.
   Sin embargo, pensamos que es más cómodo comenzar por una base de datos ya 
   creada, puesto que existen muchas herramientas visuales que asisten y 
   facilitan la creación de la misma y dicha tarea resulta más cómoda que 
   escribir manualmente el esquema.


Ahora generamos el modelo, los formularios y los filtros:

.. code-block:: bash

   $ symfony propel:build-model
   $ symfony propel:build-forms
   $ symfony propel:build-filters

Bajo el directorio *lib* del proyecto encontramos los nuevos directorios *model*,
*form* y *filter*. En ellos se encuentran ficheros cuyos nombres hacen referencia
a las tablas de la base de datos, y que contienen clases que utilizaremos en el 
desarrollo de la aplicación para manipular los datos de dichas tablas.


Las aplicaciones y los módulos
------------------------------

El *framework* puede ser imaginado como un inmenso puzzle al que le faltan sus 
piezas centrales para completarlo y poder contemplarlo en su totalidad. 
Dependiendo del contenido de las piezas que faltan, el resultado final tendrá un
aspecto u otro. Podemos pensar que el puzzle nos ofrece un paisaje natural con el
cielo azul, unas montañas al fondo, un fértil valle con su río y sus árboles 
frutales. El hueco central se puede rellenar con cualquier escena que imaginemos:
animales pastando, amigos pasando una tarde de campo, o cualquier otra cosa. Lo
que acabamos de describir sería un *framework* para la construcción de paisajes
bucólicos, en analogía con *symfony* que es un *framework* para la construcción 
de aplicaciones *web*. La tarea del programador es, precisamente, colocar esas 
piezas que faltan, las cuales representan las características propias de la 
aplicación que se desea construir. El resto de elementos comunes que tienen todas
las aplicaciones (la gestión de las sesión, la gestión de las peticiones *HTTP*,
la construcción de la respuesta *HTTP*, la validación de los formularios, la 
gestión de la seguridad, y otras muchas más) lo ofrece el *framework*, igual que
ese puzzle incompleto  proporciona el marco que alojará la obra completa. El
*framework* es como un escenario común donde pueden tener lugar distintas escenas
(las aplicaciones).

La parte que falta y cuya elaboración corresponde al programador, comprende las
acciones y las plantillas que “dibujan” los datos procesados por aquellas.
Recordemos que en el mini-*framework* de la unidad 2, las acciones se 
implementaban en ficheros (una acción por fichero) y se alojaban en un único 
directorio denominado *acciones*. Es obvio que a medida que crezca la aplicación 
el directorio de acciones se saturará de archivos dificultando su mantenibilidad.

Para evitar este hecho, *symfony* utiliza una estrategia de agrupación de las 
acciones en conjuntos denominados **módulos**, los cuales, a su vez se agrupan
en conjuntos denominados **aplicaciones**. Resumiendo: 

* *un proyecto de symfony se “acopla” a una o varias bases de datos, y está 
  compuesto por un número determinado de aplicaciones que, a su vez están 
  compuestas por módulos con acciones.*

Cómo realizar dicha agrupación de acciones en módulos y aplicaciones es una tarea
de diseño que corresponde al programador. Esta es la única decisión de diseño
arquitectónico que debemos realizar, ya que el resto las impone el *framework*. 

Veamos qué forma física adoptan estos módulos y aplicaciones mediante la práctica.
La aplicación que desarrollamos en la unidad anterior y que volvemos a desarrollar
ahora con *symfony*, comprendía 8 acciones que manipulaban los datos de una tabla 
con información dietética sobre alimentos. Se trata de un número de acciones 
bastante manejable para que sean tratadas dentro de un mismo conjunto. Por ello 
las agruparemos dentro de un único módulo. A su vez este módulo debemos crearlo
dentro de una aplicación, ya que *symfony* exige que, al menos, exista una 
aplicación para alojar módulos. 

Creamos la aplicación:

.. code-block:: bash

   $ symfony generate:app dietetica

Con lo cual hemos creado una aplicación denominada *dietetica*, que en términos
de ficheros se ha traducido en la creación de un directorio llamado *dietetica*
que cuelga del directorio *apps* del proyecto. A continuación se describen los 
directorios y archivos generados con la aplicación *dietetica*.

+-------------------+----------------------------------------------------------+
|*config*           |Es un directorio donde se alojan los archivos de          |
|                   |configuración particulares de esta aplicación. Observa que|
|                   |prácticamente todos son ficheros *YAML*.                  |
|                   |                                                          |
|                   |*app.yml*: aquí el programador puede definir los          |
|                   |parámetros de configuración que haya decidido utilizar en |
|                   |el desarrollo de la aplicación.                           |
|                   |                                                          |
|                   |*cache.yml*: donde se definen los parámetros que controlan|
|                   |el comportamiento del sistema de caché delas vistas. Por  |
|                   |defecto está deshabilitado.                               |
|                   |                                                          |
|                   |*factories.yml*: aquí podemos reemplazar los objetos      |
|                   |claves que utiliza el *framework* para llevar a cabo su   |
|                   |trabajo por otros que implementen la misma interfaz y que,|
|                   |obviamente, realicen adecuadamente su cometido, aunque de |
|                   |manera distinta a los originales. Como puedes comprender, |
|                   |tocar este archivo exige un amplio conocimiento del       |
|                   |*framework*, a parte de una importante justificación, ya  |
|                   |que estamos hablando de modificaciones a nivel del núcleo.|
|                   |                                                          |
|                   |*filters.yml*: Más adelante veremos que *symfony*         |
|                   |implementa para su ejecución un patrón de diseño          |
|                   |denominado cadena de responsabilidades, en el cual una    |
|                   |serie de objetos van operando secuencialmente sobre el    |
|                   |resultado del objeto que le precede en la cadena. Este    |
|                   |fichero permite que el programador incorpore nuevos       |
|                   |objetos (filtros) a dicha cadena.                         |
|                   |                                                          |
|                   |*routing.yml*: Que es la base del sistema de enrutamiento |
|                   |de *symfony* gracias al cual las *URL* de los proyectos   |
|                   |*symfony* presentan un aspecto más estilizado evitando el |
|                   |uso de los caracteres “&”, “=” y “?” y haciéndolas más    |
|                   |cortas gracias a la supresión del nombre de algunos       |
|                   |parámetros. La última unidad de este curso trata este tema|
|                   |con más detalle.                                          |
|                   |                                                          |
|                   |*security.yml*: Donde se definen las políticas de         |
|                   |seguridad para el acceso a las acciones. La unidad 6      |
|                   |desarrolla este tema con detalle.                         |
|                   |                                                          |
|                   |*settings.yml*: Aquí se definen los principales parámetros|
|                   |de configuración de la aplicación, tales como la cultura  |
|                   |por defecto, el máximo tiempo de respuesta para una       |
|                   |petición, los módulos habilitados, etcétera. A medida que |
|                   |avances en el curso se te revelarán el significado de los | 
|                   |parámetros más usados de este fichero.                    |
|                   |                                                          |
|                   |*frontendConfiguration.class.php*: El método *configure*  |
|                   |de esta clase se ejecuta cuando la aplicación se carga. Se|
|                   |puede utilizar para realizar operaciones de               |
|                   |inicialización, aunque en la mayoría de aplicaciones se   |
|                   |quedará tal y como está.                                  |
+-------------------+----------------------------------------------------------+
|``i18n``           |Aquí se guardarán los archivos con las traducciones de los|
|                   |textos a otros idiomas si deseamos internacionalizar la   |
|                   |aplicación.                                               |
+-------------------+----------------------------------------------------------+
|``lib``            |Contiene las clases y librerías externas utilizadas       |
|                   |exclusivamente en los módulos de la aplicación. Las clases|
|                   |que aquí se encuentren serán cargadas automáticamente por |
|                   |el *framework* cuando la acción las necesite, liberando al|
|                   |programador de tener que incluirla manualmente mediante   |
|                   |las instrucciones ``include`` o ``require`` de *PHP*. Es  |
|                   |lo que se denomina autocarga.                             |
+-------------------+----------------------------------------------------------+
|``modules``        |En este directorio se alojarán los módulos de la          |
|                   |aplicación, que no son más que directorios cuyo nombre es |
|                   |el del módulo.                                            |
+-------------------+----------------------------------------------------------+
|``templates``      |En este directorio se encuentran los *layouts* con que    |
|                   |serán decoradas las plantillas que “dibujan” los          |
|                   |resultados de las acciones de la aplicación. Recuerda los |
|                   |conceptos de *layout* y plantilla que se estudiaron en la |
|                   |unidad anterior, especialmente recuerda el gráfico que    |
|                   |ilustraba como la combinación de *layout* + plantilla da  |
|                   |lugar al documento final.                                 |
+-------------------+----------------------------------------------------------+

Pero además, bajo el directorio web del proyecto se han generado dos archivos
*PHP*: *index.php* y *dietetica_dev.php*, que son los **controladores
frontales de la aplicación**, es decir los puntos de entrada que deben ser
invocados desde el navegador *web* a través de la *URL* para ejecutar la 
aplicación.


.. note::

   Volvemos de nuevo al tema de los entornos. Veamos el contenido de cada uno de 
   los controladores frontales de la aplicación que acabamos de generar:

   **index.php**:

	.. code-block:: php
	
		<?php
		
		require_once(dirname(__FILE__).'/../config/ProjectConfiguration.class.php');
		
		$configuration = ProjectConfiguration::getApplicationConfiguration('dietetica', 'prod', false);
		
		sfContext::createInstance($configuration)->dispatch();

	**dietetica_dev.php**
	
	.. code-block:: php
	
		<?php
		
		// this check prevents access to debug front controllers that are deployed by accident to production servers.
		
		// feel free to remove this, extend it or make something more sophisticated.
		
		if (!in_array(@$_SERVER['REMOTE_ADDR'], array('127.0.0.1', '::1')))
		
		{
		
		die('You are not allowed to access this file. Check '.basename(__FILE__).' for more information.');
		
		}
		
		require_once(dirname(__FILE__).'/../config/ProjectConfiguration.class.php');
		
		$configuration = ProjectConfiguration::getApplicationConfiguration('dietetica', 'dev', true);
		
		sfContext::createInstance($configuration)->dispatch();

	Las primeras líneas del controlador de desarrollo implementan un control de 
	seguridad que garantiza que dicho archivo se pueda ejecutar únicamente si la 
	petición *HTTP* se hace desde el propio computador que aloja la aplicación, 
	que es la situación típica de desarrollo. De esta manera se evita que, por
	accidente, en un entorno de producción se haya “colado” este controlador y 
	los clientes lo ejecuten obteniendo una cantidad de datos sobre la aplicación,
	lo cual comprometería seriamente su seguridad. Al margen de este detalle el
	resto del código es el mismo, siendo la única diferencia el valor de los 
	parámetros que se pasan a la función 
	``ProjectConfiguration::getApplicationConfiguration.``

	El primer parámetro de la función 
	``ProjectConfiguration::getApplicationConfiguration`` define la aplicación
	que ejecutará el controlador frontal, el segundo define el entorno; 
	producción (*prod*), desarrollo (*dev*) o pruebas (*test*), que se utilizará 
	para la configuración de la misma y con el tercero se decide si se va a 
	utilizar el modo de depuración o no. Es decir, es en el controlador donde se 
	define qué configuración (entorno de ejecución) se utilizará en la ejecución 
	del *script*. De forma que cuando desarrollemos utilizaremos para comprobar 
	el resultado de nuestras líneas de código el controlador de desarrollo,
	mientras que cuando la aplicación este finalizada y lista para ser desplegada
	en el servidor de producción, se utilizará el controlador de producción.

	Esperamos que con esto quede más claro el sistema de configuración de 
	*symfony* basado en los entornos de ejecución y el uso de ficheros *YAML*. 
	No obstante, si quieres estudiar más a fondo este tema puedes consultar la 
	siguiente *URL*:

	.. code-block:: bash

	   http://www.symfony-project.org/book/1_2/05-Configuring-Symfony

	o en castellano:
	
	.. code-block:: bash

		http://www.librosweb.es/symfony_1_2/capitulo5.html


Como acabamos de decir, ambos ficheros controlan el acceso y la ejecución de la
aplicación, de manera que para ejecutar una acción determinada hay que solicitarlo
en la petición *HTTP* indicando mediante parámetros *GET* dicha acción y el módulo
al que pertenece. Obviamente, sólo podemos utilizar uno de ellos, de manera que:

.. code-block:: bash

	http://nonbredelservidor/index.php/nombre_modulo/nombre_accion

devolvería el resultado de la acción ``nombre_accion`` del módulo ``nombre_módulo``,
y:

.. code-block:: bash

   http://nonbredelservidor/dietetica_dev.php/nombre_modulo/nombre_accion

devolvería lo mismo con la diferencia de que en la parte superior derecha de la
página aparecería una barra de depuración mediante la que podemos acceder 
información detallada sobre la ejecución de la aplicación: datos de la petición,
datos de la respuesta, configuración de la vista, consultas *SQL* ejecutadas, 
tiempo de ejecución, configuración del servidor, etcétera. Como puedes suponer,
datos tan valiosos para el desarrollo como peligrosos para ser enviados por error
en un entorno de producción.

Ahora vamos a construir el módulo que albergará las acciones de nuestra 
aplicación.

.. code-block:: bash

   $ symfony generate:module dietetica alimentos


La instrucción anterior genera un módulo de la aplicación *dietetica*
denominado *alimentos*. Lo que físicamente se traduce en la creación del 
directorio *apps/dietetica/modules/alimentos*. Vamos a describir su contenido:


+-------------------+----------------------------------------------------------+
|``actions``        |Como el propio nombre del directorio sugiere, aquí se     |
|                   |definirán las acciones del módulo. Abrimos el directorio y|
|                   |nos encontramos con un fichero denominado                 |
|                   |*actions.class.php.*. En él se implementa una clase que se|
|                   |denomina *alimentosActions* y que extiende a la clase     |
|                   |*sfActions*. *Symfony* sabe como y cuando debe tratar las |
|                   |clases derivadas de *sfActions* cuyo nombre sigue el      |
|                   |patrón *{nombreModulo}Actions*, y que se encuentran en los|
|                   |directorios *actions* de cada módulo.                     |
|                   |                                                          |
|                   |El programador implementará las acciones del módulo como  |
|                   |métodos de dicha clase cuyos nombres deben seguir el      |
|                   |patrón ``execute{NombreAccion}``. Además deben ser        |
|                   |declarados de alcance público. Por ejemplo, una acción que|
|                   |se llame *'inicio'* sería implementada en un método de la |
|                   |clase como sigue:                                         |
|                   |                                                          |
|                   |.. code-block:: bash                                      |
|                   |                                                          |
|                   |   public function executeInicio(sfWebRequest $request)   |
|                   |   {                                                      |
|                   |   ...                                                    |
|                   |   // Aquí el código de la acción                         |
|                   |   ...                                                    |
|                   |   }                                                      |
|                   |                                                          |
|                   |El argumento del método (``$request``), es un objeto que  |
|                   |lleva información acerca de los parámetros de la petición |
|                   |*HTTP* (*GET*, *POST*, *FILES*, y algunas otras cosas     |
|                   |más), para facilitar al programador su acceso y           |
|                   |manipulación de una misma manera en todos los proyectos   |
|                   |desarrollados con *symfony*.                              |
+-------------------+----------------------------------------------------------+
|``templates``      |Una vez que la acción se ha ejecutado, al igual que en el |
|                   |mini-*framework* de la unidad anterior, los datos que ha  |
|                   |procesado deben ser visualizados de alguna manera. En este|
|                   |directorio se implementarán las plantillas encargadas de  |
|                   |realizar dicha tarea. Cada plantilla se corresponde con un|
|                   |archivo *PHP* confeccionado de manera que muestre         |
|                   |explícitamente la estructura *HTML*, exactamente de la    |
|                   |misma forma que explicamos en la unidad anterior.         |
|                   |Por lo general, el nombre de las plantillas seguirá el    |
|                   |siguiente patrón:                                         |
|                   |*{nombreAccion}Success.php*, y, como podrás imaginar,     |
|                   |será una plantilla creada para visualizar los datos de la |
|                   |acción *nombreAccion*. Sin embargo también existen        |
|                   |otras posibilidades que serán explicadas más adelante.    |
+-------------------+----------------------------------------------------------+

Aunque la tarea *generate:module* sólo crea los directorios anteriores, en 
ocasiones también tendremos que agregar a mano los directorios *lib*, *config*
y *doc* a nivel de módulo. 

En el primero podremos colocar clases y funciones externas utilizadas 
exclusivamente por el módulo. Al igual que en los otros directorios *lib*, a
nivel de aplicación y de proyecto, las librería que residan en este directorio 
serán cargadas automáticamente por el *framework* cuando sean necesarias. 

En el segundo podremos incluir archivos de configuración propios del módulos, y 
en el tercero la documentación del mismo.


¡Y ahora a programar!
---------------------

Llegó la hora de trabajar un poco. Hasta el momento ha sido *symfony* quien ha
trabajado por nosotros generando la sólida estructura del edificio que vamos a 
construir. Ahora nos corresponde a nosotros construir sus tabiques y decorar los
ambientes.

Antes de comenzar, y para que todo funcione correctamente, es preciso que 
modifiques el fichero de configuración *apps/dietetica/config/settings.yml* y
pongas el parámetro ``no_script_name`` de la sección ``prod`` (es decir del 
entorno de ejecución de producción) a ``false``. En el caso de estar activado,
este parámetro indica a *symfony* que debe eliminar el nombre del controlador 
frontal (*index.php*) cuando se construyen las *URL's* de la aplicación. Lo 
cual estiliza la *URL* pero requiere que el servidor web esté debidamente 
configurado para un entorno de producción, y este no es nuestro caso.


Acción de inicio (index)
^^^^^^^^^^^^^^^^^^^^^^^^

Comenzaremos por la implementación de la acción *'inicio'*, la más sencilla de 
la aplicación. No obstante, para aprovechar las características de *symfony*, 
en lugar de *'inicio'* le vamos a llamar *'index'*, ya que es así como se 
denomina la acción por defecto en *symfony*.

El siguiente código muestra el código de la clase *alimentosActions* una vez
implementada la acción *'index'*:

Contenido del archivo: apps/dietetica/modules/alimentos/actions/actions.class.php

.. code-block:: php
	
	<?php
	
	/**
	 * alimentos actions.
	 *
	 * @package    alimentos
	 * @subpackage alimentos
	 * @author     Your name here
	 * @version    SVN: $Id: actions.class.php 12479 2008-10-31 10:54:40Z fabien $
	 */
	class alimentosActions extends sfActions
	{
	   /**
		* Executes index action
		*
		* @param sfRequest $request A request object
		*/
		
		public function executeIndex(sfWebRequest $request)
		{
			$this -> param_mensaje = 'Bienvenido a la primera aplicación del curso "desarrollo de aplicaciones web con symfony"';
			$this -> param_fecha = date('d - M - Y');
	
		}

El código es esencialmente el mismo que su homólogo de la aplicación desarrollada
en la unidad 2. La diferencia estriba en que:

	1. la acción se implementa en un método de la clase ``alimentosActions`` en
	lugar de hacerse en un fichero.

	2. los parámetros que deseamos mostrar en la plantilla deben definirse como
	atributos de la clase *alimentosActions* mediante el identificador ``$this``, 
	que es una referencia al objeto que ha sido instanciado. 

Cuando finaliza la ejecución de la acción se ejecuta la plantilla *indexSuccess.php*,
la cual reconoce como parámetros dinámicos variables cuyo nombre coincide con 
los atributos definidos mediante ``$this`` en la acción. Veamos todo esto más 
claramente en el código de la plantilla ``indexSuccess.php``:


``Contenido del archivo: apps/dietetica/modules/alimentos/templates/indexSuccess.php``

.. code-block:: html+jinja

	<h3> Fecha: <?php echo $param_fecha ?> </h3>
	<?php echo $param_mensaje ?>

Expresado de una manera poco formal: los parámetros que deseamos mostrar en la
plantilla y que provienen de la acción, se invocan con el mismo nombre que se 
le dio en la acción pero sin utilizar ``$this``.

Y ahora ya podemos ver el resultado de nuestra primera acción. Abrimos el
navegador *web* y solicitamos el recurso correspondiente a la *URL*:

.. code-block:: bash

	http://localhost/unidad3/web/index.php/alimentos/index

Y veremos el resultado de ejecutar la acción *index* del módulo *alimentos*.
Cómo *index* es la acción por defecto, podemos obtener el mismo resultado 
eliminando el parámetro *index* de la petición: 

.. code-block:: bash

	http://localhost/unidad3/web/index.php/alimentos

.. note::

	Hay que aclarar aquí que *symfony* utiliza un sistema de enrutamiento que
	permite el uso de *URL's* limpias, lo cual significa que el paso de 
	parámetros *GET* en la petición *HTTP* no se realiza mediante los caracteres
	'?' y '&' tras el nombre del fichero *index.php*. En su lugar se utiliza 
	únicamente el carácter '/'. De manera que el primer parámetro tras *index.php,
	alimentos* en nuestro caso, corresponderá al módulo, mientras que el segundo,
	*index* en nuestro caso, a la acción que se desea ejecutar. Si la acción 
	requiriese parámetros adicionales se indicarían los pares 
	*parámetro/valor-parámetro* separados igualmente por el carácter '/'. 

	El sistema de enrutamiento es una herramienta muy potente que interpreta
	(“parsea”) la *URL* de acuerdo a un conjunto de reglas, que pueden ser 
	definidas por el programador, para saber qué acción de qué módulo debe ser 
	ejecutada y con qué parámetros. En un principio utilizaremos las reglas
	preestablecidas de enrutamiento, que consisten en lo que acabamos de 
	explicar. Por lo pronto, para desarrollar aplicaciones *web* con *symfony* 
	no es necesario saber más acerca del sistema de enrutamiento. Pero
	señalaremos que la configuración a medida de dicho sistema mejora tanto el
	aspecto como la seguridad de la aplicación, ya que es posible eliminar 
	información estructural acerca de la propia aplicación, como puede ser el
	nombre de ciertos parámetros.

Si queremos usar la herramienta de depuración debemos utilizar el controlador 
de desarrollo. Utiliza ahora esta *URL* para ejecutar la acción:

``http://localhost/unidad3/web/dietetica_dev.php/alimentos/index``

Observa la presencia de una barra de herramientas en la esquina superior derecha
de la página recibida. Te aconsejamos que eches un rato jugando con ella. Explora
e interpreta la información que ofrece. A medida que desarrolles con *symfony*
encontrarás más valiosa esta herramienta pues te ayudará a resolver muchos 
problemas. 


.. note::

   si quieres ver la barra de depuración con iconos debes copiar el siguiente
   directorio de la distribución de *symfony* al directorio *web* del proyecto:

   ``cp -R /ruta/a/symfony/data/web/sf web/``


Ya tenemos el resultado de la acción inicial de presentación, pero echamos de 
menos el menú y el pie de página que debería enmarcar a todas las acciones de
la aplicación. Lo incorporaremos a continuación definiendo el *layout* de la 
aplicación, lo que implica modificar el archivo 
*apps/dietetica/templates/layout.php.*

Contenido del archivo: apps/dietetica/templates/layout.php

.. code-block:: html+jinja

	<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
	<html>
		<head>
			<?php include_http_metas() ?>
			<?php include_metas() ?>
			<?php include_title() ?>
			<?php include_stylesheets() ?>
			<?php include_javascripts() ?>
			<link rel="shortcut icon" href="/favicon.ico" />
		</head>
		<body>
			<div id="cabecera">
				<h1>Información de alimentos</h1>
			</div>
	
			<div id="menu">
				<hr/>
				<a href="<?php echo url_for('alimentos/index') ?>">inicio</a> |
				<a href="<?php echo url_for('alimentos/insertarAlimento') ?>">insertar alimento</a> |
				<a href="<?php echo url_for('alimentos/buscarAlimentosPorNombre') ?>">buscar por nombre</a> |
				<a href="<?php echo url_for('alimentos/buscarAlimentosPorEnergia') ?>">buscar por energia</a> |
				<a href="<?php echo url_for('alimentos/buscarAlimentosCombinada') ?>">búsqueda combinada</a>
				<hr/>
			</div>
	
			<div id="contenido">
				<?php echo $sf_content ?>
			</div>
	
			<div id="pie">
				<hr/>
				<div align="center">- pie de página -</div>
			</div>
		</body>
	</html>
	
	
Lo resaltado en negrita es la parte que hemos añadido al fichero 
*apps/dietetica/templates/layout.php*  que fue generado automáticamente por 
*symfony*. Observa por otro lado la linea sombreada. Ella es la responsable de
mostrar el resultado de la acción en curso. La variable ``$sf_content`` es propia
de *symfony* y su valor es el contenido de la plantilla una vez realizadas las 
sustituciones de los parámetros dinámicos definidos en la acción. Dicha línea es 
la equivalente a la siguiente línea del *layout* del mini-*framework* de la 
unidad anterior:

.. code-block:: php

	**<?php include('plantillas/'.$mvc_vis_plantilla.'Plantilla.php') ?>**


Otra diferencia del *layout* de *symfony* respecto al de nuestro mini-*framework*
es el contenido de la sección *head*. En el caso de *symfony* las etiquetas *meta*
y el título de la página (*title*) son establecidos por configuración mediante el 
fichero de configuración de la aplicación *apps/dietetica/config/view.yml*. En
este archivo también se especifican las *CSS's* que deseamos utilizar y los
archivos *javascript*. 

Por último observa los enlaces que forman el menú: el atributo *href* se
construye a través de una función denominada *url_for()*, la cual construye la 
*URL* correcta para la ejecución del módulo y la acción que se le pasa en el 
argumento. De esta manera cuando cambiemos la aplicación de servidor y/o 
ubicación, no tendremos que cambiar todos los enlaces de las acciones, ya que
*symfony* construirá la *URL* correcta mediante dicha función. Ni que decir tiene
que para lograr esta independencia de las *URL's* respecto de la ubicación de la 
aplicación es necesario utilizar la función *url_for()*. *Symfony* proporciona 
un conjunto de funciones de este tipo y son denominadas *helpers*.

Ejecutamos de nuevo la acción *inicio* de la aplicación *dietetica* en el 
navegador y comprobamos como ahora aparece el menú y el pie de página, aunque 
aún sin estilos. Así que vamos a incluir las mismas *CSS's* de la unidad 2 para 
obtener el mismo resultado. Dos párrafo antes hemos dado la clave para saber como
incluir las *CSS's* y los *javascript*. Abre el fichero 
*apps/dietetica/config/view.yml* y dile que deseas utilizar la hoja de estilos
*estilo.css*.

.. code-block:: bash

	default:
	  http_metas:
		content-type: text/html
	
	  metas:
		title:        dietética
		description:  symfony project
		keywords:     symfony, project, alimentos
		language:     es
		robots:       index, follow
	
	  stylesheets:    [estilo.css]
	  javascripts:    []
	
	  has_layout:     on
	  layout:         layout	

Hemos aprovechado la edición de este fichero para cambiar algunas etiquetas *metas*.

Por último hemos de colocar el archivo *estilo.css* en su sitio que es, como ya
se ha explicado anteriormente, el directorio *web/css* del proyecto. Si 
quisiéramos incluir más *CSS's* tan solo tenemos que añadir los ficheros 
correspondientes en este directorio e indicarlo en el archivo de configuración
de la aplicación *view.yml*.

Ahora si, ya tenemos la primera acción de nuestra aplicación funcionando al 100%.


Búsqueda de alimentos por nombre
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ahora se trata de adaptar el código de la unidad anterior al esquema de
funcionamiento de *symfony*, tal y como hicimos con la acción *inicio* (*index*).
Para ello tenemos en cuenta que las acciones se construyen como métodos públicos
de la clase *alimentosActions* que comienzan con el prefijo *execute*, y que las
plantillas se llaman como la acción cuyos resultados se desea mostrar seguidas 
del sufijo *Success*.

En primer lugar presentamos el formulario de búsqueda de alimentos por nombre.
Para ello, igual que se hizo en la unidad 2, creamos una acción que denominaremos
*buscarAlimentosPorNombre*. Esta acción tan sólo tiene que especificar la 
plantilla que dibuja al formulario. Pero como *symfony* automáticamente utiliza 
una plantilla denominada de la misma forma que la acción terminada en *Success*
podemos dejar vacía la acción. Es decir cuando sea ejecutada esta acción se 
ejecutará una función vacía, lo cual dará un resultado satisfactorio y, 
automáticamente, se interpretará el código de la plantilla 
*buscarAlimentosPorNombreSuccess.php.*

Así pues al fichero *actions.class.php* debemos añadir la siguiente función:

.. code-block:: bash

	public function executeBuscarAlimentosPorNombre(sfWebRequest $request)
	{
	
	}

Y en el directorio *templates* del módulo alimentos, añadiremos el fichero  
*buscarAlimentosPorNombreSuccess.php* (la plantilla), cuyo contenido es el 
siguiente:

.. code-block:: html+jinja

	<form name="formBusqueda" action="<?php echo url_for('alimentos/procesarFormBusquedaPorNombre') ?>" method="POST">
	
		<table>
			<tr>
				<td>alimento:</td>
				<td><input type="text" name="nombre"></td>
				<td><input type="submit" value="buscar"></td>
			</tr>
		</table>
	
		</table>
	
	</form>


Es decir, exactamente el mismo código de la plantilla homóloga de la unidad 2,
que se denominamos entonces: *formBusquedaPorNombrePlantilla.php*. La única
diferencia es el contenido del atributo *action* del formulario; en este caso 
la *URL* se construye con el *helper url_for()*.

Podemos ejecutar ahora la acción recien horneada y veremos el formulario decorado
con el *layout* de la aplicación, es decir con el menú y el pie de página que, 
como ya debes saber, es común para toda la aplicación.

Accedemos a través de la *URL*:

``http://localhost/unidad3/web/index.php/alimentos/buscarAlimentosPorNombre1``

O también a través del menú de la aplicación.

Falta la segunda parte del proceso: procesar los datos que el usuario ha 
introducido en el formulario, realizar la búsqueda en la base de datos y mostrar
los resultados. Para ello necesitaremos una nueva acción con su plantilla
correspondiente. El nombre de esta acción ya lo hemos determinado en el 
parámetro *action* del formulario: *procesarFormBusquedaPorNombre*, así pues 
añadimos una nueva función al archivo *actions.class.php*: 

.. code-block:: bash

	public function executeProcesarFormBusquedaPorNombre(sfWebRequest $request)
		{
			$this -> param_alimentos = array();
	
			$nombreAlimento = $request -> getParameter('nombre');
	
			$c = new Criteria();
			$c -> add(AlimentosPeer::NOMBRE, $nombreAlimento, Criteria::LIKE);
	
			$this -> param_alimentos = AlimentosPeer::doSelect($c);
	
			$this -> setTemplate('mostrarAlimentos');
		}

Este código da lugar al mismo resultado que su homólogo de la unidad anterior: 
construir un *array* con información acerca de los alimentos que satisfacen un
criterio de búsqueda, aunque la forma de hacerlo es distinta. 

En primer lugar, los parámetros del formulario que forman parte de la petición
*POST*, no se recogen utilizando la variable global ``$_POST`` de *PHP*, en su
lugar se utiliza el objeto ``$request`` que es el argumento de todas las acciones
(métodos *execute*) de *symfony*, y que proporciona, como veremos a lo largo del
curso, útiles métodos para la gestión de la petición *HTTP* (*request*) además 
de una manera homogénea de tratar los parámetros en toda la aplicación. La 
función *getParameter()* admite como argumento un *string* y devuelve el valor
del parámetro (*GET* o *POST*) cuyo nombre coincide con el argumento. Así pues
``$nombreAlimento`` almacenará el valor que el usuario haya introducido en el
formulario de búsqueda.

En segundo lugar, no se ha utilizado ninguna sentencia *SQL* explícitamente para
relizar la búsqueda en la base de datos. Los responsables de realizar esta 
función son el objeto *Criteria* y la función *doSelect()* de la clase estática 
*AlimentosPeer*. Esta clase pertenece al modelo, y puedes encontrarla en un 
archivo denominado *AlimentosPeer.php* dentro del directorio *lib* del proyecto. 
El funcionamiento de las clases del modelo de *Propel* para la manipulación de las bases de datos lo estudiaremos en una unidad dedicada a ello. Pero para calmar la curiosidad explicaremos de forma breve, sin entrar en detalles profundos, como se utiliza el modelo para realizar consultas.

Comienza el proceso por definir un objeto de la clase *Criteria* (``$c`` en
nuestro código), al que se le añaden mediante el método *add* todos los criterios
de búsqueda que precisemos. Esto equivale a establecer la parte *WHERE* de una
sentencia *SQL*. Acto seguido se pasa el objeto *Criteria* como argumento a la
función *doSelect()* de una clase estática que dispone de funciones para gestionar
los registros de las tablas de la base de datos. Cada tabla tiene asociada una
clase de este tipo cuyo nombre es *NombreTablaPeer*. En el caso que nos ocupa: 
*AlimentosPeer*. La función *doSelect()* se encarga de construir la sentencia
*SQL* que corresponda al Sistema Gestor de Base de Datos que se utilice en la 
aplicación, y nos devuelve un *array* con el conjunto de registros que satisface
la consulta. Cada registro devuelto en dicho *array* es representado por un 
objeto que proporciona métodos para acceder y manipular el valor de sus campos
(*getters* y *setters*). La clase que representa dichos registros se llama igual
que la tabla que manipulamos; en nuestro caso *Alimentos*. Según lo dicho, la
variable ``$this → param_alimentos`` será un array de objetos *Alimentos*. Además,
al ser definido como miembro de la clase *alimentosActions*, estará disponible 
en la plantilla subsiguiente.

Según lo visto hasta el momento, lo que deberiamos hacer a continuación es crear
una plantilla denominada *ProcesarFormBusquedaPorNombreSuccess.php*, para pintar 
los datos obtenidos por la acción. Sin embargo, dado que vamos a desarrollar más 
acciones que también muestran una lista de alimentos, hemos decidido implementar 
una única plantilla que denominaremos *mostrarAlimentosSuccess.php* (fíjate que 
el nombre no corresponde a ninguna acción, lo acabamos de inventar), y obligaremos
a la acción a que utilice esta plantilla mediante la función *setTemplate()* de 
la clase *sfActions* (de la cual deriva *alimentosActions*). Esto es lo que hace
precisamente la última línea de la acción *executeProcesarFormBusquedaPorNombre()*. 

A continuación mostramos el código de la nueva plantilla
*mostrarAlimentosSuccess.php*:

.. code-block:: html+jinja

	<table>
		<tr>
			<th>alimento (por 100g)</th>
			<th>energía (Kcal)</th>
			<th>grasa (g)</th>
		</tr>
		<?php foreach ($param_alimentos as $alimento) :?>
		<tr>
			<td><?php echo $alimento -> getNombre()  ?></td>
			<td><?php echo $alimento -> getEnergia() ?></td>
			<td><?php echo $alimento -> getGrasatotal()?></td>
		</tr>
		<?php endforeach; ?>
	
	</table>

Volvemos a utilizar el bucle *foreach* al estilo de plantillas (llamémosle de 
esta manera cuando no se utilizan llaves para delimitar su alcance) tal y como
hicimos en la unidad anterior, para iterar sobre el *array* de objetos *Alimentos*
que representan registros de la tabla *alimentos* y que proviene de la acción
*procesarFormBusquedaPorNombre*. Observa como el valor de los campos de la tabla
*alimentos* es accedido mediante los métodos *getters* del objeto. La plantilla 
construye una tabla con los alimentos encontrados en la acción de búsqueda. Es 
importante percatarse de que esta plantilla podrá ser utilizada por cualquier
acción que le facilite un *array* de objetos *Alimentos* denominado 
``$param_alimentos``.

Ya sólo nos queda probar el funcionamiento de la búsqueda por nombre desde el
principio, es decir, rellenar el formulario y pulsar en el botón 'buscar'.


Búsqueda de alimentos por energía
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En esencia, esta funcionalidad es idéntica a la búsqueda de alimentos por nombre.
Por ello transcribimos el código de las acciones y plantillas que la implementan
y explicamos las singularidades del caso.

Añadimos los siguiente métodos a la clase *alimentosActions*:

.. code-block:: bash

	public function executeBuscarAlimentosPorEnergia(sfWebRequest $request)
		{
	
		}
	
		public function executeProcesarFormBusquedaPorEnergia(sfWebRequest $request)
		{
			if(is_numeric($_POST['energia_min']) && is_numeric($_POST['energia_max']))
			{
				$this -> param_alimentos = array();
	
				$energiaMin = $request -> getParameter('energia_min');
				$energiaMax = $request -> getParameter('energia_max');
	
				$c = new Criteria();
				$c -> add(AlimentosPeer::ENERGIA, $energiaMin, Criteria::GREATER_THAN);
				$c -> add(AlimentosPeer::ENERGIA, $energiaMax, Criteria::LESS_THAN);
				$c -> addDescendingOrderByColumn(AlimentosPeer::ENERGIA);
	
				$this -> param_alimentos = AlimentosPeer::doSelect($c);
			 
				$this -> setTemplate('mostrarAlimentos');
			}
			else
			{
				$this -> setTemplate('mostrarErrorFormulario');
	
			}
		}

Y creamos las siguientes plantillas:

El formulario de búsqueda *buscarAlimentosPorEnergiaSuccess.php*:

.. code-block:: html+jinja

	<form name="formBusqueda" action="<?php echo url_for('alimentos/procesarFormBusquedaPorEnergia') ?>" method="POST">
	
		<table>
			<tr>
				<th>propiedad</th>
				<th>mínimo</th>
				<th>máximo</th>
			</tr>
			<tr>
				<td>energía (Kcal):</td>
				<td><input type="text" name="energia_min"></td>
				<td><input type="text" name="energia_max"></td>
				<td> <input type="submit" value="buscar"></td>
			</tr>
		</table>
	
	</form>
	
Y la plantilla *mostrarErrorFormularioSuccess.php*:

.. code-block:: html+jinja
	
	<b>Algún parámetro del formulario no es válido</b>
	<br/>
	<a href="javascript:history.back(1)">Volver</a>


Examinemos las singularidades del caso:

En primer lugar, la acción recoge dos parámetros que provienen del formulario: 
``energia_min`` y ``energia_max``, y comprueba que ambos sean valores numéricos
para garantizar una búsqueda sin errores. Si no lo son, se muestra un mensaje de
error a través de la plantilla *mostrarErrorFormularioSuccess.php*, y en caso 
contrario se procesa la petición mediante la creación de un criterio. Ahora el
criterio tiene dos condiciones *AND*, lo que se traduce en utilizar dos veces el
método *add()*. Además como deseamos mostrar los alimento en orden decreciente 
de energía, utilizamos el método *addDescendingOrderByColumn()* de este mismo
objeto. La función *doSelect()* nos devolverá un *array* de objetos *Alimentos*
que cumplen dicho criterio. Por último mostramos el resultado con la misma
plantilla (*mostrarAlimentosSuccess.php*) que confeccionamos en el apartado 
anterior.

Y la funcionalidad de búsqueda queda así construida. Ahora puedes probarla 
accediendo a ella desde el menú de la aplicación o desde la *URL*:

``http://localhost/unidad3/web/index.php/alimentos/buscarAlimentosPorEnergia``


Búsqueda combinada.
^^^^^^^^^^^^^^^^^^^

Esta funcionalidad permitirá la búsqueda de alimentos cuyas  cantidades de 
energía (en Kcal), proteínas (en gramos), hidratos de carbono (en gramos), 
fibra (en gramos) y grasa en (gramos) se encuentren simultáneamente entre unas
cantidades mínimas y máximas definidas por el usuario para cada magnitud. 

Esencialmente es igual que el caso anterior, basta con definir 10 casillas en
lugar de 2 para la introducción de los valores mínimos y máximo de cada magnitud,
y modificar el criterio de búsqueda (objeto *Criteria*) para incluir todas las 
condiciones.

Sin embargo, aunque podamos proceder de esta manera para realizar la
implementación de la búsqueda combinada, desarrollaremos el apartado utilizando
un nuevo elemento muy útil y poderoso de *symfony*: el *framework* de formularios.
Como corresponde a este capítulo, realizaremos un acercamiento panorámico con la 
finalidad de mostrar su eficacia. Más adelante dedicamos un capítulo completo a
los formularios de *symfony* donde los estudiaremos con detalle.

Comencemos por mostrar el código de las acciones que debemos añadir a la clase 
*alimentosActions*:

.. code-block:: bash

	public function executeProcesarFormBusquedaCombinada(sfWebRequest $request)
	{
		$this -> formulario = new BusquedaCombinadaForm();
	
		$datos = $request -> getParameter('parametros');
	
		$this->formulario->bind($datos);
		if ($this->formulario->isValid())
		{
			$c = new Criteria();
			$c -> add(AlimentosPeer::ENERGIA, $datos['energia_min'], Criteria::GREATER_THAN);
			$c -> add(AlimentosPeer::ENERGIA, $datos['energia_max'], Criteria::LESS_THAN);
			$c -> add(AlimentosPeer::PROTEINA, $datos['proteina_min'], Criteria::GREATER_THAN);
			$c -> add(AlimentosPeer::PROTEINA, $datos['proteina_max'], Criteria::LESS_THAN);
			$c -> add(AlimentosPeer::HIDRATOCARBONO, $datos['hc_min'], Criteria::GREATER_THAN);
			$c -> add(AlimentosPeer::HIDRATOCARBONO, $datos['hc_max'], Criteria::LESS_THAN);
			$c -> add(AlimentosPeer::FIBRA, $datos['fibra_min'], Criteria::GREATER_THAN);
		  $c -> add(AlimentosPeer::FIBRA, $datos['fibra_max'], Criteria::LESS_THAN);
		  $c -> add(AlimentosPeer::GRASATOTAL, $datos['grasa_min'], Criteria::GREATER_THAN);
		  $c -> add(AlimentosPeer::GRASATOTAL, $datos['grasa_max'], Criteria::LESS_THAN);
	
		   $this -> param_alimentos = AlimentosPeer::doSelect($c);
		   $this -> setTemplate('mostrarAlimentos');
	   }
	   else
	   {
		   $this -> setTemplate('buscarAlimentosCombinada');
	   }
	}

Al igual que en los casos anteriores añadimos dos acciones: una para mostrar el
formulario y la otra para procesarlo. Analicemos las diferencias.

Comprobamos que en la acción *buscarAlimentosCombinada*, se define un atributo 
de la clase *alimentosActions* denominado ``$this->formulario`` como instancia 
de la clase *BusquedaCombinadaForm*. Dicho atributo, como ya hemos estudiado 
anteriormente, estará disponible en la plantilla correspondiente. La clase 
*BusquedaCombinadaForm* define el formulario que deseamos utilizar en la búsqueda
combinada.

En efecto, los formularios de *symfony* consisten en clases definidas por el 
usuario que derivan de la clase base *sfForm* ofrecida por el propio *framework*.
Los formularios son considerados clases de librería, y por tanto su lugar de 
residencia es alguno de los directorios *lib* del proyecto. Como es un formulario
que utilizaremos exclusivamente en el módulo *alimentos*, lo colocamos bajo el 
directorio *lib* de dicho módulo. Este directorio debemos crearlo a mano pues la
tarea *generate:module* no lo hace por defecto. Así pues creamos el directorio
*lib*:

.. code-block:: bash

	$ mkdir apps/dietetica/modules/alimentos/lib

Y creamos el fichero *BusquedaCombinadaForm.php* donde definiremos el formulario:

.. code-block:: php

	<?php
	
	class BusquedaCombinadaForm extends sfForm
	{
		public function configure()
		{
			$this->setWidgets(array(
				'energia_min'  => new sfWidgetFormInput(),
				'energia_max'  => new sfWidgetFormInput(),
				'proteina_min' => new sfWidgetFormInput(),
				'proteina_max' => new sfWidgetFormInput(),
				'hc_min'       => new sfWidgetFormInput(),
				'hc_max'       => new sfWidgetFormInput(),
				'fibra_min'    => new sfWidgetFormInput(),
				'fibra_max'    => new sfWidgetFormInput(),
				'grasa_min'    => new sfWidgetFormInput(),
				'grasa_max'    => new sfWidgetFormInput(),
			));
		
			$this->widgetSchema->setNameFormat('parametros[%s]');
	
			$this->setValidators(array(
				'energia_min'  => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'energia_max'  => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'proteina_min' => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'proteina_max' => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'hc_min'       => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'hc_max'       => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'fibra_min'    => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'fibra_max'    => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'grasa_min'    => new sfValidatorNumber(array('required' => true, 'min' => 0)),
				'grasa_max'    => new sfValidatorNumber(array('required' => true, 'min' => 0)),
			));
		}
	}
	?>


Los formularios de *symfony* están compuestos por **widgets** que representan 
los elementos de formulario *HTML* (*input, checkbox, textarea, radio,* etcétera)
y por **validadores** asociados a cada uno de los *widgets*. Analiza el código
anterior y podrás identificar fácilmente cada uno de estos elementos. Nuestro 
formulario *BusquedaCombinadaForm*, consta de 10 *widgets* del tipo
*sfWidgetFormInput*. A cada uno de esos *widgets* le hemos asociado un validador
del tipo *sfValidatorNumber* para garantizar que los valores introducidos por el
usuario sean numéricos. Además, para todos los casos, se exige mediante cada 
validador, que el campo no se puede dejar vacío y que el valor introducido debe
ser mayor o igual a 0.

Los objetos de tipo formulario, es decir las instancias de clases que derivan de
la clase base *sfForm* como por ejemplo *BusquedaCombinadaForm*, nos ofrecen dos
operaciones básicas: el renderizado, y la validación. La primera consiste en 
desplegar el código *HTML* que le corresponde a cada *widget*, y se utiliza en 
las plantillas donde se definen los formularios. La segunda consiste en comprobar 
si los datos enviados en la petición *HTTP* (*request*) cumplen los criterios 
especificados para cada *widget* a través de los validadores. Esta última
operación se realiza durante el procesado de los datos del formulario. 

La plantilla que dibuja el formulario definido en la acción  
*buscarAlimentosCombinada* se llama *buscarAlimentosCombinadaSuccess.php* y el
código es el que sigue:

.. code-block:: html+jinja

	<form name="formBusqueda" action="<?php echo url_for('alimentos/procesarFormBusquedaCombinada') ?>" method="POST">
	
	 <?php echo $formulario -> renderGlobalErrors() ?>
	 <?php echo $formulario -> renderHiddenFields() ?>
	
		<table border="1">
			<tr>
				<th>propiedad</th>
				<th>mínimo</th>
				<th>máximo</th>
			</tr>
			<tr>
				<td>energía (Kcal):</td>
				<td><?php echo $formulario['energia_min'] -> renderError() ?><?php echo $formulario['energia_min']-> render() ?>
				<td><?php echo $formulario['energia_max'] -> renderError() ?><?php echo $formulario['energia_max']-> render() ?>
			</tr>
			<tr>
				<td>proteina (g):</td>
				<td><?php echo $formulario['proteina_min'] -> renderError() ?><?php echo $formulario['proteina_min']-> render() ?>
				<td><?php echo $formulario['proteina_max'] -> renderError() ?><?php echo $formulario['proteina_max']-> render() ?>
			</tr>
			<tr>
				<td>hidratos de carbono (g):</td>
				<td><?php echo $formulario['hc_min'] -> renderError() ?><?php echo $formulario['hc_min']-> render() ?>
				<td><?php echo $formulario['hc_max'] -> renderError() ?><?php echo $formulario['hc_max']-> render() ?>
			</tr>
			<tr>
				<td>fibra (g):</td>
				<td><?php echo $formulario['fibra_min'] -> renderError() ?><?php echo $formulario['fibra_min']-> render() ?>
				<td><?php echo $formulario['fibra_max'] -> renderError() ?><?php echo $formulario['fibra_max']-> render() ?>
			</tr>
			<tr>
				<td>grasa (g):</td>
				<td><?php echo $formulario['grasa_min'] -> renderError() ?><?php echo $formulario['grasa_min']-> render() ?>
				<td><?php echo $formulario['grasa_max'] -> renderError() ?><?php echo $formulario['grasa_max']-> render() ?>
			</tr>
		</table>
		<input type="submit" value="buscar">
	</form>


Se trata de un formulario *HTML* maquetado como una tabla en la que cada fila
contienen el nombre de la magnitud y dos casillas de texto para que el usuario 
inserte el valor mínimo y máximo que se utilizarán en la búsqueda. Observa el uso
de los métodos *render()* y *renderError()*. El primero se encarga de construir 
el código *HTML* que le corresponde al *widget* según su tipo. El segundo, 
*renderError()*, devuelve un valor vacío la primera vez que se dibuja el
formulario, pero cuando la validación de los datos del formulario no ha sido 
satisfactoria, devuelve mensajes de error que indican al usuario porqué se ha
rechazado la petición.

El método *renderGlobalErrors()*, como su nombre indica, mostraría errores 
asociados al formulario en su conjunto. Por último el método *renderHiddenFields()*
dibuja los elementos *HTML* ocultos que pueda tener el formulario. Aunque no 
hemos definido ningún tipo de elemento oculto en nuestro formulario, es necesario 
utilizar el método *renderHiddenFields()* pues el formulario genera 
automáticamente un campo oculto denominado *_csrf* que sirve para evitar un tipo
de ataque conocido como *“cross site retrieve forgery”*. Este comportamiento 
podemos anularlo eliminando del archivo de configuración 
*apps/dietetica/config/settings.yml* el parametro *csrf_secret*. No obstante lo 
recomendamos pues de esa manera disminuiríamos el grado de seguridad de la
aplicación.

El flujo de operaciones que tienen lugar en la búsqueda combinada es el siguiente:

	1. En la acción *buscarAlimentosCombinada* se define el objeto 
	``$this->formulario`` como una instancia de la clase *BusquedaCombinadaForm.*

	2. La plantilla *buscarAlimentosCombinada* dibuja el formulario *HTML*
	mediante el renderizado de los *widgets* que componen el objeto formulario 
	anterior. Observa el uso del método render en el código anterior.

	3. En la parte cliente (navegador *web*), el usuario de la aplicación
	introduce los datos y envía el formulario al servidor.

	4. Se ejecuta la acción *procesarFormBusquedaCombinada* tal y como se ha
	indicado en el atributo *action* del elemento *form* de la plantilla. Esta 
	acción se encarga de validar los datos de la petición *HTTP* (*POST request*)
	que provienen del formulario y realizar la búsqueda en caso de que los datos
	sean válidos.

Observemos más de cerca este último punto para comprender como se realiza la
validación: 

	1. En primer lugar se crea un nuevo formulario, es decir un objeto de la 
	clase *BusquedaCombinadaForm*. Dicho formulario se declara como miembro de 
	la clase *alimentosActions* para que pueda estar disponible en la plantilla. 
	Dicho de otro modo, se utiliza el identificador *$this*.

	2. Se asocia el formulario recién creado a los parámetros de la petición 
	*HTTP* mediante el método *bind()* del formulario. Esta operación permitirá
	delegar la validación de los datos al propio objeto formulario, aliviando al
	programador de realizar una de las tareas más enrevesadas de la programación 
	de aplicaciones *web*.

	3. Mediante el método *isValid()* del formulario se comprueba la validez de
	los datos recibidos según lo especificado en la sección de validadores de la
	clase *BusquedaCombinadaForm* donde se definió nuestro formulario. 
	Reincidimos en el hecho de que es el propio objeto formulario quién realiza 
	la compleja tarea de validar los datos.

	4. Si los datos pasan el test de validez, se realiza la consulta construyendo 
	el criterio adecuado con los datos del formulario y utilizando el método
	*doSelect()*. El conjunto de alimentos encontrados serán visualizados por la
	plantilla *mostrarAlimentoSuccess.php* que ya hemos utilizamos en las otras
	búsquedas. Si los datos no son válidos, la acción se dibujará mediante la
	plantilla *buscarAlimentosCombinadasSuccess.php*, es decir, la misma que 
	dibuja el formulario, pero esta vez el objeto formulario que la acción le 
	pasa esta asociado a unos datos determinados (los que venían en la petición 
	*HTTP*) y, debido a que no ha pasado el test de validez, contiene unos 
	mensajes de error que informan sobre las causas del rechazo del formulario.
	Así pues el formulario que se entrega en esta ocasión al cliente irá relleno
	con los datos que el usuario introdujo anteriormente y, gracias al método 
	*renderError()*, con los mensajes de error.


.. note::

   El borrado de la caché. Para mejorar el tiempo de ejecución de los *scripts*,
   *symfony* incorpora un mecanismo de caché mediante el cual, tanto la 
   configuración como las funciones de librería son cacheadas la primera vez que 
   se ejecuta la aplicación. Eso significa que si, después de haber ejecutado la 
   aplicación haces un cambio en algún fichero de configuración o en alguna 
   librería, estos cambios no se verán reflejados en las siguientes ejecuciones 
   pues la ejecución del *framework* “tira” de los ficheros cacheados. La 
   solución a este problema es lo que se denomina borrar la caché, lo cual se
   hace mediante el comando:
   
   ``# symfony cc``
   
   el cual, al eliminar los datos de la caché, obliga a *symfony* a reconstruirlos
   utilizando los cambios que hayas realizado en la configuración y/o las 
   librerías.
   
   Como acabamos de hacer un cambio en la librería consistente en añadir un 
   formulario al módulo de alimentos, debemos borrar la caché para que este 
   cambio surta efecto.
   
   Atención: muchas veces, especialmente cuando se comienza a utilizar *symfony*,
   nos olvidamos de este detalle y podemos volvernos locos buscando un error que 
   no existe. Así que cuando algo no te funcione piensa inmediatamente si los
   cambios que has realizados exigen borrar la caché.


Ahora es el momento de probar la funcionalidad recién implementada. Puedes 
acceder a ella mediante el menú de la apicación o a través de la *url*:

``http://localhost/unidad3/web/index.php/alimentos/buscarAlimentosCombinada``

Para probar el funcionamiento de la validación de formulario deja alguna/s 
casilla/s sin rellenar, introduce en otra/s un valor no númerico, en otra/s un
valor numérico negativo, y en otra/s un valor numérico positivo. Observa como la
acción que procesa el formulario rechaza los datos y muestra de nuevo el 
formulario con los mensajes de error que le corresponde a cada casilla. ¡Y todo 
esto con tres líneas de código!, una para definir el formulario, otra para 
asociarlo a los datos de la petición *HTTP* (*bind*) y otra para realizar la 
validación (*isValid*).


Inserción de registros
^^^^^^^^^^^^^^^^^^^^^^

Finalizamos la aplicación implementando la funcionalidad de inserción de 
alimentos en la base de datos. Ahora necesitaremos un formulario con 6 casillas 
de texto que se correspondan con los campos de la tabla alimento: *nombre, 
energia, proteina, hidratocarbono, fibra* y *grasatotal*. Una vez que se envíen 
estos datos al servidor, la aplicación los procesará: si son válidos, los 
insertará en la tabla correspondiente, si no lo son mostrará al usuario los
mensajes de error correspondientes sobre el propio formulario.

Según lo que hemos visto en el apartado anterior, debemos definir una clase 
derivada de *sfForm* para definir dicho formulario. Sin embargo *symfony* ya 
hizo esto por nosotros cuando comenzamos el proyecto; en efecto, la tarea

.. code-block:: bash

	$ symfony propel:build-forms


se encarga de construir para cada tabla de la base de datos, un formulario con 
los campos que le corresponda. Además estos formularios, al estar asociados a la 
base de datos, ofrecen métodos automáticos para modificar y añadir registros a 
sus tablas asociadas. Puedes ver los archivos generados en el directorio *lib/form*
del proyecto. ¡Así pues una tarea menos que hacer!

Las acciones que añadiremos para implementar esta funcionalidad son las que 
siguen:

Trozo del archivo: apps/dietetica/modules/alimentos/actions/actions.class.php

.. code-block:: bash

	public function executeInsertarAlimento(sfWebRequest $request)
	{
		$this -> formulario = new AlimentosForm();
	}
	
	public function executeProcesarFormInsertarAlimento(sfWebRequest $request)
	{
		$this -> formulario = new AlimentosForm();
	
		$datos = $request->getParameter('alimentos');
		$this->formulario->bind($datos);
		if ($this->formulario->isValid())
		{
			$this -> formulario ->save();
			$this -> getUser() -> setFlash('alimento', $datos['nombre']);
	
			$this -> redirect('alimentos/insertarAlimento');
		}
		else
		{
			$this -> setTemplate('insertarAlimento');
		}
	}


La primera de ellas define el formulario de inserción que se dibuja mediante la 
plantilla *insertarAlimentoSuccess.php*, y la segunda procesa los datos que se 
envían a través de dicho formulario. 

Presentamos el código de la plantilla *insertarAlimento* (fichero 
*insertarAlimentoSuccess.php*):

.. code-block:: html+jinja

	<?php if($sf_user -> hasFlash('alimento')): ?>
	<div class="notice">
		   el alimento "<?php echo $sf_user -> getFlash('alimento') ?>" se ha insertado correctamente
	</div>
	<?php endif; ?>
	
	<form name="formInsertar" action="<?php echo url_for('alimentos/procesarFormInsertarAlimento') ?>" method="POST">
	   <?php echo $formulario -> renderGlobalErrors() ?>
	   <?php echo $formulario -> renderHiddenFields() ?> 
	   <table>
			<tr>
				<th>Nombre</th>
				<th>Energía (Kcal)</th>
				<th>Proteina (g)</th>
				<th>H. de carbono (g)</th>
				<th>Fibra (g)</th>
				<th>Grasa total (g)</th>
			</tr>
			<tr>
				<td><?php echo $formulario['nombre'] -> renderError() ?><?php echo $formulario['nombre'] -> render() ?></td>
				<td><?php echo $formulario['energia'] -> renderError() ?><?php echo $formulario['energia'] -> render() ?></td>
				<td><?php echo $formulario['proteina'] -> renderError() ?><?php echo $formulario['proteina'] -> render() ?></td>
				<td><?php echo $formulario['hidratocarbono'] -> renderError() ?><?php echo $formulario['hidratocarbono'] -> render() ?></td>
				<td><?php echo $formulario['fibra'] -> renderError() ?><?php echo $formulario['fibra'] -> render() ?></td>
				<td><?php echo $formulario['grasatotal'] -> renderError() ?><?php echo $formulario['grasatotal'] -> render() ?></td>
	
			</tr>
	
		</table>
		<input type="submit" value="insertar" name="insertar" />
	</form>
	* Los valores deben referirse a 100 g del alimento

Observa de nuevo el uso de los métodos *render* y *renderError* para construir el
formulario *HTML*.

Otra singularidad de esta plantilla es que  utiliza, al principio del todo, un 
bloque *if – endif* al estilo de plantillas, es decir sin llaves ({}) para 
delimitar el bloque. El funcionamiento es exactamente el mismo que los bloques
*foreach – endforeach* que ya hemos utilizado anteriormente, pero en este caso 
con la lógica de un elemento condicional. La condición que se evalúa para 
ejecutar el bloque es la existencia o no de un parámetro *flash*. Estudiaremos
más adelante qué son estos parámetros, por lo pronto baste por decir y comprobar 
que dicho parámetro es definido en la acción *procesarFormInsertarAlimento* cuando 
la inserción se ha llevado a cabo con éxito.

Cuando el usuario envía el formulario al servidor, sus datos son procesados, tal 
y como indica el atributo *action* del elemento *form*, por la acción 
*procesarFormInsertarAlimento*. Al igual que en el caso de la búsqueda 
condicional, se define un objeto formulario de la clase *AlimentosForm* y, 
mediante el método *bind()*, se le asocian los datos enviados en la petición 
*HTTP*. A continuación se comprueba la validez mediante el método *isValid()*. 
Si los datos son válidos se insertar el registro en la tabla correspondiente a
través de la acción *save()* del formulario. De nuevo nos hemos ahorrado un
trabajo, cuando menos, pesado: la inserción de un registro en la tabla
*alimentos*. Observa que no necesitamos saber nada acerca de la base de datos, 
el formulario se encarga de todo.

Ya sólo queda probar la funcionalidad que acabamos de implementar. Accede a ella 
a través del menú de la aplicación o a través de la *URL*:

.. code-block:: bash

	http://localhost/unidad3/web/index.php/alimentos/buscarAlimentosCombinada

Prueba a introducir campos no numéricos en las casillas de los parámetros y a 
dejar alguna/s casilla/s vacía/s para comprobar el funcionamiento de la 
validación de formularios.


Ejercicios de ampliación.
-------------------------

La plantilla *mostrarAlimentosHistoEnergiaSuccess.php* con la que se muestran 
los alimentos encontrados mediante un sencillo histograma:

.. code-block:: html+jinja

	<table>
		<tr>
			<th>alimento (por 100g)</th>
			<th>energía (Kcal)</th>
		</tr>
		<?php foreach ($param_alimentos as $alimento) :?>
		<tr>
			<td><?php echo $alimento -> getNombre() ?></td>
			<td><?php echo Utilidades::pintaBarra($alimento -> getEnergia())?></td>
	
		</tr>
		<?php endforeach; ?>
	
	</table>


La función *Utilidades::pintaBarra* la hemos definido en un archivo denominado
*Utilidades.class.php* y ubicado en el directorio *apps/dietetica/lib*. Su
contenido se detalla a continuación:

.. code-block:: php

	<?php
	
	class Utilidades
	{
		static public function pintaBarra($num)
		{
			$numCaracteres =  ceil($num/10.0);
			$barra = str_repeat('*', $numCaracteres);
			return $barra;
		}
	}
	
	?>


Lo que sucede cuando pedimos al controlador central la ejecución de la acción de
un módulo es que, en un determinado momento de la ejecución del *framework*, éste
crea un objeto de  la clase ``{nombreModulo}Actions``, y ejecuta el método 
``execute{NombreAccion}`` de dicho objeto. 


Conclusión
----------

Llegamos al final del capítulo y hemos concluido la primera vuelta alrededor del
*framework*, la más externa y panorámica. Para ello hemos reescrito la aplicación
de la unidad 2 sobre *symfony*. Un ejercicio que nos mostró de forma sencilla los
fundamentos del patrón de diseño *Modelo-Vista-Controlador* y los conceptos 
*acción*, *plantilla* y *layout* de la aplicación. En esta unidad hemos utilizado 
tales conceptos en el contexto de un *framework* profesional, razón por la que el
grado de complejidad ha aumentado considerablemente. Pero el estudiante también
puede intuir una mayor solidez estructural y un aumento notable en las
posibilidades ofrecidas por *symfony* en relación al mini-*framework* de la
unidad 2.

Es muy posible que al final de este capítulo hayas quedado exhausto. Es normal, 
ya que se han introducido prácticamente todos los conceptos básicos para el
desarrollo de aplicaciones en *symfony* sin entrar en profundos detalles, aunque
tratándolos de una manera operativa, ya que nuestro propósito ha sido ofrecer una
vista panorámica del *framework*. Así que no te preocupes demasiado si piensas 
que aún no lo tienes todo bien asimilado y controlado, a medida que avances en 
el curso irás profundizado en los conceptos introducidos en este capítulo. Lo que
si te debe quedar claro es la estructura que *symfony* utiliza para organizar sus
archivos: el controlador frontal, las acciones del controlador, las plantillas de 
la vista, el *layout* de la aplicación, el modelo en los directorios *lib* y los 
archivos de configuración en los directorios *config*. 

También es recomendable que, una vez finalizada la unidad, estudies con más rigor
el código desarrollado. Modifícalo y comprueba el resultado obtenido con el que
has previsto, si no coinciden rectifica y vuelve a probar. Pon a trabajar duro a
la intuición y al razonamiento deductivo y lógico, de esa manera irás asimilando 
progresivamente y casi sin darte cuenta la lógica de funcionamiento de *symfony*.
Seguramente, y aunque no conozcas en profundidad el *framework*, si unes tu 
experiencia a lo que has aprendido en esta unidad, ya podrías desarrollar 
cualquier tipo de aplicación *web* sobre *symfony*, aunque, por desconocimiento, 
no uses toda la artillería pesada que el *framework* ofrece y que mejoraría 
sustancialmente la calidad de la aplicación.











