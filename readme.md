
Qué quieren decir los bloques del compose.yml (docker-compose.yml)?

services: 

Agrupa y define todos los contenedores independientes que componen la infraestructura de la aplicación (web y db).

web:

Nombre del servicio que ejecutará la aplicación principal (Odoo). Actúa como identificador en la red interna de Docker.

image: odoo:19

Descarga e instancia la versión oficial 19 de Odoo desde Docker Hub para construir el contenedor

depends_on:

Establece el orden de inicio. Indica a Docker que debe arrancar primero el servicio db antes de iniciar web.

ports:

Mapea puertos entre el sistema anfitrión y el contenedor (puerto_host:puerto_contenedor). Permite acceder a Odoo abriendo http://localhost:8069 en el navegador.

volumes:

Enlaza carpetas locales del equipo (./volumesOdoo/...) con rutas internas del contenedor para que los datos (módulos, archivos adjuntos, sesiones) no se borren al apagar el contenedor.

environment:

Inyecta variables de entorno dentro del contenedor para configurar la conexión de Odoo hacia la base de datos (host, usuario y contraseña).

command:

Sobrescribe el comando de inicio predeterminado de Odoo. En este caso activa el modo desarrollo (--dev=all) y crea/inicializa la base de datos llamada odoo con los módulos base (-d odoo -i base)

db:

Nombre del servicio encargado de la base de datos PostgreSQL.

image: postgres:18

Descarga e instancia la imagen oficial de PostgreSQL en su versión 18 desde Docker Hub.

environment: (en db)

Inyecta las variables de entorno necesarias para inicializar la base de datos PostgreSQL (crea el usuario, contraseña y la base de datos por defecto odoo).

Qué es una imagen?

Una imagen es una plantilla de solo lectura que contiene el código, las dependencias, bibliotecas, variables de entorno y archivos de configuración necesarios para ejecutar una aplicación. Es el molde estático.


Qué es un contenedor?

Un contenedor es una instancia ejecutable e aislada de una imagen. Si la imagen es la clase en programación, el contenedor es el objeto instanciado que consume recursos de la máquina anfitriona (CPU, memoria).


Qué es un volumen?

Un volumen es un mecanismo de persistencia de datos independiente del ciclo de vida del contenedor. Permite almacenar datos en el sistema de archivos del anfitrión para evitar pérdidas al reiniciar o destruir contenedores.


Buscar alternativas en cuanto a eficiencia en utilización de recursos y como mejorar el uso de variables de entorno de forma segura (.env, secrets)

1-Sustituir Bind Mounts por Volúmenes Nombrados
En lugar de enlazar carpetas directamente del sistema de archivos local (./volumesOdoo/filestore:...), utilizar volúmenes gestionados internamente por el motor de Docker (volumes: odoo_data:).

2-Uso de Docker Secrets para Credenciales Sensibles
Reemplazar el bloque “environment:” con texto en claro por el mecanismo nativo de secrets de Docker.

3-Delimitación Explícita de Recursos
Qué es: Añadir el bloque deploy.resources en la definición de cada servicio para restringir los topes de CPU y memoria RAM.

4-Centralización mediante un archivo externo
Qué es: Mover todos los valores concretos (puertos, usuarios, contraseñas, nombres de BD) fuera del compose.yml e introducirlos en un archivo .env independiente que nunca se sube al repositorio Git.

5-Configuración de PostgreSQL Ligera en Desarrollo
Qué es: Modificar los parámetros de arranque del contenedor db ajustando la memoria compartida y forzando la escritura no sincrónica (fsync=off) únicamente durante la etapa de desarrollo local.
