---
title: "Cómo usar GeForce Experience con lectores de pantalla: una guía que jamás debió ser escrita"
date: 2024-10-25T14:25:00+02:00
reply:
uri: /posts/geforce/
categories: ["tutoriales"] # note, reply, anything else
tags:
draft: false
---

Actualización: a partir de noviembre de 2024, GeForce Experience queda obsoleto en favor de la nueva aplicación de NVIDIA, que es más accesible con lectores de pantalla. No obstante, se mantiene el contenido por motivos históricos.
Como se dijo en [una entrada anterior](/posts/driverpack), los controladores son una parte esencial de nuestro sistema que no debemos descuidar. Son los encargados de gestionar la relación entre el sistema operativo y los distintos componentes del equipo, y deben mantenerse actualizados siempre que sea posible. Esto lo sabe muy bien NVidia, que permite mantener al día los controladores de sus tarjetas gráficas mediante la aplicación GeForce Experience; una aplicación que antes era accesible, y ahora no. En este caso, no utilizo el término "accesible" como consultor de accesibilidad, sino como usuario. Si entramos a analizar el cumplimiento de las WCAG, incluso en su versión 2.0, estoy seguro de que aquello fallaría por todos lados. Así que en este caso empleo el término "accesible" como accesible para personas ciegas, una simplificación errónea pero conveniente para la historia que nos ocupa, y muy extendida en el colectivo.
GeForce Experience es una aplicación con una interfaz hecha a base de HTML, CSS y JavaScript, que se ejecuta sobre un control de navegador web en una ventana independiente. A priori esto es fantástico, porque un usuario de lector de pantalla puede recorrerla con los modos foco y exploración y acceder a todos sus contenidos. En un momento dado, alguien en NVidia tuvo la maravillosa idea de añadir atributos aria-hidden a todos los componentes. Los posibles porqués pueden ser muchos. Tal vez fue una configuración errónea de una biblioteca de terceros. Tal vez querían proteger su interfaz de programas automatizados, que aprovechan las APIs de las tecnologías de asistencia. Sea como fuere, NVidia demostró que la accesibilidad no le preocupaba demasiado. Nadie allí dentro pensó que las personas ciegas también usan tarjetas gráficas.

## Espera un momento, Jose, que yo tampoco había caído. ¿Para qué quiere un ciego una tarjeta gráfica?

Supongo que, si lo explico ahora, no tendré que explicarlo luego. Puede parecer que, como no vemos, no necesitamos tarjetas gráficas decentes, pero nada más lejos de la realidad. Hoy en día se usan para muchas otras cosas:

* Ejecutar videojuegos accesibles que no van a rebajar sus requisitos gráficos.
* Usar modelos de inteligencia artificial aprovechando la potencia de la tarjeta.
* Minar criptomonedas.
* Usar Jaws for Windows, un lector de pantalla que todavía depende de la tarjeta gráfica para obtener ciertos datos.
* Ejecutar software que requiere una resolución de pantalla determinada.
* Mejorar el rendimiento general del sistema.

Ahora que ya hemos abordado el problema y demás prejuicios, entremos en las soluciones que podemos aplicar para conseguir el objetivo: actualizar los controladores. Ya que la parte de iniciar sesión con una cuenta NVidia es accesible, nos olvidaremos de ella.

## Primer método: de memoria y a ciegas

Tras abrir GeForce Experience, NVDA nos anunciará que estamos en un "documento", como sucedería en cualquier página web. El único texto visible en ese documento es "common-crimsion-window", que no nos dice absolutamente nada. Para actualizar los controladores, procedemos del siguiente modo:

1. Activamos el modo foco. Es importante no pulsar tabulador antes de activarlo.
2. Si recorremos la ventana con tabulador, veremos dos botones etiquetados como "main-tab-label" antes de un botón "notificationButton". Pulsamos el segundo para ir a la pantalla de controladores. Importante, no confundir con los botones "mainTab", que también existen.
3. Si habíamos recibido la notificación de que había un controlador nuevo para instalar, es posible que la actualización ya esté descargada y preparada. Tabulamos hasta un botón llamado "express-driver-install" y lo activamos.
4. Aparecerá un diálogo del control de cuentas de usuario. Al confirmarlo, la instalación continuará por sí sola.
5. Mientras se instala, navegamos al escritorio y regresamos a GeForce Experience para que el foco pueda volver a navegar por la interfaz.
6. Sabremos que la instalación se acerca a su fin porque el controlador gráfico se reinicia. En este momento, NVDA puede decir una o varias veces la palabra "horizontal". Al acabar, aparecerá un diálogo modal, que logra atrapar el foco en su interior si llegamos hasta él.
7. Llegados a este punto, puede que GeForce Experience nos pida reiniciar el equipo, o puede que no. Lo sabremos por la cantidad de botones en el diálogo de finalización:
    * Si sólo aparece un botón "updatesInstallerDialog-2", podemos pulsarlo, cerrar GeForce Experience y dar el proceso por concluido hasta la próxima actualización.
    * Si aparecen los botones "updatesInstallerDialog-1" y "updatesInstallerDialog-2", el segundo reiniciará el equipo, así que cuidado! Para salir sin reiniciar, se debe pulsar el primero.

## Segundo método: destripar la aplicación para adaptarla a nuestras necesidades

Esto no siempre funciona, pero esta vez sí, así que aprovechemos! Al principio de la entrada dijimos que el responsable del problema es el atributo aria-hidden. Vamos a deshacernos de él. Para ello, necesitaremos acceso como administrador, y un editor de texto plano, como Notepad++, que pueda ejecutarse con privilegios elevados.

En primer lugar, abrimos el archivo `C:\Program Files\NVIDIA Corporation\NVIDIA GeForce Experience\www\index.html`. En él, buscamos la siguiente línea:

```
<div ui-view layout="column" flex aria-label="crimsion-window-space" aria-hidden="true"></div>
```

Cambiamos el atributo aria-hidden de true a false y guardamos. A continuación, editamos `C:\Program Files\NVIDIA Corporation\NVIDIA GeForce Experience\www\app.js`. En este caso, debemos usar la función de buscar y reemplazar con los siguientes textos:

* `aria-hidden=true` se sustituye por `aria-hidden=false`
* `aria-hidden="true" se sustituye por `aria-hidden="false"`

Tras guardar los cambios, la mayor parte de la interfaz de GeForce Experience volverá a ser accesible con lectores de pantalla. O al menos, tan accesible como puede serlo una interfaz sin textos alternativos adecuados y con otras etiquetas bastante mejorables. Es posible que sigan haciendo falta algunas de las instrucciones de la sección anterior.
Es importante tener en cuenta que esta adaptación no durará para siempre. Si GeForce Experience se actualiza, habrá que volver a repetirla. Por suerte, las actualizaciones de controlador llegan con más frecuencia que las de la aplicación, por lo que tendremos unos meses de respiro.

## Tercer método: pasar completamente de la aplicación y las cuentas NVidia

Si conoces el modelo exacto de tu tarjeta gráfica y sabes que hay una actualización, puedes visitar [esta página](https://www.nvidia.com/en-us/geforce/drivers/) y hacerte con los controladores actualizados. El instalador se puede manejar bastante bien, y su interfaz no oculta controles por defecto.

## Conclusión

En esta entrada hemos visto cómo parchear GeForce Experience o recurrir a métodos alternativos para actualizar los controladores de la tarjeta gráfica a ciegas. GeForce Experience resulta ser la alternativa más cómoda para actualizar, pero no es accesible con lectores de pantalla, lo que supone una barrera insalvable para la mayoría de usuarios. Se requieren conocimientos avanzados para llevar a cabo las prácticas explicadas en secciones anteriores, y nunca se deben utilizar documentos como este para justificar que un producto es accesible "porque los ciegos modifican los archivos, cambian un atributo aquí y otro allí y al final se las arreglan".

He sentido mucha pena y vergüenza (de la ajena, que no de la propia) mientras escribía esto. Pena porque sé que hay usuarios que tendrían que leer este documento, no lo harán y continuarán atascados en un problema sin solución aparente. Vergüenza porque, en pleno 2024, con una norma UNE en Europa y varias directivas, y con la Section 508 en Estados Unidos, NVidia sigue sin tener en cuenta la accesibilidad y se deja potenciales clientes fuera. Espero que en el futuro la situación cambie, y a ser posible para mejor.

¡Gracias por leer!