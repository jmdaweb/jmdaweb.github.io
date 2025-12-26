---
title: "Cómo trabajar con la pantalla apagada usando Virtual Display Driver"
date: 2025-12-26T10:30:00+01:00
reply:
uri: /posts/apagar-pantalla/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

La pantalla del ordenador es ese periférico que siempre está mostrando algo. Nunca falla, no puede permitírselo. Si lo hace, es porque falla el sistema entero. Es la piedra angular de cualquier ordenador, cualquier sistema, cualquier aplicación. He visto portátiles en casi perfectas condiciones desechados sólo porque la pantalla estaba rota. Así que suena absurdo trabajar con ella apagada. Tan absurdo que los fabricantes, con el paso de los años, han ido acabando con cualquier mínima posibilidad que pudiera existir de desactivarla. Porque ¿quién querría hacer algo así? Pues sin ir más lejos, un usuario ciego que no la necesita, como yo.

Trabajar con la pantalla apagada tiene múltiples ventajas. La principal, sin duda alguna, es la privacidad. Nos suele gustar usar el PC sin que alguien se acerque discretamente y vea lo que estamos haciendo. Y por desgracia, pasa con más frecuencia de la que parece. No es la primera vez que la gente me pregunta por qué no se ve nada y si el ordenador está realmente encendido, y no será la última. Pero como la privacidad no lo es todo, también tenemos otro buen motivo para querer apagarla: la exposición prolongada a una luz que no vemos y que, a largo plazo, acaba afectando a nuestra salud.

Hasta el descubrimiento que presento en esta entrada, y que hice ayer mismo, sólo conocía estas tres formas de desactivar la pantalla, cada una con sus pros y sus contras. Hablamos de la pantalla de un portátil. Evidentemente, en equipos de sobremesa, la solución es tan simple como darle al interruptor o tirar del cable:

* Bajar el brillo al 0%: reducir la luminosidad puede funcionar mejor o peor, dependiendo de la marca. La luz será menos dañina y el consumo de energía disminuirá, pero es posible que todavía se siga viendo el contenido con el que interactuamos.
* Utilizar una segunda pantalla que no está conectada: la mayoría de portátiles con Windows vienen con un conector para usar una pantalla adicional. Podemos pulsar la combinación `windows+p` y elegir "Sólo segunda pantalla". Si todo va bien, el sistema puede preguntar si queremos conservar o revertir los cambios, o efectuar el cambio de pantalla sin preguntar nada. El problema es que casi nunca va bien, ya que las tarjetas gráficas y los sistemas operativos más recientes son capaces de identificar si realmente hay una segunda pantalla conectada y, si no es así, el proceso no continúa. Otro punto en contra va relacionado con la resolución de la nueva pantalla, que tiene un impacto a la hora de navegar por el escritorio, la web, y otras aplicaciones. Como única gran ventaja, eso sí, tenemos que la pantalla principal se desactiva por completo, ahorrando energía. Si el equipo tiene conector VGA, o se usa un adaptador HDMI a VGA, este método funcionará siempre.
* Cortinas de pantalla: ciertos lectores de pantalla, como Jaws for Windows, NVDA y VoiceOver, incluyen una función llamada cortina de pantalla. Al activarla no se apaga la pantalla, sino que sus píxeles emiten de manera uniforme el mismo contenido, de tal manera que no se ve nada. Esto no reduce el consumo de energía, pero es una solución eficaz para preservar nuestra privacidad. No obstante, no está exenta de problemas. Y es que la cortina se activa a nivel de software, por lo que la pantalla tampoco se verá al realizar tareas tales como aplicar OCR, compartir en videollamada el escritorio o tomar capturas de pantalla. Las capturas aparecerán vacías, y el OCR no encontrará texto.

La solución óptima siempre ha sido utilizar una tarjeta gráfica simulada a la que se conecten pantallas simuladas. El problema es que no existía, y cuando existió no la conocimos... hasta hace poco. El nombre no es muy original, pero tampoco importa: se trata de [Virtual Display Driver](https://github.com/VirtualDrivers/Virtual-Display-Driver).

### Pasos para configurar Virtual Display Driver

En primer lugar, deberemos disponer de tres cosas importantes: el propio driver, una cuenta con privilegios de administrador, y un sistema Windows 10 o posterior X64 o ARM64. Para descargar el driver, se puede visitar la [página de la última release en GitHub](https://github.com/VirtualDrivers/Virtual-Display-Driver/releases/latest) y descargar el archivo vdd.control en la lista que hay bajo el botón desplegable "Assets". Por ejemplo, y para ahorrar tiempo, aquí está la [descarga directa de la versión 25.7.23](https://github.com/VirtualDrivers/Virtual-Display-Driver/releases/download/25.7.23/VDD.Control.25.7.23.zip), la más reciente en el momento en que escribo esta entrada.
Ahora que tenemos todo lo necesario, se pueden seguir estos pasos:

1. Extraemos el archivo zip descargado a una carpeta en la raíz de la unidad del sistema. Por ejemplo, `C:\virtualMonitor`.
2. Accedemos a la carpeta, y ejecutamos el archivo `VDD Control.exe`. Aparecerá una alerta de escritorio seguro.
3. La interfaz no es muy accesible, pero tiene un cuadro de texto con información de depuración, y algo que por suerte podemos usar: una barra de menú.
4. En la barra de menú, vamos a "Virtual Display Driver", luego a "System", y finalmente a "Install Driver". Aparecerá una advertencia de Windows preguntando si queremos instalar el controlador, ya que el firmante no es de confianza. Pulsamos Instalar, y al hacerlo, escucharemos que un nuevo dispositivo se conecta. ¡Nuestra nueva pantalla virtual está lista! Se puede desinstalar el driver del mismo modo, eligiendo la opción "Uninstall driver".
5. Pulsa el botón con una x para salir de la aplicación.

### Hora de apagar la pantalla

Una vez esté todo instalado, podemos pulsar `windows+p`. En la lista, observaremos que está seleccionada la opción "Duplicar". Bajamos hasta "Solo segunda pantalla", pulsamos intro, y la pantalla principal se apagará. Sin embargo, esta nueva pantalla comienza por defecto con una resolución de 800x600. Desde Configuración > Sistema > Pantalla, y tras seleccionar la pantalla 2, se puede modificar a un valor más adecuado, como 1920x1080, y conservar los cambios al acabar. Para encender la pantalla, es suficiente con pulsar `Windows+p` de nuevo, elegir en la lista "Solo pantalla de PC" y pulsar intro.

### Posibles problemas

Si el driver o la aplicación de control no funcionan, puede deberse a la falta de librerías en el sistema. Tal y como mencionan sus autores en GitHub, se deben instalar las versiones más recientes de Microsoft .Net Runtime y Microsoft Visual C++ Redistributable. Por ahora, no explicaremos cómo hacerlo, pero en el futuro se podría ampliar esta sección.

Si has llegado hasta aquí siguiendo todos los pasos, ahora puedes apagar la pantalla de tu portátil de la mejor forma posible: físicamente, ahorrando energía, sin impacto en las aplicaciones, y con la garantía de que se mantiene tu privacidad en todo momento. Es hora de que las miradas, ya sean discretas o indiscretas, vayan al lugar que les corresponde: los portátiles de los demás.

¡A disfrutar!