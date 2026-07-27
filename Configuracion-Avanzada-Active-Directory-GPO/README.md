# 🏢 Configuración Avanzada de Active Directory Domain Services (AD DS) y Directivas de Grupo (GPO)

Este proyecto documenta el despliegue técnico, la estructuración jerárquica y el robustecimiento de la seguridad en un dominio corporativo basado en **Windows Server**. El laboratorio aborda la instalación del rol de **Servicios de dominio de Active Directory (AD DS)**, el diseño de Unidades Organizativas (UO), la gestión y anidamiento de grupos de seguridad, la implementación de políticas de contraseñas y bloqueo de cuentas mediante **GPOs**, la automatización con scripts de inicio de sesión, y la delegación administrativa descentralizada.

---

## 🚀 Objetivos del Proyecto
* Promover un servidor de Windows Server a Controlador de Dominio principal creando un nuevo bosque (`TechSolutions.com`).
* Diseñar e implementar una estructura jerárquica de Unidades Organizativas (UO) representativas de la empresa (`Marketing`, `Ventas`, `IT`, `Recursos_Humanos`, `Usuarios_Corporativos`).
* Aprovisionar cuentas de usuario y cuentas de equipo de prueba para cada área de la organización.
* Aplicar una estrategia eficiente de anidamiento de grupos integrando grupos de seguridad globales en un grupo de seguridad universal (`GS_Empleados_Global`).
* Desplegar directivas de seguridad para forzar la complejidad, longitud e historial de contraseñas, así como el bloqueo automático de cuentas por intentos fallidos.
* Configurar perfiles móviles de usuario y carpetas personales vinculadas a recursos compartidos en red (`\\TechSolutions\Perfiles` y `\\TechSolutions\CarpetasPersonales`).
* Restringir el inicio de sesión de usuarios por equipo de trabajo específico y por franja horaria laboral.
* Automatizar el mapeo de unidades de red mediante la ejecución de scripts `.bat` distribuidos por directivas de grupo (GPO).
* Aplicar el principio de mínimo privilegio mediante la delegación del control administrativo sobre una UO a un usuario no administrador del dominio.

---

## 🛠️ Herramientas y Entorno del Sistema
* **Sistema Operativo:** Windows Server 2019 / 2016.
* **Servicios de Red:** Active Directory Domain Services (AD DS) y Servidor DNS integrado.
* **Consolas de Administración:**
  * Usuarios y equipos de Active Directory (`dsa.msc`).
  * Administración de directivas de grupo (`gpmc.msc`).
  * Línea de comandos (CMD) y Windows PowerShell.

---

## 📋 Puntos Clave e Implementación

### 1. Instalación de AD DS y Estructura de Unidades Organizativas (UO) 🌲
* **Promoción del Dominio:** Instalación del rol *Servicios de dominio de Active Directory* e inicialización del bosque `TechSolutions.com`.
* **Creación de UOs:** Configuración de la estructura corporativa dividida en departamentos (`Marketing`, `Ventas`, `IT`, `Recursos Humanos`, `Usuarios Corporativos`) activando la protección contra eliminación accidental.

### 2. Gestión de Cuentas y Anidamiento de Grupos de Seguridad 👥
* **Poblado de Objetos:** Aprovisionamiento de usuarios (ej. `usuario.marketing`) y cuentas de equipo (ej. `PC-Marketing01`) dentro de sus UOs correspondientes.
* **Grupos Globales y Universales:** Creación de un grupo de seguridad global por departamento (ej. `GS_Marketing`) y un grupo universal (`GS_Empleados_Global`).
* **Estrategia de Anidamiento:** Inclusión de los grupos globales dentro del grupo universal para simplificar la asignación de permisos globales a nivel de infraestructura.

### 3. Directivas de Grupo (GPO) y Hardening de Cuentas 🔒
Vinculación de la directiva `GPO_Contraseñas_Politica` en la UO *Usuarios Corporativos* configurando:
* **Política de Contraseñas:** Longitud mínima de **10 caracteres**, requerimiento de complejidad habilitado e historial de **5 contraseñas recordadas**.
* **Política de Bloqueo de Cuentas:** Umbral de bloqueo tras **3 intentos fallidos** con una duración de **15 minutos**.
* **Verificación Práctica:** Confirmación de denegación al intentar establecer contraseñas débiles (`123`) y verificación del estado "Bloqueado" de cuentas tras simulaciones de inicio de sesión mediante el comando `runas`.

### 4. Perfiles Móviles, Carpetas Personales y Restricciones de Acceso 🗂️
* **Almacenamiento Centralizado:** Creación del directorio `C:\Recursos` albergando los recursos compartidos `Perfiles` y `CarpetasPersonales` con permisos totales para el grupo *Todos*.
* **Perfiles Móviles (`usuario.ventas`):** Asignación de la ruta de perfil `\\techsolutions\Perfiles\%username%` para sincronizar el entorno de trabajo del usuario.
* **Carpetas Personales (`usuario.marketing`):** Conexión automática de la unidad de red `Z:` hacia `\\techsolutions\CarpetasPersonales\%username%`.
* **Restricciones de Equipo (`usuario.it`):** Limitación de inicio de sesión exclusiva a las estaciones `PC-IT01` y `PC-IT02`.
* **Restricciones Horarias (`usuario.rrhh`):** Configuración del mapa de horario permitiendo el inicio de sesión únicamente de **lunes a viernes de 8:00 a 17:00 horas**.

### 5. Automatización con Scripts de Logón y Delegación Administrativa 📜
* **Script de Mapeo Automatizado:** Creación de un archivo ejecutable `.bat` que limpia y mapea la unidad `Z:` hacia `\\WIN-SERVER\Datos_Compartidos_Empleados`.
* **Distribución vía GPO:** Configuración de `GPO_Unidad_Compartida` en el apartado de scripts de inicio de sesión de usuario y filtrado de seguridad asignado explícitamente al grupo `GS_Empleados_Global`.
* **Delegación de Control:** Asignación de permisos al usuario `AdminMarketing` sobre la UO *Marketing* para crear, eliminar y gestionar cuentas de usuario y grupos de forma descentralizada sin otorgar privilegios de administrador de dominio.
