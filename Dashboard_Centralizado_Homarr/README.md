# 📊 Creación de un Dashboard Centralizado con Homarr

Este proyecto documenta la integración de Homarr como panel de control unificado para organizar los servicios del homelab de forma visual y centralizada.

## 🚀 Características Principales
* **Gestión 100% Web:** Toda la personalización y configuración se realiza directamente desde el navegador, eliminando la necesidad de modificar archivos de texto.
* **Entorno Multiusuario:** Soporta la creación de paneles independientes con inicio de sesión para cada usuario, así como páginas públicas de acceso directo.

## 🛠️ Despliegue en Portainer (Stack)
La implementación se ejecuta creando un nuevo *Stack* en Portainer con la imagen oficial de Homarr.

### Configuración de `docker-compose`
```yaml
services:
  homarr:
    container_name: homarr
    image: ghcr.io/homarr-labs/homarr:latest
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock # Integracion Docker (opt)
      - ./appdata:/appdata
    environment:
      - SECRET_ENCRYPTION_KEY=${SECRET_KEY}
    ports:
      - '7575:7575'
Variables de EntornoLa clave de cifrado ${SECRET_KEY} se inyecta de forma segura a través del apartado Environment variables de Portainer. Esta clave se genera previamente desde una terminal Linux mediante el comando:  Bashopenssl rand -hex 32
