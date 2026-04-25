---
title: "Perdiendo el miedo a la red oscura: una breve introducción"
date: 2026-03-14T13:41:00+01:00
reply:
uri: /posts/red-oscura/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

El otro día, tras montar y probar un par de contenedores con herramientas actualizadas, me sentí preparado para conectar la instancia de la comunidad de NVDA de Mastodon a la red oscura. Me intriga conocer ese lado del fediverso, saber quién monta instancias allí y por qué, ver qué se publica, y dar a mis usuarios la oportunidad de hacer lo mismo. Pero como estos temas traen controversia, decidí preguntar primero, y dar marcha atrás en cuanto alguien dijera que no. Y alguien dijo que no, por lo que la misión quedó interrumpida. Pero me pidieron que escribiera sobre el tema, y aquí estamos.

En esta entrada quiero hablar de Tor e I2P, los dos proyectos que más destacan relacionados con navegar de forma anónima por Internet y evitar la censura impuesta en algunos países. Porque esos son sus propósitos iniciales, aunque luego se usen para otra cosa. Como toda herramienta, se pueden usar bien o usar mal. En este caso, la web oscura ha dado lugar a toda clase de leyendas (y hasta películas de terror) por su mal uso por parte de delincuentes que la emplean para traficar con armas, pornografía, violencia extrema, coordinar asesinatos y a saber qué más. Y no por ello hay que demonizarla, al igual que se hacía con el p2p a principios de siglo, donde estaba extendido el rumor de que en el momento que usaras p2p, la policía se presentaba en tu casa. Por cierto, ¿sabías que al llamar por WhatsApp a un contacto usas P2P? Al final, el término alude a una conexión directa entre dos equipos, simplemente. En fin, no me voy a extender más con ese tema, vamos a lo interesante.

### TOR

TOR son las siglas de The Onion Router, el enrutador de la cebolla. Según Wikipedia, su lanzamiento inicial se produjo en septiembre de 2002, así que ya lleva casi un cuarto de siglo entre nosotros. Su objetivo es construir una red sobre Internet donde se preserve el anonimato de las comunicaciones, y el destinatario de las mismas (por ejemplo, un servidor) no conozca su origen (dirección IP del cliente). Esto se consigue montando un circuito formado por varios nodos que cifran y retransmiten las comunicaciones entre origen y destino. Por defecto, un circuito está formado por tres nodos, y añadir más no es garantía de mayor anonimato, pero sí de mayor lentitud. La arquitectura de TOR se podría dividir en los siguientes componentes:

* Cliente: se encarga de cifrar las comunicaciones en origen y establecer un circuito de varios nodos. Existe el mito de que un cliente puede redirigir tráfico de la red TOR de otros usuarios. Antes era así, pero ahora es mentira, sólo dirigirá tu propio tráfico hacia donde tú le indiques. El cliente también puede ofrecer servicios ocultos.
* Repetidor: los repetidores son nodos que distribuyen el tráfico por la red TOR. Son mantenidos por voluntarios u organizaciones.
* Nodo de salida: es el nodo que actúa como salida de un circuito. Su propósito es descifrar las comunicaciones, enviarlas a destino, cifrar la respuesta y enviarla de vuelta por el circuito hasta el origen.
* Directorio: el directorio contiene un listado de nodos que actúan como repetidores, nodos de salida, políticas de salida de estos nodos y datos sobre servicios ocultos (explicados más adelante). Existen varios directorios, ya que si hubiera uno solo y fallara, toda la red se caería.

Un mismo nodo puede ejercer como repetidor, nodo de salida y cliente, y son los que garantizan más anonimato. Sin embargo, requieren diversos trámites para ser incluidos en los directorios y exponen a sus propietarios ante las autoridades, por lo que lo más sencillo es ejecutar simplemente un cliente, que además no requiere que se abran puertos en el router ni ningún otro tipo de configuración.

#### Clientes disponibles

El programa TOR es un cliente minúsculo que se maneja desde consola y se suele ejecutar como servicio. Si habías oído que TOR es un navegador, te has topado con otro mito. Es habitual confundir TOR con TOR Browser, el navegador recomendado por el proyecto que también lleva TOR incorporado. Veamos cómo instalar y configurar cada uno de ellos en Windows.

##### TOR Browser

TOR Browser es un navegador web basado en Mozilla Firefox ESR. Es tan accesible con lectores de pantalla como Firefox y, a excepción de algunas opciones de menú y botones propios, tiene la misma interfaz. Tor Browser cuenta, además, con un perfil configurado a medida, extensiones preinstaladas y otras modificaciones cuyo objetivo es lograr un mayor grado posible de anonimato y protección de la privacidad equilibrado con una experiencia de navegación adecuada. Esto significa que podemos configurarlo para que nos proteja aún más de lo que ya lo hace. Por ejemplo, aumentando al grado máximo las restricciones de la extensión Noscript.

Para descargarlo, podemos acudir a la [página de descarga de Tor Browser](https://www.torproject.org/download/languages/), buscar la tabla y elegir el instalador de la plataforma que corresponda. En este caso, para Windows de 64 bits, dejo el [enlace a la versión 15.0.10](https://dist.torproject.org/torbrowser/15.0.10/tor-browser-windows-x86_64-portable-15.0.10.exe), la actual mientras escribo esta guía. No uses este enlace si hay una versión posterior. Como veremos más adelante, mantenerse actualizado en este terreno no es opcional, sino una precaución más. Si quieres actualizar el navegador, conéctalo a la red TOR, ve al menú Ayuda y elige la opción Acerca de, tal y como harías con Firefox.

El instalador es muy simple. De hecho, lo único que hace es extraer un portable, y crear accesos directos en el escritorio y el menú Inicio. Así que, llegado el momento de desinstalar, será suficiente con borrar la carpeta del portable y los accesos directos. La ubicación de instalación que ofrece de manera predeterminada es el escritorio, y se puede desmarcar la creación de accesos directos para tener todo más organizado. Una vez instalado, para abrirlo, entraremos en la carpeta Tor Browser y ejecutaremos el acceso directo Tor Browser.

###### Primera experiencia de navegación

Al arrancar Tor Browser por primera vez, detectará el idioma del sistema y adaptará su interfaz. Esto significa que desde el principio lo tendremos en español si nuestro sistema está en español. Lo primero que se muestra es una pantalla de conexión, con un botón para conectar, otro de configuración de conexión que casi nunca suele ser necesario, y un interruptor para que se conecte automáticamente al abrir el navegador. Pulsamos el botón Conectar y esperamos a que se complete la conexión. Si quieres desconectarte, cierra el navegador, y TOR se cerrará automáticamente con él.

Una vez el navegador se ha conectado a la red TOR, podemos abrir cualquier página web que visitemos con frecuencia. Como se establece un circuito de nodos aleatorio, el propietario de la web verá una conexión que se produce incluso desde otros países, y sólo tendrá aquella información que tú quieras proporcionarle. Además, si la conexión es https, añade una capa de cifrado extra para impedir que el propietario de un nodo de salida intercepte y vea claramente tus comunicaciones. Como ejemplo, visita la [página principal de la audiocinemateca](https://audiocinemateca.com). Observa que tarda un poco más en cargar, pero por lo demás funciona como siempre.

La primera vez que se visite una página, el navegador preguntará si deseamos solicitar las versiones en inglés de las páginas para mejorar nuestro anonimato. Si has elegido la audiocinemateca como te he indicado, observarás algo más: tiene un servicio cebolla disponible. Esto se debe a que mi servidor le indica a Tor Browser, mediante la cabecera Onion-Location, que ofrezco ese servicio. Dile que "Ahora no" quieres visitarlo, lo siguiente que haremos será aprender a buscarlos a mano.

En la red TOR, las personas y entidades que proporcionan servicios también tienen derecho a ser anónimas. Por ello, un cliente puede ofrecer servicios ocultos o servicios cebolla, y sin abrir puertos ni nada que se le parezca. Estos servicios son más seguros que un servidor tradicional, ya que el usuario no abandona la red TOR por ningún nodo de salida para visitarlos. Simplemente, al igual que el usuario establece un circuito de varios nodos, el servicio hace lo propio hasta un punto de encuentro común, y establecen una conexión cifrada.

En lo que se refiere a web, los servicios ocultos suelen funcionar por http, dejando el cifrado en manos de TOR. Las conexiones seguras por https son poco frecuentes, ya que las pocas entidades que ofrecen certificados son de pago, y en cierto modo la transacción rompe el anonimato de quien ofrece el servicio. Sin embargo, si el propósito de la entidad es únicamente ofrecer una vía más segura y no desea ser tan anónima, https se puede usar y cumple su función de maravilla.

Cuando una web dispone de un servicio oculto y avisa de ello,Tor Browser mostrará un botón cerca de la barra de direcciones. Estando en la audiocinemateca, pulsa alt+d para ir a la barra de direcciones. Después, pulsa tabulador hasta el botón "Añadir esta página a marcadores", y luego flecha derecha. Llegarás a un botón llamado ".onion disponible". Si lo pulsas, se recargará la página. Podrás usar la audiocinemateca como siempre, pero en la barra de direcciones verás esto: `https://3vynrelomm3k43lqhetkztg2o25eif4hrppodo225gqhvglxv3bc7jqd.onion/`. Poco intuitivo, ¿verdad? Es el valor hash de una clave pública. En la red TOR, por desgracia, no se pueden comprar ni usar nombres de dominio. Si acaso, algún servicio más caro de lo que querríamos pagar ofrece la posibilidad de generar certificados hasta que la clave pública comience por las letras o palabras deseadas.

Existen montones de servicios ocultos sin un equivalente en la web ordinaria. En esta entrada no hablaremos de ellos, pero sí aprenderás a montar el tuyo propio. Para todo lo demás, busca por ahí.

##### TOR, sin navegador

Como dijimos, TOR es un pequeño programa independiente que se puede instalar y usar como cualquier otro programa. A mí me gusta compilarlo desde el código fuente, pero es una tarea algo compleja cuya explicación queda fuera del propósito de esta entrada. Cuando se ejecuta por sí solo, TOR ofrece un proxy de tipo SOCKS5 en el puerto 9050. Se puede configurar cualquier aplicación que use TCP y tenga soporte de servidores proxy para que se conecte a través de la red TOR. La configuración de puertos, maneras de usar la red y otros ajustes es demasiado extensa para explicarla, pero los valores por defecto son razonablemente seguros como punto de partida.

Para descargar la versión más reciente, podemos acudir a la [página de descarga de TOR](https://www.torproject.org/download/tor/) y elegir la expert bundle más adecuada para nuestra plataforma. La versión actual es la 0.4.9.6 y, de nuevo, advertimos que debe mantenerse actualizada por seguridad. Si no tienes tiempo de actualizar, es mejor que TOR permanezca apagado. La plataforma también importa, no sólo por rendimiento, sino por capacidad criptográfica. Dicho de otro modo: no elijas TOR para Windows de 32 bits si tu Windows es de 64.

Una vez descargado, vamos a descomprimirlo en una ubicación conocida. Por ejemplo `C:\tor`. Al hacerlo, deberíamos ver 3 subcarpetas: data, tor y docs. A continuación, crearemos un fichero de configuración. Con tu editor favorito, crea el archivo `C:\tor\torrc` y añade el siguiente contenido:

```
GeoIPFile C:\tor\data\geoip
GeoIPv6File C:\tor\data\geoip6
AvoidDiskWrites 1
ControlPort 9051
CookieAuthentication 1
DataDirectory C:\tor\data
TruncateLogFile 1
log notice file C:\tor\data\tor.log
```

Con estos ajustes, le indicamos a TOR algunas rutas básicas, le pedimos que no escriba en disco si puede evitarlo, y que borre el archivo de registro anterior cuando se reinicie. También le indicamos el puerto donde podrá recibir instrucciones de control (si usamos software compatible con ello) y la forma de autentificarse al conectar por dicho puerto. Ahora, instalaremos el servicio. Para ello, sigue estos pasos:

1. Abre una consola como administrador. Pulsa `Windows+r`, escribe `cmd` y pulsa `control+shift+intro`.
2. Navega a la carpeta de TOR: `cd C:\tor`.
3. Instala el servicio, indicando dónde está la configuración: `tor\tor --service install --options -f C:\tor\torrc`.
4. Si todo ha ido bien, el servicio se instalará y se pondrá en marcha. Utiliza la consola de `services.msc` para gestionar su modo de arranque, iniciarlo y detenerlo, como harías con cualquier servicio.

Ahora que has instalado TOR, puedes configurar el proxy de tu navegador usando el tipo SOCKS5, host 127.0.0.1 y puerto 9050. Puedes, pero te recomendamos que no lo hagas. Recuerda protegerte bien y usar un navegador especializado, o una app que tenga cualquier otro propósito, o un entorno suficientemente controlado por tu parte.

Para desinstalar TOR, ejecuta este comando como administrador: `C:\tor\tor\tor --service remove`. Si lo deseas, borra a continuación su carpeta.

###### Nuestro primer servicio oculto: un servidor de NVDA Remote

Cada cliente TOR puede ofrecer a la red tantos servicios ocultos como quiera. Los más conocidos son sitios web, pero también se pueden levantar servidores de correo electrónico, chat, o cualquier cosa que admita conexiones TCP. Al contrario que sucede con los programas cliente, los servidores no tienen que estar preparados para ningún tipo de proxy. A todos los efectos, sus conexiones entrantes llegarán siempre por 127.0.0.1, y un puerto aleatorio, por lo que por esa vía no podremos identificar a nuestros visitantes. Vamos a preparar un servidor de NVDA Remote siguiendo estos pasos:

1. Instala un servidor de NVDA Remote en Windows ([usa esta guía](https://nvda.es/documentacion/guia-de-usuario-del-servidor-de-nvda-remote/)). Genera un certificado nuevo para él y ponlo en marcha.
2. Crea una carpeta para el servicio oculto, por ejemplo `C:\tor\data\remote`.
3. Edita el archivo torrc, que dejamos en la carpeta `C:\tor` y añade el servicio oculto.
4. Reinicia TOR.
5. Accede a la carpeta que creaste para el servicio, y observa que ahora tiene algunos archivos. Abre el archivo hostname con un editor de texto. Su contenido es la dirección del servicio oculto recién creado, que se puede compartir o publicar según sea necesario.

Sí, lo sé, no he explicado qué hay que añadir al archivo torrc, no quería que se me descuadrara la lista de pasos. Simplemente, pon estas líneas:

```
HiddenServiceDir C:\tor\data\remote
HiddenServiceEnableIntroDoSDefense 1
HiddenServicePort 6837 127.0.0.1:6837
```

Estas líneas pueden repetirse tantas veces como sea necesario para añadir varios servicios. Mantén a salvo la carpeta del servicio, ya que contiene información sensible que un atacante podría usar para replicarlo, como las claves privadas. Si borras o pierdes la carpeta, perderás esa dirección de servicio sin posibilidad de recuperación.

Ahora, en NVDA, instala los complementos [TeleNVDA](https://nvda.es/2022/05/15/telenvda/) y [Soporte proxy](https://nvda.es/2021/01/08/soporte-proxy/). Abre las opciones de NVDA y ve a la categoría Proxy. En el grupo "Todo el tráfico", indica que usarás un proxy de tipo SOCKSv5, dirección localhost, y puerto 9050. Para finalizar el experimento, conéctate con TeleNVDA indicando la dirección de tu servicio oculto en el campo "Equipo o servidor". Observa que aunque tanto tu copia de NVDA como el servidor están en el mismo ordenador, la conexión se establece pasando por un circuito bastante extenso, por lo que irá lenta.

### I2P

I2P se define a sí mismo como "el Internet invisible". Su propósito es similar al de TOR: crear una red cifrada sobre Internet que permita una navegación anónima y privada. Sin embargo, tiene varias diferencias que debemos conocer:

* No está pensado para visitar servicios fuera de I2P: existen algunos nodos de salida para navegar por la web, pero pueden funcionar de forma intermitente. I2P se centra más en los contenidos de la propia red I2P.
* Es una red distribuida: mientras que TOR tiene directorios centrales, I2P dispone de una base de datos de red descentralizada a lo largo de enrutadores con buen ancho de banda, ejecutados por voluntarios y elegidos aleatoriamente por la red. Sus únicos puntos centrales son los servidores de resembrado, utilizados para conectar a la red a nuevos nodos que se ejecutan por primera vez.
* Las direcciones de los servicios I2P son también valores hash de claves públicas, pero se pueden personalizar mucho más que las .onion. Cada enrutador I2P dispone de una libreta de direcciones, que asocia nombres de servicios a hashes de claves, similar al archivo hosts de un sistema operativo.
* Cada usuario, por defecto, participa en la red redirigiendo tráfico de otros usuarios y formando parte de uno o varios túneles, lo que mejora el anonimato. Para ello, se cede ancho de banda de nuestra conexión. Como veremos, este comportamiento se puede desactivar para usar la red como nodo oculto. En condiciones óptimas en las que se cede ancho de banda, I2P requiere un puerto aleatorio abierto en el router. Si el router dispone de UPNP y lo tiene activado, I2P gestionará este puerto por sí mismo.
* No hay ninguna entidad que expida certificados SSL válidos. Los certificados son menos habituales que en TOR, y están autofirmados.

El enrutador principal ofrecido por el proyecto I2P está programado en Java. Se trata de una solución autocontenida, más pesada que TOR, que incluye aplicaciones como un cliente de correo, un cliente BitTorrent y un servidor web propios, todos ellos centrados en preservar el anonimato y la privacidad. La consola del enrutador está traducida a español, y permite configurar, activar y desactivar módulos y túneles según sea necesario. Nuevamente, es tan extenso que no vamos a explicar todo. De hecho, puesto que la interfaz es más intuitiva y la documentación tiene una buena parte traducida, no hablaremos de cómo crear un servicio oculto propio. Veamos cómo instalarlo paso a paso en Windows.

#### Instalación de Java

Como todo buen software basado en Java, el primer paso para que I2P funcione es instalar Java. La versión 8, la que tradicionalmente se instala desde java.com, ya no es válida. Se necesita Java 17 o posterior. La versión actual más reciente es la 26, y su instalador se puede obtener desde el siguiente enlace:

[Descarga el JDK de Java 26](https://download.oracle.com/java/26/latest/jdk-26_windows-x64_bin.exe)

La instalación no tiene mucho misterio, tan sólo hay que seguir las pantallas del asistente. Si dispones de varios entornos Java, asegúrate de que Java 26 es el que se ofrece por defecto. Desde una consola, ejecuta el comando `java -version` para averiguarlo. Si aparece una versión anterior, modifica las variables de entorno del sistema, en particular path y, si existe, JAVA_HOME. Para acabar, agrega los archivos java.exe y javaw.exe al firewall de Windows para que puedan operar sin restricciones.

#### Instalación de I2P

I2P no es tan portable como TOR. Ofrece dos modalidades de instalador: un archivo de gran tamaño con Java incorporado y ciertos plugins de navegador, y un instalador más sencillo que depende de Java y no incorpora tantos elementos. Por suerte o por desgracia, el segundo es el más accesible, tanto durante la instalación como después, así que es el que usaremos.

La versión actual de I2P es la 2.12. Puedes [descargar el instalador con este enlace directo](https://files.i2p.net/2.12.0/i2pinstall_2.12.0_windows.exe), o [visitar la página de descargas](https://i2p.net/es/downloads/) en caso de que haya una versión más reciente.

El instalador es intuitivo. Tan sólo hay que seguir las pantallas del asistente, con una particularidad: en la pantalla de selección de componentes debemos buscar la tabla y marcar la casilla "Windows service". El servicio de Windows es la forma más accesible de gestionar el funcionamiento de I2P.

Una vez creados los accesos directos, llegaremos a la última pantalla. Tras pulsar el botón "Hecho", el servicio se pondrá en marcha por sí solo e I2P estará listo para funcionar.

Para desinstalar I2P, navega a la ruta "C:\Program Files\i2p" y ejecuta el archivo uninstall_i2p_service_winnt.bat. Esto desinstalará el servicio, al igual que install_i2p_service_winnt.bat lo reinstala de nuevo. Después, ejecuta el archivo uninstaller.jar que hay dentro de la carpeta uninstaller. Desde consola, se puede ejecutar `java -jar uninstaller.jar`. La configuración de i2p y todos los datos, por su parte, se encuentran en "C:\Program data\i2p", y deben eliminarse a mano.

En cuanto a las actualizaciones, se descargan y se instalan por sí solas de forma transparente mientras el servicio esté en marcha. Es recomendable dejar encendido el gestor de torrents, ya que se usa como canal de intercambio por defecto.

#### Configuración inicial de I2P

Ahora que el servicio de I2P está en marcha, podemos acceder a la consola web. Para ello, se puede abrir el acceso directo "I2P router console" que hay en el escritorio o, desde un navegador, se puede visitar la siguiente dirección: <http://localhost:7657>

La primera pantalla que se mostrará al entrar es la selección de idioma. Se puede omitir la configuración inicial, pero es recomendable no hacerlo y seguir el asistente hasta el final. Las pruebas de ancho de banda, si bien es cierto que también se recomiendan, pueden omitirse para no revelar a M-Lab nuestra dirección ip. Al completarlas, I2P sugerirá unos valores máximos de entrada y salida, y nos preguntará cuánto ancho de banda deseamos compartir. El valor predeterminado es 80%, pero se puede bajar al 0. No obstante, esto todavía no oculta nuestro enrutador.

Para evitar formar parte de túneles de otros enrutadores, visitaremos la [página de configuración de red de la consola](http://localhost:7657/confignet), elegiremos la opción "Modo oculto - no se publica la IP (evita el tráfico participante)" y pulsaremos el botón Guardar cambios. Desde este momento, I2P ya no requiere puertos abiertos al exterior, ni usará UPNP para redirigirlos cuando esté disponible. El "modo portátil" también puede ser útil si istalamos I2P en un ordenador portátil y cambiamos con frecuencia de red.

#### La libreta de direcciones

El potencial de I2P radica en sus servicios ocultos. Por defecto, al igual que sucede en TOR, la dirección de un servicio oculto es el hash de una clave pública, acabado en ".b32.i2p". Por ejemplo, la dirección de la audiocinemateca en I2P es `huffwds7ecz2atf2kzljvykkazoper444ehrp5nfkgdxgzkrp2oq.b32.i2p`. Gracias a la libreta de direcciones, se puede crear un nombre más amigable, como `audiocinemateca.i2p`, mucho más sencillo de memorizar, escribir en la barra de direcciones del navegador y almacenar en marcadores.

La libreta de direcciones se gestiona desde su [página correspondiente en la consola](http://localhost:7657/susidns/index). Hay cuatro tipos de libretas:

* Privada: contiene un listado de direcciones que no queremos compartir con otros usuarios de I2P.
* Local: contiene un listado de direcciones propio que se puede compartir con otros usuarios.
* Publicada: contiene direcciones que se publican para que otros usuarios puedan sincronizar su libreta con la nuestra, siempre que estemos sirviendo un sitio web por I2P y hayamos dado permiso para compartirla. Se sincroniza con la local, pero no tiene por qué coincidir con ella.
* Router I2P: contiene direcciones obtenidas de un servicio de suscripción.

En la página de suscripciones podemos gestionar desde qué servicios se actualiza la libreta del enrutador. Por defecto se ofrece uno, mantenido por el proyecto I2P y con muy pocas entradas. A continuación dejo algunos más que se pueden agregar debajo:

```
http://notbob.i2p/hosts-all.txt
http://reg.i2p/export/hosts-all.txt
http://skank.i2p/hosts.txt
http://stats.i2p/cgi-bin/newhosts.txt
```

La audiocinemateca está registrada en varios de estos servicios, por lo que tan pronto como se sincronicen, podrás entrar escribiendo `audiocinemateca.i2p` en la barra de direcciones del navegador.

Existe una forma alternativa y rápida de añadir entradas si no podemos o no queremos sincronizar libretas externas. El propietario de un sitio web puede proporcionarnos un enlace como este: `http://audiocinemateca.i2p/?i2paddresshelper=huffwds7ecz2atf2kzljvykkazoper444ehrp5nfkgdxgzkrp2oq.b32.i2p`.

Al visitarlo con el navegador, aparecerá una ventana de la consola preguntando qué queremos hacer. Se puede agregar la entrada a cualquiera de las libretas disponibles, o no hacer nada y simplemente navegar al destino.

#### Configuración del navegador

Hasta ahora, hemos configurado el enrutador de I2P, un proceso que no entraña riesgos de seguridad. Los riesgos vienen al navegar, a pesar de que el propio enrutador ofrece una serie de protecciones para mitigarlos, como la eliminación de ciertas cabeceras http. A grandes rasgos, el proceso consiste en configurar el navegador para que utilice el proxy http que hay en la dirección 127.0.0.1, puerto 4444. Sin embargo, en este apartado vamos a hacer algo más: configurar un perfil alternativo sólo para navegar por aquí, manteniendo el resto de nuestros datos bien asegurados y apartados en el perfil por defecto. El navegador, por supuesto, será Firefox en su última versión. Sigue estos pasos:

1. Pulsa Windows+r para abrir el diálogo Ejecutar, escribe `firefox -p` y pulsa `intro`. Aparecerá una ventana de selección de perfil.
2. Crea un nuevo perfil con el nombre que prefieras. Por ejemplo, puedes llamarlo i2p.
3. Selecciona el perfil en la lista y pulsa `intro` para usarlo. Firefox arrancará como si se hubiera instalado por primera vez.
4. Configura Firefox para que sea tan privado como permitan las opciones.
5. Ve a los ajustes de Firefox, pulsa el botón "Configuración" que hay en la primera pantalla e indica los datos del proxy, que redirigirá tanto el tráfico http como el https. Recuerda: el host es 127.0.0.1 o localhost, y el puerto 4444.
6. Navega por la red I2P con normalidad (y con cuidado).

Para dejar de navegar por I2P, vuelve a ejecutar `firefox -p` y regresa al perfil por defecto.

A continuación, enumero algunas configuraciones y consejos que pueden ser útiles para aumentar el grado de privacidad:

* Instalar la extensión noscript y configurarla en modo estricto, exactamente como hicimos con Tor Browser.
* Activar el modo permanente de navegación privada.
* Bloquear todos los rastreadores y cookies, quitando las excepciones de la protección máxima. No te preocupes, las webs funcionarán.
* Deshabilitar las protecciones contra software fraudulento o descargas peligrosas, así como el envío de informes de error, y la recomendación de extensiones y funciones mientras se navega. De esa forma se reduce el envío de datos a Mozilla.
* Deshabilitar la reproducción de contenido con DRM.
* La casilla "Pedir a los sitios web que no compartan ni vendan mis datos" parece útil, pero sólo envía una cabecera http extra, no nos aportará nada.
* Existen ajustes adicionales para reducir nuestra exposición en general y nuestra huella de navegación, pero deben activarse en about:config.
* Para otros servicios de I2P que no sean web, tales como correo, chat o intercambio de archivos, se recomienda usar las aplicaciones de la consola en la medida de lo posible.

Si no tienes claro qué ajustes modificar, puedes usar este [archivo user.js](/user.js.zip). Descomprímelo y cópialo en la raíz de tu carpeta de perfil, por ejemplo `C:\users\usuario\appData\roaming\mozilla\firefox\profiles\i2p`. Está basado en el archivo user.js de Arkenfox, con algunos cambios hechos al final y comentados en español. Los ajustes de proxy vienen configurados para usar Privoxy por el puerto 8118, que es justo lo que veremos a continuación.

### Cómo usar Privoxy para conectar una aplicación a TOR e I2P a la vez

Imagina un escenario más práctico y menos anónimo donde queremos que un navegador seguro, o una aplicación de otro tipo, pueda navegar por Internet con normalidad sin enrutar tráfico por Tor o I2P, pero al mismo tiempo pueda acceder a sitios .i2p y servicios .onion. Aquí entra en juego Privoxy.

Privoxy es un proxy de exploración de la web sin caché, que incluye acciones y filtros para bloquear anuncios, mejorar la privacidad y editar contenido web al vuelo mientras navegamos. Para lograr el objetivo que nos hemos propuesto no editaremos filtros ni acciones, por lo que funcionará como un proxy casi neutral. Simplemente, nos apoyaremos en sus capacidades de redirigir tráfico según la URL buscada.

Para instalar Privoxy en Windows, se puede [descargar la versión 4.1.0 como un archivo zip](https://www.privoxy.org/sf-download-mirror/Win32/4.1.0/privoxy_4.1.0.zip). Consulta la [página principal de Privoxy](https://www.privoxy.org/) para descargar una versión posterior, si la hay.

Ahora, descomprimimos el archivo zip en una carpeta cualquiera, por ejemplo la raíz de nuestro disco duro. De esa forma, todos los ficheros del programa quedarán en `C:\privoxy_4.1.0`.

El siguiente paso es editar el fichero de configuración config.txt que se encuentra en la carpeta de Privoxy. Si lo deseas, haz una copia del original por si quisieras usar Privoxy de otra manera en el futuro. El fichero debe vaciarse por completo, y quedar sólo con estas líneas:

```
forward .   .
forward-socks5t .onion  127.0.0.1:9050    .
forward .i2p 127.0.0.1:4444
```

Teniendo la configuración lista, instalamos el servicio. Desde una consola con privilegios de administrador navegamos a la carpeta de Privoxy. Una vez allí, ejecutamos este comando: `privoxy --install`. Aparecerá una ventana con un mensaje de confirmación, ivitándonos a entrar en la consola `services.msc` para gestionar el servicio. Y es precisamente allí donde podremos iniciarlo, sin necesidad de retoques adicionales.

Para acabar, llega el turno de los clientes. Aunque no es el ideal, seguiremos poniendo como ejemplo el navegador. Si configuraste en la sección anterior un perfil de Firefox adicional, tan sólo deberás usarlo y reconfigurar el proxy en los ajustes de Firefox. Cambia el puerto 4444 por el 8118, ¡y a navegar! Podrás comprobar que accedes a cualquier web sin dificultad, y también a dominios .i2p y .onion.

### Precauciones al navegar por la red oscura

Al navegar por Internet existen multitud de peligros, y la red oscura no es una excepción. A todos los ataques habituales debemos sumar uno más: la gente va a actuar de forma proactiva para averiguar quién eres: desde el dueño de un servicio oculto que quiera conocer a sus clientes, hasta los operadores de telefonía o las autoridades. De hecho, saber quién eres puede llegar a ser incluso más importante que saber qué haces. Aunque no te importe que se sepa tu identidad y hagas cosas dentro de la ley, conviene que tengas en cuenta lo siguiente:

* Los scripts son un vector de ataque: me gusta decir que la web oscura es el reino de los elementos nativos. Una buena web en un servicio oculto debe tener Javascript como algo, si acaso, opcional si quiere dar un buen servicio a sus visitantes. Angular, React y cualquier otro framework están fuera de lugar aquí.
* El tamaño de pantalla, las media queries de css, la carga de contenido multimedia en un formato específico, e incluso las cabeceras http pueden contener elementos dirigidos a identificarte. Algo tan simple como una hoja de estilos que diferencie entre enlaces visitados o no visitados puede lanzar una conexión con una URL concreta y decirle al dueño del servicio que ya has estado ahí.
* Por tanto, además de Javascript, se recomienda desactivar también el historial y las cookies.
* Si necesitas habilitar todo o parte de lo anterior, asegúrate de que el servicio es de confianza, y rebaja las protecciones sólo para ese servicio, por ejemplo añadiéndolo como excepción en las opciones del navegador.
* Usa siempre un navegador suficientemente seguro, como TOR Browser. Evita navegadores basados en Chromium, como Brave. Aunque estén preparados para TOR y lo lleven incorporado, envían más telemetría y datos por ahí fuera de los que deben.
* No utilices estas tecnologías en empresas o equipos de empresa. Los antivirus suelen reaccionar mal, el personal de seguridad también, y el de recursos humanos probablemente también.
* Ten respeto, pero no miedo. No hay que bajar la guardia en estas redes, pero tampoco asustarse. Si realmente te asusta entrar en este terreno o no crees tener los conocimientos suficientes, lo mejor es que no toques.
* Mantén todo el software actualizado. Es una obligación, no es opcional. Cada nueva versión de TOR o I2P mejora el cifrado, la gestión de la red, y puede corregir agujeros críticos de seguridad.

### Al final no has respondido a la pregunta: ¿qué implica conectar Mastodon a la red oscura?

Para ti, como usuario, ningún cambio perceptible. Desde la configuración de perfil se pueden bloquear los dominios .onion y .i2p para no interactuar con ellos. Por otro lado, esta conexión abriría la posibilidad de seguir cuentas de instancias ocultas, conversar con ellas y conocer una parte de Internet a la que actualmente no tenemos acceso. Sin embargo, también entraña riesgos. Como hemos visto, crear dominios es una tarea muy sencilla, y si alguien decide enviar spam o acosar, detenerlo puede ser más complicado que en la red habitual. También puede que algunas conversaciones no aparezcan completas desde otras instancias no conectadas a la red oscura.

### Referencias

* [Artículo de TOR en la Wikipedia](https://es.wikipedia.org/wiki/Tor_(red_de_anonimato))
* [Acerca de TOR en la web del proyecto](https://support.torproject.org/es/about-tor/)
* [Página principal de I2P](https://i2p.net/es/)
* [Documentación de I2P](https://i2p.net/es/docs/)
* [Página principal de Privoxy](https://www.privoxy.org/)
* [Manual de usuario de Privoxy](https://www.privoxy.org/user-manual/index.html)
* [Fichero de configuración priv-config usado en Mastodon](https://github.com/mastodon/mastodon/blob/main/priv-config)
* [Fichero user.js de Arkenfox](https://github.com/arkenfox/user.js)
