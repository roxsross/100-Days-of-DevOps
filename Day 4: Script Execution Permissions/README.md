# Día 4: Permisos de Ejecución para Scripts

## 🎯 Objetivo

Otorgar permisos de **ejecución** a un script para que **todos los usuarios** puedan ejecutarlo.

---

## 🧠 Contexto

En entornos Linux es común compartir scripts entre equipos. Para que un script pueda ejecutarse, debe tener el permiso `x`. Configurarlo correctamente evita errores y problemas de automatización.

---

## 🛠️ Paso a paso

> ⚠️ **Importante:** Realizar esta tarea en el **App Server indicado por el reto** (por ejemplo: `stapp03`).

### 1️⃣ Conectarse al App Server

Ingresá al servidor correspondiente:

```bash
ssh <usuario>@<stapp0X>
```

Confirmá:

```bash
hostname
```

---

### 2️⃣ Verificar el archivo del script

Listá el archivo y sus permisos actuales:

```bash
ls -l /tmp/xfusioncorp.sh
```

Ejemplo de salida sin permisos de ejecución:

```
-rw-r--r-- 1 root root ... /tmp/xfusioncorp.sh
```

---

### 3️⃣ Otorgar permisos de ejecución a todos

Ejecutá uno de los siguientes comandos:

```bash
sudo chmod a+x /tmp/xfusioncorp.sh
```

O de forma explícita:

```bash
sudo chmod 755 /tmp/xfusioncorp.sh
```

---

### 4️⃣ Verificar permisos finales

```bash
ls -l /tmp/xfusioncorp.sh
```

Salida esperada:

```
-rwxr-xr-x 1 root root ... /tmp/xfusioncorp.sh
```

---

### 5️⃣ (Opcional) Probar ejecución

```bash
/tmp/xfusioncorp.sh
```

O como otro usuario:

```bash
sudo -u nobody /tmp/xfusioncorp.sh
```

---

## ❌ Errores comunes

* Dar permisos solo al owner (`700` o `744`)
* Cambiar permisos del directorio y no del archivo
* Ejecutar el comando en el servidor incorrecto

---

## ✅ Checklist final

* [x] Script existe en la ruta correcta
* [x] Permiso de ejecución habilitado
* [x] Ejecutable por todos los usuarios

---

> 📌 **Reto 100 Días de DevOps** – Día 4 completado. Un script sin `x` no corre 🚀
