# 🛡️ Configuración Inicial, Segmentación de Red (VLANs) y Seguridad Básica en Switches Cisco

Este proyecto documenta el despliegue práctico y la auditoría de seguridad física y lógica en un conmutador de red corporativo (Switch Cisco Catalyst 2960). A través de la simulación en Cisco Packet Tracer, se realizan tareas que van desde el aprovisionamiento básico de administración hasta la mitigación de vectores de ataque locales y la segmentación de tráfico por departamentos.

---

## 🚀 Objetivos del Proyecto
* Establecer un canal de administración local y remoto completamente seguro.
* Segmentar la infraestructura de red física en dominios de difusión lógicos independientes.
* Mitigar ataques de suplantación de identidad (MAC Spoofing) y accesos no autorizados a nivel de capa 2.
* Verificar y auditar de forma integral el estado de los servicios configurados.

---

## 🛠️ Herramientas Utilizadas
* **Software de Simulación:** Cisco Packet Tracer.
* **Dispositivo Central:** Switch Cisco Catalyst 2960-24TT.
* **Estación de Trabajo:** PC de administración con emulador de terminal enlazado mediante cable de consola (RS-232 a puerto Console).

---

## 📋 Puntos Clave del Proyecto

### 1. Inicialización y Gestión TCP/IP Básica
* **Identificación del Host:** Cambio del nombre por defecto del dispositivo a `Switch-Principal` para su correcta identificación en la topología.
* **Direccionamiento de Administración:** Configuración de la Interfaz Virtual de Switch (`interface Vlan1`) con una IP estática de gestión (`192.168.1.254/24`) y levantamiento de la interfaz (`no shutdown`).
* **Enrutamiento de Salida:** Asignación de la puerta de enlace predeterminada (`ip default-gateway 192.168.1.1`) para permitir la comunicación del switch con subredes externas.

### 2. Robustecimiento del Acceso y Seguridad de Consola (Hardening)
* **Protección de Privilegios:** Implementación de una contraseña cifrada mediante algoritmos robustos (`enable secret`) para denegar el acceso no autorizado al Modo de Configuración Global.
* **Acceso de Consola Físico:** Protección de la línea de consola (`line con 0`) mediante contraseña y requerimiento de autenticación local.
* **Creación de Cuentas Administrativas:** Configuración de un usuario local personalizado (`admin`) con el nivel máximo de privilegios en el sistema (`privilege 15`).

### 3. Habilitación de Acceso Remoto Seguro (SSH)
* **Cierre de Telnet:** Deshabilitación por completo del protocolo inseguro Telnet debido al envío de credenciales en texto plano por la red.
* **Criptografía RSA:** Definición del dominio local (`dominioempresa.com`) y generación de un par de claves criptográficas RSA asimétricas con un módulo seguro de **2048 bits**.
* **Líneas VTY:** Restricción estricta de los canales de acceso virtual remoto (`line vty 0 4`), forzando exclusivamente el transporte de entrada mediante SSH (`transport input ssh`) y la validación con la base de datos de usuarios local.
* **Bloqueo Preventivo:** Deshabilitación deliberada de las líneas virtuales remanentes (`line vty 5 15`) para reducir la superficie de ataque del conmutador.

### 4. Segmentación Lógica de la Red (VLANs)
Se divide el dominio de broadcast único del dispositivo para aislar el tráfico de red de diferentes departamentos de la organización:
* **VLAN 10 (Administración):** Asignación estricta del rango de puertos `FastEthernet 0/1` al `FastEthernet 0/10` bajo el modo de acceso.
* **VLAN 20 (Ventas):** Asignación estricta del rango de puertos `FastEthernet 0/11` al `FastEthernet 0/20` bajo el modo de acceso.

> **Ventajas Técnicas:** 
> * **Seguridad:** Aísla por completo los datos de cada departamento, conteniendo las infecciones de malware o malware de red en su subred de origen y evitando la interceptación de paquetes sin pasar por un firewall perimetral.
> * **Rendimiento:** Reduce las tormentas de broadcast y peticiones ARP, optimizando el ancho de banda global y liberando la carga de procesamiento en la CPU de los equipos.

### 5. Seguridad de Puertos (Port Security)
Como contramedida frente a intrusos físicos en la oficina, se aplica seguridad estricta en la interfaz de administración `FastEthernet 0/1`:
* **Límite de Direcciones:** Se restringe el acceso a un número máximo de **1 dirección MAC** en el puerto de manera simultánea.
* **Persistencia Dinámica (Sticky MAC):** Configuración del switch para que aprenda dinámicamente la dirección física del equipo del administrador conectado y la guarde de manera persistente ("pegajosa") en la tabla de asignaciones.
* **Modo de Violación (Shutdown):** Si se conecta un dispositivo con una MAC diferente, el puerto ejecuta una acción de penalización inmediata, **apagándose por completo de forma automática** (`violation shutdown`) para neutralizar la amenaza y enviando una alerta al sistema.

---

## 🔍 Verificación y Auditoría de Servicios

Para garantizar el correcto funcionamiento del despliegue se ejecutan y validan los siguientes comandos de diagnóstico en el sistema operativo Cisco IOS:
1. **`show ip interface brief`:** Confirmación del estado físico `Up/Up` de los puertos conectados y la asignación correcta de la IP en la interfaz Vlan1.
2. **`show vlan brief`:** Verificación visual de la creación de las VLANs 10 y 20, y la correcta distribución de los puertos de acceso asignados.
3. **`show ip ssh`:** Validación de la activación del protocolo SSH de forma segura en la versión de transporte 1.99.
4. **`show port-security interface [interfaz]`:** Comprobación del estado activo de las directivas de protección perimetral, la acción de apagado (`Shutdown`) y el contador de violaciones en cero.
5. **`show mac-address-table`:** Inspección en tiempo real del aprendizaje dinámico del switch de las direcciones físicas conectadas a los puertos activos (como `Fa0/21`).
