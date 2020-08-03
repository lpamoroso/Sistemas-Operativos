# Práctica 6

## Preguntas teóricas

### Conceptos generales

1. ¿Qué es Android?

    Android es un sistema operativo móvil desarrollado por Google, basado en Kernel de Linux y otros *software* de código abierto. Fue diseñado para dispositivos móviles con pantalla táctil, como teléfonos inteligentes, tabletas, relojes inteligentes, automóviles y televisores.

2. ¿Qué versión del kernel de Linux se esta utilizando para desarrollar Android?

    La última versión es la 4.9.

3. ¿Qué aprovecha Android de Linux?

    * Abstracción de hardware.
    * Administración de memoria.
    * Administración de CPI.
    * Networking.
    * Seguridad.

4. ¿Cuál es la mascota de Android? ¿Cuál es su etimología?

    La mascota es _Andy_, del libro _Do Androids Dream of Electric Sheep?_ de Philip K. Dick.

5. ¿Cuándo y por quién nació Android?

    En octubre de 2003, Andy Rubin, Rich Miner, Chris White y Nick Sears fundan Android.

6. ¿Cuándo fue comprado por la empresa Google?

    En julio de 2005.

7. ¿Qué es la OHA?

    La Open Handset Alliance es una alianza comercial de 84 compañías que se dedica a desarrollar estándares abiertos para dispositivos móviles.

8. ¿En qué año se lanzo la primer versión estable?

    En 2008.

9. ¿Cuál es la última versión estable de Android?

    Android 10.

10. ¿Con qué frecuencia se lanzan las versiones del SDK de Android?

    Cada año, aproximadamente.

11. Lea [esta](https://developer.android.com/guide/platform/index.html) página y conteste:
    1. ¿Qué rol cumple la capa del kernel de Linux?

        La capa del kernel Linux es la base de Android. La ART se apoya en el kernel de linux para ejecutar funcionalidades varias como hilos o manejod e memoria a bajo nivel.

    2. ¿Qué rol cumple la HAL?

        La HAL provee interfaces estándar que exponen las capasidades del hardware del dispositivo a las capas superiores del Java API Framework. La HAL se compone de varias bibliotecas de módulos, cada una de las cuales implementa una interfaz para un hardware específico, como puede ser la cámara o el módulo de bluetooth.

    3. ¿Qué rol cumple la capa de librerias?

        Muchos componentes del núcleo de Android, como la ART o la HAL, están construídos de código nativoque requiere bibliotecas nativas escritas en C o C++. La plataforma de Android provee Java Framework APIs para exponer la funcionalidad de algunas de esas bibliotecas nativas a las apps.

    4. ¿Qué rol cumple la capa de Android Runtime?

        Para las versiones superiores a las 5.0, cada app corre en su propio proceso y con su propia instancia de la ART. La ART está escrita para correr múltiples máquinas virtuales en dispositivos de poca memoria ejecutando archivos DEX.

    5. ¿Por qué fue reemplazada la DVM? ¿Qué mejora?

        Fue reemplazada por la diferencia de velocidad a la hora de mover aplicaciones y el sistema operativo en sí, debido al hecho de que elimina la necesidad de una máquina virtual o de interpretar el código de las aplicaciones, lo cual se ve reflejado en mejoras en el desempeño de la aplicación desde el lanzamiento. Entre otras cosas, consume menos memoria, ejecuta sus propios bytes codes, la compilación es AOT (Ahead of time) y se optimiza el garbage collector.

    6. ¿Qué rol cumple la capa de Application Framework?

        Todas las características de Android están disponibles a través de APIs escritas en Java. Estas APIs forman la base para crear apps, simplificando el reuso del núcleo, componentes del sistema modulares y servicios.

### Aplicaciones

1. ¿Qué componentes de una aplicación Android existen? ¿Para que sirve cada uno?

    * Vista (View): las vistas son los elementos que componen la interfaz de usuario de una aplicación. Todas las vistas van a ser objetos descendientes de la clase _View_, y por tanto, pueden ser definidas utilizando código Java.
    * Layout: un layout es un conjunto de vistas agrupadas de una determinada forma. Vamos a disponer de diferentes tipos de layouts para organizar las vistas de forma lineal, en cuadrícula o indicando la posición absoluta de cada vista. Los layouts también son objetos descendientes de la clase View.
    * Actividad (Activity): una aplicación en Android va a estar formada por un conjunto de elementos básicos de visualización, coloquialmente conocidos como pantallas de la aplicación. En Android cada uno de estos elementos, o pantallas, se conoce como actividad. Su función principal es la creación de la interfaz de usuario. Una aplicación suele necesitar varias actividades para crear la interfaz de usuario. Las diferentes actividades creadas serán independientes entre sí, aunque todas trabajarán para un objetivo común. Una actividad se define en una clase descendiente de Activity y utiliza un layout para que define su apariencia.
    * Fragmentos (Fragment): La llegada de las _tablets_ trajo el problema de que las aplicaciones de Android ahora deben soportar pantallas más grandes. Si diseñamos una aplicación pensada para un dispositivo móvil y luego la ejecutamos en una _tablet_, el resultado no suele resultar satisfactorio.

        Para ayudar al diseñador a resolver este problema, en la versión 3.0 de Android aparecen los fragments. Un fragment está formado por la unión de varias vistas para crear un bloque funcional de la interfaz de usuario. Una vez creados los fragments, podemos combinar uno o varios fragments dentro de una actividad, según el tamaño de pantalla disponible.
    * Servicio (Service): un servicio es un proceso que se ejecuta “detrás”, sin la necesidad de una interacción con el usuario. Es algo parecido a un demonio en Unix o a un servicio en Windows. Se utilizan cuando queramos tener en ejecución un código de manera continua, aunque el usuario cambie de actividad. En Android disponemos de dos tipos de servicios: servicios locales, que son ejecutados en el mismo proceso; y servicios remotos, que son ejecutados en procesos separados.
    * Intención (Intent): Una intención representa la voluntad de realizar alguna acción; como realizar una llamada de teléfono, visualizar una página web. Se utiliza cada vez que queramos:
      * Lanzar una actividad
      * Lanzar un servicio
      * Enviar un anuncio de tipo broadcast
      * Comunicarnos con un servicio

      Los componentes lanzados pueden ser internos o externos a nuestra aplicación. También utilizaremos las intenciones para el intercambio de información entre estos componentes.
    * Receptor de anuncios (Broadcast Receiver): Un receptor de anuncios recibe anuncios broadcast y reacciona ante ellos. Los anuncios broadcast pueden ser originados por el sistema (por ejemplo: Batería baja, Llamada entrante) o por las aplicaciones. Las aplicaciones también pueden crear y lanzar nuevos tipos de anuncios broadcast. Los receptores de anuncios no disponen de interfaz de usuario, aunque pueden iniciar una actividad si lo estiman oportuno.
    * Proveedores de Contenido (Content Provider): En muchas ocasiones las aplicaciones instaladas en un terminal Android necesitan compartir información. Android define un mecanismo estándar para que las aplicaciones puedan compartir datos sin necesidad de comprometer la seguridad del sistema de ficheros. Con este mecanismo podremos acceder a datos de otras aplicaciones, como la lista de contactos, o proporcionar datos a otras aplicaciones.

2. ¿Qué es Gradle? ¿Qué tarea permite realizar?

    Gradle es una herramienta que permite la automatización de compilación de código abierto, la cual se encuentra centrada en la flexibilidad y el rendimiento.

3. ¿A qué hacen referencia y para qué sirven las varibles compileSdkVersion, minSdkVersion, targetSdkVersion y buildToolsVersion? ¿Tienen que tener alguna relación entre ellas? ¿Y la variable versionCode? ¿Que impacto tiene en la distribución de una aplicación a través de Google Play Store?

    * compileSdkVersion: es la forma de decirle a Gradle qué versión del SDK de Android usar para compilar la aplicación.
    * minSdkVersion: si la variable compileSdkVersion está seteada a la API más reciente que haya, minSdkVersion indica el mínimo SDK del que debe disponerse para correr la app. Google Play utiliza esta variable para definir si un dispositivo puede o no instalarse una determinada app.
    * targetSdkVersion: esta variable es la forma principal en que android provee compatibilidad hacia adelante al no aplicar cambios de comportamiento a menos que tal variable sea actualizada. Esto es lo que permite que se usen nuevas APIs (siempre y cuando cambiemos el compileSdkVersion) previo a trabajar con los cambios que éstas traen.
    * buildToolsVersion: es la versión específica del compilador a usar. Para cada nivel de API, hay varias actualizaciones del compilador y es por eso que es importante indicar con cuál se desea trabajar.

        Todas estas variables se relacionan ya que son las que deben definirse previo a lanzar una app. La variable versionCode indica la versión de nuestra app. Impacta significativamente en la distribución en Google Play ya que:

        * Los usuarios deben contar con información específica sobre la versión de la app instalada en sus dispositivos y las versiones de actualización disponibles para su instalación.
        * Otras apps, incluidas las que publiques como un conjunto, deben realizar consultas al sistema respecto de la versión de tu app para determinar la compatibilidad e identificar dependencias.
        * Es posible que los servicios a través de los cuales publicarás tus apps también deban consultarlas para conocer su versión, a fin de poder mostrarla a los usuarios. También puede ser necesario que un servicio de publicación deba verificar la versión de tu app para determinar la compatibilidad y establecer relaciones de cambio a versiones anteriores o posteriores.

4. ¿Todas las aplicaciones tiene que estar firmadas digitalmente para ejecutar en un dispositivo? ¿A través de que mecanismo se realiza la firma digital? ¿A través de que herramienta se lo puede hacer?

    Android requiere que todos los APK estén firmados de manera digital con un certificado para poder instalarse o actualizarse en un dispositivo. Si usas Android App Bundle, debes firmar solamente el paquete de aplicación antes de subirlo a Play Console, y la firma de apps de Google Play se encargará del resto. Sin embargo, también puedes firmar manualmente tu app para subirla a Google Play y a otras tiendas de aplicaciones (utilizando _apksigner_, por ejemplo).

5. ¿Qué es el application sandbox?

    La plataforma Android aprovecha la protección basada en el usuario de Linux para identificar y aislar los recursos de la aplicación. Esto aísla las aplicaciones entre sí y protege las aplicaciones y el sistema de aplicaciones maliciosas. Para hacer esto, Android asigna una identificación de usuario (UID) única a cada aplicación de Android y la ejecuta en su propio proceso.

6. ¿Existe la posibilidad de que dos aplicaciones compartan el userId?, ¿Qué debe respetarse?

    Existe esa posibilidad, solo si ámbas apps están firmadas con el mismo certificado del desarrollador.

7. ¿Qué alternativas tienen las aplicaciones para compartir su datos?

    Declarar todo en el AndroidManifest.xml
    //PREGUNTAR

8. ¿Dónde se define el acceso a los recursos por parte de las aplicaciones? ¿Pueden realizarse cambios en tiempo de ejecución? ¿Qué excepcion se lanza si una aplicación intenta acceder a un recurso sobre el cual no definio permisos?

    En el manifest.xml se definen los permisos que la aplicación necesita. Al principio los permisos eran estáticos, pero a partir de la versión 6.0 se hicieron dinámicos. Si una aplicación intenta acceder a un recurso que no declaró, el sistema lanza una SecurityException.

9. ¿En que momento el usuario le da permiso a la aplicación para que esta utilice los recursos del dispositivo? ¿Se pueden definir de manera dinámica?

     Cuando la aplicación es instalada, el usuario debe dar su consentimiento explícitamente. A partir de Android 6.0, el acceso a los permisos es dinámico.

10. ¿Qué es y qué contiene un APK?

    Un Android PacKage es un paquete para el sistema operativo Android. Este formato es una variante del formato JAR de Java y contiene todo lo necesario para ejecutar una aplicación en un dispositivo.

11. ¿Para qué sirve el archivo AndroidManifest.xml? ¿Qué son los archivos .dex?

    Todos los proyectos de apps deben tener un archivo AndroidManifest.xml (con ese mismo nombre) en la raíz de la fuente del proyecto. El archivo de manifiesto describe información esencial de la aplicación para las herramientas de creación de Android, el sistema operativo Android y Google Play.

    Entre muchas otras cosas, el archivo de manifiesto debe declarar lo siguiente:

    * El nombre del paquete de la aplicación.
    * Los componentes de la aplicación.
    * Los permisos que necesita la aplicación para acceder a las partes protegidas del sistema o a otras aplicaciones.
    * Las funciones de hardware y software que requiere la aplicación afectan a los dispositivos que pueden instalar la aplicación desde Google Play.

    Los archivos Dalvik son archivos de desarrolladores que se utilizan para inicializar y ejecutar aplicaciones desarrolladas para el sistema operativo móvil Android. Los datos almacenados en estos archivos DEX incluye código que localiza y inicializa otros archivos de programa de la aplicación, que se requieren para ejecutar el programa compilado.

12. Detalle el proceso de firma (signing) de un APK.

    A continuación, se proporciona una descripción general de los pasos para firmar y publicar una app nueva en Google Play:

    1. Generar una clave de carga y un almacén de claves: sirve para que la app reciba la firma de Google Play. Se genera mediante Android Studio.
    2. Firmar la app con la clave de carga: si ya tenés una clave de carga, usala para firmar tu app. Si, en cambio, tu app ya está firmada y publicada en Google Play Store con una clave de firma de la app existente, usala para firmarla y asegurate de encriptarla y exportarla para que la app se registre en Google Play. Luego, podrás generar una clave de carga separada y registrar el certificado público de clave de carga con Google Play para firmar y subir las actualizaciones subsiguientes a la app.
    3. Habilitar la firma de apps de Google Play: la firma de apps de Google Play es la manera recomendada de firmar apps para su distribución mediante Google Play. Los pasos que debés seguir para inscribir la app dependen de si ya se publicó en Google Play o de si ya está firmada y publicada mediante un clave de firma de app existente.
    4. Subir la app a Google Play: después de compilar y firmar la versión de lanzamiento de tu app, el siguiente paso es subirla a Google Play para inspeccionarla, probarla y publicarla.
    5. Preparar e implementar el lanzamiento de la app.

### Procesos

1. ¿Qué comandos de GNU/Linux tenemos disponibles en Android?

    //PREGUNTAR

2. ¿Android cuenta con un área de intercambio? ¿Por qué?

    Si, y se denomina zRAM. Esta es una partición de la RAM utilizada para área de intercambio. Cuando se colocan en zRAM, se comprimen todos los elementos; luego, se descomprimen cuando se los saca de allí. El tamaño de esta parte de la RAM aumenta o disminuye cuando se agregan páginas a zRAM o que se quitan de esta. Los fabricantes de dispositivos pueden establecer el tamaño máximo.

3. ¿Qué alternativa tiene a la hora liberar memoria? ¿Cómo se clasifica los procesos?

    Utiliza dos técnicas:
    * El daemon de intercambio de kernel (kswapd): es parte del kernel de Linux y convierte la memoria usada en memoria libre. El daemon se activa cuando queda poca memoria libre en el dispositivo. El kernel de Linux mantiene umbrales de memoria libre mínimos y máximos. Cuando la memoria libre cae por debajo del umbral mínimo, el kswapd comienza a reclamar memoria. Una vez que la memoria libre alcanza el umbral máximo, el kswapd deja de reclamarla.
    * Optimizador de poca memoria. El optimizador se pone en marcha cuando el kswapd no es suficiente. En concreto, le avisa a las aplicaciones que hay poca memoria y que dejen de realizar asignaciones. Si eso no alcanza, empieza a cerrar procesos. El orden que respeta es el que sigue:
      * Apps en segundo plano: son apps que se ejecutaron y que por el momento no están activas. El LMK cerrará primero las apps en segundo plano, comenzando por la de mayor oom_adj_score.
      * App anterior: es la app en segundo plano que se usó más recientemente. La app anterior tiene mayor prioridad (una puntuación más baja) que las apps en segundo plano porque es más probable que el usuario la elija en lugar de utilizar una de las apps en segundo plano.
      * App de inicio: esta es la app del selector. Si la cierras, desaparecerá el fondo de pantalla.
      * Servicios: las apps inician los servicios, que pueden incluir la sincronización o la carga en la nube.
      * Apps perceptibles: Apps que no están en primer plano pero que son perceptibles para el usuario de alguna manera, como ejecutar un proceso de búsqueda que muestra una IU pequeña o escuchar música.
      * App en primer plano: La app que se está utilizando en ese momento. Cerrar la app en primer plano parece una falla que podría indicarle al usuario que algo funciona mal en el dispositivo.
      * (Servicios) persistentes: Son servicios principales para el dispositivo, como telefonía y Wi-Fi.
      * Sistema: Procesos del sistema. A medida que se cierran estos procesos, es posible que parezca que el teléfono se reinicia.
      * Nativo: procesos de muy bajo nivel que usa el sistema (por ejemplo, kswapd).

4. Lea [este](http://developer.android.com/guide/components/processes-and-threads.html) artículo y responda:
   1. Por defecto ¿En cuántos procesos/threads corren los componentes de una aplicación?

        Por defecto, Android inicia un nuevo proceso de Linux para la aplicación con un único thread de ejecución. De manera predeterminada, todos los componentes de la misma aplicación se ejecutan en el mismo proceso y thread (que se denomina "thread principal").

   2. ¿Los componentes de las aplicaciones pueden correr en procesos separados y/o en el mismo proceso?

        Es posible configurar que diferentes componentes de la aplicación se ejecuten en procesos separados.

   3. ¿Se pueden crear n threads de un proceso?

        Es posible crear threads adicionales para cualquier proceso.

   4. ¿Los componentes de la vista (UI) de una aplicación son thread-safe? ¿Qué reglas sigue el _Android's single thread model_?

        El paquete de herramientas de la UI de Android no es thread-safe. En consecuencia, no ha de manipularse la UI desde un thread de trabajo (la manipulación de la interfaz de usuario debe realizarse desde el thread de UI). Por lo tanto, solo existen dos reglas para el Android's single thread model:
        1. No bloquear el thread de UI.
        2. No acceder al paquete de herramientas de la UI de Android desde fuera del thread de UI.

5. ¿Qué es DVM? ¿Por qué fue diseñada? Compare este concepto con el de JVM. Adicionalmente compare el concepto de DVM con el de ART en base a [este](http://www.addictivetips.com/android/art-vs-dalvik-android-runtime-environments-explained-compared/) y [este](https://en.wikipedia.org/wiki/Android_Runtime) artículo.

    Dalvik Virtual Machine (DVM) es la máquina virtual que utiliza Android. Permite ejecutar aplicaciones programadas en Java. La DVM no afirma ser una máquina virtual de java (JVM) debido a que le ocasionaría problemas de licenciamiento, sin embargo cumple ese propósito. La mayoría de los programas escritos en Java 5 pueden correr sobre la DVM. DVM sacrifica la portabilidad que caracteriza a Java para poder crear aplicaciones con un mejor rendimiento y menor consumo de energía, estas dos características son extremadamente importantes en dispositivos móviles, debido a que la capacidad de las baterías en estos dispositivos es limitada. Está optimizada para requerir poca memoria y está diseñada para permitir ejecutar varias instancias de la máquina virtual simultáneamente, delegando en el sistema operativo subyacente el soporte de aislamiento de procesos, gestión de memoria e hilos.

    DVM | ART
    :---:|:---:|
    Usa el approach Just In Time, el cual deriva en menor consumo espacio de almacenamiento, pero mayor tiempo de carga | Utiliza el approach Ahead Of Time, el cual compila las apps cuando son instaladas, derivando en tiempos de carga más rápidos y menor uso del procesador.
    La cache se va construyendo con el tiempo, por lo tanto bootea más rápido | La cache se construye al primer booteo, por lo que reiniciar el dispositivo lleva más tiempo.
    Funciona mejor para dispositivos con menor almacenamiento, dado que se ocupa mucho menos espacio | Consume mucho más almacenamiento interno, ya que guarda las apps compiladas junto con los APKs.

6. ¿Qué es y de qué se encarga Zygote?

    Zygote es un proceso primario para todas las aplicaciones Android y contiene las bibliotecas del sistema y los frameworks utilizados por casi todas las aplicaciones. Este proceso funciona como una plantilla para cada nueva aplicación que es ejecutada.

### Almacenamiento

1. ¿Qué alternativas tiene una aplicación para almacenar sus datos? Detalle cada una.

    * Almacenamiento interno: Sirve para almacenar archivos privados de aplicaciones en el sistema del dispositivo.
    * Almacenamiento externo: Sirve para almacenar archivos en un sistema externo compartido. Se usa principalmente para archivos que comparte el usuario, como fotos.
    * _Shared preferences_: Sirve para almacenar datos privados de primitivas en pares clave-valor.
    * Base de datos: Sirve para almacenar datos estructurados en una base de datos privada.

2. ¿En dónde se almacenan las shared preferences de las aplicaciones? ¿De qué tipo pueden ser? ¿Una aplicación podría acceder a las shared preferences de otra diferente?

    Las shared preferences se almacenan en /data/data/\<package name\>/shared prefs/. Se pueden manejar de dos maneras: o bien mediante una collección de ellas, o bien a través de archivos xml. Es posible que una aplicación B acceda a las shared preferences de una app A si la app A define las shared preferences como MODE_WORLD_READABLE. De hecho, se podría usar un sharedUserId para tanto la app A como la app B.

3. ¿En dónde se almacenan las bases de datos de las aplicaciones?

    En /data/data/\<package name\>/databases/.

### File system

1. ¿Cuáles son los puntos de montaje principales del File System de Android? ¿Qué contiene cada uno?

   * /system: contiene todos los archivos del sistema operativo, a saber: la interfaz visual de Android, las apps del sistema y el bloatware. Representa el sistema operativo del dispositivo.
   * /system/bin: contiene los binarios nativos y demonios que son parte de AOSP. Todos los módulos construídos como BUILD_EXECUTABLE están acá. La única excepción es adbd, el cual tiene el LOCAL_MODULE_PATH seteado en /sbin, por lo que está instalado allí.
   * /data/app: //PREGUNTAR
   * /data/data/\<package identifier\>: representa la carpeta interna de \<package identifier\>. Son los datos que utiliza \<package identifier\> solo para la propia app.
   * /recovery: Todos los sistemas operativos tienen una partición de recuperación que sirve para “casos de emergencia”. La partición /recovery es un /boot alternativo, que contiene una consola con comandos de mantenimiento: borrar memoria, restaurar de fábrica, cargar archivos por ADB, etc.  El recovery (que es como se le llama a la consola de comandos) puede cambiarse por uno modificado para expandir sus funcionalidades.
   * /boot: La partición /boot es la que se encarga del arranque del dispositivo. En ella se encuentra el kernel y la ramdisk.
   * /cache: la partición /cache almacena los datos y archivos a los que tanto el usuario como el sistema operativo accede a menudo.

2. ¿Con qué tipos de memoria cuenta un dispositivo móvil generalmente? Detalle cada uno y luego relacionelos con los tipos de file system que cada tipo podría soportar.

    Hay dos tipos:
    RAW NAND Flash: tienen un susbsistema MTD, por lo que se recomienda que usen YAFFS. También soportan Ext4, pero no se recomienda porque no hay ni detección de bloques defectuosos ni ningún tipo de nivelación de desgaste.
    SD, MicroSD y eMMC: utilizan el driver mmcblk. El almacenamiento en MMC es particionado usando una tabla de particiones similar al la que se usaría para discos duros, usando fdisk u otra herramienta parecida.

3. Detalle las caracteristicas del YAFFS.

   * Booteo más rápido.
   * Consume menos memoria.
   * Divide los archivos en páginas.
   * GPL.
   * v1 y v2.
   * Single-theading.
   * Por lo general, hasta Android Froyo (v2.2).
   * Especializado para funcionar en memorias de tipo raw NAND.

### Licencia

1. ¿Bajo qué licencias esta el stack de Android? ¿Por qué?

    La idea de Google es mantener GPL fuera del espacio de usuario, por ende, utiliza apache v2 para el espacio de usuario pero GPL para el espacio de kernel.

2. ¿Por qué compañias como Samsung pueden cambiar la interface de sus propias versiones de Android?

    //PREGUNTAR

3. ¿Cómo se llama la libc de Android? ¿Por qué fue implementada?

    Por cuestiones de licencia, tamaño y optimización es que Google desarrolló su propia versión de libc: BIONIC.

### Rooteo

1. ¿Qué significa rootear un dispositivo Android?

    Significa darle permisos de superusuario al dispositivo.

2. ¿Qué es el bootloader? ¿De qué se encarga? ¿Qué tipos existen? ¿Qué consecuencias trae desbloquearlo?

    El bootloader es un programa sencillo que no tiene la totalidad de las funcionalidades de un sistema operativo, y que está diseñado exclusivamente para preparar todo lo que necesita para iniciar el sistema operativo. Inicializa el hardware y carga el initramfs. Permite bootear una imagen, ya sea Android o el recovery. Desbloquearlo implica perder la garantía del dispositivo.
    Hay dos tipos de bootloader: aquel que es abierto y el que está bloqueado por ID de la compañía (este último es el más ampliamente utilizado).

3. ¿Qué es el fastboot? ¿Qué acciones permite realizar?

    El fastboot es un protocolo USB que permite, entre otras cosas, bootear custom ROMs, flashear particiones y desbloquear el bootloader.

4. ¿Qué es la boot.img? ¿Con qué herramienta se la puede dividir y con cuál crear? ¿Qué contiene? ¿Qué nombre tiene la imagen del kernel de Linux? ¿Y la de Android? ¿En qué formato está empaquetado y comprimido el initramfs?

    El boot.img contiene el kernel y el ramdisk, archivos críticos necesarios que debe cargar el dispositivo previo a montar el filesystem. Para crearlo se utiliza una herramienta llamada mkbootimg, propuesta por AOSP; para dividirlo, se utiliza unmkbootimg. La estructura es la que sigue:
    * Header del boot.
    * Kernel.
    * Ramdisk.
    * Second stage.

    La el nombre de la imagen del kernel de linux es zimage (en versiones más actuales, solo Image). El nombre de la imagen de Android es system.img. El initramfs está empaquetado y comprimido en cpio.gz

## Ejercicios prácticos

### Requisitos

Para poder realizar los ejercicios prácticos deberá contar con un conjunto de herramientas. La practica esta armada para ser realizada usando Android Studio, para simplificar la instalación de las herramientas, ya que la idea no es gastar demasiado tiempo en la puesta a punto del entorno para resolver la practica. Una vez instalado, desde el IDE [Android Studio](https://developer.android.com/studio), podrá acceder casi todas las herramientas mencionadas en esta practica. Para ello, deberá primero crear un proyecto nuevo y cuando se abra la ventana del IDE tendra acceso a las herramientas mediante la pestaña Tools.

Si quisiera no instalar Android Studio, tendría que instalar todas las herramientas que se mencionan a continuación de manera separada.

* El Java SE Development Kit. Descarguelo desde la última versión desde [este](http://www.oracle.com/technetwork/java/javase/downloads) link. Elija la arquitectura correspondiente y el formato que desee:
  * Instálelo. Puede guiarse con [este](https://www.java.com/en/download/help/download_options.xml) sitio.
* El SDK de Android. Descargue la última versión desde [esta](https://developer.android.com/studio/#downloads) página:
  * Si descargo Android Studio, podrá instalar todas las herramientas necesarias de manera simple y automatizada. Para realizar esta práctica, solo tendrá que asociar los binarios correspondientes a la variable de entorno PATH.
  * De lo contrario descargue solo las herramientas de consola. Con lo cual, tendrá que desempaquetar y descomprimir los archivos correspondientes.
  * Acceda al Android SDK Manager. Para ejecutar el comando directamente agregue la herramienta “android” a la variable $PATH. Si instalo Android Studio, podrá instalar las dependencias desde una ventana gráfica.

    ```sdkmanager```

  * Descargue e instale/actualice los siguientes paquetes:
    * Android SDK Tools.
    * Android SDK Platform-tools.
    * Android SDK Build-tools.
    * Android 10.0(29) ! SDK Platform.
    * Android Emulator.
  * Para facilitar el desarrollo de los ejercicios, agregue las siguientes herramientas a la variable de entorno PATH:
    * ```<android-sdk-directory>/platform-tools/adb```
    * ```<android-sdk-directory>/platform-tools/fastboot```
    * ```<android-sdk-directory>/platform-tools/sqlite3```
    * ```<android-sdk-directory>/tools/android```
    * ```<android-sdk-directory>/tools/emulator``` (Podría encontrarse con el nombre _emulator-x86_)
    * ```<android-sdk-directory>/emulator/emulator```
* Si no instaló Android Studio, va a necesitar instalar Gradle de manera manual. Descárguela desde [este](https://gradle.org/install/) link. Tenga en cuenta [esta tabla](https://developer.android.com/studio/releases/gradle-plugin).

**NOTA DE LIHUEL**: si tenés Windows, seguí leyendo esta nota. Estoy prácticamente seguro de que en tu vida agregaste una variable de entorno en Windows, y aún más seguro que no tenés ni idea qué significa _\<android-sdk-directory\>_ ni dónde buscarlo. Hay mucho de lo que está adelante que no vas a poder hacer si no arreglamos eso primero. Si no me creés, seguí con el próximo punto: vas a ver que no te entra un solo comando.

* Primero hay que resolver qué es _\<android-sdk-directory\>_: vendría a ser el directorio donde están las herramientas del SDK. Si recién instalaste Android Studio y no tocaste nada, el path es **C:\Users\lihue\AppData\Local\Android\Sdk\\**. Fijate que cuando entres ahí, vas a encontrar platform-tools, tools y emulator. Ya con esto podemos pasar a la segunda parte: las variables de entorno.
* Si estás en Windows 10 como yo, basta con teclear _env_ que ya te sale _Edit the system environment variables_. Vas a darle click y luego al botón inferior derecho _Environment Variables..._. Van a salirte dos apartados: las variables de entorno tuyas (las del usuario) y las del sistema. Con que edites las tuyas va a ser suficiente. Fijate que hay una variable llamada _Path_. Clickeá para marcarla y apretá el botón Edit. Vos lo que querés hacer es agregar entradas, por lo que vas a apretar en nuevo. Y ahí vas a agregar el path de cada directorio, en concreto vas a agregar tres paths:
  * C:\Users\lihue\AppData\Local\Android\Sdk\platform-tools
  * C:\Users\lihue\AppData\Local\Android\Sdk\tools
  * C:\Users\lihue\AppData\Local\Android\Sdk\emulator

Después apretás en OK y aplicás los cambios. No hay que reiniciar la compu, ni cerrar y volver a abrir la sesión.

Por último abrí, una terminal con cmd y seguí con el próximo punto. Vas a ver que ahora sí entran los comandos (excepto el del _avdmanager_, ese es un .bat, si querés usarlo lo vas a encontrar en _C:\Users\lihue\AppData\Local\Android\Sdk\tools\bin_).

### Crear dispositivo AVD

* Desde el IDE Android Studio, cree un dispositivo virtual. Para esto, abra la ventana del IDE y haga click en el boton Tools -> AVD Manager. Puede encontrar una guia en [este](https://www.androidstudiotutorial.com/create-android-virtual-device/) link. Una vez creado, podrá iniciarlo desde este entorno (Recomendado).

    También puede crear y manejar el dispositivo mediante los siguientes comandos (si aparece, ingrese la letra n cuando se le realice la pregunta _Do you wish to create a custom hardware profile \[no\]_):

    ```avdmanager create avd --name emulador-so -k 'system-images;android-29;google_apis;x86```

    Nota: podrá ver la lista de targets ejecutando:

    ```android list target```

* Inicie el emulador (avd - android virtual device):

    ```emulator −avd emulador−so```

    **NOTA DE LIHUEL**: por alguna razón, el comando podría chillar que falta algo de x86. Para solucionarlo, ir a la carpeta _C:\Users\lihue\AppData\Local\Android\Sdk\emulator_ y ejecutarlo desde ahí.

### Acceder al dispositivo AVD

Una vez iniciado un dispositivo AVD, se puede acceder a este mediante una shell. Desde una consola ejecute:

* Acceda a la shell del dispositivo:

```adb shell```

Esto creara un cliente adb e iniciara el servidor adb, el cual se comunicará con el demonio adbd del dispositivo ejecutandose en background. Una vez dentro del dispositivo, podra ejecutar el comando _su_ para cambiar de modo usuario a modo root.

### SQLite

Dentro de la shell del dispositivo (habiendo accedido mediante la adb shell), puede ejecutar el comando _sqlite3_ para manejar bases de datos sqlite3.

Para ver la ayuda del entorno, ejecute:

    ```bash
    sqlite3
    sqlite > .help
    ```

    En mi caso, el _sqlite > .help_ no fue necesario, porque podía scrolear.

Mediante este comando, se puede acceder a las distintas bases de datos de las aplicaciones.

Para realizar la próxima parte, deberá crear un nuevo AVD que cumpla ciertos requerimientos.

* Acceda a la configuración del dispositivo (Requiere estar en un dispositivo con Android Lollipop 5.0/5.1, puede crear otro AVD que cumpla este requerimiento):

```sqlite3 /data/data/com.android.providers.settings/databases/settings.db```

* Liste todos los valores de la tabla system:

```sqlite > select * from system;```

Se puede apreciar el volumen del tono de llamada, el de las notificaciones, etc.

* Responda:
  1. ¿Qué pasa si abrimos la configuración del dispositivo desde la interface gráfica (Menu -> Settings -> Sound) y modificamos el volumen del tono de llamada o de notificaciones?
    Se agregan nuevas columnas a la tabla: volume_music_speaker, volume_alarm_speaker, volume_ring_speaker. Conforme se modifican los valores del volumen, estos valores van cambiando.
  2. ¿Y qué pasa si ejecutamos el comando _delete from system;_? ¿Sigue funcionando el sistema operativo? ¿Qué pasa con las configuraciones?
    Naturalmente, la tabla completa se borra. Sin embargo, el sistema operativo sigue funcionando y las configuraciones se van armando conforme se van estableciendo los valores.

### Storage

Utilice el mismo dispositivo AVD que para la etapa de Sqlite3 (Android 5.1 o menor).

* Acceda, mediante SQLite, a la información persistida por el browser. Analice la misma y borre los bookmarks.

    Lo que hice fue un ```sqlite3 /data/data/com.android.browser/databases/browser2.db```. Para ver las tablas, ejecutar ```.tables```. De ahí hice un ```delete from bookmarks;```.

* Identifique la página de inicio del browser (Tip: se encuentra almacenada a través de las shared preferences).

    //CONSULTAR

### Tipo de memoria y File system

* ¿Qué tipo de memoria tiene el dispositivo virtual creado? (Tip: utilice el comando mount o liste el directorio /dev/block/)

    Tiene memoria de tipo flash. Lo que permitió determinarlo es la presencia de filesystems como el ext4, por ejemplo.

* Agreguele almacenamiento SD al dispositivo AVD, si es que no lo creo con una SDCard.

    Abra el AVD Manager, y edite las preferencias del dispositivo para agregar la SDcard. Si desea hacerlo por comandos:

        ```bash
        #Use la herramienta mksdcard dentro del directorio
        #Tools para crear la tarjeta:
        mksdcard −l mySdCard 1024M mySdCardFile.img
        #Y agregarla al AVD:
        emulator −avd emulador−so −sdcard mySdCardFile.img
        ```

    ¿Aprecia algún otro tipo de memoria? ¿Por qué?
        Si, hay un tipo de memoria VFAT. //PREGUNTAR POR QUÉ.

* ¿Qué file systems existen en el dispositivo virtual creado? (Tip: utilice el comando mount).
    rootfs, tmpfs, devpts, proc, sysfs, debugfs, et4 y vfat.

### Apps

En Android, la forma de ejecutar aplicaciones es mediante la herramienta am. La misma provee la funcionalidad necesaria para ejecutar activities, services, entre otras cosas.

Para ver su ayuda, ejecute:

```am```

* Ejecute la actividad main de la aplicación settings:

    ```am start −a android.intent.action.MAIN −n com.android.settings/.Settings```

    ¿Cómo funciona?

  * El primer parámetro inica que se va a hacer. En este caso, se ejecuta una aplicación (start).
  * El segundo parámetro indica el tipo de acción que se ejecutará (android.intent.action.MAIN).

      Nota: para ver más tipos de acciones visite [esta](http://developer.android.com/guide/topics/intents/intents-filters.html) página.

  * El tercer parámetro es la actividad que se desea mostrar (com.android.settings/.Settings).
  * Inicie la aplicación del navegador del dispositivo, accediendo al sitio de la Facultad de Informatica:
  ```am start −a android.intent.action.VIEW −d http://info.unlp.edu.ar```

### Procesos y Usuarios

* Abra, desde la interface gráfica, el browser que viene instalado por defecto ¿Cómo identificamos al proceso? ¿Podemos matarlo? ¿Cómo?

    Desde la terminal, lo identificamos como com.android.browser. Tal proceso tiene un identificador (el pID). Usando el pID, es posible, mediante el comando kill, matar el proceso de la siguiente forma: ```kill process_pID```.

* Entendiendo el sandbox:

  1. En Android Studio, cree dos proyectos:

     * nombre/package: uno/unlp.so.android.uno
     * nombre/package: dos/unlp.so.android.dos

  2. Si existe, elimine el bloque de ámbito productivo (release), del archivo build.gradle dentro del directorio raíz de cada uno de los proyectos y/o módulos de los mismos:

        ```gradle
        buildTypes {
            release {
                runProguard false
                proguardFile getDefaultProguardFile('proguard−android.txt')
            }
        }
        ```

  3. Agregue al archivo AndroidManifest.xml, permisos de Internet para la aplicación uno:

        ```xml
        <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="unlp.so.android.uno">
        ...
        <uses-permission android:name="android.permission.INTERNET"/>
        ...
        </manifest>
        ```

  4. Construya las aplicaciones mediante el IDE (Build ->Build Bundle(s) / APK(s) - >Build APK(s)).

        Tambien puede hacerlo mediante la herramienta Gradle, posicionándose en el directorio raíz de cada proyecto (requiere conexión a Internet):

        ```gradle assembleDebug```

        Podrá ver los .apk generados en (\<directorio-raíz-proyecto\>/app/build/outputs/apk/debug/).

        Además podrá saber que tareas ya vienen configuradas a través de Gradle ejecutando lo siguiente:

        ```gradle tasks```

  5. Instale las aplicaciones en el dispositivo (requiere que el dispositivo/emulador este conectado/iniciado):

        ```bash
        adb install <debug−apk−generado−proyecto−uno>
        adb install <debug−apk−generado−proyecto−dos>
        ```

  6. Ejecute cada una de las aplicaciones.
  7. Acceda al dispositivo mediante el adb y responda:
     * ¿Qué se información puede extraer como resultado de ejecutar los siguientes comandos?
        * adb shell
        * ps (o ps −A)
        * dumpsys package unlp.so.android.uno
        * dumpsys package unlp.so.android.dos

        Que cada app tiene un userID diferente??//PREGUNTAR

  8. Como posiblemente ya sepa, el userId del usuario root en Linux es el 0. En la explicación se dijo que cada aplicación Android es un usuario Linux, es decir que tiene un userId único. Debera acceder a un archivo en el cual, entre otras cosas se define la configuración de usuarios y grupos para el sistema operativo Android. Acceda a la siguiente [página](https://android.googlesource.com/platform/system/core.git/+/master/libcutils/include/private/android_filesystem_config.h) y responda:
     1. ¿Cuál es el userId del usuario system?

        El uID 1000.

     2. ¿A partir de qué userId Android concede identificación para las aplicaciones de usuario?

        Va del uID 10000 al 19999.

     3. ¿Qué userId se le asigna al demonio adb (adbd)?

        El uID 1011.

     Nota: El que desee hacerlo puede leer [esté](http://users.encs.concordia.ca/~clark/papers/2012_spsm.pdf) artículo en donde se explica intrínsecamente la asignación del userId al momento de instalar una aplicación en Android.
