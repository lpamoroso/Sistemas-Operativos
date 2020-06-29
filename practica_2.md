# Práctica 2

## Conceptos teóricos

El propósito de esta primera sección de la práctica es introducir los conceptos preliminares que necesitará el alumno, para desarrollar la actividad práctica del apartado B de la presente guía de estudio.

1. ¿Qué es el kernel de GNU/Linux? ¿Cuáles son sus funciones principales dentro del Sistema Operativo?

    Es un programa que ejecuta programas y gestiona los dispositivos de hardware del sistema. Es el encargado de que el software y el hardware puedan trabajar juntos. Sus principales funciones son administrar la memoria principal y la CPU.

2. Indique una breve reseña histórica acerca de la evolución del kernel de GNU/Linux.

    En 1991 Linus Torvalds inicia la programación del kernel Linux basado en Minix (clon de Unix desarrollado por Tanembaum en 1987 con el fin de crear un SO de uso didáctico).

    El 5 de octubre de 1991, se anuncia la primera versión "oficial" de Linux (0.02).

    En 1992, con la release de la versión 0.12, se decide cambiar a una licencia GNU.

    En marzo de 1994, Torvalds considera que todos los componentes del kernel estaban suficientemente maduros y lanza la versión 1.0.

    En el año 1995, Linux se porta a arquitecturas DEC Alpha y Sun SPARC. Con el correr de los años se portó a otra decena de arquitecturas.

    En mayo de 1996, se decide adoptar a Tux como mascota oficial de Linux.

    En julio de 1996, se lanza la versión 2.0 y se define un sistema de nomenclatura. Se desarrolló hasta febrero de 2004 y terminó con la versión 2.0.40. Esta versión comenzó a brindar soporte a sistemas multiprocesadores.

    En 2001 se lanza la versión 2.4 y se deja de desarrollar a fines del 2010 con la 2.4.37.11. La versión 2.4 fue la que catapultó a GNU/Linux como un sistema operativo estable y robusto.

3. Explique brevemente la arquitectura del kernel de GNU/Linux teniendo en cuenta: tipo de kernel, módulos, portabilidad, etc.

    + Tipo de kernel: monolítico, es decir, el sistema operativo completo trabaja en el espacio dedicado al kernel. El modelo monolítico difiere de otras arquitecturas de sistemas operativos en que solo define una interfaz virtual de alto nivel sobre el hardware de la computadora. Un conjunto de primitivas o llamadas al sistema implementan todos los servicios del sistema operativo, como la gestión de procesos, la concurrencia y la gestión de memoria. Los controladores de dispositivo se pueden agregar al kernel como módulos.
    + Módulos: pueden cargarse y descargarse dinámicamente en tiempo de ejecución. Esta modularidad del sistema operativo está en el nivel binario (imagen) y no en el nivel de arquitectura. En términos prácticos, cargar módulos dinámicamente es simplemente una forma más flexible de manejar la imagen del sistema operativo en tiempo de ejecución, en lugar de reiniciar con una imagen diferente del sistema operativo. Los módulos permiten una fácil extensión de las capacidades de los sistemas operativos según sea necesario. Los módulos cargables dinámicamente incurren en una pequeña sobrecarga en comparación con la construcción del módulo en la imagen del sistema operativo. Sin embargo, en algunos casos, cargar módulos dinámicamente (según sea necesario) ayuda a mantener al mínimo la cantidad de código que se ejecuta en el espacio del kernel. Es decir, un módulo descargado no necesita almacenarse en una memoria de acceso aleatorio escasa.
    + Portabilidad: si bien no fue pensado para ser portable, hoy en día Linux fue portado a múltiples arquitecturas, desde ARM hasta IBM z / Architecture. Los usuarios de Amiga realizaron el primer port más allá de la arquitectura 386 original de Linux en la plataforma Motorola 68000, quienes lograron esto al reemplazar las partes principales del núcleo. Se considera que tal port fue el ímpetu que Torvalds necesitaba para liderar una importante reestructuración del código del kernel para facilitar el transporte a arquitecturas informáticas competidoras. El primer puerto respaldado por Linux fue a la plataforma DEC Alpha AXP de 64 bits que se demostró en DECUS en mayo de 1995, soportando tanto 386 como Alpha en un solo árbol fuente. DEC fue responsable de suministrar el hardware necesario para Torvalds para habilitar un puerto de Linux a 64 bits ese mismo año.

4. ¿Cuáles son los cambios que se introdujeron en el kernel a partir de la versión 3.0? ¿Cuál fue la razón por la cual se cambió de la versión 2 a la 3? ¿Y la razón para el cambio de la versión 3 a la 4?

    + El 17 Julio de 2011 se lanza la versión 3.0:
        + No agrega mayores cambios. La decisión del cambio son los 20 años del SO y no superar los 40 números de revisión.
        + Totalmente compatible con Kernel 2.6.
        + Termina con la versión 3.8.30.
        + Provee mejoras en Virtualización y FileSystems.
    + El 12 de Abril de 2015 se lanza la versión 4.0:
        + Una de sus principales mejoras es la posibilidad de aplicar parches y actualizaciones sin necesidad de reiniciar el SO.
        + Soporte para nuevas CPU.
        + La versión actual es 4.5.

5. ¿Cómo se define el versionado de los kernels de GNU/Linux?

    La anatomía de una versión a partir de 2.6 se ve de la siguiente forma:

    ```A.B.C.[D]```

    + A denota versión. Cambia con menor frecuencia. En 1994 (versión 1.0), en 1996 (versión 2.0), en 2011 (versión 3.0) y en 2015 (versión 4.0).
    + B denota revisión mayor. Antes de la versión 2.6, los números impares indicaban desarrollo y los pares, producción.
    + C denota revisión menor. Solo cambia cuando hay nuevos drivers o características.
    + D se utiliza cuando se corrige un error grave sin agregar nueva funcionalidad.

    En el año 2011, cuando se pasó de la versión 2.6.39 a la 3.0, se eliminó el número de revisión mayor (B).

    La anatomía del versionado anterior a 2.6 es la que sigue:

    ```X.Y.Z```

    + X Indica serie principal. Cambia cuando su funcionalidad sufre un cambio muy importante.
    + Y Indica si es una versión de producción o desarrollo.
        + Números Y pares indicaban versión en producción (estable).
        + Números Y impares indicaban versión en desarrollo.
    + Z Nuevas versiones dentro de la actual. Bugfixes.

6. ¿Cuáles son las razones por las cuáles los usuarios de GNU/Linux recompilan sus kernels?

    + Soportar nuevos dispositivos como, por ejemplo, una placa de video.
    + Agregar mayor funcionalidad (soporte para algún hardware específico).
    + Optimizar funcionamiento de acuerdo al sistema en el que corre.
    + Adaptarlo al sistema donde corre (quitar soporte de hardware no utilizado).
    + Corrección de bugs (problemas de seguridad o errores de programación).

7. ¿Cuáles son las distintas opciones para realizar la configuración de opciones de compilación de un kernel? Cite diferencias, necesidades (paquetes adicionales de software que se pueden requerir), pro y contras de cada una de ellas.

    Existen tres interfaces que permiten generar el archivo _.config_:
    + make config: modo texto y secuencial. Tedioso.
    + make xconfig: interfaz gráfica utilizando un sistema de ventanas. No todos los sistemas tienen instalado X.
    + make menuconfig: este modo utiliza ncurses, una librería que permite generar una interfaz con paneles desde la terminal. Generalmente el más utilizado.

8. Nombre al menos 5 opciones de las más importantes que encontrará al momento de realizar la configuración de un kernel para su posterior compilación.

    + Networking.
    + Device drivers.
    + File Systems.
    + Block layer.
    + Library Routines.

9. Indique que tarea realiza cada uno de los siguientes comandos durante la tarea de configuración/compilación del kernel:
    + make menuconfig

        Abre una interfaz basada en ncurses que permite navegar las categorías de drivers.

    + make clean

        Invoca la opción _clean_ del makefile que borra los ejecutables.

    + make (investigue la funcionalidad del parámetro -j)

        La utilidad make determina automáticamente qué partes de un programa necesitan ser recompiladas y emitirá los comandos para recompilarlas. Make suele utilizarse para programas en C, pero lo cierto es que puede usarse con cualquier lenguaje de programación cuyo compilador se pueda ejecutar con un comando de shell. De hecho, no se limita a los programas: puede usarse para describir cualquier tarea en la que algunos archivos deben actualizarse automáticamente de otros cuando los otros cambien.
        La opción -j(_jobs_) especifica el número de trabajos (comandos) para ejecutar simultáneamente. Si hay más de una opción -j, la última es efectiva. Si la opción -j se proporciona sin un argumento, make no limitará el número de trabajos que pueden ejecutarse simultáneamente.

    + make modules (utilizado en kernels antiguos, actualmente no es necesario)

        Compila archivos individuales para cada item que vaya marcado con M durante la configuración del kernel. El código objeto está vinculado al kernel recién construido (para los items marcados con Y, estos ya son parte de vmlinuz; y para los items marcados con N, se omiten).

    + make modules_install

        Instala los módulos del kernel en /lib/modules o /lib/modules/\<version\>.

    + make install

        Instala el kernel construido en /vmlinuz.

10. Una vez que el kernel fue compilado ¿Dónde queda ubicada su imagen? ¿Dónde debería ser reubicada? ¿Existe algún comando que realice esta copia en forma automática?

    Al terminar el proceso de compilación, la imagen del kernel quedará ubicada en ```directorio-del-código/arch/arquitectura/boot/```.

    El próximo paso, entonces, es instalar el kernel y otros archivos en el directorio ```/boot```. ```sudo make install``` realiza la copia automáticamente.

11. ¿A qué hace referencia el archivo initramfs? ¿Cuál es su funcionalidad? ¿Bajo qué condiciones puede no ser necesario?

    Un initramfs es un sistema de archivos temporal que se monta durante el arranque del sistema. Contiene ejecutables, drivers y módulos necesarios para lograr iniciar el sistema. Luego del proceso de arranque, el disco se desmonta. Es necesario para los módulos que no están directamente integrados al kernel, para que sean levantados antes del proceso init.

12. ¿Cuál es la razón por la que, una vez compilado el nuevo kernel, es necesario reconfigurar el gestor de arranque que tengamos instalado?

    Es para que el gestor de arranque indexe el kernel.

13. ¿Qué es un módulo del kernel? ¿Cuáles son los comandos principales para el manejo de módulos del kernel?

    Es un fragmento de código que puede cargarse/descargarse en el mapa de memoria del SO (kernel) bajo demanda. Permiten extender la funcionalidad del kernel en tiempo de ejecución (sin necesidad de reiniciar el sistema). Todo su codigo se ejecuta en modo kernel (privilegiado). Cualquier error que pudiera surgir en algún módulo, colgaría el SO. Permiten que el kernel se desarrolle bajo un diseño mas modular. Los modulos disponibles se ubican en /lib/modules/version del kernel. Con el comando lsmod es posible ver qué modulos están cargados.

14. ¿Qué es un parche del kernel? ¿Cuáles son las razones principales por las cuáles se deberían aplicar parches en el kernel? ¿A través de qué comando se realiza la aplicación de parches en el kernel?

    Es un mecanismo que permite aplicar actualizaciones NO incrementales sobre la version base. Se basa en archivos _diff_ (archivos de diferencia), que indican qué agregar y qué quitar. Se aplican sobre la versión base. Permiten agregar funcionalidad (nuevos drivers, correcciones menores, etc.). A veces puede resultar más sencillo descargar el archivo de diferencia y aplicarlo en vez de descargar todo el código de la nueva versión.

## Ejercicio taller: Compilación del kernel Linux

El propósito del siguiente ejercicio es el de guiar al alumno en el proceso de compilación del kernel de GNU/Linux. Si bien los siguientes ejercicios permiten agregar determinada funcionalidad al kernel, es aconsejable que los alumnos investiguen las distintas opciones con el fin de adquirir experiencia adicional. Para la realización de este taller se ha utilizado la versión 5.6.2 del kernel de GNU/Linux. Aquel alumno que decida utilizar otra versión deberá descargar el código fuente y los parches del kernel correspondientes de acuerdo a la versión utilizada.

A través de los siguientes pasos agregaremos nueva funcionalidad a nuestro kernel de GNU/Linux asumiendo que la misma no se encuentra soportada en nuestro kernel actual. Dentro de la funcionalidad a agregar se encuentran:

+ El soporte para sistemas de archivos BTRFS.
+ Soporte para la utilización de dispositivos de bloques loopback.
+ Actualizaremos una versión de nuestro kernel, a través de la aplicación de parches.

Aquel alumno que utilice la máquina virtual provista por la Cátedra, no tendrá que instalar el software necesario para realizar la compilación, ya que el mismo se encuentra incluido. En caso de no utilizar la máquina virtual provista, se deberá realizar la instalación del software requerido para la instalación (librerías, compiladores, etc.).

Credenciales de acceso de la máquina virtual:

Modo | Usuario | Contraseña
:---:|:---:|:---:
 Regular | so | so
 Privilegiado | root | toor

El usuario so no cuenta con privilegios para ejecutar sudo por lo cuál para ejecutar comandos que requieran estos privilegios será necesario ejecutar su - o su -c 'comando arg ...'.

En los comandos de ejemplo de esta práctica, se verá que algunos comandos empiezan con $ y otros con #. Estos símbolos representan el prompt del usuario y no deben escribirse cuando se copie el comando. El símbolo $ significa que el comando debe ejecutarse con un usuario normal. En el caso de la máquina virtual, es el usuario so. El símbolo # significa que el comando debe ejecutarse con privilegios de usuario root.

1. Descargue el archivo btrfs.image.xz publicado en la página web de la cátedra (en la máquina virtual provista por la cátedra se encuentra en el directorio /home/so).

   Este archivo representa lo que se conoce como loop device. Básicamente son archivos regulares que pueden ser tratados como dispositivos de bloques, donde uno puede crear un sistema de archivos dentro de ellos y montarlos como si montáramos cualquier otro dispositivo de bloques como, por ejemplo, particiones de un disco rígido.

    Específicamente, btrfs.image.xz es un archivo de 100MiB formateado con el filesystem BTRFS y luego comprimido con la herramienta xz. Por lo tanto, el primer paso para poder acceder a él será descomprimirlo con ```unxz -kv``` o ```unxz```. Para poder acceder a la información de este pseudo dispositivo, será necesario montarlo en un directorio y para esto es necesario que el kernel soporte el sistema de archivos BTRFS.

2. La tarea que tendremos que realizar en este ejercicio consiste en intentar montar el archivo _btrfs.image_ usando el kernel compilado en la práctica (versión 5.6.2) y ejecutar el script que se encuentra dentro de este filesystem.

    El script pedirá sus datos y los enviará a un servidor donde quedarán registrados. Es importante para la aprobación de esta entrega que el script se ejecute en la versión del kernel que se pide en esta práctica como evidencia que realizaron lo pedido.

    1. Descargaremos este archivo en algún directorio determinado de nuestro File System, como por ejemplo nuestro directorio personal, $HOME.

        Eso ya está hecho.

    2. Verificaremos que dentro del directorio _/mnt_ exista al menos un directorio donde podamos montar nuestro pseudo dispositivo. Si no existe el directorio, crearlo. Por ejemplo podemos utilizar el nombre _/mnt/btrfs/_.

        Ante la duda, meté ```mkdir /mnt/btrfs```. Si falla, el directorio ya existe.

    3. A continuación montaremos nuestro dispositivo utilizando los siguientes comandos:

        **NOTA DE LIHUEL**: EL COMANDO QUE SIGUE VA A FALLAR, NO TE MATES BUSCANDO EL ERROR POR 5 MINUTOS COMO FUE MI CASO.

        ```bash
        su -
        mount -t btrfs -o loop $HOME/btrfs.image /mnt/btrfs/
        ```

    4. ¿Puede explicar qué sucedió? _Nota: si estás usando la máquina virtual de la cátedra, el comportamiento esperado es que la invocación a **mount** falle._

        Bueno, acá recién te enterás que el comando falla. Falló porque acordate que la máquina virtual que te dieron no tiene un kernel que soporte btrfs (acordate que eso es lo que único que tenés que hacer: que el kernel logre soportarlo), ergo, te vas a volver mono tratando de que monte el filesystem.

3. Descargue el código fuente del kernel a compilar, el parche correspondiente a la versión a la que se quiere llevar el código fuente, arme la estructura de directorios y aplique el parche (en la máquina virtual provista por la cátedra se encuentran en el directorio /usr/src).

    1. Descargar el código fuente:

        ```bash
        cd /usr/src/
        su -
        wget https://kernel.org/pub/linux/kernel/v5.x/linux-5.6.tar.
        xz
        exit
        ```

    2. Descargar el parche correspondiente a la versión a la que quiere llevar su código fuente. Si se está usando la máquina virtual provista por la cátedra, el parche también se encuentra en /usr/src:

        **NOTA DE LIHUEL**: en mi caso, estaba en el propio directorio home.

        ```wget https://kernel.org/pub/linux/kernel/v5.x/patch-5.6.2.xz```

    3. Descomprimir el kernel utilizando usuario sin privilegios de root (en la virtual, el usuario so), en un nuevo directorio en el $HOME del usuario.

        ```bash
        mkdir $HOME/kernel/
        cd $HOME/kernel/
        tar xvf /usr/src/linux-5.6.tar.xz
        ```

    4. Aplique el parche descargado a nuestro código fuente. Esta tarea la haremos ubicados en el directorio de nuestro código fuente y a través de la herramienta patch, verificando que no arroje errores al finalizar.

        ```bash
        cd $HOME/kernel/linux-5.6
        xzcat /usr/src/patch-5.6.2.xz | patch -p1
        ```

        En mi caso, el patch arrojó error una vez porque justo interrumpí el proceso. Si sos igual de bobo que yo, no te preocupes, todo tiene solución: hace un make clean, volvé a meter el solo el comando patch y problema resuelto.

4. Configurar el código fuente del kernel para su compilación. Esta tarea se realiza a través de los comandos vistos en el ejercicio 8 de la primera parte de esta práctica. En primer lugar, se deberá partir de la configuración del kernel que está corriendo actualmente. Por convención, esta configuración se encuentra ubicada en /boot. La copiaremos al directorio donde se encuentra el kernel parcheado.

    ```cp /boot/config-$(uname -r) $HOME/kernel/linux-5.6/.config```

    En este caso utilizaremos la herramienta menuconfig. Para ello ejecutaremos:

    ```bash
    cd $HOME/kernel/linux-5.6
    make menuconfig
    ```

    Este comando pondrá en pantalla un menú de configuración del código fuente del kernel donde podremos seleccionar diversas opciones dependiendo de nuestro hardware y necesidades. Dentro de las opciones que debemos seleccionar con el fin de cumplir nuestro objetivo se encuentran:

    + Soporte para sistemas de archivos btrfs: En la opción File Systems, tendremos que seleccionar Btrfs filesystem support.
    + Soporte para dispositivos de Loopback: En la opción Device Drivers ! Block Devices, tendremos que seleccionar Loopback device support.

        Acá no hay mucho para explicar: yo lo hice siguiendo estas instrucciones al pie de la letra y no tuve ningún problema

    La forma de movernos a través de este menú es utilizando las flechas del cursor, la tecla Enter y la barra espaciadora. A través de esta última podremos decidir sobre determinada opción si la misma será incluida en nuestro kernel, si será soportada a través de módulos, o bien si no se dará soporte a la funcionalidad ([*], [M], [] respectivamente). Una vez seleccionadas las opciones necesarias, saldremos de este menú de configuración a través de sla opción Exit, guardando los cambios.

5. Luego de configurar nuestro kernel, nos dispondremos a realizar la compilación del mismo y sus módulos.

    Para realizar la compilación deberemos ejecutar:

    ```make -jX```

    X deberá reemplazarse por la cantidad de procesadores con los que cuente su máquina. En máquinas con más de un procesador o núcleo, la utilización de este parámetro puede acelerar mucho el proceso de compilación, ya que ejecuta X jobs o procesos para la tarea de compilación en forma simultánea. Este comando debemos ejecutarlo ubicados dentro del directorio donde se encuentra el código fuente del kernel descargado ($HOME/kernel/linux-5.6). La ejecución de este último puede durar varios minutos, o incluso horas, dependiendo del tipo de hardware que tengamos en nuestra PC y las opciones que hayamos seleccionado al momento de la configuración. Una vez finalizado este proceso, debemos verificar que no haya arrojado errores. En caso de que esto ocurra debemos verificar de qué tipo de error se trata y volver a la configuración de nuestro kernel para corregir los problemas. Una vez corregidos, tendremos que volver a compilar nuestro kernel. Previo a esta nueva compilación, debemos correr el comando make clean para eliminar pasos inconclusos de compilaciones anteriores.

    **NOTA DE LIHUEL**: acá me gustaría hacer una aclaración especial acerca del make -jX. Lo cierto es que deben de seguirse algunos pasos para que realmente funcione. El primer paso es saber cuántos nucleos tiene tu máquina. No vamos a usarlos todos (a menos que quieras que el procesador explote y te vuele el gabinete xD): al número de procesadores, dividilo en dos y restale 1. La idea de esto, es usar un número razonable sin que explote todo, digamos. Una vez que definimos el número a usar, vamos a la pantalla principal de VirtualBox, botón derecho en la máquina virtual de SO y apretás en Opciones. De ahí, vas a la pestaña Sistema y definís la cantidad de procesadores a usar. Tené en cuenta que la máquina virtual tiene que estar apagada para que puedas toquetear esa opción. Una vez asignados los procesadores a VirtualBox, recién ahí metele en comando make -jX y reemplazá X por la cantidad de procesadores que definiste antes. Estoy explicando esto, porque me pasó de intentar ejecutar el make -jX sin asignar los procesadores (suponiendo que VirtualBox se encargaba de todo) y no notaba diferencia entre ejecutar el comando con o sin ese parámetro. Doy fe de que si asignas 2 o 3 procesadores, el proceso make va como piña y, a lo mejor, en 10 minutos lo tenés ready. Si no definís ese parámetro, solo usa un procesador y puede llegar estar una hora, aproximadamente.

6. Finalizado este proceso, debemos reubicar las nuevas imágenes en los directorios correspondientes, instalar los módulos, crear una imagen initramfs y reconfigurar nuestro gestor de arranque.

    1. En primer lugar, para realizar la instalación de los módulos ejecutaremos:

        ```make modules_install```

        Recordar que debemos estar ubicados en el directorio $HOME/kernel/linux-5.6. Este comando copiará los módulos compilados al directorio /lib/modules/5.6.2.

    2. Para reubicar la imagen del kernel disponemos de al menos dos métodos.
       1. Utilizar el siguiente comando:

            ```make install```

            Este comando reubicará todos los archivos creados durante el proceso de compilación. Este es el método recomendado puesto que, entre otros detalles, no sobre-escribirá ningún archivo en caso de que el kernel que estemos instalando tenga la misma versión que el que está corriendo.

            **NOTA DE LIHUEL**: yo usé este por las dudas. Tengo que reconocer que por suerte me salió bien a la primera, pero por las dudas este método no sobreescribe el kernel existente. Y eso es algo importante a tener en cuenta por dos razones:
            + La primera, si la manqueas, podés bootear con el kernel que ya tenías y volvés a compilar.
            + La segunda, la forma de bootear el kernel recién compilado. Cuando yo compilé la primera vez y reseteé la máquina virtual (la cuál, a propósito, requiere que le seteen en la variable PATH el directorio /sbin/ para encontrar varios comandos fundamentales, como el reboot. Más abajo voy a explicar el proceso, pero si por ahora querés zafar, si el comando no te lo reconoce por las dudas ejecutá /sbin/COMANDO y fijate si de ese modo funciona), lo cierto es que inicié el sistema de manera normal. Error de novato. Naturalmente, **el kernel que inició fue el que usé para la compilación, no el que compilé** ¿Se acuerdan que más arriba dije que, si la manqueas, podés bootear con el kernel que ya tenías y volvés a compilar? Bueno, efectivamente, al no sobreescribir el /boot lo que estaremos booteando normalmente es el kernel que usamos para la compilación **¿Y el kernel que compilamos?** Ahí está lo interesante: va a estar ubicado en Opciones Avanzadas en el menú GRUB. Aparentemente, el ```make install``` crea la entrada en esa sección del menú y no en el menú principal que es donde uno se lo esperaría. Si tenés esto en cuenta, a lo mejor te ahorras recompilar un kernel que funcionaba desde un principio.

       2. Este método consiste en hacer lo mismo que el anterior, pero de forma manual. Se deben realizar la copia manual de cada uno de los archivos creados. Para ello debemos ejecutar los siguientes comandos:

            ```bash
            su -
            cp -i $HOME/kernel/linux-5.6/arch/x86_64/boot/bzImage /boot/vmlinuz-5.6.2
            cp -i $HOME/kernel/linux-5.6/System.map /boot/System.map-5.6.2
            cp -i $HOME/kernel/linux-5.6/.config /boot/config-5.6.2
            ```

       El primer comando copiará la imagen del kernel, el segundo copiará el archivo de mapa de símbolos correspondiente a la nueva imagen del kernel. El último comando nos permitirá mantener una copia del archivo de configuración con el cual fue compilado nuestro kernel. Si bien no es necesario mantener este último archivo, es recomendable hacerlo, con el fin de mantener el orden y una documentación de las opciones de compilación utilizadas en la imagen compilada.

    3. Si en el inciso anterior se utilizó la opción de ```make install```, entonces podremos saltear este paso. De lo contrario, para la creación de la imagen initramfs utilizaremos el comando:

        ```mkinitramfs -o /boot/initrd.img-5.6.2 5.6.2```

    4. El último paso previo a realizar la prueba de nuestra imagen del kernel, es reconfigurar nuestro gestor arranque de manera tal que podamos bootear con la nueva imagen. Suponiendo que el gestor de arranque que tenemos instalado es GRUB en su versión 2, ejecutaremos el comando:

        ```update-grub2```

        Este comando se encargará de agregar las entradas correspondientes en el archivo de configuración /boot/grub/grub.cfg. Nuevamente, si utilizamos make install para instalar el kernel, entonces es posible saltear este paso.

    **NOTA DE LIHUEL**: si no seteaste el /sbin en la variable path. No te va a encontrar el comando. Para agregar el /sbin al path, hay que toquetear el archivo .bashrc que está en la carpeta home del usuario. Como dato que puede servirte en el futuro (o no), el .bashrc es un archivo que se ejecuta inmediatamente el usuario inicia sesión. Lo que vamos a hacer es abrirlo con un editor de texto (ejecutá ```nano /home/USER/.bashrc```) y al final del archivo escribile PATH=/sbin:$PATH. Esa línea lo que significa es que el valor de la variable PATH va a ser igual a /sbin más lo que ya tuviera adentro de antes. Una vez escribiste esa línea, guardás el archivo y reinicias con ```/sbin/reboot``` (acordate que hasta que no reinicies la sesión, no te va a tomar los cambios ;)). Si todo lo anterior te parece muy jodido de hacer, simplemente, si el comando no te lo encuentra, probá ejecutando ```/sbin/COMANDO```. Lo que hace eso es ejecutar el comando desde ese path, sin usar la variable que no está bien seteada.

7. Como último paso, a través del comando reboot, reiniciaremos nuestro equipo y probaremos el nuevo kernel recientemente compilado. Al momento de cargarse nuestro gestor de arranque, veremos una nueva entrada que hace referencia al nuevo kernel. Para bootear, seleccionamos esta entrada y verificamos que el sistema funcione correctamente. En caso de que el sistema no arranque con el nuevo kernel, tendremos la opción de volver a bootear con nuestro kernel anterior para corregir los errores y realizar una nueva compilación.

    **NOTA DE LIHUEL**: acordate de dos cosas:
       + La primera, que no vas a poder ejecutar el reboot si antes no seteaste la variable de entorno PATH. En ese caso, modificá el archivo .bashrc; o, simplemente, rebootea con ```/sbin/reboot```.
       + La segunda, que una vez habiendo rebooteado, si usaste el ```make install```, la imagen del kernel compilado va a estar en Opciones Avanzadas en el GRUB. Ojo con eso porque sino te vas a volver mono tratando de encontrar el kernel y vas a hacer la recompilación una y otra vez y siempre vas a bootear con el kernel que usaste para hacer la recompilación. Teoricamente, este problema no se daría con la segunda opción de hacerlo a mano, pero la verdad es que no lo probé y no quiero chamuyar.

8. Repita el procedimiento del inciso dos. ¿Qué sucede ahora?

    Como nuestro kernel tiene soporte para este tipo de File System, entonces, ingresando a /mnt/btrfs, encontraremos la información contenida en este pseudo dispositivo.

    Ahora sí te va a andar :D. Si no te llega a andar:
    + O bien booteaste con el kernel que usaste para la compilación y no con el que acabás de compilar.
    + O bien algo salió mal y vas a tener que hacer la compilación de 0 :(). Tratá de que esta opción sea la última y que no haya nada que te hayas olvidado en el camino.

9. Ejercicio obligatorio

    Ejecute el script que se encuentra dentro de /mnt/btrfs usando la versión del kernel 5.6.2 y complete sus datos, los mismos serán reportados por el script a través de Internet a la cátedra para registrar realización de este ejercicio.

    Una vez que compilaste el kernel, lo más difícil ya lo hiciste. Ahora queda montar lo que ya antes habías descomprimido en la carpeta ya antes creada. Para eso, ejecutá ```mount /home/so/btrfs.image /mnt/btrfs```. Si hacés ls ```/mnt/btrfs``` vas a ver un solo archivo entregap2.sh o parecido. Ese es el que tenés que ejecutar. Y ahí si: ejecutá el archivo con ```/./mnt/btrfs/entregap2.sh```. Algo más: si llega a decirte que no tiene permisos de ejecución, simplemente dale permisos con ```chmod 777 /mnt/btrfs/entregap2.sh```. Eso es todo! Poné tu nombre y apellido y tu legajo y ya está.
