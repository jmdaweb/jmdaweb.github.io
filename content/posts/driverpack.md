---
title: "Cómo actualizar nuestros drivers con Driver Pack Solution evitando un montón de problemas"
date: 2024-06-03T09:43:45+02:00
reply:
uri: /posts/driverpack/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

A pesar de ser el sistema de escritorio más usado, Windows siempre ha tenido un gran problema con la gestión de sus actualizaciones. Hoy en día, mediante Windows Update recibimos actualizaciones del sistema, como siempre. Gracias a Winget y la Microsoft Store, podemos mantener nuestras aplicaciones actualizadas de un modo muy similar. Pero ¿qué pasa con los drivers?
Los drivers, o controladores, son una parte esencial de nuestro sistema que no debemos descuidar. Son los encargados de gestionar la relación entre el sistema y todos los componentes físicos del equipo y dispositivos periféricos. A veces, por Windows Update nos puede llegar alguna actualización de controladores. Otras, el fabricante de un componente incluye su propio software para actualizar su controlador. Pero en la mayoría de casos, tan pronto como se instala el controlador para un dispositivo y funciona, queda olvidado y ya no volvemos a saber de sus actualizaciones. Los fabricantes del equipo, interesados en que quede obsoleto tan pronto como sea posible para que te compres otro, ofrecen actualizaciones a cuentagotas y durante muy poco tiempo. Sin embargo, está demostrado que unos controladores actualizados solucionan problemas, alargan la vida del equipo, e incorporan mejoras que nunca imaginaríamos que vienen de ahí.
Existen muchas herramientas que permiten descargar e instalar los controladores más recientes para nuestros dispositivos. En este tutorial hablaremos de Driver Pack Solution, una de ellas. Lo que la diferencia de otras es algo que a mí me gusta mucho: puedes descargar todos los packs de controladores y no depender de Internet para configurar casi cualquier ordenador.
## Descarga
Para descargar la versión más reciente de Driver Pack Solution, acudiremos a [esta página](https://driverpack.io/es/foradmin). Si no es la primera vez que lo descargamos, puede ser útil comprobar el número de versión. En el momento de escribir este tutorial, la versión actual es la 17.10.14-24060. A continuación, asumiendo que queremos todos los packs, podemos descargar [este torrent](https://dl.driverpack.io/DriverPack-Offline.torrent) con nuestro cliente torrent favorito. La descarga ocupa unos 42 GB. Se recomienda añadir como excepción al antivirus la carpeta donde descarguemos Driver Pack Solution, ya que puede decidir borrar algunos de los ejecutables incluidos.

Se puede prescindir del torrent descargando el ejecutable en línea y los packs que se muestran más abajo, pero no se recomienda. Entre otras cosas, porque la velocidad de descarga directa es tan baja que nos devuelve a aquellos felices años donde había módems de 56K.
## Ejecución de DriverPack
Teniendo a mano un buen descompresor, como WinRar o 7-Zip, y el torrent descargado por completo, es momento de descomprimir ficheros. Concretamente, empezaremos por el único comprimido que hay en la raíz: DriverPack_17.10.14-24060.7z. Es muy importante elegir la opción "Extraer aquí" o similares, ya que los archivos extraídos servirán para completar la estructura de carpetas existente.

A continuación, pulsamos intro sobre el archivo DriverPack.exe. Nos pedirá privilegios de administrador, y comenzará a realizar una serie de comprobaciones del sistema que pueden tardar varios minutos. Si detecta una conexión activa a Internet y nos invita a usarla, pulsaremos Cancelar para quedarnos sólo con el contenido fuera de línea.

Después del arranque, la interfaz de DriverPack está lista para su uso. Con NVDA, se puede manejar como si de una página web se tratara, empleando los modos foco y exploración cuando sea necesario.
## Actualización de controladores
He aquí la parte complicada. Driver Pack Solution viene muy bien como herramienta de diagnóstico, pero si la dejamos hacer decidirá por nosotros, y esas decisiones puede que no nos gusten. Concretamente:

* Puede instalar software publicitario y otros programas que a lo mejor no son deseados.
* Puede forzar la instalación de controladores que hagan que el equipo se vuelva inestable.

Así que parece mejor idea hacer las actualizaciones a mano. Para ello, activamos un enlace llamado "Modo experto". Se mostrará una tabla con controladores que se pueden actualizar, y posiblemente otra con controladores que se pueden instalar. Debemos quedarnos con el contenido de esas tablas. Una vez copiado, podemos cerrar Driver Pack Solution.

A continuación, extraeremos las partes que nos interesen de cada pack a una carpeta común, por ejemplo C:\drivers. Cada pack tiene una estructura similar en su interior: nombre del fabricante y sistemas para los que se ha hecho el controlador.

Imaginemos que queremos actualizar una tarjeta de red Realtek en un Windows 10 u 11 de 64 bits. Abrimos el archivo DP_LAN_Realtek-NT_24060.7z, y extraemos el contenido de realtek\matchver\FORCED\10x64 a la carpeta común. Por si acaso, podemos incluir ntx64 y 88110x64. Debemos hacer esto con todos los packs. Si surgen dudas sobre qué extraer, siempre es mejor pasarse que quedarse corto, aunque sin extraer los packs enteros, que ocupan muchísimo descomprimidos. Por ejemplo, para actualizar una placa base Intel, es más sencillo extraer toda la carpeta intel del pack de chipset.

Concluida la extracción, le llega el turno al administrador de dispositivos de Windows. Podemos abrirlo pulsando windows+r y escribiendo devmgmt.msc, o desde el menú que aparece al pulsar windows+x. Una vez abierto, comienza un proceso algo repetitivo:

1. Abrimos el menú contextual del controlador que necesita actualizaciones, y elegimos "Actualizar controlador".
2. Pulsamos el botón "Examinar mi PC en busca de controladores".
3. Elegimos la carpeta donde están extraídos todos los drivers. Por ejemplo, C:\drivers. Nos aseguramos de que la casilla "Incluir subcarpetas" esté marcada. Si no es la primera vez que hacemos esto, todos los valores ya vendrán configurados y sólo tendremos que pulsar en Siguiente.
4. Esperamos a que el controlador se actualice. Si es necesario reiniciar el equipo, lo reiniciamos antes de continuar con el siguiente controlador.

## Algunos consejos útiles

* Windows sabe lo que es mejor para él. Si el administrador de dispositivos se niega a actualizar un controlador, y hemos comprobado que está extraído en la carpeta, no hay que forzarlo. De lo contrario, pueden producirse problemas. Es normal que Driver Pack sugiera controladores que no debe, y que la tabla de actualizaciones nunca se quede vacía.
* En otras ocasiones, Windows puede indicar que no hay nada que actualizar, simplemente porque no hemos extraído el controlador adecuado del pack correcto. Las tarjetas de sonido Realtek son un buen ejemplo de controladores repartidos entre varios packs, más aún si incluimos los componentes software de sus efectos de sonido.
* Hay controladores cuyo nombre cuesta identificar al buscar su correspondencia en la tabla. Generalmente, si llevan la palabra 'estándar' o 'genérico' en el nombre, pueden necesitar una actualización. Por ejemplo, un "Controlador Sata AHCI estándar" puede sustituirse por un "Intel Sata Ahci Controller" del paquete Mass Storage.
* A veces los fabricantes pueden tener razón y Windows no ser tan infalible. Si algún dispositivo falla tras actualizar, revierte al controlador anterior.
