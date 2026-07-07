# 🌐 Implementación de redes VLAN y enrutamiento inter-vlan

Este proyecto documenta el diseño, la configuración paso a paso y la verificación de una infraestructura de red corporativa segmentada lógicamente. Utilizando el simulador Cisco Packet Tracer, se realiza la creación de redes de área local virtuales (VLANs), la configuración de enlaces troncales (Trunking) y el despliegue de enrutamiento Inter-VLAN mediante la arquitectura de Router-on-a-Stick para permitir la comunicación segura y controlada entre los diferentes departamentos.

---

## 🚀 Objetivos del Proyecto
* Diseñar e interconectar una topología de red con un router perimetral, un switch de distribución y estaciones de trabajo finales.
* Configurar y segmentar la red local en múltiples dominios de difusión independientes utilizando VLANs.
* Implementar enlaces troncales bajo el estándar IEEE 802.1Q para el transporte multi-VLAN en un solo canal físico.
* Configurar subinterfaces en el router perimetral para habilitar el enrutamiento Inter-VLAN (Router-on-a-Stick).
* Auditar y verificar la conectividad de extremo a extremo mediante pruebas de ping y comandos de diagnóstico de Cisco IOS.

---

## 🛠️ Herramientas y Topología Utilizadas
* **Software de Simulación:** Cisco Packet Tracer.
* **Dispositivo de Enrutamiento:** Router Cisco 2911.
* **Dispositivo de Conmutación:** Switch Cisco Catalyst 2960-24TT.
* **Estaciones de Trabajo (LAN):** * `PC0` y `PC1` (Asignados al Departamento de Administración).
  * `PC2` y `PC3` (Asignados al Departamento de Ventas).

---

## 📋 Puntos Clave e Implementación

### 1. Creación y Asignación de VLANs en el Switch
Se procede a dividir el dominio de broadcast único del switch creando dos redes lógicas independientes para aislar el tráfico de los departamentos:
* **VLAN 10 (Administración):** Se configuran y asignan en modo de acceso los puertos del rango `FastEthernet 0/1` al `FastEthernet 0/10`.
* **VLAN 20 (Ventas):** Se configuran y asignan en modo de acceso los puertos del rango `FastEthernet 0/11` al `FastEthernet 0/20`.

### 2. Configuración del Enlace Troncal (Trunking)
Para permitir que las tramas etiquetadas de ambas VLANs viajen a través del único cable físico que conecta el switch con el router, se deshabilita el modo de acceso en el puerto de interconexión y se eleva a modo troncal:
* **Interfaz de Enlace:** Puerto `GigabitEthernet 0/1` del switch configurado explícitamente en modo troncal (`switchport mode trunk`). Esto habilita el encapsulado y etiquetado nativo bajo el estándar **IEEE 802.1Q**.

### 3. Configuración de Enrutamiento Inter-VLAN (Router-on-a-Stick)
Debido a que las VLANs aíslan el tráfico por completo, se requiere un dispositivo de capa 3 para intercomunicarlas. En lugar de usar un cable físico por cada VLAN, se dividida de forma lógica la interfaz física del router (`GigabitEthernet 0/0`) en subinterfaces virtuales:
* **Subinterface GigabitEthernet 0/0.10 (VLAN 10):** Se activa el encapsulado correspondiente (`encapsulation dot1Q 10`) y se le asigna la puerta de enlace predeterminada `192.168.1.1 255.255.255.0`.
* **Subinterface GigabitEthernet 0/0.20 (VLAN 20):** Se activa el encapsulado correspondiente (`encapsulation dot1Q 20`) y se le asigna la puerta de enlace predeterminada `192.168.2.1 255.255.255.0`.
* **Activación Física:** Se inicializa y enciende la interfaz física principal (`interface GigabitEthernet 0/0` -> `no shutdown`) para levantar todas las subinterfaces virtuales de forma simultánea.

---

## 🔍 Verificación y Auditoría del Entorno

Para certificar el correcto funcionamiento de la segmentación y el enrutamiento, se ejecutan comandos de diagnóstico avanzados en las consolas de los dispositivos:

### 🔹 1. Inspección de VLANs (`show vlan brief`)
Al ejecutar la consulta en el switch, se constata que las VLANs 10 y 20 se encuentran en estado activo (`active`) y tienen mapeados correctamente sus respectivos rangos de interfaces de acceso físicos, asegurando que ningún departamento pueda interceptar el tráfico local del otro.

### 🔹 2. Validación de Enlaces Troncales (`show interfaces trunk`)
Se audita el estado del puerto de distribución del switch, verificando que el modo está establecido en `trunk`, con encapsulación `802.1q` y permitiendo explícitamente el paso de los IDs de VLAN 10 y 20 hacia el router.

### 🔹 3. Comprobación de Interfaces de Enrutamiento (`show ip interface brief`)
En el router, se comprueba que tanto la interfaz física principal como las subinterfaces virtuales lógicas (`Gi0/0.10` y `Gi0/0.20`) se encuentran en estado operativo físico y de protocolo `Up/Up`, mostrando sus respectivas direcciones IP de gateway asignadas de forma correcta.

### 🔹 4. Pruebas de Conectividad de Extremo a Extremo (`ping`)
Se realizan pruebas ICMP cruzadas desde la línea de comandos (`cmd`) de los hosts:
* Los pings locales entre equipos de la misma VLAN (ej. de `PC0` a `PC1`) son exitosos de forma directa.
* Los pings inter-departamentales (ej. de `PC0` en VLAN 10 a `PC2` en VLAN 20) completan de forma exitosa a través del proceso de enrutamiento del router, validando la correcta operación de la arquitectura Router-on-a-Stick.
