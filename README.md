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
