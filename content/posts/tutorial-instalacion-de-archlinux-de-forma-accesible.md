---
title: "Tutorial: Instalación de Arch Linux de forma accesible"
date: 2024-06-02T15:10:00+02:00
reply:
uri: /posts/tutorial-instalacion-de-archlinux-de-forma-accesible/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

Hola a todos.
En esta entrada vengo con un tutorial muy especial, un tutorial que lleva años en un archivo en mi disco duro y que ha evolucionado muchísimo a lo largo del tiempo. Empecé haciéndolo para publicarlo en una web de cuyo nombre no quiero acordarme, después lo conservé y lo actualicé para mí, para no olvidar lo que en él se cuenta, y ahora he vuelto a adaptarlo para publicar una vez más. Esta vez no hablaremos de un programa, servicio o protocolo, sino de un sistema operativo completo. Un sistema que tal vez no esté preparado para sustituir a Windows todavía, o tal vez sí. Eso dejaremos que lo decida cada uno después de leer y probar por sí mismo.
Antes de entrar en faena, pido perdón a los que saben más de Linux que yo y lo usan diariamente, pues estoy seguro de que este documento no está exento de errores y suposiciones que a lo mejor en realidad resultan de otra manera. Lo he hecho lo mejor que he podido, esforzándome para que los usuarios con menos conocimientos entiendan cuál es el propósito de cada comando, paquete o ajuste realizado. Dicho todo esto, ¡empezamos!
## Introducción
En este tutorial vamos a instalar Arch Linux, una distribución ligera, simple y flexible de Linux, y lo vamos a hacer de forma accesible en una máquina virtual, con instrucciones para hacerlo también en un equipo físico o preparar un pen drive que se pueda conectar a cualquier ordenador. Al acabar, y si se siguen bien todos los pasos del tutorial, Arch Linux debería estar ejecutándose en español, con varios escritorios y lector de pantalla .
## ¿Qué caracteriza a esta distribución?
Arch Linux se caracteriza por ser una distribución pequeña, potente, simple y flexible. No se suele usar en entornos de producción, ya que actualiza los paquetes en cuanto son publicados por sus respectivos desarrolladores, y esto puede generar cierta inestabilidad aunque hayan sido calificados como estables. Viene muy bien para desarrollar y hacer pruebas de todo tipo, y para conocer Linux en toda su extensión. Arch no tiene herramientas que automaticen cosas tales como la instalación del sistema o la configuración de ciertos paquetes, y por tanto hay que operar a bajo nivel.
Otra característica por la que Arch destaca es el Arch User Repository (aur), que contiene software en forma de código fuente, así como archivos de configuración para poder compilarlo sin errores, de forma automatizada y con las dependencias necesarias.
[Visitar el sitio web de Arch Linux](https://www.archlinux.org)
## Requisitos de software
Para poder hacer todos los pasos descritos en este tutorial, se ha utilizado el siguiente software. Hay programas alternativos que hacen las mismas funciones, pero no se han probado a la hora de hacer este proceso y podrían dar malos resultados:

* (Sólo si virtualizas) VMWare Workstation Pro, versión 17.5.2 o posterior.
* Si planeas conectarte remotamente: NVDA, versión 2024.1 o posterior
* La [Imagen iso más reciente de Arch Linux](https://archlinux.org/download/)

## Conocimientos necesarios
Para no tener problemas durante la instalación de Arch, hay que disponer de los siguientes conocimientos:

* Manejo del OCR, la navegación por objetos y los comandos de revisión de NVDA (sólo en máquina virtual y con acceso SSH).
* Experiencia a la hora de crear y configurar máquinas virtuales, ya que en este tutorial no se explicará nada sobre ello.
* Tener experiencia manejando algún intérprete de línea de comandos, ya sea cmd o bash, y comprender los resultados devueltos por la ejecución de un comando.

## Preparación del equipo y el medio de instalación
Bien, teniendo todas las herramientas citadas en el apartado anterior, podemos comenzar.
### Creación de la máquina virtual
Para este tutorial se ha utilizado una máquina virtual con las siguientes características:

* Memoria ram: 4 GB, aunque se puede reducir bastante, incluso hasta 512 MB
* Procesador: Se han utilizado 6 procesadores con 2 cores y virtualización de IOMU, pero la máquina puede ejecutarse sin problema con mucha menos potencia.
* Disco duro: 32 GB NVMe, dividido en varios archivos y con expansión dinámica
* red: Para poder trabajar con la máquina independientemente del lugar en el que nos encontremos, se ha usado nat. Se puede poner en bridged para situar la máquina en la misma subred que el resto de equipos físicos, o añadir un adaptador bridged más adelante.
* Tipo de bus: paravirtualized SCSI. Se puede elegir sin problema cualquiera de los otros.
* Compatibilidad del hardware: Workstation 17.5.x, aunque las anteriores tampoco deberían dar problemas.
* Teclado mejorado.
* Controlador USB 3.1.
* Aceleración de gráficos.
* Resto de parámetros: Se pueden dejar por defecto. En el asistente de creación diremos que instalaremos el sistema más tarde, y que es un Linux de tipo Other Linux kernel 6.x 64-bit. No olvidemos apuntar la unidad de cd al archivo iso del sistema operativo.

A continuación, en las opciones avanzadas de la máquina virtual, pestaña Options, categoría Advanced, podremos elegir el tipo de firmware. Elige BIOS si quieres emular un pc tradicional, o Uefi si deseas un firmware más moderno. La forma de particionar e instalar el gestor de arranque varían.

### Preparación del medio de instalación
Al arrancar la máquina virtual por primera vez, se escucharán dos tonos ascendentes. Dichos tonos indican que el menú de arranque de Isolinux está en pantalla, listo para elegir una opción. Entraremos en la máquina, pulsaremos flecha abajo y a continuación enter.
Si tomamos instantáneas con el OCR, veremos que el sistema se va cargando, se montan los sistemas de archivos, se inicia la red, etc.
Por último, escucharemos a Speakup hablando, veremos que root ha iniciado sesión automáticamente, y que la consola está lista para escribir comandos. Es en este momento cuando entraremos dentro de la máquina.
Lo primero que se debe hacer es poner el teclado en español. Esto se hace llamando a loadkeys, que recibe como parámetro un archivo de mapa de teclado. En nuestro caso:
`loadkeys es`
Y ya estamos listos para escribir comandos.
#### Personalización de la voz de Speakup
Speakup comenzará hablando en inglés, algo que puede ser muy incómodo si no somos angloparlantes. Para cambiar el idioma de la voz, deberemos editar el archivo que carga el servicio del lector de pantalla. Usaremos un comando como este para hacerlo:
`nano /lib/systemd/system/espeakup.service`
La consola cambiará radicalmente al ejecutar este comando, mostrando el editor de textos nano. Podemos usar las flechas para movernos por el texto, NVDA y Speakup anunciarán correctamente nuestra posición. 
Usando las flechas, buscaremos una línea con el siguiente contenido:
`environment="default_voice="`
Y la completaremos del siguiente modo:
`environment="default_voice=es"`
Para guardar los cambios, pulsamos control+x, la y para confirmar, y enter. Después, ejecutamos estos dos comandos para recargar la unidad de servicio modificada y reiniciarlo:
`systemctl daemon-reload`
`systemctl restart espeakup`
Speakup ya debería hablar con una voz española. Si queremos subir el volumen, este comando puede servir: `amixer set Master 100%`
#### Conexión a Internet
Arch Linux necesita descargar sus paquetes desde la red. Se puede preparar un medio de instalación con paquetes ya descargados, pero el método para hacerlo se sale del propósito de esta guía. Al trabajar con una máquina virtual, esta ya estará conectada, por lo que puedes saltar a la siguiente sección. Antes de hacerlo, no obstante, sincroniza el reloj con el comando que aparece al final.
Si instalas Arch en un equipo real, lo mejor que puedes hacer es conectarlo por cable a la red. Si dispones de un router con dhcp, todo lo demás sucederá automáticamente.
Para conectarte a una red wi-fi, desbloquea los adaptadores disponibles, que podrían estar bloqueados por software: `rfkill unblock all`
Después, abre la consola de iwd: `iwctl`
En ella, se pueden enumerar los dispositivos disponibles: `device list`
A continuación, buscar redes: `station dispositivo scan`. Sustituye dispositivo por el identificador de tu dispositivo.
Y verlas: `station dispositivo get-networks`
Finalmente, para conectar: `station dispositivo connect nombre_red`
Si el nombre de la red contiene espacios, debe ir entre comillas. El programa preguntará la contraseña, si la red está cifrada.
Para salir de la utilidad iwctl, se debe pulsar ctrl+d.
Ahora que está conectada, podemos acceder mediante SSH a la máquina. Pero antes, sincronicemos el reloj con este comando: `timedatectl set-ntp true`
#### Acceso mediante SSH
El acceso mediante SSH nos permitirá instalar Arch desde una consola tradicional de Windows, con un lector de pantalla conocido y sin necesidad de acceder a la máquina virtual. Para habilitarlo, haremos lo siguiente:

* Cambiamos la contraseña de root: `passwd`. Se debe escribir la contraseña dos veces. No aparecerá ningún carácter en la ventana al teclear.
* Nos conectamos desde el menú VM > SSH > Connect to SSH. En un equipo real, deberemos conocer su dirección ip. El comando `ip a` proporcionará la información necesaria.

La primera vez, el cliente SSH nos preguntará si confiamos en la huella identificativa de la máquina. Escribimos `yes` y pulsamos enter.
Después, nos preguntará la contraseña asignada a root. Al escribirla y pulsar enter, estaremos dentro.
Nota: con cada arranque del medio de instalación, la huella del sistema cambia. Para evitar errores, edita el archivo .ssh/known_hosts que se encuentra en tu carpeta de usuario y elimina la línea correspondiente a la máquina virtual.

## Creación y formateo de particiones
Como ya se ha dicho antes, Arch Linux no tiene un instalador como tal, y todo debe hacerse a mano. El primer paso consiste en particionar el disco duro y formatear las particiones creadas.
Existen multitud de esquemas de particionado, como por ejemplo:

* Una partición ext4 para el sistema operativo y, opcionalmente, una partición swap (ideal en entornos BIOS).
* Una partición de arranque, que se montaría en /boot, otra partición para el sistema y, opcionalmente, una partición swap (ideal en entornos UEFI).
* Una partición de arranque, otra partición para el sistema base, otra partición para los usuarios montada en /home y otra partición montada en /usr.

Hay muchas más combinaciones, así como métodos para hacer que Linux coexista con otros sistemas operativos. Por defecto, en la máquina virtual anterior el firmware es de tipo BIOS, pero el modo UEFI es más moderno y se encuentra en todos los equipos físicos nuevos. Elige una de las dos siguientes secciones para particionar tu disco, según corresponda.
Para saber si tu sistema utiliza UEFI, usa este comando: `ls /sys/firmware/efi/efivars`
Si el comando falla, entonces estamos en un sistema que usa BIOS, o arranca en modo de compatibilidad con BIOS (CSM). De lo contrario, se trata de un sistema UEFI.
En las siguientes secciones, se asume que el disco está en /dev/nvme0n1. Sin embargo, en otras configuraciones de controlador y disco, podría estar en /dev/sda, /dev/vda, /dev/mmcblk0, etc. Utiliza el comando `lsblk` para descubrir cuál es tu disco de destino.
### En sistemas BIOS
Para particionar el disco usaremos la herramienta fdisk, y trabajaremos con el disco duro que creamos cuando hicimos la máquina virtual, y que se encuentra en /dev/nvme0n1:
`fdisk /dev/nvme0n1`
Al ejecutar este comando se cargará la consola de fdisk, y se creará automáticamente una tabla de particiones mbr, ya que el disco no tiene ninguna. Si el disco tuviera alguna, pulsa la o para reemplazarla. Ten en cuenta que los cambios no se escribirán hasta completar todos los pasos, así que no hay nada que temer.
A continuación pulsamos la tecla n y enter para crear una nueva partición.
Fdisk nos preguntará de qué tipo debe ser la partición. Como el tipo está puesto por defecto en primaria, no tenemos que pulsar la letra p, tan sólo pulsar enter de nuevo.
A continuación nos preguntará el número de partición primaria que utilizaremos de las 4 disponibles. Una vez más pulsamos enter, ya que el 1 está seleccionado por defecto.
Lo siguiente que nos pide es el tamaño de la partición, especificando el desplazamiento inicial. El valor por defecto que se nos ofrece para el sector inicial es el 2048, situado más o menos al principio del disco, así que podemos pulsar enter de nuevo sin modificar nada.
La primera partición se usará para extender la memoria RAM y será de tipo Swap. Vamos a hacer que ocupe 2 GB. Escribimos +2G y pulsamos enter.
Ahora, pulsamos la t para cambiar su tipo. Fdisk preguntará el código hexadecimal correspondiente. Escribimos 82 y pulsamos enter.
Repetimos los pasos anteriores para crear una partición para el sistema. Como queremos que la partición ocupe el resto del disco, pulsamos enter sin introducir nada cuando fdisk nos pregunta por el sector final.
Finalmente, veremos un mensaje como este: `Created a new partition 2 of type 'Linux' and of size 30 GiB`. El tipo es Linux, así que no hay que modificarlo. Lo que sí se debe hacer es marcar la partición creada como autoarrancable, o de lo contrario el sistema no podrá iniciarse. Para ello pulsamos la a, y enter nuevamente. Tras pulsar el 2 para indicar la segunda partición, veremos el siguiente mensaje: `The bootable flag on partition 2 is enabled now`.
Pulsando la p podemos ver un esquema general de lo que hemos creado. Si todo es correcto, se puede pulsar la w para escribir los cambios en el disco. De lo contrario, se puede pulsar la q para salir sin guardar nada.
Ya tenemos las particiones creadas y asociadas a los dispositivos /dev/nvme0n1p1 y /dev/nvme0n1p2. Lo siguiente que hay que hacer es formatearlas.
El siguiente comando preparará la partición swap: `mkswap /dev/nvme0n1p1`
Y con la utilidad mkfs.ext4, formatearemos la principal: `mkfs.ext4 /dev/nvme0n1p2`

### En sistemas UEFI
Para particionar el disco usaremos la herramienta gdisk, y trabajaremos con el disco duro que creamos cuando hicimos la máquina virtual, y que se encuentra en /dev/nvme0n1:
`gdisk /dev/nvme0n1`
Al ejecutar este comando se cargará la consola de gdisk, y se creará automáticamente una tabla de particiones gpt, ya que el disco no tiene ninguna. Si el disco tuviera alguna, pulsa la o para reemplazarla. Ten en cuenta que los cambios no se escribirán hasta completar todos los pasos, así que no hay nada que temer.
Lo primero que haremos será crear la partición de arranque, a la que asignaremos 512 MB, o hasta 4 veces más si pretendemos instalar varios núcleos. Para ello, pulsamos la n, seguida de enter.
En todos los casos, se puede dejar el número de partición como está y pulsar enter.
El primer sector comienza en 2048. Por defecto, gdisk siempre nos ofrecerá un primer sector recomendado en función del final de la partición anterior, o en este caso, el más adecuado al principio del disco.
Después, nos preguntará por el sector final. Escribimos +512M y pulsamos enter.
Para completar la partición, gdisk necesita conocer su tipo. El tipo de las particiones EFI es ef00.
Ahora, repetiremos los pasos y crearemos una partición swap de 2 GB, y una partición Linux con el espacio restante. El tipo correspondiente a la partición swap es 8200, mientras que para una partición Linux estándar es 8300. En este último caso, no hace falta escribirlo.
Ya tenemos la tabla de particiones lista. Pulsando la w seguida de enter, y tras responder afirmativamente a la pregunta de seguridad, se grabará en disco.
Las particiones están creadas y asociadas a los dispositivos /dev/nvme0n1p1, /dev/nvme0n1p2 y /dev/nvme0n1p3. Lo siguiente que hay que hacer es formatearlas.
Formateamos la partición EFI en FAT32: `mkfs.fat -F 32 /dev/nvme0n1p1`
El siguiente comando preparará la partición swap: `mkswap /dev/nvme0n1p2`
Y con la utilidad mkfs.ext4, formatearemos la principal: `mkfs.ext4 /dev/nvme0n1p3`

Si todo sale bien, el disco está correctamente formateado y listo para que podamos instalar el sistema operativo.

## Instalación de Arch Linux
El primer paso para poder instalar Arch Linux es montar las particiones creadas con anterioridad. Deberemos montar tanto la partición swap como la ext4, y la partición EFI en sistemas UEFI. Se puede hacer con los siguientes comandos.

### BIOS
Montamos la partición swap: `swapon /dev/nvme0n1p1`
Montamos la partición ext4 en la carpeta /mnt: `mount -t ext4 /dev/nvme0n1p2 /mnt`

### UEFI
Montamos la partición swap: `swapon /dev/nvme0n1p2`
Montamos la partición ext4 en la carpeta /mnt: `mount -t ext4 /dev/nvme0n1p3 /mnt`
Y montamos la partición EFI, creando una subcarpeta para ella: `mount --mkdir -t vfat /dev/nvme0n1p1 /mnt/boot`

Teniendo montadas las particiones, le llega el turno a pacstrap. Pacstrap es un script que se encarga de instalar paquetes. Recibe como primer parámetro la ruta donde se instalarán. El resto de argumentos son nombres de paquetes o grupos de paquetes, como base, base-devel, gnome, gnome-extra, etc. En este tutorial vamos a instalar los grupos base y base-devel, no queremos todavía entornos de escritorio. También instalaremos un kernel, el firmware, controladores para tarjetas de sonido, el lector de pantalla speakup, las utilidades para controlar la tarjeta de sonido y un editor de texto:
`pacstrap /mnt base base-devel linux linux-firmware sof-firmware espeakup alsa-utils nano`
Al ejecutar este comando se iniciará la descarga e instalación de paquetes. Obtendremos los paquetes básicos, el kernel, y el compilador gcc. Más adelante este último será indispensable para instalar paquetes desde el Arch User Repository.
Terminada la instalación, generamos un archivo fstab para que el sistema instalado pueda montar los sistemas de archivos adecuadamente al arrancar:
`genfstab -U -p /mnt >> /mnt/etc/fstab`
Lo siguiente que debemos hacer es simular que estamos trabajando desde la copia que acabamos de instalar. Para ello tenemos el comando arch-chroot. Recibe como argumento la ruta a la que queremos cambiarnos. Podemos escribir algo como esto:
`arch-chroot /mnt`
Se cargará la consola bash de la copia instalada, y la raíz del sistema de archivos estará en /mnt. No podremos manipular el sistema de archivos que se cargó inicialmente en el live cd hasta que salgamos de aquí.
Vamos a seguir modificando aspectos de la instalación. Lo primero que haremos será establecer un nombre de equipo. Este nombre hay que grabarlo en el archivo /etc/hostname, y en nuestro tutorial será arch-vmware:
`echo arch-vmware > /etc/hostname`
Importante: en este tutorial se emplea mucho el comando `echo` para escribir información en archivos de texto. Ten en cuenta que este comando escribe líneas en esos archivos, por lo que sólo debe usarse una vez. Si quieres deshacer algún cambio, utiliza un editor para cambiar el archivo, como por ejemplo nano.
Ahora vamos a establecer el idioma y la zona horaria, ya que nuestra copia instalada no está aún en español. Para ello debemos editar el archivo /etc/locale.gen:
`nano /etc/locale.gen`
Se deben eliminar los comentarios de los idiomas que queramos habilitar, borrando el signo de número que hay delante de cada uno. En nuestro caso, vamos a habilitar el inglés de Estados Unidos y el español de España, ambos en UTF-8. Corresponden a las líneas que empiezan por en_US.UTF-8 y es_ES.UTF-8. Siempre se recomienda habilitar el inglés de Estados Unidos, ya que muchos programas lo buscarán cuando alguna cadena de texto no esté traducida.
Para guardar los cambios, pulsamos ctrl+x, respondemos que sí pulsando la y, y finalmente pulsamos enter. Volveremos nuevamente a la consola bash.
Ahora, generamos los idiomas:
`locale-gen`
Y finalmente, establecemos el español como idioma del sistema:
`echo LANG=es_ES.UTF-8 > /etc/locale.conf`
El método para establecer la zona horaria es distinto al de cambiar el idioma. En este caso tenemos que crear un vínculo simbólico. La sintaxis sería:
`ln -sf /usr/share/zoneinfo/zone/subzone /etc/localtime`
En este ejemplo vamos a establecer la hora de Madrid:
`ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime`
A continuación, configuramos el ajuste horario con este comando: `hwclock --systohc`
Y habilitamos un servicio para que sincronice el reloj con Internet: `systemctl enable systemd-timesyncd`
Para terminar, debemos hacer que el teclado virtual también esté en español:
`echo KEYMAP=es > /etc/vconsole.conf`
Ya hemos terminado de establecer las preferencias de idioma, zona horaria y teclado. Es hora de ocuparnos de otras cosas, como la red.
Por defecto, la iso de Arch Linux está configurada para usar una red cableada, buscar un servidor dhcp, y obtener la dirección ip dinámicamente. En la copia instalada necesitamos instalar un gestor de red y habilitarlo:
`pacman -S --noconfirm networkmanager`
`systemctl enable NetworkManager`
`systemctl enable systemd-resolved`
Otro pequeño problema que nos vamos a encontrar es que el nombre del host no se anuncia en la red local. Para resolverlo, vamos a instalar samba:
`pacman -S --noconfirm samba`
El servicio que nos interesa habilitar es el nmb, encargado de anunciar el nombre de la máquina. Para ello hacemos:
`systemctl enable nmb`
Pero cuidado, aquí debemos hacer algo más, ya que los servicios de Samba necesitan leer de un archivo de configuración, y no tienen ninguno:
`touch /etc/samba/smb.conf`
Ya tenemos todo listo para que el servicio nmb funcione. Vamos a instalar el preciado servidor ssh, para poder conectarnos desde fuera de la máquina si fuera necesario:
`pacman -S --noconfirm openssh`
`systemctl enable sshd`
Permitimos que root inicie sesión con contraseña: `echo permitRootLogin yes >> /etc/ssh/sshd_config`
Con la ayuda del editor nano, podemos cambiar la voz de Speakup, tal y como hicimos en el medio de instalación. Consulta las primeras secciones de este tutorial para encontrar las instrucciones. Recuerda habilitar el servicio: `systemctl enable espeakup`
Para acabar de instalar nuestra copia de Arch, nos falta cambiar la contraseña de root e instalar un cargador de arranque. Lo primero se puede hacer ejecutando simplemente el comando passwd, tal y como hicimos anteriormente:
`passwd`
Para lo segundo debemos instalar otro paquete y ejecutar un par de comandos. Instalamos el gestor de arranque grub:
`pacman -S --noconfirm grub`

### En sistemas BIOS
Instalamos el gestor de arranque en el sector mbr, y en el sector de arranque de la partición:
`grub-install --recheck /dev/nvme0n1`

### En sistemas UEFI
Instalamos el paquete efibootmgr: `pacman -S --noconfirm efibootmgr`
Instalamos el gestor de arranque en la partición EFI:
`grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB`
Si se añade al comando anterior el argumento `--removable`, Grub no modificará las variables EFI alojadas en la NVRAM y quedará preparado para arrancar desde un disco extraíble. Útil si, por ejemplo, estamos creando un pen drive con un sistema operativo para llevar y usar en cualquier ordenador con Uefi.
Se puede añadir al disco extraíble soporte para sistemas BIOS con capacidad de arrancar discos GPT. Para ello, usa gdisk y crea una cuarta partición entre los sectores 34 y 2067. Deberás indicar que su tipo es ef02. No importa su orden en la tabla, puedes crearla incluso con el sistema ya instalado. Después, instala GRUB una segunda vez. Por ejemplo: `grub-install --target=i386-pc /dev/nvme0n1`
Importante: tal y como se ha configurado, Arch no iniciará si el arranque seguro está activado. Deberás desactivarlo en la configuración de tu firmware.

Opcionalmente, se pueden instalar las imágenes que actualizan el microcódigo del procesador:
`pacman -S --noconfirm intel-ucode amd-ucode`
También se puede (y se recomienda) configurar Grub para que emita un pitido al arrancar. Así sabremos en qué momento podemos manipular el gestor de arranque, llegado el caso.
Se puede configurar un pitido simple: `echo GRUB_INIT_TUNE=\"480 440 1\" >> /etc/default/grub`
O uno más complejo y entretenido: `echo GRUB_INIT_TUNE=\"1750 523 1 392 1 523 1 659 1 784 1 1047 1 784 1 415 1 523 1 622 1 831 1 622 1 831 1 1046 1 1244 1 1661 1 1244 1 466 1 587 1 698 1 932 1 1195 1 1397 1 1865 1 1397 1\" >> /etc/default/grub`
A continuación, generamos el archivo de configuración:
`grub-mkconfig -o /boot/grub/grub.cfg`
Todo listo, es hora de salir del entorno simulado, desmontar las particiones y reiniciar el sistema. Para salir del entorno simulado, podemos escribir exit o simplemente pulsar ctrl+d. Para desmontar las particiones, usaremos los siguientes comandos.

### En sistemas BIOS
`umount /mnt`
`swapoff /dev/nvme0n1p1`

### En sistemas UEFI
`umount /mnt/boot`
`umount /mnt`
`swapoff /dev/nvme0n1p2`

Y, finalmente, para reiniciar:
`reboot`
Si todo ha ido bien, nuestro sistema Arch está instalado, y accesible por ssh si se encuentra conectado por cable a la red. Podemos entrar con el siguiente comando:
`ssh root@arch-vmware`
Sigamos, ¡falta mucho por hacer!
### Cómo subir el volumen para Speakup
La primera vez que iniciemos la copia instalada de Arch, el lector de pantalla se escuchará con un volumen muy bajo o directamente no se escuchará. Esto depende del equipo. La solución pasa por subir el volumen de los canales Master y PCM (si existe) al 100%, quitar sus silencios y guardar los ajustes. Ejecuta estos comandos:
`amixer set Master unmute`
`amixer set Master 100%`
`amixer set PCM unmute`
`amixer set PCM 100%`
`alsactl store`
¡Importante! Este comando no se debe usar si estamos preparando una instalación en un disco extraíble. En su lugar, se debe copiar desde una instalación local el archivo `/var/lib/alsa/asound.state`
¡Todo listo! Hemos instalado Arch Linux, le hemos dado sonido y un lector de pantalla. Ahora ya no necesitamos un servidor ssh para trabajar desde el exterior, podemos manipular la propia máquina desde sus interfaces locales.
## Siguientes pasos.
### Actualizar el sistema
Lo primero que se debe hacer antes de instalar paquetes adicionales es tener completamente actualizado nuestro sistema. Vamos a actualizarlo, y a reiniciarlo:
`pacman -Syu`
`reboot`
### Instalación de Reflector
Por defecto, Pacman descarga los paquetes desde una lista de sitios que lleva incorporada. Estos sitios, o réplicas, cambian con el tiempo y conviene actualizarlos. De esa forma, obtendremos los paquetes más deprisa, y probablemente desde una ubicación más cercana geográficamente. Para conseguirlo, instalaremos Reflector:
`pacman -S --noconfirm reflector`
Ahora, lo configuramos para que arranque con el sistema y lo iniciamos.
`systemctl enable reflector`
`systemctl start reflector`
### Instalación de páginas de manual
El comando man puede ser muy útil a la hora de buscar la ayuda de un programa concreto y leer sus páginas de manual. Usa este comando para instalarlo:
`pacman -S --noconfirm man-pages man-db`
### Instalación de VMWare Tools (sólo en máquinas virtuales)
Las herramientas de VMWare, o VMWare Tools, permiten a la máquina virtual integrarse mucho mejor con el equipo físico. Podemos utilizar desde carpetas compartidas, hasta arrastrar y soltar para transferir archivos.
Instalar las herramientas que VMWare Workstation trae por defecto ya no se recomienda. Todas las distribuciones de Linux incorporan sus propias herramientas. En este caso:
`pacman -S --noconfirm open-vm-tools`
Con este paquete se instala un servicio que notifica a VMWare que ya tenemos las herramientas instaladas. Vamos a activarlo y a establecerlo al arranque del sistema:
`systemctl start vmtoolsd`
`systemctl enable vmtoolsd`
Tras hacer esto, podemos ir al menú vm en VMWare, ¡y ver que indica que las herramientas están instaladas! Pero… ¿Funciona todo? Todavía no, ya que no tenemos funciones de arrastrar y soltar o copiar y pegar, por ejemplo.
Para resolver el problema de arrastrar y soltar, habilitamos e iniciamos un servicio:
`systemctl enable vmware-vmblock-fuse`
`systemctl start vmware-vmblock-fuse`
Todo listo. Cuando tengamos un entorno de escritorio, podremos arrastrar y soltar o copiar y pegar archivos desde el equipo físico hacia la máquina, y viceversa.
### Instalación de Yay
Como ya se dijo más arriba, una de las características de Arch es la capacidad de compilar e instalar paquetes directamente desde el código fuente. Aunque los paquetes se pueden descargar y compilar manualmente desde el Arch User Repository, el proceso es largo y complicado, así que conviene automatizarlo. Yay es una herramienta muy similar a Pacman y admite sus mismos comandos, pero también trabaja sobre el Arch User Repository además de los repositorios oficiales. Además, no se debería ejecutar con el usuario root, necesita una cuenta estándar con privilegios de superusuario. Con yay podemos descargar, compilar e instalar paquetes, todo en un solo comando. Desgraciadamente, esta herramienta no se puede instalar desde pacman, y por eso le dedicamos un apartado entero en este tutorial.
Como a Arch no le gusta compilar paquetes siendo root, lo primero que haremos será crear un usuario administrador, pero sin tantos privilegios:
`useradd -m usuario`
En el comando hemos indicado que el usuario se llamará usuario y tendrá un directorio personal para él. Ahora vamos a darle una contraseña:
`passwd usuario`
Escribimos la contraseña 2 veces, y la cuenta de usuario ya está lista para su uso. Ahora, escribiremos el siguiente comando para que se puedan obtener más privilegios al ejecutar comandos con sudo:
`echo "usuario ALL=(ALL:ALL) ALL" >> /etc/sudoers`
Ahora vamos a instalar el paquete wget para poder descargar los archivos de yay:
`pacman -S --noconfirm wget`
Hecho esto, iniciamos sesión con la cuenta usuario. Esto se puede hacer de muchas maneras, por ejemplo con este comando:
`su -l usuario`
Si trabajamos con la consola de la máquina virtual, también podemos salir e iniciar sesión desde allí. Lo mismo si trabajamos con ssh:
`ssh usuario@arch-vmware`
Teniendo la sesión iniciada y estando en nuestro directorio personal, comenzamos a descargar y compilar paquetes.
Descargamos Yay:
`wget https://aur.archlinux.org/cgit/aur.git/snapshot/yay.tar.gz`
Tras descargarlo, el primer paso es extraerlo:
`tar -zxf yay.tar.gz`
A continuación, navegamos al directorio que se ha extraído:
`cd yay`
Construimos el paquete y lo instalamos una vez compilado:
`makepkg -si`
Yay ya está listo para la acción. Podemos instalar cualquier paquete desde el Arch User Repository con facilidad, siempre desde la cuenta estándar y sin llamar a sudo. Por ejemplo, el paquete needrestart, que ayuda a reiniciar sólo los servicios necesarios tras una actualización, en vez del equipo entero:
`yay -S --noconfirm needrestart`
En procesadores Intel, Needrestart se puede complementar con la herramienta iucode-tool:
`yay -S --asdeps --noconfirm iucode-tool`
Observa que el primer paquete se descarga desde el Arch User Repository, mientras que el segundo procede de los repositorios oficiales. El segundo se ha instalado como una dependencia opcional. Más adelante veremos qué significa eso.
### Activación del repositorio multilib
El repositorio multilib, desactivado por defecto, contiene bibliotecas y aplicaciones de 32 bits, así como software que funciona sobre varias arquitecturas, como Wine o Steam.
Para activarlo, hay que editar el archivo /etc/pacman.conf con cualquier editor, como nano.
En él, las líneas de los repositorios multilib y multilib-testing vienen comentadas. Es decir, llevan un signo de número delante. Bastará con eliminarlo y guardar los cambios para activar el repositorio. Ten en cuenta que sólo se deben quitar los comentarios de dos líneas, y que no se recomienda habilitar los repositorios testing. Si ya de por sí el software suele ser inestable en Arch, los paquetes que proceden de los repositorios testing lo son aún más.
### Búsqueda de paquetes con Pacman
Para buscar paquetes con Pacman, ejecuta un comando similar a este: `pacman -Ss palabra`
### Limpieza de la caché de paquetes
Después de instalar nuevos paquetes o actualizar el sistema, los paquetes comprimidos que se han descargado permanecen en el disco ocupando espacio. Para limpiar la caché y borrarlos, usa este comando:
`pacman -Scc`
O, incluso mejor, hazlo con Yay: `yay -Scc`
### Eliminación de paquetes huérfanos
A veces, un paquete se actualiza y deja de necesitar otro del que antes dependía. Esa dependencia permanece en el sistema y no se desinstala por sí sola. Con el tiempo, esto puede llegar a ser un problema. Ejecuta el comando `pacman -Qtdq | pacman -Rns -` para buscar y eliminar paquetes huérfanos.
### Instalación del escritorio gnome
Gnome es uno de los entornos de escritorio más populares para Linux, y uno de los que mejor integra la accesibilidad. En el caso de Arch, en los repositorios hay 2 grupos de paquetes que podemos instalar: gnome y gnome-extra.
Instalamos ambos grupos con este comando: `pacman -S --noconfirm --needed gnome gnome-extra`
Para completar Gnome con todas las aplicaciones que no se encuentran en dichos grupos, podemos ejecutar un comando como este, que instalará todos los paquetes con la palabra gnome en su nombre:
`pacman -S --needed $(pacman -Ssq gnome)`
Se instalarán todos los paquetes necesarios, incluyendo orca, el lector de pantalla de escritorio de Linux, y aplicaciones gráficas con diversas funciones (gestión de discos, correo electrónico, chat, e incluso algún juego). El proceso es largo, así que hay que tener paciencia.
Si tienes una línea Braille, añade tu usuario al grupo brlapi:
`usermod -a -G brlapi usuario`
E instala el paquete que permite el inicio automático al detectar líneas conectadas por USB:
`pacman -S --asdeps --noconfirm brltty-udev-generic`
Ponemos el teclado en español para las aplicaciones gráficas: `localectl set-x11-keymap es,es`
¡Importante! Este comando no funcionará en imágenes fuera de línea, por ejemplo al crear una unidad extraíble. En su lugar, copia desde una instalación local de Arch el archivo `/etc/X11/xorg.conf.d/00-keyboard.conf`
Por último, ponemos al arranque el servicio gdm, que se encarga de gestionar el escritorio, mostrar la pantalla de inicio de sesión y otras tareas de vital importancia:
`systemctl set-default graphical`
`systemctl enable gdm`
También activamos un servicio que montará automáticamente dispositivos extraíbles: `systemctl enable udisks2`
Y reiniciamos el equipo a modo de precaución:
`reboot`
Al arrancar el servicio gdm, desaparece la consola de la máquina virtual. Espeakup deja de hablar, y se muestra ante nosotros la pantalla de inicio de sesión. Ahora, si queremos un lector de pantalla, se debe activar orca pulsando windows+alt+s.
Pulsando ctrl+alt+tab, podemos navegar entre los distintos paneles que componen el escritorio. En este caso tenemos el de bloqueo, la barra superior y el de iniciar sesión. Iremos a este último, y entraremos con la cuenta de usuario estándar que creamos al principio. Los paneles cambiarán, pero la forma de navegar es la misma.
Al escribir la contraseña y pulsar enter, Orca dejará de hablar. Lo activamos nuevamente pulsando windows+alt+s. El entorno de escritorio gnome estará listo para su uso. ¡Disfruta!
### Instalación del escritorio Mate
Mate es un escritorio basado en el antiguo Gnome 2.x. Destaca por su ligereza y poco consumo de recursos, su accesibilidad con Orca y la familiaridad del entorno, que puede recordar más a Windows. Al contrario que Gnome, Mate sí tiene un escritorio donde se pueden depositar accesos directos y archivos. El acceso a las aplicaciones se realiza mediante la barra de menú superior, a la que se llega con alt+f1.
Mate comparte muchos paquetes con Gnome, y algunas de sus funciones sólo se activan cuando se usa GDM como el administrador gráfico. Podemos instalarlo con estos comandos. Si Gnome ya está instalado, el espacio en disco no aumentará mucho:
`pacman --noconfirm --needed -S mate mate-extra`
`pacman --needed --noconfirm -S $(pacman -Ssq mate-)`
En la ventana de inicio de sesión, si navegamos con el tabulador justo después de escribir la contraseña, encontraremos un menú para elegir el escritorio predeterminado. Aparecerán todos los escritorios que se encuentren instalados, incluyendo diversas variantes de Gnome y Mate.
### Dependencias opcionales
En Arch, muchos paquetes tienen dependencias opcionales. Esto significa que funcionan sin ellas, pero al instalarlas se obtiene una funcionalidad extra. Aquí hay algunas que puedes usar con Gnome para extenderlo aún más.
Para instalar una dependencia opcional, se recomienda indicar a Pacman que se trata de una dependencia, y no de una instalación explícita. De esa forma, al desinstalar el paquete que la necesita, también se borrará. Usa un comando como este para conseguirlo:
`pacman -S --asdeps --noconfirm paquete1 paquete2 paquete3`
El paquete Harfbuzz ofrece esta dependencia con utilidades: harfbuzz-utils
Para la app Juegos, se pueden instalar todos estos emuladores de consolas antiguas: libretro-beetle-pce-fast libretro-beetle-psx libretro-blastem libretro-citra libretro-flycast libretro-gambatte libretro-mgba libretro-nestopia libretro-parallel-n64 libretro-picodrive gamemode
Estos paquetes extenderán la app Herramientas de red: nmap bind net-tools
Con este otro, se puede jugar al ajedrez contra el ordenador: gnuchess
Este paquete aparece como opcional al instalar Code assistance: jedi-language-server
Si usas el programa de correo Evolution, estos son sus paquetes opcionales, empleados para filtrar spam: highlight evolution-spamassassin evolution-bogofilter
Este paquete permite al escritorio gestionar perfiles de energía: power-profiles-daemon. Habilita el servicio power-profiles-daemon para que funcione.
Y este otro, configurar las impresoras: system-config-printer
La aplicación Cajas usa el emulador Qemu para ejecutar máquinas virtuales. Estos paquetes contienen todas sus arquitecturas: qemu-user-static qemu-user qemu-tests qemu-system-xtensa qemu-system-tricore qemu-system-sparc qemu-system-sh4 qemu-system-s390x qemu-system-rx qemu-system-riscv qemu-system-ppc qemu-system-or1k qemu-system-nios2 qemu-system-mips qemu-system-microblaze qemu-system-m68k qemu-system-hppa qemu-system-cris qemu-system-avr qemu-system-arm qemu-system-alpha qemu-system-aarch64 qemu-docs qemu-chardev-baum qemu-block-iscsi qemu-block-gluster qemu-emulators-full qemu-user-static-binfmt qemu-full
Si usas el gestor de archivadores, estos paquetes le darán compatibilidad con todos los formatos soportados: squashfs-tools lrzip unace unrar p7zip
Brasero permite grabar contenido en cd y dvd. Con estos paquetes opcionales, además, puede personalizar más aspectos de la grabación y procesar contenido multimedia: dvdauthor vcdimager libisofs libburn
Esta extensión añade el menú "Enviar a" al explorador de archivos Nautilus: nautilus-sendto
Gedit es más que un bloc de notas, pero con sus plugins ofrece todavía más funcionalidades: gedit-plugins
Este applet mostrará información de la red: network-manager-applet
El soporte NTFS viene integrado en el kernel de Linux desde la versión 5.15, pero algunas aplicaciones siguen necesitando el paquete antiguo para usar sus utilidades: ntfs-3g
Con este paquete, el applet de sensores mostrará la temperatura del disco duro: hddtemp
Dejando de lado el escritorio, la consola bash puede beneficiarse mucho del autocompletado en ciertas situaciones, incluida la búsqueda e instalación de paquetes. Instala bash-completion para conseguirlo.
El motor de sonido Pipewire puede extenderse con este paquete: pipewire-audio
Y la herramienta Reflector dispondrá de más mecanismos para descargar la lista de réplicas si se instala el paquete rsync.
Con el paquete python-pyudev, la librería libwacom soportará más tipos de hardware.
Los paquetes udftools, exfatprogs, dosfstools y xfsprogs extienden la capacidad del servicio udisks2 para montar automáticamente sistemas de archivos.
### Ejecución de aplicaciones de Windows
Los archivos .exe y .dll que usamos en Windows no tienen nada que hacer en Linux, a menos que instalemos algo que permita ejecutarlos. Wine, del que ya hablamos al explicar cómo habilitar multilib, es un emulador que permite que los programas de Windows funcionen, siempre que no sean demasiado avanzados. Ten en cuenta que las aplicaciones que se ejecutan dentro de Wine no son accesibles, incluso si logras instalar NVDA:
`pacman -S --noconfirm wine`
Por defecto, las aplicaciones para Windows no tendrán sonido, y más si son de 32 bits. Es importante habilitarlo si vamos a trabajar a ciegas. Para ello, instala todas las dependencias opcionales:
`pacman -S --noconfirm --needed --asdeps lib32-giflib lib32-mpg123 lib32-openal lib32-v4l-utils lib32-libpulse lib32-alsa-plugins lib32-alsa-lib lib32-libxcomposite lib32-libxinerama lib32-opencl-icd-loader lib32-libxslt lib32-gst-plugins-base-libs vkd3d lib32-vkd3d dosbox lib32-gst-plugins-base lib32-gst-plugins-good lib32-libcups lib32-pcsclite pcsclite unixodbc wine-gecko wine-mono`
### Otras aplicaciones conocidas
A continuación se enumeran algunas aplicaciones de ofimática e Internet que pueden resultar conocidas en Windows y que, de hecho, llevan toda la vida en Linux.

* LibreOffice: se puede obtener mediante el paquete libreoffice-fresh, o libreoffice-still para la versión de soporte extendido, más antigua. Para que su interfaz esté en español, instala libreoffice-fresh-es o libreoffice-still-es, según corresponda.
* Firefox y Thunderbird: se pueden instalar escribiendo sus nombres en Pacman, directamente. Para traducir su interfaz, no obstante, también se deben instalar firefox-i18n-es-es y thunderbird-i18n-es-es. El grupo firefox-addons contiene algunas extensiones populares.
* Chrome: Google Chrome se encuentra disponible en el Arch User Repository con el nombre google-chrome. Para instalar un paquete más libre y procedente de los repositorios oficiales, se puede descargar chromium desde Pacman. Por defecto, el motor Chromium no se comunica con las tecnologías de asistencia. Para cambiar ese comportamiento, se debe agregar la variable de entorno ACCESSIBILITY_ENABLED y darle el valor 1. Ejecuta este comando en la cuenta de usuario estándar (no root) y reinicia el equipo: `echo export ACCESSIBILITY_ENABLED=1 >> ~/.xprofile`. Ten en cuenta que Chromium no puede usar ciertos códecs propietarios, servicios de Google o módulos DRM. Si necesitas estas funciones, usa Chrome.
* VLC: este reproductor multimedia también se encuentra disponible en los repositorios oficiales de Arch. El paquete opcional lua-socket se puede instalar para activar su servidor http.

### Controladores
Arch incluye controladores para una amplia gama de dispositivos. El paquete linux-firmware contiene los más comunes y de código abierto. Ya lo instalamos al principio, junto con sof-firmware, que contiene controladores para tarjetas de sonido. Si te aventuras a instalarlo en un equipo real y algún dispositivo no funciona como debería, prueba estos paquetes:

* linux-firmware-marvell
* linux-firmware-qcom
* linux-firmware-bnx2x
* linux-firmware-mellanox
* linux-firmware-qlogic
* linux-firmware-liquidio
* linux-firmware-nfp
* b43-fwcutter
* alsa-firmware
* mkinitcpio-firmware (se instala con yay, no forma parte de los repositorios oficiales). Instala todos los módulos firmware, lo que evita errores y advertencias al reconstruir el kernel.

Una vez instalados estos paquetes, conviene recompilar los kernels disponibles con un comando como este, y reiniciar el equipo: `mkinitcpio -P`
Por su parte, el servidor xorg puede funcionar mejor si se instalan controladores para tarjetas gráficas específicas. El grupo xorg-drivers contiene casi todos, con algunas excepciones.
Las tarjetas gráficas NVidia necesitan instalar módulos en el kernel. Se puede descargar el controlador propietario más reciente instalando el paquete nvidia para el kernel nativo de Arch, el paquete nvidia-lts para el kernel lts, o el paquete nvidia-dkms para todos en general. En este último caso, deben estar instaladas las cabeceras del kernel o kernels para el que se compilará el módulo. Por ejemplo, linux-zen-headers. El paquete nvidia-settings ofrece una aplicación de configuración para la tarjeta. Se deben habilitar con systemd los servicios nvidia-persistenced, nvidia-powerd, nvidia-hibernate, nvidia-resume y nvidia-suspend para gestionarla de forma óptima.
Las tarjetas gráficas AMD pueden funcionar mejor con el paquete amdvlk. Tiene una versión para aplicaciones de 32 bits, llamada lib32-amdvlk.
Las tarjetas gráficas Intel ya están soportadas al instalar xorg-drivers, pero otras aplicaciones pueden beneficiarse si se instalan estos paquetes: `libva-intel-driver intel-media-driver vulkan-intel lib32-libva-mesa-driver libva-mesa-driver libvdpau-va-gl mesa-vdpau lib32-libva-intel-driver lib32-vulkan-intel`
Los escáneres pueden verse complementados con el paquete sane-gt68xx-firmware, que incluye controladores para más modelos, y el paquete sane-airscan, que permite usar un escáner inalámbrico que no necesita controladores.

### Otros núcleos
Hasta ahora, hemos trabajado con un único kernel, disponible en el paquete linux. Este kernel es genérico, adecuado para cualquier tarea que queramos hacer con el sistema operativo. Sin embargo, en los repositorios oficiales hay otros con distintas características. Puedes instalar varios a la vez, pero es posible que necesites otro esquema de particiones distinto al propuesto en este tutorial para que te quepan todos:

* linux-lts: el paquete linux-lts ofrece un núcleo con soporte extendido. Va un poco retrasado respecto al kernel normal, pero da más garantías de estabilidad.
* linux-zen: el kernel zen viene optimizado para equipos de escritorio. Mejora el desempeño de los gráficos y reduce la latencia del audio.
* linux-hardened: instala este núcleo si lo que quieres es seguridad. Viene optimizado para mitigar vulnerabilidades. En consecuencia, puede hacer que el sistema vaya algo más despacio, y algunas aplicaciones no funcionan con él.
* linux-rt: núcleo optimizado para trabajar con eventos en tiempo real.
* linux-rt-lts: versión de soporte extendido del núcleo anterior.

Tras instalar cualquiera de estos núcleos, regenera el archivo de configuración de Grub: `grub-mkconfig -o /boot/grub/grub.cfg`
Por defecto, el kernel zen se situará arriba del todo, convirtiéndose en el primero en arrancar.

### Gestión de impresoras
En Linux, el paquete cups es el responsable de comunicarse con las impresoras conectadas. Con la ayuda de cups-browsed, podrá buscar impresoras de red. Instalará unos servicios, que deberemos activar:
`systemctl enable cups cups-browsed`
`systemctl start cups cups-browsed`
Cups acepta conexiones desde el navegador en http://localhost:631. Desde allí se pueden gestionar las impresoras existentes, pero Gnome y Mate también ofrecen sus propias aplicaciones para ello.
Con el paquete cups-pdf, se instala una impresora virtual capaz de imprimir en archivos PDF. Tan pronto como lo instales, usa tu interfaz preferida de configuración de impresoras para agregarla, ya que no aparecerá por sí sola.
En ordenadores reales, se pueden instalar controladores que actuarán de intermediarios entre Cups y diversos modelos de impresoras. Vienen en estos paquetes: `foomatic-db foomatic-db-engine foomatic-db-ppds foomatic-db-nonfree-ppds foomatic-db-gutenprint-ppds gutenprint`
Aunque se cubre una amplia gama de fabricantes y modelos, no funcionarán todos. Pueden hacer falta paquetes adicionales, tanto de los repositorios oficiales como del AUR. Visita [esta página en la wiki de Arch Linux](https://wiki.archlinux.org/title/CUPS/Printer-specific_problems) para encontrar el tuyo.
### Bluetooth
Teniendo instalados todos los paquetes que se han visto a lo largo de este tutorial, el bluetooth se puede usar activando e iniciando este servicio:
`systemctl enable bluetooth`
`systemctl start bluetooth`
Para agregar impresoras Bluetooth, además, se debe instalar el paquete bluez-cups.

### Redes wi-fi
¿Has instalado Arch en un equipo físico conectado por cable, pero lo tuyo son las redes inalámbricas? Habilita e inicia este servicio:
`systemctl enable wpa_supplicant`
`systemctl start wpa_supplicant`
Deberías poder configurar tu red wi-fi desde la barra superior de Gnome. Instala el paquete wireless-regdb para cumplir con la legislación vigente de tu país relativa a canales y potencia de transmisión. Se reconstruirán todos los núcleos instalados, por lo que tendrás que reiniciar el equipo.

### Otros administradores gráficos
Existen varios escritorios más que no hemos mencionado en este tutorial porque carecen de accesibilidad con lector de pantalla, o la que tienen es tan pobre que degrada demasiado la experiencia. Con los administradores gráficos sucede lo mismo. Hemos hablado de gdm, el que usan Gnome y Mate, pero no es el único. Lightdm puede ser una buena alternativa en sistemas con bajos recursos. Para instalarlo:
`pacman -S --noconfirm lightdm lightdm-gtk-greeter`
Antes de habilitarlo, se debe deshabilitar gdm, ya que sólo puede haber un administrador gráfico en ejecución:
`systemctl disable gdm`
`systemctl enable lightdm`
Para indicar a LightDM que use Orca, añadiremos una línea al archivo de configuración de la interfaz:
`echo reader=orca >> /etc/lightdm/lightdm-gtk-greeter.conf`
Al reiniciar el sistema, LightDM aparecerá en pantalla. Se puede activar el lector de pantalla Orca pulsando la tecla f4. Pulsando f10 se accede a la barra de menú, desde donde se puede seleccionar qué escritorio arrancará. La tecla f10 accederá a la barra de menú en todas las aplicaciones que la tengan.

### Descubrimiento de dispositivos en la red local
Algunos dispositivos y programas, como impresoras, televisores o servidores de archivos, facilitan información a la red local sobre cómo conectarse e interactuar con ellos. Para ello, se apoyan en protocolos como zeroconf y mdns. A pesar de que ya se han instalado utilidades que permiten explorar la red en busca de estos dispositivos, se debe habilitar e iniciar un servicio:
`systemctl enable avahi-daemon`
`systemctl start avahi-daemon`
A continuación, para resolver direcciones que acaben en `.local`, se puede instalar este paquete:
`pacman -S --noconfirm nss-mdns`
Y finalmente, usamos nano para editar el archivo /etc/nsswitch.conf. La línea que empieza por "hosts" debe quedar así:
`hosts: mymachines mdns_minimal [NOTFOUND=return] resolve [!UNAVAIL=return] files myhostname dns`
Otro servicio que mejora la compatibilidad y el descubrimiento con equipos con Windows es wsdd. Se puede instalar con el siguiente paquete:
`yay -S wsdd2`
Después, es suficiente con habilitar este servicio. Seguirá los mismos ajustes de configuración de Samba, si los hay:
`systemctl enable wsdd2`

## Conclusiones
En este tutorial hemos instalado Arch Linux, una distribución pequeña, simple y ligera, donde cada usuario elige lo que quiere y obtiene únicamente lo necesario para conseguir su objetivo. Hemos conocido Linux mejor por dentro, evitando la presencia de automatizaciones y herramientas que nos abstraen del proceso en otras distribuciones. Conociendo el funcionamiento de los programas en Arch Linux, se puede saber cómo funcionan por debajo ciertos aspectos en distribuciones como Ubuntu, Debian y Fedora, y también echar un vistazo a lo que está por llegar, ya que siempre se reciben las últimas versiones estables de los programas poco después de publicarse. Hemos aprendido a construir un sistema con una base de consola que no tiene uno, sino tantos escritorios como queramos instalarle, unos más accesibles que otros, y varios lectores de pantalla que coexisten en un mismo sistema.
A pesar de todos los temas abarcados, este tutorial sólo araña la superficie. Los paquetes que se han instalado, así como sus dependencias, tienen montones de funciones extra que se pueden explorar, y existen muchos más paquetes y programas que no se han documentado aquí.
Arch evoluciona rápidamente, por lo que no sería de extrañar que este texto se quede obsoleto pronto. Lo que acabas de leer no se parece nada a la primera versión de este tutorial, escrita en el año 2015. En aquella época, la instalación era más compleja que ahora, y el resultado menos accesible. Deshabilitar el servicio espeakup era obligatorio para usar Orca, ya que entraban en conflicto compitiendo por el dispositivo de audio, por ejemplo.
Ahora ya sabes cómo instalar Arch en una máquina virtual, pero a lo largo de las distintas secciones se han dado trucos para crear un pen drive con un sistema para llevar, o realizar la instalación en un ordenador real. ¿Te animas a hacerlo? ¿Ya lo has hecho? No dudes en compartir tu experiencia, y contarnos qué ventajas y desventajas observas respecto a otros sistemas.
¡Gracias por leer!

## Fuentes de información
Este tutorial no habría sido posible sin consultar las siguientes fuentes:

1. La [guía de instalación de Arch Linux](https://wiki.archlinux.org/title/Installation_guide).
2. La [wiki de Arch](https://wiki.archlinux.org), en la que he leído tantas páginas distintas que no sabría enlazar todas.
3. [Tono de Grub de Mario Bross](https://gist.github.com/MaxLaumeister/f93717e91c8bd9d435a5).
4. [Diferencias entre Chromium y Google Chrome](https://en.wikipedia.org/wiki/Chromium_(web_browser)#Differences_from_Google_Chrome).
5. [Uso de Orca con Chromium](https://wiki.gnome.org/Projects/Orca/Chromium).
6. Páginas de manual, la información que devuelven muchos comandos al pasar --help como argumento, y los comentarios que se encuentran en los archivos de configuración.
7. Varios años experimentando de forma ininterrumpida.

## Anexo: creación de una imagen lista para grabar y arrancar

A lo largo de este tutorial se han dado consejos útiles para preparar una unidad extraíble. El procedimiento es muy similar al de instalación en local, pero teniendo en cuenta que systemd y algunos comandos no funcionan, y que no se debe iniciar ningún servicio. En este capítulo explicaremos todo aquello que no se ha contado antes relacionado con la creación de imágenes listas para grabar, llevar a cualquier parte y arrancar.

### Creación del archivo de imagen

Para crear un archivo de imagen totalmente vacío, se puede hacer uso de la utilidad dd. Con ella, creamos un archivo del tamaño que queramos y lo llenamos de ceros. Por ejemplo, este comando preparará un archivo cuyo tamaño es muy similar al de un pen drive de 32 GB:
`dd if=/dev/zero of=archlinux.img bs=4M count=7629 status=progress`
Tan pronto como dd termine, podremos particionarlo como se vio más arriba. Podemos omitir la partición swap. Si el sistema llegara a necesitarla, el rendimiento se reduciría drásticamente:
`gdisk archlinux.img`
Una vez escribimos la tabla de particiones, podemos montar la imagen como si fuera un disco real:
`losetup -P /dev/loop0 archlinux.img`
En este caso, loop0 es el dispositivo donde irá montada la imagen. Si loop0 no está disponible, podemos recurrir a loop1, y así sucesivamente. Las particiones se montarán como loop0p1, loop0p2, etc.
Para desmontar la imagen, se debe ejecutar un comando como este después de desmontar todos los sistemas de archivos:
`losetup -d /dev/loop0`

### Preparación para la nube

Nunca sabemos dónde puede acabar la imagen que hemos creado. Prepararla para que se ejecute en cualquier parte puede ser buena idea, incluso si se configura para su uso en un proveedor de nube. Para ello, instalaremos el paquete cloud-init con alguna de sus dependencias extra:
`pacman -S --noconfirm --needed cloud-init python-passlib`
Y habilitaremos los dos servicios que ofrece, sin iniciarlos:
`systemctl enable cloud-init cloud-init-local`
El paquete cloud-init tiene una dependencia opcional, cloud-guest-utils:
`pacman -S --noconfirm --asdeps --needed cloud-guest-utils`
Esta dependencia es de especial relevancia, ya que una de sus utilidades permite aumentar el tamaño de la partición indicada hasta llenar todo el espacio que pueda en el disco. Si grabamos la imagen en una unidad cuyo tamaño es superior al disco grabado, podremos disponer del espacio sobrante con un par de comandos. El primero es growpart, que aplicaremos a la partición formateada en ext4. Imaginemos que es la segunda del disco:
`growpart /dev/nvme0n1 2`
Y luego, resize2fs adapta el sistema de archivos al nuevo tamaño:
`resize2fs /dev/nvme0n1p2`
Ten en cuenta que cloud-init generará una nueva clave para OpenSSH en el servidor la primera vez que se inicie, por lo que será necesario borrar la anterior del archivo known_hosts en los clientes. Si vas a distribuir tu imagen personalizada, instala y activa cloud-init.

### Instalación de Arch Linux desde una copia en ejecución

En este tutorial hemos usado pacstrap, arch-chroot y mkfstab, entre otras utilidades. Todas ellas se pueden obtener instalando el paquete arch-install-scripts. De hecho, es indispensable para realizar la instalación en la imagen:
`pacman -S --noconfirm --needed arch-install-scripts`
Si usas una copia instalada de Arch para preparar otro disco y no arrancas desde la imagen iso, recuerda desmontar tu propia partición swap antes de ejecutar el comando genfstab.

### Actualización de la caché de PackageKit

Al instalar paquetes, podemos ver que uno de los hooks falla porque systemd no se encuentra en ejecución en el entorno al que hemos entrado con chroot. Se puede ejecutar el siguiente comando para mantener la caché al día:
`/usr/lib/pk-offline-update`

### Preparación de la imagen para su distribución

A pesar de las velocidades actuales de conexión, 32 GB son muchos GB, y la mitad, también. Debemos preparar nuestra imagen para que ocupe el mínimo espacio posible al comprimirla, y por supuesto, comprimirla al acabar. Para ello, podemos hacer uso de las utilidades e4defrag y sfill. Los sistemas de archivos deben estar montados. En modo chroot, limpia las cachés de pacman y yay de todas las cuentas de usuario. El comando `yay -scc` desde la cuenta "usuario" debería ser suficiente si has seguido los pasos de este tutorial.
La utilidad e4defrag ya viene instalada en el sistema. Podemos aplicarla sobre la partición ext4 de la imagen y, regularmente, sobre nuestro propio disco duro:
`e4defrag /dev/loop0p2`
En cuanto a sfill, se instala con el paquete secure-delete, disponible únicamente en el AUR. Este paquete contiene utilidades para borrar archivos sin dejar rastro, grabando encima información aleatoria. Lo que nos interesa no es grabar datos aleatorios, sino ceros. De esa forma, se eliminarán los restos de ficheros ya borrados y quedará algo que ocupará mucho menos espacio al comprimirse. Para instalar secure-delete, desde una cuenta que no sea root, hacemos:
`yay -S --noconfirm secure-delete`
Ahora, le diremos a sfill que grabe ceros, haciendo una única pasada:
`sfill -f -z -l -l /mnt`
Y también en la partición de arranque:
`sfill -f -z -l -l /mnt/boot`
Teniendo la imagen completamente desmontada, ya podemos comprimirla: `xz -9 --extreme -T 0 archlinux.img`
El archivo resultante, aunque es grande, resulta mucho más manejable. Y el ratio de compresión no dejará indiferente a nadie.