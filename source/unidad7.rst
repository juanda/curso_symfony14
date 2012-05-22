**Unidad 7: Profundizando en la arquitectura MVC de Symfony**
==================================================================

Aún quedan bastantes requisitos por cumplir pero la aplicación va tomando cuerpo.
Para llegar hasta aquí hemos estudiado gran parte de los conceptos básicos de
*symfony* y algunos, como la sesión de usuario, que son propios de cualquier 
aplicación web, aunque, eso sí, desde la óptica de *symfony*.

El objetivo que perseguimos en esta unidad es mirar más de cerca algunos de los 
aspectos que ya hemos usado a lo largo del curso e introducir algunos conceptos 
de “grano fino” muy poderosos para hacer un uso eficaz del *framework*. 
Comenzaremos por repasar los principios de la arquitectura *MVC*. Posteriormente
veremos, sin entrar en los detalles del núcleo de *symfony*, el funcionamiento
básico de la parte controladora, y por último estudiaremos la manera en que 
*symfony* compone las vistas a través de los conceptos de *layout, template, 
slot, partials* y componentes.

Los conocimientos adquiridos nos permitirán incorporar a nuestra aplicación las
siguientes características:

* Un menú de navegación.

* Un botón para entrar como usuario registrado en el caso de que estemos
  utilizando la aplicación como invitado, o para desconectarnos de la aplicación 
  si ya hemos iniciado una sesión como usuario registrado en ella.

* Un espacio para indicar los datos del usuario que está utilizando la
  aplicación: nombre, apellidos y perfil.

* Una presentación distinta según el perfil que tenga asociado el usuario que la 
  utiliza.

* Un sencillo servicio *web* que suministra información actualizada a suscriptores
  *RSS* acerca de los nuevos documentos públicos.

Y sin más preámbulos ¡vamos al lío!


**El patrón MVC en symfony**
---------------------------------------

A lo largo de muchos años de programación de aplicaciones en general y de
programación orientada a objetos en particular, los diseñadores y programadores 
se han percatado de la existencia de elementos comunes en las soluciones de los
problemas que resolvían. Este hecho, especialmente en el mundo de la *POO*, ha
posibilitado el estudio y definición de los denominados patrones de diseños. En
pocas palabras, y para satisfacer los propósitos de este curso, los patrones de
diseño son  propuestas generales planteadas en término de una colaboración de 
objetos mediante las que se resuelven una gran cantidad de problemas que aparecen
una y otra vez en el desarrollo de aplicaciones informáticas. Es decir, los 
patrones nos muestran ciertas **clases** de problemas y unas estrategias bien
contrastadas por la experiencia para atacar su solución.

*Christopher Alexander*, un reconocido arquitecto inglés dice que *“cada patrón 
describe un problema que ocurre una y otra vez en nuestro entorno, así como la 
solución a ese problema, de tal modo que se pueda aplicar esta solución un millón
de veces, sin hacer lo mismo dos veces”*. Y aunque se refiere al mundo de la 
arquitectura, sus palabras describen a la perfección la esencia de los patrones
de diseño de software.

Los patrones de diseño se encuentran bien catalogados. El famoso libro *“patrones
de diseño”* de *E. Gamma, R. Helm, R. Johnson* y *J. Vlissides*, más conocidos
como la “banda de los cuatro” (*the gans of four*), realiza una exhaustiva 
catalogación y descripción de los patrones de diseño. Si tuviésemos que 
enfrentarnos a la difícil tarea de construir un complejo sistema *software*, por 
ejemplo: un *framework* para el desarrollo de aplicaciones *web*, sería de mucha
ayuda conocer con detalle este catálogo de patrones y utilizar aquellos que
encajasen en la naturaleza de nuestro problema. Esto mismo, además de estudiar
otros *frameworks* existentes en otros lenguajes como *ruby on rails*, es lo que
han hecho los chicos de *symfony* para su desarrollo. Y, seguramente, a ello se
deba gran parte del éxito de *symfony*: *No reinventar la rueda y utilizar con
rigor todo aquello cuyo funcionamiento este bien demostrado. Observer, decorator,
factory*, y *MVC* son los nombres de algunos patrones de diseño que utiliza
*symfony* en su implementación. De todos ellos, el usuario de *symfony* debería 
conocer con cierto detalle el patrón *MVC*, ya que dota a la aplicación de su
estructura “más visible”, y ayuda al programador de aplicaciones *web* con
*symfony* a colocar cada cosa en su sitio y a construir aplicaciones altamente
modulares, extensibles, portables,  mantenibles y, sobre todo, “vivas” durante 
mucho tiempo.

El patrón *MVC* consiste en tres tipos de objetos: el modelo que es el objeto de
la aplicación, la vista que es su representación y el controlador que define el 
modo en que la interfaz reacciona a la entrada del usuario. De una manera poco
ortodoxa podemos decir que el controlador controla el flujo de la aplicación, 
pide al modelo aquello que el usuario solicita, y le devuelve (al usuario) una 
representación del modelo a través de la vista. El siguiente gráfico ilustra la
cooperación entre estos tres objetos:






Esta separación en capas con responsabilidades bien definidas permite, además de
mejorar la organización del código, la posibilidad de representar de diferentes
formas la misma información. Por ejemplo, en nuestro gestor documental, podremos
ofrecer al usuario un listado de documentos en formato *HTML* para que pueda ser 
visualizado por un navegador *web*, que es lo que se suele hacer en las
aplicaciones *web*. Pero también podemos ofrecer el mismo listado en formato
*XML* con la semántica de *RSS* para que sea procesado por un programa lector 
de noticias *RSS*; o en formato *XML* con una semántica definida por nosotros
para ofrecer un servicio web a medida; o en cualquier otro formato que se nos 
ocurra (*JSON, YAML, CSV*, etcétera) para hacer lo que se nos ocurra. *Symfony*,
al implementar el *MVC* ofrece todas estas posibilidades.

En *Symfony* cada parte del patrón *MVC* constituye un sistema de varios
componentes:

====================== =========================================================

Parte del patrón         Componentes

====================== =========================================================

Controlador            El Controlador frontal, los filtros, las acciones y los
                       objetos *request, response* y *session*

Vista                  El *Layout* de la aplicación, las plantillas de los 
                       módulos, los *slots*, los *partials*, los componentes y
                       los *helpers*

Modelo                 El *ORM*, los formularios, las extensiones propias que 
                       el programador realice de las clases del *ORM* y las 
                       clases y funciones propias que el programador construya
                       para implementar la lógica de negocio.


En esta unidad volveremos a la carga con las dos primeras.


**La parte del controlador.**
------------------------------------

**El controlador frontal**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

El punto único de entrada de una aplicación *symfony* es un archivo *PHP* ubicado 
en el directorio *web*, que se conoce como el controlador frontal y que tiene el
siguiente aspecto:

.. code-block:: php

	<?php
	
	
	require_once(dirname(__FILE__).'/../config/ProjectConfiguration.class.php');
	
	$configuration = ProjectConfiguration::getApplicationConfiguration('frontend', 'prod', false);
	sfContext::createInstance($configuration)->dispatch();


Aunque, como ya hemos visto a lo largo del curso, al generar una aplicación con 
la tarea *generate:app* se crean dos controladores frontales, uno para desarrollo
y el otro para la producción, podemos crear tantos controladores como deseemos. 
La diferencia entre uno y otro, como se aclarará en los párrafos siguientes, será
la definición del entorno de ejecución.

Veamos todo lo que hacen estas tres líneas de código.

La primera línea carga la clase de configuración del proyecto y las librerías de
*symfony* (el núcleo). En el archivo *config/ProjectConfiguration.class.php* se 
declara la ruta al núcleo de *symfony*. Si estamos utilizando una instalación 
centralizada de *symfony* en el servidor (por ejemplo si hemos instalado *symfony*
con *PEAR*), esta ruta apuntará a dicha instalación, pero también podemos colocar
todo el núcleo de *symfony* dentro del árbol de directorio de nuestro proyecto
y cambiar esta ruta para que apunte allí donde hayamos colocado el núcleo. Un
buen sitio puede ser un directorio llamado *symfony* que cuelgue del directorio
*lib* del proyecto (*lib/symfony*). De esta manera nuestro proyecto será
autosuficiente y podremos portarlo a otros servidores con *PHP* que no tengan 
instalado *symfony*. Al fin y al cabo podemos decir que, a nivel de ficheros, 
*symfony* no es más que un conjunto de librerías *PHP*.

La segunda línea crea la configuración de la aplicación. El primer argumento 
indica el nombre de la aplicación que deseamos lanzar, el segundo el entorno que
se desea ejecutar, y el tercero la habilitación del modo de depuración.

Los entornos típicos son *prod* y *dev*. El primero se refiere al entorno de 
producción, y el segundo al entorno de desarrollo. Podemos crear tantos entornos
y controladores frontales como deseemos, aunque por defecto sólo se proporcionan
estos dos; suficientes para desarrollar todo tipo de aplicaciones con *symfony*.
Te habrás fijado que en muchos de los archivos *YAML* de configuración algunos
parámetros aparecen bajo la sección *dev*, otros bajos la sección *prod* y otros
en la sección *all*. En función del entorno indicado en el controlador frontal 
se utilizan unos u otros parámetros en la ejecución del *framework*. Los que
pertenecen a la sección *all* son comunes a todos los entornos a menos que el
mismo parámetro se defina en otro entorno, en cuyo caso tiene validez el de este 
último. Este hecho permite, por ejemplo, que en el entorno de desarrollo se 
utilice una versión de la capa de abstracción de acceso a base de datos *PDO*
diseñada para la depuración, mientras que en el de producción se utiliza la 
versión más optimizada de la misma capa. Puedes verlo consultando el archivo 
*config/databases.yml*. También podemos utilizar esta funcionalidad para definir
distintas bases de datos en cada entorno. En fin, a medida que avanzamos vamos 
comprobando la tremenda flexibilidad de configuración que ofrece el *framework*.

La tercera línea lanza toda la maquinaría de *symfony* con la configuración 
especificada anteriormente. La secuencia de operaciones que se disparan descrita
a alto nivel es la siguiente:

* Se cargan e inicializan las clases del núcleo.

* Se carga la configuración correspondiente al entorno de ejecución indicado en
  el controlador frontal.

* Se decodifica a *URL* de la petición *HTTP* para determinar la acción a ejecutar
  y sus parámetros.

* Si la acción no existe se redirecciona a la acción asignada al *error 404*.
  Esta acción se define en el archivo *apps/nombre_aplicación/config/setting.yml*
  a través de los parámetros *error_404_module* y *error_404_action*. En caso de
  que no definamos explicitamente estos parámetros *symfony* utiliza una propia
  del *framework* por defecto.

* Se activan los filtros. Si los ficheros de configuración de seguridad 
  (*security.yml*) lo exigen, se realiza la comprobación de la autentificación 
  y las credenciales que hemos estudiado en el tema anterior. De manera que si 
  el usuario, en su sesión, no dispone de la autentificación y las credenciales 
  exigidas se realiza una redirección a la acción de “autentificación requerida”
  o “autorización requerida”, las cuales se definen en el fichero 
  *apps/nombre_aplicación/config/setting.yml* mediante los parámetros 
  *login_module* y *login_action*, para el caso de violación de autentificación
  o *secure_module* y *secure_action*, para el caso de violación de credenciales
  (autorización). Si no definimos estos parámetros, *symfony* realiza la 
  redirección a unas acciones internas que ofrece por defecto. Sin conocer la
  existencia de estas acciones, ya las hemos visto funcionando en la unidad 
  anterior cuando estudiábamos la seguridad en la acción.

* Se ejecutan los filtros (primera pasada). Más adelante hablamos de los filtros.

* Se ejecuta la acción y se produce la vista. Es decir se ejecuta el código 
  construido por el programador, el cual constituye las peculiaridades de la 
  aplicación, es decir, las piezas que le faltan al puzzle para completarlo. 

* Se ejecutan los filtros (segunda pasada).

* Se envía la respuesta al cliente.

Este flujo constituye una parte importante del núcleo de *symfony* y conviene 
conocerlo para hacerse un plano de situación que nos dé una visión general del
conjunto. No entraremos en las profundidades del núcleo ya que no es necesario
para hacer un uso provechosos del *framework* y construir aplicaciones *web* de
calidad. No obstante, al estudiante curioso y con ganas de ir más allá de la 
construcción de aplicaciones *web* le resultará un seductor y desafiante 
ejercicio estudiar los aspectos internos del núcleo.


**Los filtros y las acciones**
----------------------------------------

Aunque ya hemos implementado unas cuantas acciones a lo largo del curso y podemos 
pensar que  tenemos un conocimiento empírico suficiente, en esta sección
mostraremos algunos detalles aún desconocidos.

En primer lugar, si volvemos al flujo de operaciones del controlador,
comprobamos que el turno de ejecución de la acción está entre dos turnos de
ejecución de filtros, o lo que es lo mismo, entre un pre-filtro y un post-filtro.
¿Y qué es esto de los filtros? Pues otro patrón de diseño denominado *chain of
responsability* o cadena de responsabilidad  en nuestro idioma. En términos 
genéricos, el propósito de este patrón es dar a más de un objeto la posibilidad 
de responder a una petición, encadenando los objetos receptores que van pasando
la petición  a través de la cadena hasta que es procesada por algún objeto final.
Cada uno de los objetos en la cadena realiza su propio proceso siendo la salida
de uno la entrada del siguiente. Un diagrama de secuencia describe con precisión 
el fundamento de este sencillo pero eficaz patrón de diseño:






Como vemos cada filtro realiza algunas operaciones durante un tiempo y pasa la 
ejecución al siguiente filtro que repite el procedimiento: realizar sus 
operaciones y pasar el testigo al siguiente filtro hasta llegar al último, que
en el caso de *symfony* es el encargado de ejecutar la acción y renderizar la 
plantilla correspondiente. Fíjate también que una vez que el último filtro 
termina su actividad el control pasa al filtro anterior, recorriéndose ahora la
cadena en sentido contrario. Es decir, que una vez ejecutada la acción se vuelve 
a pasar de nuevo por los filtros (segunda pasada), por ello la ejecución de la
acción forma un “emparedado” con los filtros. Durante toda la ejecución de los
filtros tenemos disponibles el objeto que modela la petición *HTTP* del cliente
(*sfRequest*), el que modela la respuesta *HTTP* que se enviará al cliente
(*sfResponse*) y el que modela la sesión de usuario (*sfUser*). Manipulando 
estos objetos tanto en la acción como en los filtros podemos conseguir cualquier 
cosa que se nos ocurra. De manera un tanto informal podemos decir que el 
principal objetivo de la ejecución del *framework* es construir progresivamente
un objeto respuesta a partir de los datos que se reciben en la petición, del 
estado representado en la sesión y, por supuesto, de la lógica de negocio que
decide qué debe hacer con estas entradas. El siguiente gráfico ilustra este
modelo de caja negra del funcionamiento de *symfony*.









La secuencia de filtros se establece en el fichero de configuración de la 
aplicación *apps/nombre_aplicacion/config/filters.yml*. Ábrelo y échale un
vistazo. Comprobarás la referencia a cuatro de los filtros que aparecen en la
figura. El quinto filtro (*misFiltros*) representa, en realidad, a tantos filtros
como el programador desee añadir. Normalmente no es necesario ninguno, pero a 
veces pueden resultar muy útiles. 

.. note::

   Recurso: En esta URL puedes encontrar una explicación de los filtros de
   symfony realizada por los autores de symfony:

   http://www.librosweb.es/symfony_1_2/capitulo6/filtros.html

Otro aspecto de las acciones que puede resultar muy útil son las pre-acciones y
*post-acciones*. Supongamos que en algún módulo todas las acciones necesitan 
realizar alguna actividad común antes de pasar a su ejecución. Por ejemplo, que
todas las acciones deban definir algún parámetro común o necesiten comprobar 
alguna condición o cualquier otra cosa que se nos ocurra. En tal caso, en lugar
de repetir el mismo código al principio de cada acción, que sería la solución 
inmediata, lo correcto sería colocar dicho código en la *pre-acción* del módulo 
en cuestión. Esto significa crear una función denominada *preExecute()* en dicho 
módulo. Lo mismo se haría si en el caso de que el código común tuviese que 
ejecutarse al final de cada acción, solo que en este caso la función que debemos
declarar se llama *postExecute()*: 

.. code-block:: bash

	class moduloActions extends sfActions
	{
		public function preExecute()
		{
		  //Aquí el código que será ejecutado justo antes de la ejecución de 
		  //cualquier acción del módulo
		 }
	
		public function postExecute()
		{
		  //Aquí el código que será ejecutado justo después de la ejecución de 
		  //cualquier acción del módulo
		 }
	
		 // Aquí las acciones
	}


**Asociación de la plantilla a la acción**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Finalizaremos el estudio de las acciones aclarando como se produce la asociación
de la plantilla a la acción. Hasta ahora hemos visto que a una acción denominada 
*miaccion* le corresponde una plantilla denominada *miaccionSuccess.php*. 
Symfony utiliza el valor devuelto por la acción para saber que plantilla debe
utilizar para pintar los datos. Si nosotros no indicamos el valor devuelto por
la acción, como de hecho ocurre en todas las acciones que hemos implementado
hasta el momento, el valor devuelto por defector es *sfView::SUCCESS*, de ahí 
el nombre de la plantilla utilizada. Sin embargo podemos cambiar este valor y 
el *framework* utilizará otra plantilla distinta para mostrar los datos. La 
siguiente tabla muestra los valores devueltos que se permiten en una acción y
la plantilla asociada:

============================= =================================================

Valor devuelto en la acción   Nombre de la plantilla utilizada para renderizar
                      		  los datos

============================= =================================================

*return sfView::SUCCESS*      *{nombre_accion}Success.php*

*return sfView::ERROR*        *{nombre_accion}Error.php*

*return sfView::ALERT*        *{nombre_accion}Alert.php*

*return sfView::INPUT*        *{nombre_accion}Input.php*

*Return 'MiResultado'*        *{nombre_accion}MiResultado.php*

*return sfView::NONE*         *No se utiliza ninguna vista.*

*return sfView::HEADER_ONLY*  *Envía al cliente únicamente las caberas HTTP*


Finalmente, si queremos que la acción sea dibujada por una plantilla específica
que no se corresponda con el nombre de la acción, debemos utilizar el método 
*setTemplate()*, el cual podemos combinar con los anteriores valores de retorno.

Así pues el siguientes código al final de una acción:

.. code-block:: bash

	//Código  de una acción
	...
	
	$this → setTemplate('otraPlantilla');

Produciría la renderización con la plantilla *otraPlantillaSuccess.php*, mientra 
que el siguiente código:

.. code-block:: bash

	//Código  de una acción
	...
	
	$this → setTemplate('otraPlantilla');
	return sfView::ERROR;

Produciría la renderización con la plantilla *otraPlantillaError.php.*

En conclusión, podemos utilizar cualquier plantilla que deseemos para renderizar
la acción. Eso sí, la plantilla debe estar preparada para pintar los parámetros 
que se hayan definido en la acción. La flexibilidad que *symfony* nos ofrece a la
hora de construir nuestras aplicaciones sigue poniéndose de manifiesto a medida
que avanzamos en el curso.


**Implementación de un filtro para la selección de CSS en función del perfil del usuario.**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Para ilustrar el uso de los filtros de *symfony*, vamos a incorporar a nuestro
gestor documental una nueva funcionalidad que no contemplamos en el análisis de
la aplicación. Se trata de utilizar distintas *CSS's* en función del perfil que
tenga asociado el usuario. Esta nueva funcionalidad  resultará muy atractiva y 
resultaría aun más útil si un mismo usuario pudiese tener asociado más de un 
perfil, ya que por el aspecto gráfico que muestra la aplicación el usuario sabría
el perfil con el que se encuentra trabajando. 

En primer lugar crearemos una *CSS* para cada perfil. Recuerda que hemos dividido
los estilos en tres archivos *CSS*. Para los propósitos de este ejemplo 
únicamente cambiaremos el archivo *default.css*. Creamos cuatro copias de dicho
archivo y las denominamos *default_invitado.css, default_lector.css, 
default_autor.css* y *default_administrador.css*:

.. code-block:: bash

	# cp web/css/default.css web/css/default_invitado.css
	# cp web/css/default.css web/css/default_lector.css
	# cp web/css/default.css web/css/default_autor.css
	# cp web/css/default.css web/css/default_administrador.css
	# rm web/css/default.css

Ahora cambiamos los estilos definidos en las anteriores *CSS's* para
particularizarlos al perfil. Como se trata de un ejemplo pedagógico únicamente 
cambiaremos el color del fondo del elemento *body*, asignando los siguiente
colores a cada perfil:

=========================== ===================================================

Perfil                      Color

=========================== ===================================================

Invitado                    #1F8CB5

Lector                      #E3A114

Autor                       #B4F2A2

Administrador               #E890AD



Se trata de modificar el atributo *background-color* en la línea 117 de los
ficheros *default_{nombre_perfil}.css*.

Ahora creamos el filtro como una clase denominada *FiltroCSS* y la ubicamos en
el directorio *lib* de la aplicación:

*Contenido del archivo: /apps/lib/FiltroCSS.class.php*

.. code-block:: php

	<?php
	
	class FiltroCSS extends sfFilter
	{
		public function execute($filterChain)
		{
			if($this  -> isFirstCall())
			{
				$user = $this->getContext()->getUser();
				$perfil = ($user -> hasAttribute('perfil'))? $user -> getAttribute('perfil') : 'invitado';
				
	
				$respuesta = $this -> getContext() -> getResponse();
	
				$respuesta -> addStylesheet('default_'.$perfil);
			}
	
			//Ejecutar el próximo filtro
			$filterChain->execute();
		}
	}

En este filtro se utiliza la función *isFirstCall()* para garantizar que
únicamente se ejecute una vez en el caso de que se haya realizado una redirección 
desde otra acción. Además, todos los filtros deben terminar con una llamada al 
siguiente filtro, lo cual se hace en la línea:

.. code-block:: bash

	$filterChain → execute();

El filtro detecta el tipo de perfil que tiene el usuario consultando la sesión,
y en función del resultado obtenido añade a la respuesta la hoja de estilos 
correspondiente. 

Ya casi lo tenemos. Ahora debemos indicar a *symfony* que incluya este filtro 
en su cadena de filtros. Para ello modificamos el archivo 
*apps/frontend/config/filters.yml* de la siguiente manera:

*Contenido del archivo: apps/frontend/config/filters.yml*

.. code-block:: bash

	rendering: ~
	security:  ~
	
	# insert your own filters here
	css:
	  class: FiltroCSS
	
	cache:     ~
	execution: ~

El texto en negrita muestra el código añadido. Ya únicamente nos queda hacer una
cosa, eliminar del fichero de configuración de la vista la hoja de estilos
*default.css*, ya que con los cambios realizados ha dejado de utilizarse. 

*Contenido del archivo: apps/frontend/config/view.yml*

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
	
	  stylesheets:    [ admin.css, menu.css]
	
	  javascripts:    []
	
	  has_layout:     true
	  layout:         layout

Ya está. Ahora puedes comprobar el funcionamiento registrándote con distintos 
usuario que tengan asociados distintos perfiles y verás como cambia el color del
fondo de la aplicación.


**La parte de la vista**
---------------------------------

Desde el principio del curso hemos trabajado los conceptos de *layout* de la
aplicación y plantillas (o *templates*) de los módulos, introduciendo nuevos 
aspectos a medida que los necesitábamos. En este apartado, igual que hemos hecho
con la parte del controlador, trataremos más de cerca la parte de la vista.

En las aplicaciones *web*, la mayor parte de las respuestas que el servidor
envía al cliente, contienen como datos una representación *HTML* del recurso 
solicitado, ya que es el lenguaje de marcado que entienden los navegadores *web*.
Sin embargo esto no tiene por que ser siempre así. En ocasiones el servidor *web*
puede enviar un fichero de cualquier tipo. Es lo que hace nuestro gestor 
documental cuando se le solicita una versión de un documento. En otras ocasiones 
se pueden enviar otro tipo de representaciones, siendo el *XML* uno de los
lenguajes de marcados más utilizados en las aplicaciones *web* gracias a su 
capacidad para el intercambio de datos entre aplicaciones. Es decir, es un 
formato muy adecuado para ser procesado por las máquinas facilitando la
interoperatibilidad entre las mismas. Por esa razón es el lenguaje más 
utilizados para la implementación y consumo de servicios *web*. *JSON* es otro
de los formatos de intercambio de datos de fuerte presencia en las aplicaciones
y servicios *web*, especialmente cuando la información que se transmite tiene 
que ver con estructuras de datos y objetos software que deben ser ejecutados en
el cliente. A medida que la *web* semántica extienda su popularidad, posiblemente
en muy poco tiempo, el formato *RDF* entrará de lleno en la escena como otro 
sistema de representación de recursos. El *PDF* también goza de buena fama cuando 
de imprimir documentos se trata. En definitiva, no solo de *HTML* vive la *web*,
y *symfony*, como *full stack framework* para el desarrollo de aplicaciones *web*,
ofrece la posibilidad de generar vistas más allá del *HTML*. 

Este apartado lo hemos dividido en dos partes diferenciadas; la primera trata de
la generación de vistas orientadas a la presentación en navegadores *web*, es
decir, representaciones *HTML* de los recursos. Y la segunda de la generación de
vistas en representaciones no *HTML* (todas las demás). Queremos dejar claro que,
a pesar de esta división metodológica, un mismo recurso puede ser representado 
en cualquier tipo de formato que podamos imaginar, siendo los más usuales el
*HTML* (para mostrar en los navegadores), el *PDF* (para imprimir), el *XML* 
(para casi cualquier cosa, servicios *web* como ejemplo ilustrativo) y el 
*JSON* (para enviar objetos software al cliente). 


**La vistas HTML**
^^^^^^^^^^^^^^^^^^^^^^^^^^

**El proceso de decoración. Los layouts.**
#################################################

La generación de vistas en *symfony* se realiza según lo establecido por otro 
patrón de diseño denominado *decorator*. Este patrón, de nombre bastante 
descriptivo, responde a la necesidad de añadir dinámicamente funcionalidad a un
objeto. En *symfony* dicho objeto sería la plantilla con la que se renderiza una
determinada acción de algún módulo, y la funcionalidad añadida dinámicamente 
sería el resto del documento *HTML*, definido en alguno de los ficheros alojados
en el directorio *apps/nombre_aplicacion/templates*, es decir en alguno de los 
*layouts* de la aplicación.

Recordemos el gráfico que utilizamos en la unidad 2 para explicar el concepto de
generación de la vista como combinación de una plantilla y un *layout*, pues
ilustra bastante bien el concepto de decoración.





Hasta el momento únicamente hemos hablado de un solo fichero donde se define el
*layout* de la aplicación, ya que este se utiliza por defecto para decorar las
plantillas y, ha sido suficiente para cubrir los objetivos de nuestra aplicación.
Sin embargo podemos cambiar este comportamiento en las acciones e indicar otras
plantillas para **decorarlas**. Para ello utilizamos el método *setLayout()* en
la acción en cuestión :

.. code-block:: bash

	// Código dentro de una acción
	...
	
	$this → setLayout('otroLayout');
	
	...

Obviamente, para que el trozo de código anterior tuviese efecto, tenemos que 
definir en el directorio reservado para los *layouts* de la aplicación 
(*apps/nombre_aplicacion/templates*), el fichero *otroLayout.php* con la 
definición del mismo. 


**Uso de javascripts y CSS's. Los ficheros de configuración view.yml**
##############################################################################

Los documentos *HTML* están estructurados en dos partes principales: la cabecera
entre las etiquetas *<head></head>* y el cuerpo entre las etiquetas *<body></body>*.
En la cabecera se coloca la meta-información que describe el documento en sí, se
pueden incluir enlaces a recursos *javascripts* que serán utilizados en el
navegador cliente, y enlaces a los ficheros *CSS's* que se utilizan en el cuerpo 
para dotar a los elementos visibles de un determinado aspecto gráfico. 

Si queremos incluir ficheros *CSS's* y/o *javascript* a los documentos *HTML*
generados por nuestra aplicación debemos indicarlo explícitamente en el *layout*
mediante las funciones *include_stylesheets()* y *include_javascripts()*. 
Aclaramos: estas funciones indican al *framework* que el *layout* en cuestión 
“desea” utilizar *CSS's* o *Javascript*, no especifica ningún archivos *CSS* o
*Javascript* en concreto. Esto último se puede hacer de las distintas formas que
explicaremos a continuación.

1. En el archivo *view.yml* de la aplicación 
   (*apps/nombre_aplicacion/config/view.yml*). Si queremos que unas *CSS's* y/o 
   unos *Javascripts*, sean incluidos en todos los documentos generados por la
   aplicación, o dicho de otra manera, que estén disponibles para todas las
   acciones de todos los módulos, podemos utilizar las secciones *stylesheets* y
   *javascript* del archivo *view.yml* de la aplicación para incluirlos.

2. En los archivos *view.yml* de los módulos 
   (*apps/nombre_aplicacion/modules/nombre_modulo/config/view.yml*). Si lo que 
   queremos es que únicamente los documentos que resulten de la ejecución de las
   acciones de algún módulo incluyan ciertas *CSS's* y/o *Javascripts*, entonces 
   las secciones *styleheets* y *javascript* de los ficheros *view.yml* de los
   módulos son los lugares donde podemos especificarlos.

3. En las plantillas de los módulos a través de las funciones *use_stylesheet()*
   y *use_javascript()*. Si lo hacemos de esta forma, las *CSS'S* y/o *Javascripts*,
   serán incluidos únicamente en los documentos *HTML* generados a partir de las
   acciones que utilicen la plantilla en cuestión.

4. Utilizando directamente el objeto *sfResponse*. Como ya hemos dicho en otro
   momento, el objeto *sfResponse* modela la respuesta *HTTP* que se envía al 
   cliente al final del proceso. Desde las acciones podemos acceder directamente
   a dicho objeto mediante el método *getResponse()*:

.. code-block:: bash

	// Trozo de código en una acción
	
	...
	$respuesta = $this → getResponse();
	
	$respuesta → addStylesheet('mi_hoja_de_estilo.css');
	$respuesta → addJavascript('mi_javascript.js');
	...

Como puedes imaginar, la hojas de estilo referenciada en el código anterior debe
estar ubicada en el directorio *web/css*, y el fichero *javascript* en el 
directorio *web/js*.

Desde los filtro debemos acceder a través del contexto general de la aplicación:

.. code-block:: bash

	// Trozo de código en un filtro
	
	...
	$respuesta = $this -> getContext() -> getResponse();
	$respuesta -> addStylesheet('mi_hoja_de_estilo.css');
	…

Tal y como hemos hecho en el filtro implementado en un apartado anterior.

Como puedes ver la flexibilidad de *symfony* sigue en aumento. La forma en que 
añadas las *CSS's* y/o *javascripts* dependerá de la situación en concreto. Si 
utilizas únicamente el archivo *view.yml* de la aplicación nunca fallarás, pero 
puede que estés sobrecargando innecesariamente la respuesta y, por tanto, 
desaprovechando el ancho de banda.

El fichero de configuración *view.yml* de la aplicación, además de para 
especificar las *CSS's* y *javascripts* comunes a toda la aplicación, se utiliza 
para incluir la meta-información que va en la sección *head* del fichero *HTML* 
y los parámetros de la respuesta *HTTP* como el *content-type*:

*Contenido del archivo: /apps/nombre_aplicacion/config/view.yml*

.. code-block:: bash

	default:
	  http_metas:
		content-type: text/html
	
	  metas:
		title:        symfony project
		description:  symfony project
		keywords:     symfony, project
		language:     en
		robots:       index, follow
	
	  stylesheets:    [main.css]
	
	  javascripts:    []
	
	  has_layout:     true
	  layout:         layout

Por último, el parámetro ``has_layout`` indica al *framework* si debe decorar 
las acciones (*true*) o no (*false*).


**Asociación de la vista a la acción.**
##################################################

El mecanismo de asociación entre la acción y las vista ha sido explicado en el
apartado 2.3 de esta misma unidad, correspondiente a la parte controladora. Hemos
incluido este apartado con el fin de mostrar que dicho mecanismo es algo que 
también pertenece a la parte de la vista. Podemos decir que dicho mecanismo ofrece
el punto de comunicación entre el controlador y la vista. Obviamente no vamos a
repetir la explicación y remitimos al estudiante al apartado 2.3 de esta misma 
unidad.


**Los helpers**
######################

Ya hemos utilizado algunos *helpers* a lo largo del curso. Ahora los definiremos
con precisión, presentaremos los más usuales y explicaremos como puedes producir 
tus propios *helpers*.

Los *helpers* no son más que funciones de *PHP* que devuelven una cadena con 
código para el cliente, normalmente código *HTML* o *javascript*. Se pueden
utilizar tanto en las plantillas de los módulos como en los *layouts* de la
aplicación. Estas criaturas se agrupan en librerías según su propósito. Si
echamos un vistazo al directorio *helper* del núcleo de *symfony* vemos los 
siguientes ficheros que representan estas agrupaciones:

* *AssetHelper.php*
* *CacheHelper.php*
* *DateHelper.php*
* *DebugHelper.php*
* *EscapingHelper.php*
* *HelperHelper.php*
* *I18NHelper.php*
* *JavascriptBaseHelper.php*
* *NumberHelper.php*
* *TextHelper.php*
* *UrlHelper.php*

Como ves los nombres de los ficheros que contienen helpers terminan con el sufijo
*Helper*. La regla general es que si deseamos utilizar algún *helper*, debemos
incluir en la plantilla el nombre del fichero (sin el sufijo *Helper*) que lo 
contiene mediante la función *use_helper()*. Por ejemplo si queremos usar en una
plantilla el *helper* ``format_date()`` que está en el archivo *DateHelper*,
colocaríamos al principio de la plantilla el siguiente código:

.. code-block:: php

	<?php use_helper('Date') ?>
	... 
	
	<?php echo format_date(date(),'d', 'es') ?>
	... 

Es decir, el nombre del fichero sin el sufijo.

Sin embargo este no es el caso de los ficheros *HelperHelper.php, TagHelper.php*,
*UrlHelper.php* y *AssetHelper.php*, que se incluyen automáticamente en el
*framework* ya que son necesarios para el mecanismo de plantillas.

Dos de los *helpers* más utilizados, pertenecientes al fichero *UrlHelper.php*
son *link_to()* y *url_for()*, que sirven para generar enlaces (*links*) *HTML*
y rutas válidas para el servidor donde se ejecuta la aplicación a partir de los
nombres del módulo y de la acción en combinación con los parametros de la *query 
string*. Por ejemplo:

.. code-block:: php

	<?php echo url_for('mimodulo/miaccion?param1=valor1&param2=valor2') ?>

Daría lugar una vez interpretado por *PHP* a algo así:

``http://miservidor/miruta/web/index.php/mimodulo/miaccion/param1/valor1/param2/valor2``

En realidad, la salida exacta depende de cómo se hayan definido las rutas en el
sistema de enrutamiento de *symfony*. Es la primera vez que hablamos en el curso 
del sistema de enrutamiento. Aunque su uso explicito no es estrictamente
necesario para desarrollar una aplicación *web* con *symfony*, si recurrimos a él
remataremos la aplicación con un conjunto de *URL's* que, además de limpias, 
ocultan los nombre de los parámetros que se pasan por *GET* al servidor,
ocultando por tanto detalles de implementación interna de la aplicación. Esto,
como podrás suponer, redunda en una mejora considerable de la seguridad.
Hablaremos del sistema de enrutamiento en la última unidad del curso donde se
tratarán algunos temas avanzados como la internacionalización y el enrutamiento.

Otro *helper* muy útil es *image_tag()*, el cual arroja el código *HTML* para 
la inclusión de una imagen:

.. code-block:: php

	<?php echo image_tag('miimagen.png', 'alt=imagen size=200x100') ?>

Daría lugar a algo así:

.. code-block:: bash

	<img src="/images/miimagen.png" alt="imagen" width="200" height="100"/>

Obviamente la imagen debe estar ubicada en el directorio *web/images*.

Por último vamos a explicar como puedes crear tus propios *helpers*. Creas un
fichero con el nombre tu *helper* seguido del sufijo *Helper* en el directorio
*lib* que sea más adecuado para tus propósitos (normalmente será el de la 
aplicación). En su interior defines funciones que deben devolver una cadena con
un trozo de código *HTML, javascript, XML*  o lo que sea. A continuación lo 
incluyes en la plantilla que donde  necesites alguna de estas funciones. Para
ello utilizas la función *use_helper()*, igual que si se tratase de un *helper*
de “serie”. Y punto y final.


**Partials y componentes.**
#################################

¿Recuerdas el principio *DRY: Don't Repeat Yourself*, del que hemos hablado varías
veces a lo largo del curso? Los *partials* y componentes son recursos mediante 
los que podemos modularizar y reutilizar los elementos de la vista. Una forma 
natural de llegar a ellos es a través de la sana costumbre de refactorizar 
continuamente el código que se escribe. Cuando veamos partes de la vista que se 
repiten en muchos lugares, seguramente llevar tales partes a un *partial* o a un 
componente nos resultará de gran ayuda. 

Un *partial* es una plantilla que puede ser incluida en cualquier otra plantilla
o en un *layout*. Un ejemplo vale más que mil palabras y eso es lo que vamos a 
hacer para explicar este concepto. Vamos a introducir en todas las páginas de la
aplicación un enlace para que se registre el usuario si aún no lo ha hecho, y 
para que se desconecte de la aplicación  cuando le apetezca si ya se identificó. 

Un *partial*, como plantilla que es, puede ubicarse en el directorio *templates*
de cualquier módulo. Hemos decidido ubicarlo en el módulo *inises*, puesto que
también deseamos utilizar este enlace de registro  en la aplicación de *backend*
y, como ya hemos dicho en otro momento, este módulo será convertido más adelante
en un *plugin* para que pueda ser compartido por ambas aplicaciones. El único 
requisito que una plantilla debe reunir para que sea un *partial* es que el 
nombre del fichero que la define comience con el carácter “_”. De esa manera 
podrá ser incluida como parte de otra plantilla cualquiera, y no estará asociada
a ninguna acción particular. Por último para incluir un *partial* en otra
plantilla o en un *layout* se utiliza la función *include_partial()*.

Vamos a verlo en la práctica. Crea el fichero *_signInOut.php* en el directorio
*templates* del módulo *inises* con el siguiente código:

*Contenido del archivo: /apps/frontend/modules/inises/templates/_signInOut.php*

.. code-block:: php

	<?php if($sf_user -> isAuthenticated()) :?>
	<?php echo link_to('desconectar', 'inises/signOut') ?>
	<?php else : ?>
	<?php echo link_to('registro', 'inises/signIn') ?>
	<?php endif; ?>

Este código comprueba si el usuario está autentificado (observa el uso de la 
variable *$sf_user*, recuerda que es la manera de acceder desde una plantilla 
al objeto que representa la sesión de usuario), en cuyo caso crea un enlace para
realizar la desconexión, y en caso contrario crea un enlace para realizar el 
proceso de autentificación. Fíjate, en primer lugar en el uso del *helper 
link_to()* para crear los enlaces y, en segundo lugar en que ambas acciones ya
la hemos implementado y probado a través de la barra de direcciones del navegador
en el tema 6. Ahora vamos a incorporarlas a la aplicación gracias a este *partial*.
Dicha incorporación la realizaremos en el *layout* de la aplicación utilizando, 
como hemos dicho anteriormente, la función *include_partial()*.

*Contenido del archivo: apps/frontend/templates/layout.php*

.. code-block:: php

	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
		<head>
			<?php include_http_metas() ?>
			<?php include_metas() ?>
			<?php include_title() ?>
			<?php include_stylesheets() ?>
			<?php include_javascripts() ?>
			<link rel="shortcut icon" href="/favicon.ico" />
			
		</head>
		<body>
			<div id="contenedor_general">
				<div id="cabecera">
					<div id="logo"></div>
				</div>
	
				<div id="wrapper">
					<div id="perfil"><?php include_partial('inises/signInOut') ?></div>
	
					<div id="menuprincipal"></div>
	
					<div id="admin_container"><?php echo $sf_content ?></div>
	
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

Se ha resaltado en negrita el código añadido para incluir el *partial* que
acabamos de construir. Como puedes ver, la función *include_partial()* espera
como primer argumento una cadena que indica el nombre del *partial* (sin el 
carácter “_”) y el módulo donde reside. Ahora puedes probar el funcionamiento 
del invento. Si no estás aún registrado, cuando pinchas en registrar te aparece
la ventana de registro y ya puedes registrarte. En función del perfil que tenga
el usuario le aparecerán más o menos documentos y más o menos acciones como 
consecuencia de las credenciales asociadas. 

A los *partials* también se le pueden pasar parámetros cuando se los incluye con
la función *include_partial()*. Para ello se puede usar como segundo argumento
de esta función un *array* asociativo en el que las claves son los nombres de las
variables que usará el *partial*. 

El componente es aún más poderoso que el *partial*. En pocas palabras: es un
*partial* con una acción asociada de manera que los datos que pinta son el 
resultado de un proceso más complejo que suele ser el resultado de consultar al 
modelo. Dicha acción en el lenguaje de *symfony* se denomina componente y se
implementa en un archivo llamado *components.class.php* ubicado en el directorio 
*actions* del módulo. El *partial* asociado, por su parte, se ubica en el
directorio *templates* del mismo módulo y su nombre debe comenzar por el 
carácter “_”. El funcionamiento del componente, por lo tanto, sigue las
directivas del patrón *MVC*, solo que aplicado a una porción de la vista. Para 
incluir el componente en una plantilla o en un *layout* se utiliza la función
*include_component()*, cuyo primer argumento debe ser el nombre del módulo donde
reside el componente, el segundo es el nombre del componente, y el tercero 
(opcional) un *array* asociativo con los parámetros que pueda necesitar el 
componente.

Para ilustrar este concepto, desarrollaremos un componente mediante el que
integraremos en todas las pantallas de nuestra aplicación un mensaje de 
bienvenida con el nombre y el perfil asociado del usuario que la está utilizando,
obviamente siempre que esté registrado en la aplicación, y si no lo está
indicaremos con el mismo componente su condición de invitado. 

Por la misma razón que en el caso del *partial* de registro, construiremos el
componente en el módulo *inises*. Comenzamos por crear el fichero que alojará al
componente donde escribiremos el siguiente código:

*Contenido del archivo: apps/frontend/modules/inises/actions/components.class.php*

.. code-block:: php

	<?php
	
	class inisesComponents extends sfComponents
	{
		public function executeMostrarPerfil()
		{
			$user = $this -> getUser();
	
			if($user -> isAuthenticated())
			{
				$id_usuario = $user -> getAttribute('id_usuario');
	
				$usuario = UsuariosPeer::retrieveByPK($id_usuario);
	
				$this -> nombre = $usuario -> getNombre();
				$this -> perfil = $usuario -> getPerfil();
			}
			else
			{
				$this -> nombre = 'usuario';
				$this -> perfil = 'invitado';
			}
		}
	} 

Observa que la estructura es idéntica a la de un fichero de acciones; incluso
las funciones que actúan como componentes llevan el prefijo *“execute”*. Sin
embargo existe una sutil diferencia respecto de las acciones, y es que al 
componente no se le pasa como argumento la petición *HTTP* (el objeto 
*sfWebRequest*). No obstante, si lo necesitamos, podemos acceder a la misma
mediante el método *getRequest()*. De la misma manera podemos acceder a la 
respuesta mediante *getResponse()* y, como se muestra en el código anterior, 
también a la sesión de usuario mediante *getUser()*.

Ahora tenemos que implementar la plantilla (partial) asociada al componente, la
cual debe denominarse igual que el componente pero cambiando el prefijo *execute*
por el carácter “_” . Escribimos en ella el siguiente código:

*Contenido del archivo: apps/frontend/modules/inises/templates/_mostrarPerfil.php*

``Bienvenido <?php echo $nombre ?> | <?php echo $perfil ?> |``

Y para finalizar lo incluimos en el *layout* de la aplicación, justo antes del
enlace para el registro que hemos desarrollado más atrás:

*Porción del archivo: apps/frontend/templates/layout.php*

.. code-block:: bash

	<div id="perfil"><?php echo include_component('inises', 'mostrarPerfil') ?><?php include_partial('inises/signInOut') ?></div>

Si nuestro componente necesitase algún parámetro podemos utilizar el tercer 
argumento de la función *include_component()*, que es un array asociativo cuyas 
claves se convertirán en miembros públicos del componente en cuestión y que, por
consiguiente, podrán ser accedidos mediante el identificador *$this*.

Remataremos el estudio de la parte de la vista *HTML* añadiendo a la aplicación 
un menú cuyos enlaces dependerán de las credenciales asociadas al perfil, de la
manera que se especifica en esta tabla:

=================== ===========================================================

Credencial          Menú

=================== ===========================================================

Cualquiera          Enlace a la ayuda

Escritura           Se le añade un enlace para crear un nuevo documento.

Administración      Se le añade un enlace para enlazar con la aplicación de 
                    administración.


Lo haremos a través del *partial* siguiente, cuya explicación se deja como
ejercicio al alumno:

*Contenido del archivo: apps/frontend/templates/_menu.php*

.. code-block:: php

	| 
	<?php if($sf_user -> hasCredential('escritura')) : ?>
	<?php echo link_to('Nuevo Documento','gesdoc/nuevo') ?> |
	<?php endif; ?>
	<?php echo link_to('Ayuda','gesdoc/ayuda') ?> |
	<?php if($sf_user -> hasCredential('administracion')) : ?>
	<a href="#">Administración</a> |
	<?php endif; ?>
	<hr/>

Que se incorpora al *layout* de la aplicación en la capa reservada para el menú:

*Porción del archivo: apps/frontend/templates/layout.php*

.. code-block:: bash

	 <div id="menuprincipal">
		<?php include_partial('global/menu') ?>                   
	 </div>

Este *partial*, al no ser específico de ningún módulo, lo hemos colocado 
directamente en la carpeta *template* de la aplicación. Los *partials* que no 
pertenecen a ningún módulo se referencian como si perteneciesen a un módulo 
denominado *global*.


**Las vistas no HTML**
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Como ya hemos advertido, no solo de *HTML* vive la *web*, especialmente cuando 
entran en la escena los servicios *web*. Hemos de tener en cuenta que *HTTP* es
un protocolo mediante el que se pueden transmitir cualquier tipo de archivo,
aunque los documentos *HTML* sean los más populares debido a que son los que 
pueden ser interpretados y pintados por los navegadores *web*. Por ello se 
pueden desarrollar, como de hecho se hace cada vez con más frecuencia, 
aplicaciones *web* que utilicen otro tipo de representación, fundamentalmente
*XML*, de los recursos que sirven. A este tipo de aplicaciones se les conoce como
servicios *web*. Por lo general podemos decir que las aplicaciones *web* típicas
están concebidas para ser consumidas directamente por humanos y usan 
intensivamente el *HTML* para la representación de los recursos, mientras que 
los servicios *web* buscan la interoperatibilidad entre máquinas y usan,
preferentemente, como formato de intercambio el *XML*. Este criterio ha llevado
a algunos autores a proponer los términos *web humana* y *web programable* para
designar a los dos espacios *web* que surgen cuando se toma como criterio el tipo
de consumidor de los recursos: el hombre o la máquina.

*Symfony* es un *framework* completo para la construcción de todo tipo de 
aplicaciones *web*, incluido los servicios *web*, por tanto ofrece un soporte 
nativo para distinto tipos de representaciones, de manera que podemos utilizar 
el mismo controlador y modelo para arrojar vistas con distintos formatos como 
*txt, js, css, json, xml, rdf* o *atom*. En este apartado veremos como cambiar 
el comportamiento de la vista para proporcionar estos tipos de ficheros. Como 
venimos haciendo a lo largo del curso, lo haremos añadiendo a la aplicación que
estamos desarrollando una nueva funcionalidad: La incorporación de un canal de
noticias *RSS* para mostrar las últimas versiones de los documentos públicos 
subidos al repositorio.


**Canales de noticias RSS.**
#################################

No vamos a entrar en una descripción detallada de lo que son los canales de 
noticias *RSS* y la redifusión de contenidos. Contamos con que el estudiante ya 
sabe algo acerca del tema, y si no sabe nada suponemos que tiene la suficiente
curiosidad y capacidad de autoaprendizaje para adquirir un mínimo de 
conocimientos que le permita seguir el desarrollo de este apartado. No obstante 
como introducción presentamos el resumen del artículo de la *wikipedia*.	

**“RSS** *es una familia de formatos de* **fuentes** **web** *codificados en*
**XML**. *Se utiliza para suministrar a suscriptores de* **información 
actualizada** *frecuentemente.* *El formato permite distribuir contenido sin 
necesidad de un navegador, utilizando un software diseñado para leer estos 
contenidos RSS* (**agregador**). *A pesar de eso, es posible utilizar el mismo
navegador para ver los contenidos RSS. Las últimas versiones de los principales 
navegadores permiten leer los RSS sin necesidad de software adicional. RSS es 
parte de la familia de los formatos* **XML** *desarrollado específicamente para
todo tipo de sitios que se actualicen con frecuencia y por medio del cual se 
puede compartir la información y usarla en otros sitios web o programas. A esto
se le conoce como* **redifusión web** *o sindicación web (una traducción 
incorrecta, pero de uso muy común).” Fuente: http://es.wikipedia.org/wiki/RSS*

Fundamentalmente, para la implementación de nuestro canal *RSS* lo que hay que 
saber es que tenemos que elaborar un recurso representado en *XML* según la 
especificación *RSS* en el que cada *item* será una versión de un documento 
público almacenado en nuestro gestor documental.


**Especificando el formato de la petición.**
###################################################

Como hemos visto hasta el momento, por defecto las acciones se renderizan con
plantillas con estructura *HTML* que son decoradas con un *layout* determinado.
El nombre de la plantilla sigue el patrón: *{nombre_accion}{valor_devuelto}.php*, 

por ejemplo: *indexSuccess.php.*

Sin embargo, y esto lo decimos ahora por primera vez, esto es una simplificación
del nombre completo que es: *{nombre_accion}{valor_devuelto}.html.php*, 

de manera que el ejemplo anterior sería: *indexSuccess.html.php*. Lo que ocurre 
es que *symfony*, si la plantilla no lleva información del formato, supone que 
se corresponde con *HTML*.

Así pues, como puedes imaginar, si el formato de salida no fuese *HTML*, el
patrón que sigue el nombre de la plantilla es:
*{nombre_accion}{valor_devuelto}.{formato}.php,*

por ejemplo, *indexSuccess.xml.php* representa una plantilla *XML*,
*indexSuccess.json.php* es una plantilla *JSON*, …

Ahora bien, ¿cómo sabe *symfony* qué formato debe aplicar y, por tanto con qué
formato de plantilla debe renderizar la acción? En este apartado descubriremos
una parte de la verdad, dejaremos la otra parte para el momento en que hablemos 
del mecanismo de enrutamiento ya que tiene que ver con este. Se trata de 
especificar  explícitamente en la acción el tipo de petición mediante el método 
*setRequestFormat()* del objeto *sfRequest*:

.. code-block:: bash

	// trozo de código de una acción
	...
	$request → setRequestFormat('xml');
	...

Si la acción que vamos a implementar va a ser renderizada siempre con un mismo
formato basta con que lo indiquemos según acabamos de ver. Pero también puede 
ocurrir que deseemos representar la misma acción con distintos formatos, para lo
cual tendremos que construir tantas plantillas como formatos vayamos a  utilizar.
La selección de un formato u otro se hará en función de lo que indique algún 
parámetro de la petición. Para este último caso lo más correcto es utilizar el
mecanismo de enrutamiento, ya que proporciona una manera directa de asociar 
distintas representaciones según la forma que presente la *URL*. Pero esto lo
veremos más adelante. 

Para los propósitos que perseguimos será suficiente con implementar una acción 
que recoja una lista ordenada por fecha, desde la más actual a la más antigua, 
con todas las versiones de los documentos públicos. Y construir una plantilla 
*XML* asociada a dicha acción que cumpla el estándard de redifusión *RSS*.

Añadimos la acción *rss* al módulo *gesdoc*:

*Trozo de código del archivo: apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executeRss(sfWebRequest $request)
	{
		$request -> setRequestFormat('xml');
	
		$c = new Criteria();
		$c -> add(DocumentosPeer::PUBLICO, 1);
		$c -> addJoin(DocumentosPeer::ID_DOCUMENTO, VersionesPeer::ID_DOCUMENTO);
		$c -> addDescendingOrderByColumn(VersionesPeer::FECHA_SUBIDA);
	
		$this -> versiones = VersionesPeer::doSelect($c);
	}

Fíjate en la línea resaltada en negrita. Como ya hemos explicado dicha línea
indica a *symfony* que la acción debe ser renderizada con una plantilla en 
formato *XML*. Creamos el archivo *rssSuccess.xml.php* con el siguiente contenido:

*Contenido del archivo: apps/frontend/modules/gesdoc/templates/rssSuccess.xml.php*

.. code-block:: php

	<?php echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" ?>
	<rss version="2.0">
	  <channel>
		<title>Canal RSS del Gestor Documental del Curso de Symfony de Mentor.</title>
		<link>http://www.mentor.mec.es</link>
		<description>Este canal RSS ofrece información actualizada sobre los documentos públicos
		que se van incorporando al gestor documental del curso de symfony de mentor</description>
		<image>
			<url>http://localhost/curso_symfony/gestordocumental-1.4/web/images/logo.png</url>
			<link>http://localhost/curso_symfony/gestordocumental-1.4/web/frontend_dev.php/gesdoc</link>
		</image>
		<?php foreach ($versiones as $v): ?>
		<item>
			<title><?php echo $v -> getDocumentos() -> getTitulo() ?>. Version: <?php echo $v -> getNumero() ?></title>
			<link><?php echo 'http://localhost' . url_for('gesdoc/verVersion?id_version='.$v -> getIdVersion()) ?></link>
			<description><?php echo $v -> getDocumentos() -> getDescripcion() ?>. <?php echo $v -> getDescripcion() ?>. Enviado por: <?php echo $v -> getDocumentos() -> getUsuarios() -> getNombre() ?> </description>
			<pubDate><?php echo $v -> getFechaSubida() ?></pubDate>
		</item>
		<?php endforeach; ?>
	  </channel>
	</rss>

Y ya podemos probar nuestro canal *RSS* ejecutando la acción que acabamos de 
construir introduciendo en la barra de direcciones del navegador la *URL*:

``http://localhost/gestordocumental/web/frontend_dev.php/gesdoc/rss``

Casi todos los navegadores modernos incluyen un lector de *RSS*, de manera que 
lo más probable es que al solicitar el canal *RSS* mediante la *URL* anterior
obtengas el resultado bien presentado. Si el navegador que utilizas no tiene 
soporte *RSS*, entonces obtendrás el documento *XML* sin procesar. De todas 
formas, lo habitual para leer los canales *RSS* es utilizar un programa cliente 
denominado agregador *RSS*.

Ya que hemos implementado esta funcionalidad, deberíamos mostrarla al usuario en
la propia aplicación. Lo que haremos es añadir al menú un enlace al canal *RSS*
recién implementado. Utilizaremos además la típica imagen con la que se anuncian
en casi todos los sitios *web* la existencia de un canal *RSS*. Se trata de
añadir al *partial inises/_menu*, dicho enlace:

*Contenido del archivo: apps/frontend/modules/inises/templates/_menu.php*

.. code-block:: php

	| 
	<?php if($sf_user -> hasCredential('escritura')) : ?>
	<?php echo link_to('Nuevo Documento','gesdoc/nuevoDocumento') ?> |
	<?php endif; ?>
	<?php echo link_to('Ayuda','gesdoc/ayuda') ?> |
	<?php if($sf_user -> hasCredential('administracion')) : ?>
	<a href="#">Administración</a> |
	<?php endif; ?>
	<?php echo link_to(image_tag('rss.gif'),'gesdoc/rss') ?>
	<hr/>

Y ahora ya queda expuesto con claridad el canal *RSS*. Fíjate que detrás de toda 
la terminología y teoría que hay detrás de las *RSS*, al final se trata
simplemente de construir un archivo *XML* con unas determinadas etiquetas y 
devolverlo como respuesta al cliente.


**Conclusión**
---------------------

En esta unidad hemos profundizado bastante en la parte controladora y en la vista
de *symfony*. Y ello nos ha permitido agregar a la aplicación una serie de 
funcionalidades que la enriquecen considerablemente. 

En la parte controladora hemos mostrado como los filtros nos permiten un control
preciso a la hora de manipular la respuesta. Pero la cantidad de cosas que con 
ellos podemos hacer no está limitada más que por la imaginación del programador. 
También las *pre-acciones* y *post-acciones* nos permiten organizar el código de 
la aplicación de una manera muy eficiente.

En la parte de la vista hemos introducido los conceptos de *partial*, componente,
y *helpers*, hemos ampliado el concepto de *layout* y la posibilidad de generar 
vistas en otros formatos distintos del *HTML*. Después de esta unidad debería 
quedar claro que las posibilidades del *framework* para construir todo tipo de 
aplicaciones *web* no tienen más límite que el conocimiento que el programador 
adquiera del mismo y de la imaginación a la hora de combinar los conceptos que
hemos estudiado. 

Para ilustrar mediante la práctica los conceptos estudiados, se han realizado 
las siguientes implementaciones: un *partial* que muestra un enlace para que el 
usuario se registre o se desconecte, un componente que muestra quien está 
utilizando la aplicación, un menú y un servicio *web RSS* para la difusión de las
últimas versiones que van añadiéndose al sitio *web*.

Después de esta unidad el estudiante tiene a su disposición un surtido conjunto 
de herramientas para desarrollar sus aplicaciones *web*. No dudes en probar todas 
aquellas ideas y cuestiones que se te hayan podido plantear después de seguir
esta unidad. Comprobarás que las posibilidades de todo lo aprendido van más allá
de los ejemplos que hemos construido para ilustrar los conceptos. 







