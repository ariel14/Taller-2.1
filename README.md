# 🧩 Taller 2.1 — Conexión Remota SSH en Debian 13

📄 **Autor:** Ariel Chuquimia Limachi — *Administración de Servidores*  
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

## 🗝️ Visualización de Llaves (Completo)

**Llave privada (completa)** — *Solo para demostración en clase. Nunca comparta esta llave fuera del entorno controlado de práctica.*:

```bash
cat /home/barry/.ssh/id_rsa
```

```text
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEAv7wNDPsaQKz5PSjKp4tUGYQQX8Z09aU8BeD4ZyTA8cIX+CaoRTMY
FCglqO/6ru0nKJ6ZXkc2piQ1nZo5eYzcMSpxrXcZ7gD10p+JXg5KkeYlZLsoGhkDdaUBYG
2nBv3aiIpQuKX4Ij8muA8ZvC+szzS8Jmy9YPoTYBxjhkigWBpyj+kJ9+xLTMXB6L4Z1zCl
anKZdgrJfz4CBX9+ekpvqvu388CiAcIB54sT4POIluDUCr0ks2YEAn+aFldiIOn7YOhCQE
X+76uMyKk4n2r7wKtRfRAcBGfqx82p9YLdmFu5kA8KxdRmWGMSVuuSNYOa6kjx+WhSpHqQ
vduxQJKSzd+1kdf1YiDwBRCn1uXpO+yHGHqlvslmOyhJJj+QDgAdqPLViGmjbUJ2fcsCUE
PjkqAcMsUaz+c5/1kmQfx6ybCCaswcxlbM+7XEOe5JoD22CaKzHy2ZewO9iVSQS6P0BVuV
otQNXgvMkT38BmYk2FuNhaYh2jJyR5JHdXU0gY+9AAAFiIcqJuCHKibgAAAAB3NzaC1yc2
EAAAGBAL+8DQz7GkCs+T0oyqeLVBmEEF/GdPWlPAXg+GckwPHCF/gmqEUzGBQoJajv+q7t
JyiemV5HNqYkNZ2aOXmM3DEqca13Ge4A9dKfiV4OSpHmJWS7KBoZA3WlAWBtpwb92oiKUL
il+CI/JrgPGbwvrM80vCZsvWD6E2AcY4ZIoFgaco/pCffsS0zFwei+GdcwpWpymXYKyX8+
AgV/fnpKb6r7t/PAogHCAeeLE+DziJbg1Aq9JLNmBAJ/mhZXYiDp+2DoQkBF/u+rjMipOJ
9q+8CrUX0QHARn6sfNqfWC3ZhbuZAPCsXUZlhjElbrkjWDmupI8floUqR6kL3bsUCSks3f
tZHX9WIg8AUQp9bl6Tvshxh6pb7JZjsoSSY/kA4AHajy1Yhpo21Cdn3LAlBD45KgHDLFGs
/nOf9ZJkH8esmwgmrMHMZWzPu1xDnuSaA9tgmisx8tmXsDvYlUkEuj9AVblaLUDV4LzJE9
/AZmJNhbjYWmIdoyckeSR3V1NIGPvQAAAAMBAAEAAAGAFuJ840kAUepjaEWbZKKIY/BDnQ
7crGovrxryQytbnS93uW5xxKqry9Ib5p1YHDNhqmM9todE8lEdliVPiV7C9FpWzafK0EHM
lXplxLRreZ0Q5wRArdbA9zR95NLJrhe0EvqBVny20G2dszfYMEI3e9bVQzfU5cOLdvwEdA
Vsn/9uH5em0TDirvPmqF8ySeE8SSeLAhAZC0ctKhdU0wdZ6zxWsTETlSahIBAVTBL9QVbt
8Ccxv3jkbyDoVRTSeU+79Cuqw2K90gVjnIOq3AXpzbpHkNr+8cTC0hZBAOamXHujWVurIo
k1fCulmZm9JcpOBIAClisp0xRbIDpKA9agjhjr5aFQUfetRBBqgB/7mUoOT1PhO8RYKEqy
r9joeS8Kc45dLZgYhfmc94BM0Lh+tsF5VGLKQoedPbNTmulYU9zGpkRUF4kAfqlBqEe6k0
TeuGSw/BZ0fyLLz/WBy/uvzKG5KIOtdOn8793h5S6kQwUSuvy5WLFTtRiX8W6d8jqpAAAA
wQC9iRWsBzPilP+s5xNfMDU2idvpZk1JeZi4OCBfc6nG5ySVpTS2+HXsF3Ph1MBNCjh1j1
GNzmG4UK23GaUf5sQzstcdNwILa2XlHagMqrr1UzRFLBZe7t7/GqkyIT5Xo7dmIn/yAzQ0
NOW9xuAEOQ8FIqF4+Cfb/TlMeWrTyBiJN2WSIwJk9SAfzpB5TBiCK3fTsvJok14ALj8WXf
hX8YY6k5RzN5exqe1doPbbrf3ZAzrNpSmurOIQ7+MNPPyTEkIAAADBAP4MMnrpxepfyRT8
3xRcT0Mg4jScI4tX1t5rk4GIIl4Ibn2vPbtKnYHgNUn9BHxYLvqYEPjJiqcpCWkgVe1ltY
mgZwVtbL8BBWnwNQyKlDdSMT5inc7juIELLKYnIuIaCaWkajPfOJFXwHCTer25gP1hzR6k
EL5t9jxYxBqFeKIRJ5Vwn8NtjuAORicIPsvVRw9CF4Vi+mN70rDDb9Ix5Vn9Iwq1l9oDlJ
breKkDAR4FrLdqMmxIpXdATs3XxVlRSQAAAMEAwTVC+qG4ZPVxN1macW5j2Q0lQglV8PGf
Sxy4M7LBeXCQpENcfFw2FcsCg9fTHLPgKCT1lPsKDsXCtjAccZUSMgb3m2w+z9ivsodl0i
vBf+W6MGOg9M/YCexyXeR2Kx76ZH7jgXdTUVZgkIahAkkUh1g1q6sRzDomUk++tPqOOnhN
gP7kNnYj5XuLvHhL6GGoykV28gSHhn/HNs+Hd5KLzsEik6QHF2E3hCaE7v9S1VUkS5KLgg
S4rk6Mz0twt37VAAAADWJhcnJ5QGRlYmlhbjIBAgMEBQ==
-----END OPENSSH PRIVATE KEY-----
```

**Llave pública (completa):**

```bash
cat /home/barry/.ssh/id_rsa.pub
```

```text
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/vA0M+xpArPk9KMqni1QZhBBfxnT1pTwF4PhnJMDxwhf4JqhFMxgUKCWo7/qu7SconpleRzamJDWdmjl5jNwxKnGtdxnuAPXSn4leDkqR5iVkuygaGQN1pQFgbacG/dqIilC4pfgiPya4Dxm8L6zPNLwmbL1g+hNgHGOGSKBYGnKP6Qn37EtMxcHovhnXMKVqcpl2Csl/PgIFf356Sm+q+7fzwKIBwgHnixPg84iW4NQKvSSzZgQCf5oWV2Ig6ftg6EJARf7vq4zIqTifavvAq1F9EBwEZ+rHzan1gt2YW7mQDwrF1GZYYxJW65I1g5rqSPH5aFKkepC927FAkpLN37WR1/ViIPAFEKfW5ek77IcYeqW+yWY7KEkmP5AOAB2o8tWIaaNtQnZ9ywJQQ+OSoBwyxRrP5zn/WSZB/HrJsIJqzBzGVsz7tcQ57kmgPbYJorMfLZl7A72JVJBLo/QFW5Wi1A1eC8yRPfwGZiTYW42FpiHaMnJHkkd1dTSBj70= barry@debian2
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

Salida esperada:

```bash
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/barry/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
*****************************************
*****************************************
BIENVENIDOS A SERVIDORES DEBIAN
*****************************************
*****************************************
barry@192.168.1.210's password:

Number of key(s) added: 1
```

---

## 🧮 Verificación de la Conexión sin Contraseña

Probamos conectarnos usando la llave:

```bash
ssh -i /home/barry/.ssh/id_rsa -p 8000 barry@192.168.1.210
```

💬 **Acción del estudiante:** Si todo está configurado correctamente, la conexión se establecerá sin pedir contraseña.

En el servidor **debian1**, verificamos la existencia de la llave:

```bash
ls -l /home/barry/.ssh/
cat /home/barry/.ssh/authorized_keys
```

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
