# 🌐 Configuración, Optimización y Análisis Comparativo de Redes (RIPv1 vs. RIPv2)

Este proyecto documenta el diseño, direccionamiento e implementación de una topología de red distribuida que interconecta una Oficina Principal con dos Sucursales remotas utilizando enlaces WAN seriales. A través de la simulación en Cisco Packet Tracer, se analiza el comportamiento práctico, los límites de compatibilidad y el rendimiento de los protocolos de enrutamiento dinámico **RIPv1** y **RIPv2**.

---

## 🚀 Objetivos del Proyecto
* Diseñar e interconectar una topología multi-sitio (Oficina Central y dos Sucursales) utilizando interfaces WAN Seriales con módulos `HWIC-2T`.
* Implementar direccionamiento IP estático e interfaces virtuales de gestión.
* Configurar, comparar y contrastar el enrutamiento dinámico con clase (Classful) y sin clase (Classless).
* Aplicar técnicas de optimización de ancho de banda como el uso de interfaces pasivas (`passive-interface`).
* Identificar y analizar las limitaciones lógicas de simulación y la incompatibilidad de protocolos en entornos heredados.

---

## 📐 Especificaciones de la Topología de Red

El direccionamiento lógico e interfaces empleadas para la infraestructura se estructuran de la siguiente manera:

* **Router A (Oficina Principal):**
  * Interfaz LAN: `FastEthernet0/0` (Red LAN A: `192.168.1.0/24`)
  * Interfaz WAN a Router B: `Serial0/0/0` (Enlace WAN A-B: `10.0.0.0/30`, IP: `10.0.0.1`)
* **Router B (Sucursal 1):**
  * Interfaz LAN: `FastEthernet0/0` (Red LAN B: `192.168.2.0/24`)
  * Interfaz WAN a Router A: `Serial0/0/0` (Enlace WAN A-B: `10.0.0.0/30`, IP: `10.0.0.2`)
  * Interfaz WAN a Router C: `Serial0/0/1` (Enlace WAN B-C: `10.0.0.4/30`, IP: `10.0.0.5`)
* **Router C (Sucursal 2):**
  * Interfaz LAN: `FastEthernet0/0` (Red LAN C: `192.168.3.0/24`)
  * Interfaz WAN a Router B: `Serial0/0/0` (Enlace WAN B-C: `10.0.0.4/30`, IP: `10.0.0.6`)

---

## 📋 Puntos Clave e Implementación

### 1. Configuración Básica de Interfaces 🛠️
Se realiza el aprovisionamiento de nombres de host, contraseñas de seguridad, asignación de direccionamiento IP y máscaras de subred específicas, además del levantamiento físico de las interfaces (`no shutdown`) en todos los routers de la topología.

### 2. Despliegue de Enrutamiento Dinámico Gradual
* **Segmento RIPv1 (Routers A y B):** Se habilita el proceso de enrutamiento RIP en su versión 1 en la Oficina Principal y se enlazan las redes directamente conectadas (`192.168.1.0` y `10.0.0.0`) para habilitar la publicidad de rutas.
* **Segmento RIPv2 (Routers B y C):** Se migra y configura el protocolo RIP en su versión 2 en las sucursales remotas. Se añade el comando esencial `no auto-summary` para deshabilitar la sumarización automática en las fronteras de red, permitiendo el soporte nativo de enrutamiento sin clase (CIDR/VLSM).

### 3. Optimización de Tráfico LAN (`passive-interface`)
Con el objetivo de preservar el ancho de banda y mitigar riesgos de seguridad, se configuran las interfaces `GigabitEthernet0/0` (o correspondientes de la LAN) de las sucursales como **interfaces pasivas**. Esto evita el envío innecesario de ráfagas periódicas de actualización de enrutamiento RIP hacia los switches y host de la red local, manteniendo la escucha de rutas activa.

### 4. Filtrado de Rutas Avanzado (Caso de Estudio Técnico)
Se plantea la necesidad de mitigar la publicidad de segmentos LAN privados mediante listas de control de acceso combinadas con políticas de distribución:
```cisco
access-list 10 deny 192.168.3.0 0.0.0.255
access-list 10 permit any
router rip
 distribute-list 10 out Serial0/0/0
