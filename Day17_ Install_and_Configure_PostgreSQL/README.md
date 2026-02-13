# 📅 Día 17: Configuración de PostgreSQL para Nueva Aplicación

---

## 🎯 Objetivo

Configurar el servidor **PostgreSQL** en la infraestructura Nautilus para preparar el entorno de base de datos requerido por una nueva aplicación.

Se debe:

* Crear un usuario de base de datos
* Crear una base de datos
* Asignar permisos completos al usuario sobre la base creada

⚠️ **No reiniciar el servicio PostgreSQL**

---

## 🧠 Contexto

El equipo de desarrollo de Nautilus desplegará una nueva aplicación en Stratos DC.

La aplicación utiliza **PostgreSQL** como motor de base de datos.
El servidor ya tiene PostgreSQL instalado y funcionando, por lo tanto solo debemos realizar configuración lógica (usuarios y base).

---

## 🖥 Infraestructura

| Servidor | IP            | Rol             |
| -------- | ------------- | --------------- |
| stdb01   | 172.16.239.10 | Database Server |

👤 Usuario del servidor: `peter`
🔐 Password: `Sp!dy`

---

# 🛠️ Paso a paso

---

## 1️⃣ Conectarse al servidor de Base de Datos

Desde el jump host:

```bash
ssh peter@stdb01.stratos.xfusioncorp.com
```

Ingresar password:

```
Sp!dy
```

---

## 2️⃣ Acceder a PostgreSQL

Elevar privilegios:

```bash
sudo -i
```

Cambiar al usuario postgres:

```bash
su - postgres
```

Ingresar a la consola:

```bash
psql
```

---

## 3️⃣ Crear el usuario de base de datos

```sql
CREATE USER kodekloud_joy WITH PASSWORD '8FmzjvFU6S';
```

---

## 4️⃣ Crear la base de datos

```sql
CREATE DATABASE kodekloud_db3;
```

---

## 5️⃣ Otorgar permisos completos

```sql
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db3 TO kodekloud_joy;
```

---

## 6️⃣ Salir de PostgreSQL

```sql
\q
```

Salir del usuario postgres:

```bash
exit
exit
```

---

# 🔍 Validación

Verificar que la base fue creada:

```bash
sudo -u postgres psql -l
```

Validar que aparece:

```
kodekloud_db3
```

También se puede verificar el owner:

```bash
sudo -u postgres psql -c "\l"
```

---

# ✅ Resultado Esperado

Debe existir:

* Usuario: `kodekloud_joy`
* Base de datos: `kodekloud_db3`
* Permisos completos sobre la base

Arquitectura final:

```
Aplicación
   ↓
PostgreSQL (stdb01)
   ↓
Base: kodekloud_db3
Usuario: kodekloud_joy
```

---

# 📋 Puntos Clave

✔ PostgreSQL ya estaba instalado
✔ No reiniciar el servicio
✔ Crear primero el usuario
✔ Luego la base
✔ Finalmente asignar privilegios

---

# 🚨 Problemas Comunes

## ERROR: role already exists

El usuario ya fue creado previamente.

---

## ERROR: database already exists

La base ya fue creada.

---

## Permission denied

No se ejecutó como usuario postgres.

---

# 🧠 Concepto DevOps

Separar:

* Infraestructura (Servidor)
* Motor de base de datos
* Configuración lógica (usuarios y permisos)

Es clave para mantener entornos reproducibles, seguros y escalables 🔥
