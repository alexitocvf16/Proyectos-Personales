# 💻 Implementación de un Sistema Dual-Boot Linux y Windows para Entorno Corporativo

Este proyecto documenta el procedimiento técnico y la auditoría paso a paso para desplegar un entorno de arranque dual (**Dual-Boot**) en una misma estación de trabajo física, integrando los sistemas operativos **Windows 11 Pro** y **Ubuntu Desktop** de forma nativa. El laboratorio aborda la gestión avanzada de almacenamiento real, la preparación del firmware del equipo, el particionamiento manual de discos y la configuración del gestor de arranque GRUB directamente sobre el hardware.

---

## 🚀 Objetivos del Proyecto
* Preparar el firmware de la placa base habilitando el modo **EFI/UEFI** como estándar de arranque actual y seguro.
* Instalar y aprovisionar Windows 11 Pro gestionando la tabla de particiones inicial en el almacenamiento local.
* Realizar un redimensionamiento y reducción lógica del volumen principal de Windows para liberar espacio no asignado en el disco duro.
* Instalar Ubuntu Desktop implementando un particionamiento manual personalizado para la coexistencia nativa de ambos sistemas.
* Auditar el correcto funcionamiento del gestor de arranque **GRUB** y verificar el acceso transparente a ambos entornos de escritorio en el equipo físico.

---

## 🛠️ Herramientas y Requerimientos del Sistema
* **Entorno de Despliegue:** Ordenador nativo (Hardware físico real).
* **Firmware:** UEFI (Unified Extensible Firmware Interface) nativo de la placa base.
* **Medios de Instalación:** * Unidad USB booteable con la ISO oficial de Windows 11 Pro.
  * Unidad USB booteable con la ISO oficial de Ubuntu Desktop.
* **Almacenamiento:** Un único disco duro físico (SSD/NVMe) destinado a albergar y segmentar ambos sistemas operativos.

---

## 📋 Puntos Clave e Implementación

### 1. Preparación del Entorno Lógico y Despliegue de Windows 11 Pro 🪟
* **Aprovisionamiento UEFI:** Se accede al menú de configuración de la BIOS/UEFI de la placa base para asegurar que el modo UEFI esté activo, garantizando la compatibilidad con las tablas de particiones GPT y los requisitos modernos de seguridad de Windows 11.
* **Instalación Limpia:** Se arranca el equipo desde el medio USB de Windows 11 Pro, asignando inicialmente el espacio necesario en el disco duro físico bajo la estructura GPT.

### 2. Segmentación del Almacenamiento desde Windows
* **Reducción del Volumen:** Una vez operativo el entorno de Windows, se accede a la herramienta nativa **Administración de discos** del sistema.
* **Espacio Libre:** Se realiza una reducción lógica de la partición principal de Windows (`C:`) para liberar la cantidad de almacenamiento deseada en el disco físico, dejándolo como espacio en crudo, "No asignado", para el posterior despliegue de Linux.

### 3. Instalación y Particionamiento Manual de Linux Ubuntu 🐧
Se conecta la unidad USB de Ubuntu y se inicia la estación de trabajo modificando el orden de arranque en la UEFI. Para evitar que Linux sobrescriba o borre la instalación existente de Windows, se selecciona la opción de **"Más opciones" (Particionado Avanzado)** para configurar el espacio libre manualmente:
* **Partición `/` (Raíz):** Configurada con el sistema de archivos transaccional `ext4`, destinada a albergar el sistema operativo y los archivos de raíz de Linux.
* **Partición `/home`:** Configurada de forma independiente en `ext4` para salvaguardar los datos, perfiles y carpetas personales de los usuarios de forma aislada ante futuras operaciones en la raíz.
* **Área de Intercambio (`swap`):** Espacio de paginación lógica para dar soporte a la memoria RAM física del sistema.
* **Respeto a la Partición EFI:** Se asocia el cargador de arranque de Linux a la partición EFI ya existente y creada previamente por Windows en el disco duro, permitiendo una convivencia limpia sin alterar los archivos de arranque originales.

---

## 🔍 Verificación y Auditoría del Sistema Dual

Para certificar el éxito de la coexistencia de ambos sistemas operativos en el ordenador nativo, se realizan las siguientes comprobaciones técnicas:

### 🔹 1. Auditoría del Gestor de Arranque (GRUB)
Al encender o reiniciar el equipo físico, el firmware UEFI cede el control al menú de **GRUB** (Grand Unified Bootloader) instalado en el disco. Se verifica visualmente que el menú liste de forma correcta las siguientes opciones de inicio:
* `Ubuntu`: Opción por defecto para iniciar el entorno Linux.
* `Windows Boot Manager`: Enlace directo para desviar la carga hacia el cargador nativo de Windows.

### 🔹 2. Validación de Entornos Operativos
* **Verificación de Windows:** Al seleccionar *Windows Boot Manager*, el hardware carga de forma limpia el sistema operativo Microsoft original, manteniendo intactas todas sus funciones y la integridad de su partición.
* **Verificación de Ubuntu:** Al reiniciar y seleccionar *Ubuntu*, el sistema operativo arranca con normalidad, permitiendo el inicio de sesión y la plena operatividad del entorno gráfico de Linux, consolidando con éxito un entorno de trabajo multiplataforma en un único hardware físico.
