---
layout: default
title: Gestión de usuarios
---

# Gestión de usuarios y grupos

En Linux, los usuarios y grupos son fundamentales para controlar quién puede hacer qué.

No vamos a crear grupos porque sí: cada grupo tiene su propósito.

---

## El escenario

Te hace ilusión que cuando tus amigos se conecten al servidor puedan dejarte mensajitos en un archivo. Sin embargo también hay gente que te cae fatal y que no quieres que lo lea siquiera.

Vamos a darles permiso solo a ellos. Para conseguirlo necesitaremos:
1. Crear un grupo llamado `amigos`
2. Añadir a los usuarios al grupo
3. Configurar los permisos del archivo correctamente

> **Nota:** Los comandos de gestión de usuarios requieren permisos de administrador, así que usaremos `sudo` constantemente.

---

## Parte 1: Crear el grupo y el usuario

### Paso 1: Crear el grupo "amigos"

Primero, creamos un grupo que contendrá a todos nuestros amigos:

```bash
sudo groupadd amigos
```

Podemos verificar que se ha creado mirando el archivo `/etc/group`:

```bash
grep amigos /etc/group
```

```
amigos:x:1001:
```

El formato es: `nombre:contraseña:GID:miembros`. El grupo está vacío por ahora.

---

### Paso 2: Crear un usuario para tu amigo

Claro, aquí hay un problema, y es que tus amigos no tienen usuario en el ordenador. (Así es complicado que te escriban nada...)

Vamos a crear un usuario llamado `ines` (porque soy una chica super maja):


```bash
sudo adduser ines
```
> **NOTA** También puedes usar `-m useradd` y `passwd`, pero es una forma un poco más enrevesada que te exige especificar los detalles con las opciones.

Te pedirá que escribas la contraseña dos veces. Elige algo sencillo para la práctica (como `amigo123`).

---

### Paso 3: Añadir el usuario al grupo amigos

Ahora añadimos a Inés al grupo `amigos`:

```bash
sudo usermod -aG amigos ines
```

Explicación:
- `-a`: Append (añadir, sin quitar de otros grupos)
- `-G amigos`: Al grupo "amigos"

Verificamos que está en el grupo:

```bash
groups ines
```

```
ines : ines amigos
```

Inés ahora pertenece a su grupo personal (`ines`) y al grupo `amigos`.

---

### Paso 4: Añadir el usuario al grupo sudo

Añado una nota sobre un grupo al que muy probablemente añadais a usuarios: el grupo sudo.

Si no es al grupo sudo, en su lugar es probable que inventes un grupo y le des permisos sudo limitados en sudoers usando `visudo`

Para que Inés pueda ejecutar comandos como administrador, la añadimos al grupo `sudo`:

```bash
sudo usermod -aG sudo ines
```

Verificamos:

```bash
groups ines
```

```
ines : ines amigos sudo
```

> **Nota:** En algunas distribuciones como mi Fedora el grupo de administradores se llama `wheel` en lugar de `sudo`.

---

## Parte 2: Crear el buzón de mensajes

### Paso 5: Añadirte a ti mismo al grupo amigos

Para que el sistema de "amigos" tenga sentido, vamos a añadirte a ti también al grupo de `amigos`:

```bash
sudo usermod -aG amigos $USER
```

**Importante:** Los cambios de grupo no se aplican a tu sesión actual. Necesitas cerrar sesión y volver a entrar.

Verifica tus grupos:

```bash
groups
```

Deberías ver `amigos` en la lista.

---

### Paso 6: Crear el archivo de mensajes

Vamos a crear un archivo en tu home donde recibirás los mensajes:

```bash
touch ~/buzon_amigos.txt
echo "=== Buzón de mensajes ===" > ~/buzon_amigos.txt
```

---

### Paso 7: Configurar el propietario y grupo

Ahora cambiamos el grupo del archivo a `amigos`:

```bash
chgrp amigos ~/buzon_amigos.txt
ls -l ~/buzon_amigos.txt
```

```
-rw-r--r-- 1 tu-usuario amigos 27 feb 21 12:00 buzon_amigos.txt
```

El archivo ahora pertenece a tu usuario y al grupo `amigos`.

---

### Paso 8: Configurar los permisos

Queremos que:
- **Tú** (propietario): puedas leer y escribir
- **Amigos** (grupo): puedan leer y escribir (para dejar mensajes)
- **Otros**: no puedan hacer nada

```bash
chmod 660 ~/buzon_amigos.txt
ls -l ~/buzon_amigos.txt
```

```
-rw-rw---- 1 tu-usuario amigos 27 feb 21 12:00 buzon_amigos.txt
```

Perfecto:
- `rw-` para el propietario (6)
- `rw-` para el grupo amigos (6)
- `---` para otros (0)

---

## Parte 3: Probar el sistema

### Paso 9: Cambiar al usuario amigo

Vamos a probar que Inés puede escribir en el buzón:

```bash
su - ines
```

Te pedirá la contraseña de Inés. Ahora eres Inés.

---

### Paso 10: Dejar un mensaje

Como Inés, intenta escribir en el buzón:

```bash
echo "Hola! Soy Inés, ¿te está gustando el taller de Linux?" >> /home/tu-usuario/buzon_amigos.txt
```

(Sustituye `tu-usuario` por tu nombre de usuario real)

Si todo está bien configurado, el comando funcionará sin errores.

Puedes verificar:

```bash
cat /home/tu-usuario/buzon_amigos.txt
```

```
=== Buzón de mensajes ===
Hola! Soy Inés, ¿te está gustando el taller de Linux?
```

---

### Paso 11: Volver a tu usuario

Sal del usuario Inés:

```bash
exit
```

Ahora estás de vuelta en tu usuario original.

---

## Parte 4: Verificar que otros no pueden acceder

### Paso 13: Crear un usuario que NO es amigo

Vamos a crear otro usuario que no esté en el grupo:

```bash
sudo adduser intruso
```

---

### Paso 14: Intentar acceder como intruso

```bash
su - intruso
cat /home/tu-usuario/buzon_amigos.txt
```

```
cat: /home/tu-usuario/buzon_amigos.txt: Permiso denegado
```

¡Perfecto! El intruso no puede leer el archivo.

Intenta escribir:

```bash
echo "Mensaje no autorizado" >> /home/tu-usuario/buzon_amigos.txt
```

```
bash: /home/tu-usuario/buzon_amigos.txt: Permiso denegado
```

Tampoco puede escribir. El sistema funciona correctamente.

Sal del usuario intruso:

```bash
exit
```

---

### Eliminar usuarios y grupos (limpieza)

Si quieres limpiar después de la práctica:

```bash
# Eliminar usuarios (con su directorio home)
sudo userdel -r ines
sudo userdel -r intruso

# Eliminar grupo
sudo groupdel amigos

# Eliminar el buzón
rm ~/buzon_amigos.txt
```

---

¡Genial! Ahora sabes cómo gestionar usuarios y grupos en Linux.