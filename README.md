#  Proyecto: Infraestructura de Servicios en Linux

##  Estudiante

Carlos Daniel Hernández Jiménez
Universidad Internacional San Isidro Labrador (UISIL)

---

# 1. Arquitectura del sistema

El sistema fue implementado en una máquina virtual utilizando:

* Sistema operativo: Ubuntu 24.04
* Servidor web: Apache2
* Base de datos: MySQL
* Automatización: Bash scripting
* Programación de tareas: Cron

El entorno permite simular un servidor real con múltiples servicios funcionando de manera integrada.

---

# 2.  Instalación y configuración del servidor web

## Instalación de Apache

```bash
sudo apt update
sudo apt install apache2
```

## Verificación

Se accede desde el navegador:

```
http://localhost
```

Mostrando la página por defecto de Apache.

---

# 3.  Configuración de dominios y sitios web

Se configuraron dos sitios independientes:

| Sitio   | Dominio       |
| ------- | ------------- |
| Empresa | empresa.local |
| Curso   | curso.local   |

## Creación de directorios

```bash
sudo mkdir -p /var/www/empresa.local
sudo mkdir -p /var/www/curso.local
sudo chown -R $USER:$USER /var/www/
```

## Configuración de Virtual Hosts

Archivo:

```
/etc/apache2/sites-available/empresa.local.conf
```

```apache
<VirtualHost *:80>
    ServerName empresa.local
    DocumentRoot /var/www/empresa.local
</VirtualHost>
```

Archivo:

```
/etc/apache2/sites-available/curso.local.conf
```

```apache
<VirtualHost *:80>
    ServerName curso.local
    DocumentRoot /var/www/curso.local
</VirtualHost>
```

## Activación

```bash
sudo a2ensite empresa.local.conf
sudo a2ensite curso.local.conf
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

## Configuración de hosts

Archivo:

```
/etc/hosts
```

Agregar:

```
127.0.0.1 empresa.local
127.0.0.1 curso.local
```

---

# 4.  Contenido de los sitios web

Cada sitio contiene:

* Historia
* Servicios
* Contacto
* Descripción del proyecto

Ejemplo HTML:

```html
<h1>Empresa Innova Web</h1>
<p>Historia: Empresa de tecnología.</p>
<p>Servicios: Hosting, desarrollo web.</p>
<p>Contacto: empresa@gmail.com</p>
```

---
# 5.  Implementación de Base de Datos

## Creación de base de datos

```sql
CREATE DATABASE proyecto;
USE proyecto;
```

## Tablas

```sql
CREATE TABLE clientes (
 id INT AUTO_INCREMENT PRIMARY KEY,
 nombre VARCHAR(50),
 correo VARCHAR(50)
);

CREATE TABLE servicios (
 id INT AUTO_INCREMENT PRIMARY KEY,
 nombre VARCHAR(50),
 precio INT
);

CREATE TABLE proyectos (
 id INT AUTO_INCREMENT PRIMARY KEY,
 nombre VARCHAR(50),
 cliente_id INT
);
```

## Inserción de datos

```sql
INSERT INTO clientes (nombre, correo) VALUES ('Carlos', 'carlos@gmail.com');
INSERT INTO servicios (nombre, precio) VALUES ('Hosting', 10000);
INSERT INTO proyectos (nombre, cliente_id) VALUES ('Web Empresa', 1);
```

---

# 6.  Script de respaldo automático

Archivo: `backup.sh`

```bash
#!/bin/bash

FECHA=$(date "+%Y-%m-%d_%H-%M")
CARPETA="/home/carlos/backups"

mkdir -p $CARPETA

sudo mysqldump proyecto > $CARPETA/backup_db_$FECHA.sql

gzip $CARPETA/backup_db_$FECHA.sql
```

Permisos:

```bash
chmod +x backup.sh
```

---

# 7.  Automatización con Cron

```bash
crontab -e
```

Configuración:

```
0 */12 * * * /home/carlos/backup.sh
```

Esto ejecuta el respaldo cada 12 horas.

---

# 8.  Sistema de monitoreo

Archivo: `monitor.sh`

```bash
#!/bin/bash

FECHA=$(date "+%Y-%m-%d %H:%M:%S")
LOG="/home/carlos/monitor.log"

CPU=$(top -bn1 | grep "Cpu(s)" | awk -F',' '{print $1}' | awk '{print int($2)}')
RAM=$(free | grep Mem | awk '{print int($3/$2 * 100)}')
DISCO=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')

if [ "$CPU" -gt 80 ]; then
 echo "[$FECHA] ALERTA: CPU alto ($CPU%)" >> $LOG
fi

if [ "$RAM" -gt 80 ]; then
 echo "[$FECHA] ALERTA: RAM alta ($RAM%)" >> $LOG
fi

if [ "$DISCO" -gt 90 ]; then
 echo "[$FECHA] ALERTA: Disco alto ($DISCO%)" >> $LOG
fi

echo "[$FECHA] MONITOREO EJECUTADO" >> $LOG
```

---

# 9.  Logs generados

Ejemplo:

```
[2026-04-13 16:47:55] MONITOREO EJECUTADO
```

---

# 10.  Evidencias

* Apache funcionando
* Sitios web accesibles
* Base de datos creada
* Backups generados
* Logs de monitoreo

---

# 11.  Control de versiones

Se utilizó Git para almacenar:

* Scripts Bash
* Configuración
* Documentación

---

# 12.  Conclusiones

Se logró implementar un servidor Linux completo con:

* Servicios web funcionales
* Base de datos operativa
* Automatización mediante scripts
* Monitoreo del sistema

El proyecto permitió integrar múltiples tecnologías y aplicar conocimientos prácticos en administración de sistemas.

---


