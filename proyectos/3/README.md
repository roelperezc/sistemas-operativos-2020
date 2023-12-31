# Proyecto 3
Roel Perez
Desarrollado en Python 3.

El programa se encuentra en proyecto3.py. Para arrancarlo, ejecuta el comando 'python3 proyecto3.py <PID>'. <PID> es el ID de un proceso del cual se desea ver su mapa de memoria.

## Documentación

Clase BloqueMemoria
    La clase BloqueMemoria define a una entrada del archivo /proc/<PID>/maps. Los atributos de una instancia son los correspondientes a la información que puedes obtener del archivo: dirección inicial, dirección final, permisos, offset, device, inode, y pathname. Además, se definen otros atributos que son obtenidos a partir de los anteriores: uso, página inicial, página final, número de páginas y el tamaño del bloque. 
El método procesarDatos() calcula estos datos a partir de los obtenidos del archivo. Asume que el tamaño de página es de 4 KB. Si el pathname corresponde a un directorio, se determinará si es de Texto, de Datos o de Reserva: el texto tiene permiso de ejecución, los datos tienen permiso de lectura o escritura, y las reservas (que son espacios en memoria reservados por motivos de rendimiento) no tienen permiso de escritura/lectura/ejecución. Si el pathname es heap o stack, se mantiene ese nombre. Otros se renombran: vsyscall y vdso se renombran a Sys. Calls, al ser donde se ubican las llamadas a sistema; vvar se renombra a Kernel Vars., al ser donde se guardan variables del kernel. Si el nombre del pathname no es identificado, al uso se le asignará el mismo.
    Todos los atributos son cadenas, excepto numPaginas, el cual es un número.
    El método obtenerTamanio calcula el tamaño de un bloque de memoria dado su numero de páginas.


El método obtenerBloques recibe el PID y la lista bloques, en la que guardara los bloques de memoria. Lee el archivo /proc/<PID>/maps, crea una instancia de BloqueMemoria para cada entrada y la guarda en bloques. 


El método generarMemoria recibe la lista bloques obtenida con obtenerBloques, y la lista memoria, donde ordenará el mapa de memoria. Empezando con un contador de memoria en la dirección 0, recorre la lista bloques. Si el siguiente bloque que debe añadir tiene una dirección inicial mayor que el contador de memoria, entonces debe crear un bloque vacio e insertarlo antes. La direccion inicial del bloque vacio es el contador de memoria y la direccion final es la inicial del siguiente bloque. Los bloques vacios no tienen permisos, offset, device ni inode. Al termino de la iteración, al contador de memoria se le asigna la dirección final del bloque que se insertó.
En este método también se redefine el uso de un bloque de memoria. Si un bloque de texto/datos se encuentra después del Heap, se le considera una biblioteca de texto/datos.
Ya que el heap puede estar de forma explícita o implícita, hay dos formas de reconocer que ya se pasó el heap: en la explícita, solo se verifica si el uso del bloque es 'Heap'. En la implícita, se considera que se pasó el heap si se llega a un bloque que tenga un pathname diferente al primer pathname encontrado (el primero se considera el programa base, mientras los que le siguen son bibliotecas).
  

El método mostrarMemoria recibe la lista memoria generada con generarMemoria y la muestra con un formato. Se utilizan secuencias de escape ANSI para mostrar algunos elementos a color. 








