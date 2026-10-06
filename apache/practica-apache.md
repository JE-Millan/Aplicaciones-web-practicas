## Apartado 1 ##

### Preparación del sistema ###

Se actualiza primero la lista de paquetes y el sistema:
```bash
sudo apt update
sudo apt upgrade -y
```
Despues se comprueba la version del sistema:
```bash
lsb_release -a
```
*Captura versión de Ubuntu :*

![Captura 1](./imagenes/Captura-1.png)

---


## Apartado 2 ##

### Instalación de Apache ###

Ahora se instala Apache:
```bash
sudo apt install apache2 -y
```
Y se comprueba la versión que se ha instalado:
```bash
apache2 -v
```
 #### Pregunta 1: ####
 *¿Qué paquetes adicionales se han instalado como dependencias?*
 
 Se instalaron: 
- apache2-bin
- apache2-data
- apache2-utils
- libapr1
- libaprutil1
- ssl-cert
- mime-support


---

## Apartado 3 ##

### Comprobación del funcionamiento ###

#### 3.1. Se mira el estado del servicio: ####
```bash
sudo systemctl status apache2
```
#### 3.2. Luego los puertos en escucha: ####
```bash
sudo ss -tulpn | grep apache2
```
#### 3.3. Se prueba desde el terminal y desde el navegador: ####
```bash
curl -I http://localhost
```

*Capturas del estado del servicio y de la página por defecto en el navegador:*

![Captura 2](./imagenes/Captura-2.png)

![Captura 3](./imagenes/Captura-3.png)

#### 3.4. Firewall ####
```bash
sudo ufw status
sudo ufw allow 'Apache'
```
**Pregunta 2:**

*¿Qué diferencia hay entre los perfiles Apache, Apache Full y Apache Secure?¿Qué diferencia hay entre los perfiles Apache, Apache Full y Apache Secure?*

- **Apache:** Se usa para conexiones HTTP y los datos que viajan no estan protegidos, solo abre el puerto 80.
- **Apache Secure:** Se usa para conexiones seguras HTTPS y los datos que viajan estan protegidos y cifrados, solo abre el puerto 443.
- **Apache Full:** Es la que mas se usa, porque la gente puede entrar de la forma que quieran y el servidor los llevara a la opcion segura, abre el puerto 80 y el 443.

---

## Apartado 4 ##

### Comandos principales de administración ###

Estos son los principales comandos y sus explicaciones:

|-----|-----|
| h | h |
|----|----|
