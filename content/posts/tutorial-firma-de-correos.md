---
title: "Tutorial: firma digital de correos electrónicos"
date: 2024-06-02T15:10:00+02:00
reply:
uri: /posts/tutorial-firma-de-correos/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---
¡Hola a todos!
Como todo el mundo sabe, existen muchos mecanismos para proteger nuestra cuenta de correo frente a invitados no deseados: una contraseña fuerte, métodos de autenticación de doble factor, preguntas de seguridad, etc. También existen mecanismos a nivel de servidor que previenen la suplantación de identidad: registros SPF, DKIM y DMARC. Estos últimos sólo nos interesan a los que tenemos dominios y servidores, y logran que un atacante malintencionado que desee suplantar la identidad de un usuario legítimo acabe en el buzón de correo no deseado de la víctima. Y todo ello de forma transparente, sin que el usuario tenga que hacer nada. Pero ¿cómo podemos proteger nuestros correos de la manipulación? ¿Podemos hacer algo para que un intermediario no vea lo que contienen?
Ya nos conectamos usando conexiones https, indicando que los servidores imap y smtp van con conexión segura, pero a veces el intermediario malintencionado puede ser el propio servicio de correo.
Como anécdota, hace unos 15 años una vecina de Cuba vino a casa porque quería enviarle a su hermana unas fotos de sus niños. La cuenta de correo de la hermana era de un dominio cubano. Las fotos salieron bien de aquí en PDF, pero llegaron distorsionadas. No fue ninguna sorpresa, ya que nos avisaron de que el régimen tenía por costumbre intervenir y manipular correos a conveniencia. La solución del momento fue cifrar el PDF con contraseña, y transmitir la contraseña por una vía alternativa. Misteriosamente, al hacerlo así, llegaron bien. Si eso sucediera hoy, podríamos aplicar una solución mucho mejor: una firma digital.
Las firmas digitales en los correos no tienen nada que ver con las firmas que a veces vemos al pie de los mismos. Las primeras garantizan la autenticidad del origen y la integridad del contenido, y las segundas contienen lo que el remitente quiere que veamos. Existen dos métodos para firmar digitalmente un correo:

* S/MIME: es compatible con casi todos los programas de correo existentes. Consiste en una clave privada generada en local, y un certificado expedido por una autoridad de certificación. Los certificados más comunes, que incluyen la identidad del remitente, cuestan dinero. Sin embargo, los que veremos aquí son gratis y cambian el método de generación un poco.
* OpenPGP: se trata de un sistema descentralizado donde tú generas tus propias claves privada y pública. En vez de disponer de una autoridad de certificación que nos avale, son los propios destinatarios los que deciden cuánto confían en nuestra clave. La mayoría de programas de correo suelen necesitar algún tipo de complemento para trabajar con esta tecnología. Thunderbird, desde su versión 78, lleva ya el soporte integrado. Hablaremos de ello otro día si os interesa.

Hecha esta breve introducción, vamos a ver cómo obtener un certificado de firma de correo con Actalis y cómo instalarlo en Thunderbird. Actalis es una de las pocas entidades certificadoras que nos ofrece certificados gratuitos durante un año para todas las cuentas que queramos. Está reconocida por todos los navegadores y sistemas operativos. Yo llevo varios años con ellos, y gracias a la ayuda de un buen amigo y un poco de ruido, conseguimos que cambiasen su captcha inaccesible por Recaptcha.

## Obtención del certificado

1. Accede al [formulario de solicitud](https://extrassl.actalis.it/portal/uapub/freemail?lang=en). Está en inglés, pero eso no será un problema.
2. Introduce tu correo electrónico en el único cuadro de edición que hay en la página, resuelve el captcha y envía el formulario.
3. Recibirás un correo electrónico en italiano y en inglés con un código de verificación. Copia el código y pégalo en el cuadro de edición destinado a tal efecto.
4. Marca las casillas indicando que aceptas los términos del servicio y pulsa el botón que envía este nuevo formulario.
5. En la página de confirmación hay una contraseña. Cópiala y consérvala en un lugar seguro, la necesitarás más adelante.
6. Recibirás otro correo, en italiano y en inglés, con un archivo adjunto. En ese archivo van tanto la clave privada como el certificado, cifrados en formato pfx. La contraseña que lo descifra es la que copiaste en el paso anterior.
7. Descomprime el archivo zip en una carpeta de tu disco duro. Quedará el archivo con extensión .pfx.

## Instalación del certificado en Mozilla Thunderbird

El certificado que acabamos de recibir se puede abrir directamente e instalar en Windows con un asistente muy sencillo. Al hacerlo, quedará disponible en aplicaciones tales como Outlook, el correo de Windows y también Thunderbird. Sin embargo, en este dará algunos problemas. Por lo tanto, lo importaremos en el almacén de Mozilla, donde todo funciona bien. Así, además, al copiar el perfil a otros equipos o usarlo en modo portable, el certificado se vendrá con nosotros estemos donde estemos.

1. En Thunderbird, ve al menú Herramientas, y pulsa enter sobre Ajustes.
2. En el cuadro de búsqueda, escribe Certificados y pulsa enter de nuevo.
3. Tabula hasta el botón "Administrar certificados" y actívalo.
4. Asegúrate de que está seleccionada la pestaña "Sus certificados". Si no lo está, puedes activar el modo exploración de NVDA y seleccionarla con flechas y enter.
5. Pulsa el botón Importar. En el diálogo que se carga, busca el archivo .pfx extraído en la sección anterior.
6. Introduce la contraseña que descifra el archivo pfx. El certificado aparecerá en el árbol, listo para usar durante un año. Si es necesario, Thunderbird permite salvarlo junto con la clave privada, y cifrarlo con otra contraseña distinta.
7. Pulsa Aceptar para cerrar el diálogo de certificados, y control+w para cerrar las preferencias y regresar a la bandeja de entrada.

## Vinculación de la cuenta con el certificado

Ahora, debemos indicar a Thunderbird que nuestra cuenta de correo tiene asociado ese certificado. De lo contrario, no sabrá que puede usarlo.

1. Ve al menú Herramientas, y pulsa Intro sobre Configuración de la cuenta.
2. Selecciona tu cuenta en el árbol, si no lo estaba ya. Después, tabula hasta el botón Administrar identidades y actívalo.
3. En la lista, selecciona la identidad cuyo correo va asociado al certificado. Normalmente debería haber una sola identidad. Después, pulsa el botón Editar.
4. En el nuevo diálogo que se muestra, selecciona la pestaña "Cifrado de extremo a extremo". Puedes activar el modo exploración, navegar con las flechas y pulsar enter.
5. Al tabular hasta el grupo "S/MIME", verás un botón Seleccionar. Púlsalo para elegir tu certificado de firma.
6. En el cuadro combinado se mostrarán sólo los certificados que puedes usar para firmar correos con tu dirección. Es raro que haya más de uno, pero puede suceder. Elige el que más te convenga y acepta.
7. Thunderbird preguntará si quieres usar el mismo certificado para cifrar correos. Si es así, responde que sí.
8. El resto de casillas del diálogo se pueden configurar según tus gustos. A mí me gusta mantener los correos sin cifrar, pero que vayan firmados por defecto. Para ello, se puede marcar la casilla "Firmar mensajes sin cifrar" y marcar el botón de opción "Desactivar cifrado para mensajes nuevos". Si tenemos tanto S/MIME como OpenPGP disponibles, puede ser buena idea elegir la opción "Preferir S/MIME".
9. Pulsa los botones Cerrar o Aceptar en todos los diálogos, y control+w para cerrar la configuración de la cuenta.

## Creación de un correo firmado

Me encantaría extenderme en esta sección y llenarla de complicados pasos que justifiquen por qué casi nadie firma sus correos, pero es que no se puede. Marcando la casilla del paso 8 de la sección anterior termina todo. Todos los correos que enviemos irán firmados de serie. Cuando pase un año y el certificado caduque, Thunderbird fallará y dirá que el certificado ya no es válido. En ese momento, habrá que eliminarlo y repetir este tutorial paso a paso. Ahora bien, ¿qué pasa si la firma no está activada por defecto, o queremos desactivarla para que un correo se envíe sin firmar?
En ocasiones, sabemos que un correo va a ser manipulado sí o sí. Por ejemplo, cuando lo enviamos a una lista de correo. Los destinatarios recibirán una alerta indicando que el mensaje ha sido manipulado y pueden asustarse, por lo que nos interesa quitar la firma. En la ventana de redacción del mensaje, aparecerá un nuevo menú en la barra de menú llamado "Seguridad". En él, la opción que nos interesa se llama "Firmar digitalmente", y puede estar marcada o desmarcada. Si está desmarcada, el mensaje no se firmará.

## Verificación de la firma de un correo recibido

Al recibir un correo firmado, Thunderbird almacenará el certificado del remitente en su propio almacén, dentro de la sección "Otras personas". Esto nos vendrá bien más adelante para cifrar.
Si recorremos la ventana del mensaje con el tabulador, llegaremos a sus cabeceras. En ellas, un botón llamado S/MIME nos informará del estado de la firma y el cifrado, si lo hay. Si el mensaje ha sufrido alguna manipulación mientras viajaba por la red, lo sabremos al instante.

## Creación de un mensaje cifrado

Hasta ahora hemos visto cómo garantizar la integridad de un mensaje. Sin embargo, ¿qué pasa si también queremos que nadie, salvo el destinatario, pueda verlo? En ese caso, debemos cifrarlo. En la ventana de creación de mensajes, en el menú Seguridad, también hay una opción para cifrar, pero tiene truco: aunque esté marcada, sólo funcionará si el certificado del destinatario está en la sección "Otras personas" del almacén y sigue siendo válido. De lo contrario, Thunderbird avisará con un error.
El cifrado de mensajes es asimétrico: ciframos el contenido con la clave pública del destinatario (su certificado). El destinatario, por su parte, descifrará el mensaje con la clave privada correspondiente, que sólo tiene él. Nadie más podrá saber qué contiene. Del mismo modo, si quiere respondernos cifrando el mensaje, deberá tener nuestro certificado.

## Conclusiones

En este tutorial hemos visto una de las tecnologías más usadas a nivel empresarial para firmar y cifrar mensajes de correo electrónico: S/MIME. Hemos visto cómo obtener un certificado que durará un año, y que después puede seguir renovándose por otro año totalmente gratis. Sin embargo, la cosa no acaba aquí: no hemos hablado de OpenPGP, y sólo se ha explicado la configuración en Thunderbird. ¿Qué tal si lo ampliamos para hablar de OpenPGP y otros clientes de correo? Tal vez, próximamente, haya una segunda parte.