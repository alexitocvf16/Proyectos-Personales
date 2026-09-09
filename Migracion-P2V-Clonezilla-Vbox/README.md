# 🔄 Creación y Restauración de Imágenes para Migración P2V

Este proyecto documenta el proceso técnico de una migración Physical-to-Virtual (P2V) de un servidor basado en Linux (Ubuntu). Se utiliza un enfoque de clonación a nivel de bloques en frío para asegurar la consistencia absoluta del sistema de archivos y evitar la corrupción de datos en entornos de producción críticos.

## 🚀 Objetivos del Proyecto
* Planificar y aprovisionar el almacenamiento óptimo para copias de seguridad de sistemas completos.
* Realizar un respaldo de imagen del sistema "en frío" utilizando herramientas *open source*.
* Desplegar una nueva máquina virtual de destino y realizar el volcado del sistema operativo intacto.
* Validar la integridad post-migración y optimizar el entorno virtualizado mediante la inyección de controladores (*Guest Additions*).

## 🛠️ Herramientas y Entorno del Sistema
* **Software de Clonación:** Clonezilla Live CD (Imagen ISO).
* **Entorno de Virtualización:** Oracle VirtualBox.
* **Sistema Operativo Origen/Destino:** Linux Ubuntu.
* **Almacenamiento:** Discos duros virtuales independientes (VDI) conectados por interfaz SATA.

## 📋 Puntos Clave e Implementación

### 1. Planificación y Preparación Estratégica
* **Herramienta:** Se selecciona **Clonezilla** por su capacidad de operar de forma independiente al sistema operativo nativo (Live CD), bloqueando la escritura en disco durante la copia para asegurar una migración consistente.
* **Almacenamiento:** Se opta por almacenamiento local (un disco VDI adjunto directamente a la máquina) en lugar de recursos en red (SMB/NFS) para maximizar las tasas de transferencia y anular el riesgo de microcortes que corrompan el respaldo.

### 2. Creación de la Imagen de Respaldo (Backup)
Se configura la máquina origen montando la ISO de Clonezilla y el disco duro de almacenamiento para realizar la copia:
* **Modo de Operación:** `device-image` (Disco/Partición a Imagen) seleccionando el dispositivo local (`local_dev`).
* **Parámetros de Clonación:**
  * Modo **Beginner** $\rightarrow$ `savedisk` (Guardar disco local como imagen).
  * **Compresión:** `-z1p` (Compresión gzip paralela para optimizar el uso de CPU multinúcleo).
  * **Verificación:** Omisión del chequeo del sistema de archivos origen para agilizar el proceso (`-scs`).
  * **Cifrado:** Sin cifrado (`-senc`) para facilitar el acceso en el entorno de pruebas.

### 3. Configuración y Restauración P2V
Se despliega la nueva máquina virtual (Destino) sin sistema operativo nativo, acoplándole el disco de respaldo (VDI) que contiene la imagen y la ISO de Clonezilla:
* **Modo de Restauración:** `device-image` $\rightarrow$ `local_dev` $\rightarrow$ `restoredisk`.
* **Tabla de Particiones:** Se utiliza el parámetro `-k0` para instruir a Clonezilla a que utilice y respete la tabla de particiones original de la imagen, volcándola de forma exacta sobre el nuevo disco duro.

### 4. Verificación Post-Restauración y Optimización
Una vez completado el volcado y extraída la ISO de Clonezilla, se inicia la máquina destino:
* **Auditoría de Datos:** Se verifica que el entorno gráfico, los usuarios y los directorios de prueba (ej. carpeta compartida `Web` e interior `app.by.save`) mantienen su estado y permisos exactos.
* **Inyección de Controladores:** Para garantizar el rendimiento gráfico y la integración en VirtualBox, se compilan e instalan las dependencias del núcleo y las *Guest Additions*:
  ```bash
  sudo apt install -y build-essential dkms linux-headers-$(uname -r)
