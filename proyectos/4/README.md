# Proyecto 4. (Micro) sistema de archivos
Roel Perez
Elaborado en Python 3.6.8

Para iniciar el programa, ejecute en consola:
$ python3 FiUnamFS.py <virt_disk>

donde <virt_disk> es la imagen del disco que cumple las especificación de FiUnamFS. 

Comandos:
    h		        Muestra las funcionalidades del programa.
	l		        Lista el directorio del FiUnamFS.
	imp <archivo>	Importa <archivo> de tu sistema hacia el FiUnamFS.
	exp <archivo>	Exporta <archivo> del FiUnamFS hacia tu sistema.
	del <archivo>	Elimina <archivo> del FiUnamFS.
	df		        Desfragmenta FiUnamFS.
	s		        Salir.



FiUnamFS.py

# Documentación

Clase EntradaDir
    La clase EntradaDir describe cada entrada del directorio de un sistema de archivos FiUnamFS. Incluye toda su información: nombre, tamanio, cluster inicial, fecha de creación, fecha de última modificación, y un atributo para el espacio no utilizado (reservado). Estos atributos se deben guardar como cadenas o enteros. 


## Estructuras y Datos

    cmd: Es una lista de cadenas. Cada vez que el usuario ingrese un comando, se partirá esa cadena en los espacios, y cada una de las cadenas resultantes se almacenará en cmd.

    bitmap: Es una lista de booleanos. Cada elemento representa un cluster del disco, y es True o False si el cluster está disponible u ocupado, respectivamente.

    fs: Es el archivo del disco abierto en modo de lectura y escritura de bytes. 


## Funciones
   
   ayuda: Muestra en consola comandos que puedes utilizar en esta implementación del FiUnamFS.  

    obtenerDirectorio: Devuelve una lista de EntradaDir que contiene las entradas no vacías del directorio. La lista se encuentra en el orden en que fueron encontradas las entradas. 

    obtenerTamanio: Devuelve una cadena que expresa un tamaño de archivo en bytes (o con el prefijo de bytes más conveniente: 'KB', 'MB', 'GB', etc.) 

    obtenerFecha:  Recibe una cadena con una cadena en formato 'yyyymmddhhmiss'. Devuelve una cadena con la fecha en formato 'dd/mm/yyyy hh:mi:ss'.

    listarDirectorio: Recibe la lista que se devuelve obtenerDirectorio. Muestra en consola una lista de los archivos del directorio con sus datos: nombre, tamaño, fecha de creación y fecha de modificación.

    generarBitmap: Recibe al bitmap y el directorio. Con el cluster inicial y el tamaño de cada entrada del directorio, marca los respectivos clusters del bitmap como ocupados.

    tamanioEnClusters: Recibe un entero que representa un tamaño de archivo en bytes. Devuelve el número de clusters necesarios para alojar ese tamaño de bytes.

    importar: Recibe cmd y fs. Mueve el archivo del sistema seleccionado por el usuario (cmd[1]) hacia el FiUnamFS.
    Solo puede moverse si el nombre del archivo seleccionado no se encuentra ya en el directorio de FiUnamFS, es menor a 15 caracteres, de los cuales todos pertenecen a ASCII.
    Fallará si no existe un espacio contiguo suficiente para albergar a tu archivo, o si ya no hay espacio en el directorio. 

    generarEntrada: Recibe la ruta de un archivo y genera un objeto EntradaDir, al que le asigna su nombre, tamaño, fecha de creación y fecha de última modificación, en el formato de la especificación.

    fechaEnFormato: Recibe un número flotante que representa el número de segundos desde el tiempo epoch y devuelve la fecha que representa en el formato de la especificación.

    buscarBloquesContiguos: Recibe un número de clusters. Busca secuencialmente en el bitmap a esta cantidad contigua de clusters disponibles. Cuando la encuentra, los marca a todos como ocupados y devuelve el índice de la primera. Devuelve -1 si no encuentra los bloques contiguos.

    buscarEntradaDirLibre: Recibe fs. Busca secuencialmente entradas libres en el directorio. Devuelve el índice de la primera entrada libre que encuentre, o -1 si no encuentra.

    escribirEntrada: recibe fs, una entrada (EntradaDir) y el índice de una entrada del directorio. Escribirá los datos de la entrada en el correspondiente lugar del directorio.
 
    exportar: Recibe cmd, fs y a la lista obtenida de obtenerDirectorio. Exporta un archivo del FiUnamFS hacia el sistema del usuario.
    El archivo se genera en el directorio donde se esté ejecutando el programa, sobrescribiendose si es que ya existe, y creandose en caso contrario.
    La función termina si el archivo seleccionado por el usuario (cmd[1]) no se encuentra en el directorio.

    buscarArchivoDirVirtual: Recibe una cadena que representa el nombre de un archivo y la lista del directorio. Si existe un archivo en el directorio con el nombre igual a la cadena, se devuelve el índice en directorio de éste archivo, o -1 en caso de no encontrarse.

    buscarArchivoDirReal: Recibe una cadena que representa el nombre de un archivo y a fs. Buscará en el área del directorio el archivo cuyo nombre coincida con el que se busca. Devolverá el índice en el directorio real, o -1 en caso de no encontrarlo.


    stringMenosEspacios: Recibe una cadena. Devuelve la misma cadena pero sin los espacios. 

    eliminar: Elimina de forma floja un archivo del FiUnamFS. Marca su espacio en directorio como disponible y sus clusters en el bitmap como libres.
     









    
