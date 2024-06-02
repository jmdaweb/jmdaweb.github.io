---
title: "Creación de redes seguras con OpenVPN"
date: 2024-06-02T18:43:45+02:00
reply:
uri: /posts/openvpn/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

OpenVPN es un software que nos permite construir redes vpn seguras, aprovechando los estándares de cifrado TLS y los diversos algoritmos ofrecidos por OpenSSL y bibliotecas similares. En este tutorial veremos cómo configurar un servidor, y añadir algunos clientes. Además, a diferencia de otros tutoriales disponibles en la red, intentaremos mejorar la seguridad y nos centraremos en construir una subred a la que varios equipos puedan conectarse para interactuar entre sí.

## 1. Requisitos

* Un servidor con Debian instalado y acceso como root. Asumiremos que estamos usando Debian 12.
* Conocimientos suficientes para entender lo que aparece escrito por consola, ejecutar comandos básicos y editar ficheros.
* Leves nociones sobre arquitectura de redes: direcciones ip, máscara de subred, etc.
* Saber cuál es la dirección ip pública del servidor o, si la tiene, su ip dentro de la red local. En este tutorial asumimos que la ip del servidor es 192.168.1.2. Puedes conocer tu dirección ip con la utilidad ifconfig.
* Saber cuál es el nombre del adaptador de red que tiene acceso a Internet. Puede ser eth0, o tener un nombre más complejo si el servidor es físico. Asumimos enp5s0f0. Puedes conocer los nombres de tus adaptadores con la utilidad ifconfig.

## 2. Instalación de OpenVPN en el servidor

Para instalar la versión estable más reciente, se recomienda acudir a los repositorios de OpenVPN, en vez de descargar el paquete ofrecido por nuestra distribución. Ejecutaremos los siguientes comandos en orden:

* Agregamos la clave gpg de los repositorios: `curl -fsSL https://swupdate.openvpn.net/repos/openvpn-repo-pkg-key.pub | gpg --dearmor > /etc/apt/trusted.gpg.d/openvpn-repo-pkg-keyring.gpg`
* Agregamos las URLs de los repositorios al sistema: `echo "deb [arch=amd64 signed-by=/etc/apt/trusted.gpg.d/openvpn-repo-public.gpg] http://build.openvpn.net/debian/openvpn/stable bookworm main" > /etc/apt/sources.list.d/openvpn-aptrepo.list`
* Refrescamos el índice de paquetes: `apt update`
* Y finalmente, instalamos los paquetes que nos interesan: `apt install openvpn openvpn-dco-dkms`
* Podemos instalar una serie de paquetes que nos vendrán bien más adelante, especialmente si queremos saber los nombres de adaptadores de red y las direcciones ip: `apt install net-tools iptables iptables-persistent`
* El paquete net-tools contiene la utilidad ifconfig, y el resto de paquetes permiten gestionar el cortafuegos del sistema incluido en el kernel.

## 3. Configuración de OpenVPN como servidor

OpenVPN es un software que se ha diseñado para ejecutarse tantas veces como sea necesario. Podemos tener uno o varios clientes conectados a una o varias redes de terceros, y uno o varios servidores ofreciendo redes de distintos tipos a nuestros clientes. En este tutorial, sólo montaremos un servidor dentro del sistema Debian. Más adelante, haremos una ampliación para que haya dos servidores. El resto de clientes irán en equipos independientes.

### 3.1. Creación de una autoridad de certificación propia

Para cifrar el tráfico entre todos los componentes de la red y verificar la autenticidad de la misma, trabajaremos con certificados de cliente y servidor. Dichos certificados irán firmados por una autoridad de certificación propia. Podríamos recurrir a una de terceros, pero no nos va a dar tanta flexibilidad, y el resultado va a ser el mismo.
OpenVPN viene con easy-rsa, un conjunto de scripts que facilitan la creación y gestión de autoridades y certificados. Usaremos easy-rsa para generar la autoridad, un certificado para el servidor, y tantos certificados extra como clientes vayan a conectarse. En este caso, se entiende por cliente el dispositivo, no la persona. Ya entraremos más adelante en el terreno de la autentificación con usuario y contraseña.
Nuestra autoridad de certificación se aloja dentro de una carpeta, por ejemplo /etc/openvpn/certs. La generaremos con el siguiente comando: `make-cadir /etc/openvpn/certs`
Los scripts de easy-rsa se copiarán dentro de la carpeta recién creada. Por defecto, generarán certificados y claves RSA. Si bien es cierto que a día de hoy son suficientemente seguros, nos interesa cambiarlos por ecdsa por diversas razones: en el futuro escalarán mejor, y requieren menos potencia de procesamiento para ofrecer un cifrado igual de fuerte. Por lo tanto, vamos a editar el archivo /etc/openvpn/certs/vars y añadir lo siguiente al final (o seguir los consejos del propio archivo y quitar los comentarios en las líneas apropiadas):
```
set_var EASYRSA_ALGO		ec
set_var EASYRSA_CURVE		secp384r1
set_var EASYRSA_DIGEST		"sha512"
```
De paso, hemos aprovechado para indicar que se use sha512 en vez de sha256 al calcular la suma de verificación de los certificados.
Ahora, navegamos al directorio en cuestión, si no lo habíamos hecho ya: `cd /etc/openvpn/certs`
El siguiente paso consiste en inicializar la infraestructura de clave pública, utilizando nuestro fichero de variables previamente editado: `./easyrsa --vars=/etc/openvpn/certs/vars init-pki`
Generamos nuestra autoridad de certificados raíz: `./easyrsa --vars=/etc/openvpn/certs/vars build-ca nopass`
La opción nopass hará que la clave privada no se cifre con una contraseña, lo que aporta cierta comodidad al generar certificados más adelante. Si queremos cifrarla e introducir la contraseña cada vez que usemos la autoridad, basta con quitar la opción.
Para completar la generación de la autoridad, rellenamos los datos solicitados por el asistente interactivo.
Generamos el fichero de parámetros de cifrado, siempre indicando qué variables usar: `./easyrsa --vars=/etc/openvpn/certs/vars gen-dh`
Ya tenemos nuestra autoridad lista para generar certificados de servidor y clientes. El certificado de la autoridad, necesario más adelante, está en el archivo ca.crt dentro de la carpeta pki.

### 3.2. Generación de un certificado de servidor

El certificado de la autoridad de certificación sólo se usará para firmar otros certificados, pero no cifrará las conexiones entre los clientes y el servidor. Aprovechando nuestra autoridad, vamos a generar uno específico. En esta ocasión, no es recomendable cifrar la clave privada con contraseña:
`./easyrsa --vars=/etc/openvpn/certs/vars build-server-full midominio.com nopass`
Si aparece un asistente interactivo, lo rellenamos con la información solicitada. Todos los certificados se almacenan en la subcarpeta issued dentro de la carpeta pki, y todas las claves privadas en la subcarpeta private.

### 3.3. Listas de revocación y clave extra de cifrado

A veces, el certificado de un cliente puede verse comprometido, por lo que puede hacerse necesario revocar su validez. Nuestra infraestructura de clave pública debe mantener una lista actualizada con aquellos certificados que se han revocado. Debemos ejecutar el siguiente comando después de generar, renovar y revocar cualquier certificado, ya sea de servidor o de cliente:
`./easyrsa --vars=/etc/openvpn/certs/vars gen-crl`
Ahora, vamos a hacer algo que le va a dar más cifrado al cifrado. Si bien es cierto que un atacante que interceptara nuestras comunicaciones lo tendría ya muy complicado para saber lo que hacemos, puede deducir que nuestro tráfico va cifrado mediante TLS. Con la clave que generaremos a continuación, ofuscaremos el tráfico TLS para que no parezca tráfico TLS:
`openvpn --genkey tls-crypt-v2-server tls-server-key.key`
Con todo esto, estamos preparados para elaborar un fichero de configuración de OpenVPN.

### 3.4. El fichero de configuración de OpenVPN

Como hemos mencionado anteriormente, OpenVPN puede ejecutar tantas instancias de cliente y servidor como sea necesario. Cada instancia usa su propio fichero de configuración. Para facilitar la diferenciación entre clientes y servidores, los ficheros de cliente se almacenan en /etc/openvpn/client, y los ficheros de servidor en /etc/openvpn/server. Los ficheros que se usan para configurar OpenVPN en modo conexión punto a punto pueden ir fuera de estas carpetas. Sin embargo, todos deben tener la extensión .conf. A continuación, vamos a crear un fichero en /etc/openvpn/server. Lo llamaremos servidor.conf. Encima de cada línea, hay comentarios explicando su propósito.
```
# Indicamos cuál es la ip del servidor, ya sea pública o dentro de una red de área local
local 192.168.1.2
# Puerto en el que escucha el servidor. Debe estar abierto en el router o firewall de la NAT, si lo hay, así como en el firewall del sistema
port 1194
# Dispositivo que se utilizará. El más sencillo es tun. Por su parte, tap proporciona una integración con la lan a más bajo nivel, pero su configuración es mucho más compleja
dev tun
# Parámetros del servidor: formato de la subred y máscara de subred. Los clientes que se conecten recibirán una ip en este rango. Cuidado, la subred de la VPN no debe coincidir con la subred del servidor. De lo contrario, podemos perder el contacto con la máquina.
server 10.0.0.0 255.255.255.0
# Protocolo de conexión. Se pueden usar TCP o UDP. Parecería que TCP es más estable, pero UDP ya tiene mecanismos equivalentes para evitar errores. Por tanto, TCP es más lento y sólo debería emplearse cuando no se pueda recurrir a UDP.
proto udp
# Modo rápido de entrada y salida de datos, puede acelerar las comunicaciones. Sólo en UDP
fast-io
# Las claves persisten en memoria y no se vuelven a leer al recibir señales del sistema
persist-key
# El túnel permanece abierto aunque se reciban ciertas señales del sistema
persist-tun
# cantidad de información que se almacena en el registro
verb 3
# Ruta al archivo de registro de esta instancia
log /var/log/openvpn/openvpn.log
# Reforzamos el algoritmo de autentificación
auth SHA512
# Si la instancia se desconecta, notifica a las partes conectadas para que actúen en consecuencia. Muy eficiente en UDP
explicit-exit-notify 1
# Cuando se complete la autentificación, se guardará un token en memoria para no tener que repetirla tras una caída
auth-gen-token
# Los clientes conectados podrán verse unos a otros. Útil, por ejemplo, para jugar a juegos en línea
client-to-client
# Las direcciones ip de los clientes se almacenarán en este fichero para no recibir una nueva dirección en cada conexión
ifconfig-pool-persist /var/log/openvpn/ip.txt
# Cantidad máxima de clientes que se conectarán a la red. No debería haber más de 250
max-clients 100
# Ruta al certificado de la autoridad que generamos al principio
ca /etc/openvpn/certs/pki/ca.crt
# Ruta al certificado del servidor
cert /etc/openvpn/certs/pki/issued/midominio.com.crt
# Ruta a la clave privada del servidor
key /etc/openvpn/certs/pki/private/midominio.com.key
# Ruta al fichero de lista de revocación de la autoridad
crl-verify /etc/openvpn/certs/pki/crl.pem
# Ruta a los parámetros dh generados
dh /etc/openvpn/certs/pki/dh.pem
# Los certificados remotos enviados por los clientes que se conecten deben ser de tipo cliente
remote-cert-tls client
# Reforzamos los algoritmos de cifrado de la conexión. Sólo se admitirán los más recomendados, descartando los que se consideran más débiles
tls-cert-profile preferred
# Ruta a la clave de ofuscación del tráfico
tls-crypt-v2 /etc/openvpn/certs/tls-server-key.key
# Indicamos explícitamente que actuaremos como servidor TLS
tls-server
# La versión mínima del protocolo TLS será la 1.2
tls-version-min 1.2
# Tiempo que permanecerán abiertas las conexiones
keepalive 10 120
# Forzamos explícitamente que esta instancia actúe como servidor
mode server
# Indicamos que su topología es de subred
topology subnet
# Transmitimos a los clientes directivas de configuración. Todo el tráfico irá dirigido por la VPN
push "redirect-gateway"
# Nuestro router, que incluye un servidor DNS, actuará como DNS
push "dhcp-option DNS 192.168.1.1"
# Algoritmos de cifrado de transmisión de datos
data-ciphers AES-256-GCM:AES-128-GCM:CHACHA20-POLY1305
# Además de la autentificación de cliente con certificado, también se debe introducir un usuario y una contraseña
plugin /usr/lib/openvpn/openvpn-plugin-auth-pam.so "login login USERNAME password PASSWORD"
```

### 3.5. Cambios de configuración en el kernel y retoques finales

Ahora que ya tenemos un fichero de configuración completo, estamos listos para iniciar el servidor VPN. Podemos hacerlo con este comando: `systemctl start openvpn-server@servidor`
Lo que va después del símbolo arroba es, como se puede deducir, el nombre del fichero de configuración, sin la extensión .conf.
Con este otro comando, configuraremos el servidor para que arranque al iniciar el sistema: `systemctl enable openvpn-server@servidor`
Pero ahora nos enfrentamos a un problema: esta red va a dejar a los clientes sin acceso a Internet. Podrán verse unos a otros e interactuar entre sí, pero no podrán usar el servidor para comunicarse con el exterior. Para resolverlo, editaremos el fichero /etc/sysctl.conf o, si es posible, crearemos un fichero .conf nuevo en /etc/sysctl.d con el nombre que queramos. Lo único que se debe hacer, independientemente de la opción elegida, es descomentar o escribir la siguiente línea y guardar los cambios:
```
net.ipv4.ip_forward=1
```
Podemos reiniciar el servidor para que los cambios surtan efecto, o ejecutar el comando `sysctl -p` para aplicarlos de inmediato.
Esto, sin embargo, no es suficiente. Hay que indicar a iptables que el tráfico recibido de la red interna se dirija a la red externa. Si prefieres usar UFW, puedes hacerlo. El comando con iptables sería el siguiente:
```
# Recuerda ajustar tu subred y tu adaptador con los valores que correspondan
iptables -t nat -A POSTROUTING -s 10.0.0.0/8 -o enp5s0f0 -j MASQUERADE
```
Perfecto, nuestros clientes ya podrán acceder a Internet usando la VPN, pero sólo hasta que el servidor se reinicie! Después, habrá que ejecutar el comando iptables otra vez. UFW se encarga de guardar la configuración para evitar esta situación. Sin UFW, se puede usar un comando como este para guardar las reglas actuales en disco y configurarlas para que carguen al arrancar:
`netfilter-persistent save`

## 4. Configuración de los clientes

¿De qué serviría un servidor si los clientes no pueden conectarse a él? Es exactamente lo que sucede con la instancia de OpenVPN que acabamos de poner en marcha. En esta sección, vamos a preparar dos clientes para una misma persona. Uno de ellos será un ordenador con Windows, y otro un iPhone.

### 4.1. Generación de los certificados de cliente

Supongamos que el propietario de los dos dispositivos se llama Pedro. Vamos a generar dos certificados, dos claves privadas, y dos claves de ofuscación de tipo cliente para él. En la consola, debemos navegar al directorio /etc/openvpn/certs, si es que nos habíamos salido de él. Una vez allí, ejecutamos los siguientes comandos:
`./easyrsa --vars=/etc/openvpn/certs/vars build-client-full pedro-windows nopass inline`
`./easyrsa --vars=/etc/openvpn/certs/vars build-client-full pedro-iphone nopass inline`
Si aparecen asistentes interactivos solicitando información, debemos rellenarla. La opción nopass indica que las claves privadas no deben ir cifradas con contraseña. La opción inline genera un fichero de credenciales con el certificado y la clave, que podremos usar como punto de partida para construir el fichero que recibirá el cliente. En este caso, nuestros ficheros de credenciales son /etc/openvpn/certs/pki/pedro-windows.creds y pedro-iphone.creds en la misma ruta.
Importante refrescar la lista de revocación en cuanto hayamos terminado esta parte.
Para generar las claves de ofuscación de los clientes, necesitaremos hacer referencia a la clave de ofuscación del servidor. Los comandos quedarían de una forma similar a esta:
`openvpn --tls-crypt-v2 /etc/openvpn/certs/tls-server-key.key --genkey tls-crypt-v2-client tls-client-pedro-windows.key`
`openvpn --tls-crypt-v2 /etc/openvpn/certs/tls-server-key.key --genkey tls-crypt-v2-client tls-client-pedro-iphone.key`

### 4.2. Generación de una cuenta para el cliente

Además de un certificado único, una clave privada única y una clave de ofuscación única, podemos reforzar la seguridad obligando a los clientes a que introduzcan un nombre de usuario y una contraseña. Esto, normalmente, se consigue mediante plugins. OpenVPN viene con un plugin que nos permite usar las cuentas del sistema, y que ya agregamos mientras preparábamos el servidor. Existen algunos plugins más, pero suelen ser de pago y quedan fuera de este tutorial.
Agreguemos una cuenta de usuario para Pedro: `useradd pedro`
Y ahora, démosle una contraseña: `passwd pedro`
Ya está todo preparado para que Pedro se conecte, salvo una cosa: Pedro necesita dos archivos de configuración que no tiene.

### 4.3. Generación de los archivos de configuración

A pesar de que OpenVPN se puede configurar con un archivo conf y certificados en diferentes rutas, como hemos hecho en el servidor, es más cómodo para los clientes tener un único fichero con toda la información necesaria para conectarse. Como dijimos antes, los ficheros de credenciales son un buen punto de partida. En este apartado veremos un archivo de configuración de ejemplo con todo lo necesario. Deberemos crear un archivo por cada dispositivo, y deben tener la extensión .ovpn.
```
# Utilizaremos el protocolo UDP
proto udp
# Con entrada y salida de datos rápida
fast-io
# Al igual que en el servidor, la clave y el túnel persisten a pesar de las señales del sistema que se reciban
persist-key
persist-tun
# Servidor y puerto al que nos conectaremos
remote midominio.com 1194
# Asumimos el rol de cliente en la VPN
client
# Y lo mismo al cifrar las comunicaciones con TLS
tls-client
# Reforzamos el algoritmo de autentificación
auth SHA512
# Antes de desconectarnos, notificaremos al servidor para que libere recursos de forma eficiente
explicit-exit-notify 1
# Sólo se acepta el certificado TLS si este es de tipo servidor
remote-cert-tls server
# La versión mínima soportada de TLS será la 1.2
tls-version-min 1.2
# El servidor necesita autentificación con usuario y contraseña
auth-user-pass
# El usuario y la contraseña, por seguridad, no se almacenan en memoria una vez se han usado
auth-nocache
# Tipo de dispositivo de red
dev tun
# Autoridad certificadora
<ca>
Contenidos del fichero /etc/openvpn/certs/pki/ca.crt. Viene en el fichero de credenciales.
</ca>
# Certificado del cliente
<cert>
Contenidos del archivo /etc/openvpn/certs/pki/issued/pedro-windows.crt o pedro-iphone.crt, según corresponda. Viene en el fichero de credenciales.
</cert>
# Clave privada del cliente
<key>
Contenidos del fichero /etc/openvpn/certs/pki/private/pedro-windows.key o pedro-iphone.key, según corresponda. Viene en el fichero de credenciales.
</key>
# Clave de ofuscación
<tls-crypt-v2>
Contenidos del archivo de clave de ofuscación del cliente, según corresponda. No viene en el fichero de credenciales.
</tls-crypt-v2>
```
Ahora ya tenemos todo lo necesario para que el cliente se conecte: un archivo de configuración, un usuario y una contraseña. Podrá entrar en la VPN, interactuar con los demás clientes a través de ella, y visitar sitios web dificultando que los atacantes espíen su tráfico. Sin embargo, no podríamos finalizar este tutorial sin mencionar los principales clientes.

## 5. OpenVPN y OpenVPN Connect

OpenVPN, aunque es software libre y acepta colaboraciones de la comunidad, dispone de una versión de pago con opciones avanzadas para empresas. Una de sus soluciones, OpenVPN Access Server, facilita la gestión de certificados, cuentas de usuario y distribución de configuraciones de cliente, y admite dos usuarios totalmente gratis. Por otro lado, si no disponemos de servidor propio, se puede usar la nube de OpenVPN, y establecer una conexión a través de sus propias redes. Ofrece tres conexiones gratuitas.
En cuanto a clientes, en la web se hace una diferenciación: por un lado están los de la comunidad, y por otro [OpenVPN Connect](https://openvpn.net/client-connect-vpn-for-windows/), más orientado al mundo empresarial. En realidad, tienen características muy parecidas y funcionan de manera similar. En Windows, OpenVPN Connect está creado con una interfaz Electron que, por algún motivo desconocido, resulta casi imposible de manejar con lectores de pantalla. Por tanto, es mejor el cliente libre: https://openvpn.net/community-downloads/
La interfaz de este segundo cliente está en español y utiliza controles clásicos. Desde su icono en la bandeja del sistema y su ventana podremos hacer las principales operaciones: importar archivos de configuración, conectarnos e introducir el nombre de usuario y la contraseña. Por si fuera poco, también facilita la gestión de archivos .ovpn en el explorador de Windows. Dispone de opciones más avanzadas, y se puede usar para levantar un servidor en Windows, pero eso queda fuera de este tutorial.
En las plataformas móviles, OpenVPN Connect se vuelve más manejable, y es la única alternativa que tenemos. Se puede descargar para [iPhone](https://apps.apple.com/us/app/openvpn-connect/id590379981) y [Android](https://play.google.com/store/apps/details?id=net.openvpn.openvpn). Puede importar archivos de configuración desde una URL, y puede aceptar archivos compartidos desde otras aplicaciones. Por el momento, no es capaz de importar archivos almacenados en el teléfono desde la propia app, al menos en iPhone.

## 6. Anexo: creación de una red de puente

Este apartado está pensado sólo para servidores Linux caseros (tipo Raspberry Pi) y clientes Windows. Al profundizar como vamos a hacer, las diferencias entre plataformas ya comienzan a notarse y hacen que todo se vuelva más complejo.
A lo largo de este tutorial hemos creado una VPN que nos permite redirigir nuestro tráfico de Internet, conectarnos con otros dispositivos e intercambiar información de manera segura como si todos estuvieran en la misma red. Sin embargo, hay paquetes de red a bajo nivel que no pasan por la VPN, y nos pueden servir para tareas tales como buscar y conectarnos a dispositivos que se encuentran en la red de casa, o simplemente desviar esos paquetes también por nuestra red. Con las mejoras que aplicaremos a continuación, los dispositivos que tenemos en casa podrán vernos y conectarse como si nuestro equipo estuviera ahí, y será el router el encargado de asignarnos una dirección IP.
Para conservar la implementación hecha hasta aquí, trabajaremos sobre un nuevo archivo de configuración creado a partir del que ya tenemos. Lo llamaremos servidor-tap:
`cp /etc/openvpn/server/servidor.conf /etc/openvpn/server/servidor-tap.conf`
Ahora, editamos el fichero servidor-tap.conf y realizamos los siguientes cambios:

* Cambiamos el puerto 1194 por otro, por ejemplo el 1195: `port 1195`
* Eliminamos la línea `dev tun`, ya que usaremos otro dispositivo. En su lugar, escribiremos `dev tap0`
* Eliminamos la línea que comienza por server, ya que no definiremos una subred propia. En su lugar, escribimos esto: `server-bridge`
* Podemos tener un registro independiente para esta nueva instancia: `log /var/log/openvpn/openvpn-tap.log`
* Eliminamos la línea que comienza por ifconfig-pool-persist. Ahora el encargado de asignarnos dirección IP no es OpenVPN.
* Eliminamos la línea que comienza por push "dhcp-option DNS..., ya que será el router quien nos indique los servidores DNS.

Podemos reutilizar un fichero de cliente en Windows. Los cambios son bastante más simples en este caso. Basta con sustituir la línea `dev tun` por `dev tap`.

Para acabar, generaremos y ejecutaremos un script en el servidor. Dicho script permitirá iniciar y detener el puente de red que usará el servidor VPN en modo tap. Se debe configurar con mucho cuidado, especialmente si trabajamos por ssh. Cualquier descuido nos puede dejar sin red hasta el reinicio del dispositivo. El script que se proporciona a continuación construye el puente, modifica iptables para que la VPN de apartados anteriores siga funcionando, y pone en marcha la nueva si se llama con el argumento start. Si se llama con el argumento stop, deshace todos los cambios que ha hecho. Podemos alojarlo en el archivo `/usr/local/bin/openvpn-bridge`. Antes de probarlo, se deben modificar las primeras líneas y adaptarlas a nuestras necesidades:

```
#!/bin/sh

# Interfaz de puente. Se puede dejar la que viene por defecto
br="br0"

# Lista de interfaces tap que participan en el puente,
# por ejemplo tap="tap0 tap1 tap2".
# Se puede dejar la que viene
tap="tap0"

# Interfaz de red real del sistema
# Mismo nombre de adaptador que en el tutorial
eth="enp5s0f0"
# Ip local del servidor y máscara de subred en formato abreviado
eth_ip_netmask="192.168.1.2/24"
# Ip de broadcast. Suele ser la 255 de la subred
eth_broadcast="192.168.1.255"
# Puerta de enlace. Suele coincidir con la IP del router
eth_gateway="192.168.1.1"
# MAC del adaptador de red del servidor. Se la asignaremos al adaptador de puente para que el router no note la diferencia
eth_mac="1a:2b:3c:4d:5e:6f"

case "$1" in
start)
    for t in $tap; do
        openvpn --mktun --dev $t
    done

    brctl addbr $br
    brctl addif $br $eth

    for t in $tap; do
        brctl addif $br $t
    done

    for t in $tap; do
        ip addr flush dev $t
        ip link set $t promisc on up
    done

    ip addr flush dev $eth
    ip link set $eth promisc on up

    ip addr add $eth_ip_netmask broadcast $eth_broadcast dev $br
    ip link set $br address $eth_mac
    ip link set $br up

    ip route add default via $eth_gateway
    systemctl start openvpn-server@servidor-tap
# Adaptar subred si es necesario
    iptables -t nat -D POSTROUTING -s 10.0.0.0/8 -o $eth -j MASQUERADE
    iptables -t nat -A POSTROUTING -s 10.0.0.0/8 -o $br -j MASQUERADE
    ;;
stop)
    systemctl stop openvpn-server@servidor-tap
# Adaptar subred si es necesario
    iptables -t nat -D POSTROUTING -s 10.0.0.0/8 -o $br -j MASQUERADE
    iptables -t nat -A POSTROUTING -s 10.0.0.0/8 -o $eth -j MASQUERADE
    ip link set $br down
    brctl delbr $br

    for t in $tap; do
        openvpn --rmtun --dev $t
    done

    ip link set $eth promisc off up
    ip addr add $eth_ip_netmask broadcast $eth_broadcast dev $eth

    ip route add default via $eth_gateway
    ;;
*)
    echo "Usage:  openvpn-bridge {start|stop}"
    exit 1
    ;;
esac
exit 0
```
Ahora que toda la información está en el script, lo convertimos en ejecutable: `chmod +x /usr/local/bin/openvpn-bridge`
Y para terminar, ponemos en marcha nuestra nueva VPN basada en puente de red: `openvpn-bridge start`
Para detener el puente y devolver la red a su estado anterior: `openvpn-bridge stop`
De momento, no se dan instrucciones para que el puente se ponga en marcha al iniciar el sistema. Tal vez lo hagamos en una próxima revisión de este tutorial.
Tras completar este apartado, tenemos no uno, sino dos servidores VPN. El primero podemos compartirlo con invitados de confianza que dispongan de una amplia variedad de dispositivos. El segundo creará una relación más profunda y cercana con nuestra red y sus dispositivos, y funcionará sólo con clientes que tengan Windows.

## 7. Referencias

He tenido que leer diversos tutoriales en la red para hacerme una idea de la configuración que más se ajustaba a lo que quería. Y como siempre, me olvido de quién los ha escrito y de su dirección. Sin embargo, no me baso en ellos para escribir los míos propios, sino en las fuentes de información que acompañan al producto:

* Página de manual de OpenVPN, disponible en /usr/share/doc/openvpn/openvpn.8.html. Existen versiones equivalentes en línea, pero ninguna tan actualizada como la que acompaña al paquete.
* Por qué deberías usar certificados ECC en vez de RSA: https://www.thesslstore.com/blog/you-should-be-using-ecc-for-your-ssl-tls-certificates/
* La ayuda de easy-rsa, que se puede obtener con el comando `./easyrsa help`.
* Las páginas de documentación de OpenVPN, en las que se explica, entre otras cosas, cómo agregar el repositorio para Debian y ciertos conceptos de red que pueden no quedar claros al principio.
* Y mucha, mucha paciencia y pruebas, en las que ha habido no pocos errores.

¡Muchas gracias por leer hasta aquí! Si tienes un servidor VPS, ahora te toca a ti.