# Día 5: Instalación y Configuración de SELinux

## 🎯 Objetivo

Instalar SELinux y **dejarlo deshabilitado de forma permanente**, asegurando que el sistema arranque con SELinux en estado `disabled`.

---

## 🧠 Contexto

SELinux (Security-Enhanced Linux) agrega una capa extra de seguridad mediante políticas de control de acceso. En algunos laboratorios o entornos de testing se requiere deshabilitarlo permanentemente para evitar bloqueos inesperados.

> ⚠️ Importante: **deshabilitar SELinux correctamente no es lo mismo que ponerlo en permissive**.

---

## 🛠️ Paso a paso

> ⚠️ **Importante:** Este ejercicio se realiza **solo en el App Server indicado por el reto** (por ejemplo: `stapp01`).

### 1️⃣ Conectarse al App Server

Accedé al servidor correspondiente:

```bash
ssh <usuario>@<stapp0X>
```

Confirmá:

```bash
hostname
```

---

### 2️⃣ Instalar paquetes de SELinux

Ejecutá:

```bash
sudo yum install -y selinux-policy selinux-policy-targeted
```

> ℹ️ Si los paquetes ya están instalados, el sistema lo indicará y podés continuar.

---

### 3️⃣ Editar la configuración permanente

Abrí el archivo de configuración principal:

```bash
sudo vi /etc/selinux/config
```

Buscá la línea:

```text
SELINUX=enforcing
```

(o `permissive`)

Cambiála a:

```text
SELINUX=disabled
```

Asegurate de que **no esté comentada**.

Ejemplo final:

```text
SELINUX=disabled
SELINUXTYPE=targeted
```

Guardá y salí.

---

### 4️⃣ NO reiniciar el servidor

🚫 **No ejecutes `reboot`** a menos que el reto lo indique explícitamente.

El objetivo es que SELinux quede deshabilitado **para el próximo reinicio**.

---

### 5️⃣ Verificar la configuración

Validá que el archivo quedó correctamente configurado:

```bash
grep SELINUX= /etc/selinux/config
```

Salida esperada:

```text
SELINUX=disabled
```

> ⚠️ `getenforce` o `sestatus` pueden seguir mostrando otro estado hasta el reboot. Esto es normal.

---

## ❌ Errores comunes

* Usar solo `setenforce 0` (❌ cambio temporal)
* Olvidar editar `/etc/selinux/config`
* Reiniciar cuando el reto indica que no
* Hacer el cambio en el servidor incorrecto

---

## ✅ Checklist final

* [x] Paquetes SELinux instalados
* [x] `/etc/selinux/config` editado
* [x] `SELINUX=disabled` configurado
* [x] Sin reboot

---

> 📌 **Reto 100 Días de DevOps** – Día 5 completado. SELinux controlado, entorno bajo control 🔐
