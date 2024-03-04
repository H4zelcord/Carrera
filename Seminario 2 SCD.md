#2ºCarrera 
#SCD 

#### Sección 1.- Encapsulamiento y exclusión mutua.-

1.1.- Encapsulamiento mediante clases `C++`.-

Para lograr la exclusión mutua en monitores, usaremos objetos mutex asociados a cada instancia del monitor, que llamaremos *cerrojo,* al cual se le hará `lock` y `unlock` entre la ejecución de un método público, logrando la ejecución con exclusión mutua.

1.2.- Exclusión Mutua mediante uso directo de cerrojos.-

El problema que entraña el uso de `lock/unlock` es que en casos de existencia de return, hay que hacer la copia previa en una variable local, y que en un método con varios unlock hay que repetir la llamada a unlock antes de cada return. También, en caso de ocurrirse una excepción durante la ejecución, no se liberaría el cerrojo, llevando al interbloqueo.

1.3.- Exclusión mutua mediante guardas de cerrojo.-

Una guarda de cerrojo es una variable local a cada método exportado del monitor, variable, que referencia al cerrojo; esta se declara al inicio del método, y en la creación se gana la exclusión mutua, al ser variable local, se destruye automáticamente al final del método, invocándose `unlock` en la destrucción. Se usa ``unique_lock`` o ``lock_guard`` pero generalmente ``unique_guard`` ya que es compatible con las variables condición.


--- 
#### Sección 2.- Monitores nativos tipo *Señalar y Continuar* (SC).-

El tipo de dato de las variables condición es ``variable_condition``. Una variable condición contiene una lista, seguramente vacía de hebras bloqueadas en espera de que una condición se cumpla.
Existen métodos `variable_condition` para esperar y señalar, los cuales son:
- ``wait(guarda)``: Para hacer espera bloqueada, liberando durante la espera el cerrojo del monitor. La hebra que lo llama debe de poseer el cerrojo al llamar.
- ``notify_one( )``: Para liberar una hebra de las que esperan. La hebra que llama, continúa en ejecución tras la llamada, si hay alguna hebra señalada se pone en espera en la cola del monitor para readquirir el cerrojo.
- ``notify_all( )``: Para despertar todas las hebras que esperan. La hebra señaladora continúa la ejecución y la señaladas esperan en la cola del monitor.
Si hay mas de 1 hebra esperando en una ``variable_condition``, una llamada a ``notify_one`` despierta a una **cualquiera** de ellas.

2.1.- Monitor de barrera simple.-

Para las preguntas que se plantean en la diapositiva Nº35:
- 1 .-La última hebra activa la señalización y es la primera en ser despertada porque las demás están esperando.

- 2.-La competencia por el cerrojo después de la señalización puede alterar el orden en el que las hebras entran de nuevo al monitor.

- 3.- El constructor no realiza operaciones críticas y no comparte recursos, por lo que no requiere exclusión mutua.

- 4.- `notify_all` despierta todas las hebras esperando, lo que puede cambiar la competencia por el cerrojo y afectar la sincronización y el orden de ejecución.

2.2.- Monitor de barrera parcial.-

La barrera parcial añade complejidad en la sincronización entre grupos de hebras, afectando a la entrada y salida la competencia por el cerrojo. Se alternan el cerrojo entre grupos y produce mas alteraciones en la ejecución del programa.



---
#### Sección 3.- Monitores tipo *Señalar y Espera Urgente (SU)*.-

3.1.- Monitor de barrera parcial con semántica SU.-

En los monitores con semántica Señalar y Espera Urgente, cuando una hebra señaladora hace `signal` en una cola donde hay una hebra señalada esperando:
- La hebra señalada adquiere el cerrojo del monitor y sigue ejecutando las sentencias que siguen al `wait`.
- La hebra señaladora pasa a esperar a una cola especial, llamada **cola de urgentes**.
Cuando la hebra libera el cerrojo del monitor:
- Si hay hebras esperando en **cola de urgentes**, la que antes entró se libera y continúa ejecutando código tras el ``signal``.
- Si no hay hebras en la **cola de urgentes**, pero hay en la cola del monitor, una de ellas puede acceder al mismo.
Los monitores SU suelen permitir diseños más simples:
- La hebra señalada no tiene que competir con otras hebras para adquirir el cerrojo del monitor y reanudar la ejecución.
- Cada hebra espera en la cola del monitor como mucho 1 vez.
- Garantiza que la hebra señalada reanuda su ejecución inmediatamente tras `signal`.
- Como consecuencia, la hebra señalada tiene garantizado que al salir del `wait`, se cumple la condición esperada.



---
