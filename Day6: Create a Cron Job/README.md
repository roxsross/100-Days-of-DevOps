# Día 6: Creación y Configuración de Cron Jobs

## 🎯 Objetivo

Configurar una tarea programada (**cron job**) para automatizar la ejecución periódica de un comando en Linux.

---

## 🧠 Contexto

Los **cron jobs** permiten ejecutar comandos o scripts en intervalos definidos sin intervención manual. Son clave para backups, limpiezas, monitoreo y tareas operativas repetitivas.

En este reto, el cron debe ejecutarse como **root** y configurarse en **todos los App Servers**.

---

## 🛠️ Paso a paso

> ⚠️ **Importante:** Este ejercicio debe realizarse en **TODOS los App Servers** indicados por el reto (por ejemplo: `stapp01`, `stapp02`, `stapp03`).

---

### 1️⃣ Conectarse al App Server

Ingresá a cada servidor con el usuario asignado:

```bash
ssh <usuario>@<stapp0X>
```

Verificá:

```bash
hostname
```

---

### 2️⃣ Instalar el servicio cron (cronie)

Ejecutá:

```bash
sudo yum install -y cronie
```

---

### 3️⃣ Iniciar y habilitar el servicio crond

```bash
sudo systemctl start crond
sudo systemctl enable crond
```

Verificá que esté activo:

```bash
systemctl status crond
```

Debe verse:

```
Active: active (running)
```

---

### 4️⃣ Crear el cron job para root

Editá el crontab del usuario root:

```bash
sudo crontab -e
```

Agregá **exactamente** la siguiente línea:

```cron
*/5 * * * * echo hello > /tmp/cron_text
```

Guardá y salí.

---

### 5️⃣ Verificar el cron configurado

```bash
sudo crontab -l
```

Salida esperada:

```cron
*/5 * * * * echo hello > /tmp/cron_text
```

---

### 6️⃣ (Opcional) Verificar ejecución

Esperá hasta un múltiplo de 5 minutos y ejecutá:

```bash
cat /tmp/cron_text
```

Salida esperada:

```
hello
```

---

### 7️⃣ Repetir en todos los App Servers

Repetí los pasos **1 a 6** en cada servidor requerido por el reto.

---

## ❌ Errores comunes

* Crear el cron sin `sudo` (queda en el usuario equivocado)
* Editar `/etc/crontab` en lugar de `crontab -e`
* No iniciar el servicio `crond`
* Hacer el cambio solo en un servidor

---

## ✅ Checklist final

* [x] `cronie` instalado
* [x] `crond` activo y habilitado
* [x] Cron creado para root
* [x] Configurado en todos los App Servers

---

> 📌 **Reto 100 Días de DevOps** – Día 6 completado. Automatización segura habilitada 🔑🚀
