---
layout: default
title: Chuleta de Linux
---

# Chuleta de Linux

## Rutas

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| Absoluta | Empieza desde la raíz `/` | `/home/usuario/documentos/archivo.txt` |
| Relativa | Desde el directorio actual | `documentos/archivo.txt` |
| Desde home `~` | `~` equivale a `/home/tu_usuario` | `~/documentos/archivo.txt` |
| Directorio actual `.` | Referencia al directorio actual | `./script.sh` |
| Directorio padre `..` | Sube un nivel | `../otro_directorio/archivo.txt` |

## Directorios

### Comandos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `pwd` | Muestra el directorio actual | `pwd` |
| `cd <dir>` | Cambia al directorio indicado | `cd /home/usuario` |
| `ls` | Lista el contenido del directorio | `ls` |
| `ls -l` | Lista con detalles (permisos, tamaño, fecha) | `ls -l` |
| `ls -a` | Muestra archivos ocultos (empiezan por `.`) | `ls -a` |
| `ls -i` | Muestra el número de inodo de cada archivo | `ls -i` |
| `ls -lh` | Lista con tamaños legibles (KB, MB) | `ls -lh` |
| `ls -R` | Lista recursivamente subdirectorios | `ls -R` |
| `tree` | Muestra estructura en árbol | `tree` |
| `tree -L 2` | Árbol limitado a 2 niveles | `tree -L 2` |
| `tree -d` | Solo muestra directorios | `tree -d` |
| `mkdir <dir>` | Crea un directorio | `mkdir proyecto` |
| `mkdir -p <ruta>` | Crea directorios anidados | `mkdir -p src/main/java` |
| `rmdir <dir>` | Elimina directorio vacío | `rmdir temporal` |
| `rm -r <dir>` | Elimina directorio y su contenido (r de recursivo) | `rm -r proyecto` |
| `rm -ri <dir>` | Elimina preguntando por cada archivo | `rm -ri proyecto` |
| `rm -rf <dir>` | Elimina forzadamente (sin confirmación) | `rm -rf build/` |
| `mv <orig> <dest>` | Mueve o renombra directorio | `mv viejo/ nuevo/` |

---

## Archivos

### Comandos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `touch <archivo>` | Crea archivo vacío o actualiza fecha | `touch notas.txt` |
| `cat <archivo>` | Muestra contenido completo | `cat config.txt` |
| `cat -n <archivo>` | Muestra con números de línea | `cat -n script.sh` |
| `head <archivo>` | Muestra las primeras 10 líneas | `head log.txt` |
| `head -n 5 <archivo>` | Muestra las primeras N líneas | `head -n 5 log.txt` |
| `tail <archivo>` | Muestra las últimas 10 líneas | `tail log.txt` |
| `tail -n 20 <archivo>` | Muestra las últimas N líneas | `tail -n 20 log.txt` |
| `tail -f <archivo>` | Sigue el archivo en tiempo real | `tail -f /var/log/syslog` |
| `less <archivo>` | Navega por el archivo (q para salir) | `less archivo_largo.txt` |
| `echo "texto"` | Muestra texto en pantalla | `echo "Hola mundo"` |
| `echo "texto" > archivo` | Escribe texto (sobrescribe) | `echo "línea" > file.txt` |
| `echo "texto" >> archivo` | Añade texto al final | `echo "otra" >> file.txt` |
| `mv <orig> <dest>` | Mueve o renombra archivo | `mv old.txt new.txt` |
| `cp <orig> <dest>` | Copia archivo | `cp original.txt copia.txt` |
| `cp -r <dir> <dest>` | Copia directorio recursivamente | `cp -r src/ backup/` |
| `rm <archivo>` | Elimina archivo | `rm temporal.txt` |
| `rm -i <archivo>` | Elimina pidiendo confirmación | `rm -i importante.txt` |
| `rm -f <archivo>` | Elimina forzadamente | `rm -f cache.tmp` |

---

## Permisos

### Significado de rwx

| Permiso | En archivos | En directorios |
|---------|-------------|----------------|
| `r` (read) | Leer contenido | Listar contenido (`ls`) |
| `w` (write) | Modificar archivo | Crear/eliminar archivos dentro |
| `x` (execute) | Ejecutar como programa | Entrar al directorio (`cd`) |
| `-` | Sin permiso | Sin permiso |

### Estructura de permisos

```
-rwxr-xr-- 1 usuario grupo 1234 feb 20 10:30 archivo.txt
│└┬┘└┬┘└┬┘
│ │  │  └── Otros (other)
│ │  └───── Grupo (group)
│ └──────── Usuario propietario (user)
└────────── Tipo: - archivo, d directorio, l enlace
```

### Comandos de permisos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `chmod u+x <archivo>` | Añade ejecución al usuario | `chmod u+x script.sh` |
| `chmod g-w <archivo>` | Quita escritura al grupo | `chmod g-w config.txt` |
| `chmod o=r <archivo>` | Otros solo pueden leer | `chmod o=r public.txt` |
| `chmod a+r <archivo>` | Todos pueden leer | `chmod a+r readme.txt` |
| `chmod 755 <archivo>` | rwxr-xr-x (ejecutable) | `chmod 755 script.sh` |
| `chmod 644 <archivo>` | rw-r--r-- (archivo normal) | `chmod 644 documento.txt` |
| `chmod 700 <archivo>` | rwx------ (solo propietario) | `chmod 700 privado.sh` |
| `chmod -R 755 <dir>` | Aplica permisos recursivamente | `chmod -R 755 proyecto/` |
| `chown <user> <archivo>` | Cambia propietario | `sudo chown root config` |
| `chown user:group <archivo>` | Cambia propietario y grupo | `sudo chown www-data:www-data web/` |
| `chgrp <group> <archivo>` | Cambia solo el grupo | `sudo chgrp developers src/` |
| `chown -R user:group <dir>` | Cambia propietario recursivamente | `sudo chown -R usuario:usuario proyecto/` |

### Tabla de valores numéricos

| Número | Permisos | BInar |
|--------|----------|---------|
| 7 | rwx | 111 |
| 6 | rw- | 110 |
| 5 | r-x | 101 |
| 4 | r-- | 100 |
| 3 | -wx | 011 |
| 2 | -w- | 010 |
| 1 | --x | 001 |
| 0 | --- | 000 |

---

## Buscar cosas

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `which <comando>` | Muestra ruta del ejecutable | `which python` |
| `whereis <comando>` | Muestra ejecutable, fuentes y man | `whereis bash` |
| `locate <nombre>` | Busca archivos por nombre (rápido) | `locate nginx.conf` |
| `locate -i <nombre>` | Búsqueda ignorando mayúsculas | `locate -i readme` |
| `updatedb` | Actualiza base de datos de locate | `sudo updatedb` |
| `find <ruta> -name "patron"` | Busca archivos por nombre | `find /home -name "*.txt"` |
| `find <ruta> -type d` | Busca solo directorios | `find . -type d` |
| `find <ruta> -type f` | Busca solo archivos | `find . -type f -name "*.log"` |
| `grep "texto" <archivo>` | Busca texto en archivo | `grep "error" log.txt` |
| `grep -r "texto" <dir>` | Busca recursivamente en directorio | `grep -r "TODO" src/` |
| `grep -i "texto" <archivo>` | Búsqueda ignorando mayúsculas | `grep -i "warning" log.txt` |
| `grep -n "texto" <archivo>` | Muestra números de línea | `grep -n "main" script.py` |
| `locate python \| grep bin` | Combina locate con grep | `locate .conf \| grep nginx` |

---

## Enlaces

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `ln <archivo> <enlace>` | Crea enlace duro | `ln original.txt enlace_duro` |
| `ln -s <archivo> <enlace>` | Crea enlace simbólico | `ln -s /ruta/original link` |
| `readlink <enlace>` | Muestra destino del enlace | `readlink mi_enlace` |
| `readlink -f <enlace>` | Muestra ruta absoluta del destino | `readlink -f link` |

---

## Gestión de usuarios

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `whoami` | Muestra usuario actual | `whoami` |
| `who` | Muestra usuarios conectados al sistema | `who` |
| `id` | Muestra UID, GID y grupos | `id` |
| `id <usuario>` | Muestra info de otro usuario | `id root` |
| `groups` | Lista grupos del usuario actual | `groups` |
| `su <usuario>` | Cambia a otro usuario (¡No recomendado a root!)| `su - root` |
| `sudo <comando>` | Ejecuta como superusuario | `sudo apt update` |


| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `useradd <usuario>` | Crea usuario | `sudo useradd nuevo` |
| `useradd -m <usuario>` | Crea usuario con directorio home | `sudo useradd -m juan` |
| `userdel <usuario>` | Elimina usuario | `sudo userdel antiguo` |
| `userdel -r <usuario>` | Elimina usuario y su home | `sudo userdel -r antiguo` |
| `passwd <usuario>` | Cambia contraseña | `sudo passwd juan` |
| `groupadd <grupo>` | Crea grupo | `sudo groupadd developers` |
| `groupdel <grupo>` | Elimina grupo | `sudo groupdel temporal` |
| `usermod -aG <grupo> <user>` | Añade usuario a grupo | `sudo usermod -aG docker juan` |
| `visudo` | Edita sudoers de forma segura | `sudo visudo` |

---

## Instalación de aplicaciones

### Familia Debian

| Comando | Descripción |
|---------|-------------|
| `apt update` | Actualiza lista de paquetes |
| `apt upgrade` | Actualiza paquetes instalados |
| `apt install <paquete>` | Instala paquete |
| `apt remove <paquete>` | Desinstala paquete |
| `apt purge <paquete>` | Desinstala paquete y configuración |
| `apt search <nombre>` | Busca paquetes |
| `apt show <paquete>` | Muestra info del paquete |
| `apt autoremove` | Elimina dependencias huérfanas |