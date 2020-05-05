# Práctica 2

### Conceptos teóricos

El propósito de esta primera sección de la práctica es introducir los conceptos preliminares que
necesitará el alumno, para desarrollar la actividad práctica del apartado B de la presente guía
de estudio.

1. ¿Qué es el kernel de GNU/Linux? ¿Cuáles son sus funciones principales dentro del Sistema Operativo?

    Es un programa que ejecuta programas y gestiona los dispositivos de hardware del sistema. Es el encargado de que el software y el hardware puedan trabajar juntos. Sus principales funciones son administrar la memoria principal y la CPU.

2. Indique una breve reseña histórica acerca de la evolución del kernel de GNU/Linux.

    En 1991 Linus Torvalds inicia la programación del kernel Linux basado en Minix[2](Clon de Unix desarrollado por Tanembaum en 1987 con el n de crear un SO de uso didáctico).<br/>
    El 5 de octubre de 1991, se anuncia la primera versión "oficial" de Linux (0.02).<br/>
    En 1992, con la release de la versión 0.12, se decide cambiar a una licencia GNU.<br/>
    En marzo de 1994, Torvalds considera que todos los componentes del kernel estaban suficientemente maduros y lanza la versión 1.0.<br/>
    En el año 1995, Linux se porta a arquitecturas DEC Alpha y Sun SPARC. Con el correr de los años se portó a otra decena de arquitecturas.<br/>
    En mayo de 1996, se decide adoptar a Tux como mascota oficial de Linux.<br/>
    En julio de 1996, se lanza la versión 2.0 y se define un sistema de nomenclatura. Se desarrolló hasta febrero de 2004 y terminó con la versión 2.0.40. Esta versión comenzó a brindar soporte a sistemas multiprocesadores.<br/>
    En 2001 se lanza la versión 2.4 y se deja de desarrollar a fines del 2010 con la 2.4.37.11. La versión 2.4 fue la que catapultó a GNU/Linux como un sistema operativo estable y robusto.<br/>

3. Explique brevemente la arquitectura del kernel de GNU/Linux teniendo en cuenta: tipo de kernel, módulos, portabilidad, etc.

    + Tipo de kernel: monolítico, es decir, el sistema operativo completo trabaja en el espacio dedicado al kernel. El modelo monolítico difiere de otras arquitecturas de sistemas operativos en que solo define una interfaz virtual de alto nivel sobre el hardware de la computadora. Un conjunto de primitivas o llamadas al sistema implementan todos los servicios del sistema operativo, como la gestión de procesos, la concurrencia y la gestión de memoria. Los controladores de dispositivo se pueden agregar al kernel como módulos.
    + Módulos: pueden cargarse(y descargarse) dinámicamente en tiempo de ejecución. Esta modularidad del sistema operativo está en el nivel binario (imagen) y no en el nivel de arquitectura. En términos prácticos, cargar módulos dinámicamente es simplemente una forma más flexible de manejar la imagen del sistema operativo en tiempo de ejecución, en lugar de reiniciar con una imagen diferente del sistema operativo. Los módulos permiten una fácil extensión de las capacidades de los sistemas operativos según sea necesario. Los módulos cargables dinámicamente incurren en una pequeña sobrecarga en comparación con la construcción del módulo en la imagen del sistema operativo. Sin embargo, en algunos casos, cargar módulos dinámicamente (según sea necesario) ayuda a mantener al mínimo la cantidad de código que se ejecuta en el espacio del kernel. Es decir, un módulo descargado no necesita almacenarse en una memoria de acceso aleatorio escasa.
    + Portabilidad: si bien no fue pensado para ser portable, hoy en día Linux fue portado a múltiples arquitecturas, desde ARM hasta IBM z / Architecture. Los usuarios de Amiga realizaron el primer port más allá de la arquitectura 386 original de Linux en la plataforma Motorola 68000, quienes lograron esto al reemplazar las partes principales del núcleo. Se considera que tal port fue el ímpetu que Torvalds necesitaba para liderar una importante reestructuración del código del kernel para facilitar el transporte a arquitecturas informáticas competidoras. El primer puerto respaldado por Linux fue a la plataforma DEC Alpha AXP de 64 bits que se demostró en DECUS en mayo de 1995, soportando tanto 386 como Alpha en un solo árbol fuente. DEC fue responsable de suministrar el hardware necesario para Torvalds para habilitar un puerto de Linux a 64 bits ese mismo año.

4. ¿Cuáles son los cambios que se introdujeron en el kernel a partir de la versión 3.0? ¿Cuál fue la razón por la cual se cambió de la versión 2 a la 3? ¿Y la razón para el cambio de la versión 3 a la 4?

    * El 17 Julio de 2011 se lanza la versión 3.0:
        + No agrega mayores cambios. La decisión del cambio son los 20 años del SO y no superar los 40 números de revisión.
        + Totalmente compatible con Kernel 2.6.
        + Termina con la versión 3.8.30.
        + Provee mejoras en Virtualización y FileSystems.
    * El 12 de Abril de 2015 se lanza la versión 4.0:
        + Una de sus principales mejoras es la posibilidad de aplicar parches y actualizaciones sin necesidad de reiniciar el SO.
        + Soporte para nuevas CPU.
        + La versión actual es 4.5.

5. ¿Cómo se define el versionado de los kernels de GNU/Linux?

    La anatomía de una versión a partir de 2.6 se ve de la siguiente forma:<br/>

    ```A.B.C.[D]```
    + A Denota Versión. Cambia con menor Frecuencia. en 1994 (versión 1.0), en 1996 (versión 2.0), en 2011 (versión 3.0) y en 2015 (versión 4.0).
    + B Denota revisión mayor. Antes de la versión 2.6, los números impares indicaban desarrollo, los pares producción.
    + C Denota revisión menor. Solo cambia cuando hay nuevos drivers o características.
    + D Se utiliza cuando se corrige un grave error sin agregar nueva funcionalidad.

    En el año 2011, cuando se pasó de la versión 2.6.39 a la 3.0, se eliminó el número de revisión mayor (B).

    La anatomía del versionado anterior a 2.6 es la que sigue:

    ```X.Y.Z```
    + X Indica serie principal. Cambia cuando su funcionalidad sufre un cambio muy importante.
    + Y Indica si es una versión de producción o desarrollo.
        * Números Y pares indicaban versión en Producción (estable).
        * Números Y impares indicaban versión en Desarrollo.
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
    + make xconfig: interfaz gráfica utilizando un sistema de ventanas. No todos los sistemas tienen instalado X.
    + make menuconfig: este modo utiliza ncurses, una librería que permite
    generar una interfaz con paneles desde la terminal. Generalmente el más utilizado.

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

        La utilidad make determina automáticamente qué partes de un programa grande necesitan ser recompiladas y emitirá los comandos para recompilarlas. Make suele utilizarse para programas en C, pero lo cierto es que puede usarse make con cualquier lenguaje de programación cuyo compilador se pueda ejecutar con un comando de shell. De hecho, make no se limita a los programas: puede usarse para describir cualquier tarea en la que algunos archivos deben actualizarse automáticamente de otros cuando los otros cambien.
        La opción -j(_jobs_) especifica el número de trabajos (comandos) para ejecutar simultáneamente. Si hay más de una opción -j, la última es efectiva. Si la opción -j se proporciona sin un argumento, make no limitará el número de trabajos que pueden ejecutarse simultáneamente.

    + make modules (utilizado en antiguos kernels, actualmente no es necesario)

        Compila archivos individuales para cada item que marcado con M durante la configuración del kernel. El código objeto está vinculado a su núcleo recién construido. (Para los items marcados con Y, estos ya son parte de vmlinuz, y para los items marcados con N, se omiten).

    + make modules_install

        Instala los módulos del kernel en /lib/modules o /lib/modules/<version>.

    + make install

        Instala el kernel construido en /vmlinuz.

10. Una vez que el kernel fue compilado, ¿Dónde queda ubicada su imagen? ¿Dónde debería ser reubicada? ¿Existe algún comando que realice esta copia en forma automática?

    Al terminar el proceso de compilación, la imagen del kernel quedará ubicada en ```directorio-del-código/arch/arquitectura/boot/```. El próximo paso, entonces, es instalar el kernel y otros archivos en el directorio ```/boot```. ```sudo make install``` realiza la copia automáticamente.

11. ¿A qué hace referencia el archivo initramfs? ¿Cuál es su funcionalidad? ¿Bajo qué condiciones puede no ser necesario?

    Un initramfs es un sistema de archivos temporal que se monta durante el arranque del sistema. Contiene ejecutables, drivers y módulos necesarios para lograr iniciar el sistema. Luego del proceso de arranque el disco se desmonta. Es necesario para los módulos que no están directamente integrados al kernel, para que sean levantados antes del proceso init.
    //CORREGIR

12. ¿Cuál es la razón por la que una vez compilado el nuevo kernel, es necesario reconfigurar el gestor de arranque que tengamos instalado?

    Es para que el gestor de arranque indexe el kernel.

13. ¿Qué es un módulo del kernel? ¿Cuáles son los comandos principales para el manejo de módulos del kernel?

    Es un fragmento de código que puede cargarse/descargarse en el mapa de memoria del SO (Kernel)bajo demanda. Permiten extender la funcionalidad del Kernel en tiempo de ejecución (sin necesidad de reiniciar el sistema). Todo su codigo se ejecuta en modo Kernel (privilegiado). Cualquier error que pudiera surgir en algún módulo, colgaría el SO. Permiten que el kernel se desarrolle bajo un diseño mas modular. Los modulos disponibles se ubican en /lib/modules/version del kernel. Con el comando lsmod es posible ver qué modulos están cargados.

14. ¿Qué es un parche del kernel? ¿Cuáles son las razones principales por las cuáles se deberían aplicar parches en el kernel? ¿A través de qué comando se realiza la aplicación de parches en el kernel?

    Es un mecanismo que permite aplicar actualizaciones NO incrementales sobre la version base. Se basa en archivos _diff_ (archivos de diferencia), que indican qué agregar y qué quitar. Se aplican sobre la versión base. Permiten agregar funcionalidad (nuevos drivers, correcciones menores, etc.). A veces puede resultar más sencillo descargar el archivo de diferencia y aplicarlo en vez de descargar todo el código de la nueva versión.

### Ejercicio taller: Compilación del kernel Linux

2. La tarea que tendremos que realizar en este ejercicio consiste en intentar montar el archivo _btrfs.image_ usando el kernel compilado en la práctica (versión 5.6.2) y ejecutar el script que se encuentra dentro de este filesystem.

    El script pedirá sus datos y los enviará a un servidor donde quedarán registrados. Es importante para la aprobación de esta entrega que el script se ejecute en la versión del kernel que se pide en esta práctica como evidencia que realizaron lo pedido.

    1. Descargaremos este archivo en algún directorio determinado de nuestro File System, como por ejemplo nuestro directorio personal, $HOME.

        Eso ya está hecho.

    2. Verificaremos que dentro del directorio _/mnt_ exista al menos un directorio donde podamos montar nuestro pseudo dispositivo. Si no existe el directorio, crearlo. Por ejemplo podemos utilizar el nombre _/mnt/btrfs/_.

        Ante la duda, meté ```mkdir /mnt/btrfs```. Si falla, el directorio ya existe.

    3. A continuación montaremos nuestro dispositivo utilizando los siguientes comandos:
        **NOTA: EL COMANDO QUE SIGUE VA A FALLAR, NO TE MATES BUSCANDO EL ERROR POR 5 MINUTOS COMO FUE MI CASO.**
        ```bash
        su -
        mount -t btrfs -o loop $HOME/btrfs.image /mnt/btrfs/
        ```

    4. ¿Puede explicar qué sucedió? _Nota: si estás usando la máquina virtual de la cátedra, el comportamiento esperado es que la invocación a **mount** falle._
        Bueno, acá recién te enterás que el comando falla. Falló porque acordate que la máquina virtual que te dieron no tiene un kernel que soporte btrfs(acordate que eso es lo que único que tenés que hacer), ergo, te vas a volver mono tratando de que monte el filesystem.

