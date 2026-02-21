---
layout: default
title: Instalación de aplicaciones
---

# Instalación de aplicaciones

En Debian instalaremos practicamente todo a través del gestor de paquetes `apt`.

## Comandos de APT

| Comando | Descripción |
|---------|-------------|
| `apt update` | Actualiza la lista de paquetes disponibles |
| `apt upgrade` | Actualiza todos los paquetes instalados |
| `apt install paquete` | Instala un paquete |
| `apt remove paquete` | Desinstala un paquete (mantiene configuración) |
| `apt purge paquete` | Desinstala un paquete y su configuración |
| `apt autoremove` | Elimina dependencias huérfanas |
| `apt search texto` | Busca paquetes por nombre o descripción |
| `apt show paquete` | Muestra información de un paquete |
| `apt list --installed` | Lista paquetes instalados |
| `apt list --upgradable` | Lista paquetes con actualizaciones disponibles |

> **Nota:** Todos estos comandos requieren `sudo` excepto `search`, `show` y `list`.

---

Para muchas aplicaciones poner `sudo apt install aplicación` es suficiente.

Pero para muchas otras es necesario añadir repositorios, configurar claves...

Como cada aplicación es un mundo es super importante seguir la **documentación oficial**.0

En esta práctica vamos a instalar en nuestra máquina el software que vamos a usar el resto del día.

---

## Ejercicio 1: Docker

📖 **Documentación oficial:** [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

### Tareas

1. Selecciona tu distribución en la documentación, usa apt
2. Sigue los pasos de instalación
3. Ejecuta `docker --version` para verificar la instalación

---

## Ejercicio 2: Nginx

📖 **Documentación oficial:** [https://nginx.org/en/linux_packages.html](https://nginx.org/en/linux_packages.html)

### Tareas

1. Sigue las instrucciones para tu distribución
2. Inicia el servicio: `sudo systemctl start nginx`
3. Habilita el inicio automático: `sudo systemctl enable nginx`
4. Ejecuta `nginx --version`

---

## Ejercicio 3: Certbot

📖 **Documentación oficial:** [https://certbot.eff.org/instructions](https://certbot.eff.org/instructions)

### Tareas

1. Selecciona tu servidor web (Nginx) y sistema operativo
2. Usa pip
3. Verifica la instalación con `certbot --version`

> **Nota:** Para obtener un certificado real necesitas un dominio público. En esta práctica solo instalamos la herramienta.

---

## Notas

- Si tienes problemas, lee los mensajes de error con atención
- Busca el error exacto en internet si no lo entiendes
- La documentación oficial siempre es la fuente más fiable
