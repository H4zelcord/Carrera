#2ºCarrera #SCD 

#### Sección 1.- Message Passing Interface (`MPI`).-

`MPI` es un estándar que define una API para programación paralela mediante el paso de mensajes que permite crear programas portables y eficientes.

Su esquema de funcionamiento implica un número fijo de procesos que se comunican mediante llamadas a funciones de envío y recepción de mensajes.

El modelo básico es `SPMD` (Single Program Multiple Data): Todos los procesos ejecutan un mismo programa.
Permite el modelo `MPMD` (Multiple Program Multiple Data): Cada proceso puede ejecutar un programa diferente.

La creación e inicialización de procesos no está definida en el estándar, depende de la implementación. En `OpenMDI` sería:
`mpirun -oversubscribe -np 4 -machinefile maquinas progr1_mpi_exe`
Comienza 4 copias de  `progr1_mpi_exe`, `maquinas` define la asignación de procesos a ordenadores del Sist. Distribuido.

Se tiene que hacer `#include <mpi.h>`.
Las funciones devuelven código de error o `MPI_SUCCESS`.
`MPI_Status` es un tipo estructura con metadatos de los mensajes.
`status.MPI_SOURCE`: proceso fuente.
`status.MPI_TAG`: etiqueta del mensaje.
Hay constantes para representar tipos de datos básicos de C/C++. Siendo estos `MPI_<tipo>` para `char`, `int`, `long`, `double`, `float`, `long double`, `unsigned`, `unsigned char` y `long`.

#### Sección 2.- Compilación y ejecución de programas `MPI`.-

`OpenMPI` es una implementación portable y open-source del estándar `MPI-2`.
Ofrece varios scripts necesarios para trabajar con programas aumentados con llamadas a funciones de `MPI`. Estos dos son importantes:
`mpicxx`: compilar y/o enlazar programas C++ con `MPI`.
`mpirun`: ejecuta programas `MPI`.
Se puede compilar con las opciones habituales, pero se puede compilar también sin `.o`.
La forma más común de ejecutar un programa `MPI` es:
`mpirun -oversubscribe -np 4 ./ejemplo_mpi_exe`
`-np` sirve para indicar cuantos procesos ejecutarán el programa ejemplo.
Al no indicarse `-machinefile`, `OpenMPI` lanzará los 4 procesos en el mismo ordenador donde se ejecutará `mpirun`.
La opción `-oversubscribe` puede ser necesaria si el número de procesadores disponibles es inferior al número de procesos que se quieren lanzar en ese ordenador.

#### Sección 3.- Funciones `MPI` básicas.-

Hay 6 funciones básicas en `MPI`:
`MPI_Init`: Inicializa el entorno de ejecución de `MPI`.
`MPI_Finalize`: Finaliza el entorno de ejecución de `MPI`.
`MPI_Comm_size`: Determina el número de procesos de un comunicador.
`MPI_Comm_rank`: Determina el identificador del proceso en un comunicador.
`MPI_Send`: Operación básica para envío de un mensaje.
`MPI_Recv`: Operación básica para recepción de un mensaje.

###### Subsección 3.1.- Introducción a los comunicadores.-

Un comunicador `MPI` es una variable de tipo `MPI_Comm`, formado por:
- Grupo de procesos: Subconjunto de procesos.
- Contexto de comunicación: Ámbito de paso de mensajes en el que se comunican dichos procesos. Un mensaje enviado en un contexto solo se puede recibir en dicho contexto.

La constante `MPI_COMM_WORLD` hace referencia al comunicador universal, predefinido e incluye todos los procesos lanzados.
-Un proceso puede pertenecer a diferentes comunicadores.
-Cada proceso tiene un identificador  0-`P-1` siendo P el nº de procesos del comunicador.
-Mensajes destinados a diferentes contextos de comunicación no interfieren entre si.

###### Subsección 3.2.- Funciones básicas de envío y recepción de mensajes.-

Un proceso puede enviar un mensaje usando `MPI_Send`.
-Envía los datos (`num` elementos de tipo `datatype` almacenados a partir de `buf_emi`)al proceso `des`, dentro del comunicador `comm`.
-El entero `tag` se transfiere junto con el mensaje, suele usarse para clasificar los mensajes en distintas categorías o topos en función de sus etiquetas. Es no negativo.
Implementa envío asíncrono seguro, tras acabar `MPI_Send`, se puede volver a escribir sobre `buf_emi`, y el receptor no necesariamente ha iniciado la recepción del mensaje.

Un proceso puede recibir un mensaje usando `MPI_Recv`.
-Espera hasta recibir un mensaje del proceso `source` dentro del comunicador `comm` con la etiqueta `tag`, y escribe los datos en posiciones contiguas desde `buf_rec`.
-Como espera a que el emisor envíe, es recepción síncrona, y es segura ya que al acabar se pueden leer en `buf_rec` los datos transmitidos.
-Se pueden dar valores especiales:
- Si `source` es `MPI_ANY_SOURCE` se puede recibir un mensaje de cualquier proceso del comunicador.
- Si `tag` es `MPI_ANY_TAG` se puede recibir un mensaje con cualquier etiqueta.

Los datos se copian desde `buf_emi` hacia `buf_rec`.
-Los argumentos `num` y `datatype` determinan la longitud en bytes del mensaje. El objeto `status` es una estructura con el emisor `MPI_SOURCE` y la etiqueta `MPI_TAG`.

Para determinar el emparejamiento `MPI` no tiene en cuenta el tipo de datos ni la cuenta de items, siendo responsabilidad del programador:
-Que los bytes transferidos se interpretan con el mismo tipo de datos que el emisor usó en el envío.
-Que se sepa exactamente cuantos items de datos se han recibido.
-Que se ha reservado suficiente memoria.

#### Sección 4.- Paso de mensajes síncrono en `MPI`.-

En `MPI` existe una función de envío síncrono, con los mismos argumentos de `MPI_Send`, siendo este `MPI_Ssend`.

Tras acabar:
-Ya se ha iniciado en el receptor una operación de recepción que encaja con este envío.
-Los datos ya se han leído de `buf_emi` y se han copiado a otro lugar, pudiéndose escribir en `buf_emi` de nuevo.

#### Sección 5.- Sondeo de mensajes.-

`MPI` incorpora dos operaciones que permiten a un proceso receptor averiguar si hay algún mensaje pendiente de recibir, en cuyo caso obtener los metadatos de dicho mensaje. Esto:
-No supone la recepción del mensaje.
-Se puede restringir a mensajes de un emisor.
-Se puede restringir a mensajes con una etiqueta.
-Cuando hay mensaje, permite obtener los metadatos: emisor, etiqueta y número de items (de tipo conocido).

Las dos operaciones son:
-`MPI_Iprobe`: consultar si hay o no mensajes pendientes.
-`MPI_Probe`: esperar bloqueado hasta que haya al menos un mensaje.

Con `MPI_Probe`:
-`source`: puede ser un identificador de emisor o `MPI_ANY_SOURCE`.
-`tag` puede ser una etiqueta o `MPI_ANY_TAG`.
-`status`: permite conocer los metadatos del mensaje, igual que se hace tras `MPI_Recv`.
Si hay mas de un mensaje disponible, se refieren al primero que se envió.

Con `MPI_Iprobe`:
Cuando termina, el entero apuntado por `flag` será `>0` solo si hay algún mensaje enviado al proceso que llama, y que encaje con los argumentos, si no hay mensajes el entero `=0`.
-No supone bloqueo alguno.
-La consulta se refiere a los mensajes pendientes en el momento de la llamada.
-Los parámetros (menos `flag`) se interpretan como en `MPI_Probe`.

#### Sección 6.- Comunicación insegura.-

`MPI` ofrece la posibilidad de usar operaciones inseguras (asíncronas). Permiten el inicio de una operación de envío o recepción, y después el emisor o el receptor puede continuar su ejecución de forma concurrente.
`MPI_Isend`: Inicia envío pero retorna antes de leer el bufffer.
`MPI_Irecv`: Inicia recepción pero retorna antes de recibir.

En algún momento posterior se puede comprobar si la operación ha terminado o no, se puede hacer de dos formas:
`MPI_Wait`: Espera bloqueado hasta que acabe el envío o recepción.
`MPI_Test`: Comprueba si el envío o recepción ha finalizado o no. No supone espera bloqueante.

Los argumentos de `MPI_Isend` y `MPI_Irecv` son similares a `MPI_Send`, menos:
-`request`, que es un ticket que permitirá después identificar la operación cuyo estado se pretende consultar o se espera que finalice.
-La recepción no incluye argumento `status`.

Si ya no se va a usar una variable `MPI_Request`, se puede liberar la memoria que usa con `MPI_Request_free`.

Para `MPI_Test` y `MPI_Wait`, una vez terminada la operación referenciada por el ticket:
-Podemos usar `status` para consultar los metadatos del mensaje.
-La memoria usada por `request` es liberada por `MPI`.

Las operaciones inseguras:
-Permiten simultanear trabajo útil en el emisor y/o receptor con la lectura, transmisión y recepción del mensaje.
-Aumentan el paralelismo potencial y, por lo tanto pueden mejorar la eficiencia en tiempo.

Las operaciones de consulta de estado (`MPI_Test` y `MPI_Wait`) permiten saber cuando es seguro volver a usar el buffer de envío o recepción, ya que dicen que la operación ha acabado cuando:
-Se han leído y copiado los datos del buffer del emisor.
-Se han recibido los datos en el buffer del receptor.

Una operación insegura se puede emparejar con una operación segura y/o síncrona.