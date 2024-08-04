---
title: "Cómo visualizar y crear archivos ZIM, una buena alternativa para almacenar sitios web fuera de línea"
date: 2024-08-04T13:40:45+02:00
reply:
uri: /posts/archivos-zim/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: true
---

Si buscas en cualquier buscador información para descargar sitios web completos, encontrarás montones de tutoriales que recomiendan toda clase de programas. Este es sólo uno más, que he querido escribir tras encontrarme por casualidad con el formato ZIM, el tipo de archivo que usa la Wikipedia para volcar todo su contenido y permitir su uso sin conexión. Pero espera, ¿quién querría descargar un sitio web entero y para qué? Tal vez, más personas de las que crees. Visitar un sitio web previamente descargado puede tener ventajas, como las siguientes:

* La más obvia: se puede navegar por el sitio cuando no hay conexión. Por ejemplo, si hay caídas de la red o estamos en una zona con escasa cobertura.
* La velocidad de carga no es un problema, incluso si la conexión es lenta.
* Se llevan al máximo conceptos tales como la privacidad y el anonimato al navegar... al menos, para el propietario del sitio web en cuestión, que nunca sabrá que lo estamos visitando.
* Si el sitio web se actualiza y pierde contenido, se conserva una copia con toda la información.
* Se puede visitar el sitio web incluso cuando su propietario ha decidido eliminarlo.

En esta entrada haremos algo muy similar a lo que hace [el gran archivo de Internet](https://archive.org) con su [Wayback Machine](https://web.archive.org), pero a mucha menor escala y en un formato de fichero distinto, por suerte para nuestros discos duros. Y como es habitual, lo haremos acompañados de un lector de pantalla, preferiblemente NVDA. ¡Empezamos!

## Requisitos previos

Para seguir y comprender adecuadamente las distintas secciones de esta entrada, se recomienda lo siguiente:

* Experiencia manejando la consola, ya sea cmd, bash o PowerShell. Muchas de las instrucciones dadas pueden aplicarse en casi cualquier plataforma.
* La utilidad de línea de comandos wget (sólo si quieres crear tus propios archivos ZIM). Puede emplearse desde Windows con MSYS2 o WSL, o en Linux. Se encuentra como paquete en la mayoría de distribuciones, y también podría estar disponible para Windows de forma independiente.
* Si utilizas lector de pantalla, se recomienda destreza con la navegación por objetos, uso del cursor de revisión, o mecanismos equivalentes.

## Introducción al formato ZIM

El [proyecto OpenZIM](https://wiki.openzim.org/wiki/OpenZIM) define el formato ZIM como "un formato de archivo perfectamente adecuado para guardar la Wikipedia en un pen drive USB". Pero parece que no sólo se aplica a la Wikipedia, sino a cualquier contenido en la web que pueda almacenarse sin conexión.
Un archivo ZIM contiene en su interior ficheros HTML codificados en UTF-8, enlazados adecuadamente entre sí y con todos los recursos asociados: hojas de estilo, imágenes, contenido multimedia, ficheros descargables y scripts. Una web alojada en uno de estos archivos debe ser autocontenida, eliminando por completo o reduciendo al máximo la interactividad, los comportamientos dinámicos y la dependencia del exterior. Los archivos ZIM contienen metadatos sobre el nombre de la web, su creador, su idioma y una descripción del contenido, entre otros, y facilitan al software que los interpreta la posibilidad de realizar búsquedas de texto completo.

## Cómo leer un archivo ZIM existente

Aunque es un formato poco común, la [biblioteca de Kiwix](https://library.kiwix.org/) contiene montones de archivos ZIM que se pueden visualizar y descargar. La Wikipedia, por su parte, vuelca periódicamente todos sus contenidos en [esta dirección](https://dumps.wikimedia.org/other/kiwix/zim/wikipedia/). Y efectivamente, en un pen drive de 128 GB cabrían las versiones en inglés y español juntas con todos sus recursos. Pero en este tutorial trabajaremos con algo más manejable: un [pequeño archivo ZIM](/static/nvda-addons.zim) preparado para la ocasión. En la última sección aprenderemos a crearlo, pero ahora veamos cómo acceder a su contenido.
Existen varios programas que pueden abrir archivos ZIM. Se pueden descargar en el [catálogo de aplicaciones de Kiwix](https://kiwix.org/en/applications/). En general son fáciles de usar, y cada uno tiene sus ventajas y desventajas.

### Kiwix desktop

Kiwix Desktop es un cliente de escritorio compatible con Windows y Linux. Permite cargar archivos ZIM individuales y gestionar una biblioteca completa. En Windows, su proceso de instalación es tan sencillo como [descargar la versión más reciente](https://download.kiwix.org/release/kiwix-desktop/kiwix-desktop_windows_x64.zip), descomprimirla en la carpeta donde queramos alojar el programa, y abrir el archivo ejecutable. En las distribuciones de Linux más populares, se puede instalar con pacman, dnf o apt. Y si eso no nos convence, también hay una versión para instalar con flatpack.
La interfaz de Kiwix Desktop está basada en el framework QT5. Al manejarla con teclado, el tabulador no llega a todos los elementos disponibles, pero tiene algunos atajos que podemos usar. Por ejemplo, control+o para abrir un archivo. La mayoría de los controles están etiquetados, y con navegador de objetos se pueden alcanzar las zonas a las que el foco no llega por sí solo.
A pesar de que se diseñó para ser intuitivo y amigable, la lectura de contenido con Kiwix Desktop dista mucho de ser cómoda con lectores de pantalla. Los documentos no se muestran en un búfer virtual que se pueda recorrer en modo exploración, y sólo se escucha parte del texto al tabular y pasar por encima de un enlace. Además, Kiwix Desktop no soporta scripts en los archivos ZIM. De todos los lectores, es sin duda el más restrictivo.

### Kiwix JS y extensiones de navegador

Esta alternativa puede ser la más equilibrada en términos de accesibilidad y facilidad de uso. Kiwix JS es una aplicación que se entrega de múltiples formas. Elige la que más te guste, el resultado en todos los casos será muy similar:

* [Aplicación web progresiva en el navegador](https://pwa.kiwix.org/)
* [Aplicación Electron en la tienda Microsoft](https://www.microsoft.com/store/apps/9P8SLZ4J979J)
* Extensiones para [Chrome](https://chrome.google.com/webstore/detail/kiwix/donaljnlmapmngakoipdmehbfcioahhk), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/kiwix-offline/) y [Edge](https://microsoftedge.microsoft.com/addons/detail/kiwix/jlepddlenlljlnnhjinfaciabanbnjbp).

En los casos donde el navegador esté involucrado, puede que ciertas características de seguridad limiten partes del contenido, lo que no impedirá leer la mayoría de los archivos. Kiwix JS se puede usar sin problema en modo exploración. Algunos de sus controles, dependiendo de la versión que elijas, pueden estar no etiquetados. Sin embargo, el botón para seleccionar un archivo ZIM está claramente identificado. La primera vez que carguemos un archivo, Kiwix indicará que no procede de un lugar seguro, y permitirá elegir si nos fiamos del contenido o si, por el contrario, compensa más abrirlo en modo restringido.
Una vez se abra el archivo, el contenido aparecerá en un marco. La accesibilidad a la hora de leer y navegar, llegados a este punto, ya sólo dependerá del contenido.

### Kiwix-tools

Esta es la alternativa más accesible, pero también la más avanzada, ya que implica usar la consola. La principal ventaja es que, una vez en marcha, podemos compartir la URL de acceso con otros usuarios de la red local, o de Internet en general si se configura adecuadamente. En este tutorial no se explica cómo asegurar las conexiones del exterior ni cómo crear un proxy inverso, aunque es el procedimiento recomendado. Nunca expongas el servidor Kiwix directamente en Internet.
Las herramientas de consola de Kiwix (kiwix-tools) permiten realizar búsquedas, gestionar bibliotecas con varios archivos ZIM y levantar servidores accesibles desde otro equipo. En algunas distribuciones de Linux, como Debian, se encuentran en los repositorios oficiales (paquete kiwix-tools). Sin embargo, la versión ofrecida no suele ser la más reciente. Por lo tanto, a continuación se proporcionan los enlaces de descarga directa de la versión actual a fecha de redacción de esta entrada, la 3.7.0-2:

* [Windows (sólo 32 bits)](https://download.kiwix.org/release/kiwix-tools/kiwix-tools_win-i686-3.7.0-2.zip)
* [Linux ARM64](https://download.kiwix.org/release/kiwix-tools/kiwix-tools_linux-aarch64-3.7.0-2.tar.gz)
* [Linux i686](https://download.kiwix.org/release/kiwix-tools/kiwix-tools_linux-i586-3.7.0-2.tar.gz)
* [Linux x86_64](https://download.kiwix.org/release/kiwix-tools/kiwix-tools_linux-x86_64-3.7.0-2.tar.gz)

De nuevo, no hace falta instalación. Tan sólo basta con descomprimir las herramientas en la carpeta deseada y ejecutar desde consola las que queramos. Por ejemplo, podemos usar kiwix-serve para servir por red el archivo ZIM descargado anteriormente con un comando como este:

`kiwix-serve nvda-addons.zim`

El contenido estará disponible en [localhost por el puerto 80](http://localhost), o en la dirección ip del servidor, si es un equipo remoto. El servidor kiwix-serve dispone de algunos parámetros extra que se pueden pasar al ejecutarlo:

* `--library archivo.xml`: sirve para cargar bibliotecas con varios archivos ZIM. Por ejemplo: `kiwix-serve --library biblioteca.xml`
* `-i dirección_ip`: limita las direcciones ip en las que escucha el servidor. Por ejemplo: `kiwix-serve -i 127.0.0.1 archivo.zim`
* `-M`: vigila los cambios de una biblioteca y reacciona actualizándose si la modificamos. Útil para añadir o eliminar archivos ZIM sin reiniciar el servidor. Por ejemplo: `kiwix-serve -M --library biblioteca.xml`
* `-p puerto`: si el puerto 80 está en uso por otra aplicación, este argumento permite elegir uno distinto. Por ejemplo: `kiwix-serve -p 8080 archivo.zim`
* `-r prefijo`: cambia el prefijo de URL. Útil si ejecutamos kiwix-serve tras un proxy inverso en un servidor con alojamiento compartido. Ejemplo: `kiwix-serve -r /biblioteca/ archivo.zim`. El contenido estaría disponible en http://localhost/biblioteca.

Por defecto, el servidor retendrá el control de la consola hasta que lo cerremos. Para ello, se puede pulsar control+c. En Linux, se puede cargar como servicio y dejarlo permanentemente en funcionamiento, algo que se sale del ámbito de este tutorial.

#### Gestión de bibliotecas con kiwix-manage

Imaginemos que hemos descargado la Wikipedia en español, la Wikipedia en inglés, el archivo ZIM de ejemplo de este tutorial y algunos más que nos resulten de interés. En vez de ejecutar un número indefinido de instancias de kiwix-serve para mostrarlos, podemos añadirlos a una biblioteca. Para ello, usaremos kiwix-manage:

`kiwix-manage biblioteca.xml add archivo1.zim archivo2.zim archivo3.zim`

Para agregar el archivo con la web de complementos de NVDA: `kiwix-manage biblioteca.xml add nvda-addons.zim`
Ahora, podemos ejecutar el servidor con la biblioteca recién creada: `kiwix-serve -M --library biblioteca.xml`
Para visualizar todos los archivos de la biblioteca, se puede usar un comando como este: `kiwix-manage biblioteca.xml show`
Cada archivo, entre sus metadatos, tiene un identificador (id) formado por números y letras. Dicho identificador se puede usar para eliminar uno de los archivos: `kiwix-manage biblioteca.xml remove identificador`

### Lectores para Android y dispositivos Apple

Si tienes iPhone, iPad o Mac, puedes [descargar Kiwix en la AppStore](https://apps.apple.com/us/app/kiwix/id997079563). En Android, se puede [descargar Kiwix en la Play Store](https://play.google.com/store/apps/details?id=org.kiwix.kiwixmobile&pli=1), pero está limitada y no permite abrir archivos ajenos a la biblioteca de Kiwix. Si necesitas esto último, [descarga el archivo APK directamente](https://download.kiwix.org/release/kiwix-android/kiwix.apk).

