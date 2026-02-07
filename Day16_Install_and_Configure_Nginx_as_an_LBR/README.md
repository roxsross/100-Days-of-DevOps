# Día 16: Instalación y Configuración de Nginx como LBR (Load Balancer)

## 🎯 Objetivo

Configurar un servidor **Nginx como Load Balancer (LBR)** dentro de la infraestructura Nautilus para distribuir el tráfico HTTP entre múltiples servidores de aplicación, mejorando disponibilidad, resiliencia y rendimiento.

---

## 🧠 Contexto

El tráfico hacia una aplicación administrada por el equipo Nautilus ha crecido considerablemente, provocando degradación del servicio.

Como solución, se decidió migrar la aplicación hacia una arquitectura de **alta disponibilidad en Stratos DC**, utilizando un **Load Balancer HTTP** basado en Nginx.

La migración ya está realizada y solo resta configurar el servidor LBR.

---

## 🖥 Infraestructura

| Servidor | IP            | Rol           |
| -------- | ------------- | ------------- |
| stapp01  | 172.16.238.10 | App Server    |
| stapp02  | 172.16.238.11 | App Server    |
| stapp03  | 172.16.238.12 | App Server    |
| stlb01   | 172.16.238.14 | Load Balancer |

Apache en los App Servers escucha en:

```
Puerto 8084
```

---

# 🛠️ Paso a paso

---

## 1️⃣ Verificar Apache en los servidores de aplicación

Conectarse a cada servidor App.

---

### 🔹 stapp01

```bash
ssh tony@stapp01.stratos.xfusioncorp.com
```

```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
sudo systemctl status httpd
```

Validar puerto:

```bash
sudo ss -lntp | grep httpd
curl -I http://localhost:8084
```

---

### 🔹 stapp02

```bash
ssh steve@stapp02.stratos.xfusioncorp.com
```

```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
sudo systemctl status httpd
```

---

### 🔹 stapp03

```bash
ssh banner@stapp03.stratos.xfusioncorp.com
```

```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
sudo systemctl status httpd
```

---

## 2️⃣ Configurar firewall en los App Servers

Si firewalld está activo:

```bash
sudo firewall-cmd --permanent --add-port=8084/tcp
sudo firewall-cmd --reload
```

---

## 3️⃣ Conectarse al servidor LBR

```bash
ssh loki@stlb01.stratos.xfusioncorp.com
```

---

## 4️⃣ Instalar Nginx

```bash
sudo yum install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

---

## 5️⃣ Configurar Load Balancer

Editar archivo principal:

```bash
sudo vi /etc/nginx/nginx.conf
```

---

## ✨ Configuración Final

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    upstream nautilus_app {
        server 172.16.238.10:8084;
        server 172.16.238.11:8084;
        server 172.16.238.12:8084;
    }

    server {
        listen 80;
        server_name _;

        location / {
            proxy_pass http://nautilus_app;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

---

## 6️⃣ Validar configuración

```bash
sudo nginx -t
```

Resultado esperado:

```
syntax is ok
test is successful
```

---

## 7️⃣ Reiniciar servicio Nginx

```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```

---

## 8️⃣ Validar conectividad backend

Desde LBR:

```bash
curl -I http://172.16.238.10:8084
curl -I http://172.16.238.11:8084
curl -I http://172.16.238.12:8084
```

---

## 9️⃣ Validar Load Balancer

```bash
curl -I http://localhost
```

---

## 🔟 Acceder a la aplicación

Usar el botón:

```
StaticApp
```

---

# ✅ Validación Exitosa

La arquitectura debe quedar así:

```
Cliente
   ↓
NGINX Load Balancer (stlb01)
   ↓
Apache stapp01
Apache stapp02
Apache stapp03
```

---

# 📋 Puntos Clave

✔ Solo modificar `/etc/nginx/nginx.conf`
✔ No modificar puerto Apache
✔ Apache debe estar activo
✔ Validar configuración antes de reiniciar
✔ Usar upstream en contexto http

---

# 🚨 Problemas Comunes

## Connection refused

Apache detenido o firewall bloqueando tráfico.

---

## Nginx muestra página default

Configuración proxy incorrecta o sobrescrita por includes.

---

# 🧠 Concepto DevOps

Un Load Balancer permite:

* Alta disponibilidad
* Escalabilidad horizontal
* Distribución de carga
* Tolerancia a fallos

