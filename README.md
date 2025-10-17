# 🧩 Taller 2.1 — Conexión Remota SSH en Debian 13

📄 **Autor:** Ariel Chuquimia Limachi — *Auxiliar de Administración de Servidores*  
📅 **Fecha:** 17/10/2025

---

## 🎯 Objetivo del Taller

Configurar una **conexión SSH segura y transparente** entre dos servidores **Debian 13 (Trixie)**, aplicando autenticación por llaves y parámetros de seguridad básicos para la administración remota.

---

## 💻 Material Necesario

| Elemento | Descripción |
|-----------|-------------|
| 🖥️ **2 Máquinas Virtuales** | Debian 13 (una como servidor y otra como cliente) |
| 🌐 **Red** | En modo puente o red interna (ambas deben poder hacer ping) |
| 🔑 **Usuario con privilegios** | Acceso como `root` o con permisos `sudo` |
| 📡 **Conectividad** | Acceso a red para probar SSH (puerto 22 y 8000) |

---

## 🧠 Introducción Teórica

El protocolo **SSH (Secure Shell)** permite establecer conexiones seguras y cifradas entre dos sistemas.  
Reemplaza servicios antiguos como **Telnet**, asegurando la integridad y confidencialidad de los datos.

- **Puerto predeterminado:** 22  
- **Protocolo:** TCP  
- **Uso principal:** administración remota de servidores Linux  
- **Ventajas:** cifrado, autenticación por llaves, protección contra ataques de intermediario (*man-in-the-middle*)

> 💡 *SSH es la herramienta fundamental de los administradores de sistemas Linux para acceder y controlar servidores de forma segura.*

---

## ⚙️ Instalación del Servidor SSH

Ejecutamos el siguiente comando para instalar el servidor **OpenSSH**:

```bash
sudo apt install openssh-server -y
```

Verificamos que el servicio esté activo:

```bash
sudo systemctl status ssh
```

💬 **Acción del estudiante:** Si el estado muestra `active (running)`, significa que el servicio está funcionando correctamente.

---

## 🔗 Conexión Remota al Servidor

Para conectarnos desde el cliente (por ejemplo, **debian2**) al servidor (**debian1**):

```bash
ssh -p <puerto> <usuario>@<ip>
```

**Ejemplo:**

```bash
ssh -p 22 barry@192.168.X.211
```

Verificamos conectividad con un *ping*:

```bash
ping 192.168.X.211
```

💬 **Acción del estudiante:** Si el ping responde, significa que hay comunicación entre ambas máquinas.

---

## 🪧 Personalización del Banner de Conexión

Editamos el archivo `/etc/motd` para mostrar un mensaje al conectar por SSH:

```bash
sudo nano /etc/motd
```

**Ejemplo de mensaje:**

```
⚠️ PROHIBIDO EL ACCESO A PERSONAL NO AUTORIZADO ⚠️
Todo ingreso será monitoreado.
```

Guardamos y reiniciamos el servicio SSH para aplicar los cambios:

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

💬 **Acción del estudiante:** Verifique que al volver a ingresar con SSH, el mensaje de advertencia se muestre correctamente.

---

## 📜 Revisión de Logs del Servicio SSH

Podemos consultar los registros del servicio para verificar accesos:

```bash
sudo journalctl -u ssh
```

💬 **Acción del estudiante:** Para salir del visor de logs, presione `Ctrl + C` o la tecla `q`.

---

## 🧱 Instalación y Configuración de Rsyslog

El servicio **rsyslog** gestiona los registros del sistema.

```bash
sudo apt install rsyslog -y
sudo systemctl restart rsyslog
sudo systemctl enable rsyslog
sudo systemctl status rsyslog
```

💬 **Acción del estudiante:** Asegúrese de que el servicio esté en estado `active (running)`.

---

## 🔒 Cambio del Puerto de Conexión SSH

Abrimos el archivo de configuración de SSH:

```bash
sudo nano -l /etc/ssh/sshd_config
```

Buscamos la línea del puerto y la modificamos a 8000:

```
:14 Port 8000
```

Reiniciamos el servicio SSH:

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

> ⚠️ **Nota:** Si usa `ufw`, recuerde permitir el puerto:
> ```bash
> sudo ufw allow 8000/tcp
> ```

---

## 🧩 Configuración de Seguridad

Volvemos a editar el archivo de configuración para reforzar la seguridad:

```bash
sudo nano -l /etc/ssh/sshd_config
```

Agregamos o verificamos las siguientes líneas:

```
:32 LoginGraceTime 20
:33 PermitRootLogin no
:125 AllowUsers ariel
```

Reiniciamos el servicio SSH:

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

---

## 👤 Creación del Usuario Dedicado

Creamos el usuario **ariel** para el acceso remoto:

```bash
sudo useradd -m ariel
sudo passwd ariel
```

💬 **Acción del estudiante:** Ingrese una contraseña segura para el nuevo usuario.

---

## 🔐 Generación de Llaves SSH

Desde la máquina **debian2**, iniciamos sesión como el usuario **barry** y generamos las llaves SSH:

```bash
ssh-keygen -t rsa
```

💬 **Acción del estudiante:**  
1. Presione **Enter** tres veces seguidas para aceptar las rutas y contraseñas por defecto.  
2. Espere a que se generen los archivos en el directorio `~/.ssh`.

Verificamos las llaves generadas:

```bash
ls -l /home/barry/.ssh/
```

---

## 🗝️ Visualización de Llaves

**Llave privada:**

```bash
cat /home/barry/.ssh/id_rsa
```

(Contenido completo mostrado en clase)

**Llave pública:**

```bash
cat /home/barry/.ssh/id_rsa.pub
```

---

## 🚀 Copiar la Llave Pública al Servidor SSH

Desde **debian2**, enviamos la llave pública a **debian1**:

```bash
ssh-copy-id -i /home/barry/.ssh/id_rsa.pub -p 8000 barry@192.168.X.210
```

💬 **Acción del estudiante:**  
1. Presione **Enter** tres veces cuando se le solicite.  
2. Ingrese la contraseña de `barry` en el servidor remoto.  

---

## 🧮 Verificación de la Conexión sin Contraseña

Probamos conectarnos usando la llave:

```bash
ssh -i /home/barry/.ssh/id_rsa -p 8000 barry@192.168.1.210
```

💬 **Acción del estudiante:** Si todo está configurado correctamente, la conexión se establecerá sin pedir contraseña.

---

## ✅ Conclusión del Taller

Con esta práctica aprendimos a:

- Configurar el servicio **OpenSSH Server**.  
- Personalizar mensajes de conexión.  
- Cambiar el puerto SSH a **8000**.  
- Crear un usuario seguro.  
- Generar y usar **llaves SSH** para conexión sin contraseña.  
- Usar **rsyslog** para registrar eventos del sistema.  

> 🧠 **Consejo:** Implementa autenticación por llaves y evita contraseñas en entornos de producción.  
> Esto garantiza mayor seguridad y control sobre los accesos remotos.

---

📄 **Autor:** Ariel Chuquimia Limachi — *Auxiliar de Administración de Servidores*  
📅 **Fecha:** 17/10/2025
