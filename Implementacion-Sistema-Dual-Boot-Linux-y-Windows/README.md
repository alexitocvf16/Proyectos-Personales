# 💻 Implementación de un Sistema Dual-Boot Linux y Windows para Entorno Corporativo

Este proyecto documenta el procedimiento técnico y la auditoría paso a paso para desplegar un entorno de arranque dual (**Dual-Boot**) en una misma estación de trabajo, integrando los sistemas operativos **Windows 11 Pro** y **Ubuntu Desktop**. El laboratorio aborda la gestión avanzada de almacenamiento, la preparación del firmware, el particionamiento manual de discos y la configuración del gestor de arranque GRUB en un entorno controlado mediante virtualización.

---

## 🚀 Objetivos del Proyecto
* Configurar una máquina virtual en Oracle VirtualBox simulando las especificaciones de hardware de un equipo corporativo moderno.
* Preparar el firmware del sistema habilitando el modo **EFI/UEFI** como estándar de arranque actual.
* Instalar y aprovisionar Windows 11 Pro gestionando la tabla de particiones inicial.
* Realizar un redimensionamiento y reducción lógica del volumen principal de Windows para liberar espacio no asignado.
* Instalar Ubuntu Desktop implementando un particionamiento manual personalizado para la coexistencia de ambos sistemas.
* Auditar el correcto funcionamiento del gestor de arranque **GRUB** y verificar el acceso transparente a ambos entornos de escritorio.

---

## 🛠️ Herramientas y Requerimientos del Sistema
* **Hipervisor:** Oracle VirtualBox.
* **Firmware:** UEFI (Unified Extensible Firmware Interface) habilitado.
* **Medios de Instalación:** * Imagen ISO oficial de Windows 11 Pro.
  * Imagen ISO oficial de Ubuntu Desktop.
* **Almacenamiento:** Un único disco duro virtual NVMe/SATA configurado para albergar ambos sistemas operativos.

---

## 📋 Puntos Clave e Implementación

### 1. Preparación del Entorno Lógico y Despliegue de Windows 11 Pro 🪟
* **Aprovisionamiento UEFI:** Se inicializa la máquina virtual asegurando que la opción de EFI esté activa en la configuración de la placa base virtual, un requisito indispensable para Windows 11 y sistemas de arranque modernos.
* **Instalación Limpia:** Se procede con la instalación estándar de Windows 11 Pro, asignando inicialmente la totalidad del espacio disponible en el disco duro para crear el volumen principal basado en la tabla de particiones GPT.

### 2. Segmentación del Almacenamiento desde Windows
* **Reducción del Volumen:** Una vez operativo el entorno de Windows, se accede a la herramienta nativa **Administración de discos**.
* **Espacio Libre:** Se realiza una reducción lógica de la partición principal de Windows (`C:`) para liberar la mitad del almacenamiento total del disco, dejándolo como espacio en crudo, "No asignado", para el posterior despliegue de Linux.

### 3. Instalación y Particionamiento Manual de Linux Ubuntu 🐧
Se monta la ISO de Ubuntu y se inicia la estación de trabajo desde el instalador. Para evitar que Linux sobrescriba a Windows, se selecciona la opción de **"Más opciones" (Particionado Avanzado)** para estructurar el espacio no asignado manualmente:
* **Partición `/` (Raíz):** Configurada con el sistema de archivos transaccional `ext4`, destinada a albergar el sistema operativo y los binarios.
* **Partición `/home`:** Configurada de forma independiente en `ext4` para salvaguardar los datos, perfiles y carpetas personales de los usuarios ante futuras reinstalaciones de la raíz.
* **Área de Intercambio (`swap`):** Espacio de paginación lógica para dar soporte a la memoria RAM del sistema.
* **Respeto a la Partición EFI:** Se asegura la vinculación del cargador de arranque de Linux en la partición EFI ya existente creada por Windows (`/dev/sda1` o correspondiente), evitando duplicidades innecesarias.

---

## 🔍 Verificación y Auditoría del Sistema Dual

Para certificar el éxito de la coexistencia de ambos sistemas operativos, se realizan las siguientes comprobaciones técnicas:

### 🔹 1. Auditoría del Gestor de Arranque (GRUB)
Al encender o reiniciar la estación de trabajo, el firmware del equipo cede el control al menú de **GRUB** (Grand Unified Bootloader). Se verifica visualmente que el menú liste de forma correcta las siguientes opciones de inicio:
* `Ubuntu`: Opción por defecto para iniciar el entorno Linux.
* `Windows Boot Manager`: Enlace directo para desviar la carga hacia el cargador nativo de Windows.

### 🔹 2. Validación de Entornos Operativos
* **Verificación de Windows:** Al seleccionar *Windows Boot Manager*, el sistema carga de forma limpia el sistema operativo original, manteniendo intactas todas sus funciones y la partición comprimida.
* **Verificación de Ubuntu:** Al reiniciar y seleccionar *Ubuntu*, el sistema operativo arranca con normalidad, permitiendo el inicio de sesión y la plena operatividad del entorno gráfico de Linux, consolidando con éxito un entorno de trabajo multiplataforma en un único hardware.
