# Práctica 1

## Preguntas de repaso

1. ¿Qué es la shell? ¿Para qué sirve?

    Una shell es un intérprete de comandos que puede ser interactivo o batch. En unix, las shells son conﬁgurables e intercambiables entre sí.

2. ¿En qué espacio (de usuario o de kernel) se ejecuta?

    En el espacio de usuario. Lógicamente, es posible que la shell necesite comunicarse con el hardware, pero siempre lo hará a través de las funciones que soporta el kernel.

3. Si pensamos en el funcionamiento de una shell básica podríamos detallarlo secuencialmente de la siguiente manera:

    + Esperar a que el usuario ingrese un comando.
    + Procesar la entrada del usuario y obtener el comando con sus parámetros.
    + Crear un nuevo proceso para ejecutar el comando, iniciarlo y esperar que retorne
    + Presentar la salida (de haberla) al usuario.
    + Volver a empezar.

    Este tipo de comportamiento, típico de las shell interactivas, se conoce como REPL(Read-Eval-Print-Loop, Ciclo de leer, interpretar e imprimir). Analice cómo implementaría este ciclo básico de interpretación de scripts.

    La primera parte es un input que espera a que el usuario ingrese algún comando. La segunda, para procesarlo, hay que descomponer el string en partes y buscar el comando para verificar que exista. De ahí, la shell devuelve que no existe el comando si ese fuera el caso. Si el comando existe, hay que pasar al tercer paso: crear el proceso asociado a ese comando y verificar la sintaxis del string. El cuarto paso es un stdout del resultado del proceso y la posterior muerte de este. Luego del stdout, viene el input, esperando al nuevo comando.

4. Investigue la system call fork:
    + ¿Qué es lo que realiza?

        Operación en la que un proceso crea una copia de sí mismo.

    + ¿Qué retorna?

        Si es exitosa, la syscall devuelve el PID del proceso hijo al padre, y se devuelve 0 al proceso hijo. Si hubiera alguna falla, -1 es retornado al padre, no se crea ningún proceso hijo y se setea el errno.

    + ¿Para qué podrían servir los valores que retorna?

        Para que el padre sepa cual es el PID de su hijo o sepa que hubo un error.

    + ¿Por qué invocaria a la misma al implementar una shell?

        Porque cuando es necesario llamar a un comando, es necesario invocar el fork.

5. Investigue la system call exec:
    + ¿Para qué sirve?

        Es una funcionalidad del sistema operativo que corre un archivo ejecutable en el contexto de un proceso existente, reemplazando el ejecutable anterior.

    + ¿Comó se comporta?

        El PID no cambia, pero lo que si es reemplazado es el código de máquina, los datos, la heap y el stack del proceso.

    + ¿Cuáles son sus diferentes declaraciones POSIX?

        execl, execlp, execle, execv, execvp, execvpe

6. Investigue la system call wait:
    + ¿Para qué sirve?

        La syscall wait sirve para suspender la ejecución del thread que la invocó hasta que uno de sus hijos termine.

    + Sin ella ¿Qué sucedería pensando en la implementación de la shell?

        No sería posible el scheduling como lo conocemos, los procesos se chocarian entre ellos porque no existiría una directiva que los supendiera y los organizara.

7. En una shell, ¿En qué se diferencian los comandos internos (o built-in) de los externos? ¿Qué función cumple la variable de entorno PATH? [Hint](https://www.geeksforgeeks.org/internal-and-external-commands-in-linux/).

    Los comandos internos se diferencian de los externos ya que los primeros son más rápidos al no tener que recurrir a la variable PATH para encontrarlos. Son comandos que ya vienen por defecto en la shell. La variable PATH es aquella que contiene los diferentes directorios en donde se buscarán los comandos externos que se introduzcan en la shell.

## Scripts

1. Realice un script que guarde en el archivo /tmp/usuarios los nombres de los usuarios del sistema cuyo UID sea mayor a 1000.

    ```bash
        awk -F':' '$3 > 1000' /etc/passwd
    ```

2. Implemente un script que reciba como parámetro el nombre de un proceso e informe cada 15 segundos cuántas instancias de ese proceso están en ejecución.

    ```bash
        read proc;
        while [ true ]
        do
                output=$(ps -e -o comm= | grep "$proc" -c);
                echo $output;
                sleep 15;
        done
    ```

3. Desarrolle un script que guarde en un arreglo todos los archivos del directorio actual (incluyendo sus subdirectorios) para los cuales el usuario que ejecuta el script tiene permisos de ejecución. Luego, implemente las siguientes funciones:
   + cantidad: Imprime la cantidad de archivos que se encontraron
   + archivos: Imprime los nombres de los archivos encontrados en orden alfabético

    ```bash
        function cantidad(){
                echo $(find . -type f -executable | wc -l);
        }

        function archivos(){
                echo $(find . -type f -executable | cut -d"/" -f 2);
        }

        read option;
        if [ $option == 'a' ]
        then
                cantidad;
        else
                archivos;
        fi
    ```

4. Se le ha encomendado organizar las fotos (en formato jpg) de todos los eventos de los que su empresa ha participado en el último año, los cuales se encuentran organizados en directorios con el nombre del evento. Para facilitar su búsqueda posterior, los archivos deben tener nombres que sigan el siguiente patrón: EVENTO-N.jpg, donde:
    + EVENTO es el nombre del evento (el del directorio que se está procesando)
    + N es un índice de foto, comenzando en 1

    Realice un script que renombre los archivos de cada subdirectorio del directorio actual siguiendo lo especificado en el párrafo anterior.

    Ejemplo: dada la siguiente estructura de archivos y directorios:

    ```bash
    bashconf15/
        DSC01050.jpg
        DSC01051.jpg
        DSC01052.jpg
        DSC01053.jpg
        DSC01054.jpg
    jsconf−14/
        DSC01230.jpg
        DSC01231.jpg
        DSC01232.jpg
        DSC01235.jpg
        DSC01236.jpg
    oktoberfest−14/
        DSC02229.jpg
        DSC02230.jpg
        DSC02231.jpg
        DSC02232.jpg
    ```

    Se desea terminar con la siguiente estructura luego de ejecutar su script:

    ```bash
    bashconf15/
        bashconf15−1.jpg
        bashconf15−2.jpg
        bashconf15−3.jpg
        bashconf15−4.jpg
        bashconf15−5.jpg
    jsconf−14/
        jsconf−14−1.jpg
        jsconf−14−2.jpg
        jsconf−14−3.jpg
        jsconf−14−4.jpg
        jsconf−14−5.jpg
    oktoberfest−14/
        oktoberfest−14−1.jpg
        oktoberfest−14−2.jpg
        oktoberfest−14−3.jpg
        oktoberfest−14−4.jpg
    ```

    ```bash
        for OUTPUT in $(echo */)
        do
            folder=$(echo $OUTPUT | tr -d /);
            cur=1;
            for FILES in $(find $OUTPUT -type f -name *.jpg)
            do
                    mv $FILES $OUTPUT$folder-$cur.jpg;
                    cur=$((cur + 1));
            done
        done
    ```

5. Escriba un script que liste en orden alfabético inverso el contenido del directorio actual. Es decir, si el contenido son los archivos:

    archivo_1.txt articulo.doc directorio directorio_2 script.sh

    se espera que el script los liste de la siguiente manera:

    script.sh directorio_2 directorio articulo.doc archivo_1.txt

    ```bash
        find -type f -printf "%f\n" | tac
    ```

6. Realice un script que copie todos los archivos del directorio home del usuario que lo ejecuta, a un subdirectorio del mismo llamado backup cambiándoles el nombre para que esté en mayúsculas. No se deben procesar los subdirectorios del home del usuario, únicamente los archivos ubicados directamente en este. Si el directorio backup existe al iniciar el script, el contenido del mismo debe borrarse antes de copiar los archivos.

    Ejemplo: si el home del usuario actual contiene:

    ```bash
    /
    home/
    mi_usuario/
    so/
    practica1.pdf
    ejercicios/
    ejercicio−1.sh
    ejercicio−2.sh
    archivo1.txt
    mi−script.sh
    otro_archivo.txt
    ```

    se espera tener lo siguiente luego de la ejecución del script:

    ```bash
    /
    home/
    mi_usuario/
    backup/
    ARCHIVO1.TXT
    MI−SCRIPT.SH
    OTRO_ARCHIVO.TXT
    so/
    practica1.pdf
    ejercicios/
    ejercicio−1.sh
    ejercicio−2.sh
    archivo1.txt
    mi−script.sh
    otro_archivo.txt
    ```

    ```bash
        function backup(){
                mkdir /home/$USER/backup;
                for FILES in $(find /home/$USER/ -maxdepth 1 -type f)
                do
                        cp $FILES /home/$USER/backup;
                done
        }

        if [ -d "/home/$USER/backup" ]
        then
                echo "Backup seems to exist, deleting directory..."
                rmdir /home/$USER/backup;
                echo "Updating Backup";
                backup
        else
                echo "It seems that it's not backup. Creating one"
                backup
        fi
    ```

7. Un escritor tiene organizados los capítulos de su próximo libro en distintos archivos de texto plano en un mismo directorio, y le ha solicitado ayuda para concatenar el contenido de cada uno de ellos en un único archivo final llamado libro.txt, de modo tal que éste último contenga el texto de todos los otros archivos, uno luego del otro. Puede asumir que los archivos de los capítulos tienen nombres alfabéticamente ordenados: capitulo-01.txt, capitulo-02.txt, ..., capitulo-48.txt, por ejemplo.

    Tip: man cat.

    ```bash
        read cant_chapters;
        if [ -f "libro.txt"  ]
        then
                rm libro.txt;
        fi
        touch libro.txt;
        for (( i=1;i<=cant_chapters;i++ ))
        do
                cat capitulo-$i.txt | cat >> libro.txt;
                echo $'\n' | cat >> libro.txt;
        done
    ```

## Redes y Sistemas Operativos

1. En una terminal meter ```nc -l -p PORT_NUM```. -l es para indicar que escuche y -p para indicar el puerto. En PORT_NUM poner un número de puerto no reservado. Esta terminal será la que escuche.

    El próximo paso es abrir otra terminal y meter ```nc localhost PORT_NUM```, siendo PORT_NUM el número que usamos antes.

2. Primero abrir la terminal server con el comando ```nc -l -p PORT_NUM | cat >> FILE_NAME```. Esto hará que lo que sea que llegue al puerto PORT_NUM, sea redirigido a FILE_NAME. Mientras la terminal server está abierta, en el cliente meter ```cat /etc/passwd | nc localhost PORT_NUM``` para transmitir /etc/passwd a localhost:PORT_NUM.

    Tip: recordar pipes y redirecciones.

3. Desarrolle un script que reciba en su entrada estándar una lista de hosts e imprima en su salida estándar únicamente aquellos que tienen el puerto 80 abierto. Cuando un host no tiene el puerto 80 abierto, netcat tardará varios segundos en determinar que la conexión
no se puede establecer.

    Tip: utilizar la opción -w de netcat para disminuir el tiempo de timeout.

    //PREGUNTAR

4. Desarrolle un script que reciba en su entrada estándar una lista de hosts con el puerto 80 abierto y, para cada uno, realice un requerimiento HTTP GET a la URI raíz y devuelva el valor del campo Content-Length de la respuesta. Deberá ser posible utilizar como entrada estándar la salida estándar del script anterior.

    ```bash
    echo www.google.com www.debian.org www.linti.unlp.edu.ar | ./cl.sh
    www.google.com: 262
    www.debian.org: 470
    www.linti.unlp.edu.ar: 223
    ```

    Tip: ```printf "GET / HTTP/1.0\r\n\r\n"| ...```

    //PREGUNTAR

5. Interprete y describa qué es lo que hace el siguiente fragmento de código extraído de la man page de netcat.

    ```bash
    rm −f /tmp/f; mkfifo /tmp/f
    cat /tmp/f | /bin/sh −i 2>&1 | nc −l 127.0.0.1 1234 > /tmp/f
    ```

    Tip: man mkfifo

    Pareciera ser un ciclo. Empieza por mostrar lo que hay en el pipe y eso pasarlo a un shell interactivo(que corno es el 2>&1 ese????), luego eso lo pasa al netcat que a su vez escribe en el pipe. //PREGUNTAR

6. Desarrolle un script que permita al usuario chatear con otra instancia del mismo script. Para ello, el script deberá recibir como parámetro si va a funcionar como (c)liente o como (s)ervidor. También deberá recibir como parámetro un nickname para el usuario. Por ejemplo, para invocar el script en modo servidor con el nick jvg, debería ejecutar:

    ```bash
    ./nc−chat.sh s jvg
    ```

    Además, los mensajes transmitidos deben comenzar con la fecha, hora y nick del emisor. Por ejemplo:

    ```bash
    Thu Mar 12 13:03:14 ART 2015, jvg says:
    Knock knock Neo.
    ```

    //PREGUNTAR

### Subshell

1. ¿Qué es una subshell?

   Una subshell es un subproceso creado por la shell (o un shellscript) que está en ejecución. La misma va a ser otra instancia de bash que, luego del fork, se va a convertir en un comando específico (por ejemplo, pstree) desencadenado por un execv.

2. Indique en cuáles situaciones se puede generar una subshell

   En los siguientes casos se crea una subshell:
    + De manera manual ( con los paréntesis ()).
    + Comandos externos (a menos que se los ejecute con exec, lo que reemplazaría la shell en la que se lo está ejecutando).
    + Los pipes (cada término de pipe genera una subshell diferente).
    + Uso del modo background (&).
    + Uso de sustitución de comandos (comandos del tipo ```vi $(find ~ -name myprog.c)``` requieren de una subshell).

3. Los comandos que forman parte de un pipe ¿Ejecutan en secuencialmente o en parelelo?

   De forma secuencial, ya que requieren la salida de una de las partes para poder devolver un resultado. //PREGUNTAR

4. Las variables de una subshell ¿Pueden ser accedidas por la shell padre?

   No, las variables de la subshell no son accesibles por el proceso padre.

5. Compare el resultado de los siguientes agrupamientos de comandos (ejecutar los comandos desde el home directory del usuario)

    ```bash
    CATEDRA=so
    ( cd /tmp; echo $CATEDRA > subshell; CATEDRA=so1 )
    echo CATEDRA
    { cd /tmp; echo $CATEDRA > subshell1; CATEDRA=so2; }
    echo CATEDRA
    ```

    + En el primer agrupamiento de comandos ¿Qué valor queda en el archivo subshell? Al terminar el comando ¿Se modificó el valor de la varible CATEDRA? Justificar

        El valor del archivo _subshell_ es so. Al terminar el comando, el valor de la variable CÁTEDRA **NO** fue modificado. Esto se debe a que se usó una subshell y no la shell en la que la variable estaba declarada.

    + En el segundo agrupamiento de comandos ¿Qué valor queda en el archivo subshell1? Al terminar el comando ¿Se modificó el valor de la varible CATEDRA? Justificar

        El valor del archivo _subshell1_ sigue siendo so, ya que la variable antes no había cambiado. Al terminar el comando, en este caso, el valor de la variable CÁTEDRA **SI** fue modificado. Esto se debe a que los cambios fueron hechos en la shell en la que se estaba trabajando y no en alguna otra subshell.

    + Además de lo visto en los puntos anterior, ¿Notó alguna otra diferencia?

        Algo que noté fue el uso de las llaves que no me terminó de cerrar por qué. Probé sacárselas y el resultado fue el mismo. Busqué entonces para qué servían y leí que suele usárselas para iterar sobre parámetros.
