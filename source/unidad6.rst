**Unidad 6: La sesión de usuario en el servidor. El inicio de sesión.**
===========================================================================

Terminamos la unidad anterior revelando un problema que se ha presentado cuando 
pedimos una nueva página del listado después de realizar una búsqueda. O cuando 
mostramos los metadatos de un documento y volvemos de nuevo al listado a través
del enlace *'volver'* de la pantalla que muestra dichos metadatos. Y es que en
esos casos se vuelve a mostrar un listado con todos los documentos existentes,
sin tener en cuenta el filtro que se aplicó con anterioridad. 

Además, por el momento, la aplicación no “sabe” qué usuario la está utilizando
por lo que no muestra los datos del mismo en la cabecera, tal y como se ha 
especificado en los requisitos y en los bocetos de las pantallas. Además, debido 
a este desconocimiento, cualquier persona podría utilizar todas las
funcionalidades de la aplicación. Para evitar este grave problema de seguridad 
y satisfacer los requisitos U01, U02 y U06 debemos diseñar e implementa algún 
tipo de control de acceso.

Aunque aparentemente inconexos, estos dos problemas se resuelven con un mismo 
elemento clave de todas las aplicaciones *web*: **la sesión de usuario en el 
servidor**. Este será el concepto que abordaremos y exploraremos en la presente
unidad, donde  resolveremos el problema del filtro perdido e incorporaremos el
mecanismo mediante el cual los usuarios se identifican y quedan reconocidos por
la aplicación hasta que se desconectan de la misma, es decir durante toda la 
**sesión**.


**La sesión de usuario en el servidor**
----------------------------------------------

Las aplicaciones *web* pertenecen a un tipo arquitectura de software conocida 
como cliente–servidor; la aplicación reside en un servidor que envía la interfaz
de usuario y los datos al cliente (el navegador *web*) que la solicita. Cliente 
y servidor se encuentran físicamente en distintos lugares del mundo pero se 
comunican a través de la red mediante el uso de *HTTP* que es un protocolo sin 
estado. Esto último significa que no guarda información sobre conexiones 
anteriores, es decir que cada petición realizada por el cliente debe especificar
todos los datos necesarios para que la aplicación que reside en el servidor sepa 
como debe responder. O dicho de otra manera, cada petición es independiente de 
las anteriores, no “sabe” nada de ellas.

Según lo que acabamos de explicar, si la aplicación necesita conocer, por ejemplo,
quien la está utilizando y en qué estado se encuentra, el cliente debe encargarse
de comunicárselo en cada petición que realice. En principio esta limitación puede
parecer engorrosa y peligrosa. Engorrosa por que pueden ser muchos los datos que
se necesiten para especificar tanto a un usuario como a un determinado estado.
Pensemos en nuestra aplicación; para satisfacer los requisitos se necesita el
nombre los apellidos y el perfil del usuario. Por otra parte, en el caso del 
filtro de búsqueda que hemos implementado se puede llegar a requerir hasta 5 
parámetros (título, descripción, autor, tipo y categoría). Y peligrosa por que 
los datos deberían circular por la red cada vez que el cliente realiza una
petición *HTTP* al servidor o este último devuelve una respuesta al cliente
comprometiendo la seguridad de la aplicación.

Afortunadamente existe un mecanismo que simplifica la cantidad de datos que
circulan por la red y permite preservar los datos claves que necesita la
aplicación durante el tiempo que un cliente determinado la use. Estrictamente
hablando, dicho tiempo se denomina **sesión** del usuario, y el mecanismo se
conoce como **control de la sesión de usuario**. Pero, abusando del lenguaje,
hablaremos simplemente de sesión. El contexto nos indicará en cada momento a que
nos referimos, si al tiempo o al mecanismo de control. 

Obviamente, en el lado del servidor las aplicaciones *web* pueden crear ficheros 
para leer y escribir en ellos. La técnica consiste en crear un fichero la primera
vez que un cliente conecta con la aplicación y asociarlo a tal cliente. Este 
fichero es utilizado por la aplicación para almacenar los datos que deben 
persistir durante el tiempo que el cliente la utiliza. Por motivos de seguridad 
el fichero es eliminado por el servidor un tiempo después de que el cliente haya
dejado de realizar peticiones. El nombre del fichero es una larga cadena 
alfanumérica que se genera aleatoriamente y es usado como identificador de la 
sesión. Cuando el cliente realiza una nueva petición debe enviar de alguna manera
este identificador al servidor, de esta manera sabrá qué fichero almacena los
datos persistentes relativos a la sesión que el cliente inició. Aunque el
identificativo de la sesión se puede enviar desde el cliente al servidor como
un dato de la petición *GET*, la manera más segura de hacerlo es a través de una 
*cookie*, de forma que la primera vez que el cliente pide utilizar la aplicación,
esta crea el fichero de sesión y envía al cliente una *cookie* con la cadena que
lo identifica. A partir de ese momento el cliente enviará dicha *cookie* al
servidor durante las sucesivas peticiones, y la aplicación sabrá donde guarda los
datos que necesita para ese cliente en concreto. Esta es la forma en que *symfony*
trata por defecto las sesiones y aunque el *framework* puede ser configurado para
pasar el identificativo de la sesión por la *URL* (petición *GET*) no es
recomendable.

En *PHP* el acceso a las variables almacenadas en la sesión se realiza mediante
el *array* asociativo *$_SESSION* que es una variable predefinida de *PHP*. 
Dicho *array* se construye y está disponible durante toda la sesión siempre que
se indique mediante la instrucción de *PHP start_session()*. Si necesitamos
almacenar una variable durante la sesión basta que la incluyamos como miembro 
de *$_SESSION*. Por ejemplo, si necesitamos almacenar el nombre y los apellidos 
del usuario podemos hacer los siguiente:

.. code-block:: bash

	$_SESSION['nombre']    = 'Alberto';
	$_SESSION['apellidos'] = 'González García'

Sin embargo, por cuestiones de organización, sería más correcto hacer lo 
siguiente:

.. code-block:: bash

	$_SESSION['usuario']['nombre']    = 'Alberto';
	$_SESSION['usuario']['apellidos'] = 'González García'

De esta manera podremos seguir añadiendo datos referidos al usuario y 
recuperarlos de una vez:

.. code-block:: bash

	$usuario = $_SESSION['usuario'];
	
	$nombre    = $usuario['nombre'];
	$apellidos = $usuario['apellidos']

Lo que pretendemos decir con esto es que, ya que *PHP* representa la sesión con 
un *array* asociativo, utilicemos la potencia estructural que estos ofrecen para
organizar adecuadamente los datos de la sesión.

Cuando trabajamos en un proyecto *symfony*, el propio *framework* se hace cargo 
de iniciar la sesión, por lo que no tenemos que preocuparnos de ello. Por otro 
lado, aunque podemos utilizar el *array* predefinido *$_SESSION*, *symfony* ofrece
una manera más elegante y adecuada de manipular los datos de la sesión. Como no 
podía ser de otra manera en un entorno de programación orientado a objetos,
*symfony* utiliza  un objeto para realizar operaciones con la sesión. Tal objeto
es una instancia de la clase definida para cada aplicación en el archivo
*apps/nombre_aplicación/lib/myUser.class.php*. Si abres el fichero 
*apps/frontend/lib/myUser.class.php* comprobarás que la clase se denomina 
*myUser* y deriva de la clase *sfBasicSecurityUser*. Como podrás suponer, podemos
cambiar dicha clase para adaptarla a nuestras necesidades, aunque para la 
aplicación que estamos desarrollando en el curso nos basta con la que *symfony*
nos ofrece por defecto. A partir de ahora nos referiremos a este objeto como 
*sfUser*.

Desde las acciones, se puede acceder a dicho objeto utilizando el método
*getUser()* de la acción,  

Desde una acción:

.. code-block:: bash

	//Porción de código dentro de una acción
	...
	$usuario = $this → getUser();
	...

y desde las plantillas utilizando la variable *$sf_user*.

.. code-block:: bash

	//Porción de código dentro de una plantilla.
	...
	$usuario = $sf_user;
	...

Una vez que disponemos del objeto de sesión podemos definir nuevos atributos 
(variables de sesión) y recuperarlos mediante los métodos *setAttribute()* y 
*getAttribute()* respectivamente. También es muy  útil el método *hasAttribute()*
para comprobar la existencia de una variable de sesión. A continuación mostramos
la manera de utilizarlos en ejemplos de código dentro de una acción:

.. code-block:: bash

	// Definir una variable de sesión
	$this → getUser() → setAttribute('nombre','Alberto');
	
	// Recuperar la variable de sesión 'nombre', 
	$nombre = $this → getUser() → getAttribute('nombre');
	
	// Definir un atributo que es un array
	$usuario['nombre']    = 'Alberto';
	$usuario['apellidos'] = 'González García';
	
	$this → setAttribute('usuario',$usuario);
	
	// Recuperar el atributo 'usuario'
	$usuario = $this → getUser() → getAttribute('usuario');
	
	//Comprobar si existe el atributo 'usuario'
	if($this → getUser() → hasAttribute('usuario')
	{
		//hacer algo
	}

En el  código  anterior ``$this`` se refiere al objeto *sfActions* donde se esté
trabajando en el momento. Observa la similitud que existe entre el acceso a los
datos de la sesión mediante el objeto *sfUser* y el acceso a la petición del 
cliente mediante el objeto *sfWebRequest*, pero a la vez ten en cuenta que
representan dos conceptos muy distintos, aunque ambos sirven para que la
aplicación manipule datos relativos al cliente.

Como veremos más tarde en esta misma unidad, las aplicaciones *web* construidas
con *symfony* manejan la **autentificació** y la **autorización** de sus usuarios
mediante otros métodos adicionales que ofrece el objeto *sfUser*. Pero por lo 
pronto quedémonos con lo dicho hasta el momento y resolvamos el problema del 
filtro perdido.


**De vuelta con el filtro perdido.**
-------------------------------------------

Si has entendido todo lo que llevamos dicho en esta unidad ya habrás intuido 
como resolver el dichoso problema del filtro perdido que dejamos pendiente en la 
unidad anterior. La solución consiste en diseñar un mecanismo que permita guardar
el estado del filtro entre peticiones utilizando la sesión de usuario:

1. Comprobamos si existe el parámetro *documentos* en la petición, lo cual 
significa que el usuario rellenó alguno o todos los elementos del formulario de
búsqueda. Recuerda que hemos organizado los parámetros del formulario como un 
*array* asociativo cuyas claves son los nombres de los elementos de búsqueda.

2. Si existe dicho parámetro creamos una variable de sesión, que también 
denominamos *documentos*, y copiamos el valor de aquel en esta. Así hemos
almacenado en la sesión el estado del filtro. 

3. Recorremos todos los elementos de la variable de sesión que acabamos de crear
(recuerda que es un *array* asociativo) y vamos construyendo progresivamente el 
objeto *Criteria* que posteriormente utilizaremos para recuperar los registros.
Además almacenamos los valores en variables accesibles por la plantilla (usando 
*$this*) para mostrar en el propio formulario los valores que se solicitaron en
la petición anterior, de manera que el usuario sepa que los registros recuperados
corresponden a los valores que muestra el formulario de búsqueda.

Este procedimiento modifica el código de la acción *index* de la siguiente manera:

*Código de la acción del fichero:
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executeIndex(sfWebRequest $request)
	{
		$this -> tipos = TiposPeer::doSelect(new Criteria());
		$this -> categorias = CategoriasPeer::doSelect(new Criteria());
	
		$c = new Criteria();
	
		if($request -> hasParameter('documentos'))
		{
		   $this -> getUser() -> setAttribute('documentos', $request -> getParameter('documentos'));
		}
		//Inicio del filtro
		$this -> valoresFiltro = array();
		$this -> valoresFiltro['titulo']      = '';
		$this -> valoresFiltro['descripcion'] = '';
		$this -> valoresFiltro['autor']       = '';
		$this -> valoresFiltro['id_tipo']     = '';
		$this -> valoresFiltro['categorias']  = array();
	
		if($this -> getUser() -> hasAttribute('documentos'))
		{
			$documentos = $this -> getUser() ->  getAttribute('documentos');
	
			if($documentos['titulo'] != '')
			{
				$c -> add(DocumentosPeer::TITULO, $documentos['titulo'], Criteria::LIKE);
	
				$this -> valoresFiltro['titulo'] = $documentos['titulo'];
			}
	
			if($documentos['descripcion'] != '')
			{
				$c -> add(DocumentosPeer::DESCRIPCION, $documentos['descripcion'], Criteria::LIKE);
	
				$this -> valoresFiltro['descripcion'] = $documentos['descripcion'];
			}
	
			if($documentos['autor'] != '')
			{
				$c -> addJoin(DocumentosPeer::ID_USUARIO, UsuariosPeer::ID_USUARIO);
				$c1 = $c -> getNewCriterion(UsuariosPeer::NOMBRE, $documentos['autor'], Criteria::LIKE);
				$c2 = $c -> getNewCriterion(UsuariosPeer::APELLIDOS, $documentos['autor'], Criteria::LIKE);
				$c1 -> addOr($c2);
				$c -> add($c1);
	
				$this -> valoresFiltro['autor'] = $documentos['autor'];
			}
	
			if($documentos['id_tipo'] != '')
			{
				$c -> addJoin(DocumentosPeer::ID_TIPO, TiposPeer::ID_TIPO);
				$c -> add(TiposPeer::ID_TIPO, $documentos['id_tipo']);
	
				$this -> valoresFiltro['id_tipo'] = $documentos['id_tipo'];
			}
	
			if(isset($documentos['categoria_list']))
			{
				foreach ($documentos['categoria_list'] as $cat)
				{
					if($cat != '')
					{
						$c -> addJoin(DocumentosPeer::ID_DOCUMENTO, DocumentoCategoriaPeer::ID_DOCUMENTO);
						$c -> addJoin(DocumentoCategoriaPeer::ID_CATEGORIA, CategoriasPeer::ID_CATEGORIA);
						$c -> addAnd(CategoriasPeer::ID_CATEGORIA, $cat);
	
						$this -> valoresFiltro['categorias'][] = $cat;
					}
				}
			}
		}
		//Fin del filtro
		 $pager = new sfPropelPager('Documentos', 4);
		 $pager->setCriteria($c);
		 $pager->setPage($request -> getParameter('page', 1));
		 $pager->init();
		 $this->pager = $pager;
	}

También hay que modificar la plantilla correspondiente (*indexSuccess.php*) para
que el formulario de búsqueda muestre los valores que se introdujeron en la 
petición anterior:

*Código de la plantila apps/frontend/modules/gesdoc/templates/indexSuccess.php*

.. code-block:: html+jinja

	<div id="sf_admin_header">
		<h2>Listado de documentos</h2>
		<div class="notice">Mensaje de advertencia</div>
	</div>
	
	
	<div id="sf_admin_bar">
		<div class="sf_admin_filter">
			<form name="filtro" method="post" action="<?php echo url_for('gesdoc/index') ?>" >
				<table>
					<tbody>
						<tr class="sf_admin_form_row">
							<td>Título</td>
							<td><input type="text" id="titulo" name="documentos[titulo]" value="<?php echo $valoresFiltro['titulo'] ?>" /></td>
						</tr>
						<tr class="sf_admin_form_row">
							<td>Descripción</td>
							<td><input type="descripcion" id="titulo" name="documentos[descripcion]" value="<?php echo $valoresFiltro['descripcion'] ?>" /></td>
						</tr>
						<tr class="sf_admin_form_row">
							<td>Autor</td>
							<td><input type="text" id="autor" name="documentos[autor]" value="<?php echo $valoresFiltro['autor'] ?>" /></td>
						</tr>
						<tr class="sf_admin_form_row">
							<td>Tipo</td>
							<td>
								<select name="documentos[id_tipo]" id="id_tipo">
									<option value=""/>
									<?php foreach ($tipos as $t) : ?>
									<option value="<?php echo $t -> getIdTipo()?>"  <?php if ($valoresFiltro['id_tipo'] == $t -> getIdTipo()) echo 'selected="selected"'?> >
											<?php echo $t -> getNombre() ?>
									</option>
									<?php endforeach; ?>
								</select>
							</td>
						</tr>
						<tr class="sf_admin_form_row">
							<td>Categoría</td>
							<td><select name="documentos[categoria_list][]" multiple="multiple" id="categoria_list">
									<option value=""/>
									<?php foreach ($categorias -> getRawValue() as $c) : ?>
										<?php $arrayVF = $valoresFiltro -> getRawValue() ?>
									<option value="<?php echo $c -> getIdCategoria() ?>" <?php if (in_array($c -> getIdCategoria(), $arrayVF['categorias'])) echo 'selected="selected"' ?> >
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
					<?php foreach ($pager -> getResults()as $d): ?>
					<tr>
						<td><?php echo link_to($d -> getTitulo(),'gesdoc/verMetadatos?id_documento='. $d -> getIdDocumento(), array('class' => 'example5')) ?></td>
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
				<tfoot>
					<tr>
						<th colspan="20">
							<div class="sf_sf_admin_pagination">
								<?php if ($pager->haveToPaginate()): ?>
									<?php echo link_to(image_tag('first.png'), 'gesdoc/index?page='.$pager->getFirstPage()) ?>
									<?php echo link_to(image_tag('previous.png'), 'gesdoc/index?page='.$pager->getPreviousPage()) ?>
									<?php $links = $pager->getLinks();
									foreach ($links as $page): ?>
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
			</table>
		</div>
	</div>


Et voila!, el problema del filtro perdido quedó resuelto utilizando la sesión de
usuario para almacenarlo. Observa que si el usuario cambia los valores del filtro
en una próxima petición, también se cambiará el valor de la variable de sesión
que lo representa. De hecho la variable de sesión es un reflejo del último cambio
realizado por el usuario en el filtro de búsqueda. Ahora podemos utilizar los
*links* del paginado sin que  se pierda el resultado de la búsqueda.

.. note::

   En el código anterior se ha utilizado el método *getRawValue()* sobre los
   objetos ``$categorias`` y ``$valoresFiltros``, los cuales son variables que
   provienen de la acción. Según lo que hemos dicho hasta el momento, esperamos 
   que estos objetos sean *arrays* (así ocurría en la acción). Sin embargo esto
   no es verdad debido a que tenemos activado el modo *escaping_strategy* en el
   fichero *settings.yml* de nuestra aplicación. Lo cual ofrece más seguridad 
   pero también da lugar a más complejidad en el tratamiento de los *arrays* que
   se desean pasar de la acción a la vista, ya que estos son transformados en 
   objetos *sfOutputEscaper* y para obtener el array original hay que utilizar 
   el método *getRawValue()* sobre ellos.


**Aplicaciones seguras. Autentificación y  autorización.**
-----------------------------------------------------------------

Ahora que conocemos el funcionamiento de la sesión de usuario y como es manejada 
por *symfony* a través del objeto *sfUser*, vamos a estudiar como utilizarla para
dotar a nuestra aplicación de un control de acceso que garantice su seguridad
resolviendo los requisitos exigidos en el análisis. Pero antes estudiaremos los 
conceptos de **autentificación** y **autorización** que ofrecen los fundamentos
sobre los que descansa el control de acceso de las aplicaciones *web*. 


**Autentificación y Autorización**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

La autentificación es el proceso mediante el cual la aplicación comprueba si el
usuario que pretende utilizarla es realmente quien dice ser. Es un proceso de 
identificación. Para ello la aplicación solicita al usuario ciertos parámetros 
que lo identifiquen y, mediante algún tipo de comprobación, decide si lo considera
identificado en el sistema o no. 

La autorización es un proceso mediante el cual la aplicación decide qué 
funcionalidades puede utilizar el usuario que la maneja y qué información le 
puede presentar. La aplicación toma tal decisión basándose en la identificación
del usuario, esto es, decidirá qué recursos puede ofrecerle una vez que ha 
admitido la autentificación del usuario. Es, por tanto, un segundo nivel de 
seguridad en el control de acceso. 

Algunas aplicaciones seguras ofrecen todos sus recursos a cualquier usuario 
autentificado, en cuyo caso la autorización se confunde con la autentificación, 
pero en la mayoría de las aplicaciones no es así. De hecho nuestro gestor 
documental exige en sus requisitos este doble nivel de seguridad, ya que 
dependiendo del perfil que tenga asociado el usuario podrá utilizar más o menos 
recursos de la aplicación.

En la mayor parte de aplicaciones los parámetros que se requieren para
autentificarse son dos: el nombre de usuario (*username*) y su clave o contraseña
(*password*) asociada, que constituyen un par que sólo debe ser conocido por el 
usuario en cuestión para evitar suplantaciones de identidad. También pueden
diseñarse mecanismos de autentificación que soliciten otros  parámetros distintos,
incluso se podría implementar algún tipos de control biométrico, como puede ser 
la lectura de la huella dactilar, que complementase o sustituyese al que acabamos
de describir. No obstante la mayor parte de las aplicaciones *web* basan su
control de identidad en el par de parámetros nombre de usuario y clave y, por
tanto, será este el que utilizaremos en nuestro gestor documental. 

Las aplicaciones *web* utilizan una base de datos o un directorio para almacenar
los datos que permiten comprobar tanto la identidad del usuario como sus derechos 
de acceso. Una vez realizada la comprobación guardan dichos datos en la sesión de 
usuario en el servidor, de manera que la aplicación puede saber en cada solicitud
quien la está utilizando y qué funcionalidades y datos puede ofrecer. Cuando el
usuario solicite el final de la sesión o pase un determinado tiempo sin 
actividad, la aplicación destruirá la sesión y, cuando el usuario realice una
nueva petición de un recurso, la aplicación volverá a pedir a este sus parámetros
de autentificación (*login, password*), volviendo a crear una nueva sesión.


**Seguridad en la acción**
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En las aplicaciones construidas con *symfony* podemos controlar este doble nivel
de seguridad a nivel de cada acción mediante el uso de la sesión y los **archivos 
de configuración para la seguridad**.

Los archivos de configuración de seguridad, como es de esperar, se deben colocar 
en los directorios *config* de la aplicación y de los módulos, y se denominan 
*security.yml*. El nivel de seguridad de cada acción queda definido por la 
combinación de lo que se especifique en los ficheros *security.yml* de la
aplicación y del módulo, primando lo que dicte este último en caso de conflicto.
Por lo general, en el archivo de seguridad de la aplicación se define la 
seguridad por defecto de cada aplicación y en los de los módulos se complementa 
o cambian dichos parámetros para cada acción.

**Autentificación**

El parámetro de configuración *is_secure*, que puede ser *true* o *false*, indica
al *framework* que para ejecutar la acción el usuario debe estar autentificado.
Vamos a comprobarlo. Abre el fichero de seguridad *apps/frontend/config/security.yml* 
y define el parámetro *is_secure* como *true*:

*Contenido del archivo de seguridad de la aplicación:
apps/frontend/config/security.yml*

.. code-block:: bash

	default:
	  is_secure: true	

Con esto estamos indicando que, mientras no se indique lo contrario en los 
archivos de configuración de los módulos, todas las acciones requieren que el 
usuario esté autentificado. Si intentas ejecutar ahora la aplicación verás que 
aparece una pantalla indicando que la página no es pública y que se requiere 
estar autentificado para poder acceder. 


.. note::

	Recuerda, si usas el controlador de producción el cambio será efectivo cuando 
	borres la caché con la instrucción *symfony cc*.


Con esta configuración, antes de ejecutar la acción, el *framework* comprueba si
el usuario está autentificado. La comprobación se lleva a cabo consultando al
objeto *myUser*, ya que *symfony* almacena en la sesión los datos relativos a la
seguridad de la acción.

Este objeto proporciona los método *isAuthenticated()* y *setAuthenticated()*
para manipular la autentificación. De esta manera, desde una acción cualquiera,
podemos autentificar al usuario mediante la siguiente instrucción:

.. code-block:: bash
	
	…
	$this → getUser() → setAutenticated(true);
	…
	
	O comprobar si está autentificado mediante esta otra:
	if($this → getUser() → isAuthenticated()
	{
		// haz algo
	}


**Autorización**

El segundo nivel de seguridad, la autorización, al igual que la autentificacion, 
es controlada por el *framework* mediante los ficheros de seguridad (de aplicación
y de módulos) y el objeto *sfUser.* Para ello se utilizan las **credenciales**
con las que se puede representar el modelo de seguridad (grupos, permisos, 
perfiles, etcétera). 

Las credenciales no son más que valores que podemos asignar a la sesión de 
usuario mediante el métodos *addCredential()* del objeto *sfBasicSecurityUser*:

.. code-block:: bash

	...
	$this → getUser() → addCredential('administrador');
	...

También se pueden añadir varias credenciales de una vez mediante el método 
*addCredentials()*:

.. code-block:: bash

	...
	$this → getUser() → addCredentials('lectura', 'escritura');
	...

También se pueden eliminar una credencial con *removeCredential()*:

.. code-block:: bash

	...
	$this → getUser() → removeCredential('lectura');
	...

O todas de una vez con *clearCredentials()*:

.. code-block:: bash

	...
	$this → getUser() → clearCredentials();
	...

Por último con *hasCredential()* podemos comprobar si el usuario posee ciertas 
credenciales.

.. code-block:: bash

	// Comprueba si tiene la credencial lectura
	if($this → getUser() → hasCredential('lectura'))
	{
		//haz algo
	}
	
	// Comprueba si tiene la credencial lectura Y la credencial escritura
	if($this → getUser() → hasCredential(array('lectura','escritura'))
	{
		//haz algo
	}
	
	// Comprueba si tiene la credencial lectura O la credencial escritura
	if($this → getUser() → hasCredential(array('lectura','escritura'), false))
	{
		//haz algo
	}

En los ficheros *security.yml* podemos indicar las credenciales que debe tener
el usuario para poder ejecutar las acciones. Como ocurría con la autentificación,
lo normal es definir las credenciales por defecto en el fichero de seguridad de 
la aplicación y complementar o modificar dichas credenciales para cada acción en 
el fichero de seguridad de los módulos. **Antes de ejecutar una acción, symfony
comprobará si el usuario dispone de las credenciales que, en los ficheros de 
configuración, se han especificado para tal acción.**

Para *symfony* las credenciales no son más que valores que contrasta entre la 
configuración y la sesión de usuario para permitir o no la ejecución de las 
acciones. Es decir, las credenciales sólo tienen significado dentro del modelo 
de seguridad que se haya definido en el análisis de la aplicación.

Las entradas referentes a las credenciales en los ficheros *security.yml*
admiten combinaciones lógicas entre ellas, con lo que existe una gran 
flexibilidad para implementar el modelo de seguridad de la aplicación. Así por
ejemplo, si un usuario debe poseer las credenciales *'lectura'* **Y** *'escritura'*
para ejecutar la acción *index* de un módulo determinado, se especificaría en el
fichero *security.yml* de dicho módulo de la siguiente manera:

.. code-block:: bash

	...
	index:
	  credentials: [lectura, escritura]
	...


Si la condición fuese 'lectura' **O** 'escritura':

.. code-block:: bash

	...
	index:
	  credentials: [[lectura, escritura]]
	...

Es decir, se utilizan corchetes simples para la condición **AND** y dobles para 
la condición **OR**.

Bueno, con todo esto ya tenemos suficiente carga teórica como para emprender 
la implementación de la seguridad de la aplicación *frontend* de nuestro gestor
documental. En primer lugar diseñaremos unas reglas sencillas de credenciales que 
satisfagan el modelo de seguridad especificado en los requisitos de la unidad 4. 
Después construiremos un nuevo módulo que se encargará de comprobar la identidad 
del usuario y de asignarle una sesión de usuario con los parámetros de seguridad 
que le correspondan en función de su perfil asociado.


**El modelo de seguridad**
----------------------------------

Los requisitos de la aplicación que estamos construyendo especifican lo siguiente 
respecto de la seguridad:


======= =======================================================================

U.01    La aplicación contemplará 4 tipos de usuarios:

        * **invitado**, que podrá realizar búsquedas y descargas de documentos 
          públicos.
        * **lector**, que podrá realizar búsquedas y descargas de todos los 
          documentos
        * **autor**, que además podrá subir documentos
        * **administrador**, que además podrá administrar todos los aspectos 
          de la aplicación.
          
U.02    La aplicación presentará una parte pública (perfil invitado) en la que
        cualquier persona podrá realizar búsquedas y descargas de documentos 
        públicos. Para todas las demás acciones el usuario debe estar 
        registrado.
======= =======================================================================


======= =======================================================================

C.01    Los usuarios registrados podrán enviar comentarios a los documentos.

C.02    Los usuarios registrados podrán ver los comentarios de los documentos.
======= =======================================================================


======= =======================================================================

P.01    Los usuarios registrados podrán votar sólo una vez cada documento 
        consultado

======= =======================================================================


Teniendo esto en cuenta definiremos las siguientes credenciales:

* *lectura*, para buscar y descargar todo tipo de documentos (privados y 
  públicos), para enviar y leer comentarios y para votar los documentos,
  
* *escritura*, para subir archivos al servidor,

* *administracion*, para administrar todos los aspectos de la aplicación y crear 
  usuarios
  
Y las asignaremos a los perfiles de la siguiente manera:


============== ================================================================
Perfil         Credenciales asociadas
============== ================================================================

Invitado       No se le asocian credenciales

Lector         *lectura*

Autor          *lectura, escritura*

Administrador  *lectura, escritura, administración*



Por otro lado todas las acciones serán seguras (requieren autentificación), salvo
la acción *index* para permitir al usuario invitado buscar documentos públicos y 
descargarlos.

Una vez definido el modelo de seguridad lo implementamos en los ficheros de
configuración. Comenzamos definiendo a la aplicación *frontend* segura por
defecto. Es decir, el fichero *apps/frontend/config/security.yml* quedaría así:


*Contenido del archivo de seguridad de la aplicación:
apps/frontend/config/security.yml*

.. code-block:: bash

	default:
	  is_secure: true

A continuación creamos el directorio de configuración del módulo *gesdoc* de la 
aplicación *frontend* (*apps/frontend/modules/gesdoc/config*), ya que cuando se
genera  un módulo este directorio no se crea por defecto:

.. code-block:: bash

	# mkdir apps/frontend/modules/gesdoc/config

Y creamos en dicho directorio el correspondiente fichero *security.yml* con el
siguiente contenido:

*Contenido del fichero: apps/frontend/modules/gesdoc/config/security.yml*

.. code-block:: bash

	index:
	  is_secure: false
	
	verMetadatos:
	  is_secure: false
	
	verVersion:
	  is_secure: false
	  
	modificar:
	  credentials: [escritura]
	  
	subirVersion:
	  credentials: [escritura]

Hemos definido la acción *index* como no segura para que los usuarios invitados
puedan buscar y ver documentos. Sin embargo a dichos usuarios no se les debe
mostrar los documentos privados. Esta distinción ya no la puede hacer
automáticamente el mecanismo de seguridad de *symfony*. Por tanto somos nosotros
quienes debemos modificar la acción *index* para tener en cuenta este detalle.
Basta con incluir en el criterio la condición de que se recuperen únicamente
documentos públicos en el caso de que el usuario no esté autentificado. El final
de la acción *index* quedaría:

*Comprobación de la autentificación en la acción index para mostrar o no 
documentos privados*

.. code-block:: bash

	…
	// Si el usuario no está autenticado (es invitado)
	// muestra sólo los documentos públicos.
	if(!$this -> getUser() -> isAuthenticated())
	{
		$c -> add(DocumentosPeer::PUBLICO, 1);
	}
	$pager = new sfPropelPager('Documentos', 4);
	$pager->setCriteria($c);
	$pager->setPage($request -> getParameter('page', 1));
	$pager->init();
	$this->pager = $pager;

En negrita se indica el código añadido.

Si vuelves a ejecutar la acción *index* comprobarás que aparecen menos documentos 
en el listado y que todos son públicos.

.. note::

   Recuerda borra la caché si estás utilizando el controlador de producción.

Además si intentas modificar un documento o enviar una nueva versión, aparece 
una pantalla indicando que no puedes acceder a ella puesto que no estás 
autentificado. Este último hecho nos hace pensar que, ya que el usuario no 
autentificado (invitado) no puede ejecutar estas acciones, la aplicación no 
debería mostrarles dichos accesos. Pues nada, se los vamos a quitar. Se trata 
de comprobar en la plantilla *indexSuccess.php* si el usuario no está 
autentificado y, en ese caso, no mostrar los enlaces *modificar* y *subir versión*.
El código siguiente muestra como incluir dicho control a la plantillas.

.. code-block:: html+jinja

	…
	 
	<?php if($sf_user -> isAuthenticated()) : ?>
	<th>Acciones</th>
	<?php endif; ?>
	
	…
	
	<?php if($sf_user -> isAuthenticated()) : ?>
	 <td>
	 <?php echo link_to('modificar', 'gesdoc/modificar?id_documento='.$d -> getIdDocumento()) ?> |
	 <?php echo link_to('subir versión', 'gesdoc/subirVersion?id_documento='.$d -> getIdDocumento()) ?>
	 </td>
	 <?php endif; ?>

Prueba ahora. Mucho mejor ¿no?. Si quieres volver a ver la pantalla como usuario 
autenticado, para hacer pruebas, puedes añadir al comienzo de la acción *index*
la siguiente linea:

.. code-block:: bash

	$this → getUser → setAuthenticated(true);

Ahora podrás comprobar que si intentas acceder a la acción modificar y subir 
versión a través de los correspondientes enlaces, la aplicación impide la 
ejecución y muestra un mensaje en el que se dice que se requiere autorización. 
En efecto, aunque el usuario esté autentificado, no le hemos asignado las 
credenciales exigidas en el fichero de configuración para ejecutar dichas 
acciones.

Cuando termines de hacer pruebas elimina la linea anterior, si no quieres que 
la aplicación presente un grave problema de seguridad. De todas formas aunque 
elimines tal linea si vuelves a ejecutar la acción *index* verás que el usuario 
sigue autentificado. ¿Por qué ocurre esto?. La razón es que, aunque hayas 
eliminado la linea, la sesión de usuario sigue manteniendo al usuario como 
autentificado durante el tiempo que esta dure. Así que si quieres volver a 
definir al usuario como no autentificado puedes hacer una de estas tres cosas:

* Esperar sin utilizar la aplicación el tiempo que la configuración de tu *PHP*
  (*php.ini*) tenga reservado para la duración de la sesión. Esta solución no es 
  práctica.

* Destruir la sesión de usuario cerrando completamente el navegador.

* Mediante programación, volver a definir  al usuario como no autentificado 
  mediante la siguiente instrucción: ``$this → getUser() → setAuthenticated(false)``,
  y volver a ejecutar la acción.

Una vez que hemos blindado las acciones del módulo *gesdoc* mediante la 
asignación de credenciales y exigencias de autentificación a las acciones a 
través de los ficheros de configuración *security.yml*, debemos construir un
procedimiento que construya la sesión de usuario asignándole los atributos de
seguridad que le correspondan al usuario en función de su perfil, es decir: si 
está o no autentificado, y en caso positivo qué credenciales le corresponden. 


**Registro de usuario o inicio de sesión**
---------------------------------------------------

Los usuarios registrados en el sistema (lectores, autores y administradores), si
desean utilizar la aplicación como tales deberán autentificarse en el sistema
mediante sus nombres de usuarios y contraseñas. Para ello el sistema debe contar 
con un procedimiento mediante el que recoja tales datos, los compruebe y en caso 
de éxito asigne al usuario la condición de autentificado y las credenciales que 
le corresponda según el perfil. Este procedimiento se conoce como registro de 
usuario o inicio de sesión, y es el que vamos a construir en este apartado.

Implementaremos el inicio de sesión en un nuevo módulo de la aplicación que 
denominaremos *inises*. Más adelante “pasaremos” este módulo de la aplicación
*frontend* a un *plugin* con el fin de que pueda ser compartido por la aplicación
*backend* del proyecto (aún por construir). 

La acción *signIn* del módulo será la encargada de controlar toda la lógica del
inicio de sesión cuyo flujo exponemos a continuación:

1. Si en la petición *HTTP* no se ha enviado nada, se mostrará un formulario para 
   que el usuario introduzca su nombre de usuario y contraseña. Tal formulario
   enviará los datos a la propia acción *signIn*.

2. Si en la petición *HTTP* vienen los datos *'username'* y *'password'*, 
   entonces se comprobará si existe un usuario asociado a dicho par de
   parámetros. Si no existe, la acción vuelve a mostrar el mismo formulario de
   identificación junto con un mensaje que indica que los datos introducidos no 
   corresponden a ningún usuario.

3. Si existe un usuario asociado al par *'username', 'password'*,  entonces:

	* Se define en la sesión de usuario el atributo *'id_usuario'* cuyo valor 
	  será la clave primaria del usuario en la tabla 'usuarios'. De esta manera, 
	  en sucesivas peticiones, la aplicación podrá acceder a los datos 
	  correspondientes al usuario en cuestión (que están almacenados en la base 
	  de datos).

	* Se declara en la sesión al usuario como autentificado. Así, en sucesivas
	  peticiones, la aplicación sabrá que el usuario está debidamente registrado
	  en su base de datos.

	* Se añaden a la sesión las credenciales que le correspondan al usuario en 
	  función del perfil que tenga asociado. Por tanto, en sucesivas peticiones, 
	  la aplicación sabrá qué funcionalidades puede usar el usuario. 

	* Se hace una redirección a la acción *'gesdoc/index'*, que presentará más 
	  o menos recursos según el grado de autorización del usuario, es decir, 
	  según el perfil que tenga asociado.


Por otro lado, la acción *signOut* será la encargada de realizar la desconexión 
del usuario. Consistirá en destruir la sesión y realizar una redirección al
listado de documentos.

Comenzamos por crear el módulo *inises*:

.. code-block:: bash

	# symfony generate:module frontend inises

Para implementar el formulario de identificación del usuario, utilizaremos el 
*framework* de formularios de *symfony*, ya que nos facilita la vida a la hora 
de realizar la validación de los datos. Aunque aún no hemos tratado los
formularios de *symfony* a fondo, nos basta con saber lo que se explicó acerca 
de ellos en la unidad 5. Comenzamos por crear el directorio *lib* del módulo 
*inises* donde ubicaremos el fichero con la descripción del formulario:

.. code-block:: bash

	# mkdir apps/frontend/modules/inises/lib

Y en su interior creamos el fichero *LoginForm.php* con la definición del
formulario de identificación:

*Contenido del fichero apps/frontend/modules/inises/lib/LoginForm.php*

.. code-block:: php

	<?php
	
	class LoginForm extends sfForm
	{
	  public function configure()
	  {
		$this -> setWidgets(array(
		'username'     => new sfWidgetFormInput(),
		'password'     => new sfWidgetFormInputPassword(),
		));
		
		$this->widgetSchema->setNameFormat('datos[%s]');
		
		
		$this -> setValidators(array(
		'username'        => new sfValidatorString(array('required' => true), array('required' => 'Campo requerido')),
		'password'     => new sfValidatorString(array('required' => true), array('required' => 'Campo requerido'))
		));
	  }
	} 

En pocas palabras, el formulario anterior define dos cajas de texto, una para
introducir el nombre de usuario y la otra para la clave. Ambas cajas de texto 
serán requeridas en el proceso de validación del formulario. Por último los
parámetros se pasarán en la petición *HTTP* de la siguiente forma: 
*datos[username]* y *datos[password]*, de manera que serán interpretados por
*PHP* como un *array*, mejorando la organización de los datos.

Para definir los parámetros de seguridad debemos crear el directorio *config*
del módulo:

.. code-block:: bash

	# mkdir apps/frontend/modules/inises/config

y añadirle el fichero *security.yml* con el siguiente código:

*Contenido del fichero: apps/frontend/modules/inises/config/security.yml*

.. code-block:: bash

	signIn:
	  is_secure: false

Ahora creamos la acción *signIn*, es decir añadimos el método público 
*executeSigIn()* a la clase *inisesActions* definida en el fichero 
*actions.class.php* del módulo:

*Contenido del fichero: apps/frontend/modules/inises/actions/actions.class.php*

.. code-block:: php

	class inisesActions extends sfActions
	{
		public function executeSignIn(sfWebRequest $request)
		{
			$this -> form = new LoginForm();
			if ($request->isMethod('post'))
			{
				$this->form->bind($request->getParameter('datos'));
				if ($this->form->isValid())
				{
					$datos = $request -> getParameter('datos');
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
	}

Esta acción implementa el flujo explicado al principio del apartado. La acción
comienza por declarar como atributo de la clase (uso de ``$this``) un objeto de
la clase *LoginForm*, es decir un formulario. Si la acción no ha sido llamada a
través de una petición *POST* (si no se han enviado datos desde el cliente a
través del formulario de identificación), la acción termina y da paso a su 
plantilla correspondiente, *signInSuccess.php*: 

*Contenido del fichero: apps/frontend/modules/inises/templates/signInSuccess.php*

.. code-block:: php

	<form name="loginForm" action="<?php echo url_for('inises/signIn') ?>" method="post">
		<?php echo $form -> renderGlobalErrors() ?>
		<?php echo $form -> renderHiddenFields() ?>
		<?php if (isset($mensaje)) : ?>
			<?php echo $mensaje ?>
		<?php endif; ?>
		<table>
			<tr>
				<th><label for="username">Usuario:</label></th>
				<td><?php echo $form['username'] -> renderError() ?><?php echo $form['username'] -> render() ?></td>
			</tr>
			<tr>
				<th><label for="password">Clave:</label></th>
				<td><?php echo $form['password'] -> renderError() ?><?php echo $form['password'] -> render() ?></td>
			</tr>
		</table>
		<input type="submit" value="ingresar" /> | <?php echo link_to('volver al listado','gesdoc/index') ?>
	</form>


Es decir, se envía al cliente el formulario de identificación. Observa el uso 
del objeto ``$form`` (pasado a la plantilla por la acción *signIn*): para cada
caja de texto se pinta la propia caja (``$form['elemento'] ->render()`` y, si lo
hubiera, el error que arroja la validación del formulario 
(``$form['elemento'] ->renderError()``). Obviamente, la primera vez que se envía
el formulario, como aún no ha sido validado, la función *renderError()* no
devuelve nada. Una vez que se valide el formulario tampoco devolverá nada si la 
validación es correcta. Pero si dejamos alguno de los campos sin rellenar, la 
validación arrojará un error y al volver a pintar el formulario la función 
*renderError()* devolverá la cadena *'Campo requerido'* que se definió en la
declaración de los validadores del formulario. 

La validación del formulario se solicita en la acción *signIn* cuando se ha 
comprobado que la petición es del tipo *POST*. Entonces se “enlazan” (método 
*bind()*) los datos de la petición con el objeto formulario y se realiza la
validación (``$this → form → isValid()``). La comprobación de la identidad del
usuario se realiza una vez que la validación del formulario es correcta. Entonces
se accede a la base de datos para comprobar si existe un usuario con el nombre 
de usuario y contraseña enviado. Para ello nos apoyamos en la función 
*compruebaUsuario()* que debemos añadir a la clase *inisesActions*:

*Código añadido al archivo: apps/frontend/modules/inises/actions/actions.class.php*

.. code-block:: bash

	protected function compruebaUsuario($datos)
	{
		 $c = new Criteria();
	
		 $c -> add(UsuariosPeer::USERNAME, $datos['username']);
		 $c -> add(UsuariosPeer::PASSWORD, md5($datos['password']));
	
		 $usuario = UsuariosPeer::doSelectOne($c);
	
		 return $usuario;
	}

y que devuelve el objeto ``$usuario`` si este existe. En caso contrario devuelve 
*null*. Si el usuario existe construimos la sesión añadiendo el atributo 
*id_usuario* con la clave principal del usuario en la tabla *usuarios* y
definiéndolo como autentificado. Además le asociamos las credenciales
correspondiente en función del perfil. Esto último lo realiza la función
*asociaCredenciales()*, que también debemos añadir al código de la clase 
*inisesActions*:

*Código añadido al archivo: apps/frontend/modules/inises/actions/actions.class.php*

.. code-block:: php

	protected function asociaCredenciales($usuario)
		{
			$perfil = $usuario -> getPerfil();
	
			switch ($perfil)
			{
				case 'lector':
					$this -> getUser() -> setAttribute('perfil', 'lector');
					$this -> getUser() -> addCredential('lectura');
					break;
				case 'autor':
					$this -> getUser() -> setAttribute('perfil', 'autor');
					$this -> getUser() -> addCredentials(array('lectura','escritura'));
					break;
				case 'administrador':
					$this -> getUser() -> setAttribute('perfil', 'administrador');
					$this -> getUser() -> addCredentials(array('lectura', 'escritura', 'administracion'));
					break;
			}
		}

Una vez generada la sesión se redirige a la acción *'gesdoc/index'*. Por último,
si no existe un usuario con el nombre de usuario y clave enviado, se vuelve a 
pintar el formulario de identificación con el mensaje *'Usuario no autorizado'*.

Ya sólo nos queda añadir la acción *signOut()* para la desconexión del usuario:

*Código de la acción signOut del archivo: apps/frontend/modules/inises/actions/actions.class.php*

.. code-block:: php

	public function executeSignOut(sfRequest $request)
	{
		 session_destroy();
		 $this -> redirect('gesdoc/index');
	}

La cual destruye la sesión y redirige de nuevo al listado de documentos.

Finalmente el código de la clase *inisesActions* queda de la siguiente forma:

*Código del archivo: apps/frontend/modules/inises/actions/actions.class.php*

.. code-block:: php

	<?php
	
	/**
	 * inises actions.
	 *
	 * @package    gestordocumental
	 * @subpackage inises
	 * @author     Your name here
	 * @version    SVN: $Id: actions.class.php 12479 2008-10-31 10:54:40Z fabien $
	 */
	class inisesActions extends sfActions
	{
		/**
		 * Executes index action
		 *
		 * @param sfRequest $request A request object
		 */
		public function executeSignIn(sfWebRequest $request)
		{
			$this -> form = new LoginForm();
			if ($request->isMethod('post'))
			{
				$this->form->bind($request->getParameter('datos'));
				if ($this->form->isValid())
				{
					$datos = $request -> getParameter('datos');
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
	
		public function executeSignOut(sfRequest $request)
		{
			session_destroy();
			$this -> redirect('gesdoc/index');
		}
	
		protected function compruebaUsuario($datos)
		{
			$c = new Criteria();
	
			$c -> add(UsuariosPeer::USERNAME, $datos['username']);
			$c -> add(UsuariosPeer::PASSWORD, md5($datos['password']));
	
			$usuario = UsuariosPeer::doSelectOne($c);
	
			return $usuario;
		}
	
		protected function asociaCredenciales($usuario)
		{
			$perfil = $usuario -> getPerfil();
	
			switch ($perfil)
			{
				case 'lector':
					$this -> getUser() -> addCredential('lectura');
					break;
				case 'autor':
					$this -> getUser() -> addCredentials(array('lectura','escritura'));
					break;
				case 'administrador':
					$this -> getUser() -> addCredentials(array('lectura', 'escritura', 'administracion'));
					break;
			}
		}
	}

Ahora puedes comprobar el funcionamiento del módulo de inicio de sesión
realizando la siguiente petición desde el navegador:

``http://localhost/gestordocumental/web/frontend_dev.php/inises/signIn``

Pruébalo dejando algún campo en blanco, insertando parámetros de identificación falsos y verdaderos. Prueba también la desconexión mediante la siguiente petición:

``http://localhost/gestordocumental/web/frontend_dev.php/inises/signOut``

Queda pues construido el proceso de inicio de sesión y desconexión del usuario. 
Ahora  nos queda integrarlo en la interfaz de usuario de la aplicación, ya que 
no es muy elegante ni cómodo tener que realizar peticiones directamente desde 
la barra de direcciones del navegador. Haremos esto último en la siguiente unidad
donde introduciremos algunos conceptos importantes relacionados con la vista que
nos permitirán incorporar a la aplicación un menú de navegación, un mensaje de
bienvenida con los datos del usuario que está registrado en la aplicación y un 
enlace para conectarnos como usuario registrado cuando trabajamos en modo
invitado o un enlace para desconectarnos  si trabajamos en modo registrado.


**Conclusiones.**
------------------------

En esta unidad hemos trabajado el concepto de sesión de usuario en el servidor 
gracias al cual la aplicación puede “conocer” quien la está utilizando y puede 
almacenar variables de estado entre sucesivas peticiones. Hemos visto que es un 
mecanismo gracias al cual se puede superar la restricción de ausencia de estado
impuesta por el protocolo *HTTP*, base de la la comunicación entre cliente y 
servidor en las aplicaciones *web*. Debe quedar claro que este mecanismo no dota
al protocolo *HTTP* de un control de estado; entre una petición *HTTP* y otra no
existe ninguna relación. Para que la aplicación pueda realizar un seguimiento de
quien la está utilizando, cada petición debe enviar un parámetro que identifique
al cliente. Este parámetro es la *cookie* de sesión y es utilizado por la
aplicación *web* para identificar las sesiones que tiene abierta en el servidor
donde se aloja.

Mediante el mecanismo de sesión hemos resuelto dos problemas: la pérdida del
filtro de búsqueda entre peticiones y la autentificación y autorización del 
usuario en la aplicación, conceptos que también han sido estudiado en la unidad 
junto con la estrategia propia de *symfony* para su tratamiento.








