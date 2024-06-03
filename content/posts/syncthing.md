---
title: "Syncthing, mi programa favorito para compartir archivos en grupo"
date: 2024-06-03T12:13:15+02:00
reply:
uri: /posts/syncthing
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

Imagina que quieres compartir una carpeta enorme con otra persona. Dejas la carpeta en tu nube favorita, y esperas pacientemente a que se sincronice con el servidor, un proceso que va a tardar horas. La otra persona comenzará a descargarla en cuanto se haya terminado de subir. Seguramente tardará menos horas que tú, porque su velocidad de descarga es algo mayor, pero le llevará también mucho tiempo. Para colmo, te has quedado sin espacio en tu cuenta y uno de los archivos no cabe. ¿Y si la transferencia se hiciera por P2P, directamente al ordenador de la otra persona, y el único límite estuviera en el espacio del disco duro?

Empecé a usar Syncthing allá por el 2016, cuando se puso de moda la sincronización P2P con BitTorrent Sync. A mí no me gustaba porque era algo muy centralizado, había que pagar por funciones extra y ocurrían cosas que no recuerdo con la privacidad. Así que me fui por la alternativa más libre, como siempre. Y desde entonces no la he soltado, ¡será por algo!

[Syncthing](https://syncthing.net) permite sincronizar carpetas entre dispositivos de una manera casi descentralizada. Las transferencias son de un ordenador a otro, cifrando los datos y aplicando SSL a la conexión con certificados y claves privadas en ambos extremos. Pero dejemos la parte técnica para más adelante, y empecemos por lo fácil.

## Instalación

Por defecto, el ejecutable de Syncthing para Windows funciona desde consola, almacena todos sus datos en appdata\local y deja una ventana abierta permanentemente, y eso queda feo. Además, no arranca solo al iniciar sesión. Hay que complementarlo con algo, y ese algo se llama Sync Trayzor.

Sync Trayzor se encarga de arrancar Syncthing, vigilar su actividad y enviar notificaciones cuando tienen lugar una serie de eventos, como cambios en las carpetas locales o dispositivos que se conectan y se desconectan. Se minimiza a la bandeja del sistema, y no deja ventanas molestas en medio. Una vez instalado y configurado, nos podemos olvidar de él.

La versión más reciente, compatible con sistemas de 32 y 64 bits, está aquí. Incluye instaladores y portables, y requiere .net Framework para funcionar: <https://github.com/canton7/SyncTrayzor/releases/latest>

Al instalarlo y ejecutarlo, abrirá una ventana con una barra de menú. Desde Archivo > Preferencias se puede configurar todo lo necesario. Las únicas cosas que yo suelo cambiar están en la primera pestaña. Me interesa que Sync Trayzor arranque con Windows, que se minimice y se cierre a la bandeja del sistema, que arranque minimizado y que no envíe notificaciones cuando se conecta o desconecta alguien (de verdad, pueden ser muy molestas).

En Mac no existe Sync Trayzor, hay que instalar Syncthing como una aplicación normal y corriente: <https://github.com/syncthing/syncthing-macos/releases/latest>

En Linux, para no variar, depende de la distribución. El equipo de Syncthing tiene un repositorio propio para Debian y derivados, y en Centos, Fedora y compañía Syncthing está como paquete en el repositorio EPEL. Una vez instalado, se puede activar del siguiente modo:
`systemctl enable syncthing@usuario`
`systemctl start syncthing@usuario`

Aunque es jugar con fuego y no lo recomiendo, no tiene por qué pasar nada si ese usuario es root, sólo manejamos nosotros el sistema y está bien asegurado.

En Android, Syncthing se puede [descargar desde la Play Store](https://play.google.com/store/apps/details?id=com.nutomic.syncthingandroid).

Ahora que ya hemos instalado Syncthing y está en funcionamiento, podemos empezar a jugar con él. No hay que preocuparse por las actualizaciones, llegarán de forma totalmente transparente y sin que nos enteremos (Windows), o se instalarán de la forma habitual, como cualquier otro paquete o aplicación (Mac, Linux y Android).

## Primeros pasos

Syncthing se maneja desde la web. Concretamente, desde [esta dirección](http://localhost:8384). Su interfaz es plenamente accesible y muy intuitiva. Necesita alguna mejora, como todo, pero son detalles insignificantes. Se puede dividir la ventana en las siguientes regiones:

* Menú principal.
* Notificaciones (si las hay).
* Carpetas.
* Este dispositivo.
* Otros dispositivos.
* Enlaces a webs externas.
* Diálogos varios (siempre se irán abajo del todo). Sólo aparecerán cuando los abramos nosotros.

### Cómo obtener nuestro ID de dispositivo

Por defecto, nuestro dispositivo tiene un identificador único. No va asociado a la máquina, sino a los datos empleados en la generación de nuestro certificado y clave. Por lo tanto, una copia de Syncthing podría migrarse con todos sus datos de un ordenador a otro mientras no se ejecute varias veces simultáneamente. El nombre legible del dispositivo se copia del nombre del equipo, pero podemos editarlo. Para ello, haz lo siguiente:

* En el menú principal, pulsa Acciones.
* Pulsa Ajustes.
* En el primer cuadro de edición que encontrarás, cambia el nombre de tu dispositivo.
* Pulsa Guardar.

Los cambios de nombre no se reflejarán en el resto de la red, salvo al vincular nuevos dispositivos. Para no causar confusión, se recomienda un nombre único, tan único y duradero como el identificador.

Para conectar con otras personas, tendremos que pasarles (siempre en privado) nuestro identificador, o pedirles el suyo. Para conocer tu identificador de dispositivo, haz lo siguiente:

* Pulsa Acciones.
* Pulsa Mostrar ID.
* Aparecerá el identificador, junto con un código QR. Copia el texto del identificador y dáselo a la otra persona.
* Pulsa Cerrar.

### Conexión con otro dispositivo

Si recibes un identificador de otra persona, busca y activa el botón "Añadir un dispositivo". En el formulario que se muestra, introduce el identificador en el primer campo. En el segundo, introduce el nombre que quieres darle, o déjalo vacío para tomar el nombre remoto elegido por la otra persona. Finalmente, pulsa Guardar. ¿Esperabas más? Hay más cosas que se pueden configurar de cada dispositivo, pero no las veremos todavía.

Por otro lado, si eres tú quien comparte su identificador, recibirás una notificación en la parte superior de la ventana cuando un dispositivo nuevo quiera conectar contigo. Confirma que quieres agregarlo, y se desplegará un diálogo idéntico al anterior. Los campos de identificador y nombre estarán rellenos, con todo listo para que pulses el botón Guardar.

### Creación de nuestra primera carpeta compartida

Hemos conectado con éxito con el otro dispositivo. En este momento, aparece como conectado, pero sin uso. Al pulsar intro sobre su encabezado, se desplegará una tabla debajo con la información del dispositivo. Se pueden ver ciertos datos sensibles, como la dirección ip del dispositivo, su versión de Syncthing y su sistema operativo. Por este motivo, lo ideal es conectar sólo con gente de mucha confianza. Ten en cuenta que tus "contactos" también podrán saber cuándo te conectas y te desconectas, y crear un perfil con tus hábitos frente al ordenador.

Ahora, vamos a compartir una carpeta. Para ello, busca y activa el botón "Agregar carpeta". Se desplegará un diálogo en la parte inferior.

De nuevo, vamos a rellenar dos campos:

* Etiqueta de la carpeta: el nombre que se verá en los otros dispositivos.
* Ruta de la carpeta: la ruta absoluta a la carpeta, tal y como aparece en la barra de direcciones del explorador de Windows. Por ejemplo, `D:\Carpeta compartida`.

Pero esta vez no pulsaremos el botón Guardar. Mientras agregabas dispositivos, seguramente viste una lista con varios enlaces antes del formulario. Es una lista con "pestañas" que muestran otras partes del diálogo. En el diálogo de agregar carpeta, esa lista también está presente. Pulsamos sobre un enlace llamado "Compartiendo".

Desde aquí, podremos seleccionar el dispositivo o dispositivos con los que se compartirá la carpeta. Marcamos los que queramos y esta vez sí, podemos pulsar Guardar.

¡Todo listo! Ahora, la carpeta se comportará como cualquier carpeta en la nube. Cuando dejemos un archivo, le llegará a la otra persona, y cuando lo borremos, desaparecerá. El navegador puede estar cerrado mientras hacemos un uso cotidiano de Syncthing.

Podemos añadir más dispositivos a la carpeta, pero hay un problema: si nuestro ordenador se apaga, no podrán comunicarse entre sí. De hecho, cualquier dato que llegue a la carpeta deberá pasar por nuestro equipo antes de propagarse. Somos el centro de comunicación entre dispositivos, y de transferencia de datos. No parece muy práctico, así que vamos a solucionarlo.

## Creación de un clúster radial

Si has llegado hasta aquí, has completado los primeros pasos con Syncthing, así que ¡enhorabuena! Vamos a desatar toda la potencia del P2P.

En los apartados anteriores hemos conectado dos dispositivos, que podríamos llamar A y B. Ahora, queremos agregar a la misma carpeta un dispositivo C. Repetimos todos los pasos anteriores y lo conectamos, pero sólo queda vinculado al dispositivo que lo enlazó, por ejemplo al dispositivo A. El dispositivo A puede comunicarse con B y con C, pero B no puede comunicarse con C directamente. Para que esto sea posible, el dispositivo A debe convertirse en un "presentador". Nos lo podemos imaginar en el centro de un círculo, presentando al resto de dispositivos entre sí, y estando estos en el exterior de la circunferencia. Si quieres indicar que uno de tus dispositivos enlazados es un presentador, haz lo siguiente:

* Busca el dispositivo que quieres modificar y despliégalo pulsando intro sobre su encabezado.
* Busca y activa el botón "Editar".
* En el diálogo que se muestra, pulsa intro sobre el enlace "Compartiendo" para activar la pestaña correspondiente.
* Busca la casilla "Presentador" y márcala.
* Explora esta parte del diálogo. Observa que se muestran todas tus carpetas, y están marcadas las que compartes con ese dispositivo.
* Pulsa Guardar.

A partir de ahora, cuando ese dispositivo agregue más dispositivos a la carpeta, aparecerán automáticamente en tu lista de dispositivos, y cuando los elimine desaparecerán. Esto trae múltiples ventajas:

* Si el presentador apaga su dispositivo, el resto de dispositivos seguirán comunicándose y sincronizando datos entre ellos.
* Las transferencias hacia un dispositivo se repartirán entre todos, consiguiendo que cada uno tenga que enviar menos información y consumir menos ancho de banda para completar la sincronización de la carpeta.

## Creación de un clúster en malla

Esta es una práctica posible, pero no recomendada. Consiste en marcar todos los dispositivos como presentadores en ambos extremos. Es decir, tanto el que invita como el invitado indican que el otro es un presentador. De esa forma, cualquiera puede vincular dispositivos a una carpeta existente y propagar por toda la red la información del nuevo dispositivo. Cuando un dispositivo se da de baja de la red y alguien lo elimina, la información sobre el mismo vuelve a propagarse, por lo que es imposible hacerlo desaparecer y nuestra lista puede acabar llena de dispositivos fantasma. Si Syncthing detecta que la casilla de presentador se marca en ambos lados, emitirá un mensaje de advertencia.

## Carpetas sólo enviar o sólo recibir

Por defecto, las carpetas que creamos son de tipo "Enviar y recibir". Esto significa que cualquiera puede modificar su contenido y propagar los cambios por toda la red. Pero a veces, nos puede interesar otro enfoque donde un dispositivo envíe algo y todos los demás lo reciban. Por ejemplo, imaginemos que tenemos los archivos de una web en nuestro disco duro, y todo preparado para sincronizar una carpeta con el servidor web. El servidor puede generar archivos temporales que no nos interesa recibir, pero queremos modificar la web y que los cambios queden reflejados allí casi al instante.

Al igual que con los dispositivos, las carpetas deben modificarse desde ambos extremos. Uno o más dispositivos pueden elegir que la carpeta sea de tipo "Sólo enviar", mientras otros que sea "sólo recibir". Esto no afecta a la hora de sincronizar el contenido, que se propagará por toda la red según corresponda. Para cambiar el tipo de una carpeta, haz lo siguiente:

* Pulsa intro en el encabezado de la carpeta para expandirla.
* Busca y activa el botón Editar.
* Activa el enlace "Avanzado" para desplegar la parte correspondiente del diálogo.
* Modifica el tipo de carpeta en el cuadro combinado correspondiente.
* Finalmente, pulsa Guardar.

Se pueden modificar los archivos en una carpeta de tipo sólo recibir, pero no se propagarán por la red. Syncthing detectará que hay contenido que no debería estar ahí, y ofrecerá desde la web la posibilidad de eliminar las diferencias. El resto de dispositivos también verán que la carpeta no está totalmente sincronizada.

## Syncthing y la privacidad

Como ya se ha mencionado anteriormente, las transferencias en Syncthing son seguras y van cifradas de extremo a extremo. Se realizan por conexiones SSL, y cada dispositivo dispone de su propio certificado y clave privada, generados la primera vez que arranca el programa. No obstante, hay algunas consideraciones de privacidad que se deben tener en cuenta:

* Cada dispositivo puede ver la dirección ip y el estado (conectado, desconectado, sincronizando) de todos los que tiene agregados. Usa Syncthing para compartir contenido sólo con personas de confianza.
* Ciertos datos pasan por los servidores del proyecto. Enseguida hablaremos más de ellos.

## Componentes de Syncthing

Si bien es cierto que las transferencias en Syncthing suelen ser de equipo a equipo, este programa sólo es totalmente descentralizado en redes de área local. Sin embargo, a diferencia de otras soluciones de la competencia, todos los actores involucrados en el funcionamiento de Syncthing se pueden replicar y modificar desde la configuración. Veamos cuáles son:

* Servidor de descubrimiento global: es el servidor que Syncthing usa para descubrir otros dispositivos por su identificador y conectar con ellos.
* Repetidor: aunque Syncthing soporta UPNP y gestiona de forma transparente la apertura de puertos, a veces se encuentra con algún router que no tiene activada esta tecnología o un firewall muy estricto. En esos casos, recurre a un repetidor para sincronizar las carpetas. Los repetidores suelen reducir notablemente la velocidad de transferencia.
* Servidor de lista de repetidores: un servidor al que Syncthing acude para buscar los repetidores disponibles, por si tuviera que usarlos.
* Servidor de actualizaciones: el lugar donde el programa busca nuevas versiones.
* Servidor de recopilación de datos y estadísticas: el lugar donde Syncthing envía datos de carácter anónimo para mejorar el producto, si se lo consentimos. La primera vez que abramos la web nos pedirá consentimiento mediante una notificación. Este consentimiento se puede modificar desde las opciones.

Se recomienda no modificar ninguno de estos servidores, a menos que sepas lo que estás haciendo y quieras montar una red completa y privada.

## Conclusiones

Existen muchas más cosas que se pueden cambiar en Syncthing. Se pueden limitar las velocidades de descarga y subida por dispositivo, configurar la interfaz web para que sea accesible desde el exterior, asignar un nombre de usuario y contraseña, cifrar carpetas sólo en algunos dispositivos, comprimir los datos antes de transferirlos, y mucho más. Sin embargo, son aspectos muy avanzados que no afectarán a la experiencia general de uso, y por lo tanto no hablaremos de ellos en este tutorial.

Muchas gracias por leer hasta aquí. Ahora, ¡a sincronizar carpetas!