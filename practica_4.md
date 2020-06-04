# Práctica 4

## Requisitos

_Para poder realizar la siguiente práctica es necesario tener instalado un sistema operativo Linux con un espacio libre de 10GB como mínimo. Además, se deberán instalar los paquetes necesarios para poder generar el RAID y LVM._

Obs. 1: al final de la práctica se encuentran algunos links de referencia para las siguientes preguntas

## File Systems

1. ¿Qué es un file-system?

    Es la parte del sistema operativo que se encarga del manejo de archivos. Es una abstracción que permite la creación, eliminación, modificación, búsqueda y organización de archivos en directorios. También administra el control de acceso a los archivos y el espacio en disco asignado a él. Los filesystems operan sobre bloques de datos, es decir, conjuntos de consecutivos de sectores físicos. La estructura por excelencia utilizada para el almacenamiento es jerárquica de tipo árbol. Además, definen convenciones para el nombrado de los archivos. Lo cierto es que hay muchos tipos de ellos, los hay en CDs e, incluso, de acceso de red(NFS o SMB) y virtuales(procfs o sysfs).

2. Describa las principales diferencias y similitudes entre los file systems: FAT, NTFS, Ext(2,3,4), XFS y HFS+.

    Sistema de archivos | Longitud máxima del nombre de archivo | Caracteres permitidos en las entradas de directorio | Longitud máxima de la ruta de acceso | Tamaño máximo de archivo | Tamaño máximo de volumen | *Journaling* | *Case sensitive* | Extensible
    :---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:
    exFAT | 255 caracteres | Cualquier Unicode excepto NUL | No hay límite definido | 127 PiB | 64 ZiB, 512 TiB recomendado | No | No | No
    ext2 | 255 bytes | Cualquier byte excepto NUL​ y / | No hay límite definido | 2 TiB | 32 TiB | No | Si | No
    ext3 | 255 bytes | Cualquier byte excepto NUL​ y / | No hay límite definido | 2 TiB | 32 TiB | Si | Si | No
    ext4 | 255 bytes | Cualquier byte excepto NUL​ y / | No hay límite definido | 16 TiB | 32 TiB EiB | Si | Si | Si
    FAT12 | 8.3 (255 UTF-16 code units with LFN) | Cualquier byte excepto valores del 0-31, 127 (DEL) y: " * / : < > ? \ \| + , . ; = [] (lowcase a-z are stored as A-Z). Con VFAT LFN Cualquier Unicode excepto NUL | No hay límite definido | 32 MiB (256 MiB) | 32 MiB (256 MiB) | No | No | No
    FAT16 | 8.3 (255 UTF-16 code units with LFN) | Cualquier byte excepto valores del 0-31, 127 (DEL) y: " * / : < > ? \ \| + , . ; = [] (minúsculas a-z son guardados como A-Z). Con VFAT LFN Cualquier Unicode excepto NUL | No hay límite definido | 2 GiB (4 GiB) | 2 GiB or 4 GiB | No | No | No
    FAT32 | 8.3 (255 UTF-16 code units with LFN) | Cualquier byte excepto valores del 0-31, 127 (DEL) y: " * / : < > ? \ \| + , . ; = [] (lowcase a-z are stored as A-Z). Con VFAT LFN Cualquier Unicode excepto NUL | No hay límite definido | 4 GiB (256 GiB) | 2 TiB (16 TB) | No | No | No
    HFS+ | 255 UTF-16 code units | Cualquier Unicode válido | Sin límite | 8 EiB | 8 EiB | No | Si, pero no es recomendable | Si
    NTFS | 255 caracteres | Depende del espacio de nombre utilizado | No hay límite definido | 4 GiB | 1 TiB | No | Si | Si
    XFS | 255 bytes | Cualquier byte excepto NUL | No hay límite definido | 16 EiB | 16 EiB | Si | Si | Si

3. En ext4, describa las siguientes características: extents, multiblock allocation, delay allocation y persistent pre-allocation (<https://kernelnewbies.org/Ext4>).

    * Extensiones: una extensión es un montón de bloques físicos contiguos. En criollo, es algo así como que los datos están en los próximos n bloques. Por ejemplo, se puede asignar un archivo de 100 MB en una sola extensión de ese tamaño, en lugar de tener que crear la asignación indirecta para 25600 bloques (4 KB por bloque). Los archivos enormes se dividen en varias extensiones. Las extensiones mejoran el rendimiento y también ayudan a reducir la fragmentación, ya que en cierta medida fomenta diseños contiguos en el disco. También agilizan operaciones como las de elminación y truncamiento.
    * Asignación multibloque: es decir, asignar muchos bloques en una llamada, en lugar de un solo bloque por llamada, de modo que se evita una sobrecarga. Esto mejora el rendimiento, y es particularmente útil con asignaciones retrasadas y extensiones. Esta característica no afecta el formato del disco.
    * Delay allocation: la asignación retrasada es una característica de rendimiento (no cambia el formato del disco) que consiste en retrasar la asignación de bloques tanto como sea posible, al contrario de lo que tradicionalmente, los sistemas de archivos hacen: asignar los bloques lo antes posible. A diferencia del enfoque tradicional, la asignación retrasada no asigna los bloques inmediatamente cuando el proceso escribe, sino que retrasa su asignación mientras el archivo se mantiene en caché, hasta que realmente se va a escribir en el disco. Este sistema ofrece una gran ventaja al asignador de bloques en cuanto a optimización. La asignación retrasada juega muy bien con las dos características anteriores mencionadas, extensiones y asignación multibloque, porque en muchas ocasiones cuando el archivo se escribe finalmente en el disco, se asignará en extensiones cuya asignación de bloque se realiza con el asignador multibloque. El rendimiento aumenta y la fragmentación mejora mucho en varios casos.
    * Preasignacion persistente: esta característica permite que las aplicaciones preasignen espacio en disco: las aplicaciones le dicen al sistema de archivos que preasigne el espacio, y el sistema de archivos preasigna los bloques necesarios y estructuras de datos, pero no hay datos hasta que la aplicación realmente necesite escribir los datos en el futuro. Esto tiene varios usos: primero, evita que algunas aplicaciones (como las P2P) lo hagan de manera ineficiente al llenar un archivo con ceros. En segundo lugar, para mejorar la fragmentación, ya que los bloques se asignarán a la vez, de la forma más contigua posible. Tercero, para garantizar que las aplicaciones siempre tengan el espacio que saben que necesitarán, ya que sin una asignación previa el sistema de archivos podría llenarse en medio de una operación importante.

4. ¿Es necesario tener un file system para acceder a una partición?

    Si, ya que el filesystem es lo que determina cómo se organizarán los archivos en esa partición.//CONSULTAR

5. ¿Qué es el área de swap en Linux? ¿Existe un área similar en Windows?

    El swap es un espacio de intercambio, que bien puede ser una partición lógica en el disco o simplemente un archivo. En lugar de utilizar espacio en memoria RAM, el swap utiliza espacio en disco duro para almacenar datos temporales, reduciendo así el uso de la RAM. El conjunto combinado de memoria RAM y swap crean una memoria virtual mayor a la que trae la computadora por defecto. Así, el Kernel de Linux puede ejecutar procesos que requieren de más memoria de la que se encuentra físicamente disponible.

    Microsoft Windows usa un fichero de intercambio desde su versión 3.1 (1992), la primera en usar memoria virtual. Lo implementa mediante un fichero situado en el directorio raíz _(C:\)_ o en el de sistema _(C:\WINDOWS\)_, y tiene por nombre:

    * 386SPART.PAR en Windows 3.1.
    * WIN386.SWP en Windows 3.11, 95 y 98.
    * pagefile.sys en Windows NT y sucesores.

    Este fichero tiene un tamaño variable (depende de la configuración) y no debe ser movido o borrado.

6. ¿Qué función cumple el directorio lost+found en Linux?

    Es un directorio al que van a parar los archivos que usan espacio pero que no son accesibles por medios normales cuando se invoca el ```fsck``` para hacer reparaciones o chequeos en el filesystem. Los archivos que van a este directorio por lo general son archivos deslinkeados(su nombre fue borrado, por ejemplo) pero que algún proceso los estaba usando cuando el sistema se paró (porque ocurrió un kernel panic o un corte de luz, por ejemplo). En ese caso, esos archivos no son importantes probablemente fueran borrados más adelante. Sin embargo, puede darse que los archivos que vayan a parar ahí sea por un estado inconsistente por un bug en el hardware o en el software. Esos archivos pueden estar completos o no, pueden contener algo útil o no, podrían estar actualizados o no, todo depende de qué tan grave haya sido el daño al filesystem.

7. En Linux ¿Dónde se almacena el nombre y los metadatos de los archivos?

    El nombre en la estructura del directorio y los metadatos en los inodos.

8. Seleccione una de sus particiones y conteste usando el comando dumpe2fs (si está usando la MV de la cátedra, como root, ```dumpe2fs /dev/vda1```):

    NOTA DE LIHUEL: si invocas el comando ```dumpe2fs /dev/vda1``` y el comando no es encontrado, lo más seguro es que esté en _sbin_, por lo que para invocarlo vas a tener que tipear ```/sbin/dumpe2fs /dev/vda1```. Otro detalle: puede ser que el dispositivo **vda1** tampoco exista. En ese caso, hay que listar los _filesystems_ y elegir el /. Todo eso se hace con el comando ```lsblk```(listar los dispositivos de bloque) y eligiendo el dispositivo que represente a /. En mi caso, ese dispositivo está representado por **/dev/sda1**. En resumen, el comando final que me quedó es ```/sbin/dumpe2fs /dev/sda1```.

    * ¿Qué información muestra el comando dumpe2fs?

        Muestra la información de los sistemas de archivos ext2/ext3/ext4. Imprime el super bloque y la información de los grupos de bloque para el sistema de archivos presente en el dispositivo.

    * ¿Cuál es el tamaño de bloque del file system?

        4096\.

    * ¿Cuántos inodos en total contiene el file system? ¿Cuántos archivos más se podrían crear con el estado actual del file system?

        El filesystem contiene 2.445.984 inodos. Se pueden crear 2.321.053 archivos más.

    * ¿Cuántos grupos de bloques existen?

        298 grupos.

    * ¿Cómo haría para incrementar la cantidad de inodos de un file system?

        Será necesario extender el filesystem.

9. ¿Qué es el file system procfs? ¿Y el sysfs?

    ProcFS es un pseudo-filesystem montado comunmente en el directorio /proc. Provee una interface a las estructuras de datos del kernel. Presenta información sobre procesos y otra información del sistema en una estructura jerárquica de files. No existe en disco, el kernel lo create en memoria (generalmente tamaño 0 de los files).

    Con el paso del tiempo, /proc se convirtió en un verdadero desorden. Para reemplazarlo, en Linux 2.6 se implementó un nuevo sistema de archivos virtual que exporta información sobre varios subsistemas del kernel, dispositivos de hardware y sus drivers, módulos cargados, etc. desde el espacio del kernel hacia el espacio del usuario llamado Sysfs. Este sistema, además, permite la conguración de parámetros y se monta en /sys.

10. Consultando el sysfs ¿Cuál es el tamaño del sector lógico de su disco? ¿Y el dísco?

    Tanto el tamaño del sector lógico como físico es 512.

    NOTA DE LIHUEL: para saberlo, basta tipear ```cat /sys/block/sda/queue/logical_block_size``` y ```cat /sys/block/sda/queue/physical_block_size``` para el tamaño del sector lógico y físico, respectivamente.

11. Usando el directorio /proc, contestar:

    * ¿Cuál es la versión de SO que tiene instalado?

        NOTA DE LIHUEL: para saber la versión del sistema operativo usando el /proc, tipear ```cat /proc/version```.

        Linux version 5.6.2 (so@so2020) (gcc version 8.3.0 (Debian 8.3.0-6)) #3 SMP Thu Apr 23 19:54:25 -03 2020.

    * ¿Cuál es procesador de su máquina?

        NOTA DE LIHUEL: para conocer la información relativa al procesador, tipear ```cat /proc/cpuinfo```. El campo que yo contemplé es el del _model name_.

        Intel(R) Core(TM) i5-8300H CPU @ 2.30GHz.

    * ¿Cuánta memoria RAM disponible tiene?

        NOTA DE LIHUEL: para conocer la información relativa a la memoria, tipear ```cat /proc/meminfo```. El campo que yo contemplé es el del _MemFree_.

        794988 kB.

    * ¿Qué archivo debería consultar si se quiere ver el mismo resultado que el comando lsmod?

        Debería consultar _/proc/modules_.

12. Usando el comando stat, contestar:

    * ¿Cuándo fue la última vez que se modificó el archivo /etc/group?

        NOTA DE LIHUEL: utilizar ```stat /etc/group``` y mirar la parte de _Modificación_.

        2020-03-14 17:34:48.500000000 -0300.

    * ¿Cuál es la diferencia entre los datos Cambio (Change) y Modificación (Modify)?

        "Modify" es el timestamp de la última vez que el contenido del archivo se ha modificado. Esto a menudo se llama "mtime". "Change" es el timestamp de la última vez que se cambió el inodo del archivo, por ejemplo, modificando los permisos, el ownership, el file name o el número of hard links.

    * ¿Cuál es el inodo que ocupa? ¿Cuántos bloques ocupa?

        NOTA DE LIHUEL: utilizar ```stat /etc/group``` y mirar la parte de _Nodo-i_ y _Bloques_, respectivamente.

        Ocupa el inodo 1313628 y 8 bloques.

    * ¿Qué número de inodo ocupa el directorio raíz?

        NOTA DE LIHUEL: utilizar ```stat /etc``` y mirar la parte de _Nodo-i_.

        Ocupa el inodo 1313281.

    * ¿Es posible conocer la fecha de creación de un file en ext4? ¿Por qué?

        No se puede porque no es algo que guarda el inodo, y es una de las cosas que se le critica a ext4.

13. Los permisos por defecto de Linux al crear un archivo o directorio son 666 y 777 respectivamente. Cree un nuevo archivo y analice sus permisos. ¿Es así? ¿Por qué sucede esto?

    Si, efectivamente los permisos por defecto son 666 para archivos y 777 para directorios. La razón es que es el valor que está en /etc/profile, desde ahí se pueden editar esos valores.//PREGUNTAR

14. ¿Qué es un link simbólico? ¿En qué se diferencia de un hard-link?

    Un link simbólico es un link real al archivo original, mientras que un hard-link es una copia espejo del archivo original.

15. Si se tiene un archivo llamado prueba.txt y se le genera un link simbólico ¿Qué sucede con el link simbólico si se elimina el archivo prueba.txt? ¿Y si el link fuese hard-link?

    Si yo borro prueba.txt, el link simbólico no tiene valor, porque apunta a un archivo que no existe. Sin embargo, en el caso del hard-link, lo cierto es que ocurre lo opuesto. Incluso si yo borrara el archivo original, el hard-link tendría la data del archivo original. Esto es porque precisamente este actúa como una copia espejo del archivo original.

16. Al crear un hard-link ¿Se ocupa un nuevo inodo? ¿Y con un link simbólico?

    En el caso del hard-link, tiene el mismo inodo y permisos que el archivo original; con el link simbólico, si, tiene un inodo diferente al del archivo original, así como también podría tener otros permisos.

17. ¿Para qué sirven los permisos especiales en Linux? Analizar el Sticky-bit, SUID y SGID

    * Sticky-bit: si bien es un permiso de acceso que puede ser asignado a ficheros y directorios, actualmente el sticky bit se utiliza sobre directorios. Cuando se le asigna a un directorio, significa que los elementos que hay en ese directorio solo pueden ser renombrados o borrados por su propietario o bien por root. El resto de usuarios que tengan permisos de lectura y escritura, los podrán leer y modificar, pero no borrar.
    * SUID: cuando se activa el bit SUID sobre un fichero significa que el que lo ejecute va a tener los mismos permisos que el que creó el archivo.
    * SGID: el bit SGID es lo mismo que SUID, pero a nivel de grupo. Esto es, todo archivo que tenga activo el SGID, al ser ejecutado, tendrás los privilegios del grupo al que pertenece. Es una opción bastante útil si queremos configurar un directorio para colaborar diferentes usuarios. Si se aplica este bit al directorio, cualquier archivo creado en dicho directorio, tendrá asignado el grupo al que pertenece el directorio.

18. ¿Cuáles son los permisos del archivo /etc/shadow? ¿Por qué puedo modificar mi password sino soy usuario root?

    El dueño de /etc/shadow suele ser el usuario root. Al grupo se le suele setear uno administrativo, como shadow. Otros usuarios no tienen permitido leer el archivo directamente, para evitar que puedan usar las contraseñas hasheadas de otros. Como una herramienta como _passwd_, la cual tiene seteado el bit SUID, el archivo puede ser alterado de forma controlada.

19. Crear un archivo en el directorio /tmp. Si abre otra consola y se loguea con otro usuario, distinto a root, ¿puede borrar ese archivo? ¿Por qué? (Hint.: ver permisos especiales en Linux).

    No es posible borrar el archivo ya que el directorio /tmp tiene configurado el sticky-bit.

20. ¿Qué es el sistemas de archivo F2FS?

    Es un sistema de archivos creado para construir un sistema de archivos que desde el principio tuviera en cuenta las características de los dispositivos de almacenamiento basados en memorias flash NAND, como las unidades de estado sólido (SSD) y las tarjetas eMMC y SD, los cuales han sido ampliamente usados en ordenadores, desde dispositivos móviles hasta servidores. Este sistema de ficheros esta basado en LFS (Log-structured File System) y soluciona algunas de sus limitaciones y está orientado para comportarse, teóricamente, mejor que Ext4 o extFAT.

Usando el espacio libre dejado en el disco de la MV, realizar los siguientes pasos y contestar:

1. Usando la herramienta “fdisk” o “parted” de Linux generar una partición 1GB (en la sección de RAID se muestra como usar el comando parted para crear nuevas particiones. apt-get install parted para instalarlo).

    En este caso, lo que yo usé es fdisk /dev/sda. Ponés n para nueva partición, después es enter hasta que te diga end cylinder: ahí ponés +1000M para indicar 1GB y das enter. Por último, apretá **p** para ver si la tabla de particiones queda como a vos te parece y **w** para escribir los cambios. Despues resetea.

2. Formatear la primera partición con ext4.

    Volviendo del punto anterior, a mi me creo /dev/sda3, pero depende el que te hay creado es el que vas a usar. Si no tenés ni idea cual creaste, usá ```fdisk -l``` para ver las particiones y fijate la que tenga 1GB. Un vez que conocés la partición solo queda formatear usando ```mke2fs -t ext4 /dev/sda3```.

    NOTA DE LIHUEL: puede ser que aparezca por ahí dando vueltas que también se puede usar el ```mkfs```. La diferencia reside en que llamando a ```mkfs``` sin parámetros es como si se llamara a ```mk2fs -t ext2```. Al caso, es lo mismo :D

3. Montar las particiones en el directorio “disc1”.

    Para montar la partición, antes hay que crear un directorio. Para no desordenar tanto la máquina virtual, elegí la carpeta _/mnt_, de ahí que:

    ```mkdir /mnt/disc1```

    Luego, hay que montar la partición en el directorio. Para eso:

    ```mount /dev/sda3```

4. Generar un archivo en el directorio “disc1”. Observar los datos del inodo del archivo generado.

    Primero hay que moverse al directorio creado, es decir hacer ```cd /mnt/disc1```. Luego, para crear el archivo, conviene hacer un ```touch test```, lo cual creará un archivo _test_ vacío. Por último, para ver los datos de inodo, usar ```stat /mnt/disc1/test```. Lo que yo observé es que las fechas de modificación, acceso y cambio son todas iguales.

5. Leer el contenido del archivo. ¿Se modificaron algunos de los datos del inodo del archivo?

    Para leer el contenido del archivo, hacer ```cat test```. Luego repetir ```stat /mnt/disc1/test``` para ver los datos del inodo. Podrá notarse entonces que la fecha de acceso cambió.

6. ¿Cómo puede modificar el comportamiento anterior con el fin de evitar esas modificaciones? ¿Es posible hacerlo sin desmontar la partición?

    //PREGUNTAR

---

### RAID

En este punto se va a crear un RAID Level 5 por software. Para simular los 3 discos necesarios para un RAID de este nivel se deberán generar 3 particiones del mismo tamaño. Una vez realizado este paso se generará el RAID. Para esto se utilizará la herramienta ```mdadm``` que deberá instalarla en la VM provista por la cátedra usando el siguiente comando: ```apt-get install mdadm```.

1. ¿Qué es un RAID? Explique las diferencias entre los distintos niveles de RAID.

    RAID(Redundant Array of Independent Disks) es una técnica que permite usar múltiples discos en forma conjunta con el fin de construir un sistema de discos más rápido, más grande y más confiable. Originalmente, definía los niveles 1, 2, 3, 4 y 5; sin embargo, en 1989, se introdujo un nuevo nivel: el RAID 6 que trajo, entre otras cosas, ventajas sobre un solo disco, incremento de la perfomance, mayor capacidad y aumento de la confiabilidad.
    * RAID 0: no es un nivel de RAID ya que no existe redundancia. Se requieren al menos dos discos para conformarlo. Es un array de discos con _striping_ a nivel de bloque. La capacidad del RAID está definida por la suma de la capacidad de los discos. Si falla un disco, los datos de todos los otros se vuelven inaccesibles.
    * RAID 1: asegura redundancia mediante mirroring. Mínimo de 2 discos: trabaja con pares de discos. No hay _striping_. Almacena datos duplicados en discos separados o independientes. Es ineficiente por la escritura en espejo y desperdicia el 50% de la capacidad total.
    * RAID 2: distribuye los datos entrelazados a nivel de bit. El código de error se intercala a través de varios discos también a nivel de bit. Todo giro del cabezal de disco se sincroniza y los datos se distribuyen en bandas de modo que cada bit secuencial está en una unidad diferente. La paridad se calcula a través de y los bits correspondientes y se almacena en al menos un disco de paridad. Este nivel es sólo significante a nivel histórico y teórico, ya que actualmente no se utiliza.
    * RAID 3: un RAID 3 divide los datos a nivel de bytes en lugar de a nivel de bloques. Los discos son sincronizados por los drivers para funcionar al unísono. Éste nivel actualmente no se usa. Permite tasas de transferencias extremadamente altas. Un RAID 3 necesitaría un mínimo de tres discos, utilizando uno para datos de paridad. En estos se copian los datos en distribución RAID 0 en los 2 primeros discos, sin embargo, en el tercer disco, se crea el byte de paridad. Esto quiere decir que si, por ejemplo, perdemos un byte de uno de los discos, siempre podremos recuperarlo mediante el byte de paridad que se ha generado anteriormente.
    * RAID 4: un RAID 4, también conocido como IDA (Independient Disk Array), usa división a nivel de bloques con un disco de paridad dedicado. Necesita un mínimo de 3 discos físicos. El RAID 4 es parecido al RAID 3 excepto porque divide a nivel de bloques en lugar de a nivel de bytes. Esto permite que cada miembro del conjunto funcione independientemente cuando se solicita un único bloque. Si el driver de disco lo permite, un conjunto RAID 4 puede servir varias peticiones de lectura simultáneamente. En principio también sería posible servir varias peticiones de escritura simultáneamente, pero al estar toda la información de paridad en un solo disco, éste se convertiría en el cuello de botella del conjunto.
    * RAID 5: es una de las implementaciones más utilizadas. Requiere al menos 3 discos. El _striping_ se da a nivel de bloque y la paridad es distribuida. La información de esta está distribuida entre todos los discos del array. El rendimiento es alto y no hya cuellos de botella; sin embargo, no hay solución al fallo simultáneo de discos.
    * RAID 6: es únicamente recomendado cuando se tienen varios discos, ya que requiere al menos 4. El _striping_ se da a nivel de bloque y la paridad es doble y distribuida. Tiene una alta tolerancia a fallos (hasta dos discos); sin embargo, dado que la paridad es doble las operaciones de escritura son más lentas.

2. Utilizar el comando ```parted -l``` (debe ejecutarlo con ```sudo```) para ver la tabla de particiones. Conteste:

    NOTA DE LIHUEL: si no encontrás el parted, se puede instalar haciendo ```apt-get install parted```

    1. ¿Cómo llama Linux al dispositivo físico? ¿Cuál es su tamaño total?

        Lo llama _sda_. Su tamaño total es 172GB.

    2. ¿Cuántas particiones existen? ¿Qué tipo de file-system contiene cada una?

        Existen 3 particiones: _sda1_ que es una primaria de tipo ext4, _sda2_ que es otra primaria de tipo linux-swap(v1) y _sda3_ que es otra primaria de tipo ext4(esta última es la que tuve que crear antes para el último punto de la primera parte).

    3. ¿Qué significa el flag _boot_?

        Es lo que le indica al master boot record qué partición ha de bootear.

    NOTA DE LIHUEL: antes de seguir, borré la partición de 1GB que tuve que crear para la parte anterior para no desperdiciar espacio, dado que el siguiente punto es crear una nueva partición. Para borrar la partición usé ```fdisk /dev/sda```, tipié _d_ para borrar una partición y luego ingresé _3_ para indicar que borrara sda**3**.

3. Usando el comando ```parted``` crear una nueva partición de tipo extendida, si es que no existe previamente (debe seleccionar el dispositivo donde se van a generar las particiones):

    1. ```sudo parted```

        NOTA DE LIHUEL: como no tenía ganas de configurar el _sudoers.d_, yo tuve que abrirlo una vez habiéndome logueado como usuario _root_.

    2. _(parted) print_

        Este comando te muestra tus particiones actuales, es útil porque en el próximo comando te va a pedir valores de acá.

    3. _(parted) mkpart_

        Este es el comando para crear una partición. Inicia el proceso interactivo.

        NOTA DE LIHUEL: para agilizar el proceso, se puede usar el comando con parámetros, el cual se arma de la manera que sigue: ```mkpart part-type start end```.

    4. Tipo de partición: extendida.
    5. Inicio: #MB (igual al tamaño final de la partición existente más alta.

        Se utiliza el tamaño final de la partición existente más alta para que sea lo más contiguo posible.

    6. Fin: #MB (igual al tamaño máximo del dispositivo físico).

        Si bien el tamaño máximo es 172GB, estoy seguro que no se va a usar ni un cuarto de eso y no voy a asignar disco en algo que no voy a usar, por lo que decidí bajar a 50GB y que la partición tenga 10GB (que también me parece mucho, pero más razonable). La cantidad que de espacio que va a tener la partición se define como _end - start_, esto es, si mi start fue 40GB y mi end 50GB, la partición va a tener 10GB.

    Dentro de esta partición extendida crear 3 nuevas particiones de 300MB cada una. Para esto utilizar nuevamente el comando ```mkpart```, pero debe seleccionar logical como tipo de partición. Como tipo de sistema de archivos elija ext4.

    Para este caso usé el comando ```mkpart``` con parámetros y lo construí de la siguiente manera: ```mkpart part-type fs-type start end```. Las particiones quedaron aproximadamente de 300MB(287, 299 y 299 MB respectivamente).

    Observación: como las particiones lógicas se crean dentro de una partición extendida, los valores de inicio/fin de cada partición lógica deben estar dentro de los valores seleccionados en la partición extendida.

4. ¿Por qué es necesario crear una partición extendida? Si se usase GPT, ¿Sería necesario este tipo de particiones?

    La tangente pasa por el tipo de tabla de particiones que tiene el disco definido de la máquina virtual: es un MBR, lo cual tiene un límite de cuatro particiones. Esto significa que si yo quisiera crear tres particiones más no podría, ya que antes ya habían otras dos (la partición del sistema y la swap) creadas. Como mucho podría crear otras dos, pero esto no es lo que se busca. Para eso es que uso la partición extendida, que me permite crear unas cuantas particiones lógicas dentro de esta (aparentemente el máximo es 65536, pero en linux se limita a 60, lo importante es que me permite crear todas las particiones extras que necesito). Se crea una partición extendida y luego otras tres lógicas dentro de los límites de la primera y asunto resuelto.

    Si se usara GPT, la historia sería completamente diferente. Aparentemente, GTP permite definir tantas particiones como yo quiera, aunque el límite lo define el sistema operativo (en este caso, el límite es de 128 particiones, pero lo importante, nuevamente, es que ya con ese número podemos definir las 3 particiones que necesitábamos, sin necesidad de recurrir a la partición extendida--de hecho, a priori, solo se pueden crear particiones primarias en GPT, aunque también es posible convertir de una primaria a una extendida en GPT--)

5. Mirar nuevamente la tabla de particiones para ver las nuevas particiones y salga del entorno ```parted```.

    Para salir usé ```parted quit```. Era necesario refrezcar el _/etc/fstab_, por lo que reinicié la máquina virtual (hay otras formas, pero esa era la más cómoda).

6. Utilizar el comando ```mdadm``` para crear un RAID 5 utilizando las 3 particiones lógicas que se generaron en el punto anterior (```fdisk -l``` para ver el nombre de las particiones que generaron).

    ```mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sda5 /dev/sda6 /dev/sda7```

    Observación: _md0_ es el nombre que le dará al nuevo RAID.

    NOTA DE LIHUEL: si te llega a tirar un error relacionado a que un módulo no está, es que el kernel no te soporta la funcionalidad. Esto se resuelve fácilmente instalando una imagen que si sirva (la que yo usé fue la 4.19.0.8-amd64). Sea como fuera, lo solucionas con este comando: ```apt install linux-image-amd64```. Después reiniciá la máquina virtual y, antes de bootear el Debian, asegurate de ir a las opciones avanzadas y bootear el kernel adecuado. Ahí si ejecutá el comando de ```mdadm``` y no debería haber problemas :D

7. ¿Qué significan los valores sda5, sda6 y sda7?

    SD es la forma en que se nomenclan los discos duros en Linux. La letra A significa que estamos hablando del primer disco duro detectado. Los diferentes número hacen referencia a las diferentes particiones del disco, en este caso, las particiones 5, 6 y 7.

8. Ejecutar la siguiente consulta y contestar:

    ```mdadm --detail /dev/md0```

    1. ¿Cuál es el tamaño total del RAID?

        El tamaño total que me figura es 570.43 MB.

        NOTA DE LIHUEL: El campo que usé es _Array Size_. La línea decía lo siguiente: **Array Size : 557056 (544.00 MiB 570.43 MB)**

    2. ¿Cuál es el tamaño total utilizable para almacenar datos?

        El tamaño total que me figura es 544 MB.

        NOTA DE LIHUEL: El campo que usé es _Array Size_. La línea decía lo siguiente: **Array Size : 557056 (544.00 MiB 570.43 MB)**

9. Analizar el contenido del siguiente comando:

    ```cat /proc/mdstat```

    El /proc/mdstat muestra una instantánea del estado del RAID del kernel. La línea que dice _personalities_ indica qué niveles de RAID soporta el kernel. Para soportar más niveles basta cambiar los módulos referentes al RAID o recompilar el kernel. La línea de abajo indica el estado del dispositivo RAID(debería ser _active_. Si es _inactive_, estamos en problemas) y los dispositivos que lo conforman. El orden de estos no significa nada. La siguiente línea muestra el tamaño del array, el tamaño usable y el algoritmo que usa. Al final de esa línea aparece algo similar a esto: [n/m]. Lo que significa es que, idealmente, el array tendría que tener **n** dispositivos, pero los cierto es que **m** dispositivos están en uso. Si m >= n, entonces está todo OK. Después de [n/m], aparece algo similar a esto: [UUU]. Eso representa el estado de los dispositivos. La cantidad de U varían y también podrían aparecer \_. Las U representan dispositivos *up* y los \_, dispositivos *down*.

    Observación: puede suceder que al reiniciar la VM el RAID se vea como de solo lectura y con el número 127. Para solucionar esto deben ejecutar los comandos ```mdadm –stop /dev/md127``` para parar el RAID y ```mdadm –assemble –scan``` para volverlo a generar como _md0_ y de lectura/escritura. Esto se debe hacer cada vez que se inicia la VM. Si quiere que quede en forma persistente a través de los reboots debe guardar la configuración en el archivo **mdadm.conf**, ```mdadm – assemble –scan >> /etc/mdadm/mdadm.conf``` y luego ```update-initramfs -u``` (esto último puede tardar un poco de tiempo).

10. Ahora se va a probar la funcionalidad del RAID 5. Para esto completar los siguientes pasos:

    1. Crear un file system de tipo ext4 en el RAID 5 recién generado

        ```mkfs.ext4 /dev/md0```

    2. Montar la partición con el file system generado en el directorio _/mnt/rd5_

        Para montar el filesystem, antes hay que crear el directorio con ```mkdir /mnt/rd5```. Una vez creado el directorio, se procede a montar el filesystem con el comando ```mount``` de la siguiente manera: ```mount el/dispositivo/a/montar el/directorio/donde/lo/voy/a/montar```. En síntesis, el comando ```mount``` que usé fue ```mount /dev/md0 /mnt/rd5```.

    3. Crear un directorio con dos archivos.

        Para esto, primero creo el directorio haciendo ```mkdir /mnt/rd5/dir1```. Después, creo los archivos con ```touch /mnt/rd5/dir1/file1 /mnt/rd5/dir1/file2```.

    4. Quitar una de las particiones del RAID. Para esto ponemos uno de los componentes en falla:

        ```mdadm --fail /dev/md0 /dev/sda7```

    5. Observar el estado del RAID y contestar:

        1. ¿Cuál es el estado del RAID? ¿Cuántos dispositivos activos existen?

            El raid sigue en _active_. Hay dos dispositivos levantados y uno dado de baja por estar _faulty_.

        2. El tamaño total y disponible del RAID ¿Se modificó?

            Sigue siendo exactamente lo mismo.

        3. ¿Qué sucedería si se ejecuta el comando anterior sobre una de las particiones restantes?

            Se pierden los datos definitivamente. La pérdida de un disco degrada el rendimiento. Ya cuando se pierden dos, los datos se pierden definitivamente.

    6. Quitar del RAID el componente puesto en falla en el paso anterior ```mdadm --remove /dev/md0 /dev/sda7```
    7. Observar nuevamente el estado del RAID y contestar:

        1. ¿Se puede acceder al directorio /mnt/rd5? ¿Están los archivos creados anteriormente?

            Sigue pudiéndose acceder y los archivos siguen estando ahí. ESto se da porque, si bien el sistema sabe que un disco ha fallado, sigue funcionando sólo con el fin de que el sistema operativo pueda notificar al administrador que una unidad necesita ser reemplazada: las aplicaciones en ejecución siguen funcionando ajenas al fallo. Las lecturas y escrituras continúan normalmente en el conjunto de discos, aunque con alguna degradación de rendimiento.

        2. ¿Qué hubiese sucedido si teníamos otra partición como “hot-spare”?

            Por lo general, las particiones _hot spare_(discos de reserva) se usan inmediatamente(y casi siempre automáticamente) tras algún fallo de un disco del RAID. Esto reduce el tiempo del período de reparación al acortar el tiempo de reconstrucción del RAID.

    8. Por último, remover la partición permanentemente del RAID (Observación: esto es muy importante para que el próximo booteo ```mdadm``` no intente usar a esta partición como parte del RAID, lo que provocaría la pérdida de todos los datos):

    ```mdadm --zero-superblock /dev/sda7```

    A partir de este momento la partición /dev/sda7 se puede utilizar como una partición común.

11. Para evitar la pérdida de datos es fundamental volver al RAID a un estado estable (sacarlo del estado degradado). Para esto se agregará nuevamente la partición /dev/sda7 que se quitó en el paso anterior.

    1. Ejecutar el comando ```mdadm –add /dev/md0 /dev/sda7```

        NOTA DE LIHUEL: al comando, en la parte de -add, le falta un guion.

    2. Ejecutar el comando ```mdadm –detail /dev/md0```

        NOTA DE LIHUEL: al comando, en la parte de -add, le falta un guion.

    3. ¿Qué hace el RAID con la nueva partición recientemente agregada? ¿Qué significa el estado “Rebuild Status”?

        Lo que se hace es un resync, dado que deben copiarse los datos del RAID al nuevo disco agregado. El estado _rebuild_ se da cuando el raid es inconsistente. Lo que se hace es precisamente reconstruir los discos para que sean idénticos.

    4. ¿Es posible ingresar al recurso /mnt/rd5? ¿Se encuentran disponibles los datos creados en el punto anterior?

        Si, es posible ya que, a pesar de que uno de los discos se esté re-sincronizando, los otros dos están activos, por lo que el RAID usa eso. Si, ya que el RAID solo estuvo en estado degradado y no llegó al punto de perder definitivamente todos los datos.

12. Como los datos que mantiene el RAID son muy importantes es necesario tener un disco (partición en nuestro ejemplo) de respaldo. Para esto se va a agregar una partición como “hot-spare”.
13. Usando el comando ```parted``` generar una nueva partición /dev/sda8 (con igual tamaño a las anteriores).

    Para esto primero hay que abri el ```parted```. Luego, usé el comando ```mkpart``` con parámetros y lo construí de la siguiente manera: ```mkpart part-type start end```. La partición quedó de 299MB.

14. Agregar la nueva partición al RAID: ```mdadm --add /dev/md0 /dev/sda8```

    1. ¿Cómo se agregó la nueva partición? ¿Por qué?

        La partición se agregó como _spare_, es decir como partición extra, ya que el superbloque del RAID fue declarado solo con 3 particiones, por lo que siempre van a estar activas tres como mucho.

15. Volver a poner en falla a la partición /dev/sda7. Ver el estado del RAID y contestar:

    ```mdadm --detail /dev/mda0```

    NOTA DE LIHUEL: Para marcar a _sda7_ como _faulty_ usé ```mdadm --fail /dev/md0 /dev/sda7```.

    1. ¿Qué hace el RAID con la partición que estaba como spare?

         Automáticamente le cambia el estado a activa y empieza a sincronizar.

16. Por último, se eliminará el RAID creado en los pasos anteriores.

    1. Desmontar el RAID (comando umount).

        Para desmontar el RAID usé ```umount /mnt/rd5```.

    2. Para cada una de las particiones del RAID ejecutar los pasos realizados cuando se quitó una partición del RAID (```mdadm``` con las opciones ```-–fail``` y ```–remove```). Por cada partición que se quita ir mirando el estado del RAID para ver como se comporta.

        NOTA DE LIHUEL: a ```-remove``` le falta un guión.

        Cuando se marca como _faulty_ la partición _sda8_, no pasa nada. Cuando se hace lo mismo con _sda7_, el estado pasa a _degraded_. Finalmente, cuando se aplica lo mismo a _sda6_, ahí se pasa a _failed_, idem con _sda5_.

    3. Remover los superbloques de cada una de las particiones:

        ```mdadm --zero-superblock /dev/sda5 /dev/sda6 /dev/sda7```

    4. Remover el RAID:

        ```mdadm --remove /dev/md0```

        Observación: si existe, quitar la linea ARRAY... del archivo _/etc/mdadm/mdadm.conf_

    5. Reiniciar y comprobar que el RAID ya no existe.

---

### LVM (Logical Volumen Management

A continuación se creará un LVM utilizando las particiones **/dev/sda5**, **/dev/sda6** y **/dev/sda7** (respetar el tamaño y nombre de los volúmenes y directorios). En principio solo se utilizarán las particiones 5 y 6, luego se agregará la partición 7 para incrementar el tamaño de los volúmenes.

1. ¿Qué es LVM? ¿Qué ventajas presenta sobre el particionado tradicional de Linux?

    LVM es una implementación de un gestor de volúmenes lógicos para el kernel Linux. Provee un método más flexible que los convencionales esquemas de particionamiento para asignar espacio en los dispositivos de almacenamiento masivo. Sus principales componentes son:
    * Physical Volume (PV): dispositivos físicos o particiones que serán utilizados por LVM.
    * Volume Group (VG): grupo de physical volumns. Representa el data storage.
    * Logical Volume (PV): cada una de las partes en las que se dividen los volume groups.
    * Physical Extent (PE): unidades direccionables en las que LVM divide cada physical volumns.
    * Logical Extent (LE): unidad de asignación básica en los LVs.

    Algunas ventajas de usar LVM son:

    * Particionamiento simplificado: con el uso de LVM, el disco completo puede ser asignado a un único grupo lógico y definir distintos volúmenes lógicos para almacenar /home u otros directorios. En el caso de que nos quedemos sin espacio, por ejemplo, en /home, y tenemos espacio en /opt, podríamos redimensionar /home y /opt y usar el espacio que le hemos quitado a /opt y añadírselo a /home. Hay que tener en cuenta, que para realizar esto, nuestro sistema de ficheros debe soportar el redimensionado por arriba y por abajo, como ReiserFS.
    * Administración simplificada: administrar un sistema con muchos discos es un trabajo que consume tiempo, y se hace particularmente complejo si el sistema contiene discos de distintos tamaños. Equilibrar los requerimientos de almacenamiento de distintos usuarios (a menudo conflictivos) puede ser una tarea muy laboriosa y compleja. Los distintos grupos de usuarios pueden tener sus volúmenes lógicos y éstos pueden crecer lo que sea necesario, y el administrador puede realizar las operaciones oportunas sobre dichos volúmenes.

2. ¿Cómo funcionan los _snapshots_ en LVM?

    Las instantáneas (_snapshots_) permiten al administrador crear un nuevo dispositivo que será una copia exacta del LV, congelada en algún punto del tiempo. Normalmente esto se realiza de forma automática, para no alterar el funcionamiento normal del sistema. Cuando la instantánea ha terminado, el administrador puede quitar el dispositivo sin mayor complicación. Además, no es necesario que los datos en el LV se encuentren en un estado consistente, ya que muchos sistemas de ficheros en el kernel 2.6 lo realizan de forma automática.

3. Instalar la herramienta lvm2: ```apt-get install lvm2```

4. Ejecutar el siguiente comando:

    ```pvcreate /dev/sda5 /dev/sda6```

    ¿Qué es lo que realiza?

    El comando anterior crea dos volúmenes físicos.

5. Mediante el comando ```pvdisplay``` observar el estado del volumen físico recientemente creado.

6. Crear un grupo de volúmenes (volume group, VG) llamado *so_X*, donde X es el número de grupo asignado.

    ```vgcreate so_X /dev/sda5 /dev/sd6```

    En mi caso, yo era el grupo 55, por lo que ejecuté ```vgcreate so_55 /dev/sda5 /dev/sd6```

7. Utilizar el comando ```vgdisplay``` para ver el estado del VG ¿Cuál es tamaño total del VG? ¿Qué significa PE? ¿Qué define?

    El tamaño total es 556 MiB. Cada volumen físico se divide en chunks de data, conocidos como _physical extent_ (PE), los cuales tienen el mismo tamaño que los logical extents para el grupo de volúmenes.

    NOTA DE LIHUEL: el tamaño total del VG lo saqué del campo _VG size_. A su vez, los campos sucesivos(_PE size_ y _total PE_) arrojan información acerca de los physical extents.

8. Crear dos volúmenes lógicos (logical volume, LV) de 8MB y 117MB respectivamente

    ```lvcreate -l 2 -n lv_vol1 so_X```
    ```lvcreate -L 117M -n lv_vol2 so_X```

9. ¿Cuál es la diferencia entre los dos comandos utilizados en el punto anterior?

    La diferencia es que el primero se utiliza para el número de logical extents a asignar para el nuevo volumen lógico. Al otro le das directamente el tamaño que tendrá el nuevo volumen lógico.

10. ¿Con qué tamaño se generó el LV lv_vol2? ¿Por qué?

    Se generó con 120MiB porque lvcreate redondea al extent más cercano. Siempre redondea para arriba, nunca para abajo.

11. Formatear los dos LV generados en el paso anterior con un file system de tipo ext4:

    ```mkfs.ext4 /dev/so_X/lv_vol1```

    Dado que yo era el grupo 55, los comandos que ejecuté fueron ```mkfs.ext4 /dev/so_55/lv_vol1``` y ```mkfs.ext4 /dev/so_55/lv_vol2```.

12. Crear dos directorios, vol1 y vol2, dentro de /mnt y montar ambos LVs en estos directorios (montar el LV lv_vol1 en el directorio vol1 y lv_vol2 en vol2).

    Vamos de a partes (dijo Jack):

    * Crear los dos directorios en ```/mnt```:

        ```mkdir /mnt/vol1 /mnt/vol2```

    * Luego, montar cada filesystem en un directorio:

        ```bash
        mount /dev/so_55/lv_vol1 /mnt/vol1
        mount /dev/so_55/lv_vol2 /mnt/vol2
        ```

13. Ejecutar el comando proof (Puede tomar un rato su ejecución. Este comando estará disponible en la plataforma y deberán copiarlo a la VM)

14. Crear un nuevo archivo en /mnt/vol1. ¿Es posible? ¿Por qué? ¿Qué debería hacerse para solucionarlo?

    Intenté crear el archivo con ```touch /mnt/vol1/dir1/file1```  y no fue posible ya que se había superado la máxima cantidad de inodos. Para poder crear más archivos son necesarios más inodos, por lo que se necesita extender el volumen.

15. Para aplicar la solución propuesta en el punto anterior, para esto primero se debe incrementar el tamaño del LV correspondiente:

    1. Extender el LV lv_vol1 en 20M

        ```lvextend -L +20M /dev/so_X/lv_vol1```

    2. Ejecute el comando ```df -h``` ¿Se refleja en la salida del comando el incremento del espacio? ¿Por qué?

        No se refleja porque hasta ahora se extendió el LV, pero falta que el filesystem se entere del cambio: para eso es que se va a usar el ```resize2fs```.

    3. Incrementar el tamaño del file system:

        ```resize2fs /dev/so_X/lv_vol1```

16. Después de la operación previa, ¿Siguen estando los datos disponibles?

    Si, siguen estando disponibles porque extendí el filesystem

17. Intentar crear un nuevo archivo en _/mnt/vol1_ ¿Es posible? ¿Por qué?

    Primero creé el archivo con ```touch /mnt/vol1/dir1/file1```. En este caso, es posible ya que se ha resizeado el tamaño del filesystem y ahora si se pueden usar los inodos.

18. Se desea crear un nuevo LV de 500M ¿Hay suficiente espacio? ¿Cómo lo solucionaría?

    No hay suficiente espacio ya que tengo todo usado con los dos volúmenes lógicos. Necesitaría incorporar a sda7 y crear el nuevo LV.

19. Para aplicar la solución indicada en el punto anterior, realizar lo siguiente:

    ```bash
    pvcreate /dev/sda7
    vgextend so_X /dev/sda7
    ```

20. Comprobar con los comandos correspondientes que se haya extendido el tamaño del VG.

    Si se ejecuta ```vgdisplay```, se puede apreciar en la parte de *Free PE / Size* que el tamaño ha crecido.

21. Generar el nuevo LV de 500M (llamarlo lv_vol3).

    Con el comando ```lvcreate -L 500M -n lv_vol3 so_55``` creé el nuevo volumen lógico de 500MB.

22. Montar este nuevo LV en el directorio /mnt/lv_vol3 y crear un directorio con dos archivos adentro (los nombre pueden ser cualquiera). En el siguiente paso se intentará reducir el tamaño del LV generado en el punto 18) de 500M a 400M.

    NOTA DE LIHUEL: antes de montar el LV, tuve que **definirle un filesystem** a *lv_vol3* con ```mkfs.ext4```. Dejo la constancia acá, porque me tiraba error de sistema de ficheros incorrecto, por lo que supuse que era por lo anterior y no tenía forma de seguir sino. Aplicando el comando del punto 11 con los parámetros adecuados, el error se solucionó, por lo que supuse que todo lo anterior fue un descuido de quien redactó la práctica.

    Para montar el filesystem, antes hay que crear el directorio, por lo que primero hay que hacer un ```mkdir /mnt/vol3```. Luego se procede a montar con ```mount /dev/so_55/lv_vol3 /mnt/vol3```. Finalmente, se crea el directorio dentro de _/mnt/vol3_ con ```mkdir``` y, dentro de ese directorio, los archivos con ```touch```:

    ```bash
    mkdir /mnt/vol3/dir1/
    touch /mnt/vol3/dir1/file1
    touch /mnt/vol3/dir1/file2
    ```

23. Desmontar el LV.

    Para desmontar a *lv_vol3*, hay que hacer un ```umount /mnt/vol3```.

24. Reducir el LV *lv_vol3* (decir Sí/Yes al aviso que aparece al ejecutar el siguiente comando)

    NOTA DE LIHUEL: el error que aparecía era que era probable que perdiera el filesystem porque estaría reduciendo un volumen lógico activo.

    ```lvreduce -L 400M /dev/mapper/so_X-lv_vol3```

25. Montar el LV reducido en el paso anterior. ¿Es posible ver el directorio y los archivos generados en el paso anterior?

    Intenté re-montar el LV *lv_vol3* con ```mount /dev/so_55/lv_vol3 /mnt/vol3``` pero no se pudo ya que, efectivamente, como comenté arriba, cuando reducis podés perder el filesystem. En este caso, lo perdí (junto con los archivos y el directorio).

26. ¿Cuál es el error en el procedimiento anterior? ¿Cuáles serían los pasos correctos?

    El error era tipo de sistema de ficheros incorrecto (en otras palabras, el volumen no tiene filesystem o éste está corrupto).

27. Investigar y ejecutar los pasos necesarios, y de forma ordenada, para poder achicar el LV a 400M. A continuación se mostrará el funcionamiento de los snapshot en LVM.

    NOTA DE LIHUEL: este paso me llevó un rato por dos razones:
    * La primera, porque no entendía muy bien cómo se usaba ```resize2fs```.
    * La segunda, porque cuando logré entender ```resize2fs```, ```lvreduce``` redondeó para arriba (el temilla de los extents) y me rompió todo y tuve que hacerlo una vez más.

    Mis recomendaciones para el que vaya a hacer este punto así no pierde tiempo:
    * ```resize2fs``` es un comando que lo que pide es que indiques **con cuántos MBs o GBs te va a quedar el filesystem final**. Esto significa que NO tenés que poner **cuánto querés que le saque** porque, cuando lo ejecutes, te vas a comer el chasco y vas a tener que ir de 0. Además, el tamaño al que lo reduzcas tiene que ser múltiplo del extent. Lo anterior es mucho muy importante, pero el por qué lo voy a explicar abajo.
    * ```lvreduce``` **REDONDEA**. Lo pongo en negrita y en mayúsculas porque es importante tenerlo en cuenta. Esto significa que si el tamaño del extent es 4M y vos reducís a 50M, va a redondear a 52M. Por esta razón es que en el punto anterior, indiqué reducir a un tamaño múltiplo del extent.
    * Una herramienta que está copada para saber si no rompiste nada (en lugar de tratar de montarlo y que te de errores) es ```e2fsck```. Lo que te indica es si el filesystem tiene errores. Si la ejecutas con la opción -f, no le importa que el filesystem esté limpio, igual se ejecuta. Yo la usé al principio (antes de reducir) y al final (después de reducir). Si por una de esas casualidades rompés el filesystem, esta herramienta te lo va a decir explícitamente (te va a decir algo así como que los tamaños no coinciden y que el filesystem está corrupto y te vas a dar cuenta).

    FIN DE NOTA DE LIHUEL.

    Pasos para achicar el LV a 400M (y no perder el filesystem en el intento):

    1. Desmontar el filesystem (```umount /mnt/vol3```).
    2. Reducir el tamaño del filesystem. Este es el paso que antes no habíamos hecho y la razón por la que se rompió todo. Primero hay que reducir el filesystem y luego el LVM. Para reducirlo, usar ```resize2fs /dev/mapper/so_55-lv_vol3 400M```. Asegurarse que el tamaño a reducir sea múltiplo del extent para evitar que se rompa todo (400 es múltiplo de 4).
    3. Reducir el tamaño del LVM usando ```lvreduce``` (```lvreduce -L 400M /dev/mapper/so_55-lv_vol3```). Va a aparecer una leyenda que dice que PUEDE que se destruyan todos los datos (como el filesystem, por ejemplo). Ingresar ***y*** porque PUEDE que pase, pero no va a pasar porque esta vez hicimos todo bien.
    4. Listo! El LVM se debería haber reducido de tamaño sin perder los datos. Como extra, para asegurarnos que todo está bien, se le puede ejecutar, forzosamente, el comando ```e2fsck``` (```e2fsck -f /dev/mapper/so_55-lv_vol3```), que indica si hay errores en el filesystem (el cual indicará que no hay errores) o también se puede re-montar el filesystem (```mount /dev/so_55/lv_vol3 /mnt/vol3```) y verificar que los archivos siguen ahí (```ls /mnt/vol3```).

28. Generar un LV de 100M, nombrarlo lv1, (o usar uno de los generados en pasos anteriores). Montarlo en el directorio /dir1.

    En este caso, lo que yo hice fue reducir el volumen anterior (el *lv_vol3*) a 100Mb usando el procedimiento anterior. Luego creé el dir1 en mnt y procedí a montar el LVM en /mnt/dir1. Los comandos fueron los siguientes:

    ```bash
    #Resizear el volumen a 100Mb
    umount /mnt/vol3
    e2fsck -f /dev/mapper/so_55-lv_vol3
    resize2fs /dev/mapper/so_55-lv_vol3 100M
    lvreduce -L 100M /dev/mapper/so_55-lv_vol3
    #Crear el directorio y montar el volumen ya resizeado
    mkdir /mnt/vol1
    mount /dev/so_55/lv_vol3 /mnt/dir1
    ```

29. Copiar desde /etc todo los archivos y directorios que comiencen con la letra a.

    El comando que usé (siempre estando parado sobre _/etc_) fue ```cp -r a* /mnt/mnt/dir1```.

30. Mediante el siguiente comando generar un snapshot del LV anterior.

    ```lvcreate -L 30M -s /dev/so\_X/lv1 -n lvcopy``` (-s indica que este LV será un snapshot)

31. Verificar la creación del snapshot con el comando ```lvs```. Montarlo en el directorio /snap. ¿Qué contenido tiene en el snapshot?

    El contenido del snapshot es exactamente el mismo que el de _/mnt/dir1_ (lo que dejó el comando ```cp -r a* /mnt/mnt/dir1```).

32. ¿Cuánto espacio hay consumido en el snapshot creado? ¿Por qué sucede esto?

    //PREGUNTAR

33. Para probar el snapshot, elimine una carpeta del LV original (por ej, la carpeta ```apt```). ¿Se eliminó en el LV original? ¿Y qué sucedió en el snapshot?

    La carpeta se eliminó del LV original, pero no del snapshot.

34. Si se desea volver el LV a su estado original se debe hacer un “merge” entre el LV y su snapshot. Para esto primero deben desmontar el LV original y su snapshot correspondiente. Luego, realizar el “merge” de ambos LVs:

    ```lvconvert --merge /dev/so_X/lvcopy```

    Primero tuve que desmontar los volúmenes con umount:

    ```bash
    umount /mnt/dir1
    umount /mnt/snap
    ```

    Después realicé el merge.

35. Comprobar si el LV original contiene nuevamente los datos eliminados anteriormente (Deberá montarlo nuevamente).

    Haciendo un ```mount /dev/so_55/lv_vol3 /mnt/dir1``` y luego un ```ls -l /mnt/dir1```, se puede observar que se restauró la carpeta ```apt```.

36. ¿Qué sucedió con el snapshot? Obs.: en caso que aparezca el error “Can’t merge over...” ejecutar los siguientes comandos para desactivar y activar el LV

    ```bash
    lvchange -an /dev/so_X/lv1
    lvchange -ay /dev/so_X/lv1
    ```

    En mi caso no apareció ningún error y observé que el snapshot ya no está. Esto me permite inferir que el snapshot se puede usar una vez y luego se descarta.

---

### BTRFS & ZFS

1. Tanto para BTRFS como para ZFS, responder:

    * ¿Cuál es el significado de las siglas?

        BTRFS significa **B**-**tr**ee **F**ile-**S**ystem y ZFS, originalmente, significaba **Z**ettabyte **F**ile **S**ystem, pero ahora es un acrónimo recursivo.

    * ¿Quién los creó? ¿Cuál es su modo de licenciamiento?

        A BtrFS lo creo la Oracle Corporation y a ZFS, Sun Microsystems. BtrFS está bajo la licencia CDDL y ZFS, bajo la GPL.

    * ¿Cuáles son las características más importante de cada uno?

        * BtrFS
            * Almacenamiento de archivos basado en extents.
            * 2^64 byte == 16 EiB tamaño máximo de archivo (en la práctica, el límite es 8EiB por el Linux VFS).
            * Indexado espacio-eficiente de directorios.
            * Asignación dinámica de i-nodos.
            * Writable snapshots y snapshots read-only.
            * Subvolúmenes (filesystems raíz separados internamente).
            * Checksums sobre data y metadata (crc32c, xxhash, sha256, blake2b).
            * Compresión (ZLIB, LZO, ZSTD), heuristics
            * Soporte integrado a múltiples dispositivos
                * File Striping
                * File Mirroring
                * File Striping+Mirroring
            * Optimizaciones para SSD.
            * Back-up eficiente incremental.
            * Proceso de limpieza en segundo plano para encontrar y reparar errores de archivos con copias redundantes.
            * Defragmentación del filesystem on-line.
            * Checkeo del filesystem off-line.
            * Conversión in-situ de los sistemas de archivos ext2/3/4 y reiser-fs existentes
            * Dispositivos Seed.
            * Send/receive de cambios en el subvolumen.
                * Mirroring del filesystem eficiente incremental.
            * Desduplicación por lotes o fuera de banda.
            * Soporte de Swapfiles.
            * Verificaciones tree-checker, post-read y pre-write de metadata.
        * ZFS
            * Integridad en los datos que protege al usuario de la corrupción silenciosa de datos.
            * RAID-Z.
            * Capacidad (16 exbibytes como el máximo tamaño de un archivo y 2^48 entradas com máximo en un directorio).
            * Encriptación embebida en el pipeline I/O.
            * Eficiencia en operaciones de lectura-escritura al asignar automáticamente los datos en pools de una forma que, por lo general, maximiza la performance de esta.
            * Modelo transaccional _copy-on-write_.
            * Clones y snapshots.
            * Envío y recepción de snapshots a través de otros pools o de la red.
            * Striping dinámico sobre todos los dispositivos, lo cual maximiza el throughput.
            * Tamaños de bloque variables.
            * Capacidad de desduplicación de datos.

    * Investigar qué es la técnica _copy-on-write_

        _Copy-on-write_ es una política de optimización que consiste en que si múltiples procesos piden recursos que inicialmente son indistinguibles (iguales), se les devuelven punteros al mismo recurso; en el momento en que un proceso intenta modificar su "copia" del recurso, se crea una copia auténtica para prevenir que los cambios producidos por dicho proceso sean visibles por todos los demás. Todo ocurre de forma transparente para los procesos. La principal ventaja de este método es que no se crea ninguna copia adicional del recurso si ningún proceso llega a realizar modificaciones.

2. Generar en la MV dos particiones de 3GB cada una. Crear 3 nuevos directorios llamados _/disco5_, _/volumen1_ y _/volumen2_.

    Para generar los directorios usé ```mkdir /mnt/disco5 /mnt/volumen1 /mnt/volumen2``` y las creé en _/mnt_ ya que, dado que son particiones, me es más fácil organizarlas ahí.

3. Tomar una partición, _/dev/sdaX_ (X=número de una de las particiones creadas en el punto anterior), y crearle un file system de tipo BTRFS. Montarla en el directorio _disco5_. (Sino están instalados los comandos para BTRFS: ```apt-get install btrfs-progs```).

    Para empezar, antes de crear el btrfs tuve que desmontar toda la parte de LVM. Una vez desmontado todo, tuve que forzar la creación del btrfs con la opción ```-f``` del comando ```mkfs.btrfs -f /dev/sda5```. Para montarlo en el directorio usé ```mount /dev/sda5 /mnt/disco5```

4. Por defecto, BTRFS ¿Replica los datos? ¿Y los metadatos? ¿Es posible modificar esto? ¿Cómo lo haría? (Hint: usar ```btrfs fi df -h /disco5``` o ```btrfs df usage /disco5```).

    Por defecto, BtrFS no replica Data pero si Metadata y System. Acerca de si es posible modificar esto, lo cierto es que no encontré el comando para hacerlo, pero sé que cuando se crea un BtrFS en un SSD, la replicación viene desactivada (aparentemente, por cuestiones de performance). El comando que se usa para activarla es ```sudo btrfs balance start -v -mconvert=dup /toplevel/```. //PREGUNTAR

5. ¿Cuál es el espacio alocado? ¿Y el ocupado realmente? Utilice los comandos ```df -h```, ```btrfs fi show```, ```btrfs fi df -h /disco5``` y ```btrfs df usage /disco5```.

    NOTA DE LIHUEL: el espacio asignado lo saqué del campo _Device allocated_ y el tamaño total de _Device size_. Toda esa información me apareció ejecutando ```btrfs df usage /mnt/disco5```.

    El espacio asignado es de 88MiB y el tamaño total es de 274MiB

6. Generar un archivo de 2000MB en el directorio _/disco5_ (```dd if=/dev/zero of=/disco5/so1 bs=100M count=20```). Analizar nuevamente el espacio alocado/ocupado. (Observación: puede que tenga que esperar un tiempo para ver los cambios en la salida de los comandos btrfs...). ¿Cómo quedó el espacio asignado y el utilizado tanto de los datos como de los metadatos?

    En este caso, como la partición en el punto anterior la generé de 300Mb, cuando ejecuté el ```dd```, fue bastante rápido porque se llenó enseguida. Lo importante es que el espacio asignado cambió y aumentó (pasó de 88MiB a 273MiB), lo mismo para el usado con datos (pasó de 8MiB a 193MiB). Sin embargo, el tamaño de los metadatos no se modificó (64MiB).

7. Asignar la otra partición a _/disco5_ ¿Se modificaron los valores con respecto al punto anterior?

8. Generar otro archivo de 3000MB en el directorio _/disco5_ ¿Aumenta el espacio alocado? ¿Cuánto espacio se ha ocupado realmente?

9. Usando las dos particiones anteriores crear un RAID1 y montarlo en _/disco5_. ¿Qué partición puede elegir para montar el file system? (Desmontar previamente la partición montada en _/disco5_)

10. ¿Es posible generar los dos archivos anteriores en ese filesystem? ¿Por qué?

11. Eliminar todo el contenido de _/disco5_ y generar dos subvolúmenes, llamados _vol1_ y _vol2_. ¿Puede ver los subvolúmenes creados? ¿Qué ID tiene cada uno? ¿Qué significa el ID 5?

12. Montar esos volúmenes, vol1 y vol2, en los directorios _/volumen1_ y _/volumen2_ respectivamente. ¿Qué espacio disponible tiene cada volumen? ¿Es posible acotar el espacio de un volumen? ¿Es necesario que esté montado el subvolumen top-level para poder montar sus subvolúmenes?

13. Generar un archivo de 300MB en el directorio _/disco5/vol1_, ¿es posible ver el archivo en _/volumen1_? Si ejecuta el comando ```df -h```, ¿qué espacio se ha consumido _disco5_, _volumen1_ y _volumen2_? ¿Por qué sucede esto?

14. Limitar el tamaño del subvolumen volumen2 a 300MB. Intentar copiar un archivo de 400MB, ¿es posible hacerlo? (Hint: ```btrfs quota ...```).

15. Elevar el tamaño de la cuota a 450MB ¿Es posible ahora? (Previamente revisar si el volumen está vacío)

16. Realizar un snapshot del subvolumen _/disco5/vol1_ en _/disco5/snap_. Antes de esto crear un archivo con el texto **Esto es una prueba de un snapshot** y otro archivo de 100MB. Chequear, antes y después de generar el snapshot, con ```df -h``` y los comandos de btrfs el espacio alocado y consumido ¿Se incrementó el espacio consumido? ¿Por qué?

17. Modificar el contenido el archivo original agregándole **para sistemas operativos** ¿Se modifica la copia en el snapshot?
18. Si se desea volver al subvolumen original, ¿cómo lo haría? (sin hacer un copy o move de los archivos)

<http://www.nongnu.org/ext2-doc/ext2.html>
<http://e2fsprogs.sourceforge.net/ext2intro.html>
<http://www.cyberciti.biz/tips/understanding-unixlinux-filesystem-superblock.html>
<http://www.tldp.org/LDP/tlk/fs/filesystem.html>
<https://kernelnewbies.org/Ext4>
<http://www.geekride.com/hard-link-vs-soft-link/>
<https://www.howtoforge.com/linux_lvm>
<https://www.digitalocean.com/community/tutorials/an-introduction-to-lvm-concepts-terminology-and-operations>
<https://btrfs.wiki.kernel.org/index.php/Main_Page>
<https://lwn.net/Articles/579009/>
