#2ºCarrera #SO 

---
#### FIFO (First in First Out).-

El primero en llegar es el primero en ejecutarse, a partir de que el primero se acabe, el siguiente será el segundo en haber llegado y así consecutivamente.

Si su tiempo de llegada es el mismo va por orden numérico del proceso (P1, P2, P3, P4... ).

#### SJF (Shortest Job First).-
El Tₑ Medio es menor.
Se comporta como un FIFO si todos los procesos tienen la misma duración de ráfaga
###### No apropiativa:
El proceso de ráfaga más corta es el siguiente, pero espera a que el que se está ejecutando acabe.

###### Apropiativa (SRTF):
El proceso de ráfaga más corta es el siguiente, y se ejecuta nada más aparezca si es más corto que el actual.


#### Round-Robin (RR).-
Su Tₑ Medio es bajo si la duración de los trabajos varía, pero es alto si la duración es igual.
Su rendimiento depende de q (*quantum*).
-q grande sería FIFO.
-q pequeña ocasionaría sobrecarga si no es grande respecto a la duración del cambio de contexto.

Mayor tiempo de retorno que SRTF, pero mejor respuesta.

Usando la planificación por prioridades, se le asigna un *quantum* de CPU (en ms) a cada proceso. Dado el *quantum*, si el proceso sobrepasa ese tiempo sin haber acabado, se pone al final de la cola de preparados.



### Colas.-

La cola de preparados se fragmenta en varias cola, las cuales pueden tener diferentes algoritmos de planificación en función a la necesidad.

Existirán métodos para ascender o degradar de una cola a otra a un proceso, en función de su tamaño, prioridad...

La prioridad puede asignarse en función de la cantidad de E/S, subiendo, o bajando a más CPU consuma.

En un entorno con varias CPUs, pueude distribuirse la carga entre estas, o puede equilibrarse uniformemente entre ellas.