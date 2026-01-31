## Desafío – Despliegue de aplicación Java en Tomcat (App Server 2)

---

## 📌 Contexto

El equipo de desarrollo de Nautilus finalizó una versión beta de una aplicación Java y decidió desplegarla usando **Tomcat** en **App Server 2 (`stapp02`)**.

El artefacto a desplegar es un archivo `ROOT.war` disponible en el **Jump Host** bajo:

```
/tmp/ROOT.war
```

---

## 🎯 Objetivo del desafío

Preparar el App Server 2 para despliegue de una aplicación Java:

1. Instalar Tomcat en `stapp02`.
2. Configurarlo para ejecutarse en el **puerto 6300**.
3. Copiar y desplegar `ROOT.war` (desde Jump Host).
4. Verificar acceso desde la base URL:

   ```bash
   curl http://stapp02:6300
   ```

---

## 🧠 Estrategia de abordaje

El enfoque fue:

* Instalar el servicio Tomcat usando el paquete del sistema.
* Cambiar el puerto editando el `server.xml`.
* Subir el WAR al servidor y desplegarlo como `ROOT.war` en `webapps/`.
* Reiniciar Tomcat y validar por HTTP.

---

## 🔧 Implementación

### 1️⃣ Instalar Tomcat en App Server 2

En `stapp02`:

```bash
sudo yum install -y tomcat
sudo systemctl enable --now tomcat
```

---

### 2️⃣ Configurar Tomcat para correr en puerto 6300

Archivo objetivo:

```
/etc/tomcat/server.xml
```

Editar el conector HTTP y cambiar el puerto:

```xml
<Connector port="6300" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

Luego reiniciar:

```bash
sudo systemctl restart tomcat
sudo systemctl status tomcat --no-pager
```

---

### 3️⃣ Copiar `ROOT.war` desde Jump Host a App Server 2

En **Jump Host**, el archivo estaba en:

```
/tmp/ROOT.war
```

Copia al App Server 2:

```bash
scp /tmp/ROOT.war steve@stapp02:/tmp/
```

> Nota: si aparece prompt de host key (“Are you sure you want to continue connecting”), se acepta con `yes`.

---

### 4️⃣ Desplegar el WAR como ROOT

En `stapp02`:

```bash
sudo rm -rf /var/lib/tomcat/webapps/ROOT /var/lib/tomcat/webapps/ROOT.war
sudo mv /tmp/ROOT.war /var/lib/tomcat/webapps/ROOT.war
sudo chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war
sudo systemctl restart tomcat
```

---

## ✅ Validación final

Verificación desde Jump Host (o desde cualquier host con conectividad):

```bash
curl http://stapp02:6300
```

✅ Resultado esperado:

* Respuesta HTTP del servidor (200/302/403 según la app, pero debe responder).
* La app funciona desde la URL base: `http://stapp02:6300/`

---

## 🏁 Resultado final

✅ Tomcat instalado y habilitado
✅ Ejecutando en el puerto **6300**
✅ Aplicación desplegada correctamente como ROOT
✅ Servicio validado con `curl` desde base URL

---

## 📘 Notas y buenas prácticas

* El despliegue como `ROOT.war` permite servir directamente en `/` sin rutas extra.
* El cambio de puerto se realiza en `server.xml` en el conector HTTP.
* Siempre validar con `systemctl status` y `curl` después del deploy.

> 📌 **Reto 100 Días de DevOps** – Día 11 completado. Automatización segura habilitada 🔑🚀
