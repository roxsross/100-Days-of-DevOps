## Desafío – Apache no disponible en App Servers (puerto 8083)

---

## 📌 Contexto

El sistema de monitoreo de **xFusionCorp Industries** reportó indisponibilidad del servicio **Apache (httpd)** en uno de los **App Servers** del **Stratos Datacenter**.

El requerimiento indicaba que:

* Apache debía estar **activo en todos los App Servers**
* El servicio debía escuchar **exclusivamente en el puerto 8083**
* No era necesario que sirviera contenido (HTTP 403 es válido)
* El foco estaba en **disponibilidad del servicio**, no en la aplicación

---

## 🎯 Objetivo del desafío

1. Identificar el App Server con Apache caído
2. Corregir la causa del fallo
3. Asegurar que Apache:

   * esté **activo**
   * escuche en **0.0.0.0:8083**
4. Validar desde el **Jump Host**

---

## 🧠 Estrategia de abordaje

Se aplicó un enfoque sistemático:

1. Verificar el estado de Apache en cada App Server
2. Detectar conflictos de puerto
3. Identificar procesos ocupando el puerto 8083
4. Liberar el puerto
5. Iniciar Apache y validar escucha
6. Verificar desde red externa (Jump Host)

---

## 🔍 Diagnóstico

### 1️⃣ Validación desde Jump Host

```bash
for h in stapp01 stapp02 stapp03; do
  echo "== $h ==";
  curl -sS -I --max-time 3 http://$h:8083 | head -n 1 || echo "DOWN"
done
```

Resultado inicial:

```
stapp01 → DOWN
stapp02 → OK
stapp03 → OK
```

📌 **stapp01** fue identificado como el host problemático.

---

### 2️⃣ Estado del servicio en stapp01

```bash
sudo systemctl status httpd --no-pager
```

Errores observados:

* `(98) Address already in use`
* `no listening sockets available`

---

### 3️⃣ Verificación de puerto ocupado

```bash
sudo netstat -lntp | grep ':8083'
```

Resultado:

```
127.0.0.1:8083  LISTEN  sendmail
```

✅ Se identificó que **sendmail** estaba ocupando el puerto requerido por Apache.

---

## 🔧 Solución aplicada

### 1️⃣ Detener y deshabilitar sendmail

```bash
sudo systemctl stop sendmail
sudo systemctl disable sendmail
```

También se deshabilitó el socket asociado:

```bash
sudo systemctl stop sendmail.socket 2>/dev/null || true
sudo systemctl disable sendmail.socket 2>/dev/null || true
```

---

### 2️⃣ Verificar puerto libre

```bash
sudo netstat -lntp | grep ':8083' || echo "8083 is free"
```

---

### 3️⃣ Verificar configuración de Apache

```bash
sudo grep "^Listen" /etc/httpd/conf/httpd.conf
```

Confirmado:

```
Listen 8083
```

Validación de sintaxis:

```bash
sudo apachectl configtest
```

---

### 4️⃣ Iniciar Apache

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

## ✅ Validación final

### 1️⃣ Verificar escucha local

```bash
sudo netstat -lntp | grep ':8083'
```

Resultado esperado:

```
0.0.0.0:8083 LISTEN httpd
```

---

### 2️⃣ Test local

```bash
curl -I http://localhost:8083
```

Resultado válido:

```
HTTP/1.1 403 Forbidden
```

---

### 3️⃣ Test desde Jump Host

```bash
for h in stapp01 stapp02 stapp03; do
  echo "== $h ==";
  curl -sS -I --max-time 3 http://$h:8083 | head -n 1 || echo "DOWN"
done
```

Resultado final:

```
HTTP/1.1 403 Forbidden (en todos)
```

---

## 🏁 Resultado final

✅ Apache activo en todos los App Servers
✅ Escuchando correctamente en el puerto **8083**
✅ Sin conflictos de puerto
✅ Servicio accesible desde red externa
✅ Monitoreo satisfecho

---

## 📘 Lecciones aprendidas

* Muchos fallos de Apache se deben a **conflictos de puerto**
* `sendmail` es un culpable común en entornos de laboratorio
* `403 Forbidden` confirma que Apache **está vivo**
* Siempre validar:

  * servicio
  * puerto
  * proceso
  * acceso remoto


> 📌 **Reto 100 Días de DevOps** – Día 14 completado. Automatización segura habilitada 🔑🚀
