---
layout: default
title: Enlaces duros y simbólicos
---

# Enlaces duros y simbólicos

En Linux existe un concepto muy útil: los **enlaces**.
Son accesos al mismo archivo desde diferentes ubicaciones.

¿Recuerdas los **inodos** que vimos con `ls -li`? Ahora van a cobrar sentido.

---

## ¿Qué es un inodo?

Antes de hablar de enlaces, necesitamos entender qué es un inodo.

En Linux, cada archivo tiene dos partes:
1. **El nombre** (lo que ves en el sistema de archivos)
2. **El inodo** (donde realmente están los datos y metadatos)

El nombre es solo una "etiqueta" que apunta al inodo. Un mismo inodo puede tener varios nombres apuntándole. Esto es la base de los enlaces duros.

---

## Parte 1: Enlaces duros (Hard Links)

### ¿Qué es un enlace duro?

Un enlace duro es simplemente **otro nombre para el mismo archivo**. Ambos nombres apuntan al mismo inodo, es decir, a los mismos datos en disco.

### Paso 1: Preparar el entorno

Vamos a crear un directorio de trabajo y un archivo para experimentar:

```bash
mkdir ~/practica-enlaces
cd ~/practica-enlaces
echo "Este es el contenido original" > archivo1.txt
```

Veamos el inodo del archivo:

```bash
ls -li archivo1.txt
```

```
1234567 -rw-r--r-- 1 tu-usuario tu-usuario 31 feb 21 11:00 archivo1.txt
```

Fíjate en el número `1` después de los permisos. Ese es el **contador de enlaces**: cuántos nombres apuntan a este inodo.

---

### Paso 2: Crear un enlace duro

Usamos el comando `ln` (link) para crear un enlace duro:

```bash
ln archivo1.txt archivo2.txt
ls -li
```

```
1234567 -rw-r--r-- 2 tu-usuario tu-usuario 31 feb 21 11:00 archivo1.txt
1234567 -rw-r--r-- 2 tu-usuario tu-usuario 31 feb 21 11:00 archivo2.txt
```

¡Observa!
- **Mismo inodo** (1234567) para ambos archivos
- El contador de enlaces ahora es **2**
- Ambos tienen el mismo tamaño

No hemos copiado el archivo, simplemente hemos creado otro nombre que apunta a los mismos datos.

---

### Paso 3: Comprobar que son el mismo archivo

Si modificamos uno, el otro también cambia (porque son el mismo archivo):

```bash
echo "Añadiendo más contenido" >> archivo1.txt
cat archivo2.txt
```

```
Este es el contenido original
Añadiendo más contenido
```

¡El cambio aparece en ambos! Porque realmente es el mismo archivo con dos nombres.

---

### Paso 4: Eliminar un enlace duro

¿Qué pasa si borramos uno de los archivos?

```bash
rm archivo1.txt
cat archivo2.txt
```

```
Este es el contenido original
Añadiendo más contenido
```

¡El contenido sigue ahí! Cuando borras un enlace duro, solo eliminas ese nombre. El archivo real (el inodo) se borra cuando el contador de enlaces llega a 0.

```bash
ls -li archivo2.txt
```

```
1234567 -rw-r--r-- 1 tu-usuario tu-usuario 55 feb 21 11:00 archivo2.txt
```

El contador ha vuelto a 1.

> **CUIDADO**: Si editas uno de los archivos, el resultado depende del editor. `vi`y `gedit` mantendrán el enlace por defecto, pero puede que comidicar el nombre de uno rompa el enlace y resulte en la creación de dos objetos.

---

## Parte 2: Enlaces simbólicos (Soft Links / Symlinks)

### ¿Qué es un enlace simbólico?

Un enlace simbólico es un archivo especial que **apunta al nombre de otro archivo**, no al inodo.

### Paso 5: Crear un enlace simbólico

Usamos `ln -s` (la "s" es de *symbolic*):

```bash
ln -s archivo2.txt enlace_simbolico.txt
ls -li
```

```
1234567 -rw-r--r-- 1 tu-usuario tu-usuario 55 feb 21 11:00 archivo2.txt
9876543 lrwxrwxrwx 1 tu-usuario tu-usuario 12 feb 21 11:05 enlace_simbolico.txt -> archivo2.txt
```

¡Observa las diferencias!
- **Inodo diferente** (9876543 vs 1234567)
- El tipo es `l` (link) en vez de `-` (archivo)
- Muestra `->` indicando a dónde apunta
- Los permisos son `rwxrwxrwx` (pero no importan, se usan los del archivo destino)

---

### Paso 6: Usar el enlace simbólico

Puedes usar el enlace como si fuera el archivo original:

```bash
cat enlace_simbolico.txt
```

```
Este es el contenido original
Añadiendo más contenido
```

También puedes escribir a través del enlace:

```bash
echo "Escrito desde el enlace" >> enlace_simbolico.txt
cat archivo2.txt
```

```
Este es el contenido original
Añadiendo más contenido
Escrito desde el enlace
```

---

### Paso 7: Enlaces rotos (dangling links)

¿Qué pasa si borramos el archivo al que apunta el enlace?

```bash
rm archivo2.txt
ls -l
```

```
lrwxrwxrwx 1 tu-usuario tu-usuario 12 feb 21 11:05 enlace_simbolico.txt -> archivo2.txt
```

El enlace sigue existiendo, pero ahora está "roto":

```bash
cat enlace_simbolico.txt
```

```
cat: enlace_simbolico.txt: No existe el fichero o el directorio
```

Esto se llama un **enlace colgante** (*dangling link*). El enlace apunta a algo que ya no existe.

> **Nota:** Con `ls` verás el enlace roto resaltado en rojo (en la mayoría de terminales).

---

### Paso 8: Recrear el archivo

Si volvemos a crear el archivo original, el enlace vuelve a funcionar:

```bash
echo "Archivo recreado" > archivo2.txt
cat enlace_simbolico.txt
```

```
Archivo recreado
```

El enlace apunta al **nombre**, no al contenido. Si el archivo con ese nombre vuelve a existir, el enlace funciona de nuevo.

---

### Paso 9: Enlaces a directorios

A diferencia de los enlaces duros, los simbólicos **sí pueden apuntar a directorios**:

```bash
mkdir mi_carpeta
touch mi_carpeta/documento.txt
ln -s mi_carpeta atajo_carpeta
ls -l
```

```
lrwxrwxrwx 1 tu-usuario tu-usuario 10 feb 21 11:10 atajo_carpeta -> mi_carpeta
drwxr-xr-x 2 tu-usuario tu-usuario 4096 feb 21 11:10 mi_carpeta
```

Ahora puedes acceder a la carpeta por cualquiera de los dos nombres:

```bash
ls atajo_carpeta/
```

```
documento.txt
```

---

### Paso 10: Enlaces con rutas absolutas vs relativas

Cuando creas un enlace simbólico, puedes usar rutas absolutas o relativas:

```bash
# Ruta relativa (lo que hemos hecho hasta ahora)
ln -s mi_carpeta enlace_relativo

# Ruta absoluta
ln -s /home/tu-usuario/practica-enlaces/mi_carpeta enlace_absoluto
```

- **Relativa**: Si mueves el enlace junto con el destino, sigue funcionando
- **Absoluta**: Siempre apunta al mismo lugar, aunque muevas el enlace

---

## Limpieza

Para terminar, limpiamos el directorio de práctica:

```bash
cd ~
rm -r practica-enlaces
```

---

¡Genial! Ya sabes crear enlaces simbólicos. Esto nos será muy útil para gestionar archivos en el servidor. Por ejemplo, es muy común en la organización de configuración de Debian con `sites-available` y `sites-enabled`.