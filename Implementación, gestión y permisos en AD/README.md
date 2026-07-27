# 🛡️ Implementación, Gestión de Usuarios y Permisos en Active Directory (AD DS)

Este proyecto documenta el procedimiento práctico y la auditoría paso a paso para la incorporación de estaciones de trabajo clientes a un dominio corporativo administrado por **Windows Server**, la estructuración de Unidades Organizativas (UO), la creación de grupos de seguridad, la gestión fina de permisos NTFS sobre recursos compartidos y la delegación de autoridad administrativa descentralizada.

---

## 🚀 Objetivos del Proyecto
* Configurar los parámetros de red TCP/IPv4 en clientes Windows asignando la IP del servidor como DNS preferido.
* Unir estaciones de trabajo al dominio corporativo (`TechSolutions.com`) y verificar su registro en el contenedor de equipos del controlador de dominio.
* Diseñar e implementar una estructura de Unidades Organizativas jerárquica (`UO_Departamentos` / `UO_Diseño`).
* Aprovisionar cuentas de usuario y grupos de seguridad globales orientados a proyectos específicos (`GS_ProyectoAlfa_Diseño` y `GS_ProyectoAlfa_Desarrollo`).
* Publicar carpetas compartidas (`C:\ProyectoAlfa`) aplicando el principio de mínimo privilegio mediante el solapamiento de permisos de red y permisos NTFS explícitos.
* Delegar la administración de cuentas de usuario sobre una UO específica a un perfil no administrativo.
* Auditar la asignación de permisos e identificadores de seguridad mediante herramientas de línea de comandos (`dsacls` e `icacls`).

---

## 🛠️ Herramientas y Entorno del Sistema
* **Servidor Controlador de Dominio:** Windows Server 2019 / 2016 (AD DS y DNS activo).
* **Cliente de Red:** Estación de trabajo Windows.
* **Consolas de Administración:**
  * Usuarios y equipos de Active Directory (`dsa.msc`).
  * Propiedades del sistema (`sysdm.cpl`).
  * Símbolo del sistema (`cmd`) con utilidades `dsacls` e `icacls`.

---

## 📋 Puntos Clave e Implementación

### 1. Incorporación de Estación Cliente al Dominio 🖥️
* **Configuración TCP/IPv4:** Asignación del servidor DNS preferido apuntando a la IP del controlador de dominio (`192.168.10.4`) y verificación de conectividad ICMP previa.
* **Unión Lógica:** Ejecución de `sysdm.cpl`, cambio del nombre de equipo a `PC-Diseño01` y vinculación al dominio `TechSolutions.com` mediante credenciales de administrador.
* **Verificación:** Confirmación de la correcta sincronización del objeto en el contenedor `Computers` a través de `dsa.msc` en el servidor.

### 2. Creación de UOs, Cuentas y Grupos de Seguridad 👥
* **Estructura Organizativa:** Creación de la UO principal `UO_Departamentos` y la sub-UO `UO_Diseño` protegidas contra eliminación accidental.
* **Usuarios de Prueba:** Aprovisionamiento de cuentas (`Alex Desarrollo` e `Irene Diseño`) con directivas de cambio de contraseña.
* **Grupos Globables:** Creación de los grupos de seguridad con ámbito global `GS_ProyectoAlfa_Diseño` y `GS_ProyectoAlfa_Desarrollo`, asignando a cada usuario su grupo correspondiente.

### 3. Configuración de Recursos Compartidos y Permisos NTFS 📁
* **Recurso Físico:** Creación y publicación en red del directorio `C:\ProyectoAlfa` asignando permisos de recurso compartido con **Control Total** a `Todos`.
* **Hardening NTFS (Mínimo Privilegio):**
  * Deshabilitación de la herencia de permisos y conversión a permisos explícitos.
  * Eliminación de las entradas por defecto del grupo genérico *Usuarios*.
  * **Asignación a Desarrollo:** `GS_ProyectoAlfa_Desarrollo` con acceso exclusivo de **Lectura** (`R`).
  * **Asignación a Diseño:** `GS_ProyectoAlfa_Diseño` con acceso de **Lectura y Escritura** (`R,W`).

### 4. Delegación de Autoridad Administrativa 🔑
* Mediante el asistente de delegación de control sobre `UO_Diseño`, se otorgan permisos al usuario `Juan Pérez` para **crear, eliminar y administrar cuentas de usuario** exclusivamente dentro de dicho contenedor, evitando otorgarle privilegios globales en el dominio.

---

## 🔍 Verificación y Auditoría por Línea de Comandos

Para certificar la correcta aplicación de directivas y seguridad en el entorno, se ejecutan las siguientes comprobaciones de consola:

### 🔹 1. Auditoría de Delegación en UO (`dsacls`)
```cmd
dsacls "OU=UO_Diseño,OU=UO_Departamentos,DC=TechSolutions,DC=com"
Muestra la lista de control de acceso (ACL) de la UO en Active Directory, confirmando que juan.perez posee derechos delegados sobre objetos de tipo user.

🔹 2. Auditoría de Permisos NTFS (icacls)
DOS
icacls "C:\ProyectoAlfa"
Valida en el sistema de archivos local que GS_ProyectoAlfa_Desarrollo dispone de flag (OI)(CI)(R) y GS_ProyectoAlfa_Diseño cuenta con (OI)(CI)(R,W).
