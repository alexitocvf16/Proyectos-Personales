# 🛡️ Despliegue de Pi-hole mediante Portainer

Implementación de Pi-hole (DNS sinkhole) a través de un *Stack* en Portainer para el bloqueo de rastreadores y anuncios a nivel de red local.

## 🚀 Despliegue del Stack
La configuración se realiza utilizando la herramienta *Web editor* de Portainer y la imagen oficial alojada en Docker Hub (`pihole/pihole:latest`).

### Configuración del Docker Compose
Se orquesta el contenedor mapeando los puertos estándar de resolución DNS y tráfico web HTTP, asegurando la persistencia de datos.
La variable de entorno `FTLCONF_webserver_api_password` se utiliza para establecer una contraseña segura para el panel de administración, inyectando su valor a través de la interfaz de *Environment variables* para no exponerla en el YAML.

## ⚙️ Inicialización
Una vez desplegado el stack, se verifica que el estado del contenedor cambie a *Healthy* en la lista de contenedores. La consola de administración queda accesible a través de la IP local en la ruta `/admin` (`http://ip_localhost/admin`).
