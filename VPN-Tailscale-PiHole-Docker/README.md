# 🌐 Red Privada Virtual y DNS Centralizado: Tailscale + Pi-hole

Arquitectura avanzada mediante Docker en patrón *Sidecar* que integra Tailscale y Pi-hole. Permite acceso remoto seguro a la red doméstica, enrutamiento completo (Exit Node) y bloqueo de publicidad en conexiones exteriores.

## 🚀 Preparación del Host (Sysctl)
Para habilitar el enrutamiento de red y permitir que la Raspberry Pi opere como nodo de salida, se modifica el núcleo de Linux activando el *IP forwarding* para IPv4 e IPv6.
`echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf`.

## 🛠️ Arquitectura Sidecar (Docker Compose)
Se despliega un *Stack* único en Portainer:
* **Tailscale:** Utiliza `network_mode: host` para gestionar las rutas físicas del sistema y habilitar atributos `NET_ADMIN`.
* **Pi-hole:** Se conecta directamente a la red del contenedor VPN usando `network_mode: "service:tailscale"`.
* **Integración Apple:** Se añade la variable `FTLCONF_dns_specialDomains_iCloudPrivateRelay=true` en Pi-hole para evitar conflictos con el Private Relay de iOS.
* Las credenciales críticas (`TS_AUTHKEY` y `PIHOLE_PASSWORD`) se inyectan como variables de entorno dinámicas. La clave de autorización de Tailscale se genera como "Reusable" desde el portal administrativo de Tailscale.

## ⚙️ Configuración Administrativa
Desde la consola web de Tailscale (`login.tailscale.com`):
1. **Aprobación de Rutas:** Se habilitan las *Subnet routes* (`192.168.x.x/24`) y se marca el equipo como *Exit node*.
2. **DNS Global:** Se registra la IP interna de Tailscale de la Raspberry Pi (rango 100.x.x.x) como Nameserver *Custom*.
3. **Forzado Local:** Se activa *Override local DNS* para obligar a todos los dispositivos de la VPN a filtrar su tráfico a través del Pi-hole.

## 🔧 Inicialización Forzada
Se interactúa con el contenedor desde la terminal del host para sincronizar reglas de *netfilter* y anunciar las rutas de salida:
`docker exec -it tailscale tailscale up --accept-routes --advertise-exit-node --force-reauth --netfilter-mode=on --accept-dns=false --advertise-routes=192.168.X.X/24 --hostname=raspberrypi`.
