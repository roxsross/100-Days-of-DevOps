## Desafío – Despliegue de Nginx con SSL (App Server 2)

---

## 📌 Contexto

El equipo de **xFusionCorp Industries** debía preparar **App Server 2 (`stapp02`)** para el despliegue de una nueva aplicación web.

Como pre-requisito, se solicitó:

* Instalar y configurar **Nginx**
* Habilitar **HTTPS** usando un **certificado SSL self-signed**
* Validar acceso seguro desde el **Jump Host**

---

## 🎯 Objetivo del desafío

1. Instalar y habilitar **Nginx**
2. Mover el certificado SSL y la clave privada a una ubicación segura
3. Configurar Nginx para escuchar en **puerto 443**
4. Crear un `index.html` con contenido **Welcome!**
5. Validar acceso HTTPS con `curl`

---

## 🧠 Estrategia de abordaje

El enfoque fue:

* Usar rutas estándar para certificados (`/etc/nginx/ssl`)
* Mantener configuración simple (single server block)
* Validar con herramientas de red (`ss`, `curl`)
* No depender de navegador gráfico

---

## 🔧 Implementación

### 1️⃣ Instalación de Nginx

En **stapp02**:

```bash
sudo yum install -y nginx
sudo systemctl enable --now nginx
```

Validación:

```bash
sudo systemctl status nginx --no-pager
```

---

### 2️⃣ Preparación de certificados SSL

Los archivos originales estaban en:

```
/tmp/nautilus.crt
/tmp/nautilus.key
```

Se movieron a una ubicación apropiada:

```bash
sudo mkdir -p /etc/nginx/ssl
sudo mv /tmp/nautilus.crt /etc/nginx/ssl/
sudo mv /tmp/nautilus.key /etc/nginx/ssl/
sudo chmod 600 /etc/nginx/ssl/nautilus.key
```

---

### 3️⃣ Configuración de Nginx con SSL

Archivo editado:

```
/etc/nginx/nginx.conf
```

Server block configurado:

```nginx
server {
    listen              443 ssl;
    server_name         _;

    ssl_certificate     /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    root /usr/share/nginx/html;
    index index.html;
}
```

Validación de sintaxis:

```bash
sudo nginx -t
```

---

### 4️⃣ Creación del index.html

```bash
echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html
```

---

### 5️⃣ Reinicio del servicio

```bash
sudo systemctl restart nginx
```

---

## ✅ Validación final

### 1️⃣ Confirmar que Nginx escucha en 443

```bash
sudo ss -lntp | grep ':443'
```

Resultado esperado:

```
LISTEN 0 511 0.0.0.0:443 nginx
```

---

### 2️⃣ Test desde Jump Host

```bash
curl -Ik https://stapp02
```

Resultado esperado:

```
HTTP/1.1 200 OK
```

> ⚠️ El flag `-k` es necesario por tratarse de un certificado self-signed.

---

## 🏁 Resultado final

✅ Nginx instalado y activo
✅ HTTPS habilitado con SSL
✅ Certificados en ubicación segura
✅ Página accesible por HTTPS
✅ Validación completa desde Jump Host

---

## 📘 Buenas prácticas aplicadas

* Separación clara de certificados
* Permisos restrictivos en clave privada
* Validación previa de configuración (`nginx -t`)
* Uso de herramientas CLI para testing

> 📌 **Reto 100 Días de DevOps** – Día 15 completado. Automatización segura habilitada 🔑🚀
