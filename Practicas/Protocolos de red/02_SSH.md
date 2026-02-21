---
layout: default
title: SSH
---

# SSH: Autenticación con claves

SSH (Secure Shell) es un protocolo criptográfico de red usado para la comunicación segura de datos. Se usa a menudo para administrar sistemas que no son accesibles físicamente (como los VPS del CeSViMa).

Aunque se puede usar con contraseña como hemos hecho hasta ahora, la forma más segura y cómoda es usando **pares de claves criptográficas**.

En esta práctica vamos a:
1. Generar un par de claves SSH en nuestra máquina
2. Configurar acceso a un servidor usando esas claves
3. Crear un alias para conectarnos fácilmente
4. Deshabilitar el acceso por contraseña (solo claves)

---

## ¿Cómo funciona la autenticación con claves?

En lugar de una contraseña, usamos dos archivos:
- **Clave privada**: Se queda en tu máquina. NUNCA la compartas.
- **Clave pública**: Se copia al servidor. Puedes compartirla libremente.

Cuando te conectas, el servidor verifica que tienes la clave privada correspondiente a la pública que tiene guardada.

---

## Parte 1: Generar tu par de claves

### Paso 1: Crear las claves en tu máquina local

En tu ordenador (no en el servidor), ejecuta:

```bash
ssh-keygen -t ecdsa -b 521 
```

- `-t ecdsa`: Usa el algoritmo ecdsa
- `-b 521`: El número de bits de la clave

Te preguntará:
1. **Ubicación**: Pulsa Enter para aceptar la ruta por defecto (`~/.ssh/id_ed25519`)
2. **Passphrase**: Contraseña para proteger la clave (opcional pero recomendado)

**IMPORTANTE**: No vas a ver la contraseña mientras la estás introduciendo, pero se está escribiendo.

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/tu-usuario/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/tu-usuario/.ssh/id_ed25519
Your public key has been saved in /home/tu-usuario/.ssh/id_ed25519.pub
```


---

### Paso 2: Ver tu clave pública

```bash
cat ~/.ssh/id_ed25519.pub
```

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... tu-email@ejemplo.com
```

Este es el texto que copiarás al servidor. Cópialo al portapapeles.

---

## Parte 2: Configurar acceso al servidor

### Paso 3: Conectarse al servidor con la cuenta de administración

Primero accedemos al servidor con el usuario administrador compartido:

```bash
ssh vpsadmin@DOMINIO_DEL_SERVER
```

Te pedirá la contraseña del usuario `vpsadmin`.

---

### Paso 4: Crear tu usuario en el servidor

Una vez dentro del servidor, crea un usuario con tu nombre:

```bash
sudo adduser tu-nombre
```

---

### Paso 5: Crear el directorio .ssh para tu usuario

```bash
sudo mkdir -p /home/tu-nombre/.ssh
```

---

### Paso 6: Añadir tu clave pública

Crea el archivo `authorized_keys` con tu clave pública:

```bash
sudo nano /home/tu-nombre/.ssh/authorized_keys
```

Pega tu clave pública (la que copiaste en el paso 2) y guarda el archivo y cierra (Ctrl+O, Enter, Ctrl+X).

---

### Paso 7: Configurar permisos correctos

Los permisos de SSH son muy estrictos. Si no son correctos, no funcionará:

```bash
sudo chown -R tu-nombre:tu-nombre /home/tu-nombre/.ssh
sudo chmod 700 /home/tu-nombre/.ssh
sudo chmod 600 /home/tu-nombre/.ssh/authorized_keys
```

Explicación:
- `chown -R`: Cambia el propietario del directorio y su contenido
- `chmod 700`: Solo el propietario puede entrar al directorio
- `chmod 600`: Solo el propietario puede leer el archivo de claves

---

### Paso 8: Desconectarse y probar

Sal del servidor:

```bash
exit
```

Ahora intenta conectarte con tu usuario:

```bash
ssh -i <ruta-a-la-clave-pública> tu-nombre@DOMINIO_DEL_SERVER
```

> **Nota:** En este momento el servidor acepta tanto claves como contraseñas. Nostros estamos pasando la clave, el agente te pedirá la contraseña de la privada y no será necesario introducir la contraseña del usuario remoto.

Busca una línea como esta en la salida:

```
debug1: Authentications that can continue: publickey,password
debug1: Offering public key: /home/tu-usuario/.ssh/id_ecdsa ECDSA SHA256:...
debug1: Server accepts key: /home/tu-usuario/.ssh/id_ecdsa ECDSA SHA256:...
debug1: Authentication succeeded (publickey).
```

Si ves `Authentication succeeded (publickey)`, tu clave está funcionando correctamente.

---

## Parte 3: Crear un alias con SSH config

### Paso 9: Crear el archivo de configuración SSH

En tu máquina local, crea o edita el archivo `~/.ssh/config`:

```bash
nano ~/.ssh/config
```

Añade esta configuración:

```
Host lab
    HostName IP_DEL_SERVIDOR
    User tu-nombre
    IdentityFile ~/.ssh/id_ed25519
```

Guarda y cierra.

---

### Paso 10: Conectarse usando el alias

Ahora puedes conectarte simplemente con:

```bash
ssh lab
```

Mucho más cómodo que escribir la IP y el usuario cada vez.

---

## Parte 4: Deshabilitar acceso por contraseña

Una vez que todos tengan configurado el acceso por claves, es buena práctica deshabilitar el acceso por contraseña.

> **Advertencia:** Asegúrate de que tu acceso por claves funciona antes de hacer esto. Si no, podrías quedarte sin acceso al servidor.

### Paso 11: Editar la configuración de SSH en el servidor

Conéctate al servidor y edita el archivo de configuración:

```bash
ssh lab
sudo nano /etc/ssh/sshd_config
```

Busca estas líneas y cámbialas (o añádelas si no existen):

```
PasswordAuthentication no
PubkeyAuthentication yes
```

Guarda el archivo.

---

### Paso 12: Reiniciar el servicio SSH

Para aplicar los cambios:

```bash
sudo systemctl restart sshd
```
---

¡Listo! Ahora tienes acceso seguro al servidor usando claves SSH, sin necesidad de recordar contraseñas.
