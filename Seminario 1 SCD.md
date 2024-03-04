#2ºCarrera 
#SCD
#### Sección 1. Concepto e Implementaciones de Hebras.-

En un instante, muchos procesos se pueden ejecutar de forma concurrente, correspondiéndole cada uno a un programa en ejecución y ocupando una zona de memoria con, al menos, texto, datos, pila y memoria dinámica *heap*.
Cada proceso tiene asociados PC y SP, siendo respectivamente "Contador de Programa" y "Puntero de Pila".

La gestión de varios procesos no independientes es muy útil, pero consume muchos recursos del SO. Tiempo de procesamiento para repartir la CPU entre procesos, memoria con datos del SO relativos a cada proceso, además de tiempo y memoria para comunicarse entre procesos.
Para aumentar la eficiencia se diseñaron las *hebras*:
-Un proceso puede contener una o más hebras.
-Una hebra es un flujo de control en el texto del proceso al que pertenecen.
-Cada hebra tiene su propia pila vacía al inicio.
-Las hebras de un proceso comparten la zona de datos y el *heap*.

Al principio del programa, existe una única hebra que ejecuta main(). Durante la ejecución del programa, una hebra A puede crear una hebra B en el mismo proceso de A, designando un subprograma F (función en C++) del texto del proceso y continúa su ejecución. La hebra B ejecuta la función f concurrentemente con el resto de hebras, terminando normalmente cuando finaliza de ejecutar la función, ya sea con return, o cuando el flujo de control finaliza f.
Una hebra puede esperar a que otra hebra en ejecución finalice.

#### Sección 2. Hebras en C++11.-

2.1.- Introducción.-

El estándar define tipos de datos, clases y funciones para:
-Crear una nueva hebra concurrente en un proceso y esperar a que termine.
-Declaración de variables de tipo atómico.
-Sincronización de hebras con exclusión mutua y/o variables condición.
-Bloqueo de una hebra durante un intervalo de tiempo o hasta cierto instante de tiempo.
-Generación de números aleatorios.
-Medición de tiempos reales y de proceso con alta precisión.

2.2.- Creación y finalización de hebras.-

El tipo de datos `std::thread` permite definir objetos de tipo hebra. Un objeto de ese tipo puede contener información sobre una hebra en ejecución.
-En la declaración de la variable se indica el nombre de la función a ejecutar por la hebra.
-En tiempo de ejecución, cuando se crea a variable, se empieza la ejecución concurrente de la función por parte de la nueva hebra.
-En la declaración se pueden especificar los parámetros de la nueva hebra.
-La variable sirve para poder referenciar a la hebra luego.

Es posible declarar las variables y después poner en marcha las hebras por separado.

Una hebra A que ejecuta f, finaliza cuando A llega al final de f; cuando A ejecuta un return en f; cuando se lanza una excepción que no se captura en f ni en ninguna función llamada desde f; cuando se destruye la variable hebra asociada(sería un error).
Todas las hebras en ejecución finalizan cuando llaman a exit(), abort o terminate, o cuando la hebra principal main() deja de ejecutarse (situación de error).

2.3.- Sincronización mediante unión.-

Es necesario esperar a que las hebras creadas terminen antes de terminar el programa/hebra principal; para esto existe la *operación de union* (join).

La sincronización de hebras se puede hacer usando join, mutex o variables condición. La operación join permite que una hebra A espere a que una hebra B termine.. Siendo A la hebra invocante, y B la hebra objetivo. Al finalizar la llamada, B ha terminado con seguridad, si B no ha acabado, no hace nada. Si se necesita esperar a que acabe B, la espera no consume CPU, A queda en modo suspendido.
Cualquier tarea ejecutada por A después de join, se ejecutará cuando join termine (B haya acabado).

Join solo se puede usar sobre una hebra que está ejecutándose, o sobre una hebra que, habiendo acabado, no se ha invocado join sobre la misma. En cualquier otro caso es una forma incorrecta de invocar join. El método joinable devuelve true o false en función de si lahebra se encuentra activa.

2.4.- Paso de parámetros y obtención de un resultado.-

Si bien las hebras ejecutadas devuelven void, estas se pueden usar con parámetros, pero aunque la devolución sea de un valor distinto a void, este se ignora cuando se hace join. Para obtener un resultado, se barajan estas opciones; el uso de variables globales compartidas, el uso de parámetros de salida y el uso de la sentencia return, lanzando con async la hebra.
Si la hebra tiene parámetros, se deben de especificar al poner la hebra en marcha.

2.5.- Vectores de hebras y futuros.-

En muchos casos, un problema se puede resolver con un proceso en el que varias hebras ejecutan la misma función con distintos datos de entrada, siendo necesario que cada hebra reciba diferentes parámetros, siendo común que cada hebra reciba un número de orden o identificador de hebra distinto desde 0, pudiendo usarse un vector de variables de tipo hebra o variables de tipo futuro, permitiendo esto que el número de hebras sea un parámetro configurable.

2.6.- Medición de tiempos.-

En C++11 se puede medir la duración del intervalo de tiempo real empleado en cualquier momento de la ejecución del programa. Basándose estas mediciones en servicios del SO, de alta precisión. Las mediciones se basan en dos tipos de datos en `std::chrono`; instantes en el tiempo: de tipo time_point, que es el tiempo desde un instante de inicio de un reloj. Duraciones de intervalos de tiempo, de tipo duration, que es la diferencia entre dos instantes de tiempo, que se puede representar con enteros o flotantes, ya sea en nanosegundos, microsegundos, milisegundos, segundos...

Hay 3 clases(tipo de dato) para 3 relojes distintos:
Reloj del sistema (`system_clock`). Tiempo indicado por la hora/fecha del sistema, pudiendo sufrir ajustes o cambios que le hagan dar saltos adelante o atrás en el tiempo.
Reloj monotónico (`steady_clock`). Mide el tiempo real desde un instante y no sufre saltos ni retrocede.
Reloj de alta precisión (`high_precision_clock`). Reloj de máxima precisión en el sistema, pudiendo ser el mismo que uno de los 2 anteriores, u otro diferente.

2.7.- Cálculo numérico de integrales.-

La programación concurrente se puede usar para resolver multitud de problemas, que conllevan muchas operaciones con float, rápidamente.
La programación concurrente se puede usar para acelerar el cálculo de integrales definidas, dividiendo el trabajo en múltiples hebras que calculan sumas parciales de la función en puntos discretos. Esto permite una mayor eficiencia en el cálculo, especialmente cuando se trata de un gran número de puntos. La programación concurrente distribuye el trabajo entre múltiples hebras, lo que puede aumentar significativamente la velocidad de cálculo en sistemas con múltiples núcleos o procesadores.

#### Sección 3. Sincronización básica en `C++11`

Tipos atómicos: Tipos de datos cuyas variables se pueden actualizar de forma atómica, es decir, en exclusión mutua.
Objetos mutex: Son variables que incluyen operaciones que permiten garantizar la exclusión mutua en la ejecución de trozos de código.

La interfoliación de las operaciones de consulta y actualización de variables compartidas entre hebras concurrentes puede dar resultados distintos de los esperados.
Incrementar o decrementar una variable entera o flotante se hace en varias instrucciones atómicas distintas, pudiendo hacer que dos incrementos simultáneos de una variable entera la dejen como una unidad más en lugar de dos más.
Insertar o eliminar un nodo de una lista o árbol, puede que dos intersecciones simultáneas produzcan que alguno de los nodos no se inserte.
En el primer caso se pueden usar tipos atómicos, mientras que en el segundo objetos mutex.

3.1.- Tipos de datos atómicos.-

Para cada tipo entero (char, int, long, unsigned...) existe un correspondiente atómico, llamado `atomic<T>` o `atomic_T`.
Para los enteros, las operaciones se suelen implementar con instrucciones hardware atómicas específicas del juego de instrucciones del procesador.

3.2.- Objetos mutex.-

Se usan para operaciones que requieren de exclusión mutua. Las únicas operaciones que se pueden hacer de tipo mutex son `lock` y `unlock`. Que funcionan bloqueando y desbloqueando hebras para que no ocupen CPU.

#### Sección 4.- Introducción a los Semáforos.-

4.1.- Estructura, operaciones y propiedades.-

Un semáforo es una instancia de un registro, que contiene un conjunto de procesos bloqueados (en espera) y un valor natural.
Residen en memoria compartida, el conjunto de procesos asociados estará vacío, además de que se deberá de indicar un valor inicial del semáforo.

Hay 2 operaciones básicas para usar en una variable de tipo semáforo: `sem_wait(s)` y `sem_signal(s)`, las cuales, respectivamente, si s = 0, espera a que sea mayor que 0 y el proceso se bloquea, decrementa s; el otro incrementa s y si hay procesos esperando, permite que uno de ellos salga y se empiece a ejecutar (decrementando el valor del semáforo en el proceso).

El valor del semáforo nunca es negativo, solo hay procesos esperando cuando el valor es 0, el valor de un semáforo indica cuantas llamadas a sem_wait podrían ejecutarse en ese momento sin que ninguna haga esperar.

Hay 3 tipos de problemas de sincronización; espera única, exclusión mutua y productor/consumidor con lecturas y escrituras repetidas.

4.2.- Espera única.-

Un proceso P2 no debe pasar de un lado a otro de su código hasta que P1 no haya llegado a cierto punto del suyo. P1 debe de escribir una variable compartida y después P2 debe leerla.

4.3.- Exclusión mutua.-

Queremos que en cada instante de tiempo solo haya un proceso como mucho ejecutando un trozo de código que llamamos sección crítica. Tomando la forma de interfoliación I F I F I F I F...

4.4.- Productor/Consumidor (lectura/escritura repetidas).-

Se permite interfoliación de tipo E L E L E L E L... de forma que no se puede producir a la vez que se consume ni viceversa, si no que toma turno.

#### Sección 5.- Semáforos en C++11.-

5.1.- Introducción: Operaciones y Compilación.-

Se denominan `Semaphore`. Se deben de inicializar en la declaración del semáforo.
Sus funciones son `sem_wait` y `sem_signal`. Cuando hay varias hebras esperando, sem_signal despierta a la primera de ellas, que entró al sem_wait (metodo FIFO).

Una variable semáforo no puede copiar a otra. Se pueden declarar arrays de semáforos de tamaño fijo, se puede usar un vector STL si el tamaño no se conoce o es muy grande. Se usa típicamente Sempahore variables globales.
Se tiene que hacer `#include "scd.h"` y `using namespace scd;`

5.2.- Implementación del ejemplo productor/consumidor.-

Usando Semaphore, se usará una hebra productora y una consumidora, usandose funciones de ``producir_valor()`` y ``consumir_valor()``. La función que ejecutase la hebra productora usa dos semáforos compartidos para escribir en la variable compartida. Mientras que la función que ejecuta la hebra consumidora usa los mismos semáforos para leer de la variable compartida.