# 🐳 Instalación y Configuración de Docker Portainer

Despliegue de Portainer Community Edition para la gestión visual centralizada de contenedores, stacks y volúmenes de Docker.

## 🚀 Despliegue de la Infraestructura
La instalación se realiza nativamente mediante la interfaz de comandos de Docker.
1. **Creación del volumen persistente:** `docker volume create portainer_data`.
2. **Ejecución del contenedor:** Despliegue de la imagen `portainer/portainer-ce:latest` en modo *detached*, vinculando el socket nativo y exponiendo los puertos de red.
   `docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest`.

## ⚙️ Verificación y Acceso Web
* Se confirma el estado activo del contenedor usando el comando `docker ps`.
* El acceso administrativo se realiza vía web a través de una conexión cifrada en el puerto `9443` (`https://ip_servidor:9443`).
* Si la plataforma requiere un token de inicialización en el primer inicio de sesión, se extrae mediante la instrucción `docker logs portainer`.
