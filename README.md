# 🧩 Taller 2.1 — Conexión Remota SSH

---

## 🖥️ Instalación del Servidor SSH

Instalamos el servicio **OpenSSH Server**:

```bash
apt install openssh-server
```

---

## 🔗 Conexión Remota

Para ingresar remotamente al servidor:

```bash
ssh -p <puerto> <usuario>@<ip>
```

**Ejemplo:**

```bash
ssh -p 22 barry@192.168.X.211
```

Verificamos que el servidor esté activo (responda al *ping*):

```bash
ping 192.168.X.211
```

Desde **debian1** nos conectamos a **debian2**:

```bash
ssh -p 22 barry@192.168.X.211
```

---

## 🪧 Personalización del Banner de Conexión

Editamos el archivo del mensaje del día:

```bash
nano /etc/motd
```

**Contenido sugerido:**
```
Prohibido el acceso a personal no autorizado.
Todo ingreso será monitoreado.
```

Reiniciamos el servicio SSH para aplicar cambios:

```bash
systemctl restart ssh
```

Verificamos el estado:

```bash
systemctl status ssh
```

---

## 🧾 Revisión de Logs

Podemos ver los registros de SSH con:

```bash
journalctl -u ssh
```

Para salir de *journalctl*:
```
Ctrl + C  ó  q
```

---

## 🧱 Instalación y Activación de Rsyslog

Instalamos y activamos el servicio **rsyslog** (gestión de logs):

```bash
apt install rsyslog
systemctl restart rsyslog
systemctl enable rsyslog
systemctl status rsyslog
```

---

## ⚙️ Configuración del Puerto y Seguridad

Abrimos el archivo de configuración de SSH:

```bash
nano -l /etc/ssh/sshd_config
```

Modificamos el puerto y opciones de seguridad:

```
:14 Port 8000
:32 LoginGraceTime 20
:33 PermitRootLogin no
:125 AllowUsers ariel
```

Reiniciamos el servicio:

```bash
systemctl restart ssh
systemctl status ssh
```

---

## 👤 Creación de Usuario

Creamos el usuario **ariel**:

```bash
useradd -m ariel
passwd ariel
```

---

## 🔐 Generación de Llaves SSH

Desde el **segundo servidor (debian2)**, generamos las llaves RSA:

```bash
ssh-keygen -t rsa
```

Verificamos las llaves generadas:

```bash
ls -l /home/barry/.ssh/
```

**Llave privada:**

```bash
cat /home/barry/.ssh/id_rsa
```

**Llave pública:**

```bash
cat /home/barry/.ssh/id_rsa.pub
```

Ejemplo del contenido (formato abreviado):

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/vA0M+... barry@debian2
```

---

## 🚀 Copiar la Llave Pública al Servidor (debian1)

Desde **debian2**, enviamos la llave al servidor SSH:

```bash
ssh-copy-id -i /home/barry/.ssh/id_rsa.pub -p 8000 barry@192.168.X.210
```

Durante el proceso, el sistema mostrará mensajes informativos y pedirá la contraseña del usuario destino.

---

## 🧩 Verificación en el Servidor (debian1)

Comprobamos que la llave pública haya sido recibida correctamente:

```bash
ls -l /home/barry/.ssh/
cat /home/barry/.ssh/authorized_keys
```

El archivo `authorized_keys` debe contener la llave pública enviada desde **debian2**.

---

## ✅ Conclusión

Con esta configuración se establece una **conexión SSH segura y transparente** entre los dos servidores Debian, con:
- Autenticación mediante llaves (sin contraseña)
- Banner personalizado
- Puerto modificado (8000)
- Usuario autorizado específico
- Logs gestionados por rsyslog

---

> 🧠 **Tip:**  
> Las conexiones basadas en llaves son más seguras y permiten automatizar accesos entre servidores sin exposición de contraseñas.

---

📄 **Autor:** [Ariel Chuquimia Limachi]  
📅 **Fecha:** [17/10/2025]
