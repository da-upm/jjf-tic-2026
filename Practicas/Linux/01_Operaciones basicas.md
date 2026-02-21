---
layout: default
title: Operaciones básicas
---

# Operaciones básicas

Vamos a empezar desde lo más básico: manejando archivos y directorios en un sistema linux.
Esto es esencial porque la filosofía de linux es que TODO parezca un archivo: procesos, dispositivos y sockets de red. Estas herramientas serán de utilidad en más contextos de los que uno inicialmente cree.

> Si nunca has ejecutado comandos en un terminal, es probable que la interfaz te asuste un poco, sin embargo todo va a ser más sencillo de lo que parece.

---

## Parte 1: Manejo de archivos y directorios

### Objetivo

Vamos a crear la siguiente estructura de archivos en nuestra carpeta de usuario:

```
tu-usuario
├──directorio-1
│   ├──vacio
│   ├──lectura (contenido1)
│   └──escritura (contenido1)
├──directorio-2
│   └──directorio-2-1
│       └──vacio
└──directorio-3
    └──vacio
```

Para luego borrar directorio-2 y cambiar el nombre de directorio-3 a directorio-2.

---

### Paso 1: ¿Dónde estamos?

Antes de empezar a crear cosas, es buena idea saber en qué carpeta nos encontramos. Para eso usamos el comando `pwd` (*print working directory*), que nos muestra la ruta completa del directorio actual.

```bash
pwd
```

Deberías ver algo como `/home/tu-usuario`. Esta es tu carpeta personal, donde vamos a trabajar.

---

### Paso 2: Crear los directorios principales

Ahora vamos a crear nuestros tres directorios. Necesitamos crear `directorio-2-1` dentro de `directorio-2`. 

El comando `mkdir` (*make directory*) crea carpetas nuevas.
Para crear carpetas dentro de carpetas que aún no existen, usamos la opción `-p` (*parents*), que crea toda la ruta necesaria.

>Sin el `-p`, el comando fallaría si `directorio-2` no existiera.

```bash
mkdir directorio-1
mkdir -p directorio-2/directorio-2-1
mkdir directorio-3
```

> **Truco:** También podrías crear los tres de golpe separándolos con espacios:  
> `mkdir -p directorio-1 directorio-2/directorio-2-1 directorio-3`

---

### Paso 3: Comprobar la estructura

Vamos a ver qué hemos creado hasta ahora. El comando `tree` nos muestra la estructura de directorios de forma visual.

```bash
tree
```

Deberías ver algo así:

```
.
├── directorio-1
├── directorio-2
│   └── directorio-2-1
└── directorio-3
```

> **Nota:** Si `tree` no está instalado, puedes usar `ls -R` para ver el contenido recursivamente, aunque es menos visual.

---

### Paso 4: Crear archivos vacíos

Ahora vamos a crear los archivos. El comando `touch` crea archivos vacíos (o actualiza la fecha de modificación si ya existen).

```bash
touch directorio-1/vacio directorio-1/lectura directorio-1/escritura
touch directorio-2/directorio-2-1/vacio
touch directorio-3/vacio
```

Comprueba que se han creado con `tree`:

```bash
tree
```

```
.
├── directorio-1
│   ├── escritura
│   ├── lectura
│   └── vacio
├── directorio-2
│   └── directorio-2-1
│       └── vacio
└── directorio-3
    └── vacio
```

---

### Paso 5: Crear archivos con contenido

Para los archivos `lectura` y `escritura` necesitamos escribir contenido dentro. El comando `echo` muestra texto en pantalla, pero si lo combinamos con `>` podemos redirigir ese texto a un archivo.

```bash
echo "contenido1" > directorio-1/lectura
echo "contenido1" > directorio-1/escritura
```

> **Importante:** El símbolo `>` **sobrescribe** el archivo si ya existe. Si quisieras **añadir** contenido al final, usarías `>>`.

---

### Paso 6: Ver el contenido de un archivo

¿Cómo sabemos que el contenido se ha escrito correctamente? Con el comando `cat` (*concatenate*) podemos ver el contenido de un archivo.

```bash
cat directorio-1/lectura
```

Deberías ver:
```
contenido1
```

---

### Paso 8: Ver detalles con ls -la

Ahora vamos a ver los detalles de los archivos que hemos creado. El comando `ls -la` muestra una lista detallada incluyendo:
- Permisos
- Propietario y grupo
- Tamaño
- Fecha de modificación
- Archivos ocultos (los que empiezan por `.`)

Si además añadimos `-i`, veremos el **número de inodo** de cada archivo (un identificador único en el sistema de archivos, nos será de utilidad para entender conceptos posteriores).

```bash
ls -lai directorio-1/
```

Verás algo como:
```
total 16
1234567 drwxr-xr-x 2 tu-usuario tu-usuario 4096 feb 21 10:00 .
1234000 drwxr-xr-x 5 tu-usuario tu-usuario 4096 feb 21 10:00 ..
1234570 -rw-r--r-- 1 tu-usuario tu-usuario   11 feb 21 10:00 escritura
1234569 -rw-r--r-- 1 tu-usuario tu-usuario   11 feb 21 10:00 lectura
1234568 -rw-r--r-- 1 tu-usuario tu-usuario    0 feb 21 10:00 vacio
```

Fíjate que `vacio` tiene tamaño 0, mientras que `lectura` y `escritura` tienen 11 bytes (el texto "contenido1" más un salto de línea).

---

### Paso 9: Eliminar un directorio con contenido

Ahora vamos a eliminar `directorio-2`. Como tiene contenido dentro, no podemos usar `rmdir` (que solo borra carpetas vacías). Necesitamos `rm -r` (*remove recursive*).

```bash
rm -r directorio-2
```

> **Cuidado:** Este comando es muy poderoso. Añade la opción `-i` (interactivo) si quieres que te pida confirmación para cada archivo: `rm -ri directorio-2`. También puedes usar `-f`si te gusta el peligro.

Comprueba que se ha eliminado:
```bash
tree
```
> **Dato curioso:** Es una broma muy extendida decir que hay que "borrar el paquete francés" ejecutando `rm -rf /`. Obviamente **NO lo hagas**: esto borraría todo tu sistema desde la raíz.

---

### Paso 10: Renombrar un directorio

El comando `mv` (*move*) sirve tanto para mover como para renombrar archivos y carpetas. Vamos a renombrar `directorio-3` a `directorio-2`.

```bash
mv directorio-3 directorio-2
```

---

### Paso 11: Verificar el resultado final

```bash
tree
```

```
.
├── directorio-1
│   ├── escritura
│   ├── lectura
│   └── vacio
└── directorio-2
    └── vacio
```

¡Lo conseguimos! Hemos completado el objetivo de esta primera parte.

---

### Comandos extra: head y tail

Antes de terminar, vamos a ver dos comandos muy útiles para archivos largos.

Primero, creemos un archivo con varias líneas:
```bash
seq 1 20 > directorio-1/numeros.txt
```

El comando `head` muestra las primeras líneas de un archivo:
```bash
head -n 5 directorio-1/numeros.txt
```

Y `tail` muestra las últimas:
```bash
tail -n 5 directorio-1/numeros.txt
```

> **Truco:** `tail -f archivo.log` es muy útil para ver logs en tiempo real. ¡Lo usarás mucho cuando administres servidores! Muestra los últimos logs y `-f`(follow) te permite ver en tiempo real los que se vayan escribiendo.

---

### Comando extra: cp (copiar)

El comando `cp` (*copy*) copia archivos. Vamos a hacer una copia de `lectura`:

```bash
cp directorio-1/lectura directorio-1/lectura_backup
```

Para copiar directorios enteros, usa `-r`:
```bash
cp -r directorio-1 directorio-1-copia
```

---

### Resumen de comandos aprendidos

| Comando | Descripción |
|---------|-------------|
| `pwd` | Muestra el directorio actual |
| `mkdir` | Crea directorios |
| `mkdir -p` | Crea directorios anidados |
| `touch` | Crea archivos vacíos |
| `echo "texto" > archivo` | Escribe texto en un archivo |
| `cat` | Muestra el contenido de un archivo |
| `tree` | Muestra la estructura de directorios |
| `ls -la` | Lista detallada con permisos |
| `rm -r` | Elimina directorios con contenido |
| `mv` | Mueve o renombra archivos/directorios |
| `head -n N` | Muestra las primeras N líneas |
| `tail -n N` | Muestra las últimas N líneas |
| `cp` | Copia archivos o directorios |

---

¡Muy bien! Ya dominas lo básico del manejo de archivos y directorios.

---

## Parte 2: Permisos de archivos y directorios

Probablemente te haya llamado la atención que hemos nombrado un par de archivos como lectura y escritura. Pero en ningún momento hemos hecho nada para que tengan permisos distintos.

En Linux, cada archivo y directorio tiene permisos que determinan quién puede leer, escribir o ejecutar. Entender esto es fundamental para la seguridad y el correcto funcionamiento del sistema.

### Entendiendo la salida de ls -l

Cuando ejecutaste `ls -lai` antes, viste algo como esto:

```
-rw-r--r-- 1 tu-usuario tu-usuario 11 feb 21 10:00 lectura
```

Vamos a desglosar esa primera columna de permisos:

```
-rw-r--r--
│└┬┘└┬┘└┬┘
│ │  │  └── Permisos para OTROS (cualquier usuario)
│ │  └───── Permisos para el GRUPO
│ └──────── Permisos para el USUARIO (propietario)
└────────── Tipo: - archivo, d directorio, l enlace
```

### ¿Qué significa cada letra?

| Letra | Significado | En archivos | En directorios |
|-------|-------------|-------------|----------------|
| `r` | Read (lectura) | Ver el contenido | Listar contenido (`ls`) |
| `w` | Write (escritura) | Modificar el archivo | Crear/eliminar archivos dentro |
| `x` | Execute (ejecución) | Ejecutar como programa | Entrar al directorio (`cd`) |
| `-` | Sin permiso | No puede hacer esa acción | No puede hacer esa acción |

> **Importante:** Para un directorio, el permiso `x` es necesario para poder entrar en él con `cd`. Sin `x`, aunque tengas `r`, no podrás acceder.

---

### Paso 1: Preparar el entorno

Vamos a trabajar con los archivos que ya tenemos. Primero, comprobemos los permisos actuales:

```bash
ls -l directorio-1/
```

```
-rw-r--r-- 1 tu-usuario tu-usuario   11 feb 21 10:00 escritura
-rw-r--r-- 1 tu-usuario tu-usuario   11 feb 21 10:00 lectura
-rw-r--r-- 1 tu-usuario tu-usuario    0 feb 21 10:00 vacio
```

Todos tienen permisos `rw-r--r--`:
- **Usuario:** puede leer y escribir
- **Grupo:** solo puede leer
- **Otros:** solo pueden leer

---

### Paso 2: Cambiar permisos con chmod (forma simbólica)

El comando `chmod` (*change mode*) modifica los permisos. La forma simbólica usa letras:

| Símbolo | Significado |
|---------|-------------|
| `u` | Usuario (propietario) |
| `g` | Grupo |
| `o` | Otros |
| `a` | Todos (all) |
| `+` | Añadir permiso |
| `-` | Quitar permiso |
| `=` | Establecer exactamente |

Vamos a quitar el permiso de escritura a todos y el de lectura a "otros" en el archivo `lectura`:

```bash
chmod a-w directorio-1/lectura
chmod o-r directorio-1/lectura
ls -l directorio-1/lectura
```

```
-r--r----- 1 tu-usuario tu-usuario 11 feb 21 10:00 lectura
```

Ahora intentemos escribir en el archivo:

```bash
echo "nuevo contenido" >> directorio-1/lectura
```

```
bash: directorio-1/lectura: Permiso denegado
```

¡Funciona! El sistema no nos deja escribir porque quitamos el permiso `w`. Pero sí podemos leerlo:

```bash
cat directorio-1/lectura
```

```
contenido1
```

---

### Paso 3: Hacer un archivo ejecutable

Vamos a crear un pequeño script y darle permisos de ejecución:

```bash
echo '#!/bin/bash' > directorio-1/mi_script.sh
echo 'echo "¡Hola! Este es mi primer script"' >> directorio-1/mi_script.sh
```

Intentemos ejecutarlo:

```bash
./directorio-1/mi_script.sh
```

```
bash: ./directorio-1/mi_script.sh: Permiso denegado
```

¡No funciona! Necesitamos darle permiso de ejecución:

```bash
chmod u+x directorio-1/mi_script.sh
ls -l directorio-1/mi_script.sh
```

```
-rwxr--r-- 1 tu-usuario tu-usuario 52 feb 21 10:00 mi_script.sh
```

Ahora tiene una `x` en los permisos del usuario. Ejecutémoslo:

```bash
./directorio-1/mi_script.sh
```

```
¡Hola! Este es mi primer script
```

---

### Paso 4: Cambiar permisos con chmod (forma numérica)

La forma numérica es más rápida cuando sabes lo que quieres.

Cada conjunto de permisos es un número de 3 bits que va del 0 al 7.

Se suman para cada grupo (usuario, grupo, otros):

| Número | Permisos | Binario |
|--------|----------|---------|
| 7 | rwx | 111 |
| 6 | rw- | 110 |
| 5 | r-x | 101 |
| 4 | r-- | 100 |
| 0 | --- | 000 |

Por ejemplo, `chmod 755` significa:
- Usuario: 7 (rwx)
- Grupo: 5 (r-x)
- Otros: 5 (r-x)

Vamos a probarlo:

```bash
chmod 600 directorio-1/escritura
ls -l directorio-1/escritura
```

```
-rw------- 1 tu-usuario tu-usuario 11 feb 21 10:00 escritura
```

Ahora solo el propietario puede leer y escribir. Nadie más puede ni siquiera verlo.

---

### Paso 5: Permisos comunes

Aquí tienes los permisos más utilizados:

| Permiso | Uso típico |
|---------|------------|
| `644` | Archivos normales (documentos, configs) |
| `755` | Scripts y ejecutables |
| `700` | Archivos privados (solo propietario) |
| `777` | Todos pueden todo (¡evítalo si puedes!) |

```bash
chmod 644 directorio-1/lectura
chmod 755 directorio-1/mi_script.sh
```

---

### Paso 6: Cambiar el propietario con chown

El comando `chown` (*change owner*) cambia el propietario de un archivo. Podemos escribir usuario:grupo para cambiar ambas cosas a la vez y ahorrarnos usar `chgrp`

```bash
chown tu-usuario:tu-usuario directorio-1/vacio
```

> **Nota**: para cambiar a un grupo del que no eres miembro/otro usuario necesitarás permisos de administrador/superusuario.

---

### Paso 8: Aplicar permisos recursivamente

Para aplicar permisos a un directorio y todo su contenido, usa `-R`:

```bash
chmod -R 755 directorio-1/
```

Esto aplica `755` al directorio y a todos los archivos dentro. 

> **Cuidado:** Ten cuidado con `-R`, especialmente combinado con `777`. Podrías dejar archivos sensibles accesibles a cualquiera.

---

### Resumen de comandos de permisos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `chmod u+x` | Añade ejecución al usuario | `chmod u+x script.sh` |
| `chmod g-w` | Quita escritura al grupo | `chmod g-w config.txt` |
| `chmod o=r` | Otros solo pueden leer | `chmod o=r public.txt` |
| `chmod 755` | rwxr-xr-x | `chmod 755 script.sh` |
| `chmod 644` | rw-r--r-- | `chmod 644 archivo.txt` |
| `chmod -R` | Aplica recursivamente | `chmod -R 755 carpeta/` |
| `chown` | Cambia propietario | `sudo chown root archivo` |
| `chown user:group` | Cambia propietario y grupo | `sudo chown juan:devs archivo` |
| `chgrp` | Cambia solo el grupo | `sudo chgrp developers archivo` |

---

¡Listo! Espero que el terminal se sienta un poquito menos lejano que antes y que ahora estés más cómodo manejando archivos en Linux.
