# Día 3: Deshabilitar Acceso SSH Directo como root

## 🎯 Objetivo

Deshabilitar el acceso SSH directo del usuario **root** en los servidores Linux para mejorar la seguridad.

---

## 🧠 Contexto

Permitir el login SSH como root aumenta el riesgo de ataques de fuerza bruta y dificulta la auditoría. La buena práctica es ingresar con un usuario normal y luego escalar privilegios con `sudo`.

---

## 🛠️ Paso a paso

> ⚠️ **Importante:** Este cambio debe aplicarse en **TODOS los App Servers** indicados por el reto (por ejemplo: `stapp01`, `stapp02`, `stapp03`).

### 1️⃣ Conectarse al App Server

Ingresá con el usuario asignado (no root):

```bash
ssh <usuario>@<stapp0X>
```

Confirmá el host:

```bash
hostname
```

---

### 2️⃣ Editar la configuración de SSH

Abrí el archivo de configuración del demonio SSH:

```bash
sudo vi /etc/ssh/sshd_config
```

Buscá la directiva (puede estar comentada):

```text
#PermitRootLogin yes
```

Cambiá o agregá la línea a:

```text
PermitRootLogin no
```

Guardá y salí.

---

### 3️⃣ Reiniciar el servicio SSH

Aplicá los cambios reiniciando el servicio:

```bash
sudo systemctl restart sshd
```

---

### 4️⃣ Verificar la configuración efectiva

Ejecutá:

```bash
sudo sshd -T | grep permitrootlogin
```

Salida esperada:

```
permitrootlogin no
```

---

### 5️⃣ Repetir en todos los App Servers

Repetí los pasos **1 a 4** en cada servidor requerido por el reto.

---

## ❌ Errores comunes

* Editar `/etc/ssh/ssh_config` (❌ archivo incorrecto)
* Dejar la línea comentada
* No reiniciar `sshd`
* Aplicarlo solo en un servidor

---

## ✅ Checklist final

* [x] `PermitRootLogin no` configurado
* [x] Servicio `sshd` reiniciado
* [x] Cambio aplicado en todos los App Servers

---

> 📌 **Reto 100 Días de DevOps** – Día 3 completado. Automatización segura habilitada 🔑🚀
