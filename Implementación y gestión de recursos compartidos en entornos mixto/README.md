# 📁 Implementación y Gestión de Recursos Compartidos en Entorno Mixto (Windows Server, SAMBA y NFS)

Este proyecto documenta el despliegue, la configuración y la auditoría de un entorno de red heterogéneo que integra clientes **Windows** y **Linux (Ubuntu)** interactuando con servidores de archivos e impresión. El laboratorio aborda la gestión de usuarios y grupos locales, el control de acceso fino mediante permisos compartidos y permisos NTFS/Linux, el intercambio de archivos mediante protocolos **SMB/CIFS** y **NFS**, la centralización de servicios de impresión y el análisis de directivas de seguridad corporativas.

---

## 🚀 Objetivos del Proyecto
* Aprovisionar el rol de Servidor de Archivos en Windows Server para el protocolo SMB/CIFS.
* Estructurar el control de accesos definiendo usuarios (`Laura`, `Ana`, `Pedro`, `Sofia`, `Carlos`) y grupos de trabajo (`Administracion`, `Diseño`).
* Configurar recursos compartidos en red (`AdminDocs` y `Proyectos`) aplicando el principio de mínimo privilegio mediante el solapamiento de permisos de red y permisos NTFS explícitos.
* Publicar y compartir un servicio de impresión centralizado (`Impresora_Principal`) para toda la organización.
* Mapear unidades de red en clientes Windows y validar la denegación/autorización de accesos según el perfil del usuario.
* Configurar clientes Linux (Ubuntu) con utilidades de SAMBA (`smbclient`, `cifs-utils`) y desplegar un servidor **NFS** para exportar recursos compartidos nativos entre estaciones Linux.
* Integrar la impresora compartida de Windows Server en clientes Linux empleando utilidades de impresión de red (`system-config-printer` y CUPS sobre SMB).

---

## 🛠️ Herramientas y Entorno del Sistema
* **Servidor Principal:** Windows Server (Servicios de iSCSI y archivo).
* **Clientes / Estaciones:** * Estaciones de trabajo Microsoft Windows.
  * Estaciones de trabajo Linux Ubuntu.
* **Servicios y Protocolos:** SMB/CIFS (Samba), NFS (Network File System), CUPS / Print Services.

---

## 📋 Puntos Clave e Implementación

### 1. Configuración del Servidor Windows y Permisos de Archivo 🪟
* **Inclusión de Roles:** Instalación del servicio de rol *Servicios de iSCSI y archivo* desde el Administrador del Servidor.
* **Gestión de Usuarios y Grupos:** Creación de grupos (`Administracion`, `Diseño`) y usuarios a través de la consola de administración (`compmgmt.msc`). Asignación explícita de miembros a cada departamento.
* **Publicación de Recursos:** Creación y publicación en red de las carpetas `C:\Proyectos` y `C:\AdminDocs`.
* **Seguridad NTFS y Permisos de Red:**
  * Se rompe la herencia de permisos de la carpeta madre (`C:\`) convirtiendo los permisos en explícitos.
  * **Carpeta `Proyectos`:** El grupo *Diseño* dispone de **Control Total**, mientras que el grupo *Administracion* solo cuenta con permisos de **Lectura y Ejecución**.
  * **Carpeta `AdminDocs`:** Se restringe exclusivamente al grupo *Administracion* con **Control Total**, denegando cualquier acceso a usuarios ajenos al área.
* **Servidor de Impresión:** Instalación de la `Impresora_Principal` mediante un controlador genérico de texto (`Generic / Text Only`) y compartición en red para el grupo genérico *Todos*.

### 2. Mapeo y Pruebas en Estaciones Windows 💻
* **Prueba de Perfil Diseño (`Laura`):** Se conecta la unidad de red `P:` hacia `\\WIN-SERVER\Proyectos`. Se valida la creación de contenido (`Carpeta_Laura`). Posteriormente se intenta el acceso a `\\WIN-SERVER\AdminDocs`, devolviendo una denegación explícita de acceso por falta de permisos NTFS.
* **Prueba de Perfil Administración (`Ana`):** Se mapea la unidad `A:` hacia `\\WIN-SERVER\AdminDocs`. Se valida la creación de archivos de prueba. Al acceder a `Proyectos`, se comprueba que el usuario solo puede consultar el contenido pero no crear ni modificar archivos.

### 3. Integración de Estaciones Linux (SAMBA y NFS) 🐧
* **Acceso SMB desde Terminal:** Instalación de `smbclient` e interacción del usuario `Pedro` con los recursos compartidos de Windows Server:
  * Validación exitosa de creación de directorios en `//SERVER/Proyectos`.
  * Bloqueo y respuesta `NT_STATUS_ACCESS_DENIED` al intentar listar `//SERVER/AdminDocs`.
* **Despliegue del Servidor NFS:**
  * Instalación de `nfs-kernel-server` y creación del directorio `/home/sofia/Recursos_Linux` con propietarios `sofia:diseno` y permisos POSIX estrictos `770`.
  * Exportación en `/etc/exports` para habilitar lectura/escritura síncrona.
  * Montaje remoto en el cliente (`/mnt/Carpeta_Sofia`) y verificación de creación de archivos (`prueba.txt`) por parte del usuario `Pedro`.
* **Impresión Mixta (Linux a Windows Server):**
  * Configuración de la cola de impresión mediante `system-config-printer`.
  * Vinculación mediante la URI `smb://192.168.100.5/Impresora_Principal` empleando credenciales de usuario del servidor y validando el estado activo del servicio.

---

## 🔍 Consideraciones de Seguridad y Buenas Prácticas

El proyecto concluye con un análisis sobre el robustecimiento de infraestructuras de almacenamiento e intercambio de datos:
1. **Autenticación y Autorización Robusta:** Implantación de políticas de complejidad de contraseñas mediante Directivas de Grupo (GPO) y la recomendación de usar Doble Factor de Autenticación (MFA) para mitigar el robo de credenciales.
2. **Cifrado de Datos en Tránsito:** Activación obligatoria de **SMB 3.0** en el servidor para forzar el cifrado de las comunicaciones de red y evitar la captura de tráfico sensible (*Eavesdropping/Man-in-the-Middle*).
3. **Estrategia de Respaldos (Backup 3-2-1):** Planificación de copias de seguridad automatizadas para las carpetas críticas frente a catástrofes de hardware o ataques de *Ransomware*.
4. **Auditoría y Logs:** Activación de las directivas de *Auditoría de Acceso a Objetos* en Windows Server para mantener un registro trazable de modificaciones, eliminaciones e intentos fallidos de inicio de sesión.
