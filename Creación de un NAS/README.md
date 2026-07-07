# 📂 Creación de un Servidor NAS en Entornos Linux (Ubuntu) y Windows Server

Este proyecto consiste en una guía técnica y práctica para la implementación de un sistema **NAS (Network Attached Storage)** utilizando discos duros locales adicionales conectados a una red LAN. La configuración se realiza en dos entornos de sistemas operativos diferentes de forma independiente: **Linux (Ubuntu Server/Desktop)** y **Windows Server**.

---

## 🚀 ¿Qué es un NAS?
Un **NAS** es un dispositivo de almacenamiento centralizado conectado a una red local (LAN). Funciona como una nube privada que permite a múltiples usuarios y dispositivos (ordenadores, móviles, tablets) guardar, compartir y acceder a archivos de manera simultánea sin depender de un PC encendido de forma individual. Es una solución ideal para centralizar copias de seguridad y documentos compartidos en entornos domésticos y empresariales.

---

## 🛠️ Requerimientos del Sistema
La práctica y entorno de pruebas se ha desarrollado utilizando virtualización:
* **Hipervisor:** Oracle VirtualBox.
* **Máquina Virtual 1:** Linux Ubuntu.
* **Máquina Virtual 2:** Windows Server.
* **Almacenamiento:** Discos virtuales SATA secundarios dedicados en cada máquina virtual para simular el almacenamiento local del NAS.

---

## 📋 Puntos Clave del Proyecto

### 1. Despliegue en Linux (Ubuntu) 🐧
* **Preparación del almacenamiento:** Inicialización, particionado (`fdisk`) y formateo en el sistema de archivos Linux nativo `ext4` del nuevo disco virtual (`/dev/sdb`).
* **Montaje persistente:** Creación del punto de montaje en `/mnt/nas` y configuración del archivo `/etc/fstab` para asegurar el automontaje del almacenamiento tras los reinicios del sistema.
* **Servicio de Red (Samba):** Instalación y puesta en marcha del protocolo **SMB/CIFS** mediante Samba (`smbd`).
* **Control de accesos y seguridad:** Configuración del archivo `smb.conf` para gestionar recursos compartidos restringidos por credenciales de usuario y el establecimiento de permisos UNIX locales estrictos (`chmod 770` y `chown`).

### 2. Despliegue en Windows Server 🪟
* **Administración de almacenamiento:** Inicialización y formateo en sistema de archivos `NTFS` del volumen adicional mediante el Administrador de Discos de Windows Server.
* **Roles del Servidor:** Instalación y aprovisionamiento del rol de **Servicios de archivos y almacenamiento** junto con el rol de **Servidor de archivos (SMB)**.
* **Recursos Compartidos:** Uso del asistente de almacenamiento para configurar una carpeta compartida en red con el nombre de recurso `NAS`.
* **Permisos NTFS Avanzados:** Creación de cuentas de usuario locales dedicadas y personalización fina de las directivas de seguridad y control de acceso (ACLs) a nivel de red y de carpeta local.

---

## 🔍 Resumen del Flujo de Implementación

### 🔹 Fase de Almacenamiento Local
Tanto en Linux como en Windows Server, el primer paso indispensable es preparar el hardware lógico. Se añade un volumen en crudo a la máquina virtual, se le da una estructura de partición adecuada y se le dota de un sistema de archivos compatible (`ext4` en Linux y `NTFS` en Windows).

### 🔹 Fase de Compartición en Red
Una vez el almacenamiento está operativo localmente, se expone hacia la red LAN utilizando el protocolo **SMB**. En Linux esto se logra editando el daemon de Samba, mientras que en Windows Server se utiliza la consola del Administrador del Servidor para desplegar las características de red.

### 🔹 Fase de Seguridad y Auditoría de Acceso
Para proteger la integridad de los datos, en ambos sistemas se anula el acceso por defecto a usuarios "Invitados" no autenticados. Se crean cuentas de usuario personalizadas (`Alexito`) con contraseñas seguras y se asocian permisos directos para que solo los usuarios autorizados de la red puedan mapear e interactuar con la unidad de red local.

---

## 📈 Resultados Obtenidos
Al finalizar las dos configuraciones, se verifica la correcta accesibilidad al almacenamiento centralizado desde la máquina anfitriona enlazada a la red. Introduciendo la dirección IP correspondiente (`\\192.168.1.XX`) y las credenciales creadas, se logra mapear y utilizar el almacenamiento NAS de forma transparente y segura en la red LAN.
