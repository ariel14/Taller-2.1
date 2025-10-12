# Taller-2.1
conexión remota SSH


# INSTALCION DE SSH
```bash
apt install openssh-server
```
para ingresar remotamente al servidor 
```bash
ssh -p <puerto> <usuario>@<ip>
```
```bash
ssh -p 22 barry@192.168.X.211
```

Verificar que la otra maquina este prendida o si llega el icpm

```bash
ping 192.168.X.211
```

desde la maquina debian1 nos conectamos remotamente a la maquina debian 2

```bash
ssh -p 22 barry@192.168.X.211
```
cambiamos el banner de conexion en ambos sevidores 
```bash
 nano /etc/motd
```
```bash
Prohibido el acceso a personal no autorizado
todo ingreso sera monitoreado
```
reiniciar el servicio en caso de que no se muestre el banner o los cambio realizado 
```bash
systemctl restart ssh
```
verificamos el estado del servicio
```bash
systemctl status ssh
```
verificando los logs en journalctl
```bash
journalctl -u ssh
```
```bash
Ctl + c
ó
q
```
instalando rsyslog
```bash
apt install rsyslog
```
restablecer el servicio y encenderlo
```bash
systemctl restart rsyslog
systemctl enable rsyslog
systemctl status rsyslog
```
vamos a cambiar el puerto del ssh
```bash
nano -l /etc/ssh/sshd_config
```
```bash
:14 Port 8000
```
reiniacimos el servicio
```bash
systemctl restart ssh
systemctl status ssh
```
configurando el tiempo de gracia de la coneccion 
```bash
 nano -l /etc/ssh/sshd_config
```
```bash
:32 LoginGraceTime 20
:33 PermitRootLogin no
:125 AllowUsers ariel 

```
reiniacimos el servicio
```bash
systemctl restart ssh
systemctl status ssh
```
Creamos el perfil del usuario ariel
```bash
useradd -m ariel
passwd ariel
```
desde el segundo servidor DEBIAN2
```bash
ssh -p 22 barry@192.168.X.211
```
desde el usuario barry@debian2:~$ 
vamos a crear las llaves de seguridad para ssh 
```bash
 ssh-keygen -t rsa
```
vamos a verificar las llaves 
```bash
 ssh-keygen -t rsa
```
revisamos los archivos generado 
```bash
ls -l /home/barry/.ssh/
```
verificamos la llave privada 
```bash
ls -l /home/barry/.ssh/
```
verificamos la llaver privada para ssh
```bash
cat /home/barry/.ssh/id_rsa
```
nos debe mostrar lo siguiente
```bash
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```
verificamos la llave publica y nos debe mostrar desde usuario se genero
```bash
cat /home/barry/.ssh/id_rsa.pub
```
```bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/vA0M+xpArPk9KMqni1QZhBBfxnT1pTwF4PhnJMDxwhf4JqhFMxgUKCWo7/qu7SconpleRzamJDWdmjl5jNwxKnGtdxnuAPXSn4leDkqR5iVkuygaGQN1pQFgbacG/dqIilC4pfgiPya4Dxm8L6zPNLwmbL1g+hNgHGOGSKBYGnKP6Qn37EtMxcHovhnXMKVqcpl2Csl/PgIFf356Sm+q+7fzwKIBwgHnixPg84iW4NQKvSSzZgQCf5oWV2Ig6ftg6EJARf7vq4zIqTifavvAq1F9EBwEZ+rHzan1gt2YW7mQDwrF1GZYYxJW65I1g5rqSPH5aFKkepC927FAkpLN37WR1/ViIPAFEKfW5ek77IcYeqW+yWY7KEkmP5AOAB2o8tWIaaNtQnZ9ywJQQ+OSoBwyxRrP5zn/WSZB/HrJsIJqzBzGVsz7tcQ57kmgPbYJorMfLZl7A72JVJBLo/QFW5Wi1A1eC8yRPfwGZiTYW42FpiHaMnJHkkd1dTSBj70= barry@debian2
```
desde la maquina dos debian2 vamos a mandar la llave publica al servidor de ssh "debian1"
```bash
ssh-copy-id -i /home/barry/.ssh/id_rsa.pub -p 8000 barry@192.168.X.210
```
(Enter, enter , enter ...Contraseña )
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

Now try logging into the machine, with: "ssh -i /home/barry/.ssh/id_rsa -p 8000 'barry@192.168.1.210'"
and check to make sure that only the key(s) you wanted were added.
```


en el servidor de ssh debian1 debe mostrar la llave publica recibida 
```bash
ls -l /home/barry/.ssh/
```
```bash
 cat /home/barry/.ssh/authorized_keys

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/vA0M+xpArPk9KMqni1QZhBBfxnT1pTwF4PhnJMDxwhf4JqhFMxgUKCWo7/qu7SconpleRzamJDWdmjl5jNwxKnGtdxnuAPXSn4leDkqR5iVkuygaGQN1pQFgbacG/dqIilC4pfgiPya4Dxm8L6zPNLwmbL1g+hNgHGOGSKBYGnKP6Qn37EtMxcHovhnXMKVqcpl2Csl/PgIFf356Sm+q+7fzwKIBwgHnixPg84iW4NQKvSSzZgQCf5oWV2Ig6ftg6EJARf7vq4zIqTifavvAq1F9EBwEZ+rHzan1gt2YW7mQDwrF1GZYYxJW65I1g5rqSPH5aFKkepC927FAkpLN37WR1/ViIPAFEKfW5ek77IcYeqW+yWY7KEkmP5AOAB2o8tWIaaNtQnZ9ywJQQ+OSoBwyxRrP5zn/WSZB/HrJsIJqzBzGVsz7tcQ57kmgPbYJorMfLZl7A72JVJBLo/QFW5Wi1A1eC8yRPfwGZiTYW42FpiHaMnJHkkd1dTSBj70= barry@debian2
```
esas son conecciones transparentes 


