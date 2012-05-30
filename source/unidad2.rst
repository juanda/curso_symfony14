**Unidad 2. Desarrollo de una aplicación siguiendo el patrón *MVC*.**
=====================================================================

En esta unidad desarrollaremos una sencilla aplicación que realiza consultas 
sobre una base de datos compuesta por una tabla que contiene la siguiente 
información sobre alimentos:

* El nombre del alimento,

* la energía en kilocalorías ,

* la cantidad de proteínas,

* la cantidad hidratos de carbono  en gramos

* la cantidad de fibra en gramos  y 

* la cantidad de grasa en gramos,

todo ello por cada 100 gramos de alimento.

La aplicación permitirá:

1. Insertar nuevos alimentos.

2. Realizar consultas por el nombre del alimento, por la energía y combinando 
   campos.

Así mismo contará con un menú accesible desde cualquier parte de la aplicación.

Aunque se trata de una aplicación muy sencilla, cuenta con los elementos 
suficientes para trabajar el aspecto que realmente pretendemos estudiar en esta 
unidad: la organización del código siguiendo las directrices del patrón 
*Modelo – Vista – Controlador*. Comprobaremos como esta estrategia nos ayuda a 
mejorar las posibilidades de crecimiento (escalabilidad) y el mantenimiento de
las aplicaciones que desarrollamos. 

Construiremos el ejemplo procurando en todo momento alcanzar las características
deseables en toda aplicación *web*, y que fueron explicadas en la primera unidad.

.. note::
   Aunque los patrones de diseño son pensados en términos de colaboración de 
   objetos, no utilizaremos las funcionalidades propias de la *POO* que ofrece 
   *PHP* en el desarrollo del ejemplo de esta unidad. De esa manera no 
   complicaremos más el ejemplo y podremos centrarnos exclusivamente en los 
   aspectos relativos a la organización del código.  


**Descripción general**
_______________________

El acceso a una aplicación *web* se hace mediante un software denominado 
navegador al que se le indica la dirección única del recurso correspondiente al
punto de entrada de la aplicación. Dicha dirección se denomina *URL* y contiene
información tanto de la ubicación en la red del servidor como del recurso que se
desea obtener. 

Las aplicaciones *web* que siguen el patrón *MVC* se construyen de manera que el 
acceso a cualquiera de sus funcionalidades se realiza a través de un único 
fichero denominado **controlador frontal**, el cual se encarga de controlar el
flujo básico de la misma según el valor que tome un parámetro denominado 
**acción**. Como es típico en las aplicaciones *web* construidas en *PHP*, 
nombraremos a dicho fichero *index.php*. 

Las distintas funcionalidades de la aplicación, como insertar un registro o 
realizar una consulta, son implementadas en ficheros que denominaremos 
**acciones** y cuyo código es ejecutado por el *controlador frontal* en función
de lo que se indique en el parámetro acción que enviamos mediante la una petición
*HTTP GET*2. Cada acción se encarga de construir, utilizando las funciones de 
librería (lo que en el contexto del *MVC* se denomina **modelo**) los datos que
deseamos mostrar al usuario en la denominada **vista**.

Es decir, el patrón *MVC* consiste básicamente en organizar el código de la 
aplicación en tres componentes débilmente acoplados y que se comunican mediante
una interfaz bien definida:

* El controlador, que se encargará de dirigir el flujo de la aplicación,

* El modelo, encargado de representar la lógica de negocio de la aplicación y

* La vista, cuya finalidad es representar en algún formato conocido los datos 
  ofrecidos por el modelo a petición del controlador.

La siguiente figura muestra esquemáticamente este patrón de diseño en la versión
de *MVC* que utilizamos en este curso, que obviamente es la que utiliza *symfony*.
Observa como el controlador se comunica en ambos sentidos con el modelo, es decir
puede pedir y enviar datos, mientras que sólo puede enviar datos a la vista. Es
decir, la vista actúa exclusivamente como receptor de datos. En efecto la vista 
tan solo debe ofrecer representaciones de los datos que le son enviados. Así 
mismo el modelo no se comunica directamente con la vista en ningún sentido, ya
que es el controlador el componente encargado de coordinar a los otros dos.

Como veremos a lo largo del curso, el desacoplo logrado por este patrón incrementa
enormemente la flexibilidad y la reutilización. Por ejemplo, sin modificar 
absolutamente nada del modelo, podemos representarlo de tantas maneras como 
queramos sin más que modificar la vista. 

Siguiendo el patrón *MVC* en la aplicación que desarrollaremos en esta unidad, 
incluiremos en el modelo exclusivamente las funciones que manipulen información
sobre alimentos, es decir, lo que en términos más técnicos se conoce como *lógica
del negocio*. En el modelo no nos preocupamos por otros aspectos de la aplicación
tales como la conexión con la base de datos, la interpretación de la *URL*, el
flujo de navegación, y demás elementos que constituyen el marco común 
(*framework*) donde se ejecutan las acciones, pero que no inciden sobre el asunto 
central de la misma, a saber: *“ofrecer y manipular información almacenada en una 
base de datos de alimentos”*. 

Por otro lado, la vista estará compuesta por ficheros *PHP* denominados 
**plantillas**, las cuales serán confeccionadas de manera que revelen con 
claridad la estructura *HTML* del documento que se envía a los clientes. De hecho,
a pesar de que son archivos *PHP*, están más “cercanos” en su aspecto al *HTML*,
y podemos decir, abusando del lenguaje, que son “ficheros *HTML* dinámicos”, en 
el sentido de que hay ciertos datos que se presentan parametrizados (variables 
*PHP*) y que son calculados durante la ejecución de la acción y sustituidos por 
su valor durante la ejecución de la plantilla, dando lugar finalmente al documento
*HTML* (esta vez sí *HTML* de verdad) que el servidor *web* (*apache*) envía al 
cliente.

Por ultimo, el control de la aplicación se realizará mediante la combinación de 
un fichero único de entrada a la misma (el controlador frontal) y una serie de 
ficheros de acciones que, mediante el uso coordinado del modelo y la vista, 
implementan cada una de las funcionalidades de la aplicación.


**Construcción de la aplicación**
----------------------------------

En primer lugar debemos crear la base de datos donde almacenaremos los datos de
los alimentos. La denominaremos con el nombre *alimentos* y contendrá una sola 
tabla que también se llamara *alimentos*. El siguiente código muestra la 
instrucción *SQL* que genera dicha tabla:

.. code-block:: bash

	CREATE TABLE `alimentos` (
	  `id` int(11) NOT NULL AUTO_INCREMENT,
	  `nombre` varchar(255) NOT NULL,
	  `energia` decimal(10,0) NOT NULL,
	  `proteina` decimal(10,0) NOT NULL,
	  `hidratocarbono` decimal(10,0) NOT NULL,
	  `fibra` decimal(10,0) NOT NULL,
	  `grasatotal` decimal(10,0) NOT NULL,
	  PRIMARY KEY (`id`)
	) ENGINE=InnoDB  DEFAULT CHARSET=utf8;

Inserta algunos datos de ejemplo para que puedas comprobar el funcionamiento de
la aplicación a medida que la desarrollas.

Pongamos en práctica las directrices marcadas en el apartado anterior. En primer 
lugar creamos un directorio, denominado *unidad2*, en el directorio raíz 
(*document root*) del servidor *web*.

.. code-block:: bash

    $ mkdir /opt/lamp/htdocs/unidad2

Y será ahí donde despleguemos todos los archivos de nuestra aplicación.

A continuación vamos a crear los directorios *modelo*, *vista* y *controlador*,
que hacen alusión a los tres elementos del patrón *MCV*. De manera que cada 
fichero que implementemos se almacene en el directorio que le corresponda según
la función que realice en la aplicación. 

También crearemos un directorio denominado *web* el cual, al menos en un entorno
de producción real, debe ser el único al que se pueda acceder a través de una
petición *HTTP*. Es decir, este directorio será el directorio raíz (*document root*)i
del *host* que sirva la aplicación. De esta manera protegemos el código 
fuente de la aplicación, ya que los archivos que no se alojen bajo el directorio
*web* no son recursos accesibles al servidor *web*.

.. code-block:: bash

   $ cd /opt/lamp/htdocs/unidad2
   $ mkdir modelo vista acciones web


**El controlador frontal.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^

La responsabilidad del controlador frontal será:

1. Cargar la configuración de la aplicación.

2. Cargar las librerías que utiliza la aplicación.

3. Realizar la conexión con la base de datos.

4. Interpretar la acción que el usuario ha indicado en la *URL* así como los 
   parámetros requeridos por la misma.

5. Cargar y ejecutar la acción indicada.

6. Mostrar los datos calculados en la acción mediante las plantillas 
   confeccionadas para tal fin.

Como ya hemos dicho anteriormente, el controlador frontal es el punto de acceso
único de la aplicación. Por tanto debe ubicarse en un directorio accesible por 
el servidor *web*. Al archivo que implementa el controlador frontal, como es 
tradición en el mundo de *PHP*, lo denominaremos *index.php*, y lo ubicaremos 
colgando directamente del directorio *web*.

.. code-block:: php

	<?php

	include('../configuracion.php');

	if(file_exists('../modelo/modelo.php'))
	{
	include('../modelo/modelo.php');
	}
	// Conexion con la base de datos

	$mvc_bd_conexion = mysql_connect($mvc_bd_hostname, $mvc_bd_usuario, $mvc_bd_clave);
	if (!$mvc_bd_conexion)
	{
	die('No ha sido posible realizar la conexión con la base de datos: ' . mysql_error());
	}
	mysql_select_db($mvc_bd_nombre, $mvc_bd_conexion);


	// Fin conexion con la base de datos


	// Seleccion de la accion (parametros get)

	if(!isset($_GET['accion']))
	{
	$mvc_ctl_accion = 'inicio';
	}
	else
	{
	$mvc_ctl_accion = $_GET['accion'];
	}

	// Procesamiento de la acción

	if(file_exists('../acciones/'.$mvc_ctl_accion.'Accion.php'))
	{
	include('../acciones/'.$mvc_ctl_accion.'Accion.php');
	}
	else
	{
	exit ('No exite la accion "'.$mvc_ctl_accion.'"');
	}

	// Fin del procesamiento de la acción

	// Presentación de la vista

	if(file_exists('../vista/plantillas/'.$mvc_vis_plantilla.'Plantilla.php'))
	{
	include('../vista/layout.php');
	}
	else
	{
	exit ('No existe la plantilla "'.$mvc_vis_plantilla.'"');
	}
	// Fin de la presentación de la vista

	?>

Hemos adoptado el convenio de identificar a las variables que tienen significado
dentro del *framework MVC* con el prefijo ``mvc_.`` En el código anterior aparecen
algunas de ellas, y en el siguiente cuadro se muestran todas las que utilizaremos
en el desarrollo de la aplicación.

+-------------------------+----------------------------------------------------+
|    Variable             |  Significado                                       |
+==============================================================================+
|``$mvc_bd_conexion``     | Recurso PHP de conexión a la base de datos         |
+-------------------------+----------------------------------------------------+
|``$mvc_bd_hostname``     |Nombre del servidor donde reside la  base de datos  |
+-------------------------+----------------------------------------------------+
|``$mvc_bd_nombre``       |Nombre de la base de datos                          |
+-------------------------+----------------------------------------------------+
|``$mvc_bd_usuario``      |Usuario con privilegios suficientes para acceder a  |
|                         |la base de datos                                    |
+-------------------------+----------------------------------------------------+
|``$mvc_bd_clave``        |Clave del usuario anterior                          |
+-------------------------+----------------------------------------------------+
|``$mvc_ctl_accion``      |Acción que debe ser ejecutada a petición del cliente|
+-------------------------+----------------------------------------------------+
|``$mvc_vis_plantilla``   |Plantilla con la que se deben mostrar los datos     |
|                         |calculados por la acción y solicitados por el       |
|                         |cliente.                                            |
+-------------------------+----------------------------------------------------+
|``$mvc_vis_css``         |La css que se aplicará para visualizar los          |
|                         |documentos HTML.                                    |
+-------------------------+----------------------------------------------------+


**Las acciones**
^^^^^^^^^^^^^^^^

Las acciones, que forman parte del controlador, serán implementadas en ficheros
*PHP* que serán nombrados según el siguiente patrón:

.. code-block:: bash

	{nombre_accion}Accion.php

De esa manera sabremos por el nombre del fichero, que pertenece al componente 
controlador del patrón *MVC*. Ubicaremos todas las acciones en el directorio 
denominado ``acciones``.

Vamos a implementar la acción de inicio, que denominaremos, según lo especificado,
``inicioAccion.php``: 

.. code-block:: php

	<?php

	$param_mensaje = 'Bienvenido a la primera aplicación del curso <i>"desarrollo de aplicaciones web con symfony</i>"';
	$param_fecha = date('d - M - Y');

	// definicion de la vista

	$mvc_vis_plantilla = 'inicio';

	?>

En la acción hemos definido unas variables que comienzan con el prefijo 
``$param_`` que, por convenio, van a representar los parámetros que serán 
visualizados en la vista. Es responsabilidad de la acción calcular tales 
parámetros para entregárselos a la vista. En la acción que acabamos de mostrar, 
el cálculo de estos parámetros es muy sencillo; ``$param_mensaje`` es una cadena
de texto que definimos directamente, y ``$param_fecha`` se calcula usando la 
función ``date`` de *PHP*. No obstante, como veremos en otras acciones, el 
cálculo de los parámetros puede ser más complejo y hacer uso de consultas a la 
base de datos o utilizar algoritmos más elaborados para ofrecer la información 
solicitada. Por lo general, los detalles de dicho cálculo son llevados a cabo por 
los archivos que constituyen el modelo de la aplicación.


**Las plantillas**
^^^^^^^^^^^^^^^^^^

Como en cualquier aplicación *web*, el resultado de todo el proceso debe ser un 
archivo con algún formato interpretable por el cliente que haya realizado la 
petición. En el caso que nos ocupa se trata de un archivo *HTML*. No obstante 
también se podrían generar salidas en formato *XML*, *Json* o cualquier otro. 
Ello dependerá de para qué haya sido diseñada la aplicación. Así por ejemplo 
podemos pensar en un cliente lector de *RSS*, que espera un archivo *XML* (*RDF*, 
*RSS2* o *Atom*) o en un cliente que hace uso de *Web Services*, cuyas salidas 
suelen ser formateadas en un sublenguaje *XML* que el lector *RSS* comprende.

Por tanto el próximo paso es construir el archivo *HTML* que será enviado al 
navegador con los datos calculados en la acción. Para llevar a cabo la tarea 
debemos pensar en el requisito que exigía a la aplicación a mostrar un menú de 
navegación en todas sus vistas, así como en el principio de programación *DRY*
(Don't Repeat Yourself). El problema será resuelto mediante la descomposición de 
la vista en dos partes: 

* *layout*, es una plantilla que representa todo el marco común a la aplicación 
  como es la cabecera con el menú y el pie de página. La ubicaremos en el
  directorio ``vista`` y se denominará ``layout.php``

* *plantilla*, que representa la visualización de la acción que está siendo 
  ejecutada. Las ubicaremos en un directorio llamado ``plantillas`` que cuelgue 
  de la carpeta ``vista`` y utilizaremos el siguiente convenio para nombrarlas:  
  ``{nombre_plantilla}Plantilla.php``

La siguiente figura muestra gráficamente esta idea:


El siguiente código corresponde al archivo ``layout.php``:

.. code-block:: php 

	<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
	<html>
		<head>
			<title>Información Alimentos</title>
			<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
			<link rel="stylesheet" type="text/css" href="<?php echo 'css/'.$mvc_vis_css ?>" />
	
		</head>
		<body>
			<div id="cabecera">
				<h1>Información de alimentos</h1>
			</div>
	
			<div id="menu">
				<hr/>
				<a href="index.php?accion=inicio">inicio</a> |
				<a href="index.php?accion=insertarAlimento">insertar alimento</a> |
				<a href="index.php?accion=buscarAlimentosPorNombre">buscar por nombre</a> |
				<a href="index.php?accion=buscarAlimentosPorEnergia">buscar por energia</a> |
				<a href="index.php?accion=buscarAlimentosCombinada">búsqueda combinada</a>
				<hr/>
			</div>
	
			<div id="contenido">
				<?php include('plantillas/'.$mvc_vis_plantilla.'Plantilla.php') ?>
			</div>
	
			<div id="pie">
				<hr/>
				<div align="center">- pie de página -</div>
			</div>
		</body>
	</html>

Observemos la estructura *HTML* del archivo y la manera de especificar los 
contenidos dinámicos mediante etiquetas ``<?php ?>``. Esta manera de construir 
los archivos de la vista permite expresar explícitamente la estructura *HTML* y
puede ser comprendida por diseñadores *web* con un mínimo de conocimientos de 
*PHP*. De manera que los trabajo de programación y de maquetación pueden ser 
realizados por distintas personas con perfiles de programador y de maquetador 
respectivamente. Ello es posible gracias a que se ha separado completamente la 
lógica de negocio de la visualización gracias a la aplicación del patrón *MVC*. 
Realizar futuros cambios en la presentación será inmediato; tan solo hay que 
identificar la plantilla que es responsable de la parte que deseamos cambiar y 
modificarla en consecuencia. 

En el código anterior se aprecian dos contenidos dinámicos resaltados en negrita.

El primero de ellos, ``**<?php echo 'css/'.$mvc_vis_css ?>**``, una vez 
interpretado arrojará la ruta de un archivo ``css``. La variable ``$mvc_vis_css``
se establece en un fichero de configuración y nos permitirá cambiar de *css* sin
más que alterar dicha variable en el archivo de configuración correspondiente.

El segundo, ``**<?php include('plantillas/'.$mvc_vis_plantilla.'Plantilla.php') ?>**``,
incluye un archivo que, una vez interpretado por *PHP*, es la plantilla 
correspondiente a la acción que ha sido solicitada por el cliente. El valor de la
variable ``$mvc_vis_plantilla`` es definido en la acción que se está ejecutando.
En el caso de la acción *inicio* este valor es (consultar el código de la acción) 

.. code-block:: bash

	$mvc_vis_plantilla = 'inicio';

Por lo que la plantilla que mostrará los datos será ``inicioPlantilla.php``. 
Es decir, es la acción en cuestión quien decide con qué plantilla se representarán
los datos que ha calculado. Obviamente dicha acción debe proporcionar a la 
plantilla todos los parámetros que esta necesite para que el resultado se genere 
sin errores. 

También es importante comprobar como el menú (ubicado en la capa ``menu``) no es
más que un conjunto de enlaces al controlador frontal con distintas acciones que 
desarrollaremos a lo largo de la unidad.

Creamos ahora el directorio para las plantillas,

.. code-block:: bash

	mkdir vista/plantillas

Y codificamos la plantilla ``inicioPlantilla.php``:

.. code-block:: bash

	<h3> Fecha: <?php echo $param_fecha ?> </h3>
	<?php echo $param_mensaje ?>

De nuevo podemos ver la estructura *HTML* explícita del fichero con contenidos 
dinámicos que provienen de la acción ``inicioAccion.php: $param_fecha`` y 
``$param_mensaje``.

Por último, como las *CSS's* son recursos que a los que se debe acceder 
directamente el servidor *web*, hemos determinado ubicar los ficheros *CSS's* en
un directorio denominado css que cuelga del directorio *web*:

.. code-block:: bash

	mkdir web/css

y en él creamos en él el archivo *estilo.css* que se muestra a continuación:

.. code-block:: bash

	body {
	  padding-left: 11em;
	  font-family: Georgia, "Times New Roman",
			Times, serif;
	  color: purple;
	  background-color: #d8da3d }
	ul.navbar {
	  list-style-type: none;
	  padding: 0;
	  margin: 0;
	  position: absolute;
	  top: 2em;
	  left: 1em;
	  width: 9em }
	h1 {
	  font-family: Helvetica, Geneva, Arial,
			SunSans-Regular, sans-serif }
	ul.navbar li {
	  background: white;
	  margin: 0.5em 0;
	  padding: 0.3em;
	  border-right: 1em solid black }
	ul.navbar a {
	  text-decoration: none }
	a:link {
	  color: blue }
	a:visited {
	  color: purple }
	address {
	  margin-top: 1em;
	  padding-top: 1em;
	  border-top: thin dotted }
	#contenido {
	  display: block;
	  margin: auto;
	  width: auto;
	  min-height:400px;
	}


**La configuración**
^^^^^^^^^^^^^^^^^^^^

Para que la primera acción de nuestra aplicación pueda ejecutarse correctamente 
tan sólo nos queda definir el fichero de configuración donde especificaremos los
parámetros de conexión a la base de datos y la *CSS* que decorará la aplicación. 
Vamos a colocar dicho fichero en el directorio raíz de la aplicación y lo 
llamaremos *configuracion.php*.

.. code-block:: php

	<?php
	
	$mvc_bd_hostname   = "localhost";
	$mvc_bd_nombre     = "alimentos";
	$mvc_bd_usuario    = "root";
	$mvc_bd_clave      = "root";
	
	$mvc_vis_css       = "estilo.css";
	
	?>

Con esto tenemos todo lo necesario para ejecutar con éxito la primera acción de
nuestra aplicación. Aún no hemos implementado nada en la parte del modelo, pero 
es que esta primera acción es lo más simple que se despacha en acciones. Aún así,
su programación ha mostrado las características fundamentales del patrón *MVC*.

Comprueba su funcionamiento solicitando el siguiente documento en tu navegador:

.. code-block:: bash

	http://localhost/unidad2/web/index.php?accion=inicio,


también debe funcionar con esta otra:

.. code-block:: bash

	http://localhost/unidad2/web/index.php


O esta otra:

.. code-block:: bash

	http://localhost/unidad2/web/1


Ya que el controlador frontal carga la acción *inicio* en caso de que no exista 
el parámetro *accion*.

Introduciremos el modelo cuando implementemos el resto de las acciones requeridas.
Pero antes vamos a repasar el funcionamiento conjunto de todas las partes que 
hemos desarrollado hasta el momento.

Estructura de directorio:

+----------------------------+-------------------------------------------------+
|**Archivo**                 |**Descripción**                                  |
+----------------------------+-------------------------------------------------+
|/                           |Raíz de la aplicación                            |
+----------------------------+-------------------------------------------------+
||- aciones                  |Directorio para las acciones del controlador     |
+----------------------------+-------------------------------------------------+
||  `- inicioAccion.php      |Plantilla de la acción *inicio*                  |
+----------------------------+-------------------------------------------------+
||- modelo                   |Directorio para los archivos del modelo          |
+----------------------------+-------------------------------------------------+
||- vista                    |Directorio para los archivos de la vista         |
+----------------------------+-------------------------------------------------+
||  |- plantillas            |Directorio para las plantillas de las acciones   |
+----------------------------+-------------------------------------------------+
||  |  `- inicioPlantilla.php|Plantilla de la acción *inicio*                  |
+----------------------------+-------------------------------------------------+
||  `- layout.php            |Layout de la aplicación (cabecera, menú y pie de |
|                            |página)                                          |
+----------------------------+-------------------------------------------------+
||- web                      |Directorio accesible al servidor web             |
+----------------------------+-------------------------------------------------+
||  |- css                   |Directorio donde se colocarán las CSS's          |
+----------------------------+-------------------------------------------------+
||  |  `- estilo.css         |Hoja de estilo CSS.                              |
+----------------------------+-------------------------------------------------+
||  `- index.php             |Controlador frontal de la aplicación             |
+----------------------------+-------------------------------------------------+
|`- configuracion.php        |Archivo de configuración de la aplicación        |
+----------------------------+-------------------------------------------------+

**Funcionamiento:**

El cliente solicita la ejecución de la acción *inicio* a través de la *URL*:

.. code-block:: bash

	http://localhost/unidad2/web/index.php?accion=inicio


Se ejecuta entonces el controlador frontal de la aplicación que 

1. Carga el fichero de configuración.

2. Carga las librerías del modelo (por lo pronto vacías).

3. Realiza la conexión a la base de datos con los parámetros de configuración 
   ``$mvc_hostame_bd, $mvc_nombre_bd, $mvc_usuario_bd`` y ``$mvc_clave_bd``.

4. Comprueba la acción que se ha solicitado en la petición, si no se ha 
   solicitado ninguna se ejecutará la acción *inicio*.

5. Incluye la acción *inicio* (archivo ``inicioAccion.php``) en la que se 
   establecen dos parámetros para ser mostrado por la vista: ``$param_mensaje``
   y ``$param_fecha``. Además establece ``inicioPlantilla.php`` como plantilla 
   para mostrar los datos. Esto último se establece definiendo el valor de la 
   variable ``$mvc_vis_plantilla = 'inicio'``.

6. Incluye el *layout* de la aplicación (archivo ``layout.php``) el cual 
   dibujará la cabecera, el menú, el pie de página y la plantilla especificada 
   en la variable ``$mvc_vis_plantilla``.

7. Finalmente se juntan todas las partes y se obtiene el documento *HTML* que
   se enviará al navegador cliente.

A falta del modelo, ya tenemos el mini-*framework MVC* desarrollado. Para añadir
funcionalidades a la aplicación siempre debemos proceder de la misma forma:

1. Implementamos un archivo de acción en el directorio acciones que se denomine 
   ``{nombre_accion}Accion.php.`` En él definimos y calculamos, haciendo uso de 
   librerías del modelo si es necesario, los parámetros que deseamos mostrar 
   posteriormente en la vista. Convenimos, por cuestiones de organización, no de 
   funcionalidad, que estos parámetros sean variables *PHP* que comiencen con el
   prefijo ``$param_``. Además la acción finalizará definiendo el valor de la 
   variable ``$mvc_vis_plantilla``, que será el nombre de la plantilla sin el
   sufijo ``Plantilla``.

2. Implementamos la plantilla ``{nombre_plantilla}Plantilla.php`` donde 
   definiremos la estructura *HTML* que será insertada en el *layout*. Los datos 
   dinámicos, que son los parámetros definidos en la acción, se presentarán 
   mediante las etiquetas ``<?php echo $param_{nombre_parametro} ?>``.

3. Si es necesario, se implementan las  nuevas funciones de librerías en el 
   modelo para dar servicio a la acción.

4. Ya podemos ejecutar la acción desde el navegador cliente mediante la url: 
   ``http://localhost/unidad2/web/index.php?accion=nombre_accion[&param1=p1&param2=p2...]``. 

Y siempre procedemos de igual manera. Esta organización del código, derivada de 
la aplicación del patrón *MVC*, ha definido un procedimiento homogéneo para 
añadir funcionalidad a la aplicación separando el código en partes con 
responsabilidades bien definidas. Obviamente hemos ganado en mantenibilidad y 
escalabilidad de la aplicación.


**El modelo**
^^^^^^^^^^^^^^

Para ilustrar la utilidad del modelo, vamos a implementar una nueva funcionalidad
a la aplicación. El próximo ejercicio incorporará un formulario de búsqueda de 
alimentos por nombre. El resultado de la búsqueda será mostrado por orden 
descendente de kilocalorías por cada 100g de alimento.

La secuencia lógica será: 

1. El cliente pide el formulario de búsqueda.

2. El servidor se lo envía

3. El usuario rellena el formulario y lo envía al servidor.

4. El servidor procesa los datos y envía el resultado al cliente.

Esta funcionalidad se puede implementar fácilmente en nuestro mini-*framework MVC*
mediante dos acciones; una para enviar el formulario, y otra para procesar los 
datos devueltos y enviar el resultado.

**Implementación del formulario.**

El formulario de búsqueda será una plantilla que denominaremos 
*formBusquedaPorNombrePlantilla.php*, y cuyo código es el siguiente:

.. code-block:: bash

	<form name="formBusqueda" action="index.php?accion=procesarFormBusquedaPorNombre" method="POST">
	
		<table>
			<tr>
				<td>alimento:</td>
				<td><input type="text" name="nombre"></td>
				<td><input type="submit" value="buscar"></td>
			</tr>
		</table>
		
		</table>
		
	</form>


Se ha destacado en negrita la acción que dispará el formulario cuando es enviado.
Observa que se trata de la acción correspondiente al punto nº 4 de la secuencia 
lógica que acabamos de esbozar.

Para que esta platilla pueda “pintarse”, debe ser llamada por una acción que 
denominaremos *buscarAlimentosPorNombre*, y que de acuerdo a las normas de 
nuestro framework *MVC* será implementada en un archivo de nombre  
*buscarAlimentosPorNombreAccion.php.*

.. code-block:: php

	<?php
	
	$mvc_vis_plantilla = 'formBusquedaPorNombre';
	
	?>


Lo único que hace esta acción es definir el nombre de la plantilla que dibujará 
el formulario de búsqueda.

Estos dos archivos cubren los puntos 1 y 2. Ahora ya puedes probarlo accediendo 
a la aplicación mediante la siguiente *URL*:

.. code-block:: bash

	http://localhost/unidad2/web/index.php?accion=buscarAlimentosPorNombre

Observa que esta misma acción es la que se especifica en uno de los elementos 
del menú de la aplicación definido en el *layout*. De hecho la forma correcta de
acceder es a través de dicho enlace del menú.

Ahora desarrollaremos la parte del proceso del formulario.

Implementamos una acción denominada *procesarFormBusquedaPorNombre*, en un
archivo cuyo nombre debe ser *procesarFormBusquedaPorNombreAccion.php.* 
El nombre de la acción debe corresponderse con la que se indica en el formulario
de búsqueda, y su código será:

.. code-block:: php

	<?php
	
	$param_alimentos = array();
	
	$param_alimentos = buscarAlimentosPorNombre($_POST['nombre'], $mvc_bd_conexion);
	
	$mvc_vis_plantilla = "mostrarAlimentos";
	
	?>

Y es aquí donde hacemos uso de una función de librería, *buscarAlimentosPorNombre*,
que debe ser implementada dentro del archivo *modelo.php*. El código se muestra 
a continuación:

.. code-block:: bash

	function buscarAlimentosPorNombre($nombre, $conexion)
	{
		$sql = "select * from alimentos where nombre like '".$nombre."' order by energia desc";
	
		$result = mysql_query($sql, $conexion);
	
		$alimentos = array();
		while ($row = mysql_fetch_assoc($result))
		{
			$alimentos[] = $row;
		}
	
		return $alimentos;
	}


Esta función es propia de la lógica de negocio de la aplicación, y realiza una 
consulta a la base de datos para obtener los alimentos cuyos nombres coincidan 
con el patrón que se le pasa en su primer argumento, obtenido a su vez del 
parámetro ``nombre`` enviado por el formulario de búsqueda mediante una petición 
*HTTP POST*. La función devuelve un *array* con los alimentos encontrados.

El *array* es almacenado en la variable ``$param_alimentos`` de la acción que 
procesa el formulario, y lo “entregaremos” a la vista correspondiente para que
los “dibuje”. La variable ``$mvc_vis_plantilla`` definida en la acción especifica
que el nombre de la plantilla es *mostrarAlimentos*. Luego debemos crear una 
archivo denominado *mostrarAlimentosPlantilla.php* para que se cierre el ciclo. He aquí el código de este archivo:

.. code-block:: html+jinja

	<table>
		<tr>
			<th>alimento (por 100g)</th>
			<th>energía (Kcal)</th>
			<th>grasa (g)</th>
		</tr>
		<?php foreach ($param_alimentos as $alimento) :?>
		<tr>
			<td><?php echo $alimento['nombre'] ?></td>
			<td><?php echo $alimento['energia']?></td>
			<td><?php echo $alimento['grasatotal']?></td>
		</tr>
		<?php endforeach; ?>
	
	</table>

Esta plantilla puede ser reutilizada más adelante por cualquier acción que defina
un *array* denominado ``$param_alimentos`` cuyos elementos sean *arrays* con los 
campos ``nombre, energia`` y ``grasatotal``. 

.. note:: 

   En la plantilla anterior se utiliza un recurso muy frecuente cuando se desea 
   mostrar un número de datos variables (en este caso un conjunto de alimentos). 
   Se trata de pasar a la plantilla como dato dinámico un *array* y procesarlo en
   la misma mediante un bucle *foreach*. Para no perder la estructura *HTML* y c
   onstruir plantillas limpias que puedan ser fácilmente entendida por todos, 
   *PHP* admite especificar los bloques *foreach* sin necesidad de utilizar 
   llaves ({}). Para ello se termina la instrucción *foreach* con dos puntos (:) 
   y se indica el final mediante una etiqueta ``<?php endforeach ; ?>``. Lo mismo
   se puede hacer con los bloques *if/endif*. 

   Es una buena práctica en el desarrollo de aplicaciones *web* con *PHP*
   implementar plantillas en las que únicamente se utilicen las siguiente 
   sentencias *PHP*: ``echo, foreach/endforeach`` y ``if/endif``. De esa manera
   el código de las plantillas será más próximo al *HTML* que al *PHP* y se podrá
   descubrir fácilmente la estructura *HTML*, que es lo que se pretende con las 
   plantillas.

Ya lo tenemos todo, ahora puedes probar a rellenar el formulario y enviarlo. El 
sistema mostrará un listado con el resultado de la búsqueda.


**Completamos la aplicación**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Para cubrir los requisitos que se especificaron al principio, debemos añadir las
siguientes funcionalidades:

* búsqueda por energía

* búsqueda combinada

* insertar nuevos registros

En este apartado únicamente vamos a mostrar el código que debemos añadir para 
conseguirlo. Se deja al estudiante que decida donde debe colocar cada archivo.

.. note:: 

   Cada uno de los siguientes apartados es independiente uno de otro a excepción 
   del  2.6.4, que contienen el código de las funciones del modelo que son 
   utilizadas en los apartados 2.6.1, 2.6.2 y 2.6.3. 


**Búsqueda por energía**
##########################

* **acción**: *buscarAlimentosPorEnergia*

  **archivo**: *buscarAlimentosPorEnergiaAccion.php*

.. code-block:: php

	<?php
	
	$mvc_vis_plantilla = 'formBusquedaPorEnergia';
	?>


* **acción**: *procesarFormBusquedaPorEnergia*,

  **archivo**: *procesarFormBusquedaPorEnergiaAccion.php*
  
.. code-block:: php

	<?php
	
	if(is_numeric($_POST['energia_min']) && is_numeric($_POST['energia_max']))
	{
		$param_alimentoss = array();
		
		$param_alimentoss = buscarAlimentosPorEnergia($_POST['energia_min'], $_POST['energia_max'], $mvc_bd_conexion);
	
		//$mvc_vis_plantilla = "mostrarAlimentos";
		$mvc_vis_plantilla = "mostrarAlimentosHistoEnergia";
	}
	else
	{
		$mvc_vis_plantilla = "mostrarErrorFormulario";
	
	}
	?>

* **plantilla**: *formBusquedaPorEnergia*

  **archivo**: *formBusquedaPorEnergiaPlantilla.php*
  
.. code-block:: html+jinja

	<form name="formBusqueda" action="index.php?accion=procesarFormBusquedaPorEnergia" method="POST">
	
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


* **plantilla**: *mostrarAlimentosHistoEnergia*

  **archivo**: *mostrarAlimentosHistoEnergiaPlantilla.php.*
  
.. code-block:: html+jinja

	<table>
		<tr>
			<th>alimento (por 100g)</th>
			<th>energía (Kcal)</th>
		</tr>
		<?php foreach ($param_alimentoss as $param_alimentos) :?>
		<tr>
			<td><?php echo $param_alimentos['nombre'] ?></td>
			<td><?php echo pintaBarra($param_alimentos['energia'])?></td>
			
		</tr>
		<?php endforeach; ?>
	
	</table>


* **plantilla**: *mostrarErrorFormulario*

  **archivo**: *mostrarErrorFormularioPlantilla.php.*
  
.. code-block:: bash

	<b>Algún parámetro del formulario no es válido</b>
	<br/>
	<a href="javascript:history.back(1)">Volver</a>


La acción *procesarFormBusquedaPorEnergia* utiliza una plantilla denominada 
*mostrarAlimentosHistoEnergia* y que muestra el listado de alimento mediante un 
sencillo histograma compuesto por el carácter '*'. Comprueba que puedes 
sustituirla por la plantilla *mostrarAlimentos*, y que todo sigue funcionando, 
aunque la representación es distinta. Este ejemplo muestra lo sencillo que 
resulta cambiar las cosas cuando están bien organizadas.


**Búsqueda combinada**
##########################

Esta funcionalidad permitirá la búsqueda de alimentos cuyas cantidades de energía
(en Kcal), proteínas (en gramos), hidratos de carbono (en gramos), fibra (en 
gramos) y grasa en (gramos) se encuentren simultáneamente entre unas cantidades 
mínimas y máximas definidas por el usuario para cada magnitud. 

* **acción**: *buscarAlimentosCombinada*

  **archivo**: *buscarAlimentosCombinadaAccion.php*
  
.. code-block:: php

	<?php
	
	$mvc_vis_plantilla = 'formBusquedaCombinada';
	?>

* **acción**: *procesarFormBusquedaCombinada,*

  **archivo**: *procesarFormBusquedaCombinadaAccion.php*
  
.. code-block:: php
  
	<?php
	
	$param_alimentos = array();
	
	$param_alimentos = buscarAlimentosCombinada($_POST['energia_min'], $_POST['energia_max'],
		$_POST['proteina_min'], $_POST['proteina_max'],
		$_POST['hc_min'], $_POST['hc_max'],
		$_POST['fibra_min'], $_POST['fibra_max'],
		$_POST['grasa_min'], $_POST['grasa_max'],
		$mvc_bd_conexion);
	
	$mvc_vis_plantilla = "mostrarAlimentos";
	
	?>

* **plantilla**: *formBusquedaCombinada*

  **archivo**: *formBusquedaCombinadaPlantilla.php*
  
  .. code-block:: html+jinja

	<form name="formBusqueda" action="index.php?accion=procesarFormBusquedaCombinada" method="POST">
	
	   
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
			</tr>
			<tr>
				<td>proteina (g):</td>
				<td><input type="text" name="proteina_min"></td>
				<td><input type="text" name="proteina_max"></td>
			</tr>
			<tr>
				<td>hidratos de carbono (g):</td>
				<td><input type="text" name="hc_min"></td>
				<td><input type="text" name="hc_max"></td>
			</tr>
			<tr>
				<td>fibra (g):</td>
				<td><input type="text" name="fibra_min"></td>
				<td><input type="text" name="fibra_max"></td>
			</tr>
			<tr>
				<td>grasa (g):</td>
				<td><input type="text" name="grasa_min"></td>
				<td><input type="text" name="grasa_max"></td>
			</tr>
		</table>
		<input type="submit" value="buscar">
	</form>


**Inserción de registros**
##########################

* **acción**: *insertarAlimento,*

  **fichero**: *insertarAlimentoAccion.php*

.. code-block:: php

	<?php
	
	
	if(isset($_POST['insertar']))
	{
		
	// comprobar campos formulario
		if(!insertarAlimento($_POST['nombre'], $_POST['energia'], $_POST['proteina'],
		$_POST['hc'], $_POST['fibra'], $_POST['grasa'], $mvc_bd_conexion))
		{
			$mvc_vis_plantilla = "mostrarErrorFormulario";
			return;
		}
	
		$param_alimentos = $_POST['nombre'];
	
	}
	else
	{
		if(isset($param_alimentos)) unset ($param_alimentos);
	}
	
	$mvc_vis_plantilla = 'formInsertarAlimento';
	
	?>

* **plantilla**: *formInsertarAlimento*

  **archivo**: *formInsertarAlimentoPlantilla.php*
  
.. code-block:: php
  
	<?php if(isset($param_alimentos)) :?>
	<b>El alimento "<?php echo $param_alimentos ?>" ha sido insertado correctamente.</b>
	<?php endif; ?>
	
	<form name="formInsertar" action="index.php?accion=insertarAlimento" method="POST">
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
				<td><input type="text" name="nombre" value="" /></td>
				<td><input type="text" name="energia" value="" /></td>
				<td><input type="text" name="proteina" value="" /></td>
				<td><input type="text" name="hc" value="" /></td>
				<td><input type="text" name="fibra" value="" /></td>
				<td><input type="text" name="grasa" value="" /></td>
			</tr>
	
		</table>
		<input type="submit" value="insertar" name="insertar" />
	</form>
	* Los valores deben referirse a 100 g del alimento

Esta funcionalidad ha sido implementada de manera que el propio formulario de 
entrada de datos sirva para mostrar el mensaje de notificación de la inserción 
del registro cuando este evento se ha producido.


**Ampliación del modelo**
##########################

Por último facilitamos el resto de las funciones del modelo utilizadas por las
acciones y que deben ser añadidas al archivo ``*modelo.php*``.

.. code-block:: php

	function buscarAlimentosPorEnergia($min, $max, $conexion)
	{
		$min = ($min == '' )? '0' : $min;
		$max = ($max == '' )? '100000' : $max;
	
		$sql = "select * from alimentos where energia > ".$min." and energia < ".$max;
		$sql .= " order by energia desc";
	
		$result = mysql_query($sql, $conexion);
	
		$alimentos = array();
		while ($row = mysql_fetch_assoc($result))
		{
			$alimentos[] = $row;
		}
	
		return $alimentos;
	}
	
	function buscarAlimentosCombinada($e_min, $e_max,
		$p_min, $p_max,
		$h_min, $h_max,
		$f_min, $f_max,
		$g_min, $g_max,$conexion)
	{
		$e_min = ($e_min == '')? '0' : $e_min;
		$e_max = ($e_max == '')? '100000' : $e_max;
		$p_min = ($p_min == '')? '0' : $p_min;
		$p_max = ($p_max == '')? '100000' : $p_max;
		$h_min = ($h_min == '')? '0' : $h_min;
		$h_max = ($h_max == '')? '100000' : $h_max;
		$f_min = ($f_min == '')? '0' : $f_min;
		$f_max = ($f_max == '')? '100000' : $f_max;
		$g_min = ($g_min == '')? '0' : $g_min;
		$g_max = ($g_max == '')? '100000' : $g_max;
	
	
		$sql = "select * from alimentos where energia > ".$e_min." and energia < ".$e_max;
		$sql .= " and proteina > ".$p_min." and proteina < ".$p_max;
		$sql .= " and hidratocarbono > ".$h_min." and hidratocarbono < ".$h_max;
		$sql .= " and fibra > ".$f_min." and fibra < ".$f_max;
		$sql .= " and grasatotal > ".$g_min." and grasatotal < ".$g_max;
	
		$result = mysql_query($sql, $conexion);
	
		$param_alimentoss = array();
		while ($row = mysql_fetch_assoc($result))
		{
			$param_alimentoss[] = $row;
		}
	
		return $param_alimentoss;
	}
	
	function insertarAlimento($n, $e, $p, $hc, $f, $g, $conexion)
	{
	// comprobar parámetros
	
		$sql = "insert into alimentos (nombre, energia, proteina, hidratocarbono, fibra, grasatotal) values ('".
			$n."',".$e.",".$p.",".$hc.",".$f.",".$g.")";
	
		$result = mysql_query($sql);
	
		return $result;
	}
	
	function pintaBarra($num)
	{
		$numCaracteres =  ceil($num/10.0);
		$barra = str_repeat('*', $numCaracteres);
		return $barra;
	}


.. note:: 

   La última función; ``*pintaBarra*``, estrictamente no forma parte del modelo, 
   pues lo que hace es convertir un dato de tipo numérico en un número de 
   caracteres '*' que lo representa. Esta función es utilizada por la plantilla  
   ``*mostrarAlimentosHistoEnergia*``. No forma parte del modelo por que no es 
   algo que tenga que ver con la lógica del negocio, no es algo propio de las 
   características de los alimentos, es, más bien, una función de visualización, 
   las cuales suelen ser denominadas *helpers* en el lenguaje de los *frameworks*
   de construcción de aplicaciones *web*.

   Aún así, hemos decidido incluirla en el modelo por cuestiones de simplificación 
   del ejemplo. Cuando vayamos profundizando en el estudio del *framework symfony*,
   veremos la manera correcta de organizar estas funciones.


**Conclusión**
--------------

El ejercicio que acabamos de finalizar ha puesto de manifiesto muchas de las 
ventajas que ofrece diseñar la arquitectura de la aplicación según los principios
del patrón *Modelo – Vista – Controlador*. La estricta organización del código 
según su funcionalidad dentro del patrón, la existencia de un controlador frontal
que supone la columna vertebral que articula la aplicación y el seguimiento más o 
menos estricto de unas normas para la implementación de nuevos módulos, han 
permitido confeccionar una aplicación que cumple bastante de las características 
deseables que estudiamos en la primera unidad.

No obstante, es muy probable que a medida que hayas realizado el ejercicio se te 
hayan ocurrido muchas posibilidades para mejorar el mini-*framework* que acabamos 
de construir. La siguiente lista recoge muchas de las mejoras que, de 
implementarlas, darían lugar a un *framework* de desarrollo de aplicaciones web 
en *PHP* bastante potente:

* Incorporar un mecanismo para validar los datos que se envían en los formularios
  para evitar ataques externos, por ejemplo del tipo *XSS*. 

* Posibilitar las redirecciones entre acciones de la aplicación, de manera que
  los flujos internos de la misma sean más ricos, flexibles y reutilizables.

* Posibilitar el uso distintos *layouts*, plantillas, *CSS's* y etiquetas *meta*
  en función de la acción que se ejecute.

* Posibilitar el control de la sesión de usuario, lo cual permitiría garantizar 
  la seguridad a través de la **autentificación** y **autorización**.

* Independizar el código de la aplicación del sistema gestor de base de datos 
  (*SGBD*) para no tener que reconstruir gran parte del modelo si necesitásemos 
  migrar los datos a un nuevo *SGBD*. Esto se podría realizar con una capa 
  intermedia entre la aplicación y el *SGBD*, que se encargase de construir las
  queries que correspondan al *SGBD* con qué funcione la aplicación.

* Posibilitar el uso de distintos formato de salida para presentar los datos
  además del *HTML*; por ejemplo *XML*, *Json*, *YML*, *PDF*, lo cual nos 
  permitirá crear servicios web, informes imprimibles y salidas adaptadas a 
  dispositivos móviles (*PDA, smartphones*, …)

* Posibilitar el uso de sistemas de plantillas desarrollados por terceros (como 
  *smarty*).

* Incorporar un mecanismo mediante el cual se puedan utilizar librerías y 
  *frameworks* de *javascript* (como *prototype* o *jquery*) que serían incluidos
  a petición de las acciones que lo requiriesen. Integración con *AJAX* para
  mejorar la interactividad con el usuario.

* Incorporar un sistema automático para incluir los archivos que cada acción
  necesite para su ejecución (librerías) sin que el programador tenga que
  preocuparse de ello.

* Posibilidad de definir y acceder a los parámetros de configuración de la 
  aplicación de una forma inmediata. Además sería deseable que estos parámetros
  pudiesen clasificarse con facilidad, por ejemplo mediante espacios de nombre,
  para así saber a que partes de la aplicación afecta dicho parámetro. 

* Incorporación de un sistema de depuración (*debug*) que dé suficiente
  información de las causas que provocan fallos durante la fase de desarrollo de 
  la aplicación. Esta información debe ser accesible únicamente al desarrollador,
  es decir, inhabilitada en los entornos de producción, pues ello abriría un 
  importante agujero de seguridad. 

* Incorporación de un sistema de caché que evitase volver a procesar partes que
  no cambian de una acción a otra. Esto redundaría en un mejor aprovechamiento 
  del ancho de banda disponible y mejoraría la experiencia del usuario.

* Interpretación de las *URL's* para, por un lado hacerlas más limpias al usuario
  y, por otro evitar dar información sobre la estructura de la aplicación (por 
  ejemplo el nombre de los parámetros que necesita la acción, el nombre de la
  acción y el nombre del módulo).

El próximo ejercicio podría ser:

  *“Amplia el mini-framework que acabamos de desarrollar para que incorpore este 
  listado de mejoras”.*

Afortunadamente lo tendrás más fácil; el resto del curso te presentará un 
potentísimo *framework* para el desarrollo de aplicaciones *web* con *PHP* que 
cumple todo eso y mucho más. Comprobarás como a medida que utilizas *symfony* y
vas  asimilando su filosofía de uso, mejorarás tus prácticas de programación, 
aprenderás métodos muy eficientes de desarrollo basados en patrones de diseño de
software bien estudiados, tus aplicaciones ganarán en calidad, estabilidad, 
extensibilidad,  mantenibilidad y otras características deseables y, sobre todo, 
te divertirás.





