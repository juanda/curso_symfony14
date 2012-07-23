Unidad 10: Internacionalización y enrutamiento
==============================================

Comenzamos la última unidad del curso con el gestor de contenidos finalizado y
completamente funcional. Su desarrollo nos ha servido como hilo conductor para 
explorar y aprender a utilizar una potente herramienta para la construcción de
aplicaciones *web*: el *framework symfony*. Son muchos los conceptos, las ideas 
y estrategias que se han trabajado a lo largo del curso, siendo muchos de ellos
de alcance bastante general, aunque se hayan concretados en el dominio de *symfony*.
A pesar de todo, si continuamos explorando los detalles y recovecos del *framework*,
seguiremos extrayendo nuevos tesoros. Hemos dado varias vueltas a nuestro objeto
de estudio, y en cada una de ellas nos hemos acercado un poco más al centro,
revelando nuevos y poderosos aspectos de *symfony* que nos permitirán mejorar 
la calidad de nuestros futuros desarrollos *web*. Sin embargo aún se pueden dar 
varias vueltas más. Y no dudes que extraerás conocimiento muy provechosos en cada 
nueva vuelta que des.

Esta unidad se plantea como el inicio de otra vuelta panorámica en la que se
presentan, sin entrar en profundidad, nuevos aspectos de *symfony* que no han 
sido imprescindibles para desarrollar nuestro gestor documental pero que, sin
lugar a duda, contribuirían a mejorarlo o serán muy útiles en la construcción de
otras aplicaciones. Concretamente trataremos el tema de la internacionalización 
y localización de aplicaciones *web* y del enrutamiento de *symfony*.


Internacionalización y Localización
-----------------------------------

Internacionalizar una aplicación informática significa diseñarla de tal manera
que  pueda ser adaptada a distintos idiomas sin necesidad de realizar cambios en
su ingeniería. Esto significa que, de alguna manera, los textos que se utilizan
en su interfaz deben actuar en la aplicación como si de un componente 
intercambiable se tratase. Además también debe existir un proceso mediante el 
cual la aplicación presente la interfaz en un lenguaje u otro. Se suele utilizar
la abreviatura *I18N* para referirse a la **InternacionalizacióN**, ya que 
entre la primera letra (I) y la última (N) hay 18 letras más.

Por otro lado, localizar una aplicación es el proceso de adaptarla para que 
muestre ciertos datos, como las fechas, las monedas y los números, según los 
aspectos locales de distintas regiones o países sin alterar la ingeniería de la 
misma. Se suele utilizar la abreviatura *L10N* para referirse a la **LocalizacióN**.
Adivina por qué.

En este apartado explicaremos cómo tratar estos aspectos con *symfony*. 


La cultura del usuario.
^^^^^^^^^^^^^^^^^^^^^^^

La necesidad de internacionalizar y localizar una aplicación del tipo que sea
surge del hecho de que dicha aplicación será utilizada por usuarios de distintas
partes del mundo. Es natural, por tanto, que la información que recoja este hecho
sea un atributo del usuario que está utilizando la aplicación. *Symfony* utiliza
un atributo del  objeto *sfUser*, o sea de la sesión de usuario, denominado 
*culture* para almacenar esta información. 

La cultura de usuario es un parámetro compuesto por dos partes: el lenguaje y 
la localización. Los códigos de lenguaje consisten en dos letras minúsculas que
siguen el estándar ISO-639-2. La siguiente tabla muestra los lenguajes más 
usuales para un desarrollador europeo:

========================== =================================
Lenguaje                   Código ISO-639-2
========================== =================================
Español                    es

Inglés                     en

Francés                    fr

Alemán                     de

Italiano                   it

Portugués                  pt
========================== =================================

Los códigos de localización se definen por dos letras mayúsculas según el 
estándar ISO-3166. La siguiente tabla muestra alguno de los código correspondiente
distintas  regiones en las que se puede hablar el mismo idioma:

================== ======================
Región             Código ISO-3166
================== ======================
España             ES

Argentina          AR

Reino Unido        UK

Estados Unidos     US

Francia            FR

Bélgica            BE
================== ======================
 
La combinación de ambos código separados por un carácter “_” conforman el 
atributo cultura:

===================== ========================================
Cultura               Significado
===================== ========================================
es_ES                 Español con localización de España

es_AR                 Español con localización de Argentina

en_UK                 Inglés con localización del Reino Unido

en_US                 Inglés con localización de EEUU

fr_FR                 Francés con localización de Francia

fr_BE                 Francés con localización de Bélgica
===================== ========================================

Pues muy bien, la cuestión que importa realmente, al margen de todas estas 
convenciones, es saber como definir y recuperar el atributo *culture* de la sesión.
Y ello se hace mediante los métodos *setCulture()* y *getCulture()* del objeto 
*sfUser*:

*Definición de la cultura*

.. code-block:: php

        <?php       
	// En una plantilla
	$sf_user -> setCulture('es_ES');
	
	// En una acción
	$this -> getUser() -> setCulture('es_ES');


*Recuperación de la cultura:*

.. code-block:: php

        <?php 
    
	// En una plantilla 
	$culture = $sf_user -> getCulture();
	
	// En una acción
	$culture = $this -> getUser() -> getCulture();


Es obvio que trabajar con el parámetro *culture* tiene sentido si pretendemos 
internacionalizar nuestra aplicación. Por defecto *symfony* no tiene en cuenta 
para nada este parámetro. Si deseamos utilizar las características *I18N* de
*symfony* lo  primero que debemos hacer es indicárselo en el fichero de
configuración *settings.yml* de la aplicación mediante el parámetro *i18n*:

*Uso del parámetro i18n en el fichero apps/nombre_aplicacion/config/settings.yml*

.. code-block:: yaml

	...
	all
	  .settings
		 i18n: true
	...


También podemos indicar la cultura por defecto, esto es, la cultura que utilizará
*symfony* si no se especifica explícitamente con el objeto *sfUser*. Para ello
utilizamos el parámetro *default_culture*: del archivo *settings.yml* de la
aplicación.

*Uso del parámetro default_culture en el fichero
apps/nombre_aplicacion/config/settings.yml*

.. code-block:: yaml

	...
	all
	  .settings
		 default_culture: es_ES
	...


Con lo que acabamos de ver basta para traducir al español la interfaz de la
aplicación de administración de nuestro gestor documental. Para ello añade al
archivo *apps/backend/config/settings.yml* el parámetro *i18n* y defínelo como 
*true*. Después modifica la acción *executeSignin()* del módulo *inises* para que
coloque la cultura *'es_ES'*:

*Modificación de la acción executeSignin() del módulo inises para que tenga en
cuenta la cultura*

.. code-block:: php

        <?php
        ...
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
						$this -> getUser() -> setCulture('es_ES');
						$this -> redirect('@pagina_inicial');
	
					}
					else
					{
						$this -> mensaje = 'Usuario no autorizado';
					}
				}
			}
		}


Ahora cuando entres en cualquiera de los módulos generados automáticamente 
comprobarás que se muestran en español. Esto es así por que dichos módulos están
internacionalizados y cuentan con varias traducciones de la interfaz, entre ellas
la traducción al español. En el próximo apartado explicaremos como podemos 
realizar las traducciones de nuestras interfaces.

En lugar de alterar el código del inicio de sesión podríamos haber definido en 
el archivo *settings.yml* de la aplicación *backend* el parámetro *default_culture* 
con el valor *es_ES*. Hemos preferido hacerlo en el código para mostrar un lugar
apropiado donde se podría llevar a cabo la definición de la cultura en la sesión
de usuario.

Según acabamos de ver, internacionalizar una aplicación *symfony* consiste por
un lado en indicar al *framework mediante el parámetro i18n del archivo de 
configuración settings.yml* de la aplicación que deseas internacionalizarla, y
por otro en indicar en la sesión de usuario qué cultura utilizar durante el uso 
de la aplicación. La selección de la cultura se puede hacer de muchas maneras. 

Una posible manera es utilizando el proceso de inicio de sesión. Se podría
utilizar un campo de la tabla de usuarios para indicar la cultura preferida de
cada usuario. Entonces, cuando se inicia la sesión se definiría la cultura del
objeto *sfUser* según el valor almacenado en dicho campo. Modificar el código 
anterior para implementar esta idea es muy sencillo.

Otra posible manera sería mediante la implementación de un formulario mediante 
el cual el usuario pueda seleccionar una de las culturas admitidas por la
aplicación. El *plugin* público *sfFormExtraPlugin* ofrece una solución de este 
tipo, con lo que el esfuerzo para implementar dicha solución es mínimo.

También se puede obtener la cultura a partir de la información proporcionada por
el *header HTTP Accept-Language* de la petición. El método *getLanguages()* del
objeto *request* devuelve un array de lenguajes aceptados por el navegador *web*
del cliente. Existe un método del mismo objeto que puede resultar incluso más
útil: *getPreferredCulture(array())* que devuelve la cultura aceptada por el 
cliente *web* que mejor se ajusta a la lista de culturas proporcionada en el
argumento de la función.

En fin, la forma concreta que utilices para definir la cultura de usuario 
dependerá de las especificaciones de la aplicación que desarrolles. Aunque los
más probable es que una de estas tres soluciones o alguna mezcla de ellas te 
resulte suficiente.

Una vez que la cultura de usuario esté definida, *symfony* presentará la interfaz
en el idioma correspondiente. Obviamente el *framework* no es tan listo como para
realizar él mismo la traducción, simplemente sabe como buscar los textos, que 
nosotros debemos facilitarle, en los distintos idiomas que soporte la aplicación.
En el siguiente apartado veremos como se lleva a cabo la traducción de la interfaz.


Traducción de la interfaz de las aplicaciones internacionalizadas*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Si queremos presentar los textos de la interfaz de usuario en distintos idiomas, 
lo primero que debemos hacer es internacionalizar la aplicación, es decir, poner 
el parámetro *i18n* del archivo *settings.yml* a *true*. 

Posteriormente, en las plantillas que vayamos a traducir, utilizamos el *helper
I18NHelper* ya que en él se encuentra la función clave del proceso de traducción 
de la interface de usuario. Esta función se llama ``__()``. Un nombre un poco raro,
ya que estrictamente no es un nombre, se trata dos veces el carácter “_”, lo cual
es válido como nombre de función *PHP*. El misterio de la traducción es que todos
los textos que pasemos como argumento a la función ``__()`` serán sustituidos, si
existe, por el valor indicado en un fichero *XML* donde se encuentran las
traducciones al idioma en cuestión. En caso de no existir traducción, la función
devuelve el mismo texto que se le pasó como argumento.  Veámoslo con un ejemplo:

*Ejemplo de una plantilla traducible*

.. code-block:: html+php

	<?php echo use_helper('I18N') ?>
	
	<h1><?php echo __('Listado de frutas'); ?></h1>
	
	<ul>
		<li><?php echo __('Manzana') ?></li>
		<li><?php echo __('Naranja') ?></li>
		<li><?php echo __('Limon') ?></li>
	</ul>


Por otro lado, para que la función __() sepa qué texto debe colocar en función 
de la cultura, debemos crear un fichero de traducciones por cada idioma que 
deseemos utilizar. Los argumentos que se pasan a la función se consideran como
idioma fuente (*source-language*). Este fichero de traducciones sigue el standard
*XLIFF* y se debe ubicar en el directorio *i18n* de la aplicación. El archivo de 
traducción al inglés de los términos anteriores  tendría el siguiente aspecto:

*Archivo XLIFF de traducción español-inglés: 
apps/nombre_aplicacion/i18n/en/messages.xml*

.. code-block:: xml

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE xliff PUBLIC "-//XLIFF//DTD XLIFF//EN" "http://www.oasis-open.org/committees/xliff/documents/xliff.dtd">
	<xliff version="1.0">
	  <file source-language="es" target-language="en" datatype="plaintext" original="messages" date="2010-04-29T19:12:10Z" product-name="messages">
		<header/>
		<body>
		  <trans-unit id="1">
			<source>Listado de frutas</source>
			<target>List of fruits</source>
		  </trans-unit>
		  <trans-unit id="2">
			<source>Manzana</source>
			<target>Apple</target>
		  </trans-unit>
		  <trans-unit id="3">
			<source>Naranja</source>
			<target>Orange</target>
		  </trans-unit>
		  <trans-unit id="4">
			<source>Limon</source>
			<target>Lemon</target>
		  </trans-unit>
		</body>
	  </file>
	</xliff>


Traducción de campos de tablas de la base de datos
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

En ocasiones también será necesario traducir algunos de los campos de ciertas 
tablas de la base de datos. *Symfony* también proporciona un procedimiento para 
realizar esta tarea. Lo explicaremos con un ejemplo para que resulte más concreto
y sencillo de seguir. La generalización a cualquier otro caso es inmediata.

Supongamos que deseamos traducir una tabla denominada *frutas* que cuenta, en
principio, con los siguientes campos:

* *id (clave principal)*
* *nombre*
* *descripcion*
* *kcal_gramo*
* *proteinas_gramo*

Lo normal es que deseemos tener traducciones de los campos *nombre* y *descripcion*,
ya que el valor de los demás no dependen del idioma. Lo que se hace es repartir 
los campos en dos tablas, una que contendrá los campos cuyos valores son 
dependientes del idioma y la otra con los que no lo son:

* tabla *frutas*: (independiente del idioma)
  * *id (clave principal)*
  * *kcal_gramo*
  * *proteinas_gramo*
  
* tabla *frutas_i18n*: (dependiente del idioma)
  * *id (clave principal)*
  * *cultura (clave principal)*
  * *nombre*
  * *descripcion*

En la segunda tabla, denominada tabla de idiomas o tabla i18n, se especifica como 
clave principal el par [*id, cultura*], y el campo *id* se corresponde con el
campo *id* de la tabla frutas. De esta manera cada registro de la tabla *frutas*
puede tener N registros asociados de la tabla *frutas_i18n* con distintos valores
del campo *cultura*.

Ahora hay que especificar en el archivo *schema.yml* el hecho de que la tabla 
*frutas_i18n* contiene valores dependientes de la cultura para realizar las
traducciones:

*Contenido del archivo config/schema.yml para definir la internacionalizacion de
los campos de una tabla*

.. code-block:: yaml

	mi_conexion:
	  frutas:
		_attributes: { phpName: Frutas, isI18N: true, i18nTable: frutas_i18n }
		id:          { type: integer, required: true, primaryKey: true, autoincrement: true }
		kcal_gramo:      { type: float }
		proteinas_gramo: { type: float }
	  frutas_i18n:
		_attributes: { phpName: FrutasI18n }
		id:          { type: integer, required: true, primaryKey: true, foreignTable: frutas, foreignReference: id }
		culture:     { isCulture: true, type: varchar, size: 7, required: true, primaryKey: true }
		nombre:       { type: varchar, size: 50 }
		descripcion:  { type: varchar, size: 250 }


Observa los valores resaltados en negrita en la definición de ambas tablas, pues
son los que indican la internacionalización de la tabla *frutas*. En la tabla 
principal, es decir, la que no depende del idioma, se indica tanto que es
traducible (mediante el atributo *is18N*), como la tabla asociada que tiene sus
campos traducibles (mediante el atributo *i18nTable*). Por otro lado, se debe 
especificar en la tabla de traducciones cual es el campo que representa la
cultura (mediante el atributo *isCulture*).

Cuando generemos el modelo tendremos disponible un objeto denominado *Frutas* 
que se utilizará como cualquier otro. La peculiaridad es que cuando utilicemos
los métodos *getters* y *setter* correspondientes a los campos traducibles 
*nombre* y *descripcion*, *symfony* tendrá en cuenta la cultura de usuario 
especificada en la sesión, para mostrar o definir el valor que le corresponda 
al campo en función de tal cultura.

Por ejemplo, si la cultura de usuario es *'es_ES'*, el método *$fruta -> getNombre()*
devolverá el valor del registro de la tabla *frutas_i18n* con valor del campo 
*cultura 'es_ES'*, y si la cultura es *'en_UK'* pues el valor devuelto será el 
del registro de la tabla *frutas_i18n* con valor del campo cultura *'en_UK'*. 
Con menos palabras, el método devuelve la traducción que le corresponda en el
idioma de la cultura que tenga el usuario. Por supuesto para que esto ocurra deben
existir las traducciones en los idiomas que se manejen.


Enrutamiento
------------

La *URL (Uniform Resource Locator)* es una pieza clave del protocolo *HTTP*.
Mediante ellas se especifican de manera unívoca los recursos *HTTP* disponibles 
en la red. Son cadenas de texto que contienen toda la información necesaria para
solicitar un recurso a un servidor *web*. En dicha cadena, tal información aparece
estructurada utilizando algunos caracteres especiales como separador. Vamos a 
diseccionar una URL en cada una de sus partes. Partimos del siguiente ejemplo 
ficticio que representa una URL completa, con todos sus elementos obligatorios 
y opcionales:

``http://usuario:clave@nombredelhost.org:80/ruta/recurso.html?var1=valor1&var2=valor2#ancla``

La siguiente tabla muestra la *URL* diseccionada:

=========================== =====================================================
Parte                       Descripción
=========================== =====================================================
``http``                    Esquema o protocolo (obligatorio)

``usuario``                 Nombre de usuario (opcional)

``clave``                   Clave del usuario (opcional)

``nombredelhost.org``       Ubicación del *host*. Normalmente esta ubicación se 
                            hace mediante un nombre que está registrado en un 
                            espacio *DNS*, pero también se puede usar la 
                            dirección *IP* del *host* (obligatorio)
                     
``80``                      Puerto donde escucha el *host*. (opcional, si no se 
                            especifica se usa 80 como valor)
                     
``ruta/recurso.html``       Ruta interna del servidor *web* al recurso. 
                            (obligatorio)

``var1=valor1&var2=valor2`` Cadena con datos de la petición, más conocida como
                            *query string*. El carácter & se usa para separar 
                            los datos (opcional)
                            
``ancla``                   Fragmento del recurso que se desea mostrar (opcional)
=========================== =====================================================

A lo largo del curso hemos comprobado que las *URL's* que utiliza *symfony*
prescinden de los caracteres “?”, “&” y “=” propios de la *query string* dando 
lugar a *URL's* más homogéneas y “elegantes” que únicamente utilizan el carácter 
“/” como separador. Así una *URL* como la siguiente:

``http://nombredelhost.org/gestordocumental/index.php?module=gesdoc&action=verVersion&id_version=4``

En *symfony* se convierte en:

``http://nombredelhost.org/gestordocumental/index.php/gesdoc/verVersion/id_version/4``

Esta simplificación de la *URL* es posible gracias al sistema de enrutamiento 
de *symfony* el cual permite traducir *URL's* “estilizadas” como esta última, a 
*URL's* “clásicas” que son más largas y engorrosas como la primera. A las *URL's*
del tipo “estilizado”, *symfony* las denomina *URL's* **externas**, ya que son
las que un cliente manipula para realizar las peticiones, mientras que las *URL's*
“clásicas” son denominadas **internas**.

Observa que además de la “estilización” que provoca el hecho de eliminar los 
caracteres “?”, “&” y “=” de la *URL*, también se obtienen cadenas más cortas, 
ya que se prescinde del nombre de algunos parámetros. Concretamente no aparecen
en las *URL's* externas  los nombres de parámetro *module* y *action*; *symfony*
ya reconoce que los dos primeros valores tras el controlador se corresponden con
los parámetros *module* y *action* precisados por el *framework* para su ejecutar 
una determinada acción de un módulo. 

Así pues se ha conseguido estilizar y disminuir la longitud de las *URL* que se
manejan fuera de la aplicación. Pero lo más importante de todo es que se ha 
eliminado de la *URL* información acerca de la estructura de la aplicación,
manteniendo exclusivamente información que describe al recurso localizado por
dicha *URL*. Y es que idealmente las *URL's*, como “localizadores de recursos”
que son, deberían diseñarse de manera que ofreciesen información relacionada con
el recurso que tienen asignado y nada más, de manera que no se pueda deducir a
través de ella ningún detalle interno de la aplicación que proporcione el recurso
solicitado. Ni siquiera el lenguaje de programación con el que se ha programado. 
Esto último podemos lograrlo eliminando de la *URL* el nombre del controlador 
frontal (*index.php* normalmente) activando la opción *no_script_name* en el
fichero *settings.yml* de la aplicación:

*Activación del ocultamiento del nombre del controlador frontal de las URL's
externas de la aplicación.*

.. code-block:: yaml
	
	prod:
	  .settings:
		no_script_name:         true


Con este último ajuste las URL's externas de symfony quedarían como sigue:

``http://nombredelhost.org/gestordocumental/gesdoc/verVersion/id_version/4``

Ahora la *URL* informa únicamente sobre el recurso, sin dar ninguna información
sobre la tecnología utilizada para recuperar el recurso, ni sobre aspectos propios
de la propia aplicación como son el nombre de los parámetros *module* y *action*.
Es más, en principio no se sabe ni siquiera si se trata de un recurso estático 
o generado dinámicamente por una aplicación del lado de servidor. Aún queda en 
la *URL* el nombre del parámetro *id_version*. Veremos en breve como también 
podemos eliminarlo gracias a este gran invento que es el sistema de enrutamiento 
de *symfony*.

La pregunta que surge después de esta digresión es: si eliminamos los nombres de
los parámetros de las *URL's* externas, ¿cómo sabe la aplicación qué valores 
corresponde a qué parámetros?,¿cómo sabe lo que debe hacer con los valores que 
se le están pasando?.

Y la respuesta: mediante el sistema de enrutamiento, el cual utiliza una técnica
de mapeo de *URL's* externas estilizadas y desprovistas de información propia 
de la aplicación en *URL's* internas con toda la información que la aplicación
requiere (y viceversa). Este mapeo o transformación se realiza, como cualquier 
otro, según unas reglas predefinidas. De otra manera sería imposible añadir la 
información que falta cuando se realiza la transformación de *URL* externa a 
interna, o eliminar la información que sobra cuando la transformación es de *URL*
interna a externa. La clave del enrutamiento está, por tanto, en este conjunto 
de reglas, las cuales se definen en el archivo de configuración *routing.yml* de
la aplicación. 

En el momento en que se crea una nueva aplicación, el fichero *routing.yml* de
la misma muestra el siguiente contenido:

Archivo de rutas (routing.yml) de una aplicación recién generada

.. code-block:: yaml
	
	# default rules
	homepage:
	  url:   /
	  param: { module: default, action: index }
	
	# generic rules
	# please, remove them by adding more specific rules
	default_index:
	  url:   /:module
	  param: { action: index }
	
	default:
	  url:   /:module/:action/*


En él se definen 3 rutas (o reglas de enrutamiento) que son las rutas por defecto,
suficientes para desarrollar una aplicación con *symfony*. Aunque si deseamos que
nuestra aplicación presente *URL's* estilizadas para todas sus acciones, debemos
añadir nuevas reglas. 

Cada ruta consta de un nombre de la ruta (*homepage, default_index* y *default*,
son los nombres de las 3 rutas anteriores), de un patrón de la ruta identificado
por la clave *url*, y unos parámetros identificados por la clave *param*.

El nombre de la ruta puede ser cualquiera, pero debe ser único. Desde dentro de
la aplicación podemos referirnos a ellas anteponiendo el carácter '@' a su nombre.
Recuerda que hicimos esto en el capítulo anterior cuando adaptábamos el módulo 
de inicio de sesión a un *plugin* para que pudiese ser compartido por varias
aplicaciones.

La clave *url*, es un patrón que se aplica a las *URL's* externas (únicamente a
la parte que va inmediatamente después del controlador frontal, es decir, donde
se ubica la *query string* de la *URL*), de manera que en el momento en que se
encuentra una coincidencia se construye la *URL* interna a partir de los datos 
ofrecidos por la regla (ruta) en cuestión a través de las claves *url* y *params*.
Los patrones de ruta son cadenas de elementos separados por el carácter “/”. Cada
elemento puede ser un literal o un parámetro, en cuyo caso comienza con el 
carácter “:”.  

Vamos a mostrarlo con ejemplos, ya que es la mejor manera de comprender el
funcionamiento del *routing*. Comenzamos por interpretar las tres rutas creadas
por defecto cuando se genera una nueva aplicación.

La primera de ellas, denominada *homepage*, dice lo siguiente: “Cuando la *URL*
externa no lleve ningún elemento, entonces usa *default* como valor del parámetro
*module*, y *index* como valor del parámetro *action*”.

La segunda, denominada *default_index*, dice que: “Cuando la *URL* externa
presente un único elemento, se trata de un parámetro cuyo nombre es *module* (eso
es lo que significa el carácter “:” delante del elemento), y que se utilice 
*index* como valor del parámetro *action*”.

La tercera, denominada *default*, dice que: “Cuando la *URL* externa presente 
dos o más elementos, el primero es un parámetro cuyo nombre es *module* y el
segundo es otro parámetro cuyo nombre es *action* (observa de nuevo el carácter
“:”) el resto de los elementos deberían venir dados por pares del tipo
*nombre_parametro/valor_parametro*, para que la petición sea parseada 
adecuadamente”.

Estas tres reglas explican el comportamiento por defecto que venimos comprobando
desde el principio del curso de las rutas “estilizadas” de *symfony*.
Concretamente la última de ellas hace posible que para ejecutar una acción 
determinada de un módulo tan sólo tengamos que indicar los nombres de la acción
y el módulo en cuestión, obviando el nombre de los parámetros *module* y *action*.

Hemos dado una lectura de las reglas desde la perspectiva de transformar una *URL*
externa en una interna. No obstante las mismas reglas son utilizadas para 
transformar las *URL* internas en externas. Esto se hace desde dentro de la 
aplicación cuando utilizamos *helpers* de *URL* como *url_for()* o *link_to()*.
Existen varias formas de expresar las *URL's* internas, la más común de todas
ellas es la que venimos utilizando durante todo el curso:

*Forma típica de especificar una ruta interna en symfony:*

.. code-block:: bash

	nombre_modulo/nombre_accion?param1=valor1&param2=valor2&...'


En el ejemplo que venimos mostrando en este apartado, la transformación desde 
una plantilla de una *URL* interna en externa se haría así:

.. code-block:: php

        <?php
	url_for('gesdoc/verVersion?id_version=4');


Para lo cual estamos usando la regla denominada *default*. Pero también podemos
indicar la misma *URL* interna haciendo uso del nombre de la regla. En ese caso 
se especificaría de la siguiente forma:

.. code-block:: php
      
        <?php
	url_for('@default?module=gesdoc&action=verVersion&id_version=4');


Aún existe una tercera forma de especificar las url internas, más estructurada 
si cabe:

.. code-block:: php
	
	url_for(array(
	  'module'    => 'gesdoc',
	  'action'    => 'verVersion',
	  'id_version => 4
	));


Ahora que ya sabemos los fundamentos de la transformación de *URL* internas y 
externas (y viceversa) a través de las reglas definidas en el fichero de
configuración *routing.yml*, vamos a ver como podemos añadir nuevas reglas para 
estilizar aún más las rutas de las aplicaciones de nuestro proyecto de gestión
documental. Comenzamos con la aplicación *frontend*, que está compuesta por un 
sólo módulo denominado *gesdoc*. Repasaremos todas las acciones del módulo con
sus *URL* internas respectivas, y a partir de ellas realizaremos una propuesta
para las *URL* externas deseadas:

============================================= ==================================
URL interna                                   URL externa deseada
============================================= ==================================
*inises/signIn*                               *conectar*

*inises/signOut*                              *desconectar*

*gesdoc/index*                                */*

*gedoc/index?page=n*                          */pagina/n*

*gesdoc/verMetadatos?id_documento=n*          *metadatos/n*

*gesdoc/verVersion?id_version=n*              *version/n*

*gesdoc/modificar?id_documento=n*             *modificar/n*

*gesdoc/subirVersion?id_documento=n*          *nueva_version/n*
============================================= ==================================


Según lo que hemos explicado a lo largo de este apartado, el mapeo de rutas  
propuesto se puede conseguir añadiendo las siguiente reglas de enrutamiento al 
fichero *routing.yml* de la aplicación *frontend*.

*Rutas añadidas al archivo apps/frontend/config/routing.yml para estilizar todas
las acciones de la aplicación frontend.*

.. code-block:: yaml

	...
	conectar:
	  url: /conectar
	  param: { module: inises, action: signIn }
	 
	desconectar:
	  url: /desconectar
	  param: { module: inises, action: signOut }
	
	ver_metadatos:
	  url:   /metadatos/:id_documento
	  param: { module: gesdoc, action: verMetadatos }
	
	ver_version:
	  url:   /version/:id_version
	  param: { module: gesdoc, action: verVersion }
	
	modificar:
	  url:   /modificar/:id_documento
	  param: { module: gesdoc, action: modificar }
	
	subir_version:
	  url:   /nueva_version/:id_documento
	  param: { module: gesdoc, action: subirVersion }
	
	pagina:
	  url:   /pagina/:page
	  param: { module: gesdoc, action: index }
	...


Y para rizar el rizo podemos eliminar en el entorno de producción el nombre del
controlador frontal *index.php*:

*Activación del ocultamiento del nombre del controlador frontal de las URL's 
externas de la aplicación.*

.. code-block:: yaml

	prod:
	  .settings:
		no_script_name:         true


Ya puedes probar las nuevas rutas añadidas. No te olvides de borrar la cache 
antes de probar los cambios. Ahora las *URL* externas, que podemos considerar 
como parte integrante de la interfaz gráfica de usuario, presentan un aspecto
mucho más elegante de cara al usuario, a la vez que le ocultan datos estructurales
relativos a la aplicación, mejorando de esta forma la seguridad de la misma.

Por último, en la aplicación *backend* no tenemos que realizar ninguna 
estilización de las rutas ya que todos sus módulos han sido construidos con el
generador automático de módulos de administración, el cual se encarga de añadir 
al archivo *routing.yml* correspondiente las reglas necesarias para conseguir
ocultar la información sensible de las *URL* externas y estilizarlas. Échale un 
vistazo al archivo *routing.yml* de la aplicación *backend* y podrás comprobar 
el aspecto de estas rutas. Comprobarás que son más complejas que las que hemos 
descrito. Ello se debe a que utilizan el concepto de colecciones de rutas. 

.. note::
   
   Toda la información sobre las posibilidades que brindan las rutas de *symfony*
   las puedes encontrar en la guía de referencia.

   También puedes encontrar más explicaciones sobre el sistema de enrutamiento
   en la siguiente URL:

   *http://www.symfony-project.org/gentle-introduction/1_4/en/09-Links-and-the-Routing-System*
   

Conclusión
----------

En esta unidad hemos tratado dos nuevos aspectos que enriquecerán nuestras 
aplicaciones *web*: la internacionalización y el *routing*. Aspectos para los
que *symfony*, una vez más, ofrece potentes soluciones. 

La internacionalización es resuelta, tanto a nivel de interfaz de usuario como
de registros de la base de datos, mediante la existencia de un parámetro 
perteneciente a la sesión de usuario y que se denomina *culture*. Mirando el 
valor que dicho parámetro contiene en una determinada sesión, el *framework* 
sabe qué textos, en el caso de la interfaz de usuario, o qué registros, en el 
caso de la base de datos, debe mostrar. Además, para la traducción de la interfaz
de usuario se utilizan  los catálogo *XLIFF* que son ficheros *XML* estándar
bien conocidos en el gremio de los profesionales de la traducción, con lo que 
se facilita la intercomunicación y operatividad entre los programadores y los 
traductores, de la misma manera que la adopción del patrón *MVC* facilita el
trabajo entre programadores y diseñadores *web*.

Por otro lado el *routing* proporciona la estilización de las *URL's* que son 
manipuladas en la parte del cliente, es decir, las que aparecen tanto en la
barra de direcciones del navegador como en los enlaces que contengan los 
documentos *HTML* que reciba. Aunque en un primer momento esto no parezca muy
importante para la aplicación, pues no es imprescindible para que funcione, si
nos proponemos construir aplicaciones realmente  profesionales y seguras no 
tendremos más remedio que utilizar el *routing*. Por un lado simplifica y 
estiliza las largas *URL's* que se muestran en el cliente. Pero lo más importante
es que dicha simplificación se realiza eliminando información asociada a la 
estructura de la aplicación, información que no es necesaria para describir 
el recurso que se solicita y que podría ser utilizada por el usuario con fines
maliciosos. Es una regla básica de seguridad no dar más información que la
estrictamente necesaria.

Con estos dos “extras” finaliza el curso. Ya se han tratado los principales 
aspectos del *framework* con los que se pueden construir aplicaciones *web* de
calidad. De hecho el desarrollo de una aplicación completa ha vertebrado el
desarrollo del curso que ahora terminas. Esta misma aplicación, con más o menos 
cambios, junto con las herramientas que has adquirido durante el seguimiento del
curso, te servirán como semilla para desarrollar prácticamente cualquier tipo de
aplicación. No obstante, a nuestro modo de ver, lo más importante es que ya 
dispones del bagaje suficiente para sentirte cómodo con *symfony* y comenzar a
explorar cada una de sus características en profundidad a medida que las vayas
necesitando en tu trabajo.


.. raw:: html

   <div style="background-color: rgb(242, 242, 242); text-align: center; margin: 20px; padding: 10px;">
   <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/"><img alt="Licencia Creative Commons" style="border-width:0" src="http://i.creativecommons.org/l/by-nc-sa/3.0/88x31.png" /></a>
   <br />
   <span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Desarrollo de Aplicaciones web con symfony 1.4</span> por <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Juan David Rodríguez García (juandavid.rodriguez@ite.educacion.es)</span>
   <br/>
   se encuentra bajo una Licencia <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Reconocimiento-NoComercial-CompartirIgual 3.0 Unported</a>.
   </style>
   </div>











