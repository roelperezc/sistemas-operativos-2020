# Tarea 2. Comparación de planificadores.
## Roel Perez
## Realizado en Python 3.


Modelo de procesos.
Un proceso se modela como una lista con la siguiente estructura:
[<tiempo de llegada>,<tiempo requerido>,<nombre(caracter)>,
 <tiempo de respuesta>,<tiempo en espera>,<proporcion de penalizacion>,
 <diagrama de ejecucion>]

La función 'generarProcesos' genera una lista de listas (procesos) a partir de una lista vacía. Genera un total de n procesos. Cada tick, hay una probabilidad de 1/p de que llegue un proceso con un tiempo requerido de entre tmin y tmax. T, E y P se inicializan como 0 y el diagrama de ejecucion como una cadena vacía. La lista está ordenada por tiempo de llegada. 

Funciones de uso general:
    'resultados' recibe una lista de procesos cuyo tiempo de ejecucion ya fue agregado y calcula E y P.
    'promedios' recibe una lista de procesos y una lista inicializada como [0,0,0], y en la última calcula los promedios de T, E y P.
    tabla(): recibe una lista de procesos que pasó por un algoritmo de planificacion y el nombre del algoritmo, y muestra la tabla (Nombre del proceso, Inicio, Fin, tiempo, T, E, P y promedios de T,E y P).
    'diagrama' recibe una lista de procesos y muestra el diagrama de ejecucion de cada uno. 

Algoritmos de planificacion:
*Todos los algoritmos reciben una lista de procesos inicializada con generarProcesos(), pero trabajan sobre una copia de la lista 'tareas_ent'. La lista inicializada no es afectada.

**Para cada algoritmo, en cada tick, se realiza lo siguiente:
- Si es el tiempo de llegada de un proceso en tareas_ent, se agrega (a como decida el algoritmo) una copia del proceso a la cola de tareas. 
- El proceso que se ejecuta decrementa 1 de su tiempo requerido. Ademas, agrega su propio nombre (letra) a su diagrama de ejecucion.
- Los procesos en la cola que no se vayan a ejecutar agregan '-' a su diagrama de ejecucion.
- Todos los procesos en la cola incrementan en 1 su tiempo de espera.
- Si el proceso que se ejecuta termina con un tiempo requerido de 0, se saca de la cola. Su tiempo de espera y el diagrama de ejecucion se pasan a su contraparte en tareas_ent.
- Si no hay procesos en la cola, agrega i (de 'idle') al esquema. 


FCFS:
Los procesos entrantes se encolan al final la lista tareas_cola. Un proceso permanece al principio de la cola hasta que termine de ejecutarse. 

RRN:
Ademas de la lista de procesos, recibe como parámetro la duración deseada del quantum 'n'. Se inicializa a la variable quantum como n. Los procesos entrantes se ponen al final de tareas_cola. Cada tick que ejecute un proceso, se decrementa 1 del quantum. Si al final de la ejecucion quantum es 0, se almacena el proceso en la variable encolar. Se vuelve a poner en tareas_cola en el siguiente tick, despues del posible proceso que haya llegado en ese mismo tick.

SPN:
Al momento de encolar un proceso entrante, compara con los procesos de la cola sus tiempos requeridos, y se coloca en orden ascendente. En caso de ser el más corto, solo se pondrá hasta el frente si el proceso que actualmente está enfrente no ha empezado a ejecutarse (se utiliza la variable ejec para validar esto). Un proceso en ejecución no es interrumpido hasta que termina y sale de la cola. 
 
SRR:
Ademas de la lista de procesos, se recibe a y b como parametro. Aprovechando que en la estructura de procesos se tienen espacios no utilizados durante la ejecucion, se almacena en proceso[4] el valor de a o b. Se inicializa en a. Se tiene la lista de listas tareas_colas. Si no hay procesos en ejecución, tareas_colas está vacía. Cuando hay un proceso entrante, se almacena en la lista  tareas_colas[0] (de no existir, se crea). Se ejecuta el primer proceso de la lista con mayor prioridad (la última de tareas_colas). Desde su primer ejecucion, se cambia el valor de p[4] por b. Si no ha terminado, se vuelve a poner en la misma lista. Después, iterando tareas_colas de mayor a menor prioridad, se mueven todos los procesos a la lista con a ó b más prioridad que la actual (de no existir, se crean). Finalmente, se borran las listas de tareas_colas que ya no son necesarias (las ultimas vacías).













