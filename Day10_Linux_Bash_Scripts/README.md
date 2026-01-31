
---

> 📌 **Reto 100 Días de DevOps** – Día 10 completado. Automatización segura habilitada 🔑🚀
# > 📌 **Reto 100 Días de DevOps** – Día 10 completado. Automatización segura habilitada 🔑🚀

## Desafío – Script de Backup de Sitio Web

---

## 📌 Contexto

El equipo de **xFusionCorp Industries** necesitaba automatizar el backup de un sitio web estático desplegado en **App Server 3 (`stapp03`)** dentro del **Stratos Datacenter**.

El requerimiento incluía no solo generar el backup local, sino también **copiarlo automáticamente al Nautilus Backup Server**, asegurando que el proceso fuera **no interactivo** (sin solicitud de password).

---

## 🎯 Objetivo del desafío

Crear un script Bash que cumpla con los siguientes requisitos:

* Comprimir el directorio del sitio web `/var/www/html/news`
* Generar un archivo `xfusioncorp_news.zip`
* Guardar el backup en `/backup` en App Server 3
* Copiar el backup al **Backup Server** en `/backup`
* No utilizar `sudo` dentro del script
* No solicitar password durante la copia remota
* Ubicar el script en `/scripts/news_backup.sh`

---

## 🧠 Estrategia de abordaje

Para cumplir los requerimientos se definió el siguiente enfoque:

1. Instalar las dependencias necesarias **fuera del script**
2. Utilizar `zip` para la compresión
3. Usar `scp` con autenticación por **SSH keys**
4. Ejecutar el script con el usuario del sistema
5. Mantener el script simple y reutilizable

Este enfoque permite automatizar el proceso y facilita su ejecución desde cron o pipelines.

---

## 🔧 Preparación del entorno

### 1️⃣ Instalación del paquete zip (fuera del script)

```bash
sudo yum install -y zip
```

---

### 2️⃣ Creación del directorio de scripts

```bash
sudo mkdir -p /scripts
sudo chown banner:banner /scripts
```

---

### 3️⃣ Configuración de acceso SSH sin password

En **App Server 3**:

```bash
ssh-keygen -t rsa
ssh-copy-id clint@stbkp01
```

Esto permite ejecutar `scp` sin interacción manual.

---

## 📄 Script creado

### Ubicación

```
/scripts/news_backup.sh
```

### Contenido del script

```bash
#!/bin/bash

BACKUP_DIR="/backup"
SOURCE_DIR="/var/www/html/news"
BACKUP_FILE="xfusioncorp_news.zip"

zip -r "${BACKUP_DIR}/${BACKUP_FILE}" "${SOURCE_DIR}"

scp "${BACKUP_DIR}/${BACKUP_FILE}" clint@stbkp01:/backup/
```

### Permisos

```bash
chmod +x /scripts/news_backup.sh
```

---

## ✅ Validación del script

Ejecución manual:

```bash
/scripts/news_backup.sh
```

Validaciones realizadas:

* ✔ El archivo `xfusioncorp_news.zip` se crea correctamente en `/backup`
* ✔ El archivo se copia al Backup Server sin pedir password
* ✔ El script se ejecuta sin utilizar `sudo`

---

## 🏁 Resultado final

✅ Script de backup funcional y automatizado
✅ Backup almacenado localmente y en el Backup Server
✅ Requerimientos del desafío cumplidos completamente

---

## 📘 Buenas prácticas aplicadas

* Separación entre **preparación del entorno** y **lógica del script**
* Uso de autenticación por clave para automatización
* Script simple, claro y fácil de mantener
* Compatible con futuras ejecuciones programadas (cron)

