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

TOR son las siglas de The Onion Router, el enrutador de la cebolla. Según Wikipedia, su lanzamiento inicial se produjo en septiembre de 2002, así que ya lleva casi medio siglo entre nosotros. Su objetivo es construir una red sobre Internet donde se preserve el anonimato de las comunicaciones, y el destinatario de las mismas (por ejemplo, un servidor) no conozca su origen (dirección IP del cliente). Esto se consigue montando un circuito formado por varios nodos que cifran y retransmiten las comunicaciones entre origen y destino. Por defecto, un circuito está formado por tres nodos, y añadir más no es garantía de mayor anonimato, pero sí de mayor lentitud. La arquitectura de TOR se podría dividir en los siguientes componentes:

* Cliente: se encarga de cifrar las comunicaciones en origen y establecer un circuito de varios nodos. Existe el mito de que un cliente puede redirigir tráfico de la red TOR de otros usuarios. Antes era así, pero ahora es mentira, sólo dirigirá tu propio tráfico hacia donde tú le indiques. El cliente también puede ofrecer servicios ocultos.
* Repetidor: los repetidores son nodos que distribuyen el tráfico por la red TOR. Son mantenidos por voluntarios u organizaciones.
* Nodo de salida: es el nodo que actúa como salida de un circuito. Su propósito es descifrar las comunicaciones, enviarlas a destino, cifrar la respuesta y enviarla de vuelta por el circuito hasta el origen.
* Directorio: el directorio contiene un listado de nodos que actúan como repetidores, nodos de salida, políticas de salida de estos nodos y datos sobre servicios ocultos (explicados más adelante). Existen varios directorios, ya que si hubiera uno solo y fallara, toda la red se caería.

Un mismo nodo puede ejercer como repetidor, nodo de salida y cliente, y son los que garantizan más anonimato. Sin embargo, requieren diversos trámites para ser incluidos en los directorios y exponen a sus propietarios ante las autoridades, por lo que lo más sencillo es ejecutar simplemente un cliente, que además no requiere que se abran puertos en el router ni ningún otro tipo de configuración.

#### Clientes disponibles

El programa TOR es un cliente minúsculo que se maneja desde consola y se suele ejecutar como servicio. Si habías oído que TOR es un navegador, te has topado con otro mito. Es habitual confundir TOR con TOR Browser, el navegador recomendado por el proyecto que también lleva TOR incorporado. Veamos cómo instalar y configurar cada uno de ellos en Windows.

##### TOR Browser

TOR Browser es un navegador web basado en Mozilla Firefox ESR. Es tan accesible con lectores de pantalla como Firefox y, a excepción de algunas opciones de menú y botones propios, tiene la misma interfaz. Tor Browser cuenta, además, con un perfil configurado a medida, extensiones preinstaladas y otras modificaciones cuyo objetivo es lograr un mayor grado posible de anonimato y protección de la privacidad equilibrado con una experiencia de navegación adecuada. Esto significa que podemos configurarlo para que nos proteja aún más de lo que ya lo hace. Por ejemplo, aumentando al grado máximo las restricciones de la extensión Noscript.

Para descargarlo, podemos acudir a la [página de descarga de Tor Browser](https://www.torproject.org/download/languages/), buscar la tabla y elegir el instalador de la plataforma que corresponda. En este caso, para Windows de 64 bits, dejo el [enlace a la versión 15.0.7](https://dist.torproject.org/torbrowser/15.0.7/tor-browser-windows-x86_64-portable-15.0.7.exe), la actual mientras escribo esta guía. No uses este enlace si hay una versión posterior. Como veremos más adelante, mantenerse actualizado en este terreno no es opcional, sino una precaución más. Si quieres actualizar el navegador, conéctalo a la red TOR, ve al menú Ayuda y elige la opción Acerca de, tal y como harías con Firefox.

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

Para descargar la versión más reciente, podemos acudir a la [página de descarga de TOR](https://www.torproject.org/download/tor/) y elegir la expert bundle más adecuada para nuestra plataforma. La versión actual es la 0.4.9.5 y, de nuevo, advertimos que debe mantenerse actualizada por seguridad. Si no tienes tiempo de actualizar, es mejor que TOR permanezca apagado. La plataforma también importa, no sólo por rendimiento, sino por capacidad criptográfica. Dicho de otro modo: no elijas TOR para Windows de 32 bits si tu Windows es de 64.

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

En construcción.

### Cómo usar Privoxy para conectar un navegador a TOR e I2P a la vez

En construcción.

### Precauciones al navegar por la red oscura

Al navegar por Internet existen multitud de peligros, y en la red oscura no es una excepción. A todos los ataques habituales debemos sumar uno más: la gente va a actuar de forma proactiva para averiguar quién eres: desde el dueño de un servicio oculto que quiera conocer a sus clientes, hasta los operadores de telefonía o las autoridades. De hecho, saber quién eres puede llegar a ser incluso más importante que saber qué haces. Aunque no te importe que se sepa tu identidad y hagas cosas dentro de la ley, conviene que tengas en cuenta lo siguiente:

* Los scripts son un vector de ataque: me gusta decir que la web oscura es el reino de los elementos nativos. Una buena web en un servicio oculto debe tener Javascript como algo, si acaso, opcional si quiere dar un buen servicio a sus visitantes. Angular, React y cualquier otro framework están fuera de lugar aquí.
* El tamaño de pantalla, las media queries de css, la carga de contenido multimedia en un formato específico, e incluso las cabeceras http pueden contener elementos dirigidos a identificarte. Algo tan simple como una hoja de estilos que diferencie entre enlaces visitados o no visitados puede lanzar una conexión con una URL concreta y decirle al dueño del servicio que ya has estado ahí.
* Por tanto, además de Javascript, se recomienda desactivar también el historial y las cookies.
* Si necesitas habilitar todo o parte de lo anterior, asegúrate de que el servicio es de confianza, y rebaja las protecciones sólo para ese servicio.
* Usa siempre un navegador suficientemente seguro, como TOR Browser. Evita navegadores basados en Chromium, como Brave. Aunque estén preparados para TOR y lo lleven incorporado, envían más telemetría y datos por ahí fuera de los que deben.
* No utilices estas tecnologías en empresas o equipos de empresa. Los antivirus suelen reaccionar mal, el personal de seguridad también, y el de recursos humanos probablemente también.
* Ten respeto, pero no miedo. No hay que bajar la guardia en estas redes, pero tampoco asustarse. Si realmente te asusta entrar en este terreno o no crees tener los conocimientos suficientes, lo mejor es que no toques.
* Mantén todo el software actualizado. Es una obligación, no es opcional. Cada nueva versión de TOR o I2P mejora el cifrado, la gestión de la red, y puede corregir agujeros críticos de seguridad.

### Al final no has respondido a la pregunta: ¿qué implica conectar Mastodon a la red oscura?

Para ti, como usuario, ningún cambio perceptible. Desde la configuración de perfil se pueden bloquear los dominios .onion y .i2p para no interactuar con ellos. Por otro lado, esta conexión abriría la posibilidad de seguir cuentas de instancias ocultas, conversar con ellas y conocer una parte de Internet a la que actualmente no tenemos acceso. Sin embargo, también entraña riesgos. Como hemos visto, crear dominios es una tarea muy sencilla, y si alguien decide enviar spam o acosar, detenerlo puede ser más complicado que en la red habitual. También puede que algunas conversaciones no aparezcan completas desde otras instancias no conectadas a la red oscura.

### Referencias

* [Artículo de TOR en la Wikipedia](https://es.wikipedia.org/wiki/Tor_(red_de_anonimato))
* [Acerca de TOR en la web del proyecto](https://support.torproject.org/es/about-tor/)
