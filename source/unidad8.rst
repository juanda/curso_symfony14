Unidad 8: El framework de formularios de Symfony
================================================

En esta unidad estudiaremos uno de los componentes más potentes y útiles de 
*symfony*; el sistema de formularios, el cual constituye por sí solo un *framework
MVC* independiente pero integrado en el conjunto global de *symfony*. Esto
significa que podemos utilizar este componente en otros proyectos *PHP* que no 
sean *symfony*.

En las primeras versiones de *symfony* los formularios se construían mediante
*helpers*, de forma que existía un *helper* para renderizar cada elemento de
control propio de un formulario *HTML*. A partir de la versión 1.1, *symfony*
presenta un sistema de formularios completamente rediseñado y que constituye una
colaboración de clases con dos responsabilidades fundamentales: construir el
código *HTML* de los diferentes elementos de control que lo componen, incluyendo
las *CSS's* y el código *javascript* que pudieran utilizar, y realizar la 
validación de los datos enviados por el cliente al servidor a través del
formulario. Es decir, dibujan y validan.

En esta unidad haremos uso de los formularios de *symfony* para finalizar la
implementación de las funcionalidades que aún faltan en la aplicación *frontend* 
de nuestro gestor documental, a saber:

* Creación de nuevos documentos.

* Modificación de los metadatos de los documentos.

* Subida de las versiones de los documentos numeradas automáticamente.

Con este objetivo bien definido nos disponemos a seguir rodeando, cada vez más
de cerca, las posibilidades de *symfony*.


Formularios HTML
----------------

Antes de comenzar el estudio de los formularios de *symfony*, creemos necesario
presentar algunos conceptos relativos a la producción y procesamiento de
formularios *HTML* en las aplicaciones *web*.


Componentes de la interfaz gráfica
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Los documentos *HTML*, a través de los formularios, pueden incorporar elementos
de control que el usuario utiliza para insertar datos que son enviados 
posteriormente al servidor para su procesamiento. En la siguiente tabla mostramos
el nombre de dichos elementos de control y un resumen de sus funcionalidades:

============== =================================================================

Nombre         Descripción

============== =================================================================

*input*        Es el elemento más utilizado ya que puede tomar varias formas en 
               función de su atributo *type*, el cual puede ser:

               * *text*: un texto en una línea,
               * *password*: un texto enmascarado,
               * *checkbox*: una casilla de selección que puede o no estar 
                 marcada. Si hay varias que compartan el mismo nombre pueden 
                 marcarse varias a la vez,
               * *radio*: una casilla de selección que puede o no estar marcada. 
                 Si hay varias que compartan el mismo nombre solo una puede 
                 marcarse a la vez,
               * *submit*: un botón para enviar el formulario al servidor,
               * *reset*: un botón para limpiar el formulario,
               * *file*: un seleccionador de ficheros
               * *hidden*: un campo oculto a la interfaz HTML,
               * *image*: un botón con una imagen asociada,
               * *button*: un botón para disparar una acción en el cliente.
               
*button*       Es como el *button* creado con el elemento *input* pero ofrece al
               programador más posibilidades para su renderizado. Puede ser de 
               tres tipos:

               * *submit*, para enviar un formulario al servidor,
               * *reset*, para limpiar el formulario y
               * *button*, para disparar acciones en el cliente.
               
*select*       Es un menú mediante el cual el usuario puede elegir una o varias
               opciones. Este elemento siempre lleva asociado uno o más elementos
               *options*.
               
*options*      Cada una de las opciones de un *select*.

*textarea*     Es una caja de texto que puede ocupara más de una linea, es decir,
               una caja de texto multilinea.
               
*form*         Es el elemento que actúa como contenedor de los anteriores 
               elementos. El atributo *action* especifican el *script* en el 
               servidor que procesará los datos introducidos por el usuario, y el
               atributo *method* el método *HTTP* que se usará para su envío:
               *GET* o *POST*.


Así pues un formulario *HTML* está constituido por un elemento *form* que
encierra a varios elementos de control. Alguno de estos elementos debe ser un 
botón de envío (*submit*) que, al ser pulsado por el usuario provoca, según se
haya especificado, una petición *GET* o *POST* al recurso del servidor indicado 
en el atributo *action* del formulario, con los datos introducidos por el usuario.

Combinando adecuadamente los elementos de control *HTML* se puede modelar
prácticamente cualquier componente típico de una interfaz gráfica de usuario,
aunque el resultado es bastante pobre si lo comparamos con las interfaces gráficas
de las típicas aplicaciones de escritorio. Sin embargo, si en combinación con el
*HTML* utilizamos hojas de estilo para mejorar el aspecto gráfico de los 
elementos de control y código *javascript* para aportar interactividad y efectos
visuales, podremos crear cualquier tipo de componente de control que se nos 
ocurra: barras de progresos, *sliders*, navegación por pestañas, menús avanzados, 
navegación tipo acordeón, incluso podemos inventarnos nuevos componentes. Cuando
se utilizan *CSS's* en combinación con código *javascript* para mejorar la
interfaz de las aplicaciones *web* se dice, de una manera muy descriptiva, que se
ha **enriquecido** el *HTML*. Muchos de los productores de los sitios *web* más
famosos en la actualidad, como pueden ser *facebook* o *google*, incorporan, cada 
vez más, interfaces enriquecidas a sus aplicaciones. Como veremos, una de las 
ventajas de utilizar los formularios de *symfony* es que podemos crear y 
encapsular en objetos reutilizables componentes de control combinando elementos 
*HTML, CSS's* y *javascript*.


Procesamiento de los datos introducidos en el formulario.
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

La función de los formularios *HTML* es recoger datos del usuario cliente y
enviarlos al servidor para hacer algo con ellos (almacenarlos en una base de
datos, realizar un complejo cálculo para confeccionar una carta astral o 
cualquier otra cosa). 

El navegador *web* envía los datos realizando una petición *HTTP* al servidor,
la cual, según la especificación del protocolo *HTTP*, puede ser de varios tipos
en función de lo  que el cliente tenga intención de hacer con los datos enviados 
sobre el servidor. Las operaciones *HTTP* más conocidas que un servidor *web*
pone a disposición de los clientes a través de su *interfaz uniforme* son *GET,
POST, PUT* y *DELETE*, la cuales pueden ser comparadas semánticamente con las 
operaciones de una base de datos:

============= ============================ =====================================

Método HTTP   Operación en base de datos   Descripción

GET           Retrieve                     Recupera un recurso/registro. No hay 
                                           modificación del mismo
                                           
POST          Update                       Modifica un recurso/registro. 

PUT           Create                       Crear un recurso/registro

DELETE        Delete                       Elimina un recurso/registro

============= ============================ =====================================

Los navegadores *web* actuales tan sólo soportan las dos primeras operaciones 
(*GET* y *POST*) para realizar peticiones al servidor y enviarle datos. Ahora
bien, ¿cuál de las dos debemos utilizar en nuestros formularios *HTML*? La
respuesta ya la hemos insinuado unas línea más atrás al comparar los métodos 
*HTTP* con las operaciones de una base de datos: si en el proceso de servidor 
que recibe los datos se va a llevar a cabo algún tipo de modificación (ya sea
en bases de datos, ficheros o cualquier otro tipo de recurso) debemos utilizar
*POST* que se correspondería con una operación de actualización, pero si dicho
proceso utiliza los datos enviados tan solo para recuperar algún recurso, **sin
que haya ningún tipo de modificación en los datos que gestiona la aplicación**,
entonces debemos utilizar *GET*.

Además de los matices semánticos de ambas operaciones, existe una diferencia bien
visible: En el caso de una petición del tipo *GET*, los datos se envían como
parámetros que forman parte de la *URL*, tal y como mostramos en este ejemplo:

``http://www.elservidor.es/recurso?param1=valor1&param2=valor2``

mientras que en las peticiones *POST* los datos viajan encapsulados en la sección
de datos de la petición *HTTP*. Además mediante *POST* se pueden enviar ficheros
al servidor indicando en la petición que el tipo de contenido (*content-type*) de
los datos enviados es *multipart/form-data*. Esta indicación se realiza a través
del parámetro *enctype* del elemento *form*.

Es muy importante que comprendamos que las peticiones *HTTP* se realizan desde
un cliente sobre el cual, obviamente, el servidor no tiene ningún tipo de control
directo, de manera que el cliente puede enviar al servidor los parámetros que
quiera, saltándose lo prescrito por el formulario. Expliquemos esto con más
detalle. La secuencia normal que seguiría una aplicación *web* para pedir datos
al cliente y procesarlos sería:

1. El servidor envía un formulario *HTML* al cliente con los elementos de control
   que el usuario utilizará para introducir los datos.

2. El navegador interpreta el documento *HTML* y presenta el formulario al usuario

3. El usuario introduce los datos y envía el formulario relleno, es decir, pica
   en el botón *submit* y el navegador construye una petición *HTTP* al proceso 
   de servidor indicado en el parámetro *action* del formulario y con los datos 
   que el usuario ha introducido.

4. El servidor recibe la petición con los datos esperados y los procesa.

Sin embargo no hay nada que impida saltarse el paso 3 y construir en el cliente,
sin utilizar el navegador, una petición *HTTP* al proceso de servidor con 
cualquier tipo de datos. Esto significa que el servidor **nunca** debe fiarse de
los datos que traen las peticiones, ya que han podido ser manipuladas en el
cliente y no tienen por que obedecer a lo que el programador espera, dando lugar 
a posibles brechas de seguridad en la aplicación. Conclusión: **Los procesos de 
servidor deben validar TODOS los datos que le llegan antes de realizar ninguna
operación con ellos**. Observa que el hecho de introducir validadores en el 
cliente utilizando código *javascript* no vale de nada, ya que como acabamos de 
indicar, el usuario puede “puentear” completamente el uso del navegador, y por
tanto del formulario enviado, para construir y lanzar la petición con los datos 
que desee. Este hecho junto con el desconocimiento, la pereza o la escasa
destreza del programador para blindar sus aplicaciones con los adecuados 
validadores del lado del servidor, dan lugar a una de las vulnerabilidades más
comunes de las aplicaciones *web*. Los validadores de *symfony*, que forman parte
del *framework* de formularios, ofrecen una elegante y obligatoria solución a 
este problema.


Estructura de los formularios de Symfony
----------------------------------------

Los formularios de *symfony* están compuestos por dos tipos de objectos: los 
*widgets* y los validadores. Los primeros sirven para realizar la presentación
del elemento de control en el documento *HTML*, y por tanto en el navegador, y
los segundos para realizar la validación de los datos que llegan en las 
peticiones *HTTP* al servidor. El siguiente diagrama de clases *UML* representa
la estructura de un formulario *symfony*.







El programador utiliza los métodos del formulario tanto para la presentación del 
mismo como para su validación. Internamente el formulario  se encarga de realizar
la coordinación entre los *widgets* y los validadores.

Para el estudio de los formularios describiremos la estructura y el funcionamiento
de *widgets* y validadores de manera independiente, fuera del formulario. De 
hecho podemos utilizar en nuestras aplicaciones unos y otros directamente, sin 
necesidad de incorporarlos en un formulario. Posteriormente veremos como se 
definen los formularios asociándoles *widgets* y validadores y cómo se utilizan 
en las aplicaciones *web* construidas con *symfony*.


Los widgets
^^^^^^^^^^^

Los *widgets* de *symfony* son objetos que derivan de la clase *sfWidgetForm*
Esta clase define una interfaz común mediante la que se pueden realizar las
operaciones necesarias para la definición y renderizado del *widget*.

La siguiente tabla muestra algunos de los *widget* más utilizados. Todos ellos 
derivan de la clase base *sfWidgetForm*.

============================ ====================================================

Nombre del widget            Funcionalidad 

============================ ====================================================

*sfWidgetFormInput*          Es una caja de texto de una sola línea. Representa
                             un elemento *HTML input* del tipo *text*
                             
*sfWidgetFormInputCheckBox*  Es una casilla para marcar/desmarcar. Representa un
                             elemento *HTML input* del tipo *checkbox*
                             
*sfWidgetFormInputHidden*    Es un elemento que contiene un dato oculto al
                             navegador. Representa un elemento *HTML input* del 
                             tipo *hidden*

*sfWidgetFormInputPassword*  Es una caja de texto para introducir datos 
                             enmascarados. Representa un elemento *HTML input*
                             del tipo *password*
                             
*sfWidgetFormInputFile*      Es una caja de texto con un botón que un navegador
                             para buscar e incorporar un fichero que será enviado
                             en la petición. Representa un elemento *HTML input*
                             del tipo *file*
                             
*sfWidgetFormTextArea*       Es una caja de texto multilínea. Representa un 
                             elemento *HTML textarea*
                             
*sfWidgetFormChoice*         En realidad este *widget* está compuesto por los
                             cuatro *widgets* mostrados en el diagrama anterior
                             y, en función de como lo configuremos, delegará el 
                             renderizado al que le corresponda. Es un *widget* 
                             que se utiliza para realizar operaciones de 
                             selección, a través de menús desplegables de 
                             selección simple o múltiple, conjuntos de 
                             *radiobuttons* o conjuntos de cajas de selección. 
                             Por lo tanto puede representar distintos tipos de 
                             elementos de selección *HTML*.
                             
*sfWidgetFormDate*           Es un *widget* mediante el que se pueden introducir
                             fechas en distintos formatos de localización según 
                             como se haya configurado. Representa tres elementos
                             *HTML* de tipo *select*, uno para cada elemento de 
                             la fecha: día, mes y año.


Todos los tipos de *widget* tienes dos propiedades importantes; las opciones 
(*options*) y los atributos (*attributes*). Las primeras se utilizan para 
configurarlo y la segunda representan los atributos *HTML* que se asociarán al
elemento de control *HTML* que será dibujado por el *widget*. Por otro lado, el 
método esencial de cualquier *widget* se denomina *render()* y sirve para arrojar 
el código *HTML* que le corresponda en virtud de su tipo y de su definición
(opciones + atributos).

¿Cómo se utiliza un *widget* en la práctica? Para explicarlo vamos a suponer que
deseamos mostrar en alguna de las vistas de nuestro proyecto una caja de texto 
simple y una entrada para fechas, para lo cual utilizamos los *widget 
sfWidgetFormInput*, y *sfWidgetFormDate*.


.. note::

   para realizar el ejemplo vamos a crear una acción y una vista asociada en el
   módulo *gesdoc* de la aplicación *frontend* que más tarde eliminaremos puesto
   que no forma parte de la aplicación.


Comenzamos declarando en la acción los objetos *sfWidgetFormInput* y 
*sfWidgetFormDate* :

*Trozo del archivo: apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	...
	 public function executePruebaWidget(sfWebRequest $request)
	 {
		$this -> wInput = new sfWidgetFormInput();
		$this -> wInput -> setOptions(array('default' => 'prueba'));
		$this -> wInput -> setAttributes(array('class' => 'miclase', 'onblur' => "alert('hola')"));
	
		$this -> wDate = new sfWidgetFormDate(array('format' => '%day% - %month% - %year%'), array('class' => 'fecha'));
	
	 }
	...

En este ejemplo hemos definido dos *widgets* de dos formas equivalente. En el
caso del *sfWidgetFormInput*, primero lo declaramos y después le asociamos las 
opciones y los atributos mediante los métodos *setOptions()* y *setAttributes()*.
En el caso del *sfWidgetFormDate* realizamos la declaración y la configuración 
en un solo paso facilitando como argumentos del constructor del objeto dos *arrays*,
el primero con las opciones y el segundo con los atributos. Repetimos, ambas 
formas son equivalentes. Además hay que advertir que cada *widget* tiene sus 
propias opciones. La mejor manera de conocer cuales son las opciones que acepta 
cada *widget* es consultando directamente el código fuente donde se definen. 
Dicho código lo puedes encontrar en el directorio *widget* del núcleo de *symfony*.

Ahora podemos utilizar estos objetos en la plantilla correspondiente para dibujar
tantas cajas de texto y entradas de fechas como queramos. Para hacer esto 
utilizamos el método *render()*, el cual admite tres argumentos: el nombre del 
*widget*, el valor por defecto, y los atributos *HTML* que deseemos añadir a los
que ya se han añadido cuando configuramos el *widget*. Únicamente el primero de 
los argumento es obligatorio y se utiliza para asignar el atributo *name* de los
elementos *HTML* correspondientes. Crea la plantilla *pruebaWidgetSuccess.php*
con el siguiente contenido:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/pruebaWidgetSuccess.php*

.. code-block:: php

	<div id="sf_admin_header">
		<h2>Prueba de widgets</h2>
	</div>
	
	<div id="sf_admin_content">
	
		<?php echo $wInput -> render('nombre',ESC_RAW) ?>
		<br/>
		<br/>
		<?php echo $wInput -> render('apellido','rodriguez', array('class' => 'otra'), ESC_RAW) ?>
		<br/>
		<br/>
		<?php echo $wDate -> render('fecha1',ESC_RAW) ?>
		<br/>
		<br/>
		<?php echo $wDate -> render('fecha2',ESC_RAW) ?>
	
	</div>

Es importante que observes el efecto de lo que hemos hecho en el código *HTML*
que llega al navegador *web*. Fíjate en el valor del atributo name de los
elementos *HTML*. En el caso de la caja de texto coincide con el primer
argumento del método *render()*, pero en el caso de las entradas de fecha no
coincide, si no que forma parte del nombre. Esto no puede ser de otra manera ya
que la entrada de fechas consta de tres cajas de texto, y cada una debe tener 
un nombre único para ser identificada cuando los datos lleguen al servidor. Así
que el *widget* asigna como nombre para la caja de los días *fecha1[day]*, para
la caja de los meses *fecha1[month]* y para la de los años *fecha1[year]*.


.. note::

   Es un buen momento para que juegues con este ejemplo cambiando opciones y 
   parámetros y probando nuevos *widgets*. En esta *URL* encontrarás abundante
   documentación acerca de los *widgets* de *symfony*, de su configuración y 
   renderizado:
   ``http://librosweb.es/symfony_formularios/capitulo12.html``


Los validadores
^^^^^^^^^^^^^^^

Los validadores de *symfony* son objetos que derivan de la clase *sfValidatorBase*.
Esta clase define una interfaz común mediante la que se pueden realizar las 
operaciones necesarias para la validación de los *widgets*. Además de realizar 
la validación de los datos que llegan al servidor, estos objetos también se 
encargan de limpiarlos según se les haya indicado en su configuración.

La siguiente tabla muestra algunos de los validadores más utilizados. Todos ellos 
derivan de la clase base *sfValidatorBase*.

======================== =======================================================

Nombre del validador     Funcionalidad 

======================== =======================================================

*sfValidatorString*      Comprueba que el dato enviado es una cadena de 
                         caracteres
                         
*sfValidatorEmail*       Comprueba que el dato enviado es una cadena de 
                         caracteres que se corresponde con una dirección de
                         correo electrónico
                         
*sfValidatorInteger*     Comprueba que el dato enviado es un valor entero

*sfValidatorNumber*      Comprueba que el dato enviado es un valor numerico
                         (*float*)
                         
*sfValidatorDate*        Comprueba que el dato enviado se corresponde con una
                         fecha
                         
*sfValidatorFile*        Comprueba que el dato enviado es un fichero válido. 
                         Además, como veremos más adelante, en caso de que el
                         fichero cumpla lo que la configuración del validador 
                         exige, devuelve un objeto del tipo *sfValidatedFile*
                         que encapsula al fichero y proporciona una gestión
                         sencilla del mismo.
                         

Todos los tipos de validadores tienes dos propiedades importantes; las opciones 
(*options*) y los mensajes (*messages*). Las opciones se utilizan para especificar
los requisitos del validador, es decir para definir cómo deben ser los datos 
válidos, y los mensajes sirven para indicar el mensaje que se mostrará si el dato
no cumple lo exigido.

El método principal de un validador se denomina *clean()* y lleva a cabo las
siguientes acciones:

1. Limpia el valor de espacios en blancos antes y después del dato si la *opcion
   trim* se ha especificado.

2. Chequea si el dato está vacío.

3. Comprueba si el dato cumple lo que requiere la configuración del validador.

En todos los casos el validador devuelve el valor limpio del dato en caso de que
sea válido, y si no lo es lanza una excepción con el mensaje que se haya indicado
en la configuración.

Ahora vamos a mostrar el funcionamiento de los validadores en la práctica. Para
ello creamos una nueva acción de prueba que denominaremos *executePruebaValidadores*
y su plantilla asociada *pruebaValidadoresSuccess.php*:

*Trozo de código del archivo: 
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: php
	
	public function executePruebaValidadores(sfWebRequest $request)
	{
		$strValidator = new sfValidatorString();
	
		$strValidator -> setOptions(array('required' => true, 'max_length' => '6', 'trim' => true));
		$strValidator -> setMessages(array('required' => 'el dato es requerido', 'max_length' => '%value% es demasiado larga, el máximo es 6'));
	
		$dato = "  hola";
		$this -> dato_limpio = $strValidator -> clean($dato);
	}

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/pruebaValidadoresSuccess.php*

.. code-block:: php

	<div id="sf_admin_header">
		<h2>Prueba de validadores</h2>
	</div>
	
	<div id="sf_admin_content">
	   dato limpio:<?php echo $dato_limpio ?>
	
	</div>

Como puedes ver primero hemos definido un validador y después lo hemos 
configurado indicando que el valor es requerido (no puede estar vacío), que debe
tener una longitud máxima de 6 caracteres y que hay que limpiarlo de espacios. 
También se han definido los mensajes de error que la excepción debe mostrar 
cuando la validación no sea satisfactoria. Para ello hemos utilizado los métodos
*setOptions()* y *setMessages()* pero, de la misma forma que ocurre con los 
*widgets*, podríamos haber realizado la declaración del validador y su 
configuración al mismo tiempo:

.. code-block:: bash
	
	  $strValidator = new sfValidatorString(
					array('required' => true, 'max_length' => '6', 'trim' => true),
					array('required' => 'el dato es requerido', 'max_length' => '%value% es demasiado larga, el máximo es 6')
			);

Después hemos definido una variable *valor* que contiene una cadena y la
validamos usando el método *clean()* del validador. En la plantilla simplemente
mostramos el valor devuelto por el validador. Si ejecutas la acción verás que se
muestra la cadena “hola” sin espacios al principio. Prueba ahora a cambiar el
valor de la variable dato por una cadena vacía y después por una cadena que tenga
más de 6 caracteres. Verás como se lanza una excepción con el mensaje que hemos 
definido para cada caso.

Llegados a este punto debemos de aclarar que aunque hemos mostrado como utilizar
los validadores independientemente, lo normal es utilizarlos a través de los
formularios que estudiaremos en el siguiente apartado. En tal caso no es necesario
utilizar el método *clean()* ya que el formulario se encarga de gestionar el
validador de manera transparente.

.. note::

   Es un buen momento para que juegues con este ejemplo cambiando opciones y 
   parámetros y probando nuevos validadores. En esta *url* encontrarás abundante
   documentación acerca de los validadores de *symfony* de su configuración y 
   funcionamiento:

   ``http://librosweb.es/symfony_formularios/capitulo13.html``


Los formularios
^^^^^^^^^^^^^^^

Y por fin los formularios. Como ya hemos indicado anteriormente un formulario de 
*symfony* se compone de *widgets* y validadores. Una vez declarado y definido, el
formulario es un objeto que utilizamos para tres funciones distintas:

1. Presentarlos en la vista como un formulario *HTML*.

2. Presentar los errores, si los hubiera, en cada uno de los campos que no hayan 
   pasado la validación.

3. Validar en el servidor los datos enviados desde el cliente a través de una 
   petición *HTTP*.
   
   
.. note::

   Desde la versión 1.3 los formularios proporcionan un mecanismo de seguridad 
   para evitar un tipo de ataque denominado *cross-site request forgeries* (*csrf*)
   y que es *“un tipo de exploit malicioso de un sitio *web* en el que comandos 
   no autorizados son transmitidos por un usuario en el cual el sitio *web* 
   confía”* (fuente: wikipedia). Este ataque se evita haciendo que el servidor,
   cuando envía un formulario al cliente, pase un campo oculto con un *token* de
   seguridad generado para ese formulario y usuario en concreto. Cuando el cliente
   envía el formulario el servidor comprueba si se ha devuelto ese mismo *token*,
   de esa manera se asegura de que la respuesta se ha realizado desde el mismo 
   formulario que se envió originalmente. Este tipo de vulnerabilidad es muy 
   esquiva y sutil y no vamos a estudiarla en profundidad. Simplemente debemos
   saber que *symfony* ofrece por defecto un mecanismo para combatirla que se 
   puede desactivar eliminando el parámetro *csrf_secret* del archivo de 
   configuración *apps/nombre_aplicacion/config/setting.yml*. En realidad, 
   utilizar esta funcionalidad en nuestros formularios, como veremos, es
   prácticamente transparente en la programación, por tanto recomendamos su uso
   si quieres mejorar la seguridad de tu aplicación.


Ilustraremos el funcionamiento de los formularios con un ejemplo sencillo que 
nos permitirá comprender el flujo de acciones que tiene lugar en cualquier 
operación en la que una aplicación *web* solicita un conjunto de datos al cliente
para realizar algún tipo de proceso. Las operaciones, a alto nivel, que tienen
lugar en un lado y otro son las descritas en este esquema:

1. El servidor envía el formulario *HTML* al cliente.

2. El usuario introduce los datos en el formulario que le muestra el navegador 
   y lo envía de nuevo al servidor.

3. El servidor valida los datos

4. Si los datos son válidos se procesan y se devuelve al cliente algún mensaje
   indicando que la operación se ha realizado con éxito.

5. Si los datos no son válidos el servidor envía de nuevo el formulario al
   cliente con los datos que este ya envió y con los mensajes de error que
   indican qué datos han provocado el rechazo y por qué.

Supongamos que deseamos pedir al usuario su nombre y su correo electrónico para
hacer cualquier cosa con estos datos (almacenarlos en una base de datos, por 
ejemplo). Comenzamos por definir el formulario que satisface lo requerido, el
cual se declara como una clase que deriva de la clase base *sfForm*. Siguiendo
la estructura de directorios de *symfony*, el lugar para declarar el formulario
es alguno de los directorios *lib*; el del proyecto, el de la aplicación o el 
del módulo, depende de cual sea el alcance que deseemos darle. En este ejemplo 
lo vamos a definir en el directorio *lib* de la aplicación:

*Contenido del archivo: apps/frontend/lib/FormularioEjemplo.class.php*

.. code-block:: php

	<?php
	
	class FormularioEjemplo extends sfForm
	{
		public function configure()
		{
			$this -> setWidgets(array(
					'nombre' => new sfWidgetFormInput(),
					'email'  => new sfWidgetFormInput(),
			));
	
			$this -> widgetSchema -> setNameFormat('contacto[%s]');
	
			$this -> setValidators(array(
					'nombre' => new sfValidatorString(
					array('required' => true, 'max_length' => 40),
					array('required' => 'Este campo no se puede dejar en blanco',
							'max_length' => 'el nombre es demasiado largo, 40 caracteres máximo')),
					'email' => new sfValidatorEmail(
					array('required' => true),
					array('required' => 'Este campo no se puede dejar en blanco'))
			));
	
		}
	}

Como puedes observar en el código anterior, para definir un formulario basta con
declarar un método llamado *configure()*, y definir el conjunto de *widgets* y
de validadores que compondrán el formulario. Tanto un conjunto como otro se 
corresponden con un *array* asociativo en el que la clave será el nombre del
campo en el documento *HTML*, en nuestro caso *nombre* y *email*. Tanto el *array*
de *widgets* como el de validadores deben tener los mismos valores de las claves,
ya que estas representan los nombres de los campos del formulario, y de esta
manera se podrá realizar la correspondencia entre el *widget* y el validador. 

Un detalle importante a la hora de organizar los datos y, como veremos más tarde 
imprescindible para llevar a cabo la validación de los datos, es declarar un 
formato de nombres. Esto se hace en el código anterior en la línea siguiente:

*Contenido del archivo: apps/frontend/lib/FormularioEjemplo.class.php*

.. code-block:: bash

	...
	$this -> widgetSchema -> setNameFormat('contacto[%s]');
	...

El efecto de esta línea es que los nombres de cada uno de los controles que 
componen el formulario en el documento *HTML* enviado al cliente, seguirán el 
siguiente formato: *contacto[nombre_campo]*. Esta característica permite que, 
una vez devueltos al servidor, los datos que provienen de un mismo formulario 
son los elementos de un *array* asociativo cuyas claves son los nombres de los 
campos. De esta manera es mucho más sencillo manipularlos. Por ejemplo se pueden
recorrer rápidamente haciendo uso de un *foreach*.

Ahora creamos una acción en el módulo *gesdoc* de la aplicación *frontend* que
llamaremos *executePruebaFormulario*:

*Trozo de código del archivo: 
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executePruebaFormulario(sfWebRequest $request)
	{
		$this -> formulario = new FormularioEjemplo();
	}

Y lo dibujamos en la plantilla correspondiente:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/pruebaFormularioSuccess.php*

.. code-block:: bash

	<div id="sf_admin_header">
		<h2>Prueba de formulario</h2>
	</div>
	
	<div id="sf_admin_content">
		<form name="form" action="<?php echo url_for('gesdoc/pruebaFormulario') ?>" method="post">
			<?php echo $formulario -> renderHiddenFields() ?>
			<?php echo $formulario -> renderGlobalErrors() ?>
			<?php echo $formulario ?>
	
			<input type="submit" />
		</form>
	</div>

Ahora puedes probar la acción que acabamos de escribir. Verás que el formulario
se pinta pero no queda demasiado bien. Esto es así por que lo hemos lanzado de
un “tirón” y el objeto se pinta, obviamente, sin tener en cuenta la estructura 
de nuestra *CSS's* (sería demasiado listo si lo hiciera). Sin embargo esta forma 
sencilla de pintar el formulario nos puede venir bastante bien en la primera etapa
del desarrollo de una aplicación, cuando lo que deseamos es probar la 
funcionalidad sin tener en cuenta el diseño.

A continuación vamos a manipular el objeto formulario en la vista para acceder 
a las distintas partes que lo componen: la etiqueta, el control, el mensaje de
error, los mensajes globales y los campos ocultos. De esta manera podremos 
encajarlo con cualquier estructura *HTML* que se necesite para utilizar una 
*CSS* determinada.

La plantilla anterior quedaría de la siguiente manera:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/pruebaFormularioSuccess.php*

.. code-block:: bash

	<div id="sf_admin_container">
		<h1>Formulario</h1>
	
		<div id="sf_admin_header">
			<div class="notice">Mensaje de advertencia</div>
		</div>
	
		<div id="sf_admin_content">
			<div class="sf_admin_form">
				<form name="form" action="<?php echo url_for('gesdoc/pruebaFormulario') ?>" method="post">
					<?php echo $formulario -> renderHiddenFields() ?>
					<?php echo $formulario -> renderGlobalErrors() ?>
					<fieldset id="fieldset_1">
						<h2>Contacto</h2>
	
						<div class="sf_admin_form_row">
							 <?php echo $formulario['nombre'] -> renderError() ?>
							<div>
								<?php echo $formulario['nombre'] -> renderLabel() ?>     
								<?php echo $formulario['nombre']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['email'] -> renderError() ?>
							<div>
								<?php echo $formulario['email'] -> renderLabel() ?>                           
								<?php echo $formulario['email']->render() ?>
							</div>
						</div>
	
					</fieldset>
					<input type="submit" />
				</form>
			</div>
		</div>
	
		<div id="sf_admin_footer">
		</div>
	</div>

Aunque esta nueva plantilla es algo más compleja que la anterior, fíjate que la
información dinámica es la misma y se obtiene con los métodos *renderLabel()*,
*renderError()* y *render()* de cada uno de los campos que componen el formulario.
Lo demás es pura cobertura de diseño *HTML* necesaria para una correcta 
visualización con las *CSS's* que utilizamos. Los nombres de los método son
autodescriptivos y no vamos a explicarlos para evitar redundancia. Si diremos que
es muy importante, si utilizamos la protección contra ataques *CSRF*, utilizar el
método *renderHiddenFields()*, ya que es el encargado de arrojar el campo oculto 
con el *token csrf*. 

Ya tenemos el formulario en el cliente. Ahora toca recibir los datos y 
procesarlos siempre que estos sean válidos según los especificado en la 
declaración del formulario. Vamos a utilizar la misma acción con la que hemos 
enviado el formulario para recibir sus datos. Fíjate en el atributo *action* del
formulario para comprobarlo. Esto no tiene por que ser así, es la estrategia que
aquí seguiremos y con la que vamos a construir un esquema general mediante el que 
se pueden tratar casi todos los casos de solicitud y envío de datos a través de 
formularios en aplicaciones *web* construidas con *symfony*. 

Modificamos, por tanto, la acción *executePruebaFormulario()* de manera que sirva
tanto para el envío del formulario como para la recepción y procesamiento de los
datos devueltos por el cl¡ente. El código siguiente muestra dichos cambios:

*Trozo de código del archivo: 
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executePruebaFormulario(sfWebRequest $request)
	{
		$this -> formulario = new FormularioEjemplo();
	
		if($request -> isMethod('post')) // La acción ha sido invocada por el envío de un formulario
		{
			$datos = $request -> getParameter('contacto'); // $datos es un array con los datos de la petición
			$this -> formulario -> bind($datos); // asociamos los datos de la petición al formulario
			if($this -> formulario -> isValid()) // Y comprobamos la validez de los datos
			{
			  // Aquí procesamos los datos. Por lo pronto solo los mostramos
			  // en una plantilla construida para ello
	
				$this -> nombre = $this -> formulario -> getValue('nombre');
				$this -> email  = $this -> formulario -> getValue('email');
				$this -> setTemplate('muestraDatos');
			}
		}
	 }

En negrita se han resaltado los cambios necesarios para implementar la lógica que
hemos propuesto anteriormente. En primer lugar se comprueba si la petición es del
tipo *POST*, si no lo fuera el resultado de la acción es equivalente a la 
original, es decir se pasa el formulario (vacío) a la plantilla y se envía el
resultado al cliente. Pero si el tipo de petición es *POST* significa que la 
acción ha sido invocada por el envío del formulario. Entonces, usando el método
*bind()* del formulario, se realiza la asociación entre el formulario recién 
definido y los datos enviados desde el cliente que se encuentran encapsulados en
un *array* asociativo. Accedemos a estos datos, envueltos en la petición *HTTP*,
a través del objeto *$request*. Una vez realizada la asociación le pedimos al
formulario que valide los datos asociados según los requisitos especificados en
los validadores del formulario. Para lo cual usamos el método *validate()*. Si 
la validación tiene éxito se procesan los datos. En nuestro ejemplo simplemente
los pasamos a una plantilla para que se muestren en el cliente. Si los datos no 
son válidos entonces se vuelve a pintar el formulario, pero esta vez los métodos 
*renderError()* de los campos que han fallado en la validación arrojan el mensaje
que indica la causa del error. Simple y elegante. 

A continuación mostramos el contenido de la plantilla *muestraDatosSuccess.php*
necesaria para finalizar nuestro ejemplo.

*Código de la plantilla: 
apps/frontend/modules/gesdoc/templates/muestraDatosSuccess.php*

.. code-block:: bash

	<div id="sf_sf_admin_container">
		<h1>Datos</h1>
	
		<div id="sf_sf_admin_content">
	
			Hola <?php echo $nombre ?>, este es tu e-mail: <?php echo $email ?>
		</div>
	</div>

Pues bien, lo estudiado hasta ahora es suficiente para desarrollar cómodamente
las funcionalidades que nos faltan para completar la aplicación *frontend* de
nuestro gestor documental. Observa en los siguientes apartados cómo la estructura
esencial del envío del formulario, la validación y el proceso de los datos es
exactamente la misma  a la que acabamos de explicar y desarrollar en este
apartado. A pesar de que la cantidad de código será obviamente mayor, debido a 
las complicaciones propias del proceso de datos. 


Creación y modificación de documentos.
--------------------------------------


Los requisitos del gestor documental enunciaban que los usuarios con perfil autor 
podrían crear nuevos documentos caracterizados por los siguientes  metadatos:

* título
* descripción
* autor del documento
* ¿es público?
* Categorías

Además cada documento podría tener asociado muchos ficheros que representan las
distintas versiones del mismo. Las versiones de cada documento tendrían una 
numeración consecutiva que sería asignada automáticamente mediante un proceso 
descrito en la unidad 4. Por otro lado, los autores podrían editar los metadatos
pero las versiones no podrían ser eliminadas ni, por tanto, los documentos. En
este punto es importante que vuelvas a repasar la unidad 4 y que la tengas 
dispuesta para ser consultada durante el desarrollo del resto de esta unidad. 

Lo primero que haremos es definir los formularios que necesitamos; uno para 
introducir los datos de los documentos y otro para las versiones.


El formulario DocumentosForm
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

El usuario autor, cuando vaya a crear un nuevo documento tendrá que introducir 
los metadatos y, opcionalmente, el archivo que se corresponde con la primera 
versión del mismo. Cuando estos datos lleguen al servidor y sean validados 
favorablemente, el proceso asociado a la creación del nuevo documento insertará 
un registro en la tabla *documentos* y, si se ha subido un fichero, otro en la 
tabla *versiones* relacionado con el primero. Además el fichero físico subido al
servidor se alojará en un directorio asociado al autor que lo ha enviado y se le
asignará automáticamente un nombre según el proceso que ya hemos mencionado 
anteriormente.

El siguiente formulario, que denominaremos *FormularioDocumentos* y que 
ubicaremos en el directorio *lib* de la aplicación *frontend*, contiene los 
campos necesarios para que el usuario comunique a la aplicación la creación de
un documento nuevo.

*Contenido del archivo: apps/frontend/lib/FormularioDocumentos.class.php*

.. code-block:: php

	<?php
	
	class FormularioDocumentos extends sfForm
	{
		public function configure()
		{
			$this->setWidgets(array(
					'id_documento'             => new sfWidgetFormInputHidden(),
					'titulo'                   => new sfWidgetFormInputText(),
					'descripcion'              => new sfWidgetFormInputText(),
					'publico'                  => new sfWidgetFormChoice(array('choices' => array('no','si'), 'expanded' => false, 'multiple' => false)),
					'id_usuario'               => new sfWidgetFormInputHidden(),
					'id_tipo'                  => new sfWidgetFormPropelChoice(array('model' => 'Tipos', 'add_empty' => false)),
					'documento_categoria_list' => new sfWidgetFormPropelChoice(array('multiple' => true, 'model' => 'Categorias')),
					'fichero'                  => new sfWidgetFormInputFile()
				));
	
			$this->setValidators(array(
					'id_documento'             => new sfValidatorPropelChoice(array('model' => 'Documentos', 'column' => 'id_documento', 'required' => false)),
					'titulo'                   => new sfValidatorString(array('max_length' => 255)),
					'descripcion'              => new sfValidatorString(array('max_length' => 255, 'required' => false)),
					'publico'                  => new sfValidatorInteger(array('min' => 0, 'max' => 1)),
					'id_usuario'               => new sfValidatorPropelChoice(array('model' => 'Usuarios', 'column' => 'id_usuario')),
					'id_tipo'                  => new sfValidatorPropelChoice(array('model' => 'Tipos', 'column' => 'id_tipo')),
					'documento_categoria_list' => new sfValidatorPropelChoice(array('multiple' => true, 'model' => 'Categorias', 'required' => false)),
					'fichero'                  => new sfValidatorFile(array('required' => false))
				));
	
			$this->widgetSchema->setNameFormat('documentos[%s]');
	
		}
	}

Para los campos título y descripción hemos utilizado cajas de texto sencillas
(*sfWidgetFormInputText*). Para los campo *id_documento* y *id_usuario* hemos 
optado por campos ocultos (*sfWidgetFormInputHidden*), ya que sus valores no los
decide el autor cuando crea el documento. En efecto, el *id_documento* es la 
clave primaria del documento que se está creando y será el sistema gestor de base
de datos quien lo decida cuando sea almacenado en la tabla correspondiente. Por 
otro lado, el *id_usuario* debe coincidir con el del autor que está creando el
nuevo documento. Recuerda que este último dato (*id_usuario*) lo tenemos 
disponible en la sesión. 

El *id_tipo* debe corresponderse con alguno de los registros de la tabla *tipos*. 
Por ello utilizamos un *widget* muy útil con el que se pueden crear controles de
selección con valores que provienen de una base de datos: el 
*sfWidgetFormPropelChoice*. Vamos a describir el funcionamiento de este *widget*:
En la declaración de sus opciones se especifica el parámetro *model*, que hace 
alusión a la clase del modelo de donde se pretende seleccionar los datos, en este
caso de la clase *Tipos*. Además especificamos mediante el parámetro *add_empty*
que deseamos incorporar un elemento vacío al menú de selección. Otro parámetro 
muy útil, aunque aquí no lo utilizamos, es *criteria*, mediante el cual podemos
asociar un criterio de selección para elegir un subconjunto de todos los 
registros de la tabla en cuestión. Con estos datos, el *sfWidgetFormPropelChoice*
construirá un menú de selección en el que los elementos *HTML option* tendrán 
asociado como valores (atributo *value*) los *id's* correspondientes a la clave 
principal de la tabla y mostrará al usuario el valor devuelto por el método 
*__toString()* de cada uno de los objetos que han poblado el control. Por ello, 
para utilizar este *widget* con un objeto de *Propel* es indispensable definir
el método *__toString()* de dicho objeto. En nuestro caso debemos definir el
método *__toString()* del objeto Tipos:

*Contenido del archivo: lib/model/Tipos.php*

.. code-block:: php

	<?php
	
	class Tipos extends BaseTipos
	{
		public function __toString()
		{
			return self::getNombre();
		}
	}
 

Para la selección de las categorías a las que pertenece un documento hacemos lo
mismo que con los tipos, usamos el *sfWidgetFormPropelChoice* aplicado a la clase
*Categorias*. Además, para que podamos seleccionar varias categorías especificamos
en sus opciones el parámetro *multiple* como *true*. Por supuesto debemos definir
el método *__toString()* del objeto *Categorias*:

*Contenido del archivo: lib/model/Categorias.php*

.. code-block:: php

	<?php
	
	class Categorias extends BaseCategorias
	{
		 public function __toString()
		 {
			 return self::getNombre();
		 }
	}

Para que el usuario decida si el documento es público o no utilizamos el *widget*
de selección *sfWidgetFormChoice* que es similar al anterior. La única diferencia
es que los elementos de selección no se obtienen de la base de datos, sino 
directamente de una *array* que se pasa como opción del *widget* que, en nuestro
caso, contiene los valores no y si con índices 0 y 1 respectivamente.

Por último, para que el usuario envíe, si lo desea, un archivo que corresponda 
a la primera versión del documento, hemos utilizado el *widget
sfWidgetFormInputFile*, que se presentará en el navegador un control *HTML
input* de tipo *file* para la selección y subida de ficheros locales.

Segunda parte: los validadores. Recuerda lo que dijimos sobre la desconfianza 
obligatoria que toda aplicación *web* debe mostrar ante los datos que les llegan
en las peticiones. Como puedes observar en la definición del formulario, hemos
asociado validadores a todos los campos. 

Los campos que han sido poblados con valores de la base de datos son validados 
con el objeto *sfValidatorPropelChoice*, que se configura pasándole como opción
el nombre del objeto de *Propel* que servirá para obtener los registros válidos 
y el nombre de la columna que se utilizará como valor de validación. Esto ocurre
con los campos *id_documento, id_usuario id_tipo* y *documento_categoria_list*.
Observa que ni el campo *id_documento* ni el campo *documento_categoria_list* 
son requeridos.

Los campos *titulo* y *descripcion* se validan como cadenas de no más de 255 
caracteres, siendo el primero obligatorio y el segundo opcional. Hacemos esto
mediante un *sfValidatorString*.

El campo *publico* debe ser uno o cero. Eso es lo que exigimos con el validador 
*sfValidatorInteger*.

Por último el fichero subido al servidor se valida con el objeto *sfValidatorFile*.
Como único parámetro especificamos que no es requerido. Sin embargo podemos
configurarlo de manera que establezcamos el tamaño máximo y otros aspectos
relacionados con el fichero a validar. La mejor forma de estudiar todas las
posibilidades de validación tanto de este validador como del resto, así como de
los *widgets* es consultando directamente la *API* de *symfony*.

Es importante saber que una vez validados los datos en el servidor mediante el
método *isValid()* del formulario, se puede acceder a los valores limpios usando 
el método *getValue()* del formulario pasándole como argumento el campo cuyo 
valor limpio deseamos obtener:

.. code-block:: bash

	...
	// más atrás el formulario ha sido validado
	$valor_limpio = $formulario → getValue('nombre_campo');
	...

En el desarrollo de las funcionalidades que aún quedan por implementar haremos
un uso extensivo del método *getValue()* del formulario, así que conviene 
comprenderlo bien. 

El valor que devuelve el método anterior cuando se aplica a un campo del tipo 
*sfWidgetFormInputFile*, es un objeto denominado *sfValidatedFile*, el cual 
representa al fichero subido y facilita una serie de métodos muy útiles para la 
gestión del mismo. La tabla siguiente muestra los métodos del objeto
*sfValidatedFile* que utilizaremos más adelante. Para una referencia completa
remitimos de nuevo a la *API* de *symfony*.

=================================== ============================================

Métodos del objeto sfValidatedFile  Descripción

=================================== ============================================

*generateFileName()*                Genera un nombre aleatorio para el fichero 
                                    actual

*save($ruta)*                       Guarda el fichero en la ruta especificada

*isSaved()*                         Devuelve *true* si el fichero ya ha sido 
                                    guardado y *false* en caso contrario


También dispone de otros métodos para conocer otros aspectos del fichero como 
el tamaño, la extensión, el nombre original, y otros más. Ya sabes, consulta la
*API* para conocerlos y utiliza el que resuelva tu problema concreto.

Por último observa que hemos utilizado como formato de nombres para los campos
del formulario el patrón *documentos[%s]*.


Implementación de la creación de nuevos documentos
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ya disponemos del formulario para crear nuevos documentos. Ahora hay que 
construir la lógica para presentarlo al usuario a través de su navegador y para
procesar los datos que este devuelva. No es más que volver a repetir el flujo de 
operaciones del ejemplo desarrollado en el apartado 2.3 adaptándolo a las
necesidades marcadas por los requisitos de nuestra aplicación. La acción que se
encargará de controlar dicho flujo la denominaremos *executeNuevo()*, y el código
correspondiente es el que sigue:

*Trozo de código del archivo: 
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	 public function executeNuevo($request)
	 {
		 $this -> formulario = new FormularioDocumentos();
		 $this -> formulario -> setDefault('id_usuario', $this -> getUser() -> getAttribute('id_usuario'));
	
		 if($request -> isMethod('post'))
		 {
			 $this -> formulario -> bind($request -> getParameter('documentos'), $request -> getFiles('documentos'));
			 if($this -> formulario -> isValid())
			 {
	
				 $this -> procesaNuevoDocumento($this -> formulario);
				 $this -> getUser() -> setFlash('mensaje', 'el documento ha sido creado');
				 $this -> redirect('gesdoc/index');
			 }
		 }
	 }


Y este es el código de la plantilla correspondiente *nuevoSuccess.php*:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/nuevoDocumentoSuccess.php*

.. code-block:: html+jinja
	
	<div id="sf_admin_container">
		<h1>Formulario</h1>
	
		<div id="sf_admin_header">
	
		</div>
	
		<div id="sf_admin_content">
			<ul class="sf_admin_actions">
				<li class="sf_admin_action_list"><a href="<?php echo url_for('gesdoc/index') ?>">volver al listado</a> </li>
			</ul>
			<div class="sf_admin_form">
				<form name="form" action="<?php echo url_for('gesdoc/nuevo') ?>" method="post" enctype="multipart/form-data">
					<?php echo $formulario -> renderHiddenFields() ?>
					<?php echo $formulario -> renderGlobalErrors() ?>
					<fieldset id="fieldset_1">
						<h2>Nuevo Documento</h2>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['titulo'] -> renderError() ?>
							<div>
								<?php echo $formulario['titulo'] -> renderLabel() ?>
								<?php echo $formulario['titulo']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['descripcion'] -> renderError() ?>
							<div>
								<?php echo $formulario['descripcion'] -> renderLabel() ?>
								<?php echo $formulario['descripcion']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['publico'] -> renderError() ?>
							<div>
								<?php echo $formulario['publico'] -> renderLabel() ?>
								<?php echo $formulario['publico']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['id_tipo'] -> renderError() ?>
							<div>
								<?php echo $formulario['id_tipo'] -> renderLabel() ?>
								<?php echo $formulario['id_tipo']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['documento_categoria_list'] -> renderError() ?>
							<div>
								<?php echo $formulario['documento_categoria_list'] -> renderLabel() ?>
								<?php echo $formulario['documento_categoria_list']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['fichero'] -> renderError() ?>
							<div>
								<?php echo $formulario['fichero'] -> renderLabel('fichero (versión 1)') ?>
								<?php echo $formulario['fichero']->render() ?>
							</div>
						</div>
					</fieldset>
					<input type="submit" />
				</form>
			</div>
		</div>
	
		<div id="sf_admin_footer">
		</div>
	</div>


Como puedes comprobar el código es esencialmente el mismo que el del ejemplo del
apartado 2.3, solo que hemos utilizado el formulario recién definido para la 
creación y edición de documentos. Vamos a explicar los elementos novedosos. En
primer lugar utilizamos el método *setDefault()* del formulario para definir el
valor del campo *id_usuario*, ya que este debe coincidir con el *id* del usuario 
que está utilizando la aplicación y que tenemos disponible en la sesión de 
usuario. Este método acepta como primer argumento el nombre del campo que se 
desea definir y como segundo el valor por defecto que se le asignará. Recuerda 
que este campo se ha definido como oculto y, aunque estará disponible en el
formulario *HTML*, no será visible al usuario.

Por otro lado, en la plantilla, hemos declarado el elemento *HTML form* como
*multipart/form-data*, pues además de datos queremos enviar un fichero. Los 
ficheros que se envían en la petición *HTTP*, se asocian al formulario mediante 
el segundo argumento del método *bind()*: 

.. code-block:: bash

	$this -> formulario -> bind($request -> getParameter('documentos'), $request -> getFiles('documentos'));


Es decir, el primer argumento del método *bind()* representa los datos que vienen
en la petición, y el segundo los archivos que llegan vía dicha petición. La razón
de esto radica en el funcionamiento de la operación *POST* del protocolo *HTTP*.
Esta operación *HTTP* permite enviar simultáneamente al servidor ficheros y datos
pero, por decirlo de alguna forma no muy rigurosa, cada uno “van por su parte”, y
en la composición de la petición *POST* hay que indicarlo explícitamente mediante
el parámetro *enctype=”multipart/form-data”*. 

A continuación, si los datos que hemos recibido son válidos (el método *isValid()*
del formulario es quien lo decide utilizando los validadores que hemos declarado 
en la definición del formulario), se lleva a cabo el procesamiento de los datos.
Con el fin de construir un código más limpio y legible, hemos llevado dicho 
procesamiento a un método protegido (*protected*) de la misma clase *gesdocActions*
denomindado *procesaNuevoDocumento()*. Se ha declarado protegido ya que sólo se 
accederá a él desde dentro de la propia clase *gesdocActions*. El código de dicho
método el el siguiente:

*Trozo de código del archivo:
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: php

	protected function procesaNuevoDocumento($formulario)
	{
		// Antes de nada colocamos en su sitio el fichero (si se ha subido alguno)
		if($formulario -> getValue('fichero')  instanceof sfValidatedFile)
		{
			$fichero = $formulario -> getValue('fichero');
	
			$tipo = TiposPeer::retrieveByPK($formulario -> getValue('id_tipo'));
				
			if(!in_array($fichero -> getType(), $tipo -> dameTiposMimePermitidos()))
			{
				$this -> getUser() -> setFlash('error', 'el fichero de tipo '.$fichero -> getType(). ' no se corresponde con el tipo de fichero seleccionado '.$tipo -> getTipoMime());
				$this -> redirect('gesdoc/index');
			}
				
			$nombreFichero = time() . $fichero -> generateFileName();
			$ruta = sfConfig::get('sf_upload_dir').'/usuario_'.$this -> getUser() -> getAttribute('id_usuario').'/'.$nombreFichero;
			$fichero -> save($ruta);
	 
			if(!$fichero -> isSaved())
			{
				$this -> getUser() -> setFlash('mensaje', 'el fichero no ha podido guardarse');
				$this -> redirect('gesdoc/index');
			}
	
		}
	
			// Realizamos una transacción para evitar que se guarden los datos si el
			// proceso, por algún motivo, se rompe
		$con = Propel::getConnection();
	
		$con -> beginTransaction();
	
		try
		{
			// Creamos el documento
			$documento = new Documentos();
			$documento -> setTitulo($formulario -> getValue('titulo'));
			$documento -> setDescripcion($formulario -> getValue('descripcion'));
			$documento -> setPublico($formulario -> getValue('publico'));
			$documento -> setIdTipo($formulario -> getValue('id_tipo'));
			$documento -> setIdUsuario($formulario -> getValue('id_usuario'));
			$documento -> save();
	
				//Asociamos las categorias
			foreach ($formulario -> getValue('documento_categoria_list') as $categoria)
			{
				$documento_categoria = new DocumentoCategoria();
				$documento_categoria -> setIdDocumento($documento -> getIdDocumento());
				$documento_categoria -> setIdCategoria($categoria);
				$documento_categoria -> save();
			}
	
			// Creamos la version 1 (si se ha subido un fichero)
			if($formulario -> getValue('fichero') instanceof sfValidatedFile)
			{
				$version = new Versiones();
			 $version -> setNumero(1);
				$version -> setNombreFichero($nombreFichero);
				$version -> setDescripcion('Versión 1 del documento');
				$version -> setFechaSubida(date('Y-m-d H:i:s'));
				$version -> setIdDocumento($documento -> getIdDocumento());
				$version -> save();
			}
	
			$con ->commit();
		}
		catch (Exception $e)
		{
			$con -> rollBack();
			throw $e;
		}
	
	}


El flujo troncal de este procedimiento es el siguiente:

1. Si se ha subido un fichero se comprueba que el tipo *mime* del mismo coincida
   con uno de los tipos *mimes* que se han asociado al tipo de fichero en la 
   tabla *tipos*. En caso afirmativo se asigna un nombre **único** al fichero que 
   consiste en una concatenación del tiempo actual medido en segundos desde el 1 
   de junio de 1970 a las 00:00:00 (lo que se llama época Unix) con un nombre 
   generado aleatoriamente con el método *generateFileName()* del objeto 
   *sfValidatedFile()*. Entonces se guarda el archivo con ese nombre en la
   carpeta del usuario que lo ha enviado a través del formulario. Si alguno de
   estos subprocesos no ha ido bien, se aborta el procedimiento y se realiza una
   redirección a la acción *gesdoc/index*, enviando un mensaje de error a través 
   de la sesión de usuario.

2. Utilizando un objeto de *Propel* del tipo *Documentos*, se crea un nuevo
   registro en la tabla *documentos* con los datos enviados.  

3. Se crean tantos registros como categorías se hayan seleccionado en la tabla 
   *categorias* asociados al documento recién creado. Para ello se utilizan 
   objetos *DocumentoCategoria* de *Propel*.

4. Si se subió un fichero, que corresponde a la primera versión del documento, se
   crea un nuevo registro en la tabla versiones asociado al documento recién 
   creado. El nombre del campo *nombre_fichero* es el nombre calculado en el punto
   uno. De nuevo utilizamos un objeto de *Propel*, en este caso *Versiones*, para
   realizar la creación del registro.

Las operaciones sobre la base de datos (pasos del 2 al 4) se realizan dentro de 
una transacción, para garantizar que si algo va mal no se realice ninguna 
inserción de datos que crearían datos huérfanos no deseados en la base de datos.
Dicha transacción se ha encajado dentro de una estructura *try-catch*. 

Para la comprobación de los tipos *mime* permitidos hemos ampliado la clase de
*Propel Tipos* con un método denominado *dameTiposMimePermitidos()* que devuelve
un *array* con los valores separados por coma del campo *tipo_mime* de la tabla 
tipos. Este es el código:

*Trozo del archivo: lib/model/Tipos.php*

.. code-block:: bash

	 public function dameTiposMimePermitidos()
	 {
	   $tipos_mime = explode(',', $this -> getTipoMime());
	   return $tipos_mime;
	}


Sería mucho más elegante construir un procedimiento que asociase a través del
tipo *mime* del archivo que se ha subido al servidor su tipo de archivo 
(*openoffice, pdf*, etc), sin necesidad de que el usuario indicase lo indicase. 
Sin embargo, hemos optado por este procedimiento “manual” con el fin de no 
oscurecer los conceptos básicos que queremos mostrar enredándonos en la 
implementación de farragosos procedimientos.

Por último hemos hecho uso del operador *instanceof* para comprobar si el 
formulario validado contenía un archivo válido (*sfValidatedFile*). Este operador 
es muy útil para conocer el tipo (la clase) de los objetos que manipulamos en
nuestros programas *PHP*.

Si todo ha salido bien habremos creado un nuevo documento con un número
determinado de categorías asociadas, la primera versión del documento y un
archivo físico correspondiente a dicha versión almacenado en la carpeta del
usuario que utiliza la aplicación. O dicho en otros términos más concretos: 
Un nuevo registro en la tabla *documentos*, varios registros en la tabla 
*documento_categoria* asociados al anterior registro, un nuevo registro en la 
tabla *versiones* asociado a ese mismo registro, y un archivo físico en la 
carpeta del usuario que está manejando la aplicación.


Implementación de la modificación de documentos existentes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

La modificación de los metadatos de cada documento se realiza a través del *link
modificar* que aparece en la columna de acciones del listado de documentos. Dicho
*link* aparece únicamente en los documentos que pertenecen al autor que está
utilizando la aplicación. Ahora falta implementar la acción que lleva a cabo la
operación de modificación. Y esto es lo que haremos en este apartado.

Comenzaremos por definir el formulario que utilizaremos para la modificación de 
los metadatos de la aplicación. Para ello, en primer lugar, debemos analizar en 
qué consiste la operación de modificación. Nos remitimos para ello a las 
especificaciones de la aplicación. Allí se especifica que los ficheros de las 
versiones subidas al repositorio deben ser del tipo definido en el documento al
que pertenecen. Además, una vez subidas al repositorio no se podrán eliminar. De
estos dos requisitos podemos inferir como solución más sencilla, que en la edición
de los metadatos de un documento no podemos cambiar el campo *id_tipo*. Además 
la modificación de los metadatos no incluye la posibilidad de subir un fichero 
como en el caso de la creación de un documento. Por estas razones no podemos 
utilizar directamente el formulario *FormularioDocumento* para implementar la 
modificación, ya que nos sobra el campo *fichero* y no debemos mostrar el campo 
*id_tipo*. Parece que la solución pasa por definir otro formulario distinto para
la modificación. Y aunque es así, debido a que este nuevo formulario es 
prácticamente el mismo que el que se utilizó para la creación de documentos, la
solución más elegante y aconsejada es utilizar el concepto de herencia tan útil
en la *POO*, y crear el nuevo formulario, al que llamaremos
*FormularioModificacionDocumentos*, como una clase hija del formulario 
*FormularioDocumentos*, y retocar esta nueva clase modificando los elementos
diferentes, es decir, redefiniendo el campo *id_tipo*, que ahora deseamos que 
sea un campo oculto, y eliminando el campo *fichero* ya que no lo necesitamos 
para la operación de modificación. El siguiente código implementa el nuevo 
formulario para la modificación de documentos basado en el que ya existe para 
la creación de los mismos:

*Contenido del archivo: 
apps/frontend/lib/FormularioModificacionDocumentos.class.php*

.. code-block:: php

	<?php
	
	class FormularioModificacionDocumentos extends FormularioDocumentos
	{
		public function configure()
		{
			parent::configure();
	
			$this -> widgetSchema['id_tipo'] = new sfWidgetFormInputHidden();
	
			unset($this -> widgetSchema['fichero']);
			unset($this -> validatorSchema['fichero']);
		}
	}


El resto de los *widgets* los podemos reutilizar, así como los validadores.

Una vez definido el formulario vamos a construir la acción *executeModificar()*,
la cual se encargará de enviar el formulario al cliente para que este lo rellene.

*Trozo del archivo: apps/frontend/modules/gesdoc/action/action.class.php*

.. code-block:: bash

	public function executeModificar(sfWebrequest $request)
	 {
		$this -> forward404Unless($request -> hasParameter('id_documento'));
	
		$documento = DocumentosPeer::retrieveByPK($request -> getParameter('id_documento'));
	
		$this -> forward404Unless($documento instanceof Documentos);
	
		$categorias = array();
		foreach ($documento -> getDocumentoCategorias() as $categoria)
		{
			$categorias[] = $categoria -> getIdCategoria();
		}
	
		$this -> formulario = new FormularioModificacionDocumentos();
	
		$datos = array(
			'id_documento' => $documento -> getIdDocumento(),
			'titulo'       => $documento -> getTitulo(),
			'descripcion'  => $documento -> getDescripcion(),
			'documento_categoria_list' => $categorias,
			'publico'      => $documento -> getPublico(),
			'id_usuario'   => $documento -> getIdUsuario(),
			'id_tipo'      => $documento -> getIdTipo(),
			'_csrf_token'  => $this -> formulario ->       getCSRFToken(sfConfig::get('sf_csrf_secret'))
			);
	
		$this -> formulario -> bind($datos, array());
	  }

La acción que acabamos de mostrar declara un objeto formulario del tipo 
*FormularioModificacionDocumentos* y le asocia (método *bind()*) los datos del 
documento que se desea modificar. Por ello hemos recuperado previamente el
documento cuyo id nos viene en la petición (*request*). Ahora construimos la 
plantilla asociada *modificarSuccess.php*:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/modificarSuccess.php*

.. code-block:: bash

	<div id="sf_admin_container">
		<h1>Modificación del Documento</h1>
	
		<div id="sf_admin_header">
	
		</div>
	
		<div id="sf_admin_content">
			<ul class="sf_admin_actions">
				<li class="sf_admin_action_list"><a href="<?php echo url_for('gesdoc/index') ?>">volver al listado</a> </li>
			</ul>
			<div class="sf_admin_form">
				<form name="form" action="<?php echo url_for('gesdoc/guardarModificacion?id_documento='.$id_documento) ?>" method="post">
					<?php echo $formulario -> renderHiddenFields() ?>
					<?php echo $formulario -> renderGlobalErrors() ?>
					<fieldset id="fieldset_1">
						<h2>Modificar Metadatos Documento</h2>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['titulo'] -> renderError() ?>
							<div>
								<?php echo $formulario['titulo'] -> renderLabel() ?>
								<?php echo $formulario['titulo']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['descripcion'] -> renderError() ?>
							<div>
								<?php echo $formulario['descripcion'] -> renderLabel() ?>
								<?php echo $formulario['descripcion']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['publico'] -> renderError() ?>
							<div>
								<?php echo $formulario['publico'] -> renderLabel() ?>
								<?php echo $formulario['publico']->render() ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['documento_categoria_list'] -> renderError() ?>
							<div>
								<?php echo $formulario['documento_categoria_list'] -> renderLabel() ?>
								<?php echo $formulario['documento_categoria_list']->render() ?>
							</div>
						</div>
	
					</fieldset>
					<input type="submit" />
				</form>
			</div>
		</div>
	
		<div id="sf_admin_footer">
		</div>
	</div>

La cual es muy parecida a la plantilla correspondiente a la creación de un 
documento. Ahora ya puedes probar el funcionamiento de la acción en tu navegador
picando en el *link modificar* de alguno de los documentos. Observa que la acción
que dispara el formulario es *gesdoc/guardarModificacion*, la cual debemos 
implementar aún. Pero antes de hacer esto vamos rehacer la acción que acabamos 
de implementar sustituyendo el formulario *FormularioModificacionDocumentos* por
otro más adecuado que, además, fue creado automáticamente por *symfony* cuando 
al principio del proyecto invocamos la tarea:

.. code-block:: bash

	# symfony propel:build-forms

Esta tarea construye en el directorio *lib/forms* un formulario por cada objeto
de nuestro modelo de *Propel*, es decir, por cada tabla de nuestra base de datos. 
Si echas un vistazo a este directorio podrás ver un conjunto de clases cuyo 
nombre sigue el patrón *{ObjetoPropel}Form.php*, entre las que se encuentra 
*DocumentosForm.php*, asociada al objeto *Documentos*. Al igual que ocurría con 
los objetos de *Propel*, las clases formularios, en un principio, no contienen 
ninguna acción, simplemente derivan de las clases bases *Base{ObjetoPropel}Form.php*
ubicadas en el directorio *lib/form/base*, que son las que contienen el código
construido automáticamente a partir de la definición de los objetos del modelo 
de *Propel*. La idea que subyace bajo esta forma de organización del código es
la misma que ya explicamos en la unidad 5 sobre los objetos de *Propel* y que
resumimos a continuación: cada vez que modificamos el modelo de datos por
cualquier razón (se añade, modifica o elimina algún campo a alguna tabla, se
crean nuevas tablas, etcétera) debemos regenerar los formularios con la tarea 
*propel:build-form*, para que dichos formularios incorporen los cambios 
realizados. Pues bien esta regeneración únicamente tiene lugar en las clases
bases del directorio *lib/form/base* de manera que no perdamos los cambios o 
adiciones que hayamos realizado en las clases hijas correspondientes. 

Si miras el código de cualquier formulario base podrás comprobar que son clases
que no derivan de *sfForm*, como los formulario que hasta el momento hemos 
estudiado en esta unidad, sino que derivan de *BaseFormPropel* que es una clase
derivada de *sfForm* pero que incluye ciertas funcionalidades asociadas a los 
objetos de *Propel*. De hecho cada formulario está asociado a una tabla de la
base de datos y sus *widgets* y validadores hacen referencia a los campos de 
cada una de las tablas. Gracias a esto, los formularios de *Propel* implementan 
una nueva operación, denominada *save()*, gracias a la cual el propio formulario
se encarga de grabar los campos del mismo en la tabla que le corresponde, 
aliviando al programador de realizar dicha tarea y arrojando un código más
sencillo y legible.

Vamos a ver como podemos utilizar dichos formularios para llevar a cabo la
operación de modificación de los documentos. En primer lugar miramos el código 
del objeto *BaseDocumentosForm* y comprobamos que casi coincide con nuestros
requisitos. Los cambios que debemos hacer para nuestros propósitos son: 

* Los campos *id_usuario* y *id_tipo* deben ser ocultos,
* el campo descripción encajaría mejor si fuese un *TextArea*,
* el campo *publico* debe ser un *widget* de selección con dos opciones: 'no' y 'si',
* y el validador del campo *publico* debe garantizar que únicamente se acepten
  como valores 0 (correspondiente a 'no') y 1 (correspondiente a 'si')

Todo lo cual podemos realizar modificando adecuadamente la clase hija 
*DocumentosForm* de la siguiente manera:

*Contenido del archivo: lib/form/DocumentosForm.php*

.. code-block:: php

	<?php
	
	class DocumentosForm extends BaseDocumentosForm
	{
	  public function configure()
	  {
		  $this -> widgetSchema['publico']     = new sfWidgetFormChoice(array('choices' => array('no','si'), 'expanded' => false, 'multiple' => false));
		  $this -> widgetSchema['id_usuario']  = new sfWidgetFormInputHidden();
		  $this -> widgetSchema['id_tipo']     = new sfWidgetFormInputHidden();
		  $this -> widgetSchema['descripcion'] = new sfWidgetFormTextarea();
	
		  $this -> validatorSchema['publico'] = new sfValidatorInteger(array('min' => 0, 'max' => 1));
	  }
	}


Ahora rehacemos la acción *executeModificar()* para que utilice este formulario:

*Trozo del archivo: apps/frontend/modules/gesdoc/action/action.class.php*

.. code-block:: bash

	public function executeModificar(sfWebRequest $request)
	{
		$this -> forward404Unless($request -> hasParameter('id_documento'));
	
		$documento = DocumentosPeer::retrieveByPK($request -> getParameter('id_documento'));
	
		$this -> forward404Unless($documento instanceof Documentos);
	
		$this -> formulario = new DocumentosForm($documento);
		$this -> id_documento = $request -> getParameter('id_documento');
	}

Como puedes comprobar hemos conseguido un código bastante más escueto y sencillo. 
Igual que antes, lo primero que hacemos es recuperar el objeto que deseamos 
modificar a través del *id* que viene en la petición. Pero una vez que lo tenemos
no tenemos que preocuparnos de extraer sus campos y asociarlos al formulario, 
directamente pasamos como argumento en la creación del nuevo formulario de *Propel*
el objeto documento que deseamos modificar y él ya se encarga de realizar la
asociación. Prueba de nuevo la acción *gesdoc/modificar* y comprueba que funciona
exactamente igual que antes. Observa, además, que no hemos tenido que hacer
ningún cambio en la plantilla *modificarSuccess.php* ya que está “preparada” para
manipular un objeto formulario y la interfaz del nuevo formulario coincide con 
la del antiguo, es decir, tiene los mismos campos y ofrece los mismos métodos que
requiere la plantilla.

Ahora vamos a implementar la acción que se ocupará de grabar los datos devueltos
al servidor a través del formulario *HTML*. Hemos llamado a esta acción 
*gesdoc/guardarModificacion*, así pues la función asociada se denominará 
*executeGuardarModificacion()*:

*Trozo de código del archivo: 
apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: bash

	public function executeGuardarModificacion(sfWebRequest $request)
	{
		$this -> forward404Unless($request -> hasParameter('documentos'));
	
		$documento = DocumentosPeer::retrieveByPK($request -> getParameter('id_documento'));
	
		$this -> formulario = new DocumentosForm($documento);
		$this -> formulario -> bind($request -> getParameter('documentos'));
	
		if($this -> formulario -> isValid())
		{
			$this -> formulario -> save();
	
			$this -> getUser() -> setFlash('mensaje', 'Los metadatos del documento ha sido modificado');
	
			$this -> redirect('gesdoc/index');
			}
	
		$this -> setTemplate('modificar');
	}

En esta acción recuperamos el documento correspondiente al *id* que viene por 
la petición *HTTP* y creamos un formulario *DocumentosForm* asociado a este
documento. Por lo pronto tenemos lo mismo que en la acción anterior. A
continuación asociamos el formulario recién creado a los datos que vienen por 
la petición *HTTP* y si pasa el test de validez procedemos a guardalo en la base 
de datos utilizando el método *save()* del formulario. Fíjate; es el formulario
el que se encarga de realizar la operación sobre la base de datos, por que al ser
un formulario de *Propel* “sabe” como debe realizar dicha operación. Si 
hubiésemos utilizado el formulario *FormularioModificacionDocumentos*, tendríamos
que realizar dicha operación nosotros, es decir, tendríamos que modificar 
manualmente cada uno de los campos del objeto *Documentos* con los valores de 
los campos del formulario validado y salvar los cambios con el método *save()*
del objeto *Documentos*. Por último, como ya hemos visto en esta misma unidad, 
si todo va bien se genera una notificación para advertir el éxito de la operación
y se redirige la acción hacia el listado de documentos, y si la validación no es 
correcta se vuelve a enviar el  formulario (*setTemplate('modificar')*) con los
mensajes de error que han provocado el rechazo de la validación.


Subida de versiones
-------------------

Para finalizar la aplicación *frontend* de nuestro proyecto tan sólo nos queda
desarrollar la subida de nuevas versiones. Cuando un usuario desee subir al 
servidor una nueva versión de algún documento tendrá que facilitar a la
aplicación el fichero asociado a la versión, que debe ser del tipo especificado
en el documento correspondiente, y una descripción de la misma. El resto de los
campos de la tabla versiones: *nombre_fichero, numero, fecha_subida* y 
*id_documento*, deben ser calculados automáticamente por la aplicación.

Al igual que en el apartado anterior, comenzaremos por definir un formulario 
apropiado para llevar a cabo la operación de subida de ficheros para, 
posteriormente, implementar dicha operación.


El formulario VersionesForm
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Como acabamos de indicar, para subir nuevas versiones, el autor necesita 
introducir los siguientes datos:

* la descripción de la nueva versión y
* el fichero que corresponde a la nueva versión

El primero será modelado como un *sfWidgetFormInput* y el segundo como un 
*sfWidgetFormInputFile*.

El resto de los datos; el nombre del fichero, el número de versión, la fecha de
la subida y el *id* del documento asociado deben ser asignados automáticamente 
por la aplicación.

A continuación presentamos el código correspondiente al formulario para la subida
de versiones:

*Contenido del archivo: app/frontend/lib/FormularioVersiones.class.php*

.. code-block:: php

	<?php
	
	class FormularioVersiones extends sfForm
	{
		public function configure()
		{
			$this->setWidgets(array(
					'descripcion'    => new sfWidgetFormTextarea(),
					'fichero'        => new sfWidgetFormInputFile()
			));
		  
			$this->setValidators(array(
					'descripcion'    => new sfValidatorString(array('max_length' => 255, 'required' => true)),
					'fichero'        => new sfValidatorFile(array('required' => 'true'))
				));
	
			$this->widgetSchema->setNameFormat('versiones[%s]');
		}
	}


Implementación de la subida de nuevas versiones
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Construimos ahora la acción que enviará al cliente el formulario *HTML* para la 
subida de nuevas versiones. Denominaremos a tal acción *executeSubirVersion()*:

*Trozo del archivo: apps/frontend/modules/gesdoc/actions/actions.class.php*

.. code-block:: php

	public function executeSubirVersion(sfWebRequest $request)
	{
		$this -> forward404Unless($request -> hasParameter('id_documento'));
		$this -> formulario = new FormularioVersiones();
	
		$documento = DocumentosPeer::retrieveByPK($request -> getParameter('id_documento'));
		$this -> tipoDocumento = $documento -> getTipos() ->getNombre();
		$this -> id_documento = $request -> getParameter('id_documento');
			
		if($request -> isMethod('post'))
		{
		   $this -> formulario -> bind($request -> getParameter('versiones'), $request -> getFiles('versiones'));
	
		   if($this -> formulario -> isValid())
		   {
			   if($this -> formulario -> getValue('fichero')  instanceof sfValidatedFile)
			   {
				   $fichero = $this -> formulario -> getValue('fichero');
	
				   $nombreFichero = time() . $fichero -> generateFileName();
				   $ruta = sfConfig::get('sf_upload_dir').'/usuario_'.$this -> getUser() -> getAttribute('id_usuario').'/'.$nombreFichero;
				   $fichero -> save($ruta);
	
				   if(!$fichero -> isSaved())
				   {
						$this -> getUser() -> setFlash('mensaje', 'el fichero no ha podido guardarse');
						$this -> redirect('gesdoc/index');
				   }
			   }
	
			   $version = new Versiones();
			   $version -> setNombreFichero($nombreFichero);
			   $version -> setNumero(VersionesPeer::calculaVersion($this -> id_documento));
			   $version -> setFechaSubida(date('Y-m-d H:i:s'));
			   $version -> setIdDocumento($this -> id_documento);
			   $version -> setDescripcion($this -> formulario -> getValue('descripcion'));
			   $version -> save();
	
			   $this -> getUser() -> setFlash('mensaje', 'Nueva versión subida al repositorio');
			   $this -> redirect('gesdoc/index');
		   }
	   }
	}


Esta acción es parecida a la correspondiente a la creación de un nuevo documento.
Vamos a comentarla muy brevemente, dejando una interpretación en profundidad al
estudiante.

En primer lugar definimos un formulario del tipo *FormularioVersiones* para 
mostrarlo en la vista. Además obtenemos el *id* del documento pues nos hará falta
en la plantilla para construir el parámetro *action* del formulario. También 
recuperamos el tipo del documento para colocar un mensaje en el formulario que
indique al usuario que el archivo a subir debe ser de ese tipo. Si la petición 
no proviene del envío del formulario, es decir, si no es de tipo *POST*,
simplemente se envía un formulario vacío para ser rellenado por el usuario. El
código de la vista correspondiente es el que sigue:

*Contenido del archivo: 
apps/frontend/modules/gesdoc/templates/subirVersionSuccess.php*

.. code-block:: html+jinja

	<div id="sf_admin_container">
		<h1>Nueva Versión</h1>
	
		<div id="sf_admin_header">
	
		</div>
	
		<div id="sf_admin_content">
			<ul class="sf_admin_actions">
				<li class="sf_admin_action_list"><a href="<?php echo url_for('gesdoc/index') ?>">volver al listado</a> </li>
			</ul>
			<div class="sf_admin_form">
				<form name="form" action="<?php echo url_for('gesdoc/subirVersion?id_documento='.$id_documento) ?>" method="post" enctype="multipart/form-data">
					<?php echo $formulario -> renderHiddenFields() ?>
					<?php echo $formulario -> renderGlobalErrors() ?>
					<fieldset id="fieldset_1">
						<h2>Datos de la versión</h2>
						<div class="sf_admin_form_row">
							<div>
								<label>Tipo de documento</label>
								<?php echo $tipoDocumento ?>
							</div>
						</div>
	
						<div class="sf_admin_form_row">
							<?php echo $formulario['descripcion'] -> renderError() ?>
							<div>
								<?php echo $formulario['descripcion'] -> renderLabel() ?>
								<?php echo $formulario['descripcion']->render() ?>
							</div>
						</div>
	
						 <div class="sf_admin_form_row">
							<?php echo $formulario['fichero'] -> renderError() ?>
							<div>
								<?php echo $formulario['fichero'] -> renderLabel() ?>
								<?php echo $formulario['fichero']->render() ?>
								<br/>
								<help> El archivo debe ser un <?php echo $tipoDocumento ?></help>
							</div>
						</div>
					</fieldset>
					<input type="submit" />
				</form>
			</div>
		</div>
	
		<div id="sf_admin_footer">
		</div>
	</div>


Si por el contrario se llega a la acción a través del envío del formulario *HTML*,
entonces hay que proceder al procesamiento de sus datos. Para ello asociamos el 
formulario a los valores que llegan en la petición (datos y ficheros, fíjate que
el formulario es de tipo *form-data/multipart*) mediante el método *bind()* y 
procedemos a la validación. Si los datos enviados son válidos entonces comenzamos
por guardar el fichero en la carpeta correspondiente al usuario que lo ha subido
al servidor y, posteriormente, creamos un nuevo registro de la tabla versiones 
cuyo campo descripción es el que envió el usuario, siendo el resto calculados 
automáticamente por la aplicación. Observa que para definir el valor del campo
*numero* hemos ampliado la clase *VersionesPeer* para incorporar una función que 
calcule la última versión de un documento.

*Contenido del archivo: apps/lib/model/VersionesPeer.php*

.. code-block:: php

	<?php
	
	class VersionesPeer extends BaseVersionesPeer
	{
		static public function calculaVersion($id_documento)
		{
			echo $id_documento;
			$c = new Criteria();
			$c -> add(VersionesPeer::ID_DOCUMENTO, $id_documento);
			$c -> addDescendingOrderByColumn(VersionesPeer::NUMERO);
	
			$ultimaVersion = self::doSelectOne($c);
	
			if($ultimaVersion instanceof Versiones)
			{
				$numero = $ultimaVersion -> getNumero();
				$numero += 1;
			}
			else
			{
				$numero = 1;
			}
	
			return $numero;
		}
	}

Y ya puedes probar a subir nuevas versiones. Terminamos con esto la aplicación 
*frontend* del gestor documental.


Conclusión
----------

Hemos finalizado la primera de las aplicaciones del gestor documental, la que
hemos llamado *frontend* que constituye la parte esencial del gestor. La 
aplicación muestra los documentos disponibles, públicos y privados, en función
del perfil del usuario que la esté utilizando. Se pueden realizar búsquedas sobre
todos los metadatos de los documentos, se pueden crear nuevos documentos, 
modificar sus metadatos y subir al repositorio versiones de los documentos. 

Hemos estudiado el funcionamiento y la estructura de los formularios de *symfony*,
sus *widgets* y sus validadores y hemos mostrado como utilizarlos implementando
las funcionalidades de creación de nuevos documentos, la modificación de sus 
metadatos y la subida de nuevas versiones asociadas a ellos. Es importante 
indicar aquí, aunque el estudiante ya lo habrá percibido, que para utilizar con 
soltura los formularios de *symfony* es imprescindible practicar intensiva y 
extensivamente con ellos. Además, debido a la gran cantidad de tipos de *widgets*
y validadores resulta prácticamente imprescindible tener a mano la documentación 
de la *API* del *framework* de formularios para trabajar con ellos.

También hemos introducido otro tipo de formulario que está asociado con el modelo
de *Propel* y, por tanto, con las tablas de la base de datos. Dichos formularios 
facilitan el intercambio de datos entre los formularios y las tablas de la base
de datos y, como veremos en la unidad siguiente, son especialmente adecuados para 
el desarrollo de aplicaciones de administración de las bases de datos, aunque,
como hemos mostrado en esta unidad, también pueden sernos muy útiles en otras 
situaciones. Se trata siempre de estudiar qué objetos podemos reutilizar mediante
su extensión y adaptación a nuestras necesidades.










