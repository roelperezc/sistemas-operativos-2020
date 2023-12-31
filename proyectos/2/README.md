# Proyecto 2. 
Roel Pérez.

## Desarrollado en:
Python 3.6.8
Linux Mint 19
Para la interfaz de usuario se utilizó el módulo curses (ya incluido en Python).

Para arrancar el programa, ejecuta interfaz.py en la terminal con el comando: 'python3 interfaz.py', en el mismo directorio que config.py y proyecto2.py


## Instrucciones para el usuario.

Al ejecutar interfaz.py en la terminal, se observará un menú. Desde este, si se presiona '1' se podrá ver una descripción del problema planteado; si se presiona '2' se lanzarán los hilos de ejecución y se mostrará una visualización de cómo avanzan los procesos, así como un apoyo textual. Para de cualquiera de éstos dos, presiona 's' para volver al menú principal. Para terminar el programa, presiona 'q' desde el menú principal.

NOTA: Salir al menú una vez iniciada la ejecución detendrá el lanzamiento de los hilos de cortesanos, pero continuará la ejecución de los hilos que se estén ejecutando. No es recomendable salir y volver a entrar a la ejecución; es mejor terminar el programa y volver a ejecutarlo. 


# DOCUMENTACIÓN

En el archivo descripcionDelProblema.txt se describe a fondo el problema planteado.

## Mecanismos de sincronización
Para este problema, se utilizaron semáforos (s) y variables de condición (vc). Éstos y los métodos de los hilos se encuentran en proyecto2.py, y se describen a continuación:

reyPresente (vc). Booleano que indica que el rey está presente en la sala.

bufonSentado (vc). Booleano que indica que el bufón está sentado.

cortesanosPasados (vc). Entero que cuenta los cortesanos que han pasado por la sala mientras el bufón no está sentado. 

cortesanosEsperando (vc). Entero que cuenta los cortesanos que están afuera esperando en la puerta. 

presentes (vc). Entero que cuenta a los presentes en la sala, cortesanos o rey, sin incluir al bufón.

debeLevantarse (s). Es una señalización que le indica al bufón cuándo debe levantarse del trono. 

puedeSentarse (s). Es una especie de apagador (apagador cuando lo usa el rey y el cortesano; señalizador cuando lo usa el bufón) que se prende y apaga por el primero y el último en entrar, respectivamente (ya sea cortesano o el rey), pero únicamente si el bufón no está sentado. También lo prende y apaga el bufón al sentarse y levantarse. 

puertaCortesanos (s). Es un torniquete para los cortesanos. Se puede cerrar o abrir dependiendo de cuántos cortesanos han pasado o cuántos cortesanos hay esperando, respectivamente.

bufonSeSienta (s). Es una señalización del bufón que indica a un actor (ya sea cortesano o rey) que el bufón se ha sentado luego de haber señalizado puedeSentarse.

mutex (s). Es un mutex que protege todas las variables de condición. 

También se tiene a los booleanos puertaCerrada y reySentado (auto explicativos) que no son requeridos para resolver problemas de concurrencia, pero son utilizados para visualizar el escenario en la interfaz.


## Descripción de los hilos y su interacción. 

(léase varias veces para entender bien la interacción)
Nota: Cada escritura o lectura de una variable de condición se hace dentro del semáforo mutex (aún si no se nota directamente)
Nota 2: Utilizaré los términos "adquirir" y "liberar" para referirme a las operaciones de los semáforos.

rey: El hilo del rey se encuentra dentro de un ciclo y una condición tal que cada segundo, hay una probabilidad de 1/p de que el rey entre a la sala.
Cuando el rey entra, reyPresente se vuelve verdadero y presentes incrementa en 1. Si el rey es el primero en entrar y el bufón no está sentado, adquiere el "apagador" puedeSentarse (es un caso particular de inicio, si el rey llega antes que el bufón). Si el bufón está sentado, liberará debeLevantarse para señalizar al bufón, y adquirirá puedeSentarse, esperando a que el bufón se levante y le ceda puedeSentarse.
El rey se sentará por unos segundos. 
Al terminar, decrementará presentes en 1 y hará falso reyPresente. Si al retirarse es el último (presente = 0), y ya que tenemos garantizado en este caso que el bufón no está sentado, liberará puedeSentarse y adquirirá bufonSeSienta, con el que espera a que el bufón termine de sentarse para continuar.

bufon: El hilo del bufón se ejecuta en un ciclo infinito.
Al iniciar, empezará la cuenta de cortesanosPasados en 0. Se contemplan dos posibilidades al llegar a este punto: 1. Ningún otro actor a adquirido puedeSentarse() (caso particular de inicio, en el que bufón es el primero en llegar), por lo que el bufón puede pasar a sentarse, 2. Alguien tiene adquirido puedeSentarse() y bufón tiene que esperar a que un actor le señalice. En el primer caso, el bufón tiene que mantenerse dentro del mutex para cambiar bufonSentado a verdadero. En el segundo caso, ya que se sabe que alguien dentro de un mutex le señalizará que puede sentarse, tendrá adquirir puedeSentarse afuera de su propio mutex; cambiará el estado de bufonSentado a verdadero y liberará bufónSentado para indicarle al actor que ha terminado.  
Adquirirá debeLevantarse, esperando a que alguien le señalice. 
Nuevamente, ya que se sabe que se le señalizará debeLevantarse dentro del mutex de otro actor, no es necesario proteger las variables dentro de otro mutex. Si hay M cortesanos esperando, liberará el torniquete puertaCortesanos.

cortesano: Existen múltiples hilos de cortesanos que corren concurrentemente, iniciando aleatoriamente en el tiempo. Estos son lanzados por el hilo de llegadaCortesanos.
Al iniciar incrementa en uno cortesanosEsperando. Si hay M cortesanos esperando, y la puerta está cerrada: 1. Si el bufón está sentado, se le libera debeLevantarse para señalizar al bufón (éste se encargará de abrir el torniquete puertaCortesanos) y se adquiere puedeSentarse para esperar a que termine, 2. Si el bufón no está sentado, solamente se reinicia la cuenta de cortesanosPasados y se libera el torniquete puertaCortesanos. Después, se pasa por el torniquete (se adquiere y libera puertaCortesanos de forma seguida).
Pasado el torniquete, se incrementa en 1 a presentes y cortesanosPasados, mientras se decrementa cortesanosEsperando. Si es el primero en entrar (presentes = 1), el bufón no está sentado y no hay una fila de cortesanos, se adquiere a puedeSentarse (caso particular en el que el cortesano es el primero en llegar). Si para este punto la cuenta de cortesanosPasados ya llego a N y el bufón no está sentado, se adquirirá puertaCortesanos para cerrar el torniquete.
El cortesano está unos segundos en la sala.
Al terminar, el cortesano decrementa en 1 a presentes. Si es el último en salir y el bufón no está sentado, libera puedeSentarse y adquiere bufonSeSienta para esperar al bufón.

llegadaCortesanos: Es un hilo que controla el lanzamiento de los hilos de cortesano. Se ejecuta dentro de un ciclo infinito en el que se pasa por un torniquete que se puede abrir y cerrar si se sale o entra del menú. Cada segundo, hay cierta probabilidad de que se lance un hilo de cortesano.

actualizarPantalla: se describe en la siguiente sección.


# Funcionamiento de la interfaz de usuario y la sincronización con los actores del problema:

La interfaz de usuario se encuentra en interfaz.py. Consiste del método menu. Este utiliza el módulo curses para mostrar un menú, desde el que se puede seleccionar leer el problema o lanzar a los hilos y observar la simulación.

Para manejar la interfaz de usuario, se utilizaron semáforos y variables. No deben confundirse con los semáforos descritos anteriormente, utilizados para resolver el problema propuesto; estos semáforos sirven únicamente para comunicar a los actores con la interfaz. Los semáforos y variables se encuentran en config.py y son los siguientes:

sigHilos y sigInterfaz: Son señalizadores entre los actores y la interfaz. sigHilos es adquirido por la interfaz y espera a que un actor le señalice que debe hacer una actualización a la pantalla. sigInterfaz es adquirido por un actor después de liberar sigHilos, y es liberado por la interfaz una vez que realizó la actualización de la pantalla.

pausa: Es un torniquete para el hilo llegadaCortesanos, para evitar que lleguen cortesanos cuando se sale al menú una vez inicializada la ejecución de los hilos. Sin embargo, no se detendrán a los hilos que ya estén corriendo (el rey, el bufón, y los cortesanos que ya hayan llegado). Su utilidad es cuestionable, pues de todas formas no es recomendable salir y volver a entrar a la ejecución.

grafico: Es una lista de 8 cadenas. Las siete primeras sirven para describir visualmente el estado actual del escenario, mientras la octava tiene la descripción textual del evento más reciente. 

En proyecto2.py se agrega el siguiente método:

actualizarPantalla: Método llamado por los actores para indicarle a la interfaz que debe actualizar la pantalla. Se revisa el estado de las variables puertaCerrada, reyPresente, reySentado, bufonSentado y presentes para decidir cómo se debe mostrar a las cadenas de la lista grafico. Al terminar, se libera a sigHilos para señalizar a la interfaz que debe actualizarse, y se adquiere a sigInterfaz para esperar a que termine de actualizar la pantalla. Cabe destacar que actualizarPantalla siempre es llamado por los actores dentro de un mutex, lo que asegura la integridad de las variables.

 





