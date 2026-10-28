# PRACTICA INSTALACION WORDPRESS # 
**Aplicaciones web**

### Nombre: ### 
John Eric Millan Cobacho

### Curso: ###
2º SMX

##### Fecha: 28/10/2026 #####

---
---

## Apartado 1 ##

### Conexión por SSH y comprobación del servidor ###

Primero averiguamos la dirección IP del servidor ejecutando el siguiente comando:
```bash
hostname -I
```

Nos conectamos por SSH desde nuestro ordenador anfitrión (por ejemplo con PowerShell):
```bash
ssh alumno@192.168.56.25
```

Una vez dentro comprobamos que todo lo anterior sigue funcionando, en ambas debe poner **active**:
```bash
systemctl is-active apache2 mariadb
php -v
```

>Mostrara algo asi:
```bash
active
active
PHP 8.3.6 (cli) (built: Apr 15 2024 19:21:47) (NTS)
```

*Captura de la respuesta de los comandos:*

![Captura WordPress 1](./imagenes/WordPress-1.png)

**Pregunta 1:**

*¿Qué dos "puertas" (programas y puertos) usas para trabajar con el servidor en esta práctica y para qué sirve cada una?*

-Usamos la puerta **SSH (puerto 22)** con la terminal para administrar el servidor (instalar paquetes, copiar ficheros y configurar texto), y la puerta **HTTP (puerto 80)** con el navegador para ver y usar la parte gráfica de WordPress.

---

## Apartado 2 ##

### Dejar Apache con un único sitio ###

Desactivamos los dos sitios virtuales de la práctica anterior y dejamos únicamente el sitio por defecto activo:
```bash
sudo a2dissite sitio1.conf sitio2.conf
sudo a2ensite 000-default.conf
sudo apache2ctl configtest
sudo systemctl reload apache2
```

Comprobamos que solo hay un sitio activo y que la carpeta asignada es `/var/www/html`:
```bash
ls /etc/apache2/sites-enabled/
grep DocumentRoot /etc/apache2/sites-enabled/000-default.conf
```

>Mostrara algo asi:
```bash
000-default.conf
    DocumentRoot /var/www/html
```

*Captura de los comandos y de la página de Apache:*

![Captura WordPress 2A](./imagenes/WordPress-2A.png)

**Pregunta 2:**

*¿Qué diferencia hay entre desactivar un sitio con a2dissite y borrar sus ficheros? ¿Por qué ahora ves la página por defecto de Apache y antes veías sitio1?*

-Desactivar un sitio con **a2dissite** quita la configuración activa para que Apache deje de servirlo, pero mantiene los ficheros guardados en el disco por si nos hacen falta. Ahora se ve la página por defecto porque es el único sitio habilitado apuntando a la carpeta `/var/www/html`.

---

## Apartado 3 ##

### Crear la base de datos de WordPress ###

Entramos en MariaDB para crear la base de datos, el usuario y sus permisos:
```bash
sudo mariadb
```

Escribimos las siguientes órdenes:
```bash
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY '********';
GRANT ALL ON wordpress.* TO 'wp_user'@'localhost';
```

>Cada orden responderá de la siguiente forma:
```bash
Query OK, 1 row affected (0.000 sec)
```

Salimos con `exit` y comprobamos que el usuario funciona conectándonos con él:
```bash
mariadb -u wp_user -p wordpress
```

>Mostrara algo asi:
```bash
Welcome to the MariaDB monitor. Commands end with ; or \g.
Server version: 10.11.14-MariaDB

MariaDB [wordpress]>
```

Tabla con la función de cada cosa:

| Elemento | Nombre | Funcion |
|-----|------|------|
| Base de datos | wordpress | El armario donde WordPress guardara todos sus datos |
| Usuario | wp_user | La llave que utilizara WordPress para acceder |
| Permisos | GRANT ALL | Le da permisos totales sobre la base wordpress y ninguna otra |

*Captura de las órdenes y de la entrada con wp_user:*

![Captura WordPress 3](./imagenes/WordPress-3.png)

**Pregunta 3:**

*¿Por qué crees que WordPress usa su propio usuario wp_user con permiso solo sobre su base y no el usuario root de MariaDB?*

-Por seguridad. Si un atacante consiguiera acceder a la web de WordPress, solo tendría acceso a su base de datos y no a la totalidad del servidor MariaDB.

---

## Apartado 4 ##

### Descargar e instalar los ficheros de WordPress ###

#### 4.1 Descarga y descompresión ####
Descargamos el archivo comprimido de WordPress en la carpeta `/tmp` y lo descomprimimos:
```bash
sudo apt install wget -y
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
ls wordpress
```

#### 4.2 Colocación en la carpeta del servidor ####
Borramos la página por defecto de Apache y copiamos los ficheros de WordPress a la carpeta `/var/www/html`:
```bash
sudo rm /var/www/html/index.html
sudo cp -a /tmp/wordpress/. /var/www/html/
rm -r /tmp/wordpress /tmp/latest.tar.gz
ls /var/www/html
```

#### 4.3 Asignación de permisos ####
Cambiamos el propietario a **www-data** y ajustamos los permisos para carpetas y archivos:
```bash
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
ls -ld /var/www/html
```

>Mostrara algo asi:
```bash
drwxr-xr-x 5 www-data www-data 4096 Oct 28 09:00 /var/www/html
```

*Captura de la carpeta y los permisos de WordPress:*

![Captura WordPress 4](./imagenes/WordPress-4.png)

**Pregunta 4:**

*Explica qué hace cada una de las órdenes que has ejecutado. ¿Por qué hay que cambiar el propietario de la carpeta de WordPress a www-data? ¿Qué podría fallar si siguiera siendo tu usuario?*

- **sudo rm /var/www/html/index.html**: Borra la página por defecto de Apache para que no tape a WordPress.
- **sudo cp -a /tmp/wordpress/. /var/www/html/**: Copia todos los ficheros de WordPress a la raíz web.
- **sudo chown -R www-data:www-data /var/www/html**: Otorga la propiedad de las carpetas al usuario de Apache.
- **sudo find /var/www/html -type d -exec chmod 755 {} \;**: Pone permisos 755 a todas las carpetas.
- **sudo find /var/www/html -type f -exec chmod 644 {} \;**: Pone permisos 644 a todos los archivos.
- Hay que cambiarlo a **www-data** porque es el usuario con el que se ejecuta Apache; si perteneciera a nuestro usuario personal, WordPress no podría subir imágenes, instalar plugins ni guardar su configuración.

---

## Apartado 5 ##

### Terminar la instalación desde el navegador ###

Entramos en la IP del servidor desde el navegador `http://192.168.56.25` para completar la instalación con la parte gráfica:

| Paso | Pantalla | Funcion |
|-----|------|------|
| 1 | Idioma | Seleccionamos Español y le damos a continuar |
| 2 | Bienvenida | Pulsamos en ¡Vamos a ello! |
| 3 | Base de datos | Ponemos wordpress, wp_user y la contraseña creada |
| 4 | Confirmación | Pulsamos en Iniciar la instalación |
| 5 | Información del sitio | Ponemos el título del sitio, usuario administrador y correo |
| 6 | Éxito | Iniciamos sesión con la cuenta de administrador creada |

*Capturas de los datos de la base de datos, datos del sitio y del panel de administración:*

![Captura WordPress 5A](./imagenes/WordPress-5A.png)
![Captura WordPress 5B](./imagenes/WordPress-5B.png)

**Pregunta 5:**

*¿Qué partes de la instalación has hecho en la terminal y cuáles en el navegador? ¿Por qué el servidor no necesita tener interfaz gráfica?*

- En la terminal hemos realizado la preparación del servidor, creación de la base de datos, descarga de WordPress y asignación de permisos. En el navegador hemos introducido los datos del sitio y creado el usuario administrador. El servidor no necesita interfaz gráfica porque solo envía código PHP e hipertexto; es nuestro propio navegador en el cliente el que dibuja las páginas visuales.

---

## Apartado 6 ##

### Comprobacion del funcionamiento ###

Publicamos una entrada de prueba en la web y comprobamos desde la terminal que se guarda en MariaDB ejecutando:
```bash
sudo mariadb wordpress -e "SHOW TABLES;"
```

>Aparecerá el listado de tablas de WordPress:
```bash
+-----------------------+
| Tables_in_wordpress   |
+-----------------------+
| wp_commentmeta        |
| wp_comments           |
| wp_links              |
| wp_options            |
| wp_postmeta           |
| wp_posts              |
| wp_termmeta           |
| wp_terms              |
| wp_term_relationships |
| wp_term_taxonomy      |
| wp_usermeta           |
| wp_users              |
+-----------------------+
```

*Captura de la entrada publicada y de las tablas de la base de datos:*

![Captura WordPress 6A](./imagenes/WordPress-6A.png)
![Captura WordPress 6B](./imagenes/WordPress-6B.png)

**Pregunta 6:**

*¿Dónde guarda WordPress las entradas que escribes: en ficheros del servidor o en la base de datos? ¿Cómo lo has comprobado?*

- Las guarda en la base de datos, exactamente dentro de la tabla **wp_posts**. Lo hemos comprobado revisando las tablas creadas en MariaDB con el comando **SHOW TABLES;**.

---

## Apartado 7 ##

### Para que no se rompa si cambia la IP ###

Añadimos estas dos líneas al fichero de configuración para que acepte cualquier IP desde la que nos conectemos:
```bash
sudo nano /var/www/html/wp-config.php
```

>Añadimos antes de la línea `/* That's all, stop editing! */`:
```php
define( 'WP_HOME',    'http://' . $_SERVER['HTTP_HOST'] );
define( 'WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST'] );
```

Comprobamos que no hay ningún error de sintaxis en el archivo:
```bash
php -l /var/www/html/wp-config.php
```

>Mostrara algo asi:
```bash
No syntax errors detected in /var/www/html/wp-config.php
```

*Captura de las líneas dentro de wp-config.php y de la verificación de sintaxis:*

![Captura WordPress 7](./imagenes/WordPress-7.png)

---

## Apartado 8 ##

### Dos ajustes para trabajar cómodamente ###

#### 8.1 Direcciones "bonitas" (Enlaces permanentes) ####
Para que funcionen los enlaces permanentes sin error 404, permitimos a Apache leer el fichero `.htaccess`:
```bash
sudo nano /etc/apache2/conf-available/wordpress.conf
```

>Ponemos este contenido:
```apache
<Directory /var/www/html>
    AllowOverride All
</Directory>
```

Activamos el módulo y la configuración reiniciando Apache:
```bash
sudo a2enmod rewrite
sudo a2enconf wordpress
sudo apache2ctl configtest
sudo systemctl restart apache2
```

#### 8.2 Subir el límite de tamaño de subida ####
Para poder subir ficheros grandes de hasta 64 MB configuramos PHP:
```bash
printf 'upload_max_filesize = 64M\npost_max_size = 64M\n' | sudo tee /etc/php/8.3/apache2/conf.d/99-wordpress.ini
sudo systemctl restart apache2
```

*Captura de la configuración de Apache y del tamaño máximo de subida en el panel:*

![Captura WordPress 8](./imagenes/WordPress-8.png)

**Pregunta 8:**

*¿Por qué las direcciones "bonitas" dan error 404 si Apache no tiene permitido leer el fichero .htaccess?*

- Porque si Apache no lee el fichero **.htaccess**, no sabe aplicar las reglas de reescritura para redirigir las URL hacia el fichero principal `index.php`, provocando que el servidor web responda que la página no existe (error 404).

---

## Apartado 9 ##

### ¿Qué pasa si MariaDB se para? ###

Detenemos deliberadamente MariaDB para ver qué ocurre en el sitio web:
```bash
sudo systemctl stop mariadb
```

>Al recargar el navegador saldrá este error:
```bash
Error al establecer una conexión con la base de datos
```

Para volver a dejarlo en marcha escribimos:
```bash
sudo systemctl start mariadb
systemctl is-active mariadb
```

*Captura del error en el navegador y del servicio recuperado:*

![Captura WordPress 9A](./imagenes/WordPress-9A.png)
![Captura WordPress 9B](./imagenes/WordPress-9B.png)

**Pregunta 9:**

*Cuando WordPress muestra "Error al establecer una conexión con la base de datos", ¿qué dos cosas comprobarías y en qué orden?*

- 1º Usaría el comando **systemctl is-active mariadb** para ver si el servicio MariaDB está arrancado o se ha parado.
- 2º Probaría a entrar manualmente con **mariadb -u wp_user -p wordpress** para comprobar si el usuario y la contraseña siguen siendo válidos.

---

## Apartado 10 ##

### Comprobación final ###

Comprobamos que todo el stack LAMP y WordPress están funcionando correctamente:
```bash
systemctl is-active apache2 mariadb
php -v
ls /etc/apache2/sites-enabled/
sudo mariadb wordpress -e "SHOW TABLES;"
```

Tendra este estado:
| Pieza | del LAMP | Programa Estado |
|------|-------|------|
| Linux | Ubuntu 24.04 | Instalado |
| Apache | Apache2 (un sitio) | Funcionando |
| MariaDB | MariaDB 10.11 | Funcionando con la base wordpress |
| PHP | PHP 8.3 | Funcionando |
| WordPress | Instalado en /var/www/html | Funcionando y listo |



❓ ¿Qué dos "puertas" (programas y puertos) usas para trabajar con el servidor en esta práctica y para qué sirve cada una?

-Uno de ellos es SSH en puerto 22 por la terminal, sirve para administrar el servidor como por ejemplo instalar, copiar o configurar texto, y el otro es HTTP en puerto 80 en el navegador, sirve para usar WordPress para ver la web y el panel de control gráfico.


❓ ¿Qué diferencia hay entre desactivar un sitio con a2dissite y borrar sus ficheros? ¿Por qué ahora ves la página por defecto de Apache y antes veías sitio1?

-Lo que hace a2dissite es desactivar el fichero pero no es lo mismo que borrar porque puedes volver a activarlo, pero si lo borras no puedes volver a activarlo sin mas. Ahora se ve la pagina por defecto de apache porque se ha descativado sitio1.conf y sitio2.conf.
