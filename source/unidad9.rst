Unidad 9: Desarrollo de la parte de administración.
===================================================

Llegados a este punto sólo nos restan dos unidades para terminar el curso y aún
tenemos que desarrollar la aplicación de administración (*backend*) completamente.
Tal aplicación, como se especificó en la unidad 4, constará de tres módulos: 

* la gestión de los usuario,
* la gestión de tipos de archivos y
* la gestión de categorías,

entendiendo por gestión la consulta, creación, modificación y borrado de 
elementos. Es decir, la aplicación *backend* la compondrán tres módulos que 
realizan las cuatro operaciones básicas que ofrecen los sistemas gestores de base
de datos. Por esta razón a este tipo de módulos se les conoce como módulos *CRUD*
(*Create – Retrieve – Update* y *Delete*). Además la aplicación debe incorporar
un mecanismo de inicio de sesión para garantizar que únicamente los usuarios con 
perfil de administración puedan acceder a ella y utilizar sus funcionalidades. 

A lo largo de esta unidad, aunque te cueste creerlo, construiremos la aplicación
de *backend* completa. Y lo haremos dentro de las aproximadamente 25 páginas que
vienen ocupando cada unidad. Este aparente milagro; desarrollar tres módulos de
administración completos con control de acceso en una sola unidad, será posible
gracias a la reutilización de código a través de los *plugins* de *symfony*, y 
al uso del generador automático de módulos de administración mediante el cual,
como indica su nombre, se construyen al vuelo módulos para la gestión de las 
tablas de las bases de datos incorporadas a nuestro proyecto.

La hoja de ruta que marcará el desarrollo de esta unidad es la siguiente: primero 
convertiremos el módulo de inicio de sesión (*inises*) de la aplicación *frontend*
en un módulo de un *plugin* del proyecto con el objetivo de que podamos utilizarlo
también en la aplicación *backend* que nos disponemos a desarrollar. Cuando, 
después de esta transformación, hayamos comprobado que la aplicación *frontend*
sigue funcionando bien, procederemos a la creación de la aplicación *backend*
reutilizando el *layout*, las *CSS's* que ya utilizamos en la aplicación *frontend*
y el proceso de inicio de sesión que, para entonces, ya forma parte de un *plugin*
del proyecto compartido por ambas aplicaciones. Después generaremos todos los 
módulos *CRUD* utilizando el generador de módulos de administración de *symfony*.
Finalmente, realizaremos algunos pequeños ajustes sobre los módulos generados
automáticamente para adaptarlos a nuestras necesidades.


Transformación del módulo de inicio de sesión en un plugin
----------------------------------------------------------

Los plugins de symfony
^^^^^^^^^^^^^^^^^^^^^^

Los *plugins* de *symfony* ofrecen una importante vía de expansión para los 
proyectos *symfony*. En lugar de buscar una definición precisa, lo cual sería 
bastante complicado y no sé hasta que punto comprensible, vamos a contar qué es
lo que se puede hacer con ellos y como se utilizan.

En primer lugar los *plugins*, como los proyectos, pueden contener todo tipo de
elementos: modelos, formularios, librerías de clases, módulos, *CSS's, javascripts*,
etcétera. Qué elementos incluya cada *plugin* es cuestión de la finalidad del
mismo. Un *plugin* puede ser tan sencillo como una librería de clases o tan 
sofisticado como un *CMS* completo. En ambos casos el *plugin* (y el nombre es 
bastante descriptivo), puede ser fácilmente conectado (enchufado) al proyecto 
*symfony* extendiendo las funcionalidades de este. 

En nuestro caso el *plugin* que desarrollaremos consistirá en un módulo para 
realizar un inicio de sesión y, como *plugin* que será, podrá ser utilizado en 
cualquier aplicación de nuestro proyecto o de cualquier otro proyecto *symfony*
que desarrollemos más adelante. Es más, si pensamos que puede ser útil a otros
desarrolladores podemos pensar en compartirlo a través del repositorio público 
de *plugins* de *symfony*. En este repositorio podemos encontrar cientos de 
*plugins* muy útiles y estables. Así pues un *plugin* de *symfony* puede ser 
pensado como una forma de reutilizar y compartir código, de no reinventar la 
rueda cada vez.

La estructura de directorios de un *plugin* es similar a la de una aplicación. 
Por ello podemos concebir a los *plugins* una forma de organizar el código por
funcionalidad en lugar de por capas. 

Los *plugins* deben ubicarse dentro del directorio *plugins* del proyecto, bajo
una carpeta que representa el nombre del *plugin* y que debe terminar con el 
sufijo *Plugin*. El siguiente esquema muestra la estructura de un posible *plugin*
con dos módulos, varias librerías y recursos *web* (*javascripts, CSS's* e 
imágenes)

*Estructura de un plugin de symfony*

.. code-block:: bash
	
	{nombre_plugin}Plugin 
	   |- config
	   |- lib
	   |  |- model
	   |  |- forms
	   |  |- filter
	   |  |- task
	   |  `- helper
	   |- modules
	   |  |- module_1
	   |  |  |- actions
	   |  |  |- config 
	   |  |  `- templates
	   |  |- module_2
	   |  |  |- actions
	   |  |  |- config 
	   |  |  `- templates
	   |  |
	   |  `- templates 
	   |   
	   `- web
		  |- images
		  |- css
		  `- js


Como ya hemos dicho un *plugin* no tiene por qué contener todos los directorios
mostrados en este ejemplo. O bien puede ser aún más complejo, pero sea como sea 
siempre responderá a la estructura estándar de un proyecto *symfony*. 

La manera de incorporar un *plugin* a un proyecto *symfony* es registrándolo en 
el archivo *config/ProjectConfiguration.class.php*. De hecho si lo abres verás 
que ya existe un *plugin* registrado en el momento de crear el proyecto; el 
*plugin* de *Propel*. Así pues, si quisiéramos utilizar un *plugin* llamado,
pongamos, *cmsPlugin* a nuestro proyecto, lo colocaríamos dentro del directorio 
*plugins*, y lo registraríamos en el archivo 
*config/ProjectConfiguration.class.php* de la siguiente manera:

*Contenido del archivo config/ProjectConfiguration.class.php para registrar un 
plugin llamado cmsPlugin*

.. code-block:: php

	<?php
	
	require_once '/Applications/MAMP/bin/php5/lib/php/symfony/autoload/sfCoreAutoload.class.php';
	sfCoreAutoload::register();
	
	class ProjectConfiguration extends sfProjectConfiguration
	{
	  public function setup()
	  {
		$this->enablePlugins('sfPropelPlugin', 'cmsPlugin');
	  }
	}


A partir de este momento podríamos utilizar las librerías y *recursos web* que
el *plugin* proporcionase en cualquiera de las aplicaciones del proyecto. Pero 
si el *plugin* además contiene módulos y queremos utilizarlos en alguna de las 
aplicaciones del proyecto, debemos indicarlo en el archivo 
*apps/nombre_aplicacion/config/settings.yml*. Supongamos que *cms* y *adminCms*
son dos módulos del *plugin* que estamos utilizando como ejemplo. Entonces, si
deseamos utilizar en una aplicación el módulo *adminCms* lo habilitaríamos 
añadiendo al fichero *settings.yml* de dicha aplicación la siguiente línea:

*Línea añadida al archivo: apps/nombre_aplicacion/config/settings.yml*

.. code-block:: bash

	...
	 all:
	  .settings:
		...
		enabled_modules: [ default, adminCSS ]
	...


.. note::

   El módulo *default* debemos añadirlo si queremos utilizar las acciones comunes
   que *symfony* proporciona para realizar algunas operaciones como pueden ser:
   mostrar que se ha producido alguna violación de seguridad o el famoso *error 
   404* al que se redirige la acción cuando el recurso no existe. Estas  acciones
   puede ser sustituidas por algunas que nosotros hayamos desarrollado para tal 
   fin. 

   En la guía de referencia de *symfony* puedes encontrar un capítulo dedicado 
   a la descripción de cada uno de las directivas del archivo *settings.yml*:

   *http://www.symfony-project.org/reference/1_4/en/*


Nuestro plugin de inicio de sesión
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ahora vamos a aplicar la teoría expuesta en el apartado anterior para crear 
nuestro *plugin* de inicio de sesión. Comenzamos por crear, dentro del directorio
*plugins* del proyecto, una carpeta para alojar el código del *plugin*. La
denominaremos *IniSesPlugin* (recuerda que debe finalizar con el sufijo *Plugin*):

.. code-block:: bash

	# mkdir plugins/IniSesPlugin


Como ya hemos dicho, el *plugin* de inicio de sesión consistirá en un módulo que
será compartido por las dos aplicaciones de nuestro proyecto: *frontend* y 
*backend*. Así pues, siguiendo la estructura de los *plugins* de *symfony* (que
es la misma que la de una aplicación), creamos dentro del directorio *IniSesPlugin*
la carpeta *modules*:

.. code-block:: bash

	# mkdir plugins/IniSesPlugin/modules


Entonces movemos el módulo *inises* de la aplicación *backend* al directorio que
acabamos de crear:

.. code-block:: bash

	# mv apps/frontend/modules/inises plugins/IniSesPlugin/modules


Y acabamos de construir el *plugin* de inicio de sesión. Para que esté 
disponible en la aplicación *frontend* tenemos que registrarlo en el proyecto:

*Contenido del archivo config/ProjectConfiguration.class.php para registrar el
plugin de inicio de sesión*

.. code-block:: php

	<?php
	
	require_once '/Applications/MAMP/bin/php5/lib/php/symfony/autoload/sfCoreAutoload.class.php';
	sfCoreAutoload::register();
	
	class ProjectConfiguration extends sfProjectConfiguration
	{
	  public function setup()
	  {
		$this->enablePlugins('sfPropelPlugin', 'IniSesPlugin');
	  }
	}

Y habilitar el módulo *inises* del *plugin* en la aplicación *frontend*:

*Línea añadida al archivo: apps/frontend/config/settings.yml*

.. code-block:: bash

	...
	 all:
	  .settings:
		...
		enabled_modules: [ default, inises ]
	...


Ahora puedes probar a registrarte y desconectarte en la aplicación *frontend* y
verás que todo sigue funcionando como si no hubiésemos hecho nada. Y es que, al
habilitar el módulo *inises* del *plugin* en la aplicación *frontend*, este se
puede considerar como otro módulo más de la misma.

.. note::

	Los cambios realizados exigen limpiar la cache para que sean efectivos.


Bueno, ahora que hemos comprobado que todo sigue funcionando, hay que realizar 
algunos retoques para que el módulo *inises* pueda ser realmente utilizado por
otras aplicaciones. En efecto, observa que la acción *executeSignin()* de dicho
módulo realiza una redirección a la acción *executeIndex()* del módulo *gesdoc*:

*Código de la acción executeSignin() del archivo:
plugins/IniSesPlugin/modules/inises/actions/actions.class.php*

.. code-block:: php

	public function executeSignIn(sfWebRequest $request)
		{
			$this -> form = new LoginForm();
			
			if ($request->isMethod('post'))
			{
				$datos = $request -> getParameter($this->form -> getName());
				$this->form->bind($datos);
				if ($this->form->isValid())
				{
					$usuario = $this -> compruebaUsuario($datos);
					if($usuario instanceof Usuarios) // Existe el usuario con los datos dados
	
					{
						$this -> getUser() -> setAuthenticated(true);
						$this -> getUser() -> setAttribute('id_usuario', $usuario -> getIdUsuario());
						$this -> asociaCredenciales($usuario);
						$this -> redirect('gesdoc/index');
	
					}
					else
					{
						$this -> mensaje = 'Usuario no autorizado';
					}
				}
			}
		}


Observa la línea resaltada que es donde se hace dicha redirección. Esto tiene
sentido cuando es la aplicación *frontend* la que utiliza el inicio de sesión, 
ya que dispone de un módulo denominado así y es allí donde queremos ir cuando 
el proceso de registro de un usuario en la aplicación *frontend* tiene éxito. 
Pero cuando sea otra la aplicación que haga uso del módulo *inises* el proceso 
fallará, ya que por lo general no existirá un módulo denominado *gesdoc* en la 
nueva aplicación, y la redirección cuando se registre satisfactoriamente un 
usuario debe realizarse al módulo y acción que esta nueva aplicación decida. Para
arreglar esto, y aunque aún no lo hemos explicado, utilizaremos el sistema de
enrutamiento de *symfony*. 

En lugar de redirigir a una acción concreta, realizaremos una redirección a una 
ruta y será la aplicación en cuestión quien defina el módulo y la acción 
correspondiente a esa ruta. Las rutas se especifican en los argumentos de las
funciones que esperan pares módulos/acción (como *redirect(), url_for(), 
link_to()*, etcétera) con el nombre que se les haya asignado precedido por el
carácter '@'. De esa forma *symfony* reconoce que debe buscar un par
módulo/acción en el archivo *apps/nombre_aplicacion/config/routing.yml* bautizado
con el nombre indicado tras el carácter '@'. 

Así pues sustituimos en la acción *executeSignin()* del módulo *inises* la ruta 
concreta *'gesdoc/index'* por la ruta parametrizada *'@pagina_inicial'* (este 
nombre lo hemos decidido nosotros).

*Parametrización de la redirección a la acción @pagina_inicial en el archivo
plugins/IniSesPlugin/modules/inises/actions/actions.class.php*

.. code-block:: bash

	...
	$this -> redirect('@pagina_inicial');
	...


Y en el archivo *apps/frontend/config/routing.yml* añadimos la ruta denominada 
*@pagina_inicial*:

*Trozo del archivo  apps/frontend/config/routing.yml donde se define la ruta 
@ pagina_inicial*

.. code-block:: bash

	...
	pagina_inicial:
	  url:   /inicio
	  param: { module: gesdoc, action: index }
	...


Por la misma razón debemos parametrizar la redirección que se hace en la acción
*executeSignout()* del módulo *inises* hacia *'gesdoc/index'*. Llamaremos a esta
nueva ruta *'@logout'*. Dicha acción quedaría:

*Código de la acción executeSignOut del archivo 
plugins/IniSesPlugin/modules/inises/actions/actions.class.php*

.. code-block:: bash

	public function executeSignOut(sfRequest $request)
	{
		session_destroy();
		$this -> redirect('@logout');
	}


Y tenemos que añadir dicha ruta a la aplicación *frontend*:

*Trozo del archivo  apps/frontend/config/routing.yml donde se define la ruta @ 
logout*

.. code-block:: bash
	
	...
	logout:
	  url:   /salir
	  param: { module: gesdoc, action: index }
	…


Como hemos tocado archivos de configuración debemos borrar la caché de *symfony*
antes de volver a ejecutar la aplicación.

.. code-block:: bash

	# symfony cc
 
 
Y ya puedes comprobar que todo sigue funcionando igual que antes. La diferencia 
es que tenemos parametrizadas las redirecciones del módulo de inicio de sesión 
de manera que es la aplicación la que decide, mediante la definición de las rutas
*@pagina_inicial* y *@logout*, las acciones a las que se debe redirigir el inicio 
de sesión cuando un usuario se registra adecuadamente o cuando desea desconectarse
de la aplicación. Ahora el *plugin* sí es completamente reutilizable. Es 
importante que observes que aunque sean muchas las líneas que ha ocupado explicar
la transformación del módulo de inicio de sesión en un *plugin*, los cambios
realizados sobre el código son muy pocos. Resumiendo:

* Se crea el directorio para alojar el *plugin*

* Se reubica el módulo de inicio de sesión

* Se parametrizan las redirecciones de este módulo con rutas de *symfony*

* Se definen las rutas en la aplicación.

Cualquier aplicación que utilice este *plugin* para llevar a cabo el inicio de
sesión tan solo tendrá que habilitar el módulo *inises* y definir en su archivo
*routing.yml* las rutas *@pagina_inicial* y *@logout*.


Desarrollo de la aplicación de administración (backend).
--------------------------------------------------------

Creación de la aplicación backend
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Según lo especificado en la unidad 4, la aplicación de administración debe 
facilitar la gestión de las categorías, los tipos de ficheros admitidos y los 
usuarios de la aplicación. Además el acceso a estas funcionalidades debe estar 
restringidos a los usuarios con perfil administrador. 

En este apartado construiremos el esqueleto funcional de la aplicación, 
entendiendo por tal lo siguiente:

* el *layout* y el menú de la aplicación,
* las *CSS's* para la visualización de la aplicación,
* el procedimiento de inicio de sesión para garantizar que únicamente los 
  administradores puedan utilizar la aplicación de administración.

Una vez hecho esto generaremos automáticamente los módulos de administración y 
la aplicación quedará prácticamente finalizada.

Comenzamos por generar la aplicación *backend*:

.. code-block:: bash

	# symfony generate:app backend


Ahora vamos a construir el *layout* y el menú de la misma. En realidad lo que
haremos será reutilizar el mismo *layout* de la aplicación *frontend* y modificar
su menú:

.. code-block:: bash

	# cp apps/frontend/templates/layout.php apps/backend/templates
	# cp apps/frontend/templates/_menu.php apps/backend/templates


Y cambiamos el código del menú por el siguiente:

*Código del partial: apps/backend/templates/_menu.php*

.. code-block:: php

	<?php if($sf_user -> hasCredential('administracion')): ?>
		<?php echo link_to('Usuarios','gesusu/index') ?> |
		<?php echo link_to('Categorias','gescat/index') ?> |
		<?php echo link_to('Tipos de archivos','gestip/index') ?> |
		<?php echo link_to('Ayuda','gesdoc/ayuda') ?> |
	<hr/>
	<?php endif; ?>


Reutilizaremos las mismas *CSS's* que hemos aplicado en la aplicación *frontend*.
Esto se indica en el archivo *view.yml* de la aplicación:

Contenido del archivo: apps/backend/config/view.yml

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
	
	  stylesheets:    [ default_administrador.css, admin.css, menu.css ]
	
	  javascripts:    [ ]
	
	  has_layout:     true
	  layout:         layout


Hemos aprovechado para definir los atributos *metas* que se enviarán en las 
respuestas *HTTP* generadas por la aplicación *backend*.

Ahora vamos a definir la seguridad de la aplicación exigiendo que todas sus 
acciones sean seguras por defecto (requieran autentificación) y sólo puedan ser
utilizadas por aquello usuarios con credencial de administración. Como ya hemos
estudiado, todo esto se realiza a través del fichero *security.yml* de la
aplicación:

Contendido del archivo: apps/backend/config/security.yml

.. code-block:: bash

	default:
	  is_secure: true
	  credentials: administracion


Y ahora nos queda implementar el proceso de inicio de sesión, para lo cual 
utilizaremos nuestro recién horneado *plugin IniSesPlugin* que ya tenemos
registrado en el proyecto. Únicamente nos queda habilitar el módulo *inises* en
la aplicación *backend*:

*Línea añadida al archivo: apps/backend/config/settings.yml*

.. code-block:: bash

	...
	 all:
	  .settings:
		...
		enabled_modules: [ default, inises ]
	...


Y ya lo tenemos disponible, pero recuerda que este módulo requiere que se definan 
las rutas *@pagina_inicial* y *@logout* para saber donde tiene que redirigir la 
acción cuando el usuario se registre con éxito y cuando se desconecte. Dichas 
rutas se añaden al archivo *routing.yml* de la aplicación:

Rutas añadidas al archivo: apps/backend/config/routing.yml

.. code-block:: bash

	...
	pagina_inicial:
	  url:   /inicio
	  param: { module: gesusu, action: index }
	
	logout:
	  url:   /salir
	  param: { module: inises, action: signIn }
	...


Hemos decidido que la acción inicial tras el proceso de *login* sea la acción
*index* del módulo de gestión de usuarios *gesusu*, el cual aún tenemos que
desarrollar. Y la acción que se ejecute tras la desconexión sea la acción 
*signIn* del módulo *inises*, el cual  presenta el formulario de inicio de sesión
al usuario.

Con esto ya tenemos lo que hemos llamado esqueleto de la aplicación *backend*
finalizado. Ahora puedes probar la aplicación mediante la siguiente *url*:

.. code-block:: bash

	http://localhost/gestordocumental/web/backend_dev.php/inises/signIn

Te mostrará el formulario de inicio de sesión. Puedes probar a introducir un 
usuario con perfil administrador para probar que todo marcha, el problema es que
como aún no hemos desarrollado el módulo *gesusu, symfony* te advertirá de ello
cuando tenga lugar la redirección. 


El generador automático de módulos de administración.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Casi todas las aplicaciones *web* incorporan una sección para la administración
de la misma. Qué es lo que se vaya a administrar dependerá obviamente de la 
aplicación en sí aunque, generalmente, una parte importante de “lo administrable”
se corresponde con colecciones de elementos almacenados en una base de datos. 
Si, por ejemplo, la aplicación admite el registro de usuarios, será necesario 
una sección para la administración de los mismos. La administración de este tipo 
de colecciones de elementos suele presentar una interfaz común que consiste en 
4 operaciones:

* creación de datos (Create)
* recuperación de datos (Retrieve)
* actualización de datos (Update) y
* borrado de datos (Delete)

Lo que se conoce ampliamente como una interfaz *CRUD*, acrónimo de *Create, 
Retrieve, Update* y *Delete*. 

*Symfony* incorpora una poderosa herramienta mediante la cual se generan de forma 
automática módulos *CRUD* para la administración de los objetos de su modelo.
Estos objetos, como ya hemos aprendido a lo largo del curso, presentan este tipo 
de interfaz a través de los métodos que se muestran en la tabla siguiente: 	

================ ====================== ========================================

Operación        Método                 Ejemplo

================ ====================== ========================================

*Create*         *new()*                *Usuarios → new()*

*Retrieve*       *retrieveByPk()*       *UsuariosPeer → retrieveByPK(5)*
                 *doSelect()*
                 *doSelectOne()*
                 
*Update*         *save()*               *Usuarios → save()*

*Delete*         *delete()*             *Usuarios → delete();*



Gracias a esta interfaz uniforme que presentan los objetos de *Propel* es posible
la generación automática de módulos *CRUD* para cualquier objeto del modelo.

Por otro lado, los módulos *CRUD*, necesitan presentar al usuario formularios para
solicitar los datos que requieren para dar de alta un nuevo objeto, actualizarlo,
eliminarlo o recuperarlo. Como a lo mejor habrás deducido, el generador automático
de *symfony* utiliza los formularios y filtros que se han generado mediante la
tarea *propel:build-forms* y *propel:build-filter*, alguno de los cuales hemos
introducido y utilizado en la unidad anterior. Así pues, las bases sobre las que
se asientan los módulos de administración generados automáticamente son los 
objetos, formularios y filtros del *ORM* que utilicemos en nuestro proyecto: 
*Propel* o *Doctrine*, lo cual proporciona al desarrollador, no solo la
posibilidad de reutilización, sino una gran flexibilidad para cambiar el 
comportamiento de las operaciones *CRUD* mediante la extensión de dichos objetos.

Como veremos en breve, el código que se genera en cada uno de estos módulos es
prácticamente inexistente, y se limita por una parte a extender unas clases bases
comunes a todos los módulos de administración, y por otra a la generación de un 
importante fichero de configuración, denominado *generator.yml*, cuya manipulación
por parte del desarrollador, posibilitará modificar el comportamiento por defecto
del módulo generado. Cuando el *framework* ejecuta uno de estos módulos, construye
a partir de dicho fichero de configuración el código real que será ejecutado en 
el servidor. Dicho código se graba en el directorio *cache* del proyecto la 
primera vez que se solicita la ejecución del módulo y, a partir de ese momento y
hasta que la caché vuelva a ser limpiada mediante la tarea *cache:clear (symfony
cc)*, el *framework* hará uso de este código generado “al vuelo”. 

En ocasiones el módulo generado por defecto cubre las necesidades de la aplicación,
pero la mayor parte de las veces será necesario modificar el fichero 
*generator.yml* para configurar el módulo como deseamos. La principal dificultad
a la que se enfrenta el programador cuando utiliza la generación automática de 
módulos de administración es conocer la enorme cantidad de elementos que admite 
este fichero. Si se desea realizar muchos cambios sobre el módulo por defecto es
imprescindible conocer en profundidad dichos elementos y su sintaxis. 

Además, si la manipulación del fichero de configuración del módulo de 
administración generado automáticamente no fuese suficiente, también podemos
modificar el comportamiento de las acciones y plantillas por defecto mediante 
su redefinición en el fichero de acciones  o plantillas del propio módulo. 
También debemos tener en cuenta que podemos extender tanto los objetos *Propel*
como los formularios para conseguir nuestro propósitos. Con lo cual las 
posibilidades de adaptación de estos módulos sólo tienen como límite el 
conocimiento y habilidad del desarrollador.

Hasta el momento todo ha podido sonar un poco abstracto, no obstante es la base 
teórica mínima necesaria para enfrentarse a la generación automática de módulos
de administración de *symfony*. En los siguientes apartados mostraremos cómo 
utilizar en la práctica esta potente herramienta.


Generación de los módulos de administración
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Y ahora la magia. Primero invocamos el abracadabra:

.. code-block:: bash

	# symfony propel:generate-admin backend Usuarios –-module=gesusu
	# symfony propel:generate-admin backend Tipos –-module=gestip
	# symfony propel:generate-admin backend Categorias -–module=gescat

Y entonces … ¡tachán!, ¡tachán! con todos ustedes … ¡Los módulos de 
administración!. Fin de la aplicación. Bueno casi. Aún nos quedan unos cuantos
retoques y algunas explicaciones. Por lo pronto vamos a probar los módulos que
nos han generado las instrucciones anteriores. Como habrás imaginado cada una de
las líneas genera un módulo de la aplicación *backend* sobre los objetos *Usuarios,
Tipos* y *Categorias* respectivamente y cuyos nombres son *gesusu, gestip* y
*gescat*. 

Para probarlos debemos registrarnos en la aplicación *backend* como usuario
administrador, ya que hemos especificado en los archivos de seguridad de la
misma que todos los módulos requieren, por defecto, autentificación por parte
del usuario. Tras el proceso de inicio de sesión la aplicación mostrará la
pantalla correspondiente a la acción *index* del módulo *gesusu*, que como podrás
ver se corresponde con un listado de usuarios con filtro de búsqueda. Desde el
menú podemos acceder también a las acciones *index* de los módulos *gestip* y
*gescat* que nos muestran listados con filtros de búsqueda para tipos de archivos
y categorías respectivamente. Juega un rato con los tres módulos. Advertirás que
prácticamente tenemos la aplicación resuelta.

Como has podido comprobar cada módulo consiste en dos pantallas; la primera es 
un listado de elementos con un filtro para realizar búsquedas, y la segunda es 
una pantalla de edición para añadir nuevos elementos o editar los que ya existen.
También podemos borrar los elementos uno a uno, o varios de una vez 
seleccionándolos y eligiendo la opción borrar del desplegable que aparece bajo el
listado. Es decir, se pueden realizar las cuatro operaciones básicas de gestión
*C-R-U-D*.

Una vez que salgamos de nuestro asombro caeremos en la cuenta de hay unas pocas
cosas que no cuadran. Para comenzar la interfaz se presenta en inglés. Así que
vamos a realizar un listado de las cosas que nos gustaría modificar. El resto de
la unidad explicará como realizar estos cambios. De esta manera ofreceremos de
una forma operativa la técnica a seguir para trabajar con los módulos de
administración generados automáticamente. 


Cambios propuestos para adaptar los módulos de administración a nuestras necesidades.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Vamos a construir una lista de tareas (*TO-DO*) con los cambios que nos gustaría
realizar sobre los módulos generados:

**Para todos los módulos**

* Presentar la interfaz en castellano.

* Los títulos de las pantallas se corresponden con los nombre de los módulos 
  (*Gesusu List, New Gesusu, Edit Gesusu*), lo cual no es muy presentable, sería
  preferible títulos más descriptivos como *Listado de usuarios, Edición del 
  usuario 'anselmo'*, etcétera.

* Los listados muestran páginas de 20 elementos. Deseamos reducirlos a 10.

**Módulo gesusu**

* **Listado**

  * Quitar el campo *'password'* del listado, ya que no da ninguna información
    útil y ocupa un espacio innecesario.
  * Quitar el campo *'password'* del filtro, ya que no tiene sentido realizar una
    búsqueda por un *password* encriptado.
    
* **Edición**

  * Convertir el campo *'perfil'* en un desplegable con los valores *'lector', 
    'autor'* y *'administrador'*, para evitar que se introduzcan valores que no 
    reconoce la aplicación.
  * Cambiar el proceso de grabado de los datos para que el valor del *'password'*
    que introduzca el usuario administrador se grabe en la base de datos 
    encriptado con el algoritmo *MD5*, ya que si no se hace así los usuarios que
    se den de alta mediante este módulo no podrán registrarse jamás en la
    aplicación.

**Módulo gescat**

* **Listado**

    Quitar del filtro el elemento *'Documento categoria list'*. Este campo 
    representa las relaciones de cada categoría con los documentos existentes. 
    En nuestro caso, el número de documentos que se registrarán será muy grande 
    y, por tanto, este campo de búsqueda será prácticamente inmanejable.

* **Edición**

    Quitar el elemento *'Documento categoria list'*, ya que es el usuario que 
    sube documentos quien realiza la asociación de categorías.

**Módulo tipos**

No necesita más cambios que los que se han propuesto comunes a todos los módulos.


Estructura de un módulo de administración generado automáticamente.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Antes de emprender las acciones pertinentes para realizar los cambios que hemos 
propuesto en el apartado anterior, echaremos un vistazo a la estructura de uno 
de los módulos de administración generados automáticamente. Pongamos por caso 
el módulo de gestión de tipos de archivos. 

Los módulos de administración generados automáticamente, como todos los módulos 
de *symfony*, estan compuesto por los directorios *actions* y *templates*. Además
requieren el directorio de configuraciones (*config*) y el de librerías (*lib*):

*Estructura de un módulo de administración generado automáticamente:*

.. code-block:: bash

	gestip
	|-actions
	| `-actions.class.php
	|-config
	| `-generator.yml
	|-lib
	| |-gestipGeneratorConfiguration.class.php
	| `-gestipGeneratorHelper.class.php
	`-templates


Si abres cada uno de los ficheros que componen el módulo podrás comprobar que, 
salvo *generator.yml*, están prácticamente vacíos, únicamente contienen 
declaraciones de clases que derivan de otras clases base. Además no hay ningún 
archivo de plantilla. La razón de todo esto es que el auténtico código que se
ejecuta cuando el módulo es invocado no es exactamente este, sino que sirve como
“semilla” para la generación “al vuelo” del código que realmente será ejecutado
y que se almacena en el directorio *cache* de la aplicación. La generación de
este nuevo código se realiza según lo especificado en el archivo de configuración
*generator.yml*, el cual podemos modificar para cambiar el comportamiento por
defecto del módulo. Si quieres ver este código generado “al vuelo” no tienes más
que ejecutar al menos una vez el módulo en cuestión y navegar por el directorio
*cache*. En el caso del módulo *gestip*, puedes ver el código en 

*cache/backend/dev/modules/autoGestip/*

si has utilizado el controlador de desarrollo, o en

*cache/backend/prod/modules/autoGestip/*

si utilizaste el controlador de producción.

Ahora puedes ver código contundente, puro y duro, con acciones concretas y gran
cantidad de elementos parciales en las vistas (*partials*) que implementan de 
una manera modular las vistas arrojadas por estos módulos de administración. 
Echa un vistazo a este código, es un buen ejemplo de código bien horneado, 
elegante y bien refactorizado. 

La siguiente tabla muestra el nombre y un pequeño resumen de las acciones que 
se generan en los módulos de administración:

======================= ========================================================
**Acción**              **Descripción**
======================= ========================================================

*executeIndex()*        Elabora un listado paginado de elementos. 

*executeFilter()*       Ejecuta el filtrado con los datos que les llega desde el
                        formulario de búsqueda de la pantalla donde se listan 
                        los elementos
                        
*executeNew()*          Acción que se ejecuta cuando se solicita la creación de
                        un nuevo elemento. Muestra un formulario de edición 
                        vacío.
                        
*executeCreate()*       Realiza el grabado (creación) del elemento cuyos datos 
                        se han introducido en el formulario enviado por la 
                        acción anterior.
                        
*executeEdit()*         Acción que se ejecuta cuando se solicita la edición de
                        un elemento existente.
                        
*executeUpdate()*       Realiza el grabado (actualización) del elemento cuyos 
                        datos se han introducido en el formulario enviado por la
                        acción anterior.
                        
*executeDelete()*       Realiza el borrado de un elemento

*executeBatch()*        Realiza la acción por lotes seleccionada en el 
                        desplegable de la pantalla donde se muestra el listado
                        de elemento.
                        
*executeBatchDelete()*  Realiza la acción de borrado por lotes. En realidad, 
                        esta acción es llamada por la acción anterior cuando se
                        ha elegido la opción 'Borrar' del desplegable de 
                        acciones por lotes.
                        
*processForm()*         Procesa el formulario de edición o creación de elementos

*getFilters()*          Devuelve un array con los filtros aplicados que están
                        almacenados en la sesión.
                        
*setFilters()*          Define los filtros a aplicar en el listado

*getPager()*            Devuelve el objeto *Pager* (paginado)

*getPage()*             Devuelve la página actual del paginado

*setPage()*             Define la página que se debe mostrar en el paginado

*buildCriteria()*       Construye el criterio con el listado

*addSortCriteria()*     Añade un criterio de ordenación para el listado

*getSort()*             Devuelve la columna por la que se está ordenando 
                        actualmente el listado
                        
*setSort()*             Define la columna por la que debe ordenarse el listado


Todas estas funciones pueden ser redefinidas en el archivo de acciones del módulo
*apps/nombre_aplicacion/modules/nombre_modulo/actions/actions.class.php*, el 
cual, como ya hemos visto, originalmente está vacío. Es decir, si creamos una
función en el archivo de funciones que se llame como algunas de las que hemos 
enumerado en la tabla anterior, *symfony* ejecutará esta nueva acción en lugar 
de la existente en el archivo de acciones generado al vuelo y ubicado en el 
directorio *cache*. Así pues, si deseamos cambiar el comportamiento de una de
estas acciones podemos copiar el código del fichero de la caché en el archivo 
de acciones del módulo y modificarlo a nuestro antojo.

La siguiente tabla muestra las plantillas y *partials* que se generan el los 
módulos de administración:

=================== ============================================================
Plantilla           Descripción
=================== ============================================================

*_assets.php*       Arroja las *CSS's* y *Javascript* que utilizará la plantilla

*_filters.php*      Pinta el filtro de búsqueda

*_filters_field.php*Pinta un campo del filtro de búsqueda

*_flashes.php*      Pinta los mensajes *flash*

*_form.php*         Pinta el formulario de edición o creación

*_form_actions.php* Pinta las acciones del formulario de edición o creación

*_form_field.php*   Pinta un campo del formulario de edición o creación

*_form_fieldset.php*Pinta un conjunto de campos del formulario de edición o
                    creación
                    
*_form_footer.php*  Pinta el pie de formulario de edición o creación

*_form_header.php*  Pinta la cabecera del formulario de edición o creación

*_list.php*         Pinta el listado de elementos

*_list_actions.php* Pinta las acciones del listado de elementos

*_list_batch_actions.php* Pinta la lista de acciones por lotes del listado 
                          de elementos
                          
*_list_field_boolean.php* Pinta un campo booleano en la lista de elementos

*_list_footer.php*  Pinta el pie de la lista de elementos

*_list_header.php*  Pinta la cabecera del listado de la lista de elementos

*_list_td_actions.php* Pinta las acciones sobre un elemento de la lista de
                       elementos
                       
*_list_td_batch_actions.php* Pinta las casillas de verificación en cada fila
                             de la lista de elementos
                             
*_pagination.php*   Pinta el paginado de la lista de elementos

*editSuccess.php*   Plantilla que pinta la vista de edición

*indexSuccess.php*  Plantilla que pinta vista con el listado de elementos

*newSuccess.php*    Plantilla que pinta la vista de creación



Como puedes observar las tres plantillas principales están fragmentadas en
numerosos *partials* que permiten un control flexible sobre su aspecto final.
Si deseamos cambiar algunas de estas plantillas o *partials* no tenemos más que
crear un fichero con el nombre de la  plantilla o *partial* en el directorio 
*templates* del módulo (*apps/nombre_aplicacion/modules/nombre_module/templates*)
y escribir el código correspondiente. El *framework* utilizará este nuevo fichero
en lugar del creado “al vuelo” en el directorio *cache*. Como punto de partida 
del código de estos ficheros podemos utilizar una copia del original (el del 
directorio *cache*).

Por tanto las posibilidades de modificación son espectaculares. De todas formas, 
antes de redefinir una función de las acciones o una plantilla/*partial*, lo más
natural es procurar realizar la modificación a través del archivo de
configuración *generator.yml*, el cual nos permite un amplio control sobre el
comportamiento del módulo.

En definitiva, combinando la capacidad de control que ofrece el fichero
*generator.yml*, las posibilidad de redifinir las acciones y plantillas del
módulo y la posibilidad de modificar y extender los objetos, los formularios y
los filtros de *Propel*, podremos hacer prácticamente cualquier cosa que se nos
ocurra a partir de los módulos de administración generados automáticamente. La
aplicación *frontend* que hemos desarrollado a lo largo del curso, por ejemplo,
se podría haber construido a partir de un módulo de administración generado
automáticamente sobre el objeto *Documentos*. Aunque dado las peculiaridades de
dicha aplicación se precisaría un conocimiento bastante profundo de esos módulos
de administración, conocimiento que únicamente con la práctica y mostrando una
actitud de aprendizaje continuo y autónomo se puede lograr.


El fichero de configuración generator.yml
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Antes de enfrentarnos a la implementación de las modificaciones propuestas en 
el apartado 2.4 vamos a realizar una descripción básica del fichero de 
configuración *generator.yml*, a partir del que se pueden realizar muchísimos 
cambios sobre el comportamiento por defecto de los módulos generados 
automáticamente. De hecho es la primera vía que debemos tomar cuando el módulo 
de administración no satisface completamente nuestras necesidades. 

El número de opciones disponibles es enorme y no vamos a tratarlas todas. La
guía de referencia de *symfony* es el recurso más completo y organizado donde 
podemos encontrar todos los elementos admitidos por el fichero de configuración
*generator.yml* así como su sintaxis. En este apartado nos limitaremos a 
describir la estructura de dicho fichero y los elementos más usados.

Comenzaremos por echar un vistazo al contenido del fichero *generator.yml*
original, antes de realizar ningún cambio:

*Contenido del fichero:
apps/nombre_aplicacion/modules/nombre_modulo/config/generator.yml*

.. code-block:: bash

	generator:
	  class: sfPropelGenerator
	  param:
		model_class:           Tipos
		theme:                 admin
		non_verbose_templates: true
		with_show:             false
		singular:              Tipos
		plural:                Tiposs
		route_prefix:          tipos
		with_propel_route:     1
		actions_base_class:    sfActions
	
		config:
		  actions: ~
		  fields:  ~
		  list:    ~
		  filter:  ~
		  form:    ~
		  edit:    ~
		  new:     ~


La modificación de los módulos de administración se lleva a cabo añadiendo 
elementos a cada una de las 7 subsecciones de que consta la sección *config* del
archivo anterior. Cada subsección define una parte del módulo de administración.

============== =================================================================
Subsección     Descripción
============== =================================================================

*actions*      Define las acciones por defecto tanto del listado de elementos 
               como del formulario de edición/creación
               
*fields*       Configuración de los campos del objeto (tabla) que se gestiona

*list*         Configuración del listado de elementos

*filter*       Configuración del filtro de búsqueda

*form*         Configuración del formulario de edición/creación

*edit*         Configuración específica de la pantalla de edición de elementos

*new*          Configuración específica de la pantalla de creación de elementos


*Columnas reales y virtuales**

Hay muchas opciones que toman como argumento una lista de campos. Cada campo 
puede ser el nombre de una columna real o virtual. Una columna real es aquella
que existe en la definición de la tabla representada por el modelo en cuestión,
mientras que la columna virtual no existe como tal. En ambos casos es necesario 
que se haya definido en el modelo correspondiente un método *getter*
(*get{NombreColumna()*) con el nombre de la columna que se quiere mostrar. En 
el caso de que la columna sea real, estos métodos ya están definidos en el modelo.
Pongamos a nuestro modelo como ejemplo: para el modelo *Usuarios*, que representa
registros de la tabla *usuarios*, ya existen *getters* para las columnas reales
*nombre, apellidos , username, password* y *perfil*. Pero además podemos definir
columnas virtuales sin más que definir nuevos *getters*. Supongamos que deseamos 
mostrar las iniciales de del nombre y apellidos del usuario. Podemos definir el
*getter getIniciales()* de manera que devuelva las *iniciales* que deseamos. 
Entonces podremos tratar en el archivo de configuración el término  iniciales 
(que es una columna virtual) como cualquier otro campo del modelo.

**Placeholders**

Otras opciones toman como argumentos los denominador *placeholders* que son 
cadenas de la forma *%%NAME%%*, donde la cadena *NAME* puede ser cualquier cosa 
que pueda ser convertida a un *getter* existente. Por ejemplo, si estamos
trabajando en el módulo de administración del modelo *Usuarios*, el *placeholder
%%nombre%%* será reemplazado por el resultado de ejecutar *$usuario → getNombre()*.
Los *placeholders* son dinámicamente reemplazados en tiempo de ejecución según 
el objeto asociado con el contexto actual.

**Credenciales**

Las acciones que se muestran tanto en el listado como en los formularios de 
edición y creación, pueden ser ocultadas en función de las credenciales del
usuario. Esto se hace mediante la opción *credential*. Es importante indicar que
esta opción del fichero *generator.yml* únicamente se ocupa de ocultar las 
acciones, no de evitar su ejecución. Es decir, si introducimos directamente la 
*url* que provoca la acción que se ha ocultado mediante la opción *credential*
del módulo, dicha acción se ejecutará. La manera de evitar esto, como habrás 
podido deducir, es mediante la creación de un fichero *security.yml* para el
módulo con las opciones adecuadas de seguridad.

**Herencia de la configuración.**

Hemos visto que la configuración del módulo de administración se establece en 7
subsecciones. Pues bien, dicha configuración se realiza en cascada según las 
siguiente reglas:

* las subsecciones *new* y *edit* derivan de la subsección *form*, que a su vez 
  deriva de *fields*,

* la subsección *list* deriva de *fields* y

* la subsección *filter* deriva de *fields*.

El término herencia tiene el significado que se le da en la *POO*, es decir, los
elemento derivados heredan las propiedades de los elementos padres por defecto, 
y si es necesario se puede cambiar este comportamiento en la definición de ellos
mismos.

En la guía de referencia de *symfony* encontrarás todas las opciones que permiten
cada una de la subsecciones de configuración. Utilizaremos algunas de ellas para 
realizar las modificaciones que hemos propuesto en el apartado 2.4.


Implementación de las modificaciones propuestas
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Llegó el momento de aplicar toda la teoría anterior para realizar las 
modificaciones que hemos propuesto en el apartado 2.4. Dejaremos la traducción
de la interfaz para la próxima unidad, ya que allí trataremos el tema de la 
internacionalización de aplicaciones con *symfony*. Para las demás modificaciones 
ya conocemos las herramientas necesarias.

Ajustes globales
################

**Títulos**

Lo primero que haremos es cambiar los títulos de las pantallas por otros más 
apropiados. Para lo cual utilizamos la opción *title*, que está disponible en las
subsecciones *list, edit* y *new*.

*Uso de la opción title al fichero de configuración:
apps/backend/modules/gesusu/config/generator.yml*
 
.. code-block:: bash

	... 
	list:
	 title: Listado de Usuarios
	...
	edit:
	 title: Edición del usuario %%nombre%% %%apellidos%%
	new:
	 title: Nuevo Usuario


Observa el uso de los *placeholders %%nombre%%* y *%%apellidos%%* en el título
para la pantalla de edición. Cuando se edita un usuario, tenemos disponible un
objeto usuario determinado. Cuando se genera “al vuelo” el código del módulo, 
*symfony* interpreta los *placeholders* anteriores como *$usuario → getNombre()*
y *$usuario → getApellidos()* respectivamente.

Hacemos lo mismo con los módulos *gestip* y *gesusu*:

Uso de la opción *title* al fichero de configuración:
apps/backend/modules/gestip/config/generator.yml

.. code-block:: bash

	... 
	list:
	 title: Listado de tipos de archivos permitidos
	...
	edit:
	 title: Edición del tipo %%nombre%%
	new:
	 title: Nuevo tipo de archivo permitido


Uso de la opción *title* al fichero de configuración:
apps/backend/modules/gescat/config/generator.yml

.. code-block:: bash

	... 
	list:
	 title: Listado de categorías
	...
	edit:
	 title: Edición de la categoria %%nombre%%
	new:
	 title: Nueva categoría 


De nuevo hemos aplicado *placeholders* para definir el título de la pantalla de
edición tanto de tipos de archivos permitidos como de categorías. 

Ahora puedes probar a navegar por todas las pantallas de la aplicación y 
comprobar el resultado de las modificaciones realizadas en los ficheros de
configuración.

**Paginado**

A continuación vamos a definir paginados de 10 elementos en cada uno de los
módulos de administración. La opción *max_per_page*, disponible en la subsección 
*list* ajusta este comportamiento:

Uso de la opción max_per_page en los ficheros de configuración: 
apps/backend/modules/ges{usu,tip,cat}/config/generator.yml

.. code-block:: bash
	
	...
	list:
	  ...
	  max_per_page: 10


Ahora puedes comprobar como los listados se generan con un paginado y con 10 
elementos como máximo.


Ajustes del módulo usuario
##########################

**Eliminación de los campos *id_usuario* y *password* del listado**

Vamos a eliminar los campos *id_usuario* y *password*, ya que no ofrecen 
información relevante, además vamos a incluir una columna virtual para mostrar 
las iniciales del usuario que, aunque tampoco ofrece ninguna información
relevante, si que muestra el uso de columnas virtuales.

La columna virtual se crea sin más que añadir un nuevo *getter* al modelo
*Usuarios*:

*Getter añadido a la clase Usuarios (archivo: lib/Usuarios.php)*

.. code-block:: bash

	... 
	public function getIniciales()
	{
		return substr($this -> getNombre(), 0,1).'.'.substr($this -> getApellidos(), 0,1).'.';
	}
	...
 
 
Y ahora utilizamos la opción *display*, la cual requiere el listado de campos que
serán mostrados. Esta opción está disponible en las subsecciones: *list, form,
filter, edit* y *new*. Como queremos actuar sobre el listado definimos la opción
*display* sobre la subseccion *list*:

*Uso de la opción display sobre la subsección list en el fichero de configuración:
apps/backend/modules/gesusu/config/generator.yml*

.. code-block:: bash

	... 
	list:
	  ...
	  display: [ =nombre, apellidos, username, iniciales, perfil ]
	  ...


Fíjate que la columna iniciales se utiliza como un campo más, *symfony* se
encargará de utilizar para cada campo de la lista un *getter* que coincida con
él. Observa además que hemos colocado delante del campo nombre el carácter '='. 
Esto provoca que el listado convierta el campo seleccionado (*nombre*, en este 
caso) en un *link* que enlaza con la pantalla de edición del registro.

**Eliminación del campo *password* del filtro de búsqueda**

De nuevo se trata de usar la opción *display*, pero esta vez en la subsección
*filter*:

*Uso de la opción display sobre la subsección filter en el fichero de 
configuración: apps/backend/modules/gesusu/config/generator.yml*

.. code-block:: bash

	...
	filter:
	  display: [ nombre, apellidos, username, perfil ]
	...


Y volvemos a probar.

**Convertir el campo *perfil* de los formularios de edición y creación en un
desplegable con los valores lector, autor y usuarios**

En realidad esto no es algo que podamos hacer a través del fichero de 
configuración. El código que se genera se limita a utilizar el formulario de 
*Propel* correspondiente, en este caso, al modelo *Usuarios*. Por tanto lo que 
debemos modificar es dicho formulario, que es el responsable de pintar los 
distintos *widgets* correspondientes a cada uno de los campos del formulario.
Se trata de redefinir tanto el *widget perfil*, que en el formulario base se ha
definido como una caja de texto (*sfWidgetFormInput*),  como el validador *perfil*, 
definido originalmente como un validador de cadenas (*sfValidatorString*), y 
colocar en su lugar un *widget* de selección y un validador apropiado a dicha
selección:

*Código del archivo: lib/form/UsuariosForm.class.php en el que se redefine el
widget perfil y el validador correspondiente.*

.. code-block:: php

	<?php
	
	class UsuariosForm extends BaseUsuariosForm
	{
	  public function configure()
	  {
		  $this -> widgetSchema['perfil'] = new sfWidgetFormChoice(array(
			  'choices'  => array('lector' => 'lector','autor' => 'autor','administrador' => 'administrador'),
			  'multiple' => false,
			  'expanded' => false
		  ));
	
		  $this -> validatorSchema['perfil'] = new sfValidatorChoice(array(
			  'choices' => array('lector', 'autor', 'administrador')
		  ));
	  }
	}

Ahora puedes comprobar como las pantallas de edición y creación del módulo de 
gestión de usuarios muestran el campo *perfil* como un desplegable. Es importante
que comprendas que la forma en que se muestran los campos del formulario no es 
responsabilidad del módulo en sí, sino que la responsabilidad es del formulario
donde se definen los *widgets* y *validadores*. El módulo no hace más que utilizar
el formulario.

**Encriptación MD5 del password**

Esto que suena complicado es, en realidad, de lo más sencillo de todo lo que 
estamos haciendo. Lo importante es saber donde hay que realizar el cambio. Y para 
ello tenemos que reflexionar y deducir cual es el elemento responsable de dicho
cambio. Pensamos un poco y llegamos a la conclusión de que el elemento encargado
de definir el valor del campo *password* es el método *setPassword() (setter)*
del objeto *Usuario*. Como cualquier otro *setter* recibe como argumento el valor
que deseamos para ese campo y se almacena en la base de datos cuando es invocado 
el método *save()* del objeto. Lo que haremos, entonces, es redefinir el método 
*setPassword()* para que en lugar de asignar el valor del argumento, asigne
resultado de aplicar el algoritmo *MD5* a dicho valor. Para realizar dicha
redefinición partimos del código original de la función *setPassword()* en la
clase base *BaseUsuarios*:

*Código original de la función setPassord() en el archivo
lib/model/om/BaseUsuarios.php*

.. code-block:: php

	public function setPassword($v)
	{
		if ($v !== null) {
			$v = (string) $v;
		}
	
		if ($this->password !== $v) {
			$this->password = $v;
			$this->modifiedColumns[] = UsuariosPeer::PASSWORD;
		}
	
		return $this;
	} // setPassword()

Analizando un poco el código podemos concluir que la primera asignación 
simplemente garantiza que en adelante la función utilice una variable de tipo 
*string*. Más adelante encontramos la auténtica asignación en la línea siguiente:

*Línea donde se realiza la asignación del atributo password del objeto Usuarios 
en el archivo: lib/model/om/BaseUsuarios.php*

.. code-block:: bash

	...
	$this->password = $v;
	…


Lo que haremos será copiar el código de esta función en la clase hija Usuario 
y modificar la línea anterior por la siguiente:

.. code-block:: bash

	$this->password = md5($v);


La cual asigna al atributo *password* del objeto *Usuario* la encriptación *MD5*
del argumento pasado a la función. Nos quedará lo siguiente:

*Código  modificado de la función setPassword() de la clase Usuarios en el
archivo lib/model/Usuarios.php*

.. code-block:: bash

	public function setPassword($v)
	{
		if ($v !== null) {
			$v = (string) $v;
		}
	
		if ($this->password !== $v) {
			$this->password = md5($v);
			$this->modifiedColumns[] = UsuariosPeer::PASSWORD;
		}
	
		return $this;
	} // setPassword()


Y de nuevo a probar. Cuando se envía el formulario de edición o creación de
usuarios, el campo *password* se guarda codificado en MD5.


Ajustes del módulo de gestión de categorías.
############################################

Queremos eliminar del formulario el campo *documento_categoria_list*. Pues vamos 
al formulario *CategoriasForm* y lo eliminamos:

*Código del formulario de categorías definido en el archivo: 
lib/form/CategoriasForm.class.php*

.. code-block:: php

	<?php
	
	class CategoriasForm extends BaseCategoriasForm
	{
		public function configure()
		{
			unset($this -> widgetSchema['documento_categoria_list']);
			unset($this -> validatorSchema['documento_categoria_list']);
		}
	}


Y ya está, ya no aparece en las pantallas de edición ni de creación de categorías.

Ahora debemos quitar el campo *documento_categoria_list*  del filtro de búsqueda
de la pantalla donde se listan las categorías. Una posible solución sería
indicarlo a través de la opción *display* de la subsección *filter*:

.. code-block:: bash

	...
	filter:
	  display: [ nombre ]
	...


Aunque esto funciona, como puedes comprobar, lo más correcto es eliminar el campo 
del formulario *CategoriasFormFilter* que define el filtro de búsqueda, de la
misma forma que hemos hecho con el formulario *CategoriasForm*.

*Código del formulario de búsqueda de categorías definido en el archivo:
lib/filter/CategoriasFormFilter.class.php*

.. code-block:: php

	<?php
	
	class CategoriasFormFilter extends BaseCategoriasFormFilter
	{
		public function configure()
		{
			unset($this -> widgetSchema['documento_categoria_list']);
			unset($this -> validatorSchema['documento_categoria_list']);
		}
	}


Ahora ya no es necesaria la opción *display* de la subsección *filter*.


Ajustes del módulo de gestión de tipos de ficheros permitidos
#############################################################

Aunque en un principio dijimos que, a excepción de los títulos de las pantallas
y de la traducción al castellano de la interfaz, no íbamos a realizar 
modificaciones en este módulo, para hacerlo homogéneo y coherente con los
anteriores eliminaremos el campo *id_tipo* del listado de elementos:

*Uso de la opción display sobre la subsección filter para eliminar el campo
id_tipo en el fichero: apps/backend/modules/gestip/config/generator.yml*

.. code-block:: bash

	...
	filter 
	  display: [ =nombre, tipo_mime ]
	...


Conclusión
----------

A lo largo de la unidad hemos desarrollado completamente la aplicación de
administración, denominada *backend*, del gestor documental. Ya disponemos, por 
tanto, de una versión completa del gestor documental que cumple todos los 
requisitos propuestos en la unidad 4.

Construyendo la aplicación de administración, la unidad que acabas de finalizar
ha demostrado la potencia desarrollada cuando la arquitectura del *software* está 
diseñada para reutilizar y compartir código. Uno de los objetivos de la 
programación orientada a objetos es precisamente lograr un código reusable en 
el que cada objeto realice adecuadamente las funciones para las que ha sido
diseñado y colabore eficazmente con sus objetos asociados. Esto es lo que hace 
*symfony* en su conjunto, lo que se ha mostrado a lo largo del cuso y, de forma 
contundente, a lo largo de esta unidad.

Primero convertimos, con muy poco esfuerzo, el módulo de inicio de sesión de la 
aplicación *frontend* en un módulo compartido por cualquier otra aplicación 
gracias a los *plugins* de *symfony*. Ello hizo posible que en cuestión de minutos
tuviésemos la aplicación *backend* provista de un mecanismo para realizar el
registro de usuarios y el correspondiente inicio de sesión. Es más, este mismo
*plugin* de inicio de sesión lo puedes reutilizar en cualquier otra aplicación 
que desarrolles. Y prácticamente cualquier aplicación *web* requiere el registro
de usuarios. Así que ya tienes un problema prácticamente resuelto para tus futuras
aplicaciones.

En segundo lugar hemos construidos tres módulos de administración para la gestión
otras tantas tablas en menos de cinco minutos (todo depende de lo veloz que seas
tecleando la frase: 
*symfony propel:generate-admin backend Usuarios –-module=gesusu*), y  hemos 
explicado las bases teóricas necesarias para modificar a nuestro antojo el 
comportamiento por defecto de estos módulos, las cuales se basan en:

* conocer las posibilidades del fichero de configuración del módulo de 
  administración *generator.yml*,

* redefinir y extender, si fuese necesario, las acciones y plantillas del código
  generado “al vuelo” a partir del archivo *generator.yml*,

* redefinir y extender las clases del modelo, los formularios y los filtros de 
  *Propel*.

Además hemos ilustrado estos puntos adaptando a nuestras necesidades los módulos 
de administración. A medida que los utilices y estudies la herramienta de 
generación automática de módulos de administración irás descubriendo nuevas y
poderosas posibilidades que ofrecen. Esto, por supuesto, es extensible al resto
del *framework* y a cualquier asunto relativo al desarrollo de software. 
Practicar, investigar, ensayar, errar, conseguir resultados y asimilarlos es la 
mejor, si no la única receta, que te permitirá desarrollar sistemas software de 
calidad.






