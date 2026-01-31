## Desafío – Hardening de Apache con iptables (permitir solo LBR)

---

## 📌 Contexto

El equipo de **Security** de xFusionCorp Industries detectó que los **App Servers** exponían el puerto de Apache públicamente, sin ningún control de acceso a nivel de firewall.

Dado que el acceso a las aplicaciones debe realizarse **exclusivamente a través del Load Balancer (LBR)**, se decidió implementar reglas de **iptables** para restringir el acceso directo.

---

## 🎯 Objetivo del desafío

En **todos los App Servers** (`stapp01`, `stapp02`, `stapp03`):

1. Instalar **iptables** y sus dependencias
2. Bloquear el acceso al puerto Apache (**5003**) para todos
3. Permitir acceso **únicamente** desde el **LBR (`stlb01`)**
4. Asegurar que las reglas **persistan tras reboot**

---

## 🧠 Estrategia de abordaje

Se aplicó una estrategia de **deny by default + allow explícito**:

1. Permitir tráfico al puerto 5003 **solo desde la IP del LBR**
2. Bloquear el resto del tráfico al mismo puerto
3. Mantener reglas mínimas para no afectar SSH u otros servicios
4. Validar conectividad desde:

   * App Servers ❌
   * Jump Host ❌
   * Load Balancer ✅

---

## 🔧 Implementación

> 🔁 **Los siguientes pasos se ejecutaron en cada App Server**

---

### 1️⃣ Instalación de iptables

```bash
sudo yum install -y iptables-services
```

Habilitar el servicio:

```bash
sudo systemctl enable iptables
sudo systemctl start iptables
```

---

### 2️⃣ Variables utilizadas

```bash
APACHE_PORT=5003
LBR_IP=172.16.238.14
```

---

### 3️⃣ Reglas de firewall aplicadas

#### Permitir tráfico desde LBR

```bash
sudo iptables -I INPUT 1 -p tcp -s $LBR_IP --dport $APACHE_PORT -m state --state NEW -j ACCEPT
```

#### Permitir conexiones establecidas

```bash
sudo iptables -I INPUT 2 -m state --state ESTABLISHED,RELATED -j ACCEPT
```

#### Bloquear el resto del tráfico al puerto Apache

```bash
sudo iptables -A INPUT -p tcp --dport $APACHE_PORT -j DROP
```

---

### 4️⃣ Validar reglas aplicadas

```bash
sudo iptables -L INPUT -n --line-numbers
```

Ejemplo esperado:

```
ACCEPT tcp -- 172.16.238.14  0.0.0.0/0  tcp dpt:5003
DROP   tcp -- 0.0.0.0/0      0.0.0.0/0  tcp dpt:5003
```

---

### 5️⃣ Persistencia tras reboot

Guardar reglas:

```bash
sudo service iptables save
```

---

## ✅ Validaciones finales

### ❌ Desde App Servers

```bash
curl -I http://stapp01:5003
```

Resultado:

```
curl: (7) No route to host
```

---

### ❌ Desde Jump Host

```bash
curl -I http://stapp01:5003
```

Resultado:

```
No route to host
```

---

### ✅ Desde Load Balancer

```bash
curl -I http://stapp01:5003
curl -I http://stapp02:5003
curl -I http://stapp03:5003
```

Resultado esperado:

```
HTTP/1.1 403 Forbidden
```

> 🔎 **403 Forbidden es válido**: Apache responde → puerto accesible → firewall correcto.

---

## 🏁 Resultado final

✅ iptables instalado en todos los App Servers
✅ Puerto Apache protegido
✅ Acceso exclusivo desde LBR
✅ Reglas persistentes tras reinicio
✅ Hardening aplicado sin romper conectividad

---

## 📘 Lecciones aprendidas

* El firewall **no reemplaza** al Load Balancer, lo refuerza
* Siempre validar desde **orígenes distintos**
* `403` ≠ error → es una **respuesta saludable**
* iptables sigue siendo clave en troubleshooting real


> 📌 **Reto 100 Días de DevOps** – Día 13 completado. Automatización segura habilitada 🔑🚀
