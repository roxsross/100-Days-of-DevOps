
## Desafío – Instalación de Ansible en Jump Host

---

## 📌 Contexto

El equipo de DevOps de **xFusionCorp Industries** decidió comenzar a utilizar **Ansible** como herramienta de automatización y gestión de configuración, debido a su simplicidad y bajos pre-requisitos.

Para las pruebas iniciales, se definió el **Jump Host** como **Ansible Controller**, desde donde se ejecutarán tareas contra el resto de los servidores del datacenter.

---

## 🎯 Objetivo del desafío

Instalar **Ansible versión 4.10.0** en el **Jump Host**, cumpliendo las siguientes condiciones:

* La instalación debe realizarse **exclusivamente con `pip3`**
* El binario de Ansible debe quedar **disponible globalmente**
* **Todos los usuarios** del sistema deben poder ejecutar comandos de Ansible
* Verificar que la versión instalada sea la correcta

---

## 🧠 Estrategia de abordaje

Para cumplir el requerimiento se decidió:

* Utilizar `pip3` en lugar del gestor de paquetes del sistema (`yum/apt`)
* Ejecutar la instalación con privilegios administrativos para que quede accesible globalmente
* Validar la instalación ejecutando el comando `ansible --version`

Este enfoque evita conflictos con paquetes del sistema y garantiza compatibilidad con futuras automatizaciones.

---

## 🔧 Pasos ejecutados

### 1️⃣ Acceso al Jump Host

```bash
ssh thor@jump_host.stratos.xfusioncorp.com
```

---

### 2️⃣ Verificación de pip3

Antes de instalar Ansible, se confirmó que `pip3` estuviera disponible:

```bash
pip3 --version
```

---

### 3️⃣ Instalación de Ansible usando pip3

```bash
sudo pip3 install ansible==4.10.0
```

> ⚠️ Se utilizó `sudo` para asegurar que el binario quede instalado en una ubicación global (`/usr/local/bin`), accesible para todos los usuarios.

---

### 4️⃣ Verificación de la instalación

```bash
ansible --version
```

Salida esperada (ejemplo):

```text
ansible [core 2.11.x]
  python version = 3.x
```

---

## ✅ Validaciones realizadas

* ✔ `ansible --version` responde correctamente
* ✔ La versión instalada corresponde a **Ansible 4.10.0**
* ✔ El comando puede ejecutarse sin problemas por distintos usuarios
* ✔ Ansible queda disponible globalmente en el sistema

---

## 🏁 Resultado final

✅ **Ansible 4.10.0 instalado correctamente en el Jump Host**
✅ **Configurado como Ansible Controller** para futuras tareas de automatización
✅ **Requerimientos del desafío cumplidos en su totalidad**

---

## 📘 Notas adicionales

* No se realizó ninguna configuración de inventario en este desafío
* No se ejecutaron playbooks en esta etapa
* El Jump Host queda listo para ser utilizado en los próximos desafíos de automatización


> 📌 **Reto 100 Días de DevOps** – Día 8 completado. Automatización segura habilitada 🔑🚀
