# 📅 Día 18: Configuración de LAMP Server para Aplicación Nautilus

---

## 🎯 Objetivo

Configurar un entorno **LAMP (Linux, Apache, MariaDB, PHP)** distribuido en la infraestructura Nautilus para permitir que la aplicación web se conecte correctamente a la base de datos.

Se debe:

* Instalar y configurar Apache y PHP en los App Servers
* Configurar Apache para escuchar en el puerto **5002**
* Crear la base de datos y el usuario en el servidor MariaDB
* Crear un archivo PHP de prueba que valide la conexión a la base de datos
* Verificar acceso a través del Load Balancer

⚠️ No es necesario configurar storage compartido en este entorno específico del lab

---

## 🧠 Contexto

La empresa xFusionCorp desplegará una aplicación basada en PHP en Stratos Datacenter.

La arquitectura incluye:

* App servers con Apache + PHP
* DB server con MariaDB
* Load Balancer que distribuye tráfico hacia los App servers

La aplicación debe conectarse a la base de datos utilizando el usuario configurado.

---

## 🖥 Infraestructura

| Servidor | IP            | Rol             |
| -------- | ------------- | --------------- |
| stapp01  | 172.16.238.10 | App Server      |
| stapp02  | 172.16.238.11 | App Server      |
| stapp03  | 172.16.238.12 | App Server      |
| stdb01   | 172.16.239.10 | Database Server |
| stlb01   | 172.16.238.14 | Load Balancer   |

---

Credenciales:

App servers:

```
tony / Ir0nM@n
steve / Am3ric@
banner / BigGr33n
```

DB server:

```
peter / Sp!dy
```

---

# 🛠️ Paso a paso

---

# 1️⃣ Configurar App Server 1 (stapp01)

Conectarse:

```bash
ssh tony@stapp01.stratos.xfusioncorp.com
```

Instalar Apache y PHP:

```bash
sudo yum install -y httpd php php-mysqlnd
```

Configurar Apache en puerto 5002:

```bash
sudo sed -i 's/^Listen 80$/Listen 5002/' /etc/httpd/conf/httpd.conf
```

Iniciar Apache:

```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
```

---

# 2️⃣ Repetir configuración en stapp02 y stapp03

stapp02:

```bash
ssh steve@stapp02
sudo yum install -y httpd php php-mysqlnd
sudo sed -i 's/^Listen 80$/Listen 5002/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl restart httpd
```

stapp03:

```bash
ssh banner@stapp03
sudo yum install -y httpd php php-mysqlnd
sudo sed -i 's/^Listen 80$/Listen 5002/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl restart httpd
```

---

# 3️⃣ Configurar MariaDB en Database Server

Conectarse:

```bash
ssh peter@stdb01
```

Instalar MariaDB:

```bash
sudo yum install -y mariadb-server
```

Iniciar servicio:

```bash
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

Crear base de datos:

```bash
sudo mysql -e "CREATE DATABASE kodekloud_db4;"
```

Crear usuario:

```bash
sudo mysql -e "CREATE USER 'kodekloud_rin'@'%' IDENTIFIED BY 'ksH85UJjhb';"
```

Asignar permisos:

```bash
sudo mysql -e "GRANT ALL PRIVILEGES ON kodekloud_db4.* TO 'kodekloud_rin'@'%';"
```

Aplicar cambios:

```bash
sudo mysql -e "FLUSH PRIVILEGES;"
```

---

# 4️⃣ Crear archivo PHP de prueba

En cada App Server:

Ejemplo stapp01:

```bash
sudo tee /var/www/html/index.php <<EOF
<?php
$conn = new mysqli("stdb01.stratos.xfusioncorp.com", "kodekloud_rin", "ksH85UJjhb", "kodekloud_db4");

if (\$conn->connect_error) {
 die("Connection failed: " . \$conn->connect_error);
}

echo "App is able to connect to the database using user kodekloud_rin";
?>
EOF
```

Repetir en stapp02 y stapp03

---

# 5️⃣ Validación local

En cualquier App Server:

```bash
curl http://localhost:5002
```

Debe mostrar:

```
App is able to connect to the database using user kodekloud_rin
```

---

# 6️⃣ Validación desde Load Balancer

Desde jump host:

```bash
curl http://stlb01:5002
```

Resultado esperado:

```
App is able to connect to the database using user kodekloud_rin
```

---

# ✅ Resultado esperado

Arquitectura final:

```
           Load Balancer
                │
        ┌───────┼────────┐
        │       │        │
     stapp01 stapp02 stapp03
        │       │        │
        └───────┴────────┘
                │
              stdb01
           MariaDB Server
```

---

# 📋 Componentes configurados

✔ Apache instalado
✔ PHP instalado
✔ Puerto 5002 configurado
✔ MariaDB instalado
✔ Base de datos creada
✔ Usuario creado
✔ Permisos asignados
✔ Aplicación conectando correctamente

---

# 🚨 Problemas comunes

## Apache no responde

Solución:

```bash
sudo systemctl restart httpd
```

---

## Error conexión DB

Verificar usuario:

```bash
sudo mysql -e "SELECT user,host FROM mysql.user;"
```

---

## Load balancer no responde

Verificar Apache en los App servers:

```bash
curl localhost:5002
```

---

# 🧠 Concepto DevOps

Separación de responsabilidades:

* App layer → Apache + PHP
* Data layer → MariaDB
* Network layer → Load Balancer

Esto permite:

* Alta disponibilidad
* Escalabilidad horizontal
* Arquitectura distribuida
