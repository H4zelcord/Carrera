#2ºCarrera 
#SO 

---

**2.6.- Sistemas Unix Tradicionales.-**

- **Historia.-**
	 UNIX se desarrolló inicialmente en los laboratorios Bell, y se hizo operacional en un PDP-7 en 1970, parte de las personas de laboratorios Bell habían participado en el desarrollo de CTSS y Multics, siendo UNIX influenciada por las ideas de ambas.
	 El primer hito fue portar UNIX de PDP-7 a PDP-11, el siguiente fue la reescritura de UNIX, a lenguaje C, lo que fue una estrategia sin precedentes en ese tiempo, ya que se pensaba que un SO tenía que escribirse en ensamblador para tratar eventos críticos en el tiempo. La implementación en C demostró las ventajas de usar un lenguaje de alto nivel, siendo que hoy en día casi todas las implementaciones de UNIX están en C.
	
 
- **Descripción.-**
	El hardware es gestionado por el software del SO, denominado frecuentemente el núcleo del sistema, a esto se le denominará UNIX.
	Los programas de usuario pueden invocar los servicios del SO directamente o a través de programas de biblioteca. La interfaz de llamada a sistemas es la frontera con el usuario y permite que el software de alto nivel obtenga acceso a funciones específicas del núcleo. El sistema se encarga de 2 parte, una es el control de procesos, y la otra la gestión de ficheros y E/S; el subsistema de control de procesas se encarga de la gestión de memoria, la planificación y la ejecución de los procesos; el sistema de ficheros intercambia datos entre memoria y dispositivos externos, tanto flujo de caracteres como bloques. Para lograr esto se unan controladores. Para las transferencias orientadas a bloques, se usa una técnica de cache de discos, poniendo un buffer de sistema en memoria principal.

---

**2.7.- Sistemas UNIX Modernos.-**

Cuando Unix evolucionó, un gran número de implementaciones proliferó, cada una de las cuales proporcionaba algunas características útiles. En un núcleo UNIX moderno, existe un pequeño núcleo de utilidades, escritas de forma modular, que proporciona funciones y servicios necesarios para procesos del SO.
- Algunos ejemplos de sistemas UNIX modernos:
	- **System V r4:** Fue cas una reescritura completa del núcleo de System V y produjo una implementación bien organizada, aunque compleja; una de las nuevas características incluye soporte al procesamiento en tiempo real, clases de planificación de procesos, estructuras de datos asignadas dinámicamente, gestión de la memoria virtual, sistema de ficheros virtual y núcleo expulsivo.
	- **Solaris 9**: Es una versión UNIX de Sun basada en SVR4, proporciona todas sus características más un conjunto de características avanzadas, esta es la implementación UNIX más utilizada y más exitosa.
	- **4.4BSD**: Estas series han jugado un gran papel en el desarrollo de la teoría de diseño de los SO, se ha usado ampliamente en instalaciones académicas y ha servido como base de algunos productos comerciales UNIX, siendo el responsable de la popularidad de UNIX. La última versión Mac OS X se basa en 4.4BSD.

---

**2.8.- Linux.-**

- **Historia.-**
	El comienzo de Linux fue como una variante UNIX para PC IBM, la primera versión se distribuyó en 1991. Como Linux es de código libre, se convirtió en una alternativa para estaciones de trabajo UNIX, como Sun e IBM, hoy en día Linux es un sistema UNIX completo que se ejecuta en casi todas las plataformas.
	La clave del éxito de Linux ha sido la disponibilidad de los paquetes de software libre bajo los auspicios de FSF, el proyecto GNU... Hoy en día Linux es un producto del proyecto GNU de FSF y los esfuerzos de Linus Torvalds y muchos colaboradores del mundo.

- **Estructura Modular.-**
	La mayoría de los núcleos Linux son monolíticos. Todos los componentes funcionales del núcleo tienen acceso a todas las estructuras internas de datos y rutinas. Linux está estructurado como una colección de módulos, algunos cargables, que presentan 2 características importantes:
	-Enlace Dinámico: Puede cargarse y enlazarse al núcleo mientras este está en memoria y ejecutándose; se puede desenlazar de la memoria en cualquier momento.
	-Módulos Apilables: Se gestionan como una jerarquía. Los módulos individuales actúan como bibliotecas cuando los módulos cliente los referencian, y actúan como clientes cuando referencian a un módulo de la parte inferior. Tienen 2 ventajas significativas:
		-El código común para un conjunto de módulos similares se puede mover a un único módulo, reduciendo la replicación.
		-El núcleo puede asegurar que los módulos necesarios están presentes, impidiendo descargar un módulo del cual otros módulos que ejecutan dependen y cargando algunos módulos adicionalmente requeridos cuando se carga un nuevo módulo.
		 
- **Componentes del núcleo.-**
	Los principales componentes del núcleo son:
		-Señales: El núcleo usa señales para llamar a un proceso.
		-Llamadas al Sistema: Es la forma en la cual in proceso requiere un servicio de núcleo específico. Hay varios cientos de llamadas al sistema que pueden agruparse en 6 categorías ->Sistema de ficheros, proceso, planificación, comunicación entre procesos, socket, y misceláneos.


