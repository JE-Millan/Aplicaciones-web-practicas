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
*Captura de la respuesta de **systemctl is-active apache2***
![Captura Mariadb 1](./practica-mariadb/imagenes/MariaDB-1.png)

