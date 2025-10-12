# UNIDAD 4: INTRODUCCIÓN A DOCKER
## CONCEPTO Y CARACTARÍSTICAS

### ¿QUE ES DOCKER?
* Tecnología de virtualización ligera de software libre, también conocida como virtualización por contenedores.
* Son entornos de ejecución aislados, con su propio sistema de ficheros, su propia configuración de red y que acceden directamente a los recursos del anfitrión (memoria, procesador...)
* No tienen kernel propio, utilizan el del anfitrión.
* Su uso principal es para el despliegue de aplicaciones.

### ELEMENTOS BÁSICOS DE DOCKER
* **IMAGEN**: aplicación empaquetada, es el sistema de ficheros de nuestro contenedor
* **CONTENEDOR**: una imagen en ejecución
* Los contendores pueden conectarse a diferentes **REDES VIRTUALES** y tienen sus propios **VOLÚMENES** o sistemas de almacenamiento.

### ARQUITECTURA DE DOCKER
* DEMONIO: es el más importante (Docker Engine) estará siempre en ejecución y es el que gestiona los contenedores
* CLIENTE: linea de comandos que nos permite decirle al demonio que queremos hacer
* REGISTRO: repositorio de imágenes local (existe tambien uno público, Docker Hub, de donde podemos descargar)
* DOCKER SWARN: software orquestador de contenedores. Sirve para gestionar conjuntos de contenedores en un cluster de servidores. 
![Arquitectura Docker](https://miro.medium.com/v2/resize:fit:1400/1*09i6gCc0tBhSsXToKA7Cnw.png) 

* Alternativas a Docker: Podman (RedHat), Containerd (proyecto independiente basado en Docker), CRI-0 (RedHat, pensada para trabajar con Kubernetes), Pouch (AliBaba).
* Los orquestadores de contenedores, se usan para automatizar y facilitar muchas de las tareas que se llevan a cabo con contenedores, sobretodo cuando hay que gestionar grandes cantidades de estos. Ejemplos son: __Kubernetes, Apache Mesos o Docker Swarn__.
 
## INSTALACIÓN DE DOCKER
* [Documentación oficial](https://docs.docker.com/engine/install/)
* [Requerimientos mínimos Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
* Docker solo corre sobre distribuciones Linux, para Windows y MAC, se puede usar instalando el software **Docker Desktop**, que en realidad crea una máquina virtual Linux para ejecutar el demonio Docker sobre ella. Sin embargo el cliente si corre sobre el SO anfitrión.

* Existen varios métodos de instalación, en nuestro caso usaremos los repositorios apt de de Docker:

    1. Actualizamos los repositorios e instalamos los paquetes necesarios para instalar desde repositorios HTTPS:

            sudo apt-get update
            sudo apt-get install ca-certificates curl gnupg

    2. Añadir la clave GPG oficial de Docker:

            sudo install -m 0755 -d /etc/apt/keyrings
            curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
            sudo chmod a+r /etc/apt/keyrings/docker.gpg

    3. Añadimos el repositorio oficial de Docker:

            echo \
            "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
            "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
            sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    4. Instalamos Docker Engine:

            sudo apt-get update
            sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

* Para manejar Docker con un usuario sin privilegio, debemos añadir el usuario al grupo `docker`, para ello:

        sudo usermod -aG docker $USER

* Debes volver a iniciar una terminal con el usuario, o ejecutar el siguiente comando:

        su - $USER

* Y finalmente, comprobamos la versión con la que vamos a trabajar:

        docker --version

* Si queremos más información de las versiones de los componentes con los que vamos a trabajar, ejecutamos:

        docker version

* Para más información del sistema que hemos instalado podemos ejecutar:

        docker info

## EJECUCIÓN DE CONTENEDORES
* **HOLA MUNDO:**
* Todos los subcomandos referidos a contenedores, estan dentro del bloque "docker container", pero por abreviación, se omite "container" y solo se escribe "docker". Por ejemplo: **docker container run = docker run**

* Para ver todos los subcomandos con referidos a contenedores, podemos ejecutar:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4container
```

* Como mínimo, para crear un contenedor debemos indicarle una imagen. Esa imagen debe estar en nuestro REGISTRO (repositorio) local, si no es así, la buscara en el REGISTRO público (**Docker Hub**) y la descargará. Por ejemplo, existe la imagen pública "hello-world" que ejecuta un programa Hello World en el contenedor.

* Si no especificamos un nombre de contenedor al crearlo, la herramienta le asigna un nombre aleatorio.

* Todas las imágenes tienen definido un comando por defecto que ejecutarán al crear un contendor con ellas. Por ejemplo, en el caso de las imagenes de sistemas operativos, como ubuntu, por defecto ejecutan el comando  **`bash`**.

* **COMANDOS DE GESTIÓN BÁSICA:**
* Crear y ejecutar un contenedor:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4run hello-world
```
* Crear un contenedor sin ejecutarlo:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4create hello-world
```
* Iniciar un contenedor ya creado:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4start -a micontendor1
# La opción -a sirve para ver la salida que genera la ejecución de ese contenedor, en este caso el mensaje "Hello form Docker!"
```
* Pausar la ejecución de un contenedor:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4pause micontenedor
```
* Finalizar la ejecución de un contenedor:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4stop micontenedor
```
* Reiniciar la ejecución de un contenedor:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4restart micontenedor
```
* Renombrar un contenedor ya creado:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4rename micontenedor nuevo_nombre
```
* Borrar un contenedor:
```bash
    #Medocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4diante su ID:
    docker rm 64f44087771a
    # Mediante su nombre
    docker rm micontenedor1

    # Para forzar el borrado de un contenedor en ejecución, se usaría la opción -f
    docker rm -f micontenedor1
```
* Enlazar con la entrada/salida de un contenedor interactivo en ejecución:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4attach micontenedor
```
* Mostrar contenedores en ejecución:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4ps
```
* Mostrar contenedores creados:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4ps -a
```
* Mostrar imágenes de nuestro REGISTRO local:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4images
```
* Descargar una imagen del REGISTRO público:
```bash
#Por ejdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4emplo, descargar la imagen del SO Ubuntu
docker pull ubuntu
```
* Visualizar los pasos que se dan cuando creamos o ejecutamos contenedores:
```bash
#Dejamodocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4s lanzado este comando y se nos mostrarán logs detallados de todos las acciones que ejecutemos despues:
docker events
```

* **OPCIONES COMUNES SOBRE LOS COMANDOS BÁSICOS:**
* Indicar nombre del contenedor al crearlo:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4run --name contenedor1 ubuntu
```
* Para especificar que comando o comandos queremos que ejecute nuestro contenedor, se indican despues de la imagen:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4run --name contenedor1 ubuntu echo "Hola Mundo"
```

* Indicar hostname del contenedor al crearlo:
```bash
#Aunquedocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4 no es muy común utilizarlo, ya que los contenedores ofrecen un servicio y por lo general no nos conectamos a ellos. Es decir, no suelen tratarse como máquinas virtuales tradicionales a las que solemos conectarnos para realizar distintas tareas
docker -h contenedor_ubuntu
```

* Crear una sesión de terminal interactivo al crear un contendor:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4run -it --name contenedor2 -h cont2 ubuntu bash
```

* Borrar el contenedor despues de que termine su ejecución:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4run -it --rm --name contenedor3 -h cont3 ubuntu top
```

* **CONTENEDORES DEMONIO:** ejecutan procesos indefinidamente y de forma desatendida (no veremos su salida). Ejemplo:
```bash
# La opdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4ción -d indica que la ejecución es desatendida, es decir, no veremos su salida. Y la opción -c, permite especificar entre comillas, un listado de comandos para ejecutar:
docker run -d --name micontenedor ubuntu bash -c "while true; do echo hello world; sleep 1; done"

# Para ver la salida generada por un contenedor demonio
docker logs micontenedor

#Y si queremos verlo de forma continuada, podemos especificar
docker logs -f micontenedor

# Para detener un contenedor demonio
docker stop micontenedor

# Una vez detenido, podemos eliminarlo si queremos
docker rm micontenedor
```
* **CREACIÓN DE VARIABLES DE ENTORNO ASOCIADAS A UN CONTENEDOR:** Las variables de entorno son del tipo "clave:valor", y podemos crearlas al arrancar un contenedor. En el siguiente ejemplo, creamos la variable "USUARIO" y le asignamos el valor "prueba":
```bash
#Crea udocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4n contenedor interactivo (-it) con la imagen de ubuntu y define una variable de entorno (-e) para ese contenedor:
docker run -it --name micontenedor -e USUARIO=prueba ubuntu
```
* Para comprobar que realmente se ha creado la variable, dentro del BASH del contenedor escribimos **`env`** y veremos todas las variables d entorno.
* Las variables de entorno, suele utilizarse mucho para definir ciertos parámetros de configuración de las aplicaciones que se desplegamos mediante contenedores.

* **COMANDOS DE GESTIÓN MÁS AVANZADOS:**
Ejecutar un comando dentro de un contenedor en ejecución:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4 exec micontenedor ls
#o
docker exec micontenedor cat datos.txt
```
El comando anterior es muy utilizado para levantar sesiones interactivas de consola en contenedores ya creados, por ejemplo:
```bash
# Abrir una sesión interactiva en un contenedor MariaDB existente, ejecutando bash y pasándole usuario y contraseña para autenticarse
docker exec -it some-mariadb bash -c 'mysql -u root -p$MARIADB_ROOT_PASSWORD'
```

Copiar ficheros del Host al Contenedor o viceversa:
```bash
#Para cdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4opar del host al contenedor, se especifica el nombre del contenedor y el directorio donde lo queremos copiar
docker cp mifichero.txt micontenedor:/

#Para copiar del contenedor al host, debemos especificar el directorio destino, en este caso "." para indicar el directorio actual
docker cp micontenedor:/hora.txt .
```

Visualizar los procesos que se estan ejecutando dentro de un contenedor:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4top micontenedor
```

Obtener información detallada de un contenedor:
```bash
# Muestdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4ra en formato JSON el identificador, puertos abiertos, almacenamiento, tamaño, configuración de red, comandos, varibles de entorno... 
docker inspect micontenedor
```

Podemos formatear la salida del comando anterior, para que solo muestre ciertos parámetros:
```bash
# Para docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4mostrar el ID del contenedor
docker inspect --format='{{.Id}}' micontenedor

#Para mostrar todas las variables de entorno:
docker inspect --format='{{range .Config.Env}}{{println .}}{{end}}' micontenedor

#Para mostrar la dirección o direcciones IPs que tiene el contenedor:
docker inspect --format='{{range .NetworkSettings.Networks}}{{IPAddress}}{{end}}' micontenedor
```

Etiquetar contenedores, para filtrarlos o agruparlos
```bash
# USO ETIQUETADO

```

Limitar recursos utilizados por contenedores:
```bash
# La cantidad de CPU --cpus

# La cantidad de memoria --memory
```

Visualizar estadísticas de uso de recursos de un contenedor
```bash
# ESTADISTICAS

```

Modificar en tiempo real las limitaciones de recursos de un contenedor en ejecución
```bash
# MODIFICACIÓN LIMITACIONES

```


## DESPLEGAR UN SERVIDOR WEB APACHE CON DOCKER

Generalmente, nunca se va a utilizar la dirección IP del contenedor para acceder a los servicios que despliega, ya que esta es dinámica y puede ir cambiando dependiendo de que acciones realicemos en el contenedor.

Además, la dirección IP de un contenedor solamente es accesible desde el anfitrión. No sería alcanzable desde otros equipos de la red.

Por lo tanto, en este caso para acceder al servidor Web que nos proporcionará este contenedor, vamos a `mapear puertos`. Es decir, con la opción `-p` indicamos que cuando accedamos al puerto 8080 del host, esa petición se reenvíe al puerto 80 del contenedor, independientemente de la dirección IP.

Sería como si definieramos una regla de NAT en el Firewall del anfitrión.

```bash
# httpddocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4:2.4 es la imagen oficial del Servidor Web APACHE
docker run -d --name my-apache-app -p 8080:80 httpd:2.4
```

Para ver las redirecciones o nateos que se están haciendo en nuestro contenedor:
```bash
# Muestdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4ra tanto IPv4 como IPv6
docker port my-apache-app
```

Para conectarnos al servidor web bastará con ir al navegador del host y acceder a `https://localhost:8080`

## DESPLEGAR UN SERVIDOR DE BBDD CON DOCKER

## INTRODUCCIÓN A LAS IMAGENES EN DOCKER
Son plantillas de solo lectura que define el sistema de ficheros que va a tener el contenedor. También establece el comando por defecto que ejecutará ese contenedor, siempre que no se indique otro.

Por defecto en docker se usa el registro público de Docker Hub para descargar imágenes a nuestro registro local.

La nomenclatura para identificar una imagen es `usuario/nombre:etiqueta`. Las imágenes oficiales de docker vienen sin nombre de usuario para diferenciarlas.

Si no idicamos etiqueta al usar una imagen, por defecto se utiliza la etiqueta "latest" que se refiere a la última versión disponible de esa imagen.

Además de la version, las etiquetas pueden indicar la tecnología interna que se usa para servir esa aplicación y el SO base que usa esa imagen.

* **¿Como se organizan las imágenes?**
El sistema de ficheros (FS) de una imágen se llama sistema de ficheros de unión, ya que es el resultado de la unión de varias capas.

Cada capa representa una serie de directorios, que al juntarlos todos obtenemos nuestro FS final.

Las imagenes suelen partir de una capa base, que contiene los directorios y archivos básicos para su funcionamiento. Y luego cada una de las otras capas define un conjunto de diferencias respecto a la capa anterior (ficheros añadidos, modificados o eliminados).

Si tengo varias imágenes que tengan la misma capa base, solo se almacenará una vez en nuestra máquina y será compartida por todas esas imágenes. Esto podemos verlo con el comando:
```bash
# Nos ddocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4a la información del almacenamiento utilizado por docker
docker system df -v
```
Esto por ejemplo tambien ocurre cuando creamos contenedores con diferentes versiones de una misma imagen, muchas de sus capas, son compartidas.

Cuando creamos un contenedor, usa el FS de la imagen en `solo lectura`. Por tanto, cuando instalamos o modificamos algo en un contenedor, esto se hace en una nueva `capa de lectura/escritura` que se genera al crearlo, para así no modificar las capas de la imagen. De este modo, si creamos varios contenedores a partir de la misma imagen, todos ellos compartirar el mismo FS.

Esto hace que al crear un contenedor, su tamaño sea mínimo Esto podemos verlo con el comando:
```bash
# La opdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4ción -s nos muestra el tamaño del contenedor
# indicando el tamaño de la capa R/W del contenedor y
# el tamaño (virtual) del FS de la imagen usada
docker ps -a -s
```

Cuando borramos un contenedor, esta capa R/W desaparece, se perderían los datos de ese contenedor.

## GESTIÓN DE IMÁGENES
* Buscar imágnes en Docker Hub:
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4search nginx

# Si queremos ver lso posibles filtros
docker searhc --help
```

* Para borrar una imagen que ya no vayamos a usar:
```bash
# OJO! docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4La imagen no se podrá borrar mientras exista algun conenedor creado a partir de ella
docker rmi hello-world
```

* Ver la información detallada de una imagen, como su identificador, puertos que utiliza, arquitectura, SO, variables de entorno, el comando por defecto, las distintas capas...
```bash
docker docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4inspect nginx:stable
```

## DOCKER HUB

[Enlace Docker Hub](https://hub.docker.com/)

Su filosofía es bastante parecida a GitHub, ya que denominan a las imágenes como repositorios y usan practicamente los mismos comandos que este (pull, push, commit...)

Existe contenido confiable marcado con 3 tipos de insignias, como `imagen oficial de docker` (las mantiene el equipo de docker), `proveedores verificados` (suelen ser fabricantes reconocidos) y `proyectos de código libre esponsorizados`.

Podemos entrar a un repositorio par ver toda su información, etiquetas, versiones, como utilizarlo... Hay imagenes de SO, de servicios (apache, maraidb...), de lenguajes de programación o aplicaciones comerciales (Wordpress...)

Es imporante tener en cuenta la arquitectura en la que estamos trabajando, ya que dependiendo de eso deberemos buscar una imagen u otra.

## ALMACENAMIENTO DE DATOS EN DOCKER

Para no perder la información generada por los contenedores una vez sean borrados (por defecto se guarda en la capa R/W del contenedor), existen formas de almacenarla en el equipo anfitrión.

Un **VOLUMEN** es un directorio creado y gestionados únicamente por Docker, que se va a montar en un directorio interno del contenedor. Es decir, cuando guardemos información en ese directorio montado, se estaremos escribiendo en el directorio de nuestro Host que corresponde a ese volumen. Es decir, esa información no se perdería al borrar el contenedor.

El directorio del contenedor donde se montan es `/var/lib/docker/volumes/`.

Los volúmenes se pueden montar simultaneamente en diferentes contenedores (almacenamiento compartido). Es decir, lo que escribe un contenedor, puede ser leido más tarde por otro contenedor.

Como la información persiste al borrar el contenedor, podemos usar esa información para que sea utilizada por otro contenedor que creemos en el futuro. Además, gracias a esto son muy útiles para realizar copias de seguridad o migrar datos.

```bash
# Dos pdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4osibles formas de asociar un Volumen a un contenedor:
-v miweb:/usr/local/apache2/htdocs:ro

# ó
--mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs,ro
```

Otra opción es **BIND MOUNT**, que es un directorio o fichero propiedad del usuario, que podemos montar en un directorio interno del contenedor.

Se referencian por su ruta completa, aunque podemos crear un punto de montaje para más comodidad.

En este caso, no se gestionan desde el cliente Docker. Y se puede cambiar su contenido desde el anfitrión.

Son útiles cuando queremos compartir archivos de configuración o código fuente en nuestro contenedor.

```bash
# Dos pdocker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4osibles formas de asociar un Bind Mount a un contenedor:
-v /opt/web:/usr/local/apache2/htdocs:ro

# ó
--mount type=bind,src=/opt/web,dst=/usr/local/apache2/htdocs,ro
```

* **Ejercicio con Volúmenes:**
Todos los subcomandos de acciones con Volúmenes, estan bajo el bloque `docker volume`. Por ejemplo:

```bash
# Para docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4crear un volumen:
docker volume create mivolumen

# Para borrar un volumen:
docker volume rm mivolumen

# Mostrar los volumenes creados:
docker volume ls

# Mostrar información detallada de un volumen que hayamos creado:
docker volume inspect mivolumen
```

Para asociar un contenedor a un volumen que hayamos creado, un comando de ejemplo sería el siguiente. Aunque igualmente, si indicamos un volumen que no se haya creado previamente, docker lo creará en este momento:
```bash
docker run -d --name my-apache-app --mount type=volume,src=miweb,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4
```
Podemos comprobar el acceso al servidor web usando el navegador web o desde la consola, usando el comando `curl`:
```bash
curl http://localhost:8080
```

* **Ejercicios con Bind Mount:**

En este caso, no necesitamos crear volúmenes, si no que vamos a crear un directorio en el sistema de archivos del Host, donde añadiremos nuestro fichero `index.html`. Para ello, desde la consola del host:

```bash
mkdir web
cd web
echo "<h1>Hola Mundo</h1>" > index.html
```

A continuación, ya podemos montar ese directorio en un contenedor:
```bash
docker run -d --name my-apache-app --mount type=bind,src=/home/usuario/web,dst=/usr/local/apache2/htdocs -p 8080:80 httpd:2.4

# O si lo preferimos, sería lo mismo que:
docker run -d --name my-apache-app -v /home/usuario/web:/usr/local/apache2/htdocs -p 8080:80 httpd:2.4
```

En este caso, con bind mount, puedo modificar mi fichero HTML desde el anfitrión y al estar montado, esos cambios se verán directamente reflejados en mi servidor web.

## DESPLEGAR EL SERVICIO NEXTCLOUD EN UN CONTENEDOR CON ALMACENAMIENTO PERSISTENTE

Lo primero es ir a la página de documentación de la imagen de Nextcloud en [Docker Hub](https://hub.docker.com/_/nextcloud).

Allí buscamos el apartado `Persistent data` para saber cual es el directorio que debo montar en un volumen para no perder los datos que nos interesan generados por esta aplicación. En este caso sería `/var/www/html`.

Por lo tanto, si usamos volumenes el comando quedaría algo así:
```bash
docker run -d -p 8080:80 -v nextcloud:/var/www/html --name contenedor_nextcloud nextcloud:28.0.1
```

## DESPLEGAR EL SERVICIO MARIADB EN UN CONTENEDOR CON ALMACENAMIENTO PERSISTENTE

De nuevo, vamos a la página de documentación de la imagen que vamos a usar en [Docker Hub](https://hub.docker.com/_/mariadb).

En el caso de MariaDB, la información sobre alamcenamiento persistente, aparece bajo el título `Where to Store Data`. Ahí podemos leer que el directorio a montar es `/var/lib/mysql`

En este caso vamos a hacerlo con Bind Mount, y quedaría así:
```bash
docker run --name some-mariadb -v /opt/mariadb:/var/lib/mysql -e MARIADB_ROOT_PASSWORD=my-secret-pw -d mariadb:10.5
```

Si el directorio `/opt/mariadb` no existe en nuestro host, con la opción -v se creará en ese momento.

## OTROS USOS DEL ALMACENAMIENTO PERSISTENTE
Almacenamiento compartido entre distintos contenedores.

Por ejemplo, tener un servidor web en un contenedor, cuyo `index.html` deba ser actualizarse automáticamente leyendo de un repositorio GitHub o similar. Podemos tener un segundo contenedor, que modifique el código de ese HTML.

Otro ejemplo sería probar como funciona un mismo código en diferentes versiones del lenguaje de programación (vease el caso de PHP que hay isntrucciones que cambian su comportamiento entre la versión 5 y la 7, o entre la 7 y la 8).
