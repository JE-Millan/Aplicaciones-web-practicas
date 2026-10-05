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
 #### Pregunta: ####
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

![Captura 2](./imagenes/)

