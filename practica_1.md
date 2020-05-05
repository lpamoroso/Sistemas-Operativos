# Práctica 1

### Preguntas de repaso

1. ¿Qué es la shell? ¿Para qué sirve?

    Una shell es un intérprete de comandos que puede ser interactivo o batch. En unix, las shells son conﬁgurables e intercambiables entre sí.

2. ¿En qué espacio (de usuario o de kernel) se ejecuta?

    En el espacio de usuario. Lógicamente es posible que la shell necesite comunicarse con el hardware, pero siempre lo hará a través de las funciones que soporta el kernel.

3. Si pensamos en el funcionamiento de una shell básica podríamos detallarlo secuencialmente de la siguiente manera: 

    + Esperar a que el usuario ingrese un comando.
    + Procesar la entrada del usuario y obtener el comando con sus parámetros.
    + Crear un nuevo proceso para ejecutar el comando, iniciarlo y esperar que retorne
    + Presentar la salida (de haberla) al usuario.
    + Volver a empezar.

<ul><li style="list-style-type: none;">Este tipo de comportamiento, típico de las shell interactivas, se conoce como REPL(Read-Eval-Print Loop, Ciclo de leer, interpretar e imprimir). Analice cómo implementaría este ciclo básico de interpretación de scripts.</li></ul>

<ul><li style="list-style-type: none;"><ul><li style="list-style-type: none;">La primera parte es un input que espera a que el usuario ingrese algún comando. La segunda, para procesarlo, hay que descomponer el string en partes y buscar el comando para verificar que exista. De ahí, la shell devuelve que no existe el comando si ese fuera el caso. Si el comando existe, hay que pasar al tercer paso: crear el proceso asociado a ese comando y verificar la sintaxis del string. El cuarto paso es un stdout del resultado del proceso y la posterior muerte de este. Luego del stdout, viene el input, esperando al nuevo comando.</li></ul></li></ul>

4. Investigue la system call fork:
    + ¿Qué es lo que realiza?<br/><br/>
        Operación en la que un proceso crea una copia de sí mismo.<br/><br/>
    + ¿Qué retorna?<br/><br/>
        Si es exitosa, la syscall devuelve el PID del proceso hijo al padre, y se devuelve 0 al proceso hijo. Si hubiera alguna falla, -1 es retornado al padre, no se crea ningún proceso hijo y se setea el errno.<br/><br/>
    + ¿Para qué podrian servir los valores que retorna?<br/><br/>
        Para que el padre sepa cual es el PID de su hijo o sepa que hubo un error.<br/><br/>
    + ¿Por qué invocaria a la misma al implementar una shell?<br/><br/>
        Porque cuando es necesario llamar a un comando, es necesario invocar el fork.<br/>

5. Investigue la system call exec:
    + ¿Para qué sirve?<br/><br/>
    Es una funcionalidad del sistema operativo que corre un archivo ejecutable en el contexto de un proceso existente, reemplazando el ejecutable anterior.<br/><br/>
    + ¿Comó se comporta?<br/><br/>
    El PID no cambia, pero lo que si es reemplazado es el código de máquina, los datos, la heap y el stack del proceso.<br/><br/>
    + ¿Cuáles son sus diferentes declaraciones POSIX?<br/><br/>
    execl, execlp, execle, execv, execvp, execvpe<br/>

6. Investigue la system call wait:
    + ¿Para qué sirve?<br/><br/>
    La syscall wait sirve para suspender la ejecución del thread que la invocó hasta que uno de sus hijos termine.
    + Sin ella, ¿qué sucedería, pensando en la implementación de la shell?<br/><br/>
    No sería posible el scheduling como lo conocemos, los procesos se chocarian entre ellos porque no existiría una directiva que los supendiera y los organizara.<br/>

7. En una shell, ¿En qué se diferencian los comandos internos (o built-in) de los externos? ¿Qué función cumple la variable de entorno PATH? Hint: https://www.geeksforgeeks.org/internaland-external-commands-in-linux/
    Los comandos internos se diferencian de los externos ya que los primeros son más rápidos al no tener que recurrir a la variable PATH para encontrarlos. Son comandos que ya vienen por defecto en la shell. La variable PATH es aquella que contiene los diferentes directorios en donde se buscarán los comandos externos que se introduzcan en la shell.

### Scripts

1. 
```bash
    awk -F':' '$3 > 1000' /etc/passwd
```
2. 

```bash
    read proc;
    while [ true ]
    do
            output=$(ps -e -o comm= | grep "$proc" -c);
            echo $output;
            sleep 15;
    done
```

3. 
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

4. 
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

5. 
```bash
    find -type f -printf "%f\n" | tac
```

6. 
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

7. 
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

### Netcat

1. En una terminal meter ```nc -l -p PORT_NUM```. -l es para indicar que escuche y -p para indicar el puerto. En PORT_NUM poner un número de puerto no reservado. Esta terminal será la que escuche.<br/>
El próximo paso es abrir otra terminal y meter ```nc localhost PORT_NUM```, siendo PORT_NUM el número que usamos antes.<br/>
2. Primero abrir la terminal server con el comando ```nc -l -p PORT_NUM | cat >> FILE_NAME```. Esto hará que lo que sea que llegue al puerto PORT_NUM, sea redirigido a FILE_NAME. Mientras la terminal server está abierta, en el cliente meter ```cat /etc/passwd | nc localhost PORT_NUM``` para transmitir /etc/passwd a localhost:PORT_NUM.
3. 
4. 
5. Pareciera ser un ciclo. Empieza por mostrar lo que hay en el pipe y eso pasarlo a un shell interactivo(que corno es el 2>&1 ese????), luego eso lo pasa al netcat que a su vez escribe en el pipe. //CORREGIR

### Subshell

1. Una subshell es un subproceso creado por la shell(o un shellscript) que está en ejecución. La misma va a ser otra instancia de bash que, luego del fork, se va a convertir en un comando específico(por ejemplo, pstree) desencadenado por un execv.
2. En los siguientes casos se crea una subshell:
    + De manera manual( con los paréntesis ()).
    + Comandos externos(a menos que se los ejecute con exec, lo que reemplazaría la shell en la que se lo está ejecutando).
    + Los pipes(cada término de pipe genera una subshell diferente).
    + Uso del modo background(&).
    + Uso de sustitución de comandos(comandos del tipo ```vi $(find ~ -name myprog.c)``` requieren de una subshell).
3. De forma secuencial, ya que requieren la salida de una de las partes para poder devolver un resultado. //CORREGIR
4. No, las variables de la subshell no son accesibles por el proceso padre.
5. Compare el resultado de los siguientes agrupamientos de comandos (ejecutar los comando desde el home directory del usuario)
    ```bash
    CATEDRA=so
    ( cd /tmp; echo $CATEDRA > subshell; CATEDRA=so1 )
    echo CATEDRA
    { cd /tmp; echo $CATEDRA > subshell1; CATEDRA=so2; }
    echo CATEDRA
    ```

    + En el primer agrupamiento de comandos, ¿Qué valor queda en el archivo subshell? Al terminar el comando, ¿se modificó el valor de la varible CATEDRA? Justificar

        El valor del archivo _subshell_ es so. Al terminar el comando, el valor de la variable CÁTEDRA **NO** fue modificado. Esto se debe a que se usó una subshell y no la shell en la que la variable estaba declarada.

    + En el segundo agrupamiento de comandos, ¿Qué valor queda en el archivo subshell1? Al terminar el comando, ¿se modificó el valor de la varible CATEDRA? Justificar

        El valor del archivo _subshell1_ sigue siendo so, ya que la variable antes no había cambiado. Al terminar el comando, en este caso, el valor de la variable CÁTEDRA **SI** fue modificado. Esto se debe a que los cambios fueron hechos en la shell en la que se estaba trabajando y no en alguna otra subshell.

    + Además de lo visto en los puntos anterior, ¿Notó alguna otra diferencia?

        Algo que noté fue el uso de las llaves que no me terminó de cerrar por qué. Probé sacárselas y el resultado fue el mismo. Busqué entonces para qué servían y leí que suele usárselas para iterar sobre parámetros.