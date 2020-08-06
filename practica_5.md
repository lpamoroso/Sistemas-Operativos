# Práctica 5

## Parte 1: Conceptos teóricos

1. Defina virtualización. Investigue cuál fue la primer implementación que se realizó.

    Es una técnica que permite realizar "abstracción" de recursos de la computadora. Hoy en día también se virtualizan las redes, datacenters, entre otros. Es una capa abstracta que desacopla el hardware físico del sistema. Permite ocultar detalles técnicos a través de la encapsulación; y, entre otras cosas, que una computadora pueda realizar el trabajo de varias a través de la compartición de recursos de un único dispositivo de hardware.

    La primera virtualización la llevó a cabo IBM en 1964 en el Centro Científico de Cambridge con el desarrollo de la CP-40, la precursora de la CP-67. CP-40 era un sistema operativo máquina virtual/memoria virtual de tiempo compartido para el IBM System/360 modelo 67. CP-40 corrió varias instancias de sistemas operativos cliente - en particular CMS, el Sistema de monitoreo de Cambridge, construidos como parte del mismo esfuerzo.

2. ¿Qué diferencia existe entre virtualización y emulación?

    Un emulador es un programa de software que simula la funcionalidad de otro programa o un componente de hardware. Dado que implementa funcionalidad por software, proporciona una gran flexibilidad y la capacidad de recopilar información muy detallada acerca de la ejecución.

    El programa incluso podría ser escrito para una arquitectura diferente a aquella sobre la cual el emulador se ejecuta –como ser, ejecutar un programa de Android escrito para ARM en un emulador sobre un host x86. El inconveniente de la emulación es que la capa de software incurre en una penalización en el rendimiento, que debe ser cuidadosamente administrada a fin de crear un sistema escalable.

    Con la virtualización, el programa huésped se ejecuta realmente en el hardware subyacente. El software de virtualización (VMM por Virtual Machine Monitor) sólo media en los accesos de las diferentes máquinas virtuales al hardware real. Así, éstas son independientes, y pueden ejecutar programas a velocidad casi nativa.

    Sin embargo, un programa en ejecución ocupa los recursos físicos reales, y como resultado, el VMM y el sistema de análisis no pueden ejecutarse simultáneamente, volviendo un desafío a la recolección detallada de datos. Además, es difícil lograr ocultar por completo el VMM de las rutinas de detección embebidas en las muestras de malware.

3. Investigue el concepto de hypervisor y responda:

    1. ¿Qué es un hypervisor?

        Un hipervisor es una plataforma que permite aplicar diversas técnicas de control de virtualización para utilizar, al mismo tiempo, diferentes sistemas operativos (sin modificar o modificados, en el caso de la paravirtualización) en una misma computadora.

    2. ¿Qué beneficios traen los hypervisors? ¿Cómo se clasifican?

        Algunas ventajas de los hipervisores es que son los que se encargan de la multiprogramación y ofrecen varias máquinas virtuales hacia arriba. Hay dos tipos:
        * Tipo 1: se ejecuta sobre el hardware. El hypervisor se ejecuta en modo kernel real. El sistema operativo invitado es en modo kernel "virtual" (pero es modo usuario).
        * Tipo 2: se ejecuta como un programa de usuario sobre un sistema operativo anfitrión. Arriba de él, están los sistema operativos invitados. Interpreta solo un conjunto de instrucciones de máquina. El sistema operativo anfitrión es quién se ejecuta sobre el hardware.

    3. Indique por qué un hypervisor de tipo 1 no podría correr en una arquitectura sin tecnología de virtualización. ¿Y un hypervisor de tipo 2 en hardware sin tecnología de virtualización?

        El hipervisor de tipo 2 ejecuta en modo usuario como un proceso más del sistema operativo anfitrión. Las instrucciones sensibles se sustituyen por llamadas a procedimiento que emulan las instrucciones. El verdadero hardware **NUNCA** ejecuta aquellas sensibles que emite el sistema operativo invitado: las ejecuta siempre como llamadas el hypervisor. Todo el hardware es emulado, inclusive la CPU.

4. Investigue el concepto de paravirtualización y responda:

    1. ¿Qué es la paravirtualización?

        La paravirtualización es una técnica que permite virtualizar por software sistemas operativos. El programa paravirtualizador presenta una interfaz de manejo de máquinas virtuales. Cada máquina virtual se comporta como un computador independiente, por lo que permite usar un sistema operativo o varios por computador emulado.

    2. ¿Sería posible utilizar paravirtualización en sistemas operativos como Windows o iOS? ¿Por qué?

        La paravirtualización requiere que el sistema operativo invitado sea portado de manera explícita para la API. Una distribución de un sistema operativo convencional que no soporte paravirtualización no puede ser ejecutada ni visualizada en un monitor de máquina virtual VMM.

    3. Mencione algún sistema que implemente paravirtualización.

        Windows XP tiene una versión paravirtualizable, por ejemplo, así como también GNU/Linux. El tema con Windows XP es que no puede distrubuirse fuera de XenSource ya que la adaptación de Windows fue realizada bajo los términos del Academic Licensing Program, que permitía a los investigadores acceso y modificación al código fuente, pero no su redistribución.

    4. Defina VMI.

        VMI (Virtual Machine Interface o Interfaz de Máquina virtual, en español), es una especificación extensible claramente definida para la comunicación del sistema operativo con el hipervisor.

    5. ¿Qué beneficios trae con respecto al resto de los modos de virtualización?

        VMI ofrece un gran rendimiento sin requerir que los desarrolladores del kernel conozcan conceptos que solo son relevantes para el hipervisor. Como consecuencia, es posible seguir el ritmo acelerado del desarollo del kernel de Linux, ya que las nuevas versiones del kernel puede ser trivialmente paravirtualizadas. Con VMI, un solo binario del kernel de Linux puede ejecutarse en una máquina nativa y en uno o más hipervisores.

    6. Investigue si VMI podría correr sobre hypervisors de tipo 1 ó 2, y justifique por qué.

        VMI puede correr sobre cualquier hypervisor ya que es una interface para permitir la paravirtualización, no depende sobre que hypervisor ejecuta.

5. Investigue sobre _containers_ en el ámbito de la virtualización y responda:

    1. ¿Qué son?

        Son grupos de procesos corriendo sobre el mismo kernel que pueden aislarse en entornos que parecen máquinas separadas. No son una máquina virtual sino entornos virtuales, con su propio espacio de procesos y de nombres. Se basan en cgroups.

    2. ¿Dependen del hardware subyacente?

        No, ya que lo que emulan es un sistema operativo y no el hardware subyacente.

    3. ¿Qué lo diferencia por sobre el resto de las tecnologías estudiadas?

        Que, por ejemplo, es posible controlar la asignación de recursos: puedo asignarle a un container más ancho de banda de red, o tiempo de CPU. También es posible _freezar_ un container (funcionalidad muy útil para migrciones, por cierto), de modo que todos los procesos de éste se suspenden.

    4. Investigue qué funcionalidades son necesarias para poder implementar containers.

        Solo es necesario un motor para el container.

## Parte 2: chroot, Control Groups, Namespaces y Containers

Debido a que para la realización de la práctica es necesario tener más de una terminal abierta simultáneamente tenga en cuenta la posibilidad de lograr esto mediante alguna alternativa (ssh, terminales gráficas, etc.)

### chroot

En algunos casos suele ser conveniente restringir la cantidad de información a la que un proceso puede acceder. Uno de los métodos más simples para aislar servicios es chroot, que consiste simplemente en cambiar lo que un proceso, junto con sus hijos, consideran que es el directorio raíz, limitando de esta forma lo que pueden ver en el sistema de archivos. En esta sección de la práctica, se preparará un árbol de directorios que sirva como directorio raíz para la ejecución de una shell.

1. Crear un subdirectorio llamado _sobash_ dentro del directorio root. Intente ejecutar el comando ```chroot /root/sobash``` ¿Cuál es el resultado? ¿Por qué se obtiene ese resultado?

    Es posible crear un directorio el /root ejecutando ```mkdir /root/sobash```. Tener en cuenta que el comando anterior debe ser ejecutado siendo root. Una vez creado el directorio, procedemos a ejecutar el ```chroot /root/sobash```. Tal comando va a tirar error porque no hay un directorio /bin/bash dentro de chroot. Para resolver el problema, basta con señalar dónde está el ejecutable de bash (u otro shell) está en el directorio chroot.

2. Copiar en el directorio anterior todas las librerías que necesita el comando bash. Para obtener esta información ejecutar el comando ```ldd /bin/bash``` ¿Es necesario copiar la librería _linux-vdso.so.1_? ¿Por qué? Dentro del directorio anterior, crear las carpetas donde va el comando bash y las librerías necesarias. Probar nuevamente ¿Qué sucede ahora?

    NOTA DE LIHUEL: es muy importante simular la estructura que tiene el sistema operativo dentro de la carpeta que se utilizará en el _chroot_. Esto significa que si uno de los archivos está en una carpeta llamada _lib64_, tal carpeta dentro de la carpeta (valga la redundancia) que se utilizará en el _chroot_ deberá llamarse _lib64_ y no _lib649_ (por ejemplo).

    Vamos de a partes, dijo Jack:
    * Antes de empezar con el proceso, quisiera explicar el por qué de que no haya que copiar la biblioteca _linux-vdso.so.1_. La razón es que es una biblioteca virtual, esto significa que no existe físicamente como archivo en el disco como tal. Lo cierto es que, en realidad, es una parte del kernel que es exportada a cada espacio de direcciones de un programa cuando es cargada. El por qué de todo lo anterior es mera performance en ciertas _system calls_.
    * Antes de copiar todo a _sobash_, hay que crear los respectivos directorios y subdirectorios para simular la estructura que utilizará el _chroot_:

        ```bash
        #Primero creamos el carpetaje. El -p es para subdirectorios
        mkdir -p /root/sobash/lib/x86_64-linux-gnu
        mkdir /root/sobash/lib64
        mkdir /root/sobash/bin
        ```

    * Habiendo creado el carpetaje hay que copiar los _shared objects_ y el comando _bash_:

        ```bash
        # Copiamos los shared objects necesarios de /lib/x86_64-linux-gnu a /root/sobash/lib/x86_64-linux-gnu/
        cp /lib/x86_64-linux-gnu/libtinfo.so.6 /lib/x86_64-linux-gnu/libdl.so.2 /lib/x86_64-linux-gnu/libc.so.6 /root/sobash/lib/x86_64-linux-gnu/
        # Copiamos el shared object necesario de /lib64 a /root/sobash/lib64/
        cp /lib64/ld-linux-x86-64.so.2 /root/sobash/lib64/
        # Copiamos el comando bash de /bin a /root/sobash/bin/
        cp /bin/bash  /root/sobash/bin/
        ```

    * Por último ejecutamos el _chroot_ con ```chroot /root/sobash```. Ahora el comando va a ejecutarse sin ningún problema.

3. ¿Puede ejecutar los comandos ```cd directorio``` o ```echo```? ¿Y el comando ```ls```? ¿A qué se debe esto?

    Es posible ya que tanto _cd_ como _echo_ son comandos _built-in_, esto es, vienen metidos en el kernel. Esto hace que incluso haciendo un _chroot_ sea posible ejecutarlos. El comando _ls_ es externo, esto significa que no es parte del kernel, que es un archivo más en algún lugar del filesystem y que, si se desea alcanzarlo, se lo debe crear y configurar su ubicación en variable PATH para que sea localizable.

4. ¿Qué muestra el comando ```pwd```? ¿A qué se debe esto?

    Muestra /. Esto es así, porque precisamente lo que hace _chroot_ es cambiar el directorio raíz del sistema para un proceso determinado (en nuestro caso, fue el _bash_).

5. Salir del entorno chroot usando ```exit```.
6. Ejecute el siguiente script que simplemente informa al administrador datos básicos sobre el sistema operativo, lista el contenido del directorio _/home_ y muestra la cantidad de procesos corriendo.

    ```bash
    #!/bin/bash
    while true; do
        clear
        echo -e "Hostname: $(hostname)\n"
        echo -e "Current date: $(date)\n"
        echo -e "/home directory contents:\n"
        ls -l /home
        echo -e "\nSome processes:\n$(ps -e | tail)"
        echo -e "\nProcess quantity:\n$(ps -e | wc -l)"
        sleep 10
    done
    ```

    Este sencillo script es capaz de acceder a información del sistema operativo como cualquier otro servicio nativo. Esta información consiste en archivos y directorios sobre los cuales se tengan permisos de lectura, procesos en ejecución, información sobre particiones, interfaces de red, etc.
7. ¿Cuál es la finalidad de la herramienta _debootstrap_? Instalarla en un sistema operativo basado en Debian.

    _debootstrap_ es una herramienta que instala un sistema basado en Debian dentro de un subdirectorio de otro sistema ya instalado. Esto no requiere un CD de instalación, solamente acceso al repositorio de Debian.

8. La sintaxis de _debootstrap_ para crear un sistema base es: ```debootstrap <suite> <target> <mirror>```. Utilice como suite la versión estable de Debian, _stable_; como _target_, el directorio donde alojará el árbol de directorios; y como mirror <http://httpredir.debian.org/debian>.

    NOTA DE LIHUEL: para este punto recomiendo cambiar el mirror porque el que da la cátedra está rancio. Yo usé <http://ftp.us.debian.org/debian>  porque el que daba la cátedra me traía problemas después. Otra cosa: por las dudas, borrá todo lo que haya en tu target (en mi caso fue, _/root/sobash/_) porque también puede ser que eso influya, aunque la verdad _no idea_.

    En mi caso, el _target_ que usé fue _/root/sobash/_. El comando me quedó así:

    ```debootstrap stable /root/sobash/ http://ftp.us.debian.org/debian```

9. Para que un chroot funcione correctamente en Linux, se deben montar ciertos sistemas de archivos que necesita el sistema operativo.

    ```bash
    # mount --bind /dev/ target/dev/
    # mount --bind /proc/ target/proc/
    # mount --bind /sys/ target/sys/
    ```

    Tenga en cuenta que debe reemplazar _target/_ por el directorio donde inicializó su ```debootstrap```.

    De nuevo, el target que usé fue _/root/sobash/_ por lo que los comandos me quedaron así:

    ```bash
    # mount --bind /dev/ /root/sobash/dev/
    # mount --bind /proc/ /root/sobash/proc/
    # mount --bind /sys/ /root/sobash/sys/
    ```

10. Copie el script que ejecutó en la sección anterior a un directorio accesible desde el chroot. Por ejemplo, _target/bin/script.sh_.

    De nuevo, el target que usé fue _/root/sobash/_ por lo que el comando que usé fue ```mv script /root/sobash/bin/```.

11. Ejecute una shell cuyo directorio raíz sea _target/_. Para tal fin, utilice, con privilegios de root, el comando _chroot_.

    De nuevo, el target que usé fue _/root/sobash/_ por lo que el comando que usé fue ```chroot /root/sobash```.

12. Ejecute el script instalado previamente y analice los resultados.

    Al estar _bindeado_ el _proc_, el _dev_ y el _sys_ en realidad se está usando, como base, las estructuras del sistema base.

13. ¿Puede ver los procesos que corren en el sistema operativo base? ¿Por qué?

    Esto se da por lo que dije más arriba: cuando se montaron los _bind_, en efecto lo que se _bindeo_ fueron _proc_, _sys_ y _dev_.

14. Los contenidos de los directorios _/home_, ¿son iguales? ¿Por qué?

    No, porque el _/home_ está aparte, por eso los resultados son diferentes. Si se hubiera _bindeado_ el /home también, no habría diferencia entre el _/home_ del sistema base y el del _chroot_.

15. Si se ejecuta un servidor HTTP en el sistema operativo base ¿Es posible ejecutar otro servidor HTTP escuchando en el mismo puerto en el entorno chroot? ¿Por qué?

    Esto no sería posible por todo lo ya mencionado. Cuando se hacen los _bind_ en el comando _mount_, una de las cosas que se _bindean_ son los puertos. Esto significa que si hay en el sistema base un programa escuchando en el puerto 80, no va a poder haber otro en el chroot que así lo haga.

## Control Groups

A continuación se probará el uso de _cgroups_. Para eso se crearán dos procesos que compartirán una misma CPU y cada uno la tendrá asignada un tiempo determinado.

Nota: es posible que para ejecutar _xterm_ tenga que instalar un gestor de ventanas. Esto puede hacer con ```apt-get install xterm```.

1. ¿Dónde se encuentran montados los _cgroups_? ¿Qué versiones están disponibles?

    La interface de _cgroups_ del kernel es provista mediante un pseudo-filesystem llamado cgroups. Hay dos versiones: cgroupV1 y cgroupV2.

2. ¿Existe algún controlador disponible en cgroups v2? ¿Cómo puede determinarlo?

    Primero hay que montar el cgroup (porque no es un filesystem más como cualquier otro). En mi caso, voy a montarlo en */mnt/cgroup2* con el comando ```mount -t cgroup2 none /mnt/cgroup2```. Una vez montado, puede apreciarse que hay varios archivos en él. Uno de esos archivos es **cgroup.controllers**. Cuando se le hace *cat* a hace archivo, está en blanco, lo que me da la pista que no hay ningún controlador en cgroupV2.

3. Analice que sucede si remueve un controlador de cgroups v1 (por ej. ```umount /sys/fs/cgroup/rdma```).

    Si ejecuto el *umount*, automáticamente, el controlador se agrega al cgroupV2.

4. Crear dos cgroups dentro del subsistema cpu llamados *cpualta* y *cpubaja*. Controlar que se hayan creado tales directorios y ver si tienen algún contenido

    ```mkdir /sys/fs/cgroup/cpu/"nombre_cgroup"```

    Efectivamente, haciendo el *mkdir* y luego los ls se puede constantar que los directorios fueron creados. Además, cuando se crean, automáticamente se inicializan con los archivos propios del cgroup.

    ```bash
    #Creo los directorios pertinentes
    mkdir /sys/fs/cgroup/cpu/cpualta /sys/fs/cgroup/cpu/cpubaja
    #Listo el contenido de los directorios
    ls /sys/fs/cgroup/cpu/cpualta
    ls /sys/fs/cgroup/cpu/cpubaja
    ```

5. En base a lo realizado, ¿qué versión de cgroup se está utilizando?

    Se está utilizando cgroupV1, ya que los controladores se montaron contra un filesystem de tipo cgroup. En este caso, ese filesystem es un *tmpfs* montado en */sys/fs/cgroup*.

6. Indicar a cada uno de los cgroups creados en el paso anterior el porcentaje máximo de CPU que cada uno puede utilizar. El valor de cpu.shares en cada cgroup es 1024. El cgroup cpualta recibirá el 70% de CPU y cpubaja el 30 %.

    ```bash
    echo 717 > /sys/fs/cgroup/cpu/cpualta/cpu.shares
    echo 307 > /sys/fs/cgroup/cpu/cpubaja/cpu.shares
    ```

7. Iniciar dos sesiones por ssh a la VM.(Se necesitan dos terminales, por lo cual, también podría ser realizado con dos terminales en un entorno gráfico). Referenciaremos a una terminal como termalta y a la otra, termbaja.
8. Usando el comando taskset, que permite ligar un proceso a un core en particular, se iniciará el siguiente proceso en background. Uno en cada terminal. Observar el PID asignado al proceso que es el valor de la columna 2 de la salida del comando.

    ```taskset -c 0 md5sum /dev/urandom &```

9. Observar el uso de la CPU por cada uno de los procesos generados (con el comando top en otra terminal). ¿Qué porcentaje de CPU obtiene cada uno aproximadamente?

    Entre el 50% y el 49.5% aproximadamente.

10. En cada una de las terminales agregar el proceso generado en el paso anterior a uno de los cgroup (termalta agregarla en el cgroup cpualta, termbaja en cpubaja. El process_pid es el que obtuvieron después de ejecutar el comando taskset)

    ```echo "process_pid" > /sys/fs/cgroup/cpu/cpualta/cgroup.procs```

11. Desde otra terminal observar como se comporta el uso de la CPU. ¿Qué porcentaje de CPU recibe cada uno de los procesos?

    Los porcentajes cambiaron: el proceso agregado a cpualta oscila entre un 69 y un 70%; en cambio, el proceso asignado a cpubaja oscila entre un 29 y 30%.

12. En termalta, eliminar el job creado (con el comando jobs ven los trabajos, con ```kill %JOB_ID``` lo eliminan. No se olviden del %.). ¿Qué sucede con el uso de la CPU?

    Se concentra casi pura y exclusivamente al otro proceso creado. Aparentemente, los cgroups parecen funcionar cuando hay varios procesos y no solo uno.//PREGUNTAR

13. Finalizar el otro proceso md5sum.

    Para finalizar el task, usar ```kill %JOB_ID```, donde JOB_ID representa el id del job (verlo con el comando *jobs*).

14. En este paso se agregarán a los cgroups creados los PIDs de las terminales (Importante: si se tienen que agregar los PID desde afuera de la terminal ejecute el comando echo $$ dentro de la terminal para conocer el PID a agregar. Se debe agregar el PID del shell ejecutando en la terminal).

    ```bash
    echo $$ > /sys/fs/cgroup/cpu/cpualta/cgroup.procs (termalta)
    echo $$ > /sys/fs/cgroup/cpu/cpubaja/cgroup.procs (termbaja)
    ```

15. Ejecutar nuevamente el comando ```taskset -c 0 md5sum /dev/urandom &``` en cada una de las terminales. ¿Qué sucede con el uso de la CPU? ¿Por qué?

    El proceso agregado a cpualta oscila entre un 69 y un 70%; en cambio, el proceso asignado a cpubaja oscila entre un 29 y 30%. Esto es así porque ya la shell está asignada al cgroup, por lo que no es necesario asignarle cada proceso, ya directamente por ser un proceso dentro de otro proceso se ejecuta con los parámetros del cgroup del padre.

16. Si en termbaja ejecuta el comando ```taskset -c 0 md5sum /dev/urandom &``` (deben quedar 3 comandos md5 ejecutando a la vez, 2 en el termbaja). ¿Qué sucede con el uso de la CPU? ¿Por qué?

    La cpu que es posible usar (el 30,7%) se reparte entre los dos procesos, de forma que el proceso de cpualta sigue ejecutándose con la misma intensidad que antes (no se le quita cpu).

## Namespaces

A continuación se crearán dos *networks namespaces* y se comunicarán entre ellos. Para esto se utilizará el comando *ip* que debe ejecutarse como root.

1. Explique el concepto de namespaces. ¿Cuáles son los posible namespaces disponibles?

    Un namespace es una capa de abstracción que hace que parezca que los procesos dentro de un determinado espacio de usuario tienen aislados sus propios recursos hardware. Los cambios en los recursos globales son visibles para procesos miembros del mismo namespace, pero no para procesos desplegados en distintos namespaces.

    Desde la versión 3.8 del kernel de Linux existen 6 tipos de namespaces:
   * mnt: controla el aislamiento de los distintos puntos de montaje.
   * pid: encargado de asignar un nuevo PID a cada proceso.
   * net: proporciona el aislamiento de los recursos asociados con el networking (devices, IPv4, IPv6, etc…).
   * ipc: identificadores IPC de SysV.
   * uts: nombres de hosts y dominios.
   * user: identificadores de usuarios y grupos.

2. Crear dos namespaces que se llamarán nsserver y nsclient nsserver:

    ```ip netns add "nombre_namespace"```
    ```ip netns list```: permite ver si se crearon los namespaces

    Efectivamente, ejecutando ```ip netns add``` me fue posible crear los network namespaces. Además, ejecutando ```ip netns list```, me fue posible ver los namespaces de los que disponía. Lo que ejecuté fue lo que sigue:

    ```bash
    #Creo los dos network namespaces
    ip netns add nsserver
    ip netns add nsclient
    #Listo los network namespaces existentes para asegurarme que los creé
    ip netns list
    ```

3. Crear dos interfaces virtuales, tipo veth (se generan de a pares y están conectadas por medio de una tubería), que se llamarán vethsrv y vethcli:

    ```ip link add "nombre_veth0" type veth peer name "nombre_veth1"```
    ```ip link list```: permite ver las veth recién creadas (aún pertenecen al namespace global o default).

    Mediante el comando ```ip link add vethsrv type veth peer name vethcli``` pude crear las interfaces virtuales. Efectivamente, ejecutando ```ip link list``` pude ver que todo se había creado exitosamente.

4. A continuación se deben agregar las veth a los namespaces (vethsrv a nsserver y vethcli a nsclient)

    ```ip link set "nombre_vethX" netns "nombre_namespace"```

    ```ip netns exec nombre_namespace ip link list```: permite ver las interfaces correspondientes a cada namespace (puede verificar que las interfaces creadas ya no se encuentran en el namespace global).

    En efecto, con ```ip link set``` se agregan las veth a los namespaces. Con ```ip netns exec NAMESPACE_NAME ip link list```, se puede ver qué interfaz está en qué namespace. Una manera de darse cuenta que algo salió mal, es cuando el comando anterior da error indicando que no es posible abrir el network namespace. Los comandos que tiré en la consola son los que siguen:

    ```bash
    #Para agregar las veth a los namespaces
    ip link set vethsrv netns nsserver
    ip link set vethcli netns nsclient
    #Para corroborar el estado de las interfaces
    ip netns exec nsserver ip link list
    ip netns exec nsclient ip link list
    ```

5. Asignarle IPs a las interfaces virtuales de cada uno de los namespaces (10.10.10.1/24 a la interface en nsserver y 10.10.10.2/24 a la existente en nsclient)

    ```bash
    ip netns exec "nombre_namespace" ip addr add 10.10.10.x/24 dev "nombre_vethX"
    ```

    Utilice los comando correspondientes para comprobar que se han asignado correctamente las IPs:

    ```ip netns exec "nombre_namespace" ip addr show```

    Efectivamente, siguiendo las instrucciones no hubo ningún problema. Lo que ejecuté fue lo siguiente:

    ```bash
    #Para asignar las IPs a las interfaces virtuales.
    ip netns exec nsserver ip addr add 10.10.10.1/24 dev vethsrv
    ip netns exec nsclient ip addr add 10.10.10.2/24 dev vethcli
    #Para chequear que la asignación fue corecta.
    ip netns exec nsserver ip addr show
    ip netns exec nsclient ip addr show
    ```

6. Si el comando anterior indica que las interfaces están bajas (DOWN) se deben activar. Puede que también la de loopback esté down:

    ```ip netns exec "nombre_namespace" ip link set "nombre_vethX" up```

    Naturalmente, tanto la interfaz de loopback como el resto de las interfaces estaban down. Para levantarlas, ejecuté los comandos y no hubo mayores problemas. Lo que hice fue lo siguiente:

    ```bash
    #Para levantar la interfaz de loopback. Fijarse cómo se llama tu interfaz de loopback (en mi caso era lo)
    ip netns exec nsserver ip link set lo up
    ip netns exec nsclient ip link set lo up
    #Para levantar las interfaces
    ip netns exec nsserver ip link set vethsrv up
    ip netns exec nsclient ip link set vethcli up
    #Para chequear que las interfaces están levantadas
    ip netns exec nsserver ip addr show
    ip netns exec nsclient ip addr show
    ```

7. ¿Es posible realizar un ping entre ambos namespaces?

    **NOTA DE LIHUEL**: leer lo que está más adelante, pero a priori lo que importa es lo siguiente: EL PING NO VA A FUNCIONAR.

    No es posible realizar el ping

8. Ejecutar el comando nc como servidor, usando el puerto 9043, en el namespace nsserver y el client en el nsclient. Comprobar que funciona en forma correcta.

    Esta parte me llevó más tiempo y tuve que investigar un poco. De hecho, de esa investigación también saltó el tema de que aprendiera a levantar la interfaz de loopback en un namespace.Para ejecutar netcat como servidor usando el puerto 9043, en el namespace nsserver, es necesario ejecutar ```ip netns exec nsserver nc -l -p 9043```. El procedimiento es muy similar para el cliente: ```ip netns exec nsclient nc 10.10.10.1 9043```. La comunicación ahora si va a ser establecida exitosamente.

9. Sin cerrar los comandos nc, ejecutar en el entorno global el comando ss -nat. ¿Puede ver el estado en que se encuentra el puerto 9043? ¿Por qué? Ejecute el mismo comando pero con la opción -N "namespace". ¿Es posible ver alguna información con respecto a ese puerto?

    Ejecutando ```ss -nat``` no es posible ver el estado en el que se encuentra el puerto 9043 ya que está completamente aislado en un namespace. En cambio, agregando la opción -N NAMESPACE_NAME es posible ver el local y el peer address tanto desde la perspectiva de nsserver como de la de nsclient. Los comandos que usé fueron los que siguen:

    ```bash
    #Con este comando no obtuve respuesta
    ss -nat
    #Con los comandos que siguen me figura la conexión del puerto
    ss -nat -N nsserver
    ss -nat -N nsclient
    ```

10. Ejecutar un bash dentro de uno de los nameservers (ip netns exec nsserver bash) y ver qué interfaces de red están activas. ¿Se ven las mismas inferfaces que fuera del namespace?

No, adentro del namespace se ve la de loopback y la respectiva veth, pero afuera tenés por ejemplo la enp que es la del WiFi. El asunto está en que el namespace es un espacio aislado aparte al punto que también las interfaces están aisladas.

## Containers

En GNU/Linux hay varias implementaciones de la tecnología de containers que provee el kernel. En esta sección de la práctica estudiaremos una de ellas, **LXC: Linux Containers**.

1. Instalar LXC mediante el comando:

    ```apt-get install lxc```

    El comando se ejecutó exitosamente sin contratiempos.

2. Comprobar mediante el siguiente comando si el *kernel* soporta LXC:

    ```lxc-checkconfig```

    Nota: puede que no todas las opciones estén habilitadas

    Ejecutando el comando, pude observar que todas las opciones estaban o bien habilitadas, o bien habilitadas pero no cargadas.

3. Comprobar los *templates* disponibles (a partir de donde se crean los *containers*):

    ```ls /usr/share/lxc/templates```

4. Crear un nuevo *container* desde el template de Debian con el nombre sodebian:

    ```lxc-create -t debian -n sodebian```

    Nota: dependiendo del *template* seleccionado durante la instalación se muestra el usuario y su contraseña, generada aleatoriamente que se utilizará para ingresar al *container*. Este comando puede demorar varios minutos la primera vez que es ejecutado ya que debe descargar muchos componentes.

5. Una vez creado el container iniciarlo mediante el siguiente comando:

    ```lxc-start -n sodebian```

    Nota: -n para ingresar el nombre del container que queremos iniciar. Con la secuencia CTRL-A-Q es posible salir del container.

    Acá pueden pasar dos cosas:
    1. El comando entró bien: Bien ahí! Pasá al próximo punto, bestia.
    2. Un error rancio (entiéndase por rancio un código **833 No such file or directory**): en ese caso, descargá la máquina virtual de la cátedra. Podría tirarte mil soluciones (como que, por ejemplo, modifiques el apparmor del archivo config del container lxc a *unconfined* en lugar de *generated*), pero la verdad es que más adelante te van a chillar tantas cosas que es mejor que tengas la máquina virtual que te dieron ellos y fue.

6. Utilizar el comando ```lxc-ls -f``` para comprobar el estado de los containers. ¿En qué estado se encuentra el container sodebian?

    Efectivamente, ejecutando ese comando puede verse que el container sodebian aparece listado. El estado del container es RUNNING, es decir, que está ejecutándose.

7. Mediante el siguiente comando ingresar al container

    ```lxc-console -n sodebian -t 0```

    En efecto, mediante ese comando fue posible ingresar al container.

8. ¿Tiene la password para ingresar al container? En caso de no tenerla modificar la password de root usando chroot.

    Acá te pueden pasar 2 cosas:
    1. Tenés la password: qué tipo groso y organizado! Dale, dale, metela y mandate al próximo punto.
    2. *Pará pará! La huevada esta nunca escupió una pass/¿Esto traía pass?*: Depende: si bien es muy probable que estés usando una versión superior a la 2.0.8 (yo ahora mismo estoy usando la 3.0.3, chequealo con ```lxc-ls --version```), si es inferior a ese número si viene. Como seguro estás usando una superior, vas a tener que resetearla, pa, no hay otra. Para cambiarla usá chroot de la siguiente forma: ```chroot /var/lib/lxc/CONTAINER_NAME/rootfs```. Vas a entrar sin problemas y desde ahí mandale passwd y ponele la password que quieras al root. Antes de que preguntes, no, **NO** hay otro usuario aparte del root en el container, por lo que las credenciales del login son **root - PASSWORD_QUE_HAYAS_ELEGIDO**. También podés usar ```lxc-attach -n CONTAINER_NAME passwd```. Vofi. Si seguiste los pasos bien, vas a poder entrar sin ningún problema.

    Nota: con el comando lxc-attach es posible acceder a un container sin tener la password

9. Ejecutar el script utilizado en los ejercicios anteriores y responder:
    1. ¿Puede acceder al /home del sistema operativo base?

        Es posible acceder al /home, así como crear archivos y tal. Notar que como esto no trae otros users, el /home viene vacío.

    2. ¿Es posible ver los procesos que están corriendo en el sistema operativo base?

        Efectivamente, también es posible: ejecutá ps o top y vas a ver los diferentes procesos del sistema.

10. Montar el home directory del usuario con el que ingresó al SO base en el directorio /mnt del contenedor

    Antes de responder las preguntas, quisiera explicar cómo diantres se hace esto. Primero, DETENER el container. Esto se logra ejecutando ```lxc-stop -n CONTAINER_NAME```. Una vez detenido el container, hay que modificar el archivo config de éste. Primero hay que abrirlo con algún tipo de editor, yo usé el nano. Ejecutás ```nano /var/lib/lxc/CONTAINER_NAME/config``` y ya lo vas a tener ahí. Una vez abierto, le appendeas ```lxc.mount.entry=/path/a/montar /var/lib/lxc/CONTAINER_NAME/rootfs/mnt/MOUNT_DIR none bind,create=dir 0 0``` y guardás y reinicias el container con ```lxc-start -n CONTAINER_NAME``` y vas a ver que va a ir como piña.

    1. ¿Se montó correctamente el directorio? (Compare el contenido de ambos directorios)

        Efectivamente, el directorio se montó correctamente (esta vez si kjj).

    2. En el directorio /mnt del container crear una carpeta MountSO. ¿Existe también en el home directory?

        Para crear el directorio usá ````mkdir /mnt/home_so/MountSO```. Naturalmente también se crea, ya que el parámetro *bind* que usamos en el lxc.mount.entry bindea el directorio.

    3. Elimine la carpeta creada en el punto anterior ubicado en home directory y comprobar si también se elimina en el directorio del contenedor

        Por la misma razón que en el punto anterior, es que también se elimina el directorio. Para borrar el directorio usá ````rmdir /home/so/MountSO```.

    4. Desmontar el directorio

        Para desmontar, seguir los mismos pasos que para montar, con la diferencia que cuando estás con el archivo abierto:
        1. O borrás la línea.
        2. O hacés lo que todo buen programador hace: comentar la línea porque nunca se sabe cuándo va a ser necesario volver a usarla.

11. ¿Qué interfaces de red existen en el container? Analizar el contenido del archivo config en el directorio /var/lib/lxc/sodebian/.

    Si entrás al path */var/lib/lxc/CONTAINER_NAME/config* con un editor (nano, por ejemplo), vas a ver una línea que dice lxc.net.0.type que tiene un valor empty. Eso significa que el tipo de network del tty0 está empty. A priori, eso podría sugerirnos que el container está aislado de internet. Lo confirmas cuando haces un ```apt-get update``` dentro del container y no encuentra un solo src.

12. Usando el archivo config del container asignarle una nueva interface de red con la dirección 172.16.2.10/24. La interface debe ser de tipo veth y asociarse en el otro extremo a un bridge llamado brso1 (tengan en cuenta que el bridge definido de esta manera se pierde al rebootear):
    1. ```brctl addbr brso1``` - crea un bridge virtual en la VM
    2. ```brctl show``` - para ver los bridges definidos en la VM
    3. En el archivo config del container agregar las siguientes líneas:

        ```bash
        ## Se indica el tipo de interface (veth igual al tipo de interface visto en namespaces)
        lxc.net.0.type=veth
        ## Un extremo de la interface va en el container y el otro en el bridge brso1
        lxc.net.0.link=brso1
        ## Levantamos la interface
        lxc.net.0.flags = up
        ## Definimos la dirección MAC
        lxc.net.0.hwaddr = 4a:49:43:49:79:bf
        ## Definimos la dirección IPv4
        lxc.net.0.ipv4.address = 172.16.2.10/24
        ```

    *Vayamos por partes*, dijo Jack:
    * Primero vamos a stopear el container con ```lxc-stop -n CONTAINER_NAME```.
    * Ahora hay que editar todas esas líneas en el archivo config.
    * Luego resta crear el bridge con ```brctl addbr brso1```. El comando ```brctl show``` sirve para asegurarnos que está creado.
    * Después solo resta volver a iniciar el container con ```lxc-start -n CONTAINER_NAME```.

13. Reinicar el container y utilizando el comando ```ip addr show``` comprobar si todo está correcto.

    Naturalmente, el comando muestra el bridge que configuramos antes.

14. Detener el container llamado sodebian.

    ```lxc-stop -n sodebian```

15. Clonar el container utilizando el comando lxc-copy. Llamar so1 al nuevo container.

    ```lxc-copy -n "old_container" -N "new_container"```

    **NOTA DE LIHUEL**: como aclaración, old_container en realidad vendría a ser el current_container o aquel que vayas a tomar como referencia para la copia.

16. En estas condiciones, ¿es posible iniciar ambos containers? ¿Qué debería hacer para solucionarlo?

    Es posible iniciar ambos simultáneamente, el problema es que posteriormente van a haber problemas de conectividad.

17. Iniciar los dos containers y ejecutar nuevamente lxc-ls -f. ¿Ambos containers tiene IP asignada?

    Si, tienen la misma IP.

18. Al loguear en el nuevo container, ¿qué nombre tiene? ¿Podría ser modificado ese nombre sin estar en la consola del container? De ser posible, cambiarlo a so1.

    Dado que es una copia exacta, tiene el mismo *hostname* que el container padre: *sodebian*. Para cambiarlo en un comando basta ejecutar//CONSULTAR

19. ¿Es posible hacer un ping entre ambos containers? ¿Por qué?

    No es posible, ya que la herramienta ping no se encuentra instalada.

20. Para solucionar el punto anterior vamos a conectar ambos containers a Internet para que puedan instalarse el paquete *iputils-ping*:
    1. ```ip addr del 10.0.2.15/24 dev enp0s3``` – removemos la IP de la interface enp0s3, que en esta VM es la que nos da la salida a Internet
    2. ```brctl addif brso1 enp0s3``` – agregamos la interface al mismo bridge donde están conectados los containers
    3. ```dhclient brso1``` – asignamos IP al bridge (la interface enp0s3 debe quedar sin IP)
    4. Remover del archivo config de cada container las líneas donde se definen las IPs
    5. Reiniciar los containers. Ver si tomaron IP del mismo rango que el bridge (debería ser de la red 10.0.2.0/24)
    6. Ingresar al container *sodebian* e instalar el paquete requerido
    7. Probar de hacer ping al otro container o al nodo host
21. Crear un nuevo contenedor, llamado so2, igual que como se generó sodebian. ¿Por qué tarda mucho menos tiempo que cuando se creó *sodebian*?

    Esto es así ya que el filesystem completo está cacheado en /var/cache/lxc. En esa ubicación, existe una carpeta por cada sistema operativo diferente.

22. Usando los cgroups limitar la cantidad máxima de memoria RAM que puede consumir un container. Ver cuánta memoria tiene asignada (comandos top o free) y bajarla a la mitad. Por ej. si tiene asignados 1GB quedaría así (lo llevamos a 512MB):

    ```lxc-cgroup -n "nombre_contenedor" memory.limit_in_bytes 536870912```

    Nota: el container debe estar corriendo

    **NOTA DE LIHUEL**: para evitar ese numero chancho, se pueden definir cantidades en megabytes, kilobytes o gigabytes insertando la cantidad seguida de la primera letra de la unidad (M para megabytes, K para kilobytes o G para gigabytes). En el ejemplo de la cátedra, se podría simplificar el comando a ```lxc-cgroup -n "nombre_contenedor" memory.limit_in_bytes 512M```.

23. Comprobar que se modificó la memoria RAM en el container.

    Naturalmente, se modifica. Para verlo yo usé el comando free con el parámetro -m que muestra la cantidad de memoria en megabytes. Si no se usa el comando con ese parámetro, por defecto, devuelve la cantidad en kilobytes.

24. ¿Dónde se debería ver reflejado la modificación anterior? ¿Se ve realmente el cambio realizado en el lugar correspondiente?

    En el cgroup del container, esto es, en /sys/fs/cgroup/memory/memory.limit_in_bytes. El número presente en el archivo está en kilobytes.

25. Volver la RAM a su valor original (1GB en este ejemplo).
26. Eliminar definitivamente todos los containers creados mediante el comando:

    ```lxc-destroy -n "nombre_contenedor"```
