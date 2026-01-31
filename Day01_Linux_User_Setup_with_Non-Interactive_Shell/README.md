
# Día 1: Creación de Usuario Linux con Shell No Interactivo

## 🎯 Objetivo

Crear un usuario Linux **sin acceso interactivo** para uso de servicios o automatización, evitando logins por shell.


---
## 🧠 Contexto

Los usuarios de servicio no deberían iniciar sesión de forma interactiva. Asignar un shell como `/sbin/nologin` o `/usr/sbin/nologin` mejora la seguridad y cumple buenas prácticas.

---

## 🛠️ Paso a paso

### 1️⃣ Conectarse al servidor

Accedé al servidor indicado por el reto con un usuario con privilegios sudo:

```bash
ssh <usuario>@<servidor>
```

Verificá:

```bash
hostname
```

---

### 2️⃣ Crear el usuario con shell no interactivo

Usá `useradd` indicando el shell nologin:

```bash
sudo useradd -s /sbin/nologin rose
```

> ℹ️ En algunas distros el path puede ser `/usr/sbin/nologin`. Si `/sbin/nologin` no existe, usá:

```bash
sudo useradd -s /usr/sbin/nologin rose
```

---

### 3️⃣ Verificar que el usuario existe

```bash
id rose
```

Salida esperada (ejemplo):

```
uid=1005(rose) gid=1005(rose) groups=1005(rose)
```

---

### 4️⃣ Verificar el shell asignado

```bash
grep '^rose:' /etc/passwd
```

Debe terminar en `nologin`, por ejemplo:

```
rose:x:1005:1005::/home/rose:/sbin/nologin
```

---

### 5️⃣ (Opcional) Crear home directory

Si el reto requiere directorio home:

```bash
sudo useradd -m -s /sbin/nologin rose
```

Verificar:

```bash
ls -ld /home/rose
```

---

## ❌ Errores comunes

* Crear el usuario con `/bin/bash` (❌ no cumple el objetivo)
* Usar `nologin` inexistente en la distro
* No usar `sudo`

---

## ✅ Checklist final

* [x] Usuario creado
* [x] Shell no interactivo (`nologin`)
* [x] Verificado en `/etc/passwd`

---


> 📌 **Reto 100 Días de DevOps** – Día 1 completado. Automatización segura habilitada 🔑🚀
