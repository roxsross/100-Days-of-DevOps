## Desafío – Apache no disponible por conflicto de puerto (Address already in use)

---

## 📌 Contexto

El monitoreo reportó que el servicio **Apache (httpd)** no estaba disponible en un puerto específico en un App Server.
Al intentar iniciar el servicio, Apache fallaba con error de bind:

* `Address already in use`
* `no listening sockets available`

---

## 🎯 Objetivo del desafío

* Identificar por qué **Apache no iniciaba**
* Resolver el conflicto sin modificar páginas (`index.html`)
* Asegurar que `httpd` quede **activo y funcionando**

---

## 🧠 Estrategia de abordaje

El enfoque seguido fue:

1. Confirmar el error desde `systemctl status`
2. Identificar qué proceso ocupa el puerto (`netstat`)
3. Detener el servicio conflictivo
4. Iniciar Apache
5. Validar conectividad desde el Jump Host

---

## 🔍 Diagnóstico

### 1️⃣ Estado del servicio httpd

```bash
sudo systemctl status httpd --no-pager
```

Se observó el mensaje clave:

* `(98) Address already in use: AH00072`
* `could not bind to address 0.0.0.0:<PUERTO>`

---

### 2️⃣ Identificación de proceso usando el puerto

Dado que `ss` no estaba disponible, se utilizó `netstat`:

```bash
sudo netstat -lntp | grep <PUERTO>
```

Resultado (ejemplo real observado en el entorno):

```
127.0.0.1:<PUERTO>  LISTEN  492/sendmail: accep
```

✅ Se identificó que **sendmail** estaba ocupando el puerto requerido por Apache.

---

## 🔧 Solución aplicada

### 1️⃣ Detener y deshabilitar sendmail

```bash
sudo systemctl stop sendmail
sudo systemctl disable sendmail
```

Además, por seguridad, se detuvo el socket si existía:

```bash
sudo systemctl stop sendmail.socket 2>/dev/null || true
sudo systemctl disable sendmail.socket 2>/dev/null || true
```

---

### 2️⃣ Confirmar que el puerto quedó libre

```bash
sudo netstat -lntp | grep <PUERTO> || echo "Port is free now"
```

---

### 3️⃣ Iniciar Apache

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl status httpd --no-pager
```

---

## ✅ Validación final

### 1️⃣ Confirmar que Apache está escuchando en el puerto

```bash
sudo netstat -lntp | grep <PUERTO>
```

### 2️⃣ Test desde Jump Host

```bash
curl http://stapp01:<PUERTO>
```

✅ Si responde con HTTP (200/403/301), el servicio está vivo.

> En estos labs, `403 Forbidden` también es válido: significa que Apache está funcionando.

---

## 🏁 Resultado final

✅ Se identificó conflicto de puerto
✅ Se liberó el puerto deteniendo `sendmail`
✅ Apache iniciado y habilitado
✅ Servicio accesible y validado desde red

---

## 📘 Lecciones aprendidas

* `Address already in use` casi siempre implica:

  * otro servicio escuchando el mismo puerto
  * configuración duplicada de Listen en Apache
* Siempre validar con:

  * `systemctl status httpd`
  * `netstat` / `ss`
  * `curl` desde host remoto

> 📌 **Reto 100 Días de DevOps** – Día 12 completado. Automatización segura habilitada 🔑🚀
