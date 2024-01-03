#2ºCarrera 
#SO 

---
#### 3.1.-  ¿Qué es un proceso? .-

La gestión de la ejecución de aplicaciones se hace de forma que:
-Los recursos estén disponibles para múltiples aplicaciones.
-El procesador se conmute entre múltiples aplicaciones, de forma que se procesen todas.
-El procesador y los dispositivos de E/S se puedan usar de forma eficiente.
El enfoque adoptado por todos los SO modernos tiene como base la ejecución de una aplicación que corresponde con la existencia de varios procesos.

**Procesos y Bloques de Control de Procesos**

Un proceso se podía definir como:
-Un programa en ejecución.
-Una instancia de un programa ejecutado.
-La entidad asignable y ejecutable en un procesador.
-Unidad de actividad caracterizada por la ejecución de una secuencia de instrucciones, un estado actual y un conjunto de recursos del sistema asociados.

También se puede definir como una entidad que tiene un número de elementos, siendo dos esenciales el código del programa y un conjunto de datos asociados a dicho código.
Un proceso se caracteriza por una serie de elementos mientras está en ejecución, estos son:
-Identificador: Único asociado al proceso.
-Estado: stop, exec y wait (entre los estados de actividad de un proceso).
-Prioridad: Nivel de prioridad de ejecución respecto al resto.
-Contador de Programa: La dirección de la siguiente instrucción del programa a ejecutar.
-Punteros a memoria: Punteros al código de programa y datos asociados a dicho proceso, y cualquier bloque de memoria compartido con otros procesos.
-Datos de contexto: Datos presentes en los registros del procesador cuando está en ejecución.
-Información de estado E/S: Peticiones de E/S pendientes, disp. E/S asignados al proceso, una lista de ficheros en uso por el mismo...
-Información de auditoría: Cantidad de tiempo de procesador y de tiempo de reloj usados, límites de tiempo, registros contables...


---
#### 3.2.- Estados de los procesos.-

**Un Modelo de Proceso de dos Estados**

La responsabilidad principal de SO es controlar la ejecución de los procesos, incluyendo determinar el patrón de entrelazado para la ejecución y asignar recursos a los procesos.
En un modelo simple que consta de los estados, Ejecutando y No Ejecutando, al crear un proceso este se guarda con el estado No Ejecutando y se le da visibilidad en el SO, para que al pasar un tiempo pase a Ejecutando y se haga.

**Creación y Terminación de Procesos**

- Creación de un proceso:
El sistema operativo construye las estructuras de datos usadas para manejar el procesa y reserva el espacio de direcciones en memoria principal para el mismo, esto es la creación de un nuevo proceso.
Un proceso puede lanzar otro, siendo el que lanza el proceso *padre*.

- Terminación de procesos:
Todo SO debe proporcionar los mecanismos mediante los cuales un proceso indica su final, o su finalización de la tarea pertinente. Un trabajo por lotes debe de incluir una instrucción HALT o una llamada a un servicio del SO específica para la terminación. Para una aplicación interactiva, las acciones del usuario indicarán la finalización del proceso. En un sistema de tiempo compartido, el proceso de un usuario en particular puede terminar cuando el usuario sale del sistema o apaga su terminal...

**Modelo de Proceso de 5 Estados**

Una forma más eficiente de manejar los estados es dividiendo el estado No Ejecutando en Listo y Bloqueado, haciendo un diagrama de 5 estados que serían:
-Ejecutando: Proceso en ejecución.
-Listo: Preparado para ejecutarse.
-Bloqueado: No ejecutable hasta cumplido determinado evento o operación E/S.
-Nuevo: Proceso recién creado que aún no se ha cargado en memoria principal (No está en listo aún).
-Saliente: Proceso liberado de ejecutable que ha sido detenido o abortado.

El cambio entre estados sería el siguiente:
Null -> Nuevo: Creación del proceso nuevo.
Nuevo -> Listo: En cuanto el proceso se cargue y esté preparado se pasa a listo (si no pasa el límite de procesos en listo disponible).
Listo -> Ejecutando: A elección del SO, y con ayuda del planificador se ejecutan los procesos de 1 en 1.
Ejecutando -> Saliente: Finaliza la ejecución del proceso, ya se aborte o se haya completado su ejecución.
Ejecutando -> Listo: El proceso en ejecución ha alcanzado el tiempo de ejecución máximo permitido por el SO o bien hay un proceso con un nivel más alto de prioridad en Listo.
Ejecutando -> Bloqueado: Al solicitar algo que requiere espera (por llamadas al sistema), como un fichero, comunicación entre procesos o una operación E/S.
Bloqueado -> Listo: Al terminar el evento que se estaba esperando.
Listo -> Saliente: Al terminar el proceso, un proceso padre ocasiona la terminación de procesos hijos.
Bloqueado -> Saliente: Como comentado antes, ocurre si es un proceso hijo y el padre lo termina.

**Procesos Suspendidos**

El swapping implica mover parte o todo el proceso de memoria principal al disco. Puede suponer un riesgo de empeorar el problema, pero ya que las E/S en disco son mucho más rápidas que en otros sistemas, suele mejorar el rendimiento del sistema.

Al hacer la operación swap, hay 2 opciones para seleccionar un proceso a traer a memoria principal: admitir un proceso creado o traer un proceso en estado suspendido. Se replantea el sistema anterior y nos quedamos con estos estados:
-Listo: Disponible para ejecutar.
-Bloqueado: En memoria principal y esperando un evento.
-Bloqueado/Suspendido: En almacenamiento secundario y esperando un evento.
Listo/Suspendido: El proceso está en almacenamiento secundario y disponible para ejecución nada más se cargue en memoria principal.

Pueden cambiar entre si de esta manera:

Bloqueado -> Bloqueado/Suspendido
Bloqueado/Suspendido -> Listo
Listo/Suspendido -> Listo
Listo -> Listo/Suspendido

Aunque otros cambios entre estados pueden ser:

Nuevo -> Listo/Suspendido
Nuevo -> Listo
Bloqueado/Suspendido -> Bloqueado
Ejecutando -> Listo/Ejecutando
Cualquier estado -> Saliente

---
#### 3.3.- Descripción de los procesos.-

**Estructuras de Control del Sistema Operativo**

Para que el SO se encargue de la gestión de procesos y recursos, debe disponer de información sobre el estado actual de cada proceso y de cada recurso, manteniendo tablas de información sobre cada entidad a gestionar.

Son las tablas de memoria, que se usan para mantener un registro de la memoria principal y de la secundaria, parte de la memoria principal se reserva para el uso del SO, el resto es para el uso de los procesos, estos se mantienen en memoria secundaria usando swapping, las tablas de memoria deben de incluir:
-Reservas de memoria principal por parte de los procesos.
-Reservas de memoria secundaria por parte de los procesos.
-Atributos de protección que restringen el uso de memoria principal y secundaria, de forma que los procesos accedan a áreas de memoria compartida.
-Información necesaria para manejar la memoria secundaria

El SO también maneja tablas de E/S y de ficheros, que regulan su uso, existencia y asignación, además de su estado. Y de forma obligatoria una tabla de procesos para gestionarlos bien.

**Estructuras de Control de Proceso**

*Localización de los procesos:* La posición de la imagen del proceso dependerá del esquema de gestión de memoria a utilizar, en el caso más simple se mantiene como un bloque de memoria contiguo o continuo, mantenido en memoria secundaria, disco. Para que el SO pueda gestionar el proceso, al menos una parte se tiene que localizar en memoria principal, y para ejecutarlo, su imagen debe de cargarse al completo en memoria principal, o en secundaria al menos; el SO debe de conocer la posición en disco de cada proceso y para los que se encuentren en memoria principal, su posición en la misma.

*Atributos de proceso:* Cualquier SO de la actualidad requiere de mucha información para el manejo de cada proceso, que se suele guardar en el BCP, que agrupa:
-Identificación del proceso: Identificador numérico único por proceso.
-Información de estado del procesador: Contenido de los registros del procesador.
-Información de control del proceso: Necesaria para controlar y coordinar varios procesos activos por parte del SO.

El BCP (Bloque de Control de Proceso) es la estructura de datos de mayor relevancia del SO, cada BCP contiene toda la información de un proceso que el SO necesita, pudiendo decirse que los BCP definen el estado del SO. 
Esto toma gran relevancia en el diseño, ya que un fallo en una simple rutina, en una estructura o semántica de los BCP pueden suponer el fallo total del SO.

---
#### 3.4.- Control de procesos.-

**Modos de Ejecución**

Existen varios modos, desde uno con menos privilegios, el Modo Usuario, que es en el que los programas usuario se ejecutan normalmente, pasando al más privilegiado que es el Modo Sistema/Control/Núcleo, que se encarga de la gestión del SO, los BCP, el control de los componentes... Para diferenciar entre los Modos se usa el bit de la PSW que indica el modo de ejecución.

**Creación de Procesos**

La creación de procesos se hace de la siguiente manera:
-Asignación de un identificador de proceso único.
-Reserva de espacio para el proceso.
-Inicialización del BCP.
-Establecer los enlaces apropiados.
-Creación o expansión de otras estructuras de datos.

**Cambio de Proceso**

*Cuando se realiza:* Puede ocurrir en cualquier instante, puede ser por interrupción de reloj, de E/S o por un fallo de memoria, en todo caso devolviendo el control al Sistema Operativo para la gestión.

*Cambio de modo:* El PC lo pone en la dirección de comienzo de la rutina del programa manejador de la interrupción. Luego cambia de modo usuario a modo Núcleo de forma que el código de tratamiento de la interrupción pueda incluir instrucciones de privilegio.

*Cambio del Estado del proceso:* Para que se realice el cambio de proceso completo se siguen estos pasos:
-Salvar el estado de la CPU, incluyendo los registros.
-Actualizar el BCP que está en estado Ejecutando y cambiar de estado.
-Mover el BCP a la cola correspondiente.
-Seleccionar un nuevo proceso a ejecutar.
-Actualizar el BCP elegido y pasarlo a Ejecutando.
-Actualizar las estructuras de datos de gestión de memoria.
-Restaurar el estado de la CPU.

**Ejecución del Sistema Operativo**

*Núcleo sin Procesos:* Visión tradicional común en muchos SO antiguos. Cuando el proceso se interrumpe o invoca una llamada al sistema, se guarda el contexto y se pasa el control al núcleo; el SO tiene su propia región de memoria y su propia pila de sistema para controlar la llamada a procedimientos y sus retornos y puede restaurar el contexto del proceso interrumpido, que provoca que se retome la ejecución del proceso de usuario afectado. Se separan procesos de código de SO, como una entidad de modo privilegiado.

*Ejecución dentro de los procesos de usuario:* No requiere de un doble cambio del proceso ya que invoca el cambio de Modo dentro de la ejecución del proceso, pudiendo tener varios modos en diferentes momentos de la ejecución del mismo. Dentro de un proceso pueden ejecutarse tanto un programa de usuario como programas del SO.

*Sistemas Operativos basados en procesos:* Las funciones del SO se separarán en forma de procesos, con claras interfaces entre módulos y diferentes prioridades de cada proceso, que el SO implementado decide activar, todo pasa por el SO y se incrementa el rendimiento.

---
