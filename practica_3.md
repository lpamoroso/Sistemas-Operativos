# Práctica 3

## Requisitos

_Para poder agregar una nueva System Call al sistema es necesario contar con el código fuente del núcleo del sistema operativo. Cuando utilicemos el termino <\kernel_code\> haremos referencia al directorio donde se encuentra el código fuente del Kernel. Por convención el mismo es colocado en /usr/src/linux. Para realizar la practica debe utilizarse el código utilizado en la practica 1._

### Conceptos generales

**Referencia:** <https://speakerdeck.com/georgiknox/linux-kernel-hacking-a-crash-course>

1. ¿Qué es una System Call? ¿Para que se utiliza?

    Son llamados al kernel para ejecutar una función específica que controla un dispositivo o ejecuta una instrucción privilegiada. Su propósito es proveer una interfaz común para lograr portabilidad. Su funcionalidad se ejecuta en modo kernel pero todo dentro del contexto del proceso.

2. ¿Para qué sirve la macro syscall? Describa el propósito de cada uno de sus parámetros.

    Ayuda: <http://www.gnu.org/software/libc/manual/html_mono/libc.html#System-Calls>

    syscall() es una pequeña biblioteca de funciones que invoca las system call las cuales poseen una interfaz de lenguaje ensamblador que especifican un número con argumentos específicos. Usar syscall() es bastante útil, por ejemplo, cuando se desea invocar un system call que no posee una función wrapper en la biblioteca de C.

    Su parámetro es un _integer_ que indica el número de syscall.

3. ¿Para que sirven los siguientes archivos?

    + \<kernel_code\>/arch/x86/syscalls/syscall_32.tbl

        Es una tabla que contiene los números y las systemcall de las arquitecturas de 32 bits.

    + \<kernel_code\>/arch/x86/syscalls/syscall_64.tbl

        Es una tabla que contiene los números y las systemcall de las arquitecturas de 64 bits.

4. ¿Para qué sirve la macro asmlinkage?

    Es una macro que le indica al compilador que busque los parámetros de la función en el stack de la CPU en lugar de los registros. Esto se hace porque las llamadas al sistema son servicios que el espacio de usuario puede llamar para solicitar que el kernel realice algo para ellos (y, por lo tanto, ejecutarlo en el espacio del kernel). Estas funciones son bastante poco ortodoxas en el sentido de que no se puede esperar que se comporten como funciones normales, donde los parámetros generalmente se pasan escribiendo en la pila de programas, sino que se escriben en los registros.

5. ¿Para qué sirve la herramienta _strace_? ¿Cómo se usa?

    _strace_ es una utilidad para comprobación de errores que permite vigilar las llamadas al sistema usadas por un determinado programa y todas las señales que éste recibe.​ Su funcionamiento es posible por una característica del kernel linux llamada ptrace. Su uso más común consiste en arrancarlo junto al programa al que se efectúa el trazado, el cual imprime una lista de las llamadas al sistema que ejecuta. Es útil para averiguar la causa del fallo de un programa determinado porque informa de situaciones en las que, por ejemplo, el programa está intentando acceder a un fichero que no existe o para el que no tiene permiso de lectura, entre otros problemas.

## System Calls

**Referencia:** <https://www.kernel.org/doc/html/v4.10/process/adding-syscalls.html>

La System Call que vamos a implementar accederá a la estructura rq (runqueue), que es la estructura de datos básica del planificador de procesos (scheduler). Esta estructura es definida en \<kernel_code\>/kernel/sched/sched.h. Hay una rq por cada procesador y cada proceso del sistema estará sólo en una rq. Esta estructura mantiene información adicional por cada procesador, la cual será el objetivo de la syscall.

Fragmento de la definición de la estructura _runqueue_ rq:

```C
unsigned int nr_running;
#ifdef CONFIG_NUMA_BALANCING
unsigned int nr_numa_running;
unsigned int nr_preferred_running;
/*
* This is part of a global counter where only the total sum
* over all CPUs matters. A task can increase this counter on
* one CPU and if it got migrated afterwards it may decrease
* it on another CPU. Always updated under the runqueue lock:
*/
unsigned long nr_uninterruptible;
struct task_struct __rcu *curr;
struct task_struct *idle;
struct task_struct *stop;
unsigned long next_balance;
```

Intentaremos acceder con nuestra llamada al sistema a dos datos almacenados en los campos de la estructura _runqueue_:

+ nr_running: contiene el número de tareas en estado de ejecución para este procesador, es decir, tareas en estado TASK_RUNNING.
+ nr_uninterruptible: contiene el número de tareas en estado no interrumpible, TASK_UNINTERRUMPIBLE. Es decir, tareas esperando por un evento de entrada/salida no activables por la llegada de una señal.

Por tanto al utilizar nuestra llamada al sistema, que denominaremos a partir de este momento _rqinfo_, obtendrá los datos antes comentados del espacio kernel en el espacio de usuario de nuestra aplicación.

### Agregamos una nueva System Call

1. Añadir una entrada al final de la tabla que contiene todas las System Calls, la Syscall Table. En nuestro caso, vamos a dar soporte para nuestra syscall a las arquitecturas x86_64 y x86_32.

    Atención:
    + En general, la nomenclatura oficial para denominar la syscall, esto es, el nombre de la syscall, ha de construirse con el prefijo ```sys_```, excepto en este paso donde haremos referencia a ella usando el prefijo ```__x64_sys_```.
    + Debemos asignar un número único a nuestra system call, de modo que aumentaremos en 1 el número de la última existente.

    \<kernel_code\>arch/x86/entry/syscalls/syscall_64.tbl:

    ```C
    433 common fspick __x64_sys_fspick
    434 common pidfd_open __x64_sys_pidfd_open
    435 common clone3 __x64_sys_clone3/ptregs
    437 common openat2 __x64_sys_openat2
    438 common pidfd_getfd __x64_sys_pidfd_getfd
    439 common rqinfo __x64_sys_rqinfo
    ```

    **NOTA DE LIHUEL**: es posible que notes que en /usr/src no hay ninguna carpeta linux. Al menos en mi caso, fue así. También en mi caso, en la entrega anterior, había que compilar un kernel para la cátedra. Si sos oCservador, vas a notar que esa estructura está presente en la carpeta de la entrega. Ahí es donde vas a hacer las modificaciones.

2. Ahora incluir la declaración de nuestra system call (sólo la línea que dice sys_rqinfo, el resto se provee como contexto).

    <kernel_code>/include/linux/syscalls.h:

    ```C
    asmlinkage long sys_ni_syscall(void);
    asmlinkage long sys_rqinfo(unsigned long *ubuff, long len);
    #endif /* CONFIG_ARCH_HAS_SYSCALL_WRAPPER */
    ```

    **NOTA DE LIHUEL**: con este inciso no tuve problemas, lo complicado, digamos, es encontrar la linea final sin tener un editor de texto con UI (yo estaba usando nano), pero si te sabés los comandos para moverte en el archivo, no vas a tener ningún drama.

3. El próximo paso es incluir el código de nuestra syscall en algún punto del árbol de fuentes ya sea añadiendo un nuevo archivo al conjunto del kernel o incluyendo nuestra implementación en algún archivo ya existente. La primera opción obligará a modificar los makefiles del kernel para incluir el nuevo archivo fuente. Por simplicidad añadiremos el código de nuestra syscall en algún punto del código ya existente. Esta segunda opción es la que utilizaremos, pero ¿Dónde colocamos nuestro código? En nuestro ejemplo, un buen sitio para implementar la syscall inforq podría ser en el archivo kernel/sched/core.c, donde podemos acceder a la estructura que nos interesa con facilidad. En ese mismo archivo, se implementan otras syscalls relacionadas con el planificador de CPU y el scheduler del sistema operativo. Otro motivo (forzoso) por el cual colocar aquí nuestra system call es porque muchas funciones que necesitamos están en este archivo y no son reexportadas.

    \<kernel_code\>/kernel/sched/core.c:

    ```C
    SYSCALL_DEFINE2 (rqinfo, unsigned long *, ubuff, long, len)
    {
        struct rq *rqs;
        struct rq_flags flags;
        unsigned long kbuff[2];
        /*
        * Si el buffer size del usuario
        * es distinto al nuestro devolvemos error.
        */
        if (len != sizeof (kbuff))
        return -EINVAL;
        /*
        * Delimitamos la region critica para
        * acceder al recurso compartido.
        */
        rqs = task_rq_lock (current, &flags);
        kbuff[0] = rqs->nr_running;
        kbuff[1] = rqs->nr_uninterruptible;
        task_rq_unlock (rqs, current, &flags);
        if (copy_to_user (ubuff, &kbuff, len))
        return -EFAULT;
        return len;
    }
    ```

    Notas:
    + El valor "current" utilizado es una macro definida en el kernel la cual retorna una estructura que representa el proceso actual (el que ejecuto el llamado a la System Call). Esta estructura, llamada task_struct, se encuentra definida en \<kernel_code\>/include/linux/sched.h.
    + Bloqueo del recurso: es importante destacar esto, ya que en el código hemos bloqueado la estructura rq antes de acceder a ella, para ello hemos usado funciones que nos proporciona el propio código del planificador. Es común y necesario conseguir acceso exclusivo a este tipo de recursos, pues son compartidos por muchas partes del sistema y tenemos que mantenerlos consistentes. En especial, puesto que las syscall son interrumpibles, tenemos que tener mucho cuidado a la hora de acceder a este tipo de recursos.
    + Notar que nuesta system call debe exportar de alguna manera los datos obtenidos al espacio de usuario, es decir al programa o librería que utlizará nuestra system call, es por eso que tenemos la función copy_to_user para llevar a cabo esta tarea.

    **NOTA DE LIHUEL**: en la cátedra te sugieren que uses el editor que más te guste y que uses el putty o el ssh (depende si estás en windows o linux, respectivamente) para copiar y pegar el código. Yo lo hice con el nano a manopla para ir comprendiéndolo a medida que lo tipeaba. Si sos medio masoca y abriste el core.c con el nano, te vas a dar cuenta que cuando quieras llegar al final es interminable(tiene 7978 lineas jajaja, vas a estar un rato). Cuestión que un buen dato es apretar ```ctrl + shift + _```. Esto lo que hace es preguntarte a qué línea querés saltar. Vos metele 7978 y problema resuelto. Te ahorras ese detallín.

4. Lo próximo que debemos realizar es compilar el Kernel con nuestros cambios. Una vez seguidos todos los pasos de la compilación como lo vimos en el trabajo práctico 1, acomodamos la imagen generada y arrancamos el sistema con el nuevo Kernel.

    **NOTA DE LIHUEL**: acá no hay mucha ciencia, es compilar el kernel y listo, pero te dejo los pasos así no tenés que buscar mucho:
    1. Te metés a la carpeta raiz del kernel.
    2. Tipeas make. Si no la manqueaste mientras editabas el core.c, no debería haber errores. Acordate que si metes ```make -jNUMERO_DE_PROCESADORES_DE_LA_VM```, donde NUMERO_DE_PROCESADORES_DE_LA_VM indica la cantidad de procesadores que le asignaste a la virtual machine, el proceso va a ir como piña. Trata de asignarle un par de procesadores a la VM (a menos que te guste esperar), no seas ratón, porque posta te hace el proceso mucho más rápido. Igual, si no tenés apuro metele ```make``` así pelado y fue.

5. Nuestro último paso es realizar un programa que llame a la System Call. Para ello crearemos un archivo, por ejemplo prueba_inforq.c, con el siguiente contenido:

    ```C
    #include <stdio.h>
    #include <errno.h>
    #include <sys/syscall.h>
    #define NR_rqinfo 439
    int main(int argc, char **argv)
    {
        long ret;
        unsigned long buf[2];
        printf("invocando syscall ..\n");
        if ((ret = syscall(NR_rqinfo, buf, 2*sizeof(long))) < 0){
        perror("ERROR");
        return -1;
        }
        printf("runnables: %lu\n", buf[0]);
        printf("uninterruptibles: %lu\n", buf[1]);
        return 0;
    }
    ```

    Notas:
    + Cuando utilizamos llamadas al sistema, por ejemplo open() que permite abrir un archivo, no es necesario invocarlas de manera explícita, ya que por defecto la librería libc tiene funciones que encapsulan las llamadas al sistema.

        Luego lo compilamos para obtener nuestro programa. Para ello ejecutamos:

        ```gcc -o prueba prueba_inforq.c```

        Por ultimo nos queda ejecutar nuestro programa y ver el resultado.

        ```./prueba```
    + Para más opciones de compilación de programas en C se sugiere la lectura del manual de compilador de c.

### Monitoreando System Calls

1. Implemente el siguiente programa.

    ```C
    #include <stdio.h>
    int main() {
        printf("Hola, mundo!\n");
        return 0;
    }
    ```

    Ejecútelo utilizando el comando strace.

    ```# strace mi_programa```

    Analice que System Calls son invocadas.

    Aclaración: Es posible que el programa strace no esté instalado en su equipo. Para ello deberá instalarlo. Si estamos en debian podemos hacerlo ejecutando lo siguiente:

    ```# sudo apt-get install strace```

    **NOTA DE LIHUEL**: cuando uses el ```strace```, en vez de meter el nombre de programa, meté el path completo del archivo. De nada jajaj.

2. Compile y ejecute los siguientes programas. Realice un trace de los mismos utilizando la herramienta ```strace``` ¿Existe alguna diferencia?

    Invocando _getpid_ a través de libc:

    ```C
    #include <stdio.h>
    #include <stdlib.h>
    #include <sys/types.h>
    #include <unistd.h>
    int main(void) {
        int p_id= (int) getpid();
        printf("El pid es %d\n",p_id);
        return 0;
    }
    ```

    Invocando _getpid_ a través de syscall:

    ```C
    #include <syscall.h>
    #include <unistd.h>
    #include <stdio.h>
    #include <sys/types.h>
    int main(void) {
        int p_id= syscall(SYS_getpid);
        printf("El pid es %d\n",p_id);
        return 0;
    }
    ```

    No hay diferencia dado que usan la misma _syscall_.

## Módulos y Drivers

**Referencia:** <http://tldp.org/LDP/lkmpg/2.6/html/c38.html>

### Conceptos Generales

1. ¿Cómo se denomina en GNU/Linux a la porción de código que se agrega al kernel en tiempo de ejecución? ¿Es necesario reiniciar el sistema al cargarlo? Si no se pudiera utilizar esto ¿Cómo deberíamos hacer para proveer la misma funcionalidad en GNU/Linux?

    Se le denomina módulo. No es necesario reiniciar el sistema ya que los módulos están hechos para cargarse bajo demanda. Si no existieran los módulos, habría que crear kernels monolíticos y añadir las funcionalidades directamente a la imagen del kernel.

2. ¿Qué es un driver? ¿Para que se utiliza?

    Es un código específico que permite realizar operaciones sobre un dispositivo.

3. ¿Por qué es necesario escribir drivers?

    Porque, de otra forma, no sería posible la comunicación entre el sistema operativo y los dispositivos.

4. ¿Cuál es la relación entre modulo y driver en GNU/Linux?

    Los drivers se implementan en forma de módulos.

5. ¿Qué implicancias puede tener un bug en un driver o módulo?

    Que el sistema no arranque o que se resetee indefinidamente.

6. ¿Qué tipos de drivers existen en Gnu/Linux?

    + Drivers de bloques: son un grupo de bloques de datos persistentes. Leemos y escribimos de a bloques, generalmente de 1024 bytes.
    + Drivers de carácter: Se accede de a 1 byte a la vez y 1 byte sólo puede ser leído por única vez.
    + Drivers de red: tarjetas ethernet, WIFI, etc.

7. ¿Que hay en el directorio /dev? ¿Qué tipos de archivo encontramos en esa ubicación?

    En _/dev_ se encuentran las representaciones de los dispositivos (device files). Los tipos de archivo pueden ser b o c según pertenezcan a dispositivos de caracter o de bloque.

8. ¿Para qué sirve el archivo _/lib/modules/\<version\>/modules.dep_ utilizado por el comando modprobe?

    El archivo _modules.dep_ es un archivo generado por el comando depmod y lista las dependencias para cada módulo en los directorios que haya en _/lib/modules/\<version\>_, allí donde modules.dep esté.

### Agregando un módulo a nuestro kernel

El objetivo de este ejercicio es crear un módulo sencillo y poder cargarlo en nuestro kernel con el fin de consultar que el mismo se haya registrado correctamente.

1. Crear el archivo ```memory.c``` con el siguiente código.

    ```C
    #include <linux/module.h>
    MODULE_LICENSE("Dual BSD/GPL");
    ```

2. Crear el archivo Makefile con el siguiente contenido

    ```obj-m := memory.o```

    Responda lo siguiente:
    + Explique brevemente cual es la utilidad del archivo Makefile.

        $(obj-m) especifica archivos objeto los cuales están construídos como módulos cargables del kernel.

        Un módulo podría ser construído desde un archivo fuente o varios. En caso de que sea de un solo archivo fuente, el makefile kbuild simplemente añade el archivo a $(obj-m).

        Si un módulo del kernel está construído desde varios archivos fuente, se especifica que se quiere construir un módulo de la misma forma que arriba; sin embargo, kbuild necesita sabe cuáles archivos objeto se utilizarán para construir el módulo, por lo cual hay que decírselo seteando una variable $(\<module_name\>-y).

    + ¿Para qué sirve la macro MODULE_LICENSE? ¿Es obligatoria?

        Sirve para indicar la licencia del módulo. La macro existe, basicamente, por 3 razones:
        1. Para que ```modinfo``` pueda mostrar la información de licencia para usuarios que quieran saber que la configuración es libre.
        2. Para que la comunidad ignore reportes de bugs que incluyan módulos propietarios.
        3. Para que los proveedores pueden hacer lo mismo según sus propias políticas.

3. Ahora es necesario compilar nuestro modulo usando el mismo kernel en que correrá el mismo, utilizaremos el que instalamos en el primer paso del ejercicio guiado.

    ```make -C \<KERNEL_CODE\> M=`pwd` modules```

    Responda lo siguiente:
    + ¿Cuál es la salida del comando anterior?

        El comando compila el archivo C, luego crea el archivo objeto y finalmente el módulo del kernel.

    + ¿Qué tipos de archivos se generan? Explique para qué sirve cada uno.

        Se genera el .o que es un archivo objeto común, el .mod.o que es el módulo objeto y el .ko que es el módulo definitivo.

    **NOTA DE LIHUEL**: este inciso me pareció complejo y me llevó tiempo. Por las dudas, si te quedás trabado, \<KERNEL_CODE\> hace referencia al directorio donde está el kernel, es decir, **/lib/modules/VERSION/build**, siendo VERSION la version del kernel(meté el comando ```uname -r``` y te salta enseguida). Puede ser que tampoco sepas muy bien donde crear el Makefile o el módulo. Lo cierto es que solo tenés que crear el modulo y el Makefile en el mismo directorio, donde más te guste. Cuando le des al ```make```, vas a ver que se va a meter en la carpeta donde tengas tu kernel y hace todo solo.

4. El paso que resta es agregar y, eventualmente, quitar nuestro modulo al kernel en tiempo de ejecución. Ejecutamos:

    ```# insmod memory.ko```

    Responda lo siguiente:
    + ¿Para qué sirve el comando insmod y el comando modprobe? ¿En qué se diferencian?

        Tanto ```insmod``` como ```modprobe``` sirven para cargar módulos al kernel, la diferencia está en las dependencias: ```modprobe``` es lo suficientemente inteligente para cargar el módulo con las dependencias; ```insmod``` se limita solo a cargar el módulo. De hecho, al ejecutarse el ```modprobe``` en _verbose_ puede verse que detrás hay un ```insmod```.

    **NOTA DE LIHUEL**: este inciso me llevó tiempo porque no me tomaba el ```insmod```. No te mates buscando el paquete para instalar u otras opciones como el ```modprobe```, aparentemente con todas es igual. El tema está en que el binario no está en _/bin_, sino en _/sbin_ y por eso el sistema no toma el comando. Para poder usarlo, meté ```/sbin/insmod``` y va como piña.

5. Ahora ejecutamos

    ```lsmod | grep memory```

    Responda lo siguiente:
    + ¿Cuál es la salida del comando? Explique cuál es la utilidad del comando lsmod.

        El comando ```lsmod``` lista los distintos módulos cargables que están cargados actualmente en el kernel.

    + ¿Qué información encuentra en el archivo _/proc/modules_?

        Este archivo muestra una lista de todos los módulos cargados en el kernel

    + Si ejecutamos ```more /proc/modules``` encontramos los siguientes fragmentos:

        ```C
        parport_pc 37412 0 - Live 0xf8b02000
        lp 12580 0 - Live 0xf8ae1000
        parport 37448 3 ppdev,parport_pc,lp, Live 0xf8ae9000
        .memory 3844 0 - Live 0xf89fe000
        ```

    + ¿Qué información obtenemos de aquí?
        + La primera columna contiene el nombre del módulo.
        + La segunda columna refiere al tamaño de memoria del módulo en bytes.
        + La tercera columna lista cuántas instancias de ese módulo hay actualmente cargadas. Si el valor es 0, significa que el módulo no está cargado.
        + La cuarta columna indica si el módulo depende de otro módulo para funcionar y lista tales dependencias.
        + La quinta columna lista en que estado de carga se encuenta el módulo: _Live_(activo), _Loading_(cargando), _Unloading_(descargando) son los únicos valores posibles.
        + La sexta columna lista el actual offset de memoria para el módulo cargado. Esta información es útil para el debbugging, o para otras herramientas varias como ```oprofile```.

    + ¿Con qué comando eliminamos el módulo de nuestro kernel?

        Con ```rmmod```.

6. Elimine el modulo recién agregado al kernel. Para corroborar que efectivamente el mismo ha sido eliminado del kernel ejecute lo siguiente:

    ```lsmod | grep memory```

    **NOTA DE LIHUEL**: acordate que ```rmmod``` es uno de esos comandos que está en _/sbin_, por lo que si querés que funque vas a tener que ejecutarlo de la siguiente manera: ```/sbin/rmmod```. Pequeños detalles que ahorran tiempo :)

7. Modifique el archivo memory.c de la siguiente manera

    ```C
    #include <linux/init.h>
    #include <linux/module.h>
    #include <linux/kernel.h>
    MODULE_LICENSE("Dual BSD/GPL");
    static int hello_init(void) {
        printk("<1> Hello world!\n");
        return 0;
    }
    static void hello_exit(void) {
        printk("<1> Bye, cruel world\n");
    }
    module_init(hello_init);
    module_exit(hello_exit);
    ```

    Responda lo siguiente:
    + ¿Para qué sirven las funciones module_init y module_exit? ¿Cómo haría para ver la información del log que arrojan las mismas?
        + module_init: función que se ejecutará cuando se inserta el módulo o cuando el kernel bootea.
        + module_exit: función que se ejecutará cuando el módulo sea removido.

        Con ```dmesg``` podría verse la información del log.

    + Hasta aquí hemos desarrollado, compilado, cargado y descargado un módulo en nuestro kernel. En este punto y sin mirar lo que sigue ¿Qué nos falta para tener un driver completo?

        Las operaciones del driver: _read, write, open, release._

    + Clasifique los tipos de dispositivos en Linux. Explique las características de cada uno.

        Tipo | Dispositivo
        :-------:|:-------
        hd | Discos duros IDE y dispositivos IDE como cdroms
        sd | Discos duros SCSI
        scd | Cdrom SCSI
        st | Unidades de cinta SCSI
        ht | Unidades de cinta IDE
        fd | Unidades de diquetes
        lp | Puertos paralelos
        tty | Terminales o consolas
        pty | Terminales remotas o de red, incluyendo las abiertas en Window X
        ttyS | Puertos seriales
        cua | Puertos de comunicación
        eth | Tarjetas o interfaces de red ethernet

### Desarrollando un Driver

Ahora completaremos nuestro módulo para agregarle la capacidad de escribir y leer un dispositivo. En nuestro caso, el dispositivo a leer será la memoria de nuestra CPU, pero podría ser cualquier otro dispositivo.

1. Modifique el archivo memory.c de la siguiente manera

    ```C
    #include <linux/init.h>
    #include <linux/module.h>
    #include <linux/kernel.h> /* printk() */
    #include <linux/slab.h> /* kmalloc() */
    #include <linux/fs.h> /* everything... */
    #include <linux/errno.h> /* error codes */
    #include <linux/types.h> /* size_t */
    #include <linux/proc_fs.h>
    #include <linux/fcntl.h> /* O_ACCMODE */
    #include <linux/uaccess.h> /* copy_from/to_user */

    MODULE_LICENSE("Dual BSD/GPL");

    int memory_open(struct inode *inode, struct file *filp);
    int memory_release(struct inode *inode, struct file *filp);
    ssize_t memory_read(struct file *filp, char *buf, size_t count, loff_t *
    f_pos);
    ssize_t memory_write(struct file *filp, const char *buf, size_t count,
    loff_t *f_pos);
    void memory_exit(void);
    int memory_init(void);

    /* Structure that declares the usual file */
    /* access functions */
    struct file_operations memory_fops = {
        read: memory_read,
        write: memory_write,
        open: memory_open,
        release: memory_release
    };

    /* Declaration of the init and exit functions */
    module_init(memory_init);
    module_exit(memory_exit);

    /* Global variables of the driver */
    /* Major number */
    int memory_major = 60;

    /* Buffer to store data */
    char *memory_buffer;
    int memory_init(void) {
        int result;

        /* Registering device */
        result = register_chrdev(memory_major, "memory", &memory_fops);
        if (result < 0) {
            printk("<1>memory: cannot obtain major number %d\n", memory_major);
            return result;
        }

        /* Allocating memory for the buffer */
        memory_buffer = kmalloc(1, GFP_KERNEL);
        if (!memory_buffer) {
            result = -ENOMEM;
            goto fail;
        }
        memset(memory_buffer, 0, 1);
        printk("<1>Inserting memory module\n");
        return 0;
    fail:
        memory_exit();
        return result;
    }

    void memory_exit(void) {
        /* Freeing the major number */
        unregister_chrdev(memory_major, "memory");
        /* Freeing buffer memory */
        if (memory_buffer) {
            kfree(memory_buffer);
        }
        printk("<1>Removing memory module\n");
    }

    int memory_open(struct inode *inode, struct file *filp) {
        /* Success */
        return 0;
    }

    int memory_release(struct inode *inode, struct file *filp) {
        /* Success */
        return 0;
    }

    ssize_t memory_read(struct file *filp, char *buf, size_t count,
    loff_t *f_pos) {

        /* Transfering data to user space */
        if (copy_to_user(buf,memory_buffer,1)) {
            // return 0 if copy_to_user fails
            return 0;
        }

        /* Changing reading position as best suits */
        if (*f_pos == 0) {
            *f_pos+=1;
            return 1;
        } else {
            return 0;
        }
    }
    ssize_t memory_write( struct file *filp, const char *buf, size_t count,
    loff_t *f_pos) {
        const char *tmp;
        tmp=buf+count-1;
        if (copy_from_user(memory_buffer,tmp,1)) {
            // return 0 if copy_from_user fails
            return 0;
        }
        return 1;
    }
    ```

    Responda lo siguiente:

    + ¿Para qué sirve la estructura ```ssize_t``` y ```memory_fops```? ¿Y las funciones ```register_chrdev``` y ```unregister_chrdev```?

        _ssize\_t_ se utiliza para representar el tamaño de un bloque alocado de memoria. Tiene la particularidad de que es un tipo signado, es decir, le es posible representar, por ejemplo, el -1 (un valor que retornan varias _syscalls_ para indicar error).

        ```memory_fops``` es una estructura que define para cada operación del driver, la función que se encarga de llevarla a cabo.

        Tanto ```register_chrdev``` como ```unregister_chrdev``` son funciones que se utilizan para registrar y desregistrar (si es que esa palabra existe), respectivamente, el dispositivo char al kernel.

    + ¿Cómo sabe el kernel que funciones del driver invocar para leer y escribir al dispositivo?

        Todo eso se toma de la struct _file\_operations_ que indica en cada variable un puntero a las funciones que implementan las operaciones sobre el dispositivo.

    + ¿Cómo se accede desde el espacio de usuario a los dispositivos en Linux?

        De la misma forma que como con cualquier otro fichero. Tales ficheros están en _/dev_. Para ligar ficheros con dispositivos se utilizan dos números: número mayor y número menor. El número mayor es el que utiliza el kernel para relacionar el fichero con su driver. El número menor es para uso interno del dispositivo (si maneja varios dispositivos dentro de un mismo driver, por ejemplo).

    + ¿Cómo se asocia el modulo que implementa nuestro driver con el dispositivo?

        Se usa la función ```register_char()```.

    + ¿Qué hacen las funciones ```copy_to_user``` y ```copy_from_user```?(<https://developer.ibm.com/technologies/linux/articles/l-kernel-memory-access/>)

        ```copy_to_user``` copia un bloque de datos desde el kernel hacia el espacio de usuario. Esta función requiere de un puntero a una dirección de espacio de usuario, un puntero al buffer del kernel y una longitud en bytes (qué tantos bytes se transferirán). La función retorna 0 si no hubo errores; u otro número que indicará la cantidad de bytes que no se transfirieron.

        ```copy_from_user```, en cambio, copia un bloque de datos desde el espacio de usuario hacia el buffer del kernel. Requiere de un buffer destino (que esté en el kernel), un buffer fuente (que esté en el espacio de usuario) y la longitud de lo que se deba transferir en bytes. La función retorna 0 si no hubo errores; u otro número que indicará la cantidad de bytes que no se transfirieron.

    **NOTA DE LIHUEL**: algo que la práctica no dice (en parte, porque medio que cae de maduro, pero por las dudas lo aclaro, porque yo tuve que volver para atrás) es que tenés que recompilar a memory.c de la misma manera que lo hiciste más arriba (acordate que, a fin de cuentas, es un módulo más xD).

2. Ahora ejecutamos lo siguiente:

    ```sudo mknod /dev/memory c 60 0```

    **NOTA DE LIHUEL**: va a estar medio complicado usar sudo sin antes haber definido los sudoers... Para evitar ponerte a definir eso, simplemente ejecutá su y corré el comando desde el usuario root.

3. Y luego:

    ```sudo insmod memory.ko```

    **NOTA DE LIHUEL**: acordate, bestia, que tenés que ejecutar el ```insmod``` mediante ```/sbin/insmod```.

    Responda lo siguiente:

    + ¿Para qué sirve el comando mknod? ¿Qué especifican cada uno de sus parámetros? ¿Qué son el "major" y el "minor" number? ¿Qué referencian cada uno?

        mknod crea un FIFO (tubería con nombre), un fichero especial de bloques, o un fichero especial de caracteres, con el nombre especificado.

        Un fichero especial es una tripleta (booleano, entero, entero) almacenado en el sistema de ficheros. El valor lógico escoge entre fichero especial de caracteres y fichero especial de bloque. Los dos enteros son los números de dispositivo mayor y menor. El número mayor es el número que utiliza el kernel para identificar al dispositivo. El número menor es un número que utiliza el driver de forma interna (se suele utilizar cuando el driver maneja varios dispositivos desde un mismo módulo).

        Los ficheros especiales de bloque son dispositivos similares a discos (donde se puede acceder a los datos dado un número de bloque, y, por ejemplo, tiene sentido tener un caché de bloques). Todos los otros dispositivos son ficheros especiales de caracteres.

4. Ahora escribimos a nuestro dispositivo

    ```echo -n abcdef > /dev/memory```

5. Ahora leemos desde nuestro dispositivo

    ```more /dev/memory```

Responda lo siguiente:

+ ¿Qué salida tiene el anterior comando? ¿Porque? (ayuda: siga la ejecución de las funciones memory_read y memory_write)

    Se imprime solo f porque es archivo de a caracteres. Si fuera de a bloques y se implementaran las operaciones, sería posible guardar la cadena entera.

+ ¿Cuantas invocaciones a memory_write se realizaron?

    6, una para cada caracter, sin embargo solo quedó lo último que se escribió (la f).

+ ¿Cuál es el efecto del comando anterior? ¿Por qué?

    Va a escribir de a un caracter en el archivo.

+ Hasta aquí hemos desarrollado un ejemplo de un driver muy simple pero de manera completa. En nuestro caso, hemos escrito y leído desde un dispositivo que, en este caso, es la propia memoria de nuestro equipo.

    En el caso de un driver que lee un dispositivo como puede ser un file system, un dispositivo usb, etc. ¿Qué otros aspectos deberíamos considerar que aquí hemos omitido? ayuda: semáforos, ioctl, inb,outb.

    A priori, debería ser un archivo de bloques. También habría que tener en cuenta las operaciones relacionadas a la lectura y escritura del archivo de bloques.
