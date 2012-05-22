**Unidad 5: Profundizando en el modelo.**
==========================================


Tras la pausa necesaria para definir la aplicación que vamos a construir, 
volvemos en esta unidad al dominio de *symfony*. Recordemos la estrategia de
acercamiento en espiral que estamos siguiendo; podemos considerar que comenzamos 
la segunda vuelta. Nos acercamos al centro del problema, lo cual impone ampliar
los detalles de algunos conceptos que ya se han tratado y a introducir algunos 
nuevos.

El desarrollo de aplicaciones se realiza añadiendo progresivamente, e incluso de
manera incompleta, las funcionalidades requeridas  de manera que, en ocasiones, 
una vez iniciado el trabajo, hay que volver a algún punto que ya se había 
comenzado a desarrollar para finalizarlo definitivamente. Este trabajo es 
parecido al del artesano que elabora una pieza de, digamos, alfarería; comienza
por modelar el material de una forma aproximada según formas geométricas 
sencillas como esferas o cubos, y progresivamente, volviendo sobre las partes que
ya comenzó, elimina o añade material para conferir al objeto la forma definitiva. 
Si esto hace el programador experto en una tecnología de desarrollo, con más razón
será necesario proceder así en el desarrollo de un curso donde presentamos los
conceptos de forma gradual, volviendo sobre ellos a medida que avanzamos.  

Tomando como referencia el análisis de la unidad 4 y los recursos que allí se 
facilitaron, construiremos en primer lugar el proyecto, la aplicación *frontend*
y su módulo *gesdoc* (vacío por lo pronto). A continuación enlazaremos con la
base de datos y generaremos, mediante la herramienta *symfony*, el esquema, el 
modelo, los formularios y los filtros y ubicaremos en su lugar las *CSS's* y el
*layout* de la aplicación con su respectivo menú. Es decir, construiremos los 
pilares sobre los que se construirá la aplicación tal y como se estudió en la 
unidad 3.

En segundo lugar iniciaremos el desarrollo del módulo de gestión documental 
(*gesdoc*) de la aplicación *frontend*, implementando parcialmente las 
funcionalidades relativas a la generación de listados paginados y sus respectivos
filtros de búsqueda (requisitos D.08 y D.09). Dicho trabajo nos obligará a 
profundizar en el modelo; concretamente en el estudio de la capa de abstracción
de base de datos (*ORM*) que utiliza *symfony* para acceder a la misma y que ya 
hemos presentado y utilizado en la unidad 3. 


**Construcción del proyecto**
-------------------------------------

Esta parte requiere pocas explicaciones; seguimos el procedimiento indicado en 
la unidad 3:

Creamos el directorio accesible al servidor *web* donde se alojará el proyecto:

.. code-block:: bash

   # mkdir /opt/lamp/htdocs/gestordocumental


Y generamos el proyecto en él:

.. code-block:: bash

   # cd /opt/lamp/htdocs/gestordocumental
   # symfony generate:project gestordocumental --orm=Propel


Definimos los parámetros de conexión a la base de datos en los ficheros 
*config/databases.yml* y *config/propel.ini*:

**Fichero  config/databases.yml**

.. code-block:: yaml

	# You can find more information about this file on the symfony website:
	# http://www.symfony-project.org/reference/1_4/en/07-Databases
	
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
		  dsn:        mysql:dbname=gestordocumental;host=localhost
		  username:   root
		  password:   root
		  encoding:   utf8
		  persistent: true
		  pooling:    true


**Líneas 6 – 9 del fichero config/propel.ini**

.. code-block:: bash

	propel.database.url        = mysql:dbname=gestordocumental;host=localhost
	propel.database.creole.url = ${propel.database.url}
	propel.database.user       = root
	propel.database.password   = root

Generamos el *schema*, el modelo, los formularios y los filtros:

.. code-block:: bash

	# symfony propel:build-schema


.. note::

   Debido a un error de *Propel* con el tratamiento de los tipos de datos 
   *Timestamps* es necesario modificar en el archivo *config/schema.yml* que se 
   acaba de generar las líneas que hacen referencia a este tipo de datos de la
   siguiente manera:

   ``fecha_publicacion: { phpName: FechaSubida, type: TIMESTAMP, required: true, Value: CURRENT_TIMESTAMP }``
   
   debe ser:
   
   ``fecha_publicacion: { phpName: FechaSubida, type: TIMESTAMP, required: true }`` 

   y

   ``fecha_subida: { phpName: FechaSubida, type: TIMESTAMP, required: true, Value: CURRENT_TIMESTAMP }``

   debe ser:

   ``fecha_subida: { phpName: FechaSubida, type: TIMESTAMP, required: true }``
   

.. code-block:: bash
   
   # symfony propel:build-model
   # symfony propel:build-forms
   # symfony propel:build-filters

Generamos la aplicación y su módulo:

.. code-block:: bash
 
   # symfony generate:app frontend
   # symfony generate:module frontend gesdoc


A continuación colocamos los archivos *admin.css*, *default.css* y *menu.css* que
hemos construido en la sección de recursos de la unidad 4 en la carpeta *web/css*
del proyecto, y los recursos de imágenes correspondientes en la carpeta
*web/images*. De esta forma ya disponemos de los estilos y las imágenes que 
utilizaremos en la elaboración de las plantillas de nuestras aplicaciones. 
También debemos colocar, bajo el directorio *web/uploads*, los documentos de
ejemplo correspondiente a las versiones de los documentos que han subido los 
usuarios. Para ello descomprime el archivo *documentos.zip* que encontrarás en los
recursos de la unidad 4 en el directorio *web/uploads*.

Por último modificaremos el fichero *apps/frontend/templates/layout.php* de
acuerdo al siguiente código que tienen en cuenta las *CSS's* anteriores para la 
aplicación de los estilos gráficos.


.. code-block:: html+jinja

	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
		<head>
			 <?php include_http_metas() ?>
			 <?php include_metas() ?>
			 <?php include_title() ?>
			 <link rel="shortcut icon" href="/favicon.ico" />
			 <?php include_stylesheets() ?>
			 <?php include_javascripts() ?>
		</head>
		<body>
			<div id="contenedor_general">
				<div id="cabecera">
					<div id="logo"></div>
				</div>
	
				<div id="wrapper">
					<div id="perfil"></div>
	
					<div id="menuprincipal"></div>
	
					<div id="sf_admin_container"><?php echo $sf_content ?></div>
	
				</div>
	
				<div class="PiePagina">
					<ul>
						<li><a href="#" title="Aviso legal" target="">Aviso legal</a>|</li>
						<li><a href="http://www.w3.org/WAI/" title="Accesibilidad" target="">Accesibilidad</a>|</li>
						<li>
							<a href="http://www.w3.org/WAI/" title="Logo de la WAI" target="">
								<?php echo image_tag('valid-xhtml10.png', array('alt' => 'Accesibilidad web','width' => '50'))?>
							</a>
						</li>
					</ul>
					<p>
						<a href="#" title="© Juan David Rodríguez" target="#">© Juan David Rodríguez</a>
						<br/>
						<a href="#" title="Mentor Soft" target="_blank">Mentor Soft</a>
					</p>
					<p>
						Información general : <a href="mailto:#" title="Contacte con el webmaster">webmaster at gmail dot com</a>
						<br/>
			C/ Torrelaguna, nº10 28005 Madrid
					</p>
	
				</div>
			</div>
		</body>
	</html>


Ya sólo nos queda indicar en el ficheros *apps/frontend/config/view.yml* las hojas
de estilo que deseamos utilizar para ver el resultado de lo que vamos construyendo
con estilos gráficos aplicados.

.. code-block:: bash

	default:
	  http_metas:
		content-type: text/html
	
	  metas:
		title:        Gestor Documental
		description:  Un gestor documental construido con symfony para un curso de Mentor
		keywords:     symfony, gestor_documental, mentor
		language:     es
		robots:       index, follow
	
	  stylesheets:    [default.css, admin.css, menu.css]
	
	  javascripts:    []
	
	  has_layout:     true
	  layout:         layout


**La capa de abstracción de base de datos en symfony**
------------------------------------------------------------

La organización de una aplicación según el patrón *MVC* implica agrupar en el
modelo aquellos componentes que tengan que ver con la lógica de negocio del
dominio en que la aplicación se desarrolla. En las aplicaciones *web* gran parte 
de los procesos que conforman la lógica de negocio requieren del acceso a una o
varias bases de datos, por ello el modelo está estrechamente relacionado con los
componentes que se utilicen para realizar dicho acceso. Por otro lado *symfony*
es un *framework* orientado a objetos, por lo que es obvio que utilice una capa 
orientada a objetos para la manipulación de las bases de datos: *la capa de 
abstracción de base de datos*. A partir de ahora nos referiremos a ella con el 
término anglosajón *ORM*, *Object Relational Mapping*, algo así como mapeado de 
bases de datos relacionales a objetos. Y es que los sistemas gestores de base de
datos más utilizados (*MySQL, PostgresSQL, Oracle*) utilizan el modelo relacional
para la administración de los datos. El mapeado relacional consiste en abstraer 
las entidades de la base de datos (tablas, vistas y atributos) en objetos que las
representan.

La gran ventaja del *ORM* es la reutilización de los objetos en distintas partes 
de la aplicación e incluso en distintas aplicaciones, proporcionando un modelo 
que encaja perfectamente en un desarrollo orientado a objetos. La manera de
recuperar, introducir y modificar datos es independiente del sistema gestor de 
base de datos que utilicemos; la sintaxis utilizada es siempre la misma y es el 
propio *ORM* quien construye la *query (SQL)* adecuada al sistema gestor de base 
de datos que en cada momento utilice la aplicación. Esto proporciona una gran 
portabilidad a nuestros proyectos, ya que si deseamos hacerlos funcionar con otro
sistema de base de datos distinto bastará con indicarlo en los ficheros de
configuración del *ORM*.

*Symfony* incorpora los dos *ORM* más potentes que actualmente se ofrecen en el
mundo del *PHP*: *Propel* y *Doctrine*. En este curso utilizaremos el primero de
ellos. La razón de esta elección no es que *Propel* sea más potente que *Doctrine*,
sino que fue el primero que se acopló a *symfony* y es el que mejor conocemos. 
De hecho, a la luz de las numerosas comparativas que presentan los *blogs*
dedicados al mundo del desarrollo de aplicaciones *web*, podemos concluir que
ambos *ORM* están al mismo nivel. También podemos decir que una vez que se conoce
uno de ellos, migrar al otro no es una tarea complicada.

En la unidad 3 ya hicimos uso de las clases de *Propel* para recuperar e insertar 
registros en la base de datos, pero nos acercamos a ellas de una manera puramente
operativa, describiendo muy resumidamente, más que su funcionamiento, su uso.  En
este apartado presentaremos una explicación que, aunque sigue estando centrada en
lo operativo, criterio principal utilizado en el desarrollo de este curso,
profundiza lo suficiente para que el programador se sienta cómodo con *Propel*.


**Generación del ORM de Propel (Modelo)**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ya sabemos que el modelo de *Propel* se genera automáticamente mediante la tarea
``generate:model`` de *symfony*, y que se ubica en el directorio *lib/model* del
proyecto. Ahora explicaremos e proceso de generación. Cada base de datos que 
utilice nuestro proyecto debe tener asociada una **conexión**, la cual es un 
conjunto de parámetros definidos bajo un mismo nombre en el archivo 
*config/databases.yml*. Por defecto el nombre de la conexión es *propel*, como
puedes ver indicado en el siguiente código. Si, por ejemplo necesitamos utilizar 
otra base de datos adicional en el proyecto debemos añadir sus datos de conexión
como se indica en el texto sombreado:






Ten en cuenta que el código anterior es un ejemplo para la explicación y no debes 
añadirlo al proyecto.

Por otro lado la descripción de cada base de datos (sus tablas, atributos, y 
referencias) se define en los ficheros *schema*. Lo normal es trabajar con tantos 
ficheros *schema* como conexiones se hayan definido en el *databases.yml*; si
únicamente tenemos una conexión el fichero *schema* se denominará *schema.yml*, si
son varias las conexiones cada fichero se denominará según el siguiente patrón: 
*{nombreconexion}.schema.yml*.

Cada fichero *schema* define en su primera línea el nombre de la conexión asociada
a la base de datos que describe. Además podemos indicar en los ficheros *schema*
el directorio donde deseamos que se almacene el modelo que va a generarse a partir
de ellos a través de la directiva *package* (mira el fichero *schema.yml* de tu
proyecto). Por defecto su valor es ``lib.model``, por eso se genera el modelo en
el directorio *lib/model*, pero podemos cambiar esta ubicación, lo cual nos
permitirá, si trabajamos con varias bases de datos, organizar los modelos 
respectivos en distintas ubicaciones. Incluso con un poco de imaginación podemos 
generar varios modelos y ubicarlos en distintos paquetes (directorios donde se 
ubican los modelos) a partir de una única base de datos. Para ello podemos definir
dos conexiones con nombres distintos pero con los mismos parámetros de conexión,
crear dos archivos *schema* (por ejemplo *modelo1.schema.yml* y *modelo2.schema.yml*)
cada uno con una parte de la base de datos haciendo referencia a la conexión 
correspondiente y definiendo la directiva *package* con la ubicación donde
deseamos colocar el modelo generado. Todo esto puede venir muy bien para organizar
proyectos con bases de datos con muchas tablas o con varias bases de datos.

No obstante en la mayoría de los proyectos bastará con utilizar una sola base de 
datos, una única conexión y un único modelo ubicado en el directorio por defecto
*lib/model*.

Volvamos al proceso de generación del modelo. Con las conexiones definidas en 
*databases.yml* y los *schemas* que definen las entidades de las base de datos,
*symfony* genera a través de la tarea ``generate:model`` un conjunto de clases
que el programador utilizará para realizar operaciones con las bases de datos.


**Jerarquía de clases del modelo con Propel.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Veamos ahora la pinta que tiene el modelo generado en los directorios que hayamos
especificado con la directiva *package* de los ficheros *schemas*. Cada tabla da
lugar a 5 clases definidas en otros tantos archivos. Para hacer la explicación 
más sencilla y concreta tomemos la tabla *usuarios* de nuestro proyecto como 
ejemplo. Bajo el directorio *lib/model* vemos que se han generado los siguientes 
archivos asociados a esta tabla:

* *Usuarios.php*
* *om/BaseUsuarios.php*
* *UsuariosPeer.php*
* *om/BaseUsuariosPeer.php*
* *map/UsuariosMapBuilder.php*

El último fichero podemos obviarlo pues es utilizado internamente por *Propel* y
el programador no lo necesita para su trabajo.

Si echamos un vistazo al fichero *Usuarios.php* veremos que define la clase 
*Usuarios* que deriva de la clase *BaseUsuarios* la cual, como podrás adivinar y 
comprobar se define en el fichero *om/BaseUsuarios.php*. Además puedes ver que 
la clase ``Usuarios`` no implementa ningún método, simplemente realiza la 
derivación que hemos indicado. Por lo tanto, en un principio, la clase *Usuarios*
es equivalente a *BaseUsuarios*, su clase base. Esta última sí que está repleta
de métodos que podremos utilizar en el desarrollo de nuestro proyecto. Lo mismo 
ocurre con el fichero *UsuariosPeer.php* y *om/BaseUsuariosPeer.php*. La razón de
esta aparente complejidad es que las clases hijas están pensadas para que el 
programador las utilice en lugar de las clases bases y, además, si lo requiere, 
defina en ellas nuevos métodos que modelen la lógica particular del negocio que 
anda desarrollando. ¿Vale y qué? preguntarás, ¿no podemos definir, si fuese 
necesario, los nuevos métodos directamente en las clases bases?. Pues sí,
podríamos hacerlo, pero si más adelante, por la razón que fuese, modificamos y/o 
añadimos nuevos campos de la tabla tendríamos que volver a generar el modelo, y 
en esa regeneración ¡PERDERÍAMOS los métodos que hubiésemos añadido a la clase
base!, ya que volver a generar el modelo implica volver a generar los archivos de
las clases bases. Sin embargo, si nuestros nuevos métodos los implementamos en
las clases hijas, este desastre no ocurrirá, ya que estas clases, una vez
generadas la primera vez que se invoca la tarea ``generate:model``, no vuelven a
ser tocadas por dicha tarea en lo sucesivo. De esta forma podemos extender el 
modelo a nuestro antojo y regenerarlo tantas veces como sea necesario sin que
perdamos nuestras extensiones. En realidad se trata de una organización de los 
métodos de las clases; por una parte tenemos los métodos generados automáticamente
a partir de la definición de la tabla que son implementados en la clase base, y 
por otro lado los métodos propios del programador que los define en la clase hija.
Así pues, y como la teoría de la orientación a objetos enseña, la clase hija 
comprende a la clase base y la amplía.

Aclarado este importante detalle acerca de la organización de los métodos, 
veremos ahora qué representan las clases *Usuarios* y *UsuariosPeer*. La primera 
de ellas, *Usuarios*, es una clase instanciable, es decir, que se pueden declarar 
variables que son objetos de dicha clase. Cada objeto así declarado **representa
un registro de la tabla** *usuarios* y podemos acceder y modificar sus valores
mediante una serie de métodos, denominados *getters* y *setters*, que 
describiremos en el próximo apartado.

La segunda, *UsuariosPeer*, es una clase estática, lo cual significa que no puede 
ser instanciada, es decir, no se pueden definir variables que sean objetos de 
dicha clase. Este tipo de clases se puede concebir como un conjunto de funciones
(sus métodos), es decir como una librería. La funcionalidad de esta clase es 
proporcionar métodos para recuperar registros de la tabla *usuarios* que
satisfagan criterios definidos por el programador, en la forma de objetos 
*Usuarios*. Una vez que dispongamos de dichos objetos podremos manipularlo a 
través de sus métodos.

Obviamente, todo lo que hemos contado acerca de las clases *Usuarios* y 
*UsuariosPeer*, que representan el *ORM* de la tabla *usuarios*, es válido para
el resto de tablas de la base de datos. A partir de ahora llamaremos **clases
registro** a las clases del modelo que representan registros de las tablas, y 
**clases peer** a las clases estáticas que implementan los métodos para recuperar
registros encapsulados como objetos.


**Métodos de las clases registro**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Antes de nada hay que indicar que la mejor forma de saber qué métodos implementa 
una determinada clases es viendo su código fuente y la documentación 
correspondiente. Tanto en este apartado como en el siguiente mostraremos los
métodos más relevantes de las clases de *Propel*. El estudiante interesado en
profundizar siempre puede analizar el código generado y complementar el estudio
con las referencias sugeridas.

Como cualquier objeto en *PHP*, los objetos de las clases registro se instancian
de la siguiente manera:

.. code-block:: bash

	$usuario = new Usuarios();

La línea anterior declara la variable ``$usuario`` como un objeto (o instancia) 
de la clase *Usuarios*, el cual, como ya se ha dicho antes, representa un 
registro de la tabla *usuarios*. Por lo pronto es un registro inexistente en 
dicha tabla, de hecho aún es un objeto vacío de contenido. ¿Qué podemos hacer con
él?

**Definir sus atributos**

Mediante los métodos llamados *setters* podemos definir el valor de los atributos
del objeto. Estos atributos coinciden con los campos de la tabla que representa
la clase. Siguiendo con nuestro ejemplo, los atributos del objeto ``$usuario``
serían ``id_usuario``, ``nombre``, ``apellidos``, ``username``, ``password`` y
``perfil``, sin embargo, como dicta la norma de la encapsulación, el acceso a los
miembros del objeto debe realizarse a través de métodos construidos para tal fin.
Los métodos que sirven para definir valores se denominan *setters*, y los que se
utilizan para recuperar valores *getters*. Así pues podemos dotar de contenido al
objeto que hemos construido de la siguiente manera:

.. code-block:: bash

	$usuario → setNombre('Anselmo');
	$usuario → setApellidos('González García');
	$usuario → setUsername('anselmo');
	$usuario → setPassword('s8udR3');
	$usuario → setPerfil('administrador');


Es decir los métodos *setters* se nombran anteponiendo el prefijo *set* al nombre
del campo reescrito mediante la notación *UpperCamelCase*, lo cual significa 
colocar en mayúsculas la primera letra del nombre del campo y las letras que van 
tras el carácter '_' (*underscore*), eliminando de la cadena este último. 

Tras escribir el código anterior tenemos un objeto con sus atributos definidos. 
Sin embargo **aún no existe como registro** en la base de datos.

**Obtener el valor de los atributos**

Ahora podemos obtener los valores de los campos mediante los métodos *getters*:

.. code-block:: bash

	$nombre    = $usuario → getNombre();
	$apellidos = $usuario → getApellidos();
	$userName  = $usuario → getUsername();
	$password  = $usuario → getPassword();
	$perfil    = $usuario → getPerfil();


El nombre de los métodos *getters* se construye de la misma manera que el de los
*setters* pero utilizando el prefijo *get* en lugar de *set*.

**Insertar en la base de datos un nuevo registro**

Cuando queramos grabar en la base de datos la información que contiene el objeto 
utilizamos el método *save()*:

.. code-block:: bash

   $usuario → save();


Una vez invocado este método, el *ORM* crea un nuevo registro en la tabla 
*usuarios*, y por tanto se asigna un valor numérico al campo correspondiente a la
clave principal, *id_usuario* en el ejemplo. Esta operación se corresponde con
una inserción en la base de datos (*INSERT*).

**Actualizar un registro existente**

Ahora si hacemos:

.. code-block:: bash

   id_usuario = $usuario → getIdUsuario();


obtenemos el valor que el sistema gestor de base de datos ha asignado a la clave
principal del registro insertado. Dicho valor no está disponible hasta que no se
graba en la base de datos mediante *save()*.

Podemos volver a cambiar los valores de los atributos mediante los métodos 
*getter*, pero recuerda que solo se grabarán en la base de datos cuando se
invoque el método *save()*. Si cambiamos algún atributo del objeto mediante los 
*getters* correspondientes y volvemos a utilizar el método *save()*, se actualiza
en la base de datos el registro en cuestión. Por tanto, en esta situación, el
método *save()* se corresponde con una operación de actualización (*UPDATE*) en 
la base de datos.

**Métodos de las clases peer y  el objeto Criteria.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ya te habrás dado cuenta de que nos faltan dos operaciones fundamentales de 
acceso a base de datos; la recuperación de registros (*SELECT*) y su eliminación
(*DELETE*). Las clases peer definen una serie de métodos estáticos que, en
combinación con el objeto *Criteria*, nos proporcionan colecciones (*arrays*) de
objetos-registro que satisfacen un criterio dado y otros métodos para realizar 
la eliminación de registros. También constituyen en sí mismas una descripción de 
las tablas que representan gracias a la definición de constantes que representan 
los nombre de los campos de la tabla que modelan. Estas constantes, como veremos
en breve, son muy utilizadas para construir los criterios de selección. Así por 
ejemplo el nombre de los campos de la tabla *usuarios* puede accederse mediante
las siguientes constantes:

* *UsuariosPeer::ID_USUARIO*
* *UsuariosPeer::NOMBRE*
* *UsuariosPeer::APELLIDOS*
* *UsuariosPeer::USERNAME*
* *UsuariosPeer::PASSWORD*
* *UsuariosPeer::PERFIL*

Presentaremos ahora las operaciones más corrientes que se realizan para recuperar 
registros en forma de objetos de *Propel*.

**Obtener un registro específico**

El método *retrieveByPK()* nos permite recuperar un registro cuya clave principal 
se conoce:

.. code-block:: bash

	$usuario = UsuariosPeer::retrieveByPK(21);


La línea anterior construye un objeto de la clase *Usuarios* a partir del registro
nº 21 de la tabla *usuarios* siempre que dicho registro exista, en caso contrario
la variable ``$usuario`` sería definida como ``null``. A partir de este momento
podemos obtener los valores de sus campos, modificarlos y actualizarlos en la base
de datos según hemos visto en el apartado anterior.

**Obtener todos los registros de una tabla**

El objeto *Criteria* modela la parte *WHERE* de una consulta *SQL*. La siguiente 
línea declara un objeto *Criteria* vacío:

.. code-block:: bash

	$c = new Criteria();


Dicho objeto se utiliza como argumento de la función *doSelect()* de las clases 
*peer* para recuperar los objetos que satisfacen un criterio determinado. Si 
utilizamos un criterio vacío como el anterior obtendremos todos los registros de la tabla:

.. code-block:: bash

	$usuarios = UsuariosPeer::doSelect($c);


La línea anterior define un *array* de objetos *Usuarios* con todos los registros 
de la tabla *usuarios*. Como es un *array*, puede ser iterado mediante las 
instrucciones que *PHP* ofrece para la iteración, siendo *foreach* la más usada 
con diferencia:

.. code-block:: bash

	foreach($usuarios as $u)
	{
		echo '<b>'. $usuario → getNombre() .' '. $usuario → getApellidos(). '</b>;
		echo '<br/>';
	}

El código anterior imprimiría en el navegador una lista con los nombres y 
apellidos de todos los usuarios en negrita.

**Obtener un subconjunto de registros que satisface un criterio**

Se haría como en el ejemplo anterior, mediante la función *doSelect()*, la 
diferencia es que el criterio (``$c``) que pasamos por argumento no está vacío 
y modela un filtro (*WHERE*) de selección de registros. La definición del 
criterio se realiza utilizando los métodos  del objeto *Criteria*. Aunque son
muchos, en la práctica se puede hacer casi de todo con el subconjunto que a 
continuación describiremos.

El método *add()* del objeto *Criteria* se utiliza para añadir condiciones de 
búsqueda sobre la tabla especificada en su primer argumento:

.. code-block:: bash

	$c = new Criteria();
	$c → add(UsuariosPeer::NOMBRE, 'Anselmo');
	
	$usuarios = UsuariosPeer::doSelect($c);


El código anterior declara un objeto *Criteria* y le añade la condición de que el
campo nombre de la tabla *usuarios* sea igual *'Anselmo'*. De esa manera, la 
variable ``$usuarios`` recibirá a través de la función *doSelect()* de la clase 
*UsuariosPeer* un *array* con todos los objetos usuarios cuyo nombre es igual a
*'Anselmo'*.

Podemos realizar otras operaciones de comparación que tienen correspondencia en
las condiciones de la clausula *WHERE* de una consulta *SQL*. Las más usuales son
*LIKE, NOT_EQUAL, NOT_LIKE, IS_NULL, IS_NOT_NULL*.

Así por ejemplo, si en el código anterior sustituimos la segunda línea por la 
siguiente:

.. code-block:: bash

	$c → add(UsuariosPeer:NOMBRE, 'An%', Criteria::LIKE);

El *array* ``$usuarios`` contendría una colección de objetos *Usuarios* cuyo 
nombre satisface el patrón de búsqueda *'An%'*, teniendo el carácter '*%*' el
mismo significado que en las consultas *SQL*.

Si utilizamos varias veces el método *add()*, el criterio final será el producto
lógico (*AND*) de cada uno de las comparaciones:

.. code-block:: bash

	$c = new Criteria();
	
	$c → add(UsuariosPeer::NOMBRE, 'An%', Criteria::LIKE);
	$c → add(UsuariosPeer::PERFIL, 'lector');
	
	$usuarios = UsuariosPeer::doSelect($c);


Con este código obtenemos todos los usuarios que tienen el perfil *lector* y
cuyo nombre comienza por *'An'*.

Si queremos realizar sumas lógicas (*OR*), utilizamos el método *addOr()* en
lugar de *add()*. Su forma de uso es exactamente la misma.

También podemos añadir uniones (*Joins*) con otras tablas para recuperar 
registros. Esto se hace añadiendo al criterio condiciones de unión mediante el
método *addJoin()*:

.. code-block:: bash

	$c = new Criteria();
	
	$c → add(UsuariosPeer::NOMBRE, 'An%', Criteria::LIKE);
	$c → addJoin(UsuariosPeer::ID_USUARIO, DocumentosPeer::ID_USUARIO);
	
	$documentos = DocumentosPeer::doSelect($c);

Con este código obtendríamos en ``$documentos`` un *array* con todos los objetos
*Documentos* que pertenecen a los usuarios cuyo nombre satisface el patrón de 
búsqueda *'An%'*.

De la misma manera que podemos recuperar registros mediante el método *doSelect()*
de las clases peer, podemos borrarlos con el método *doDelete()*:

.. code-block:: bash
	
	$c = new Criteria();
	
	$c-> add(UsuariosPeer::NOMBRE, 'An%', Criteria::LIKE);
	
	UsuariosPeer::doDelete($c);

El código anterior eliminaría de la tabla *usuarios* todos los registros cuyo
nombre satisfaga el patrón de búsqueda *'An%'*.


**Recuperación de objetos relacionados con otros objetos.**

Cualquier base de datos, por muy sencilla que sea, define relaciones entre sus 
tablas que permiten relacionar registros de unas tablas con otras mediante claves
foráneas. En el dominio del *ORM* decimos que los objetos de una clase (registros)
pueden estar relacionados con los de otra. Por ello cada objeto-registro,
implementa unos métodos que nos permiten obtener todos los objetos que con él se
relacionan. 

Cuando una tabla contiene un índice que se relaciona con la clave principal de 
otra tabla, cada objeto de la primera tiene asociado un objeto de la segunda. 
Por ejemplo, en nuestra base de datos cada registro de la tabla *documentos* tiene
asociado un registro de la tabla *usuario*, lo que equivale a decir que cada 
objeto de la clase *Documentos* tiene asociado un objeto de la clase *Usuarios*.
Podemos obtener el objeto *Usuarios* asociado a un objeto *Documentos* dado de la
siguiente manera:

.. code-block:: bash

	$documento = DocumentosPeer::retrieveByPk(2);
	
	$usuario = $documento → getUsuarios();

El código anterior recupera el documento con clave principal nº 2, y 
posteriormente obtiene su usuario asociado. Es decir, los objetos que están en
relación N – 1 con otros objetos, implementan un *getter* de la forma 
*get{NombreClaseRelacionada}()*, para recuperar su objeto asociado.

También podemos estar en la situación opuesta, un objetos tiene otros muchos 
objetos relacionados; se trata de la relación contraria 1 – N. Por ejemplo un
objeto *Usuarios* puede tener asociados muchos objetos *Documentos*. La forma de
obtenerlos sería la siguiente:

.. code-block:: bash

	$usuario = UsuariosPeer::retrieveByPK(5);
	
	$documentos = $usuarios → getDocumentoss();

El código anterior recupera el usuario con clave principal nº 5 y posteriormente 
obtiene un *array* (``$documentos``) con todos los objetos *Documentos* asociados
a él. Es decir, los objetos que están en relación 1 - N con otros objetos,
implementan un *getter* de la forma *get{NombreClaseRelacionda}s()*, para 
recuperar un *array* con sus objetos asociados.

De esta manera podemos navegar por toda la estructura de datos, de objeto en
objeto  a través de sus relaciones, y obtener todos los atributos referidos
directa e indirectamente con él.

Son muchas más las funcionalidades y métodos que ofrece *Propel*. No obstante 
paramos aquí, por lo pronto, el estudio teórico del este *ORM*. Con lo dicho 
hasta el momento se cubre una amplia gama de problemas reales. Remitimos al
estudiante a los recursos que indicamos sobre *Propel* para profundizar en su 
estudio y para utilizarlo como referencia en caso de necesitar nuevas 
características del mismo. Así pues continuamos la implementación de nuestro
gestor documental.

.. note::

	Recursos sobre Propel:
	
	Sitio oficial:
	
	*http://www.propelorm.org/*
	
	Chuleta sobre el objeto *Criteria*:
	
	http://www.cheat-sheets.org/saved-copy/sfmodelcriteriacriterionrsrefcard_enus.pdf
	
	Chuleta sobre el *Schema*:
	
	http://www.cheat-sheets.org/saved-copy/sfmodelsecondpartrefcard.pdf


**Implementación del listado documentos**
-------------------------------------------------

Una vez que tenemos montado el marco de nuestro cuadro ya podemos empezar a 
pintarlo; es la hora de picar el código propio de nuestra aplicación sobre los
pilares del proyecto que acabamos de definir. La primera funcionalidad que 
implementaremos será la generación de listados de documentos filtrados mediante 
los campos de un formulario de búsqueda.


**Listado de todos los documentos**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

El listado de documentos forma parte del módulo *gesdoc* de la aplicación 
*frontend*. Llamaremos *index* a la acción encargada de mostrar dicho listado. 
La elección del nombre no es arbitraria; ya se ha dicho en otra ocasión que 
*symfony* interpreta como acción por defecto de cualquier módulo la que se llame 
de está manera. Como en el caso del gestor documental el listado de documento es 
la acción de inicio del módulo, pues su nombre está cantado.

Por lo pronto, la acción de inicio (*index*), recogerá todos los documentos, y 
su plantilla asociada, *indexSuccess.php*, los pintará en una tabla diseñada 
según el boceto que se ha planteado en el análisis de la aplicación. Para recoger
todos los documentos debemos acceder a la base de datos, es decir, debemos usar 
los objetos de *Prope*l tal y como se ha explicado en esta misma unidad: creamos
un criterio vacío y lo pasamos como argumento al método *doSelect()* de la clase 
peer correspondiente a la tabla *documentos*. 

Esto es, añadimos al fichero de acciones del módulo el siguiente código:


*Trozo del archivo: apps/frontend/modules/gesdoc/actions/action.class.php*

.. code-block:: bash

	public function executeIndex(sfWebRequest $request)
	{
		$c = new Criteria();
	
		$this -> documentos = DocumentosPeer::doSelect($c);        
	}

el atributo ``$this → documentos`` recibe un *array* con todos los objetos 
*Documentos* existentes, y estará disponible en la plantilla correspondiente para
ser iterado y mostrar los atributos de los documentos que nos interese. Agregamos
el siguiente código a la plantilla *indexSuccess.php*:

*Contenido del archivo:  apps/frontend/modules/gesdoc/templates/indexSuccess.php*


.. code-block:: html+jinja

	<table>
		<thead>
			<tr>
			  <th>Título</th>
			  <th>Autor</th>
			  <th>Versiones</th>
			  <th>Acciones</th>
			</tr>
		</thead>
	
		<tbody>
			<?php foreach ($documentos as $d): ?>
			<tr>
			  <td><?php echo $d -> getTitulo() ?></td>
			  <td></td>
			  <td></td>
			  <td></td>
			</tr>
			<?php endforeach; ?>
		</tbody>
	</table>


Ya puedes probar desde el navegador el resultado de esta acción:

``http://localhost/gestordocumental/web/frontend_dev.php/gesdoc1``

Y verás una tabla (aún por completar) con el título de todos los documentos, los cuales se han obtenido en la plantilla accediendo a cada uno de los objetos Documentos del array $documentos y pidiendo su título a través de su getter getTitulo().

Para completar la tabla debemos acceder a los autores y versiones de cada documento. Según nuestro modelo de datos, Usuarios (que pueden ser autores) y Versiones son objetos (registros) relacionados con los documentos de forma que un documento sólo puede tener un autor, y puede tener asociadas varias versiones. Se trata por tanto de aplicar los métodos de acceso explicados en el apartado 2.5, para realizar dicha tarea. 

Si $d representa un objeto Documentos, entonces:

$autor = $d → getUsuarios();

$autor es un objeto Usuarios asociado al documento y
$versiones = $d → getVersioness();

$versiones es un array de objetos Versiones asociados al documento.

Con esto podemos mostrar los datos que nos faltan en las columnas:

Contenido del archivo:  apps/frontend/modules/gesdoc/templates/indexSuccess.php

.. code-block:: html+jinja

	<table>
		<thead>
			<tr>
			  <th>Título</th>
			  <th>Autor</th>
			  <th>Versiones</th>
			  <th>Acciones</th>
			</tr>
		</thead>
	
		<tbody>
			<?php foreach ($documentos as $d): ?>
			<tr>
			  <td><?php echo $d -> getTitulo() ?></td>
			  <td><?php echo $d -> getUsuarios() -> getNombre().' '. $d -> getUsuarios() -> getApellidos()?></td>
			  <td>|
				<?php foreach ($d -> getVersioness() as $v): ?>
				<?php echo $v -> getNumero() ?> |
				<?php endforeach; ?>
			  </td>
			  <td>
				<?php echo link_to('modificar', 'gesdoc/modificar?id_documento='.$d -> getIdDocumento()) ?> |
				<?php echo link_to('subir versión', 'gesdoc/subirVersion?id_documento='.$d -> getIdDocumento()) ?>
			  </td>
			</tr>
			<?php endforeach; ?>
		</tbody>
	</table>


Es decir, por un lado accedemos desde cada documento a su usuario asociado, y 
extraemos el nombre y los apellidos, y por otro accedemos al *array* con sus 
versiones asociadas, lo iteramos y extraemos los números de versión. Además se 
ha completado el código de las acciones mediante los enlaces correspondientes 
a la edición del documento y a la subida de nuevas versiones. Por su puesto 
estas acciones aún no existen, pero ya hemos decidido sus nombres y módulo 
(*gesdoc/modificar* y *gesdoc/subirVersion*) en esta etapa. 

Los enlaces de las acciones se han construido utilizando una función denominada
*link_to()*, la cual construye el código *HTML* del enlace con el texto que se
indique en su primer argumento y la *URL* del módulo y acción adaptada al 
servidor donde se ejecuta la aplicación, en su segundo argumento. De esta manera
cuando cambiemos la aplicación de servidor y/o ubicación, no tendremos que 
cambiar todos los enlaces de las acciones, ya que *symfony* construirá la *URL*
correcta mediante dicha función. La función *link_to()*, al igual que *url_for()*
que vimos en la unidad 3, es lo que se denominan un *helper* de *symfony*.

Aprovecharemos este momento para ilustrar como, extendiendo el modelo, podemos
simplificar el código y hacerlos más legible y reutilizable. Fíjate que el acceso
al nombre y apellido del usuario se realiza mendiante el siguiente código:

.. code-block:: bash

	<?php echo $d -> getUsuarios() -> getNombre().' '. $d -> getUsuarios() -> getApellidos()?> 

 
Es decir, tenemos que usar dos veces el objeto para mostrar el nombre y los 
apellidos. Sería todo más sencillo si el objeto *Usuarios* ofreciese un método 
que devolviese el nombre y los apellidos de una vez. Pero ya sabemos que *Propel*
nos brinda la posibilidad de extender los objetos a través de las clases hijas. 
Así pues podemos implementar un método en la clase *Usuarios* (archivo 
*lib/model/Usuarios.php*), que se llame, por ejemplo, *dameNombreYApellidos()*,
y que realice dicha labor: 


*Función añadida al  archivo: lib/model/Usuarios.php*

.. code-block:: bash

	public function dameNombreYApellidos()
	 {
		return self::getNombre().' '.self::getApellidos();
	 }

Ahora podemos sustituir en la plantilla la farragosa línea anterior por la
siguiente que es mucho más legible:

.. code-block:: bash

	<?php echo $d -> getUsuarios() -> dameNombreYApellidos()?> 
	

Pero podemos ir incluso más lejos. Y es que *PHP* está construido de tal manera 
que si el programador implementa en sus objetos un método denominado *__toString()*,
que devuelva una cadena de texto, se puede enviar dicho objeto directamente a
la salida estándar (la pantalla) mediante la instrucción *echo*. Por ello, si
cambiamos de nombre al método *dameNombreYApellidos()* y lo llamamos *__toString()*,
la línea de la plantilla donde se pinta el nombre y el apellido del usuario 
quedaría reducida a:

.. code-block:: bash

	<?php echo $d -> getUsuarios() ?> 


Como veremos más adelante cuando estudiemos los formularios y la generación 
automática de módulos *CRUD* de *symfony*, es muy importante que los objetos de
*Propel* implementen el método *__toString()*. Y esta tarea la debemos realizar
manualmente, ya que el *framework* no puede adivinar cómo queremos mostrar por
pantalla nuestros objetos.

Por último, adaptamos la plantilla a los estilos que estamos utilizando en 
nuestro proyecto (*CSS's*), y nos queda el siguiente código para el fichero
*indexSuccess.php* (¡atención!, no olvides implementar el método *__toString()*
de la clase *Usuarios*):

*Contenido del archivo:  apps/frontend/modules/gesdoc/templates/indexSuccess.php*

.. code-block:: html+jinja

	<div id="sf_admin_header">
		<h2>Listado de documentos</h2>
		<div class="notice">Mensaje de advertencia(que modificaremos más tarde)</div>
	</div>
	<div id="sf_admin_content">
		<div id="sf_admin_list">
			<table>
				<thead>
					<tr>
						<th>Título</th>
						<th>Autor</th>
						<th>Versiones</th>
						<th>Acciones</th>
					</tr>
				</thead>
	
				<tbody>
					<?php foreach ($documentos as $d): ?>
					<tr>
						<td><?php echo $d -> getTitulo() ?></td>
						<td><?php echo $d -> getUsuarios() ?></td>
						<td>|
							<?php foreach ($d -> getVersioness() as $v): ?>
							<?php echo link_to($v -> getNumero(),('gesdoc/verVersion?id_version='.$v -> getIdVersion())) ?> |
							<?php endforeach; ?>
						</td>
						<td>
							<?php echo link_to('modificar', 'gesdoc/modificar?id_documento='.$d -> getIdDocumento()) ?> |
							<?php echo link_to('subir versión', 'gesdoc/subirVersion?id_documento='.$d -> getIdDocumento()) ?>
						</td>
					</tr>
					<?php endforeach; ?>
				</tbody>
			</table>
		</div>
	</div>

Observa que en este código se han implementado, mediante el uso del *helper 
link_to()*, los *links* correspondientes para descargar los documentos asociados
a las versiones al picar en el nº de versión. Se ha decidido llamar a la acción 
en cuestión *verVersion*, y se alojará en el propio módulo *gesdoc*.


**Implementación del filtro de documentos**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Ahora, según lo especificado en el análisis que realizamos en la unidad 4, vamos 
a colocar en la pantalla que acabamos de construir un formulario para filtrar los
documentos. Lo ideal para realizar esta labor es utilizar los filtros que hemos 
generado al principio del proyecto mediante la tarea *propel:build-filters*. Los 
filtros son una serie de clases que modelan formularios para realizar búsquedas. 
No obstante, para no sobrecargar de contenidos la presente unidad, hemos decidido
prescindir de ellos y construir manualmente el código *HTML* correspondiente al
filtro en cuestión. Más adelante, cuando tratemos los formularios y filtros de 
*symfony*, volveremos a revisar este punto de la aplicación.

Insertaremos cajas de texto para los campos *título, descripción* y *autor*, un 
desplegable para el campo *tipo*, y una caja de selección múltiple para las
*categorías*. El formulario de búsqueda que debemos añadir a la plantilla 
*indexSuccess.php* quedaría como sigue:

*Formulario de búsqueda añadido al archivo: apps/frontend/modules/gesdoc/templates/indexSuccess.php*

.. code-block:: bash

	...
	<form name="filtro" method="post" action="<?php echo url_for('gesdoc/index') ?>" >
	   titulo:<input type="text" id="titulo" name="documentos[titulo]" value="" />
	
		descripcion:<input type="descripcion" id="titulo" name="documentos[descripcion]" value="" />
	
		autor:<input type="text" id="autor" name="documentos[autor]" value="" />
	
		tipo: <select name="documentos[id_tipo]" id="id_tipo">
			<option value="" selected="selected" />
			<?php foreach ($tipos as $t) : ?>
			<option value="<?php echo $t -> getIdTipo()?>" selected="selected">
					<?php echo $t -> getNombre() ?>
			</option>
			<?php endforeach; ?>
		</select>
	
		categorias: <select name="documentos[categoria_list][]" multiple="multiple" id="categoria_list">
			<option value="" selected="selected">
				<?php foreach ($categorias as $c) : ?>
			<option value="<?php echo $c -> getIdCategoria() ?>" selected="selected">
				<?php echo $c -> getNombre() ?></option>
			<?php endforeach; ?>
		</select>
	
		<input type="submit" value="Buscar" />
	
	</form>
	...

Fíjate que la lista de tipos y de categorías hay que construirlas dinámicamente
mediante consulta a la base de datos, es decir, a través de objetos de *Propel*.
Es decir, para que esta plantilla pueda ser interpretada correctamente por *PHP*,
debemos proporcionarle las variables ``$tipos`` y ``$categorias``, que serán
respectivamente objetos de las clases de *Propel Tipos* y *Categorias*. Como ya
debes saber, el lugar donde se definen estos objetos es en la acción 
correspondiente. Así pues la acción *index* (método *executeIndex()*) quedaría
como sigue:

*Trozo de código del archivo:  apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executeIndex(sfWebRequest $request)
	{
		 $c = new Criteria();
	
		 $this -> tipos = TiposPeer::doSelect($c);
		 $this -> categorias = CategoriasPeer::doSelect($c);
	
		 $this -> documentos = DocumentosPeer::doSelect($c);    
	}


Podemos utilizar el mismo criterio en los tres métodos *doSelect()* de cada clase
*peer* ya que, por lo pronto, está vacío. Ahora puedes probar el resultado de la
acción y verás el formulario de búsqueda con el conjunto de *tipos* y *categorías*
debidamente relleno. El problema es que no queda muy bonito, le falta formato.
Bastaría adaptar el código *HTML* correspondiente al formulario de búsqueda a las
*CSS's* facilitadas en la unidad 4. Fíjate en el documento *HTML* de ejemplo
denominado *listadoConFiltro.html* para comprender como usar la hoja de estilo en
la visualización del formulario:

*Formulario de búsqueda añadido al archivo: apps/frontend/modules/gesdoc/templates/indexSuccess.php*

.. code-block:: html+jinja

	<div id="sf_admin_bar">
			<div class="sf_admin_filter">
				<form name="filtro" method="post" action="<?php echo url_for('gesdoc/index') ?>" >
					<table>
						<tbody>
							<tr class="sf_admin_form_row">
								<td>Título</td>
								<td><input type="text" id="titulo" name="documentos[titulo]" value="" /></td>
							</tr>
							<tr class="sf_admin_form_row">
								<td>Descripción</td>
								<td><input type="descripcion" id="titulo" name="documentos[descripcion]" value="" /></td>
							</tr>
							<tr class="sf_admin_form_row">
								<td>Autor</td>
								<td><input type="text" id="autor" name="documentos[autor]" value="" /></td>
							</tr>
							<tr class="sf_admin_form_row">
								<td>Tipo</td>
								<td>
									<select name="documentos[id_tipo]" id="id_tipo">
										<option value="" selected="selected" />
										<?php foreach ($tipos as $t) : ?>
										<option value="<?php echo $t -> getIdTipo()?>">
												<?php echo $t -> getNombre() ?>
										</option>
										<?php endforeach; ?>
									</select>
								</td>
							</tr>
							<tr class="sf_admin_form_row">
								<td>Categoría</td>
								<td><select name="documentos[categoria_list][]" multiple="multiple" id="categoria_list">
										<option value="" selected="selected">
											<?php foreach ($categorias as $c) : ?>
										<option value="<?php echo $c -> getIdCategoria() ?>">
												<?php echo $c -> getNombre() ?></option>
										<?php endforeach; ?>
									</select>
								</td>
							</tr>
						</tbody>
					</table>
					<input type="submit" value="Buscar" />
	
				</form>
			</div>
		</div>


Este código, que define la capa *sf_admin_bar* se puede colocar en cualquier 
parte de la plantilla *indexSuccess.php* siempre que quede al mismo nivel que
las capas *sf_admin_header* y *sf_admin_content*. Vuelve a cargar la página y 
verás que tiene un aspecto más agradable. No obstante aún no hemos acabado 
nuestro trabajo, ya que no sólo basta con pintar el formulario, también hay 
que procesarlo para realizar el filtrado de documentos. 

La acción que dispara el envío del formulario de búsqueda es la propia acción
que lo genera, esto es, la acción *index*. Se trata ahora de recoger los 
parámetros de la petición *HTTP* que se han pasado por *POST* y construir un
criterio adecuado para que el método *doSelect()* de la clase *DocumentosPeer*
devuelva únicamente los documentos que satisfacen el criterio de búsqueda. 


**Procesamiento de la petición. El objeto SfWebRequest.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*Symfony* proporciona un objeto especial para la manipulación de las peticiones
*HTTP*: el objeto *SfWebRequest*, que sirve al programador, fundamentalmente, 
para recoger los parámetros que el cliente (*browser*) envía al servidor *web*
y para comprobar la existencia de dichos parámetros. Los métodos más usados son:

================================= ================================================
hasParameter('nombre_parametro'); Devuelve ``true`` si la petición contiene un
								  parámetro llamado ``nombre_parametro`` y 
								  false en caso contrario.

getParameter('nombre_parametro',  Devuelve el valor del parámetro 
'valor_defecto);				  ``nombre_parametro`` de la petición si este
								  existe o el valor indicado en ``valor_defecto``
								  si no existe.
================================= ================================================

Este objeto es uno de los más utilizados en la implementación de las acciones, 
ya que la interacción con el usuario se lleva a cabo, fundamentalmente, a través
de parámetros que se envían desde el cliente mediante peticiones *HTTP*. Otro
mecanismo fundamental para controlar la interacción con el usuario es la *sesión
de usuario*. Como es de esperar, *symfony* también cuenta con un objeto, que 
estudiaremos en detalle en la próxima unidad, para la manipulación de la sesión.

Con este nuevo concepto en mente vamos a describir el procedimiento que
emplearemos para procesar los valores enviados y recoger los documentos 
pertinentes.

En primer lugar comprobaremos que se han enviado parámetros para la búsqueda, ya
que en caso contrario se desea listar todos los documentos y el criterio se dejará
vacío. Si, en efecto, la petición viene con el *array documentos* revisaremos
los valores de cada uno de los campos e iremos añadiendo criterios de búsqueda 
al objeto *Criteria* que será pasado como parámetro al método 
*DocumentosPeer::doSelect()*.

Aplicando este procedimiento la acción *executeIndex()* nos queda como sigue:

*Código de la acción index del archivo:
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executeIndex(sfWebRequest $request)
	{
		$this -> tipos = TiposPeer::doSelect(new Criteria());
		$this -> categorias = CategoriasPeer::doSelect(new Criteria());
	
		$c = new Criteria();
	
		//Inicio del filtro
		if($request -> hasParameter('documentos'))
		{
			$documentos = $request -> getParameter('documentos');
	
			if($documentos['titulo'] != '')
			{
				$c -> add(DocumentosPeer::TITULO, $documentos['titulo'], Criteria::LIKE);
			}
	
			if($documentos['descripcion'] != '')
			{
				$c -> add(DocumentosPeer::DESCRIPCION, $documentos['descripcion'], Criteria::LIKE);
			}
	
			if($documentos['autor'] != '')
			{
				$c -> addJoin(DocumentosPeer::ID_USUARIO, UsuariosPeer::ID_USUARIO);
				$c1 = $c -> getNewCriterion(UsuariosPeer::NOMBRE, $documentos['autor'], Criteria::LIKE);
				$c2 = $c -> getNewCriterion(UsuariosPeer::APELLIDOS, $documentos['autor'], Criteria::LIKE);
				$c1 -> addOr($c2);
				$c -> add($c1);
			}
	
			if($documentos['id_tipo'] != '')
			{
				$c -> addJoin(DocumentosPeer::ID_TIPO, TiposPeer::ID_TIPO);
				$c -> add(TiposPeer::ID_TIPO, $documentos['id_tipo']);
			}
	
	
			$kuku = true;
			foreach ($documentos['categoria_list'] as $cat)
			{
				if($cat != '')
				{
					$c -> addJoin(DocumentosPeer::ID_DOCUMENTO, DocumentoCategoriaPeer::ID_DOCUMENTO);
					$c -> addJoin(DocumentoCategoriaPeer::ID_CATEGORIA, CategoriasPeer::ID_CATEGORIA);
					$c -> addAnd(CategoriasPeer::ID_CATEGORIA, $cat);
				}
			}
		 }//Fin del filtro
			
		$this -> documentos = DocumentosPeer::doSelect($c);
	}

Con esto tenemos la funcionalidad especificada en el requisito D07 satisfecha. 
Para completar el requisito D08 tan sólo nos falta presentar el listado con un
paginado. Eso es lo que haremos en el próximo apartado.


**Implementación del páginado.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cuando el número de documentos crezca lo suficiente, será más cómodo presentar 
el listado subdivido en páginas de una cierta cantidad de registros, con la 
posibilidad de pasar de página hacia adelante y hacia atrás. *Symfony* también 
nos ayuda en esto gracias al objeto *sfPropelPager*. 

Como su nombre indica, pertenece a la capa de abstracción de base de datos 
*Propel*. Lo cual es natural ya que en los listados se presentan colecciones
de registros que provienen de la base de datos.

Veamos como utilizarlo. En primer lugar sustituimos la última línea de la acción
*executeIndex()*

.. code-block:: bash

	$this -> documentos = DocumentosPeer::doSelect($c);

Por el siguiente fragmento de código:

.. code-block:: bash

	 $pager = new sfPropelPager('Documentos', 4);
	 $pager->setCriteria($c);
	 $pager->setPage($request -> getParameter('page', 1));
	 $pager->init();
	 $this->pager = $pager;


Cuyo funcionamiento es el siguiente:

1. Primero se construye un objeto de la clase *sfPropelPager* (un paginador)
   indicando en el primer argumento de su constructor el objeto de *Propel* 
   sobre el que deseemos realizar la paginación (en nuestro caso *Documentos*),
   y en el segundo argumento el número de registros que mostrará por página. 

2. Después indicamos el criterio de búsqueda que se utilizará para recuperar 
   los registros.

3. Indicamos qué número de página queremos mostrar. En el código anterior dicho
   número se recoge de la petición *HTTP* a través del parámetro *page*, si no 
   existe este parámetro se sustituye por el valor *1*. Esto último nos indica 
   que debe de existir una interacción con el usuario a través de la página que 
   muestra los documentos. Veremos claramente esta interacción en la 
   implementación de la parte de la vista (la plantilla).

4. Se inicializa el paginador, lo que significa que se ejecuta la consulta 
   relativa al criterio utilizado.

5. Por último se hace el paginador accesible a la plantilla, ya que es allí donde
   lo utilizaremos.

Una vez que el paginador ha sido debidamente construido, podemos utilizar sus 
métodos para acceder a las entidades relacionadas con él. Los métodos más usados
son los siguientes:

==================== ==========================================================
Método               Descripción
==================== ==========================================================
*getNbResults()*     Devuelve el nº de registros total que se han recuperado en
                     la consulta.
                     
*setPage(n)*         Define la página actual como la número *n*. Este método es 
                     el que se ha utilizado en la acción.
                     
*getResults()*       Devuelve un *array* con los *m* objetos de *Propel* (en 
                     nuestro caso *Documentos*) de la página *n*, siendo *m* el 
                     número de elementos que se desean mostrar definido cuando 
                     se crea el objeto paginado. 
                    
*getFirstIndice()*   Devuelve el número del primer registro mostrado en la
                     página
                     
*getLastIndice()*    Devuelve el número del último registro mostrado en la
                     página
                     
*haveToPaginate()*   Devuelve true si hay que paginar, es decir, si el nº de
                     registros devueltos por la consulta es superior al nº de 
                     registros por página que deseamos mostrar.
                    
*getFirstPage()*     Devuelve el número de la primera página que ha construido 
                     el paginador. En realidad siempre devolverá el nº 1.
                     
*getLastPage()*      Devuelve el número de la última página que ha construido
                     el paginador.
                     
*getPreviousPage()*  Devuelve el número de la página anterior a la que se está 
                     mostrando (es decir a la actual, resultado de utilizar el 
                     método *setPage()*)
                     
*getNextpage()*      Devuelve el número de la página siguiente a la que se está 
                     mostrando.
==================== ==========================================================

Utilizando adecuadamente estos métodos del objeto *sfPropelPager* en la plantilla
vamos a mostrar un listado con un pequeño nº de documentos por página (4 en 
nuestro ejemplo) y un menú de navegación que permitirá ir directamente a las 
primera y última página, avanzar o retroceder una página por vez, o ir 
directamente a  un número de página específico.

En la plantilla *indexSuccess.php*, sustituimos la iteración sobre el *array*
de objetos ``$documentos`` por la iteración sobre el *array* de *m* objetos (4 
en nuestro caso) devuelto por ``$pager → getResults()``. De esta manera
mostraremos únicamente los 4 registros de la página que se haya indicado a la
acción *index* mediante el párametro *page* de la petición *HTTP*.

.. code-block:: bash

	...
	<?php foreach ($pager -> getResults()as $d): ?>
	…

El menú de navegación lo colocaremos en el pie de la tabla que muestra el listado.
Añadimos a la tabla de la plantilla *indexSuccess.php* el siguiente trozo de 
código que implementa el menú de navegación teniendo en cuenta los estilos de
las *CSS's* que estamos utilizando:

*Código añadido para implementar la navegación del paginado en el archivo*
*apps/frontend/modules/gesdoc/templates/indexSuccess.php*

.. code-block:: html+jinja

	...
	<tfoot>
	   <tr>
		 <th colspan="20">
		   <div class="sf_sf_admin_pagination">
		   <?php if ($pager->haveToPaginate()): ?>
		   <?php echo link_to(image_tag('first.png'), 'gesdoc/index?page='.$pager->getFirstPage()) ?>
		   <?php echo link_to(image_tag('previous.png'), 'gesdoc/index?page='.$pager->getPreviousPage()) ?>
		   <?php $links = $pager->getLinks(); foreach ($links as $page): ?>
		 <?php echo ($page == $pager->getPage()) ? $page : link_to($page, 'gesdoc/index?page='.$page) ?>
		 <?php if ($page != $pager->getCurrentMaxLink()): ?> - <?php endif ?>
		 <?php endforeach ?>
		 <?php echo link_to(image_tag('next.png'), 'gesdoc/index?page='.$pager->getNextPage()) ?>
		 <?php echo link_to(image_tag('last.png'), 'gesdoc/index?page='.$pager->getLastPage()) ?>
		 <?php endif ?>
		 </div>
	   <?php echo $pager->getNbResults() ?> resultados (del <?php echo $pager->getFirstIndice() ?> al <?php echo $pager->getLastIndice() ?>)
		 </th>
	   </tr>
	</tfoot>
	...

Aunque aún quede por delante mucha aplicación, esta ya comienza a tomar forma 
permitiéndonos listar documentos filtrados y paginados. Incluso hemos 
incorporado el acceso mediante *links* a ciertas operaciones (*ver versión, 
modificar* y *subir versión*) cuya implementación realizaremos en los próximos
apartados. 

Sin embargo, antes de continuar, queremos advertirte que el listado con filtro
y paginado que acabamos de implementar presenta un grave fallo. En efecto, 
mientras no utilicemos el formulario de búsqueda la paginación funciona 
perfectamente. Pero si realizamos una búsqueda cuyo resultado contenga más
registros que los mostrados en cada página, cuando accedemos a otra página a
través de algunos de los enlaces del menú de navegación, aparecen de nuevo todos
los registros, perdiéndose el filtro que habíamos aplicado. Pruébalo, tienes
derecho a enojarte.

La razón de este mal comportamiento es que la aplicación no guarda memoria del
filtro que se ha aplicado, y al ser *HTTP* un protocolo sin estado (*stateless*),
cuando se realiza la próxima petición a través de cualquier *link* del páginado
tras aplicar el filtro, se vuelve a procesar la acción *index* sin enviar datos
en el formulario de búsqueda. Por eso se vuelve a construir un criterio vacío y 
se recuperan de nuevo todos los documentos.

Resolveremos este problema utilizando la *sesión del usuario en el servidor*, 
que es un mecanismo que se utiliza extensivamente en el desarrollo de 
aplicaciones *web* para almacenar en el propio servidor datos persistentes que
la aplicación necesite conocer acerca del cliente mientras este último la esté 
utilizando. La sesión es una manera de superar la ausencia de control de estado
del protocolo *HTTP*. 

Como no queremos introducir más conceptos nuevos en esta unidad, resolveremos
este “pequeño” fallo de la aplicación en la próxima unidad, que estudia y utiliza
a fondo la sesión de usuario. Por lo pronto continuaremos implementando algunas
de las operaciones básica que se establecieron en el requisito D08 del análisis, 
concretamente:

* Descargar una versión del documento
* Ver metadatos del documento


**Implementación de operaciones sobre documentos concretos.**
---------------------------------------------------------------------

**Descarga de las versiones de un documento.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En el listado que acabamos de construir hemos colocado en la columna *'versiones'*
enlaces a cada una de las versiones de los documentos. Cuando piquemos en estos 
enlaces la aplicación nos enviará el archivo asociado a la versión, es decir,
realizaremos una descarga del mismo. 

En el momento de construir dichos enlaces ya propusimos la acción que realizaría 
la descarga del archivo: *verVersion*, acción que espera recibir a través de la
petición *HTTP* la clave principal de la versión a descargar. Fíjate en el código 
*HTML* del *link* correspondiente a las versiones de los documentos. Tienen este 
aspecto:

.. code-block:: bash

	<a href="/gestordocumental/web/frontend_dev.php/gesdoc/verVersion/id_version/4">2</a>

Así pues el próximo paso es crear una nueva acción en el módulo *gesdoc* de la
aplicación *frontend* denominada *verVersion()*, que reciba por *GET* la clave
principal de una versión y devuelva al navegador el archivo correspondiente.

Esta acción presenta una particularidad; la respuesta que el servidor *web* debe
enviar al cliente no es un documento *HTML*. Actualmente este hecho no debe 
sorprendernos ya que en el mundo de las aplicaciones *web* cada vez es más 
frecuente que el servidor *web* envíe a los clientes otro tipo de respuestas,
especialmente archivos *XML* que son interpretados por elementos *javascript* en
la parte cliente cuando se utilizan interfaces de usuario enriquecidas.

La acción que vamos a implementar ahora funcionará de la siguiente manera: en 
primer lugar se recoge la clave principal de la versión que se ha solicitado, 
se recupera el registro de la tabla versiones correspondiente a través de un 
objeto de *Propel* y se usa la información del mismo para localizar en el sistema 
de archivos el fichero solicitado y enviarlo al cliente en la respuesta. Esta
acción, por tanto, no tendrá ninguna plantilla asociada.

Añadimos la acción al módulo implementando el método *executeVerVersion()*
según el siguiente código:

*Código de la acción verVersion del archivo:
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executeVerVersion(sfWebRequest $request)
	 {
		$this ->forward404Unless($request -> hasParameter('id_version'));
	
		$idVersion = $request -> getParameter('id_version');
		$oVersion = VersionesPeer::retrieveByPK($idVersion);
	
		$fichero = sfConfig::get('sf_upload_dir').'/usuario_'.$oVersion -> getDocumentos() -> getIdUsuario().'/'.$oVersion -> getNombreFichero();
	
		$ctype = $oVersion -> getDocumentos() -> getTipos() -> getTipoMime();
		$path = pathinfo($oVersion -> getNombreFichero());
		$nombreFichero = $oVersion -> getDocumentos() -> getTitulo().'_ver-'.$oVersion -> getNumero().'.'.$path['extension'];
			
		header("Pragma: public"); // required
		header("Expires: 0");
		header("Cache-Control: must-revalidate, post-check=0, pre-check=0");
		header("Cache-Control: private",false); // required for certain browsers
		header("Content-Type: $ctype");
		
		header("Content-Disposition: attachment;
				filename=\"".basename($nombreFichero)."\";" );
		header("Content-Transfer-Encoding: binary");
		header("Content-Length: ".filesize($fichero));
			
		if(file_exists($fichero))
		{
			echo readfile($fichero);
		}
		else
		{
			echo 'No se puede descargar el '. $fichero . '. Comuníquenlo al responsable de la aplicación.' ;
	
		}
	
		 return sfView::NONE;
	}


El fichero es enviado directamente utilizando la función *header()* de *PHP*, la
cual envía una cabecera *HTTP* pura en la que se indica que la respuesta consiste
en un fichero adjunto con un determinado tipo *MIME*, que es el se asoció al 
documento en la base de datos cuando se subió al servidor. 

Para enviar el fichero mediante esta función necesitamos las siguientes variables:

=================== =============================================================
Variable            Significado
=================== =============================================================

``$fichero``        Es la ruta absoluta del fichero que se va a descargar. Sirve 
                    para leer el contenido del fichero en cuestión y enviarlo 
                    mediante la instrucción ``echo readfile($fichero)``
               
``$ctype``          Es el tipo *MIME* del fichero que se va a descargar.

``$path``           Es un *array* devuelto por la función *pathinfo()* que nos
                    permitirá extraer la extensión del fichero en cuestión para
                    utilizarla en el nombre del fichero que el cliente 
                    interpretará.
               
``$nombreFichero``  Es el nombre que le asignaremos al contenido que estamos 
                    enviando en la respuesta, de manera que cuando llegue al 
                    cliente será este el nombre que tendrá el contenido que se ha
                    enviado. Observa que una cosa es el contenido del fichero y 
                    otra distinta el nombre que tiene. De hecho en el servidor el
                    archivo tiene un nombre que se originó automáticamente en el 
                    proceso de subida al servidor, y cuando lo enviamos al 
                    cliente lo cambiamos por *$nombreFichero*, que es más 
                    descriptivo y que hemos compuesto uniendo el título que le 
                    dimos al documento en la base de datos junto con el nº de 
                    versión que se descarga.


Por último debemos indicar a *symfony* que la acción no debe ser renderizada por
ninguna plantilla. Este es el significado de la última línea:

.. code-block:: bash

	return sfView::NONE;

Ahora ya deben funcionar los enlaces de la columna versiones del listado de 
documentos. Compruébalo y observa como se lleva a cabo la descarga del documento 
sin que se produzca un cambio de la interfaz de usuario (página *web*) con el 
listado.


**Mostrar los metadatos de un documento.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En el listado de documentos no hemos incluido ninguna operación (enlace) con la
intención de mostrar los metadatos de cada documento. Una posibilidad inmediata 
sería incluir en la columna de acciones la nueva acción que precisamos. Si 
embargo, para no sobrecargar demasiado dicha columna propondremos una solución
alternativa; se trata de convertir el título de cada uno de los documentos en un 
enlace que dispare la acción que muestra los metadatos de un documento. 
Llamaremos a esta acción *verMetadatos()*, y utilizaremos el *helper link_to()*
para cambiar los elementos de la columna 'Titulo' por enlaces a dicha acción. 

Cambiamos en la plantilla *indexSuccess.php* la línea:

.. code-block:: bash

	 <td><?php echo $d -> getTitulo() ?></td>

Por esta otra:

.. code-block:: bash

 	<td><?php echo link_to($d -> getTitulo(),'gesdoc/verMetadatos?id_documento='. $d -> getIdDocumento()) ?></td>

Y ahora escribimos la acción *executeVerMetadatos()*:

*Acción verMetadatos del archivo: 
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash
	
	public function executeVerMetadatos(sfWebRequest $request)
	{
		$this -> forward404Unless($request -> hasParameter('id_documento'));
		$id_documento = $request -> getParameter('id_documento');
	
		$this -> documento = DocumentosPeer::retrieveByPK($id_documento);
	}

Y creamos el fichero *verMetadatosSuccess.php* donde implementamos la plantilla
que mostrará los atributos del objeto ``$this → documento``:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/verMetadatosSuccess.php*

.. code-block:: html+jinja

	<div id="sf_admin_container">
		<h1>Metadatos del documento: <i><?php echo $documento -> getTitulo() ?></i></h1>
	
		<div id="sf_admin_content">
			<ul class="sf_admin_actions">
				<li><?php echo link_to('volver','gesdoc/index') ?></li>
			</ul>
			<div class="sf_admin_form">
				<form>
					<fieldset id="fieldset_1">
						<h2>Metadatos</h2>
						<div class="sf_admin_form_row">
							<div>
								<label>Título:</label>
								<?php echo $documento -> getTitulo() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<div>
								<label>Descripción:</label>
								<?php echo $documento -> getDescripcion() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<div>
								<label>Tipo:</label>
								<?php echo $documento -> getTipos() -> getNombre() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<div>
								<label>Público:</label>
								<?php echo ($documento -> getPublico() == 1)? 'Si' : 'No' ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<div>
								<label>Autor:</label>
								<?php echo $documento -> getUsuarios() ?>
							</div>
						</div>
				</form>
			</div>
		</div>
	
		<div id="sf_admin_footer">
		</div>
	</div>

En cuya confección se ha tenido en cuenta la aplicación de los estilos de las
*CSS's* que estamos utilizando.

Ya puedes probar la nueva funcionalidad. Observa que si realizas una búsqueda y, 
a continuación, miras los metadatos de algún documento, cuando usas el enlace 
*'volver'* para mostrar de nuevo el listado de documentos, se pierde la búsqueda
que realizaste. Se trata del mismos problema que ya hemos comentado con los 
enlaces del paginado. Lo solucionaremos en la próxima unidad.


**Conclusión**
--------------------

Hemos comenzado el desarrollo de nuestro gestor documental implementando el 
listado de documentos disponibles. Para ello hemos partido de una serie de datos
de ejemplo introducidos en la base de datos “a pelo”, ya que nuestra aplicación
aún no permite la subida de archivos. También se ha construido un formulario para
realizar búsquedas de documentos que será muy útil cuando el número de estos sea
muy alto. Además hemos incorporados la posibilidad de descargar las versiones
asociadas a los documento y una pantalla para mostrar los metadatos de los mismos.

Para realizar todo esto hemos necesitado conocer con suficiente profundidad la
capa de abstracción de base de datos que proporciona *Propel*. Por eso la primera 
parte de la unidad se ha dedicado a un estudio teórico, aunque centrado en lo
operativo, sobre el *ORM Propel*. Aún queda bastante por hacer pero la aplicación
ya tiene “cuerpo” y “presencia” y satisface casi al completo los requisitos D07
y D08 del análisis planteado en la unidad 4. Es cierto que queda por resolver la
pérdida del filtro cuando cambiamos de acción a través del paginado o cuando
volvemos al listado desde la pantalla de metadatos, pero quédate tranquilo pues
volveremos a este punto y lo resolveremos  en la próxima unidad que trata el tema
de la sesión de usuario en el servidor.

Como siempre te aconsejamos que una vez finalizado el tema lo vuelvas a estudiar 
con más profundidad “toqueteando” el código desarrollado y reflexionando sobre
los resultados obtenidos. 











