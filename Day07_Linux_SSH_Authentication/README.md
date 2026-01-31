# Día 7: Configuración de Acceso SSH sin Contraseña (Password-less SSH)

## 🎯 Objetivo

Configurar **acceso SSH sin contraseña** mediante claves públicas, permitiendo conexiones seguras y automatizadas desde un servidor origen hacia servidores remotos.

---

## 🧠 Contexto

El acceso SSH por clave pública es una práctica fundamental en DevOps. Permite:

* Automatizar tareas remotas
* Evitar el uso de contraseñas
* Mejorar la seguridad

En este reto, el acceso debe configurarse **desde el jump host** usando el usuario `thor` hacia **todos los App Servers**.

---

## 🛠️ Paso a paso

> ⚠️ **Importante:** Todos los pasos se realizan como el usuario **thor** en el **jump host**.

---

### 1️⃣ Verificar el usuario actual

```bash
whoami
```

Debe mostrar:

```
thor
```

---

### 2️⃣ Verificar si ya existe una clave SSH

```bash
ls ~/.ssh
```

Si existen los archivos:

```
id_rsa  id_rsa.pub
```

podés continuar al paso 4️⃣.

Si **no existen**, continuá con el paso 3️⃣.

---

### 3️⃣ Generar un par de claves SSH

```bash
ssh-keygen -t rsa -b 2048
```

Cuando se solicite:

* Presioná **Enter** para la ubicación por defecto
* **Enter** para no usar passphrase

Las claves se crean en:

```
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

---

### 4️⃣ Copiar la clave pública a los App Servers

Copiá la clave pública al usuario correspondiente en cada servidor.

#### 🔹 App Server 1

```bash
ssh-copy-id tony@stapp01.stratos.xfusioncorp.com
```

Password:

```
Ir0nM@n
```

---

#### 🔹 App Server 2

```bash
ssh-copy-id steve@stapp02.stratos.xfusioncorp.com
```

Password:

```
Am3ric@
```

---

#### 🔹 App Server 3

```bash
ssh-copy-id banner@stapp03.stratos.xfusioncorp.com
```

Password:

```
BigGr33n
```

Cada comando debe mostrar algo similar a:

```
Number of key(s) added: 1
```

---

### 5️⃣ Verificar acceso SSH sin contraseña

Probá conectarte a cada servidor **sin que pida password**:

```bash
ssh tony@stapp01.stratos.xfusioncorp.com hostname
ssh steve@stapp02.stratos.xfusioncorp.com hostname
ssh banner@stapp03.stratos.xfusioncorp.com hostname
```

Salida esperada:

```
stapp0X
```

---

## 🆘 Alternativa si `ssh-copy-id` no está disponible

```bash
cat ~/.ssh/id_rsa.pub | ssh tony@stapp01.stratos.xfusioncorp.com 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

(Repetir para cada App Server.)

---

## ❌ Errores comunes

* Generar la clave como `root`
* Copiar la clave al usuario incorrecto
* Configurar solo un servidor
* Permisos incorrectos en `~/.ssh`

---

## ✅ Checklist final

* [x] Clave SSH generada como `thor`
* [x] Clave pública copiada a todos los App Servers
* [x] Acceso SSH sin contraseña validado

---

> 📌 **Reto 100 Días de DevOps** – Día 7 completado. Automatización segura habilitada 🔑🚀
