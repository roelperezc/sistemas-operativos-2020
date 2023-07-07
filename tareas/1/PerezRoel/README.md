# EJERCICIOS DE SINCRONIZACION
## Ejercicio: Los alumnos y el asesor
### por Roel Perez

El programa fue realizado en Python. El comando para ejecutarlo es: 
 $python3 tarea1.py

Para esta simulación, se corre un hilo para el asesor y un hilo para cada estudiante. El hilo del estudiante tiene un id y un número de preguntas 'preguntas_totales' aleatorio entre 1 y y. Los hilos entre estudiante y asesor se comunican a través de la lista 'Q'. Cuando un estudiante hace una pregunta, pone su id y el numero de su pregunta en la lista Q. El asesor debe tomar el id y el numero de pregunta de la lista Q y 'responderla' (se imprime lo que obtuvo de la lista). La limitación es que, por diseño, la lista solo puede tener una pregunta a la vez ("sólo un alumno puede presentar su duda (y esperar su respuesta) al mismo tiempo. Los demás alumnos sentados deben esperar pacientemente su turno").

Para este problema fue necesaria una combinación de multiplex, mutex, y señalización. 

El multiplex se utiliza para asegurarse de que en todo momento solo hay x alumnos en el cubículo, por lo que se crea un semaforo 'cubiculo' inicializado en x. El alumno lo adquiere una vez inicializado su numero de preguntas y lo libera despues de que todas han sido resueltas. 

Los demás semáforos se utilizan para resolver la comunicación entre alumnos y asesor, similar al problema de productores y consumidores. 

El semaforo 'atencion' es un mutex que se utiliza para proteger a Q de las modificaciones por alumnos y asesor. 

El semaforo 'preguntas', inicializado en 0, sirve para que un alumno señalice al asesor que una pregunta ha sido hecha. Es el primer semaforo que adquiere el asesor (él espera a que haya una pregunta antes de hacer cualquier cosa). El alumno lo libera una vez ha agregado su pregunta a Q. 

El semáforo 'capacidad', inicializado en 1, se refiere a la capacidad de atención del profesor. Es el que se encarga de verificar que siempre hay solo una pregunta en Q (si el profesor fuera capaz de retener n preguntas al mismo tiempo antes de responderlas, el semaforo se inicializaria en n). El alumno adquiere este semáforo antes de hacer una pregunta (incluso antes de adquirir el mutex 'atencion'). El asesor lo libera cuando ya terminó de responder la pregunta. Es una especie de mutex, pero la 'sección crítica' que protege es todo el proceso de pregunta y respuesta. 

Cosas a destacar: 
- El hilo del asesor está dentro de un "while(true)", pero no es espera activa; está esperando a que alguien libere el semáforo 'preguntas' (El programa indica que "El asesor espera..."). Para efectos prácticos, esperar a una pregunta y dormirse cuando no hay nadie es lo mismo. Siguiendo la lógica de hilos, el asesor duerme entre pregunta y pregunta, incluso cuando hay alumnos en el cubículo. Por esta razón, es necesario cortar hilo del asesor para salir. 

- Si un alumno hace una pregunta, esa debe ser la siguiente que responda el asesor, y no se pueden hacer más preguntas entre esos dos eventos. Sin embargo, es posible que alumnos entren o salgan del cubículo en ese lapso de tiempo. 


