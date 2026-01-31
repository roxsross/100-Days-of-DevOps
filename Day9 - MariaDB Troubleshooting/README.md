
## Desafío – MariaDB caído en Database Server

---

## 📌 Contexto

El equipo de soporte de **xFusionCorp Industries** detectó que la aplicación Nautilus no podía conectarse a la base de datos en el **Stratos Datacenter**.
El monitoreo indicó que el servicio **MariaDB** se encontraba **caído** en el **Database Server (`stdb01`)**, impactando directamente en la disponibilidad de la aplicación.

---

## 🎯 Objetivo del desafío

* Identificar la causa por la cual **MariaDB no iniciaba**
* Corregir el problema sin reinstalar el servicio
* Asegurar que el servicio quede **activo y estable**

---

## 🧠 Estrategia de abordaje

El enfoque seguido fue:

1. Verificar el estado del servicio
2. Analizar mensajes de error del sistema
3. Identificar problemas de permisos o filesystem
4. Aplicar la corrección mínima necesaria
5. Validar que el servicio quede operativo

Este método evita cambios innecesarios y reduce riesgos en entornos productivos.

---

## 🔍 Diagnóstico inicial

### 1️⃣ Verificación del estado del servicio

```bash
sudo systemctl status mariadb
```

Resultado:

* Servicio en estado **failed**
* Error durante el proceso de arranque

---

### 2️⃣ Análisis de logs

```bash
sudo journalctl -xeu mariadb.service
```

Los mensajes indicaban problemas de acceso al directorio de datos de MariaDB.

---

## 🧪 Análisis de la causa raíz

Se detectó que el directorio de datos de MariaDB:

```
/var/lib/mysql
```

tenía **permisos y ownership incorrectos**, lo que impedía que el usuario `mysql` pudiera:

* Crear archivos temporales
* Escribir logs
* Inicializar correctamente el motor de base de datos

---

## 🔧 Solución aplicada

### 1️⃣ Corrección de permisos

```bash
sudo chown -R mysql:mysql /var/lib/mysql
```

Este comando restablece el ownership correcto para todos los archivos y subdirectorios utilizados por MariaDB.

---

### 2️⃣ Reinicio del servicio

```bash
sudo systemctl restart mariadb
```

---

## ✅ Validación final

### Verificación del estado del servicio

```bash
sudo systemctl status mariadb
```

Resultado esperado:

* Estado **active (running)**
* Sin errores en el arranque

---

## 🏁 Resultado final

✅ Servicio **MariaDB restaurado correctamente**
✅ Aplicación nuevamente capaz de conectarse a la base de datos
✅ Problema resuelto sin reinstalaciones ni cambios invasivos

---

## 📘 Lecciones aprendidas

* Muchos fallos de servicios están relacionados con **permisos de filesystem**
* Antes de reinstalar, siempre revisar:

  * Logs del servicio
  * Ownership de directorios críticos
* Un diagnóstico correcto reduce el tiempo de resolución y el impacto en producción


> 📌 **Reto 100 Días de DevOps** – Día 9 completado. Automatización segura habilitada 🔑🚀
