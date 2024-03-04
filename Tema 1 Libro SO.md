#2ºCarrera 
#SO

---

**1.1.- Elementos Básicos.-**
Los componentes de un computador, en el nivel alto, son:

- **Procesador.-**
Se encarga de controlar el funcionamiento del computador y realiza las funciones de procesamiento de datos; llamado usualmente ``CPU`` cuando solo hay 1 procesador.
Registros:
-PC: Contador de Programa
-IR: Registro de Instrucción
-RDIM: Registro de Dirección de memoria
-RDAM: Registro de Datos de memoria
-RDI E/S: Registro de Dirección de  E/S
-RDA E/S Registro de Datos de E/S

- **Memoria Principal.-**
Almacena datos y programas, suele ser volátil, perdiendo su contenido tras apagar el computador, en contraste, el contenido de la memoria del disco se mantiene.

- **Módulos E/S.-**
Se encargan de la transferencia de datos entre el computador y su entorno externo, formado por diversos dispositivos, entre memoria secundaria, equipos de comunicación y terminales.

- **Bus del Sistema.-**
Proporciona comunicación entre los procesadores, la memoria principal y los módulos de E/S.

Un módulo de memoria, que consta de conjuntos de posiciones definidas por direcciones numeradas secuencialmente, contiene *buffers* que son zonas de almacenamiento temporales, donde los datos se mantienen hasta poder ser enviados.

---

**1.2.- Registros del Procesador.-**
Los procesadores incluyen un conjunto de registros que proporcionan un tipo de memoria más rápida y de menor capacidad que la memoria principal. Los registros tienen dos funciones:

- **Registros Visibles para el usuario.-**
Se puede acceder a el mediante el lenguaje máquina ejecutado por el procesador; los programadores pueden usar los registros de datos para diversas funciones. Suelen ser de propósito general, pero existen restricciones de registros dedicados a flotantes, o a operaciones con enteros.

-Registro Índice: El direccionamiento indexado es un modo común de direccionamiento que implica sumar un índice a un valor base para conseguir una dirección efectiva.

-Puntero de segmento: Mediante direccionamiento segmentado, la memoria se divide en bloques de palabras de longitud variable, referenciando con la memoria a un determinado segmento y un desplazamiento dentro del segmento. Es de gran importancia para la gestión de la memoria esta modo de direccionamiento.

-Puntero de Pila: Si el direccionamiento de pila es visible para el usuario, hay un registro dedicado que apunta a la cima de la pila, permitiendo el uso de *push* y *pop*.

Dependiendo del procesador, las funciones de salvar y restaurar se pueden hacer en hardware o software.

- **Registros de Control y Estado.-**
Muchos de los registros usados para el control del funcionamiento del procesador no son visibles para el usuario.

Todos los diseños de procesador incluyen un registro o conjunto de ellos, conocidos como PSW, Program Status Word, que contiene información de estado, contiene normalmente códigos de condición e información de estado, como bits para habilitar e inhabilitar interrupciones y un bit de modo usuario/supervisor.

Un factor fundamental del diseño de la organización del registro de control es dar apoyo al SO; se puede diseñar la organización de registros de manera que se proporcione soporte por parte del hardware con características particulares del SO, en aspectos como la protección de memoria y multiplexación entre programas del usuario.

--- 

**1.3.- Ejecución de Instrucciones.-**
El procesamiento de una instrucción, en su base, lee instrucciones de memoria, y ejecuta cada una de ellas, la ejecución de un programa consiste en repetir este proceso.
Un ciclo de instrucción es el procesamiento requerido por una instrucción.

- Búsqueda y Ejecución de una Instrucción.-
Al comienzo de cada ciclo de instrucción, el procesador lee una instrucción de memoria; típicamente, el registro PC almacena la dirección de la siguiente instrucción por leer, a menos de que se le indique otra cosa, el procesador siempre incrementa PC después de cada instrucción ejecutada. Estas ejecuciones se dividen en 4 categorías:

-Procesador<->Memoria: Se pueden transferir datos de procesador a memoria y viceversa.
-Procesador<->E/S: Se pueden enviar y recibir datos de un procesador a un periférico E/S.
-Procesamiento de datos: Operaciones aritmético/lógicas de los datos.
-Control: Alteración de la secuencia de ejecución.

- Sistema de E/S.-
Se puede intercambiar datos entre un módulo de E/S y el procesador, siendo deseable en algunos casos permitir que los intercambios de E/S se produzcan directamente con la memoria para liberar al procesador de la tarea de E/S, concediendole al módulo permiso de leer o escribir en memoria y así liberando al procesador de ser responsable de la acción. Esta operación se conoce como DMA, Directo Memory Access.

---

**1.4.- Interrupciones.-**

Existen 4 tipos de Interrupciones:

-De Programa: Generada por alguna condición producto de la ejecución de una instrucción.

-Por temporizador: Generada por un temporizador del procesador.

-De E/S: Generada por un controlador de E/S para señalar la conclusión normal de una operación o para señarlar condiciones de error.

-Por fallo del hardware: Generada por un fallo, ya sea de suministro energético o un error de la memoria.

- **Interrupciones y el Ciclo de Instrucción.-**
El procesador puede dedicarse a la ejecución de varias instrucciones gracias al uso de operaciones E/S, generando interrupciones.
Estas interrupciones se hacen mediante la captación de entradas, que provocan la interrupción y el salto a la ejecución de otro de los programas del usuario, saltando y saltando hasta terminar con los procesos de todos los programas, entradas y salidas; como todo este proceso ocurre en milisegundos, da la sensación de simultaneidad.

- **Procesamiento de interrupciones.-**
Los eventos producidos en el hardware tras la aparición de una interrupción son los siguientes:
-El dispositivo genera la interrupción.
-El procesador termina la ejecución de la instrucción actual.
-El procesador comprueba si hay una petición de interrupción pendiente, determina que hay una y manda una señal de reconocimiento para eliminar la señal de interrupción.
-El procesador se prepara para transferir el control a la rutina de interrupción, salvando la información requerida para reanudar el programa actual en el momento de la interrupción.
-El procesador carga el contador del programa con la posición del punto de entrada de la rutina de manejo de interrupción que responderá a la interrupción; una vez cargado el contador del programa, el procesador continúa con el siguiente ciclo de instrucción, que comienza con una lectura de instrucción.
-El contador del programa y la PSW se han almacenado en la pila del sistema; se necesita salvar el contenido de los registros del procesador, así como cualquier información de estado.
-El manejador de interrupción comenzará a procesar la interrupción, incluyendo un examen de la información de estado relacionada con la operación de E/S u otro evento que haya causado la interrupción.
-Al finalizar el procesamiento de la interrupción, se recuperan los valores de los registros guardados en la pila y se restituyen en los registros.
-La ultima acción es restituir los valores de la PSW y el contador del programa que se guardaron en la pila, la siguiente instrucción ejecutará el programa previamente interrumpido.

- **Múltiples Interrupciones.-**
Hay dos alternativas a considerar a la hora de tratar con múltiples interrupciones. 
La primera consiste en inhabilitar las interrupciones mientras ya haya una en procesamiento, estas interrupciones será ignoradas y pendientes de ser procesadas, solo pudiendo serlo una vez el procesador termine de procesar la interrupción y pase a la siguiente. El problema de este método es que no tiene en cuenta la prioridad relativa o la urgencia de las interrupciones, pudiendo propiciar el desbordamiento de buffers de E/S.
La segunda consiste en definir unas prioridades para las interrupciones, y permitir que si llega una interrupción de prioridad mayor a la que se esté ejecutando, esta se interrumpa y pase a ser ejecutada la de mayor prioridad, de esta forma, no hay riesgo de pérdida de datos ni de overflow.

- **Multiprogramación.-**
Incluso utilizando interrupciones, el procesador puede seguir siendo usado de forma ineficiente, para ello, se puede permitir que múltiples programas de usuario estén activos a la vez.
Esto se produce con el uso de interrupciones, dependiendo de la prioridad relativa de las instrucciones estas se van procesando y consecutivamente el procesador salta a la siguiente del otro programa.

---

**1.5.- La Jerarquía de Memoria.-**

Las restricciones en cuanto a memoria se clasifican en 3 cuestiones, su capacidad, su velocidad y su coste.
-A menor tiempo de acceso, mayor coste de bit.
-A mayor capacidad, menor coste de bit.
-A mayor capacidad, menor velocidad de acceso.

Este es el dilema al que el diseñador se enfrenta, para cumplir los requisitos de rendimiento, necesita usar memorias de capacidad relativamente baja con tiempos de acceso rápidos.
 Para solventar esto, se emplea una jerarquía de memoria:
 -Disminución de coste por bit.
 -Aumento de la capacidad.
 -Aumento del tiempo de acceso.
 -Disminución de la frecuencia de acceso a la memoria por parte del procesador.

De esa forma, las memorias más rápidas, caras y pequeñas se complementan con memorias más lentas, baratas y grandes, la clave del éxito de esta últimas es al disminución de la frecuencia de acceso.

---

**1.6.- Memoria Caché.-**

La memoria caché, a pesar de ser invisible para el SO, esta interactúa con otros elementos del hardware de gestión de memoria.

- **Motivación.-**
En todos los ciclos de instrucción, el procesador accede a memoria al menos una vez, para leer la instrucción y, más de una vez, para leer y almacenar los resultados, la velocidad a la que el procesador puede ejecutar instrucciones está claramente limitada por el tiempo de ciclo de memoria, limitación que es significativa. Idealmente, con la idea de velocidad, coste y tamaño, la memoria principal se construiría con la misma tecnología que la de los registros del procesador, pero es muy costoso. De forma que se aprovechó del principio de proximidad usando una memoria pequeña y rápida entre procesador y memoria principal, que es la cache.

- **Fundamentos de la Cache.-**
El propósito de la cache es proporcionar un tiempo de acceso a memoria próximo al de las memorias más rápidas, y ofrecer un tamaño de memoria grande que tenga el precio de los tipos de memorias de semiconductores, menos costosas.

- **Diseño de la Cache.-**
-Tamaño de la cache.
-Tamaño del bloque.
-Función de correspondencia.
-Algoritmo de reemplazo.
-Política de escritura.

El *tamaño de la cache*, se llega a la conclusión de que si una caché es de un tamaño razonablemente pequeo, impacta en el rendimiento significativamente.
El *tamaño del bloque*, según se incrementa el tamaño, se llevan a la cache más datos útiles, pero la tasa de aciertos comenzará a decrecer si el tamaño del bloque sigue creciendo.
La *función de correspondencia* determina la posición de la cache que ocupará el bloque, habiendo 2 restricciones; cuanto más flexible es una función de correspondencia, mayor grado de libertad a la hora  de diseñar un algoritmo de reemplazo que maximice la tasa de aciertos, por otra parte, también es más compleja la circuitería requerida para buscar en la caché, y determinar si hay un bloque ahí.
El *algoritmo de reemplazo*  selecciona que bloque reemplazar, siendo deseable que se reemplace aquel bloque que menos probablemente se use en el futuro inmediato.
La *política de escritura* dicta cuando tiene lugar una operación de escritura en memoria, cada vez que se actualiza el bloque, o solo cuando se reemplaza el bloque, el problema de la última es que, a pesar de que minimiza las operaciones de escritura en memoria, deja la memoria principal en estado obsoleto temporalmente.

---

**1.7.- Técnicas de Comunicación de E/S.-**

Existen 3 técnicas para llevar a cabo las operaciones de E/S:
-E/S programada
-E/S dirigida de interrupciones.
-Acceso directo a memoria, DMA.

- **E/S Programada.-**
Cuando el procesador ejecuta un programa y encuentra una instrucción relacionada con E/S, ejecuta esa instrucción generando un mandato al módulo de E/S correspondiente. En este caso, el módulo realiza la acción solicitada y fija los bits correspondientes en el registro de estado de E/S sin interrumpir al procesador.
Hay instrucciones de E/S de estas categorías:
-Control: Usadas para activar un dispositivo externo y darle una orden.
-Estado: Usadas para comprobar condiciones de estado del módulo de E/S y sus periféricos.
-Transferencia: usadas para leer y/o escribir datos entre registros del procesador y dispositivos externos.

- **E/S Dirigida por Interrupciones.-**
El módulo de E/S recibe mandatos de lectura del procesador, el módulo lee los datos y genera una interrupción, luego el procesador pide los datos al módulo, este los sitúa en el bus de datos y se prepara para otra operación.

- **Acceso Directo a Memoria.-**
Es la técnica más eficiente, cuando el procesador quiere leer o escribir un bloque de datos, genera un mandato al módulo de DMA, especificándole si es lectura o escritura, la dirección del dispositivo E/S, la posición inicial de memoria y el número de palabras a leer o escribir. Tras esto el procesador se centra en otra cosa, delegando la operación al módulo DMA, que se encargará de esta, de manera que el procesador solo se involucra al comienzo y al final de la operación.

---

-Alejandro Bernal Suárez
	Sistemas Operativos 2º
