# Día 2: Usuario Linux Temporal con Fecha de Expiración

## 🎯 Objetivo

Crear un usuario Linux **con fecha de expiración**, ideal para accesos temporales (contratistas, soporte puntual, labs).

---

## 🧠 Contexto

Las cuentas temporales reducen riesgos de seguridad. Definir una fecha de expiración evita que accesos queden activos por olvido.

---

## 🛠️ Paso a paso

### 1️⃣ Conectarse al servidor correcto

Ingresá al servidor indicado por el reto (App Server correspondiente):

```bash
ssh <usuario>@<servidor>
```

Confirmá:

```bash
hostname
```

---

### 2️⃣ Crear el usuario con fecha de expiración

Usá `useradd` con la opción `-e` (formato `YYYY-MM-DD`):

```bash
sudo useradd -m -e 2027-03-28 anita
```

> 📌 `-m` crea el directorio home del usuario.

Si el reto indica otro usuario/fecha (ej. `yousuf`, `2027-02-17`), ajustá el comando:

```bash
sudo useradd -m -e 2027-02-17 yousuf
```

---

### 3️⃣ (Opcional) Asignar contraseña

Solo si el reto lo requiere:

```bash
sudo passwd anita
```

---

### 4️⃣ Verificar que el usuario fue creado

```bash
id anita
```

Salida esperada (ejemplo):

```
uid=1006(anita) gid=1006(anita) groups=1006(anita)
```

---

### 5️⃣ Verificar la fecha de expiración

```bash
sudo chage -l anita
```

Debe verse algo como:

```
Account expires : Mar 28, 2027
```

---

## ❌ Errores comunes

* Crear el usuario en el **servidor incorrecto**
* Usar mal el formato de fecha
* Crear el usuario sin `sudo`
* Nombre con mayúsculas (ej. `Anita`)

---

## ✅ Checklist final

* [x] Usuario creado
* [x] Fecha de expiración configurada
* [x] Verificado con `chage -l`

---

> 📌 **Reto 100 Días de DevOps** – Día 2 completado. Seguridad también es saber cuándo quitar accesos.
