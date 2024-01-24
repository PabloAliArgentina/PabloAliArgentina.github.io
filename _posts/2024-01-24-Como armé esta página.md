---
layout: post
title:  "Cómo armé esta página con Jekyll + Git + GitHub"
---

Este post, de hecho mi primer post en esta página, lo estoy usando a la vez de prueba y de "tutorial" para quienes necesitan desarrollar un sencillo blog personal en GitHub, o incluso para mi mismo si tuviera que repetir el proceso.

Para entender este "how to" es necesario ya tener algún conocimiento de Git y de Github. Asumo que en general, por puro contexto, quien esté leyendo este post conocerá en mayor o menor profundidad. Pero de no ser así, hay vasto y muy buen material para aprender a usar esas maravillosas herramientas.

Jamás había usado Jekyll, pero me gustaron algunas particularidades del framework:

* La idea de tener un blog al estilo Wordpress pero generando un sitio estático, sencillo, fácil de mantener
* Que puediera ser desplegado dentro del propio GitHub, ya que en principio el blog será sobre cuestiones de desarrollo informático.
* Poder redactar post en formato *markdown* 

En el proceso de aprendizaje y búsqueda, tuve que decidir entre tres maneras de hacer el proceso

* Hacer un fork de algún otro proyecto o sitio hecho con jekyll (licencias mediante) y desde allí, modificar el contenido a mi gusto.
* Desarrollar desde cero un proyecto Jekyll en local y luego subirlo a GitHub.
* Desarrollar desde un template de Jekyll en local y luego subirlo a GitHub.

Decidí hacer uso de la tercera opción, me interesaba "jugar" con el sitio de manera local, tocar, cambiar, probar cosas de manera muy dinámica, pero tampoco quería embarrarme desde el principio en las cuestiones estéticas o aspectos profundos de diseño web.

Expongo a continuación los pasos que seguí:

---
---
---
## Instalar las herramientas necesarias para renderizar y desplegar localmente el sitio:


  **ATENCIÓN: Los pasos que daré son exclusivos para el sistema operativo Debian Linux. Para otros sistemas operativos, [la página oficial de Jekyll](https://jekyllrb.com/) provee la información necesaria y abarca muchos otros aspectos que no serán tratados en este tutorial.**



* Abrimos una terminal y desde allí instalamos las dependencias y los paquetes necesarios utilizando los siguientes comandos:

            sudo apt update
            sudo apt-get install ruby-full build-essential zlib1g-dev
            gem install jekyll bundler
            echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
            echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
            echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc source ~/.bashrc
            gem install jekyll bundler

--- 

* Creamos nuestro proyecto de blog:

        jekyll new myblog

Se creará una carpeta *myblog* con todos los archivos necesarios para desplegar el blog

---


* Entramos a la carpeta *myblog* (la cual consideraremos de aquí en mas nuestra carpeta raiz o root) y ya podemos desplegar el sitio en forma local

        cd myblog
        bundle exec jekyll serve

Si todo salió bien, ahora podemos abrir nuestro blog en el navegador con la siguiente url: http://localhost:4000 se verá algo como esto:

![image](/static/img/first_blog.png)

Excelente, ya tenemos nuestro blog vivito y coleando. Ahora podemos empezar a personalizarlo:

---
---
---
---

## Iniciar el control de versiones con Git

Ahora que tenemos el blog funcionando, es un buen momento para instalar el control de versiones.Situados en la carpeta raiz del proyecto ejecutamos:

        git init
        git add .
        git commit -m "Commit Inicial"

De aquí en mas el control de flujo de las versiones queda a criterio y entendimiento de cada uno. Cabe aclarar que nuestra carpeta de proyecto ya tiene configuradas sus correspondientes archivos .gitignore para excluir archivos que no queremos incluir en el seguimiento.


---
---
---
## Personalizar los datos del blog

Como comenté anteriormente, una de los puntos altos de Jekyll es la posibilidad de utilizar  markdown para formatear nuestros posteos o cualquier información visible del sitio. Por supuesto que también puede usarse html y hasta combinar ambos, tal cual sucede con los típicos archivos READ\.ME que solemos escribir en nuestros repos.

Comenzaremos con las cuestiones mas generales y mas estáticas de nuestro sitio, para ello nos dirigimos al archivo __config.yml_ ubicado en el directorio raiz de nuestro proyecto. Las primeras líneas, comentadas con #, nos indican justamente el caracter estático del archivo, donde se configurarán los aspectos globales del sitio, que se supone no deberemos modificar con frecuencia. Nos advierte también que tendremos que reiniciar nuestro servidor para que los cambios se vean reflejados en el sitio, cosa que no sucederá luego con el contenido variable de la página, como por ejemplo, los posteos. 
Unas líneas mas abajo ya aparecen parámetros que podemos empezar a personalizar:

        title: Your awesome title
        email: your-email@example.com
        description: >- # this means to ignore newlines until "baseurl:"
        Write an awesome description for your new site here. You can edit this
        line in _config.yml. It will appear in your document head meta (for
        Google search results) and in your feed.xml site description.
        baseurl: "" # the subpath of your site, e.g. /blog
        url: "" # the base hostname & protocol for your site, e.g. http://example.com
        twitter_username: jekyllrb
        github_username:  jekyll

Aquí no hay mucho secreto, ya que cada línea nos viene con una explicación de cual es su funcionalidad, es cuestión de cambiar los datos por los que nosotros querramos que se muestren en el blog, por ejemplo:

        title: El blog de Pablo Alí
        email: pavloali@gmail.com
        description: >-
        Crear, desarrollar, pensar, divertirse e innovar
        baseurl: ""
        url: "http://pabloaliargentina.github.io"
        #twitter_username: jekyllrb
        github_username:  pabloaliargentina

Nótese que miesntras quité los comentarios en las líneas para mayor claridad, por otro lado comenté la línea de twitter_username, ya que no quiero mostrar esa información. Una vez realizados los cambios guardamos el archivo.

Ahora vamos a la terminal donde está corriendo nuestro servidor local.
Detenemos el servidor presionando CRTL+C
Y lo volvemos a arrancar con:

        bundle exec jekyll serve

Nos fijamos por las dudas si el servidor no encontró errores, si todo va bien, nuestro blog debería haber cambiado.

![image](/static/img/first_blog_01.png)

Ahora vamos a hacer click arriba a la derecha y hacemos click en _about_

Nos aparecerá algo como esto:

![image](/static/img/first_blog_about.png)

Ahora nos dispondremos a generar nuestro propio _about_
Abrimos el archivo about.markdown en la raiz de nuestro proyecto.

Nos encontramos en las lineas iniciales una sección encasillada entre tres lineas consecutivas:

        ---
        layout: page
        title: About
        permalink: /about/
        ---

este encabezado se llama bloque _front matter_ y permite definir variables que le servirán al render para crear nuestro sitio, lo volveremos a ver mas adelante.
Por ahora nos limitaremos a reescribir la linea

        title: About

por

        title: Acerca de mi. 

Mas abajo estará el cuerpo del texto que querramos escribir. podemos ver ya algunas líneas con caracteres especiales de markdown. No entraremos en detalle acerca de Markdown, hay suficiente información en la web para aprender a utilizarlo convenientemente.
Borramos todo ese texto y lo resscribimos a nuestro antojo. Finalmente guardamos el archivo y vamos al navegador a recargar la página, notando los cambios efectuados.
Nótese que en esta ocasión, no tuvimos necesidad de reiniciar el servidor, ya que automáticamente detectó los cambios y volvió a renderizar el sitio.

![image](/static/img/first_blog_about_01.png)

Ya tenemos personalizados los datos del blog, Por supuesto que quedan muchas cosas por mejorar, sobre todo referidas al estilo de la página, pero por ahora dejaremos eso para mas adelante, ahora toca dar el siguiente paso.

---
---
---
## Escibir el primer posteo

Bueno, ahora toca la parte mas divertida, que es empezar a publicar posteos. Ya que esta será la tarea mas frecuente, uno esperaría que también sea la mas sencilla, y por suerte lo es.
Vayamos ahora a la carpeta _/\_posts/_
Allí nos encontraremos con un archivo con extensión markdown, que es ni mas ni menos que el archivo que genera el post de ejemplo de nuestro sitio: "Welcome to Jekyll". Si hacemos click en el enlace dentro del blog, nos llevará a mostrarnos el cuerpo de texto asociado a esa entrada.
De aquí en mas, cada posteo que querramos subir al blog consistirá simplemente en escribir un nuevo archivo .md o .markdown. El nombre de ese archivo debe tener el formato AAAA/MM/DD-título.md.
En mi caso, el archivo de ejemplo es _2024-01-24-welcome-to-jekyll.markdown_

Al abrirlo nos volvemos a encontrar con el encabezado o _front matter:_

        ---
        layout: post
        title:  "Welcome to Jekyll!"
        date:   2024-01-24 11:30:25 -0300
        categories: jekyll update
        ---

Nuestros posts deberán replicar al menos las variables layout y title para mostrarse correctamente en el blog. El resto del archivo no es mas que el cuerpo de texto de nuestro posteo.
Con esto en mente, hagamos nuestro propio posteo, creando un archivo nuevo, por ejemplo 2024-01-24-El-primer-post.md


_NOTA: Se recomienda utilizar algun editor de texto con soporte para archivos markdown, a fin de tener una vista previa en tiempo real de lo que se está escribiendo, en mi caso uso el propio vscode que ya tiene incorporada esa funcionalidad_

Dentro de nuestro archivo escribimos lo siguiente:

        ---
        layout: post
        title: El Primer Post
        ---

        # Y finalmente lo hemos logrado, tenemos nuestra primera publicación en el blog!!!!!

        O al menos eso es lo esperado...

Guardamos los cambios.

Aprovechamos y renombramos el archivo de ejemplo agregándole un caracter "_" al principio, nos quedaría: _2024-01-24-welcome-to-jekyll.markdown

Y ahora recargamos nuestra página

![image](/static/img/first_post_link.png) 

Nótese que el posteo de ejemplo ya no está en la lista, debido a que renombramos el archivo en cuestión.
Ahora  click en el enlace a nuestro post y...

![image](/static/img/first_blog_post.png)

Voilá! Nuestro primer post está publicado.
