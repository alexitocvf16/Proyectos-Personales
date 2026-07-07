# 🌐 Implementación de Acceso a Internet y Servicios Internos mediante NAT y PAT

Este proyecto documenta el diseño, configuración y auditoría de un escenario de red corporativa enfocado en la escasez de direccionamiento IPv4 y la seguridad perimetral. A través del simulador Cisco Packet Tracer, se implementa la traducción de direcciones de red dinámicas (NAT con sobrecarga/PAT) para permitir el acceso a internet de la LAN y se configura la redirección de puertos (Port Forwarding) para publicar un servidor web interno de forma controlada.

---

## 🚀 Objetivos del Proyecto
* Configurar e interconectar los direccionamientos lógicos en interfaces WAN públicas y LAN privadas.
* Implementar NAT de sobrecarga (PAT) para unificar la salida a internet de toda una subred bajo una única dirección IP pública.
* Configurar NAT Estático (Port Forwarding) para exponer servicios HTTP internos hacia la red exterior sin comprometer la seguridad de la LAN.
* Auditar en tiempo real las tablas de traducción de direcciones mediante comandos de diagnóstico en Cisco IOS.

---

## 🛠️ Herramientas y Topología Utilizadas
* **Software de Simulación:** Cisco Packet Tracer.
* **Dispositivo de Borde:** Router Cisco 2911.
* **Infraestructura LAN (Privada):** Switch de acceso, una estación de trabajo (`PC0`) y un servidor web de producción interno (`Server0`).
* **Infraestructura WAN (Internet pública simulada):** Switch de tránsito, un cliente web externo (`PC1`) y un servidor remoto genérico (`Server1`).

---

## 📋 Puntos Clave e Implementación

### 1. Aprovisionamiento y Direccionamiento de Interfaces
Se configuran las direcciones lógicas y máscaras de subred correspondientes, asegurando el correcto levantamiento de los enlaces físicos:
* **Interfaz de Red Local (LAN):** `GigabitEthernet0/0` configurada con la puerta de enlace interna `192.168.1.1/24`.
* **Interfaz de Red Externa (WAN):** `GigabitEthernet0/1` configurada con la dirección IP pública asignada por el proveedor `203.0.113.1/24`.

### 2. Implementación de PAT (NAT Overload) para Salida a Internet
Para permitir que todos los host del rango privado `192.168.1.0/24` naveguen hacia el exterior empleando únicamente la IP de la interfaz pública, se aplican los siguientes bloques de configuración:
* **Delimitación de Dominios:** Se etiqueta la interfaz de la LAN como el origen interno (`ip nat inside`) y la interfaz WAN hacia internet como el extremo público (`ip nat outside`).
* **Definición de Tráfico Calificado:** Se genera una lista de control de acceso estándar (`access-list 1 permit 192.168.1.0 0.0.0.255`) para especificar qué subred tiene permitido ser traducida.
* **Activación de Sobrecarga:** Se vincula la ACL con la interfaz externa utilizando el parámetro de reutilización de puertos por sockets (`ip nat inside source list 1 interface GigabitEthernet0/1 overload`).

### 3. Redirección de Puertos (Port Forwarding) para el Servidor Web
Para dar acceso desde internet al servidor web privado (`192.168.1.100`) de forma segura, se mapea un canal de entrada estático en el puerto estándar HTTP (puerto 80):
```cisco
ip nat inside source static tcp 192.168.1.100 80 203.0.113.1 80
```

Esta directiva instruye de forma estricta al router perimetral para interceptar cualquier petición dirigida a su IP pública por el puerto 80 y reenviarla de inmediato al servidor correspondiente dentro de la red local.

🔍 Verificación y Auditoría del Entorno
Para garantizar el correcto funcionamiento del despliegue se realizan pruebas exhaustivas de tráfico de extremo a extremo:

🔹 1. Validación de Salida LAN a Internet
Desde el navegador web del host interno (192.168.1.10), se realiza una petición exitosa a la URL pública del servidor de internet http://203.0.113.20, confirmando que el enmascaramiento dinámico (PAT) opera de forma transparente para los usuarios.

🔹 2. Validación de Acceso Externo (Port Forwarding)
Desde el cliente exterior ubicado en la WAN (203.0.113.10), se ingresa la IP pública del router (http://203.0.113.1). El router traduce la trama e interactúa directamente con el servidor interno, devolviendo la página web con el mensaje de confirmación de éxito.

🔹 3. Inspección Analítica de la Tabla de Traducciones (show ip route / translations)
Al ejecutar el comando show ip nat translations en el prompt del Router, se analizan los registros lógicos activos:

Traducción Dinámica (PAT): Se evidencia cómo el flujo originado en 192.168.1.10:1025 se enmascara hacia el exterior con la dirección pública 203.0.113.1 empleando el puerto mapeado 1025 para diferenciar la sesión TCP del resto.

Traducción Estática (Mapeo Fijo): Se comprueba el registro persistente que vincula permanentemente el puerto 80 público con el puerto 80 interno del servidor 192.168.1.100.

Tráfico Concurrente: Se constatan las conexiones entrantes desde el cliente externo 203.0.113.10 interactuando perfectamente a través de sockets dinámicos (puertos 1026, 1027, etc.) hacia el servidor web privado.
