# 📘 Manual de Uso de la Terminal (Linux / SSH) — Nautilus

Este documento explica **cómo usar la terminal** para trabajar con el laboratorio **Nautilus**, conectarte por SSH, moverte entre servidores y ejecutar tareas básicas. Está diseñado para personas que están comenzando en DevOps y necesitan una guía práctica y sencilla.

---

## 📌 1. Qué es la terminal (CLI)

La **terminal** (o consola / shell) es una interfaz de línea de comandos donde podés ejecutar instrucciones para interactuar con el sistema operativo sin interfaces gráficas.

En Linux, la terminal más común es **Bash**.

---

## 🚀 2. Cómo abrir la terminal

### 🖥️ En Linux / macOS

* Buscá la aplicación llamada **Terminal**
* Abrila
* Deberías ver un prompt del estilo:

  ```
  usuario@maquina:~$
  ```

### 🪟 En Windows

Podés usar:

* **Git Bash**
* **Windows Terminal**
* **WSL (Ubuntu / Debian)**

Todos funcionan como terminal Linux.

---

## 🔐 3. Conectarse al laboratorio por SSH

En este reto trabajamos con un **jump host** y luego varios servidores de aplicaciones (App Servers).

### 📌 Conexión al jump host

Desde tu terminal local:

```bash
ssh thor@jump_host.stratos.xfusioncorp.com
```

La primera vez te pedirá **contraseña**:

```
mjolnir123
```

Una vez conectado verás algo como:

```
thor@jump_host:~$
```

---

## 🖥️ 4. Conectarse a los App Servers

Desde el **jump host** (usuario `thor`) ejecutá:

### App Server 1

```bash
ssh tony@stapp01.stratos.xfusioncorp.com
```

Contraseña:

```
Ir0nM@n
```

### App Server 2

```bash
ssh steve@stapp02.stratos.xfusioncorp.com
```

Contraseña:

```
Am3ric@
```

### App Server 3

```bash
ssh banner@stapp03.stratos.xfusioncorp.com
```

Contraseña:

```
BigGr33n
```

---

## 📁 5. Comandos básicos de Linux

Aquí están los comandos que usarás con frecuencia:

| Comando                       | Qué hace                                         |
| ----------------------------- | ------------------------------------------------ |
| `ls -l`                       | Lista archivos con detalles                      |
| `pwd`                         | Muestra ruta actual                              |
| `cd /ruta`                    | Cambia de directorio                             |
| `mkdir nombre`                | Crea un directorio                               |
| `cat archivo`                 | Muestra el contenido de un archivo               |
| `nano archivo` | `vi archivo` | Editores de texto en terminal                    |
| `chmod`                       | Cambia permisos de archivo                       |
| `sudo`                        | Ejecuta comando con privilegios de administrador |

---

## 🔧 6. Editores de texto en la terminal

### 📌 `vi`

```bash
sudo vi /etc/ssh/sshd_config
```

* `i`: entrar en modo insertar
* `Esc`: salir de insertar
* `:wq`: guardar y salir
* `:q!`: salir sin guardar

### 📌 `nano`

```bash
sudo nano /etc/ssh/sshd_config
```

* `Ctrl + O`: guardar
* `Ctrl + X`: salir

---

## 🧠 7. Ejecución de comandos con privilegios

Muchas tareas del reto requieren permisos de administrador (`root`).
Para eso usamos:

```bash
sudo <comando>
```

Después de eso, el sistema puede pedir tu contraseña.

---

## 🔁 8. Verificación de cambios

Cada vez que cambias una configuración (por ejemplo `/etc/ssh/sshd_config`), **siempre verificá**:

```bash
sudo systemctl status sshd
```

o

```bash
sudo sshd -T | grep permitrootlogin
```

---

## 🔐 9. SSH sin contraseña (Password-less)

Para evitar ingresar la contraseña cada vez:

1. Generás clave SSH:

   ```bash
   ssh-keygen -t rsa -b 2048
   ```
2. Copiás la clave al servidor remoto:

   ```bash
   ssh-copy-id usuario@servidor
   ```

Esto facilita automatización y es buena práctica DevOps.

---

## 🧪 10. Consejos de buenas prácticas

✔️ Lee siempre los mensajes de error
✔️ Usá `man <comando>` para ver ayuda
✔️ No trabajes como `root` directamente
✔️ Documentá cada cambio que haces

---

## 🧭 11. Recursos útiles

* Manual básico de Linux: `man bash`
* Referencia SSH: [https://linuxize.com/post/ssh-keygen-guide/](https://linuxize.com/post/ssh-keygen-guide/)
* Nautilus infra docs: [https://kodekloudhub.github.io/kodekloud-engineer/docs/projects/nautilus#infrastructure-details](https://kodekloudhub.github.io/kodekloud-engineer/docs/projects/nautilus#infrastructure-details)

---

## 📌 Conclusión

Este README te da una base sólida para usar la terminal en el laboratorio Nautilus y en cualquier entorno Linux. La terminal es tu principal herramienta en DevOps 🚀

---

Si querés, lo adaptamos con **pasos de cada día del reto enlazados** (para que quede tipo “capítulos del diario de aprendizaje”).
