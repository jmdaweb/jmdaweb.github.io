---
title: "De Docker al fediverso"
date: 2024-06-03T15:45:34+02:00
reply:
uri: /posts/docker-mastodon/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

En este tutorial aprenderemos conceptos básicos de Docker. Después levantaremos una instancia de Mastodon, una red social descentralizada, y tomaremos medidas para que sea lo más segura posible. Dicha instancia estará formada por contenedores que operan de forma sincronizada, y que ofrecerán sus servicios al público mediante el servidor web Apache.

## Requisitos

* Un servidor con Debian 12 o Ubuntu. Se recomienda que esté protegido, sólo con los puertos ssh, http y https abiertos.
* Acceso por SSH y conocimientos del manejo de la consola.

## Introducción a Docker

Docker es una tecnología que nos permite desplegar aplicaciones y simular ciertas condiciones para que funcionen correctamente. Estas aplicaciones se ejecutan dentro de "contenedores". Un contenedor se queda a medio camino entre el equipo anfitrión y una máquina virtual:

* Engaña a la aplicación contenida.
* Simula redes que en el equipo anfitrión no están.
* Simula un sistema de archivos propio.
* Le da a la aplicación las versiones de las librerías que necesita, que a lo mejor no se corresponden con las del equipo anfitrión. Eso ofrece más garantías de que siempre funcionará igual, esté donde esté.

Sin embargo, un contenedor no se ejecuta sobre un núcleo separado. Una aplicación preparada para tal fin puede escalar y acceder al equipo anfitrión sin nuestro permiso.

Los contenedores tienden a ser efímeros: cuando terminan de ejecutarse, lo normal es destruirlos. Se crean a partir de imágenes, que podemos construir por nuestra cuenta o descargar de algún registro. El registro por defecto, si no configuramos ninguno, es el [Docker Hub](https://hub.docker.com), y allí es donde suelen subirse las imágenes con las que vamos a trabajar. De hecho, si nos hacemos una cuenta, podemos subir nuestras propias imágenes.

## Instalación de Docker

Una vez instalado, los comandos para interactuar con Docker son los mismos en cualquier plataforma, también en Windows. Ya que en todos los tutoriales de este sitio hemos trabajado con Debian, vamos a usar Debian aquí también:

1. Instalamos paquetes que nos permitirán agregar repositorios https: `apt update && apt install apt-transport-https ca-certificates curl gnupg lsb-release`
2. Agregamos la clave GPG del repositorio de Docker: `mkdir -m 0755 -p /etc/apt/keyrings && curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg`
3. Agregamos el repositorio: `echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null`
4. Instalamos el motor de Docker: `apt update && apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

Se puede cambiar la palabra debian por ubuntu en la URL de los repositorios si queremos hacer la instalación en ese sistema.

En la consola, hemos encadenado dos comandos para que se ejecuten seguidos. Esto se hace con el operador &&, que no habíamos visto hasta ahora.

## Nuestro primer contenedor: Fedora

Comprar un servidor VPS sólo para probar una nueva distribución de Linux cuesta dinero, y virtualizarla puede ser imposible cuando no hay accesibilidad. Antes de lanzarnos a cambiar de distribución, puede ser una buena idea hacer algunas pruebas y ver si entendemos sus comandos.

En primer lugar, vamos a bajarnos la imagen más reciente de Fedora: `docker pull fedora:latest`

En Docker, es habitual que la etiqueta latest apunte a la versión estable más reciente de la imagen deseada. De hecho, la palabra latest se asume si no indicamos otra etiqueta.

Una vez descargada, podemos verla con el siguiente comando: `docker images`

La imagen, además de su nombre, lleva un identificador asociado. Gracias a él, podremos eliminarla cuando ya no la queramos: `docker rmi identificador`

Pero es un poco pronto para eliminarla, teniendo en cuenta que no la hemos usado. Creemos un contenedor:

`docker run --rm -t -i fedora:latest /bin/bash`

En este comando, hemos indicado que el contenedor será destruido al apagarse. Se acoplará una terminal para él, así como la entrada estándar de teclado, usará la imagen de Fedora con el tag latest, y dentro ejecutará el comando /bin/bash. El resultado es una consola dentro del contenedor. Podemos experimentar con ella, llamar a `dnf update` para actualizar los paquetes, y salir con control+d o el comando exit. Al abandonarla, el contenedor será destruido.

Si queremos hacer un contenedor que no se destruya, el comando varía ligeramente:

`docker run --name micontenedor -t -i fedora:latest /bin/bash`

En este caso, el contenedor guardará y recordará los cambios que hagamos en su interior. Al salir se detendrá, pero no se destruirá. 
Podremos volver a él con este comando:

`docker start -i -a micontenedor`

Si queremos eliminarlo: `docker rm micontenedor`

Es muy importante eliminar todos los contenedores creados a partir de una imagen antes de eliminar la imagen. Se puede hacer al revés con el modificador `--force`, pero puede tener efectos no deseados.

## Segundo contenedor: un servidor de NVDA Remote

Tener la consola en pantalla es útil para experimentar, pero no es lo habitual. Lo ideal es que cada contenedor ofrezca sus servicios en segundo plano. Para ello, durante su creación, prescindiremos de los modificadores -t y -i.

El contenedor que vamos a crear ahora ofrece un servicio de red. Por defecto, Docker no abre los puertos del contenedor al exterior, sino que es algo que debemos pedirle nosotros. Para ello, usaremos el argumento -p, seguido del puerto exterior, y finalmente del interior. Sabemos que la imagen de NVDA Remote Server abre el puerto 6837. Sin embargo, desde la red del trabajo sólo nos dan libertad para acceder a los puertos 80 y 443, así que configuraremos el 443 como puerto de entrada. Con el argumento -d, además, le diremos al contenedor que se ejecute en segundo plano y nos devuelva el control de la consola en cuanto se inicie:

`docker run -d -p 443:6837 --name micontenedor jmdaweb/nvda-remote-server:latest`

Como se puede observar, esta imagen no estaba descargada, pero se descarga en cuanto la solicitamos.

Ahora, con el comando `docker ps`, podemos ver que el contenedor está activo. El comando `docker logs micontenedor` mostrará por pantalla la salida de la aplicación. Y si con start iniciábamos, `docker stop micontenedor` detendrá el contenedor indicado.

Con el contenedor activado, podremos conectarnos a un servidor de remote totalmente operativo en ipDelServidor:443.

## Comunicación entre contenedores

Docker permite crear y gestionar redes. No profundizaremos demasiado en ello, ya que sólo necesitaremos unos conocimientos básicos.

El comando `docker network --help` nos dará más información. Cuando conectamos varios contenedores a una misma red, estos pueden interactuar entre sí sin que sus puertos estén abiertos al exterior. Por ejemplo, si tenemos una arquitectura en la que PHP se ejecuta con Wordpress en un contenedor, Apache en otro y MySQL en otro, nos interesará que todos se conecten entre sí, pero sólo Apache aceptará conexiones entrantes del exterior. Al crear un contenedor con docker create o docker run, se puede especificar su red con el argumento `--network`, seguido del nombre de la misma. Si escribimos `--network host`, el contenedor se comportará como cualquier aplicación del equipo anfitrión, ofreciendo sus puertos sin ninguna clase de traducción de red. Por tanto, el modificador `-p` dejaría de ser necesario.

## Compartir archivos con volúmenes

Y si lo normal es destruir un contenedor al acabar de usarlo, ¿qué sucede con los datos que se generan en su interior? ¿De qué vale la base de datos que me he montado en MySQL? Cuando queremos preservar información, se emplean volúmenes. Dichos volúmenes pueden ser carpetas del sistema montadas en el contenedor, o almacenes gestionados por el propio Docker. Quizá la primera opción nos interese más, ya que así tendremos en todo momento los datos a mano. Hagamos un experimento sencillo con la imagen de Fedora que ya tenemos:

1. Creamos una carpeta dentro de /root que actuará como volumen: `mkdir /root/contenedores-pruebas`
2. Arrancamos un nuevo contenedor con Fedora: `docker run --rm -v ./contenedores-pruebas:/root/contenedores-pruebas -t -i fedora:latest /bin/bash`
3. Desde la consola de Fedora, escribimos información en un fichero de texto dentro del volumen: `echo hola > /root/contenedores-pruebas/prueba.txt`
4. Salimos de la consola, lo que provoca la destrucción del contenedor. Sin embargo, en la carpeta asociada al volumen, permanece el archivo txt con lo que escribimos.

¡Importante! Para que Docker monte volúmenes a partir de carpetas existentes, se debe indicar la ruta absoluta. De lo contrario, pensará que se trata de un volumen administrado, y fallará si el volumen no existe.

## Ejecución de comandos dentro de un contenedor

Normalmente, cada contenedor ejecuta un único proceso, pero viene equipado de tal forma que podemos ejecutar más. Si queremos modificar el fichero de configuración del servidor de NVDA Remote, debemos usar Nano dentro del contenedor. De hecho, se ha incluido a propósito en la imagen original para poder hacerlo. Mientras el contenedor está activado, podemos llamar al comando docker exec, indicando el nombre y el comando que se ejecutará. Por ejemplo:

`docker exec -t -i my_nvda_remote nano /etc/NVDARemoteServer.conf`

Esto se aplicará más adelante en Mastodon para hacer copias de seguridad de la base de datos o gestionar aspectos de la instancia, por ejemplo.

## Los ficheros docker-compose.yml

Cuando queremos desplegar un servicio que necesita usar varios contenedores, redes y volúmenes, poner en marcha cada elemento puede convertirse en una tarea repetitiva y no exenta de fallos. Docker-compose viene a solucionar el problema. Podemos crear un archivo docker-compose.yml con una serie de instrucciones en su interior que siguen un formato concreto en lenguaje Yaml, y esta herramienta se encargará de levantar o destruir el servicio a petición, creando y eliminando contenedores en el camino.
Tiempo atrás, docker-compose se distribuía como un ejecutable independiente. Había que prestar atención para actualizarlo cada vez que salía una nueva versión. Ahora, viene en forma de plugin de Docker, y ya lo hemos instalado al principio de este capítulo. El gestor de paquetes de nuestra distribución ayudará a mantenerlo al día junto con todo lo demás.

Para utilizar un archivo docker-compose.yml, navegamos al directorio donde se encuentra. A continuación, podemos ejecutar algunos de estos comandos:

* `docker compose up -d`: pone en marcha todos los servicios del archivo. Descarga imágenes si es necesario, y luego crea redes, volúmenes y contenedores.
* `docker compose down`: apaga el servicio. Elimina redes, contenedores y volúmenes, pero no imágenes.
* `docker compose run --rm servicio comando`: crea sólo un contenedor con el servicio indicado, cuyo nombre está en el archivo docker-compose.yml, y ejecuta en su interior el comando solicitado.
* `docker compose pull`: actualiza todas las imágenes relacionadas con el servicio a sus versiones más recientes.

### Archivo docker-compose.yml de ejemplo para Libre Translate

Libre Translate es un traductor gratuito, de código abierto y autoalojado. Con poco más de 20 GB libres, cualquier persona puede montarlo en su servidor con todos los modelos de idioma disponibles. A continuación hay un archivo docker-compose.yml que lo pone en marcha. 

```
version: "3"

services:
  libretranslate:
    container_name: libretranslate
    image: libretranslate/libretranslate:latest
    restart: unless-stopped
    command: --req-limit 30 --char-limit 5000 --api-keys --disable-files-translation --req-limit-storage redis://127.0.0.1:6379 --update-models
    environment:
      - LT_API_KEYS_DB_PATH=/app/db/api_keys.db
    volumes:
      - /root/libretranslate/api_keys:/app/db
      - /mnt/resource/libretranslate:/home/libretranslate
    network_mode: host
```

## Instalación y configuración de la instancia de Mastodon
### Obtención del código
En primer lugar, instalaremos Git en Debian: `apt update && apt install git`

Vamos a asumir que tenemos la sesión iniciada como root. Clonaremos el repositorio de Mastodon en nuestra carpeta de perfil de usuario, que se encuentra en /root:

`git clone https://github.com/mastodon/mastodon.git`

Ahora, accedemos a su interior: `cd mastodon`

Y usamos Git para situarnos en el tag de la versión más reciente. En el momento de redactar este tutorial, la versión más reciente es la 4.2.9: `git checkout v4.2.9`

### Preparación de volúmenes

Mastodon no es una aplicación simple. Está formada por un conjunto de aplicaciones que se coordinan entre sí. En Docker, esto se traduce en varios contenedores, uno por aplicación. Vamos a verlos en detalle:

* Web: ofrece la interfaz web y casi toda la API de Mastodon.
* Streaming: es el encargado de ofrecer notificaciones y publicaciones en tiempo real, así como la API de streaming.
* Sidekiq: ejecuta tareas en segundo plano. Es el corazón de esta red social, el nexo de unión entre todos los demás componentes. Si Sideqkiq falla o va lento, los usuarios lo notarán.
* PostgreSQL: gestor de la base de datos principal. Almacena publicaciones, cuentas de usuario y algunos aspectos de la configuración de la instancia.
* Redis: base de datos de rápido acceso cuyo objetivo es alojar una caché para que las líneas temporales carguen más deprisa, entre otras cosas.
* Elasticsearch: componente opcional que añade capacidades de búsqueda a la instancia. Por supuesto, en este tutorial no es opcional. Ya que está, vamos a usarlo.
* Tor y Privoxy: permiten que la instancia federe con otras instancias de la red Tor, y que los usuarios puedan visitarla de forma anónima. No lo documentaremos aquí. No es seguro ofrecer este tipo de acceso en una instancia de Mastodon.

Muchos de estos contenedores necesitan volúmenes. Como queremos que sus datos persistan entre reinicios y tenerlos a mano, vamos a crearlos como carpetas en el sistema anfitrión dentro del repositorio de Mastodon. Se podrían crear sin problema en cualquier otro sitio, siempre que luego ajustemos las rutas.

```
mkdir elasticsearch
mkdir redis
mkdir postgres16
mkdir system
```

Nota: la carpeta system contendrá elementos multimedia descargados de otras instancias y subidos por nuestros usuarios, por lo que se recomienda que esté en una partición con suficiente espacio libre.

Las carpetas están creadas, pero los contenedores intentarán escribir en ellas utilizando cuentas de usuario con menos privilegios que root por motivos de seguridad. Conociendo los identificadores de usuario y grupo, podemos asignarlos a cada carpeta:

```
chown -R 991:991 system
chown -R 70:70 postgres16
chown -R 1000:0 elasticsearch
chown -R 999:1000 redis
```

### Preparación del núcleo para ElasticSearch

Ya tenemos los volúmenes listos. Antes de continuar poniendo en marcha la instancia, debemos modificar en el núcleo del sistema un parámetro que ElasticSearch necesita, y que por defecto tiene un valor muy bajo.

Para ello, podemos ejecutar el siguiente comando: `echo vm.max_map_count=262144 > /etc/sysctl.d/elastic.conf`

O crear el archivo /etc/sysctl.d/elastic.conf y escribir en su interior vm.max_map_count=262144, que al final se traduce en lo mismo. Después, mediante el comando reboot, reiniciamos el servidor.

### El archivo docker-compose.yml de Mastodon

En el repositorio de Mastodon podemos encontrar un archivo docker-compose.yml en el que se definen varios contenedores y redes. Está preparado para compilar las imágenes desde el principio, algo que no nos interesa pudiendo descargarlas. También tiene muchas líneas comentadas, ya que el buscador ElasticSearch viene deshabilitado. Vamos a modificarlo para adaptarlo a nuestras necesidades. Este sería un posible fichero resultante:

```
version: '3'
services:
  db:
    restart: always
    image: postgres:16-alpine
    shm_size: 256mb
    networks:
      - internal_network
    healthcheck:
      test: ['CMD', 'pg_isready', '-U', 'postgres']
    volumes:
      - ./postgres14:/var/lib/postgresql/data
    environment:
      - 'POSTGRES_HOST_AUTH_METHOD=trust'

  redis:
    restart: always
    image: redis:7-alpine
    networks:
      - internal_network
    healthcheck:
      test: ['CMD', 'redis-cli', 'ping']
    volumes:
      - ./redis:/data

  es:
    restart: always
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.4
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m -Des.enforce.bootstrap.checks=true"
      - "xpack.license.self_generated.type=basic"
      - "xpack.security.enabled=false"
      - "xpack.watcher.enabled=false"
      - "xpack.graph.enabled=false"
      - "xpack.ml.enabled=false"
      - "bootstrap.memory_lock=true"
      - "cluster.name=es-mastodon"
      - "discovery.type=single-node"
      - "thread_pool.write.queue_size=1000"
    networks:
       - external_network
       - internal_network
    healthcheck:
       test: ["CMD-SHELL", "curl --silent --fail localhost:9200/_cluster/health || exit 1"]
    volumes:
       - ./elasticsearch:/usr/share/elasticsearch/data
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    ports:
      - '127.0.0.1:9200:9200'

  web:
    image: ghcr.io/mastodon/mastodon:v4.2.9
    restart: always
    env_file: .env.production
    command: bash -c "rm -f /mastodon/tmp/pids/server.pid; bundle exec rails s -p 3000"
    networks:
      - external_network
      - internal_network
    healthcheck:
      # prettier-ignore
      test: ['CMD-SHELL', 'wget -q --spider --proxy=off localhost:3000/health || exit 1']
    ports:
      - '127.0.0.1:3000:3000'
    depends_on:
      - db
      - redis
      - es
    volumes:
      - ./system:/mastodon/public/system

  streaming:
    image: ghcr.io/mastodon/mastodon:v4.2.9
    restart: always
    env_file: .env.production
    command: node ./streaming
    networks:
      - external_network
      - internal_network
    healthcheck:
      # prettier-ignore
      test: ['CMD-SHELL', 'wget -q --spider --proxy=off localhost:4000/api/v1/streaming/health || exit 1']
    ports:
      - '127.0.0.1:4000:4000'
    depends_on:
      - db
      - redis

  sidekiq:
    image: ghcr.io/mastodon/mastodon:v4.2.9
    restart: always
    env_file: .env.production
    command: bundle exec sidekiq
    depends_on:
      - db
      - redis
    networks:
      - external_network
      - internal_network
    volumes:
      - ./system:/mastodon/public/system
    healthcheck:
      test: ['CMD-SHELL', "ps aux | grep '[s]idekiq\ 6' || false"]

networks:
  external_network:
  internal_network:
    internal: true
```
Como vemos, se hace referencia a un archivo .env.production que no existe. Ya mencionamos que parte de la configuración de la instancia se almacena en la base de datos. Otra parte se almacena en este archivo.

### El archivo .env.production

En la misma carpeta donde está el archivo docker-compose.yml (en este caso, la raíz del repositorio) debe haber un archivo llamado .env.production con las siguientes variables, como mínimo. La documentación de Mastodon proporciona otras variables que podemos añadir. Este es sólo un archivo de ejemplo:

```
# El contenido estático se deja en manos de Ruby y Docker
RAILS_SERVE_STATIC_FILES=true
# El nivel de registro se configura en advertencias
RAILS_LOG_LEVEL=warn
# Ajustes de Redis
REDIS_HOST=redis
REDIS_PORT=6379
# Ajustes de la base de datos
DB_HOST=db
DB_USER=postgres
DB_NAME=postgres
DB_PASS=
DB_PORT=5432
# Buscador
ES_ENABLED=true
ES_HOST=es
ES_PORT=9200
# Comienza a modificar a partir de aquí
# Dominio de la instancia.
LOCAL_DOMAIN=miinstancia.com
# Valores secretos
SECRET_KEY_BASE=secreto1
OTP_SECRET=secreto2
VAPID_PRIVATE_KEY=secreto3
VAPID_PUBLIC_KEY=secreto4
# Ajustes para enviar correos
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_LOGIN=miinstancia@gmail.com
SMTP_PASSWORD=clave_de_gmail
SMTP_FROM_ADDRESS=miinstancia@gmail.com
SMTP_OPENSSL_VERIFY_MODE=peer                                                                                           
SMTP_AUTH_METHOD=plain                                                                                                  
# Cambiar a true si la instancia sólo contendrá un usuario (instancia unipersonal)
SINGLE_USER_MODE=false
# Ajustes del traductor con DeepL
# DEEPL_API_KEY=clave-api-de-DeepL
# DEEPL_PLAN=free
# Ajustes del traductor con Libre Translate
#LIBRE_TRANSLATE_ENDPOINT=http://URL-del-traductor
#LIBRE_TRANSLATE_API_KEY=clave-de-api-opcional
```

Como se puede observar, hay varios ajustes que debemos configurar:

* Datos del servidor de correo: necesitamos una cuenta en un proveedor de correo. Gmail suele ser el más utilizado, especialmente con Google Workspace (de pago), por lo que decidimos dejar los ajustes del servidor. Esto no significa que no puedan usarse otros servidores, incluido un postfix local.
* Nombre de dominio o subdominio: una vez que lo elijamos y pongamos en marcha la instancia, ya no podrá cambiarse, así que es una decisión que debe tomarse con calma.
* Datos del traductor: el traductor es un componente opcional. Se pueden usar Libre Translate o DeepL. Descomenta las líneas del que prefieras, o no descomentes nada si no quieres traductor en tu instancia.
* Valores secretos: los generaremos más adelante.
* ¿La instancia es unipersonal? Si la respuesta es sí, esta variable debe estar a true.

### Generación de los 4 valores secretos

Es hora de iniciar uno de los contenedores y responder una serie de preguntas. El siguiente comando ejecutará un asistente interactivo y realizará los primeros preparativos. Al finalizar, imprimirá por pantalla el resultado. Copia los 4 valores secretos, y sustitúyelos en el archivo .env.production del apartado anterior: `docker compose run --rm web bundle exec rake mastodon:setup`

### Puesta en marcha y creación de la cuenta de administrador

La instancia está lista para arrancar. Y como ya vimos en una sección anterior, basta ejecutar un único comando para que todo se ponga en marcha: `docker compose up -d`

Cuando recuperamos el control de la consola, podemos ver el estado de los contenedores con `docker ps`. Tras un par de minutos, todos ellos deberían aparecer como 'healthy', que significa sano. Si alguno de ellos se reinicia continuamente o no se encuentra en buen estado, comprueba que no te has equivocado al seguir los pasos anteriores, y pregunta, no vaya a ser que yo me haya equivocado con el tutorial.

Los contenedores perdurarán y se activarán por sí solos cada vez que se reinicie el sistema. Para detener la instancia, ejecutaremos `docker compose down`

Ahora, desplegamos los índices del buscador, un paso indispensable para que funcione: `docker exec mastodon-web-1 tootctl search deploy`

Para finalizar la configuración, crearemos una cuenta de administrador. Tras ejecutar el siguiente comando, aparecerá en pantalla la contraseña de la cuenta, compuesta por 32 caracteres. Se puede cambiar más adelante desde la interfaz de administración:

`docker exec mastodon-web-1 tootctl accounts create usuario --email usuario@miinstancia.com --confirmed --role owner`

¡Todo listo! Mastodon ya está en funcionamiento. Pero aún no se puede entrar, falta mucho por hacer.

## Obtención de un certificado para nuestro dominio

No puede haber instancia de Mastodon sin un dominio, o al menos un subdominio dentro del dominio. Vamos a asumir que ya tenemos un dominio que apunta a la dirección ip de nuestro servidor, y que los puertos 80 y 443 se encuentran abiertos. Esto último es importante, ya que son necesarios para obtener el certificado.

Con el certificado, garantizaremos que la conexión a la instancia se hace por https, y por tanto que el tráfico va cifrado entre cliente y servidor. Para comenzar, instalaremos Certbot:

```
apt update && apt install certbot
```

Antes de realizar la solicitud, tal vez sea conveniente reforzar la seguridad. Sabéis que a mí me gusta mucho dejar atrás RSA, aunque rompa compatibilidad con navegadores antiguos, y cambiarlo por algo más reciente. Para hacerlo, editaremos el archivo /etc/letsencrypt/cli.ini:

`nano /etc/letsencrypt/cli.ini`

No vamos a cambiar nada de lo que se encuentra en él, pero sí agregaremos lo siguiente:

```
key-type = ecdsa
elliptic-curve = secp384r1
```

Ahora, pulsamos control+x para salir, la s o la y (según el idioma) para confirmar que queremos guardar, e intro para guardar el fichero sin cambiar su nombre.

Finalmente, solicitamos un certificado. En este ejemplo, se usa el dominio miinstancia.com: `certbot certonly --standalone -d miinstancia.com`

Si es la primera vez que usamos Let's Encrypt, tendremos que aceptar los términos del servicio, y proporcionar un correo electrónico. Los certificados caducan cada 3 meses. Cuando falten pocos días para alcanzar la fecha de caducidad, recibiremos un correo que informará de ello. Para renovar todos los certificados solicitados, basta con apagar el servidor web, ejecutar `certbot renew`, y ponerlo en marcha de nuevo.

Ahora que ya tenemos un certificado, podemos continuar con el servidor web, Apache.

## Instalación y configuración de Apache

Apache es un servidor web modular cuyo comportamiento se programa en uno o varios archivos de configuración. En cada distribución de Linux se presenta de una manera distinta. En Centos, todos los módulos vienen habilitados. En Debian y Ubuntu hay que habilitar a mano algunos para casos concretos, e incluso los hosts virtuales pueden encenderse o apagarse.

Para instalar Apache, ejecutaremos el siguiente comando:

`apt update && apt install apache2`

A continuación, habilitamos los módulos necesarios para usar SSL, redirigir a https y conectarse a los contenedores de Mastodon que funcionarán en el equipo: `a2enmod ssl proxy_wstunnel rewrite headers`

A pesar de que Apache nos lo solicite, aún no es momento de reiniciarlo. Debemos añadir y habilitar el fichero de configuración de la instancia. Por ejemplo, podemos situarlo en /etc/apache2/sites-available/miinstancia.conf. En su interior, se debe forzar el uso de https, y redirigir todas las solicitudes seguras hacia la instancia. Este podría ser un fichero de ejemplo:

```
<VirtualHost *:80>
ServerName miinstancia.com:80
<IfModule mod_rewrite.c>
  RewriteEngine on
 RewriteRule ^ - [E=protossl]
  RewriteCond %{HTTPS} on
RewriteRule ^ - [E=protossl:s]
   RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
  RewriteRule ^ http%{ENV:protossl}://%1%{REQUEST_URI} [L,R=301]
</IfModule>
</VirtualHost>
<VirtualHost *:443>
ServerAdmin correo@miinstancia.com
ServerName miinstancia.com:443
SSLEngine on
DocumentRoot /root/mastodon/public
<LocationMatch "^/(assets|avatars|emoji|headers|packs|sounds|system)">
Header always set Cache-Control "public, max-age=31536000, immutable"
Require all granted
</LocationMatch>
<Location "/">
Require all granted
</Location>
ProxyPreserveHost On
RequestHeader set X-Forwarded-Proto "https"
ProxyAddHeaders On
ProxyPass /api/v1/streaming ws://localhost:4000/api/v1/streaming
ProxyPass / http://localhost:3000/
ProxyPassReverse / http://localhost:3000/
ErrorDocument 500 /500.html
ErrorDocument 501 /500.html
ErrorDocument 502 /500.html
ErrorDocument 503 /500.html
ErrorDocument 504 /500.html
CustomLog logs/ssl_request_log \
          "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"
BrowserMatch "MSIE [2-5]" \
         nokeepalive ssl-unclean-shutdown \
         downgrade-1.0 force-response-1.0
SSLProxyEngine on
<IfModule mod_headers.c>
Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
</IfModule>
SSLHonorCipherOrder off
SSLCipherSuite ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
SSLProxyCipherSuite ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
SSLCertificateFile /etc/letsencrypt/live/miinstancia.com/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/miinstancia.com/privkey.pem
SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1
SSLProxyProtocol all -SSLv3 -TLSv1 -TLSv1.1
SSLSessionTickets       on
SSLUseStapling on
SSLStrictSNIVHostCheck on
</VirtualHost>
```

Para completar la configuración de Apache, habilitamos el nuevo fichero y reiniciamos:

`a2ensite miinstancia`
`systemctl restart apache2`

¡Servidor listo! Si intentamos entrar desde el exterior, observaremos que la conexión es segura, y podremos acceder a la instancia. La cuenta de administrador creada al principio permitirá personalizar un montón de aspectos desde la web. Pero eso es algo que dejamos para otro día. ¡Feliz federación!

## Algo de protección extra: inclusión del dominio en la lista de precarga

Al conectar a una web que fuerza conexiones seguras, como la que acabamos de construir, la mayoría de los usuarios escribirán su nombre en la barra de direcciones, sin anteponer el prefijo https. El navegador primero accederá por http estándar, y acabará siendo redirigido. Si bien esto no supone un fallo muy serio de seguridad, le puede dar una pista a un potencial espía de la URL exacta a la que queremos ir. Para evitarlo, los navegadores incluyen listas de precarga con dominios conocidos. Cuando un dominio está en la lista de precarga, el navegador inicia directamente una conexión segura con él.

Si quieres que tu dominio esté en esa lista, sigue estos pasos:

1. Accede a la web https://hstspreload.org
2. En el primer cuadro de edición, que automáticamente recibe el foco, escribe el nombre de tu dominio. No se admiten subdominios.
3. Si se superan todos los requisitos, marca la casilla de aceptación y envía el formulario.
4. Sigue los pasos 1 y 2 para comprobar el estado de precarga del dominio cada pocos días. Tardará varias semanas en estar listo, pero no dejes que esto te impida usar tu instancia con normalidad y anunciarla. Como se menciona en el título de la sección, es un extra que no interfiere.

Esto es todo por ahora. ¡Gracias por leer! Espero ver nuevas instancias pronto!