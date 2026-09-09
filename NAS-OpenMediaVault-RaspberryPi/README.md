# 🗄️ Creación de un NAS con OpenMediaVault en Raspberry Pi 5 y SSD

Transformación de una Raspberry Pi 5 en un servidor NAS utilizando OpenMediaVault (OMV) y almacenamiento sólido (SSD) externo.

## 🚀 Requisitos Previos y Limpieza del Sistema
Para garantizar una instalación limpia sin conflictos, se elimina el entorno gráfico del sistema operativo base.
1. **Deshabilitar el inicio gráfico:** `sudo systemctl set-default multi-user.target`.
2. **Purgar paquetes visuales:** Se elimina el servidor gráfico (X11) y el gestor de inicio de sesión (`xserver-xorg lightdm raspberrypi-ui-mods`).
3. **Conexión de Hardware:** El SSD (ej. SanDisk de 1TB) se conecta estrictamente a los puertos USB 3.0 (color azul) para evitar cuellos de botella en las transferencias de red.

## 🛠️ Instalación de Open Media Vault
Se descarga y ejecuta el script de instalación oficial para desarrolladores. Tras el reinicio, el panel de control web es accesible en la IP de la Raspberry Pi con las credenciales por defecto (`admin` / `openmediavault`).

## ⚙️ Aprovisionamiento del Almacenamiento (SSD)
* **Formateo:** El SSD se formatea en el sistema de archivos nativo de Linux `EXT4` desde el menú *Sistema de Archivos*.
* **Montaje:** Se monta el volumen habilitando advertencias de umbral de uso al 80%.
* **Estructura:** Se crea la carpeta compartida raíz (ej. `NAS/`) asociada a la unidad montada.

## 🔐 Configuración del Servicio SMB/CIFS
Para compartir los directorios en la red local se habilita el protocolo SMB/CIFS.
* **Seguridad (Cifrado):** En las *Opciones extra* se incluye `server min protocol = SMB3` para rechazar conexiones SMBv1 y forzar criptografía moderna.
* **Permisos y ACL:** Se otorgan permisos directos de Lectura/Escritura al administrador y a usuarios autorizados, configurando el acceso de invitados como *Sin acceso*.
