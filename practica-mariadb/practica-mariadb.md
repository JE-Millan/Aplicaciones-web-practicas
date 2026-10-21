# PRACTICA INSTALACION MARIADB # 
**Aplicaciones web**

### Nombre: ### 
John Eric Millan Cobacho

### Curso: ###
2º SMX

##### Fecha: 16/10/2026 #####

---
---

## Apartado 1 ##

### Preparación del sistema ###

Primero hay que deshabilitar los dos sitios virtuales de la anterior practica y dejamos solo uno:
```bash
sudo a2ensite 000-default.conf
sudo systemctl reload apache2
```

Luego hay que actualizar la lista de paquetes y comprobar que Apache sigue funcionando, en la ultimas linea tiene que poner **active**:
```bash
sudo apt update
sudo apt upgrade -y
systemctl is-active apache2
```
*Captura de la respuesta de **systemctl is-active apache2***:

![Captura Mariadb 1](./imagenes/MariaDB-1.png)

---

## Apartado 2 ##

### Instalacion de MariaDB ###

Comando para instalar MariaDB:
```bash
sudo apt install mariadb-server -y
```

Ahora se comprueba la version instalada con este comando:
```bash
mariadb --version
```

>Mostrara algo asi:
```bash
mariadb Ver 15.1 Distrib 10.11.14-MariaDB, for debian-linux-gnu (x86_64)
```

*Captura de la version*:

![Captura Mariadb 2](./imagenes/MariaDB-2.png)

---

## Apartado 3 ##

### Comprobacion del funcionamiento ###

#### 3.1 Estado del servicio ####
El primer comando sirve para ver el estado de MariaDB y tiene que mostrar *active (running)* que significaria que esta funcionando, y el segundo comando mostrara *enabled* que significa que arrancará solo cada vez que el equipo se encienda:
```bash
sudo systemctl status mariadb
systemctl is-enabled mariadb
```

#### 3.2 Entrar en la base de datos ####

Con este comando se entra en la base de datos y mostrara un mensaje de bienvenida si todo va bien, eso indica que todo funciona correcto:
```bash
sudo mariadb
```

>Se vera algo asi:
```bash
Welcome to the MariaDB monitor. Commands end with ; or \g.
Server version: 10.11.14-MariaDB-0ubuntu0.24.04.1 Ubuntu 24.04

MariaDB [(none)]>
```

*Captura del estado de servicio y de la entrada en MariaDB:*

![Captura Mariadb 3A](./imagenes/MariaDB-3A.png)
![Captura Mariadb 3C](./imagenes/MariaDB-3C.png)

---

## Apartado 4 ##

### Comandos principales ###

Estos comandos se usan para manejar el servicio y su funcion:

| Comando | Funcion |
|-----|------|
| sudo systemctl start mariadb | Arranca el servicio |
| sudo systemctl stop mariadb | Detiene el servicio |
| sudo systemctl restart mariadb | Reinicia el servicio |
| sudo systemctl status mariadb | Muestra su estado |
| sudo systemctl enable mariadb | Lo arranca automáticamente al encender el equipo |

---

## Apartado 5 ##

### ¿Que pasa si MariaDB esta parado? ###

Pregunta:

Si WordPress no pudiera conectar con la base de datos, ¿qué dos comandos de este apartado usarías
para saber si el problema es que MariaDB está parado?

-Usaria los comandos **sudo mariadb** para ver si se puede acceder y **systemctl is-active mariadb** para ver si esta activo o si no lo esta.

Pregunta Apartado 6:

¿Por qué es peligroso dejar info.php en un servidor real? ¿Para qué sirve el paquete php-mysql?

- Es peligroso dejarlo porque hay informacion de la configuracion del servidor, *php-mysql* es como un puente de comunicación entre MariaDB o MySQL y PHP.
