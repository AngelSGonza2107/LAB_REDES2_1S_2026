# GUÍA PRÁCTICA — Laboratorio de Redes 2

**Protocolos:** DNS • HTTP • Redes Inalámbricas

## 1. TOPOLOGÍA Y DISPOSITIVOS

La topología integra conmutación capa 2 (VLANs, VTP, EtherChannel, STP), enrutamiento capa 3 (OSPF, Router-on-a-Stick) y conectividad inalámbrica, todo en un escenario compacto pero funcional

### 1.1 Tabla de dispositivos y direccionamiento

| Dispositivo | Modelo     | Interfaz       | Dirección IP      | Función                            |
| ----------- | ---------- | -------------- | ----------------- | ---------------------------------- |
| R1          | 2911       | Gig0/0.10      | 192.168.10.1/24   | Gateway VLAN 10 (Administración)   |
| R1          | 2911       | Gig0/0.20      | 192.168.20.1/24   | Gateway VLAN 20 (Ventas)           |
| R1          | 2911       | Gig0/0.30      | 192.168.30.1/24   | Gateway VLAN 30 (Servidores)       |
| R1          | 2911       | Gig0/1         | 10.0.0.1/30       | Enlace WAN hacia R2                |
| R2          | 2911       | Gig0/0         | 10.0.0.2/30       | Enlace WAN hacia R1                |
| R2          | 2911       | Gig0/1         | 172.16.0.1/24     | Enlace hacia router inalámbrico    |
| WR1         | WRT300N    | Internet (WAN) | 172.16.0.2/24     | Puerto WAN (GW: 172.16.0.1)        |
| WR1         | WRT300N    | LAN / WiFi     | 172.16.1.1/24     | Red inalámbrica (DHCP .100–.150)   |
| SW1         | 2960       | —              | —                 | VTP Server, STP Root, EtherChannel |
| SW2         | 2960       | —              | —                 | VTP Client, acceso VLAN 10 y 30    |
| SW3         | 2960       | —              | —                 | VTP Client, acceso VLAN 20 y 30    |
| SRV-DNS     | Server-PT  | Fa0            | 192.168.30.10/24  | Servidor DNS (GW: 192.168.30.1)    |
| SRV-HTTP    | Server-PT  | Fa0            | 192.168.30.20/24  | Servidor HTTP (GW: 192.168.30.1)   |
| PC1         | PC-PT      | Fa0            | 192.168.10.10/24  | Cliente VLAN 10 (GW: 192.168.10.1) |
| PC2         | PC-PT      | Fa0            | 192.168.20.10/24  | Cliente VLAN 20 (GW: 192.168.20.1) |
| Laptop1     | Laptop-PT  | WiFi (WPC300N) | DHCP (172.16.1.x) | Cliente inalámbrico                |
| Phone1      | Smartphone | WiFi           | DHCP (172.16.1.x) | Cliente inalámbrico                |

> **ℹ️ Notas sobre la topología:**
>
> - **Router-on-a-Stick:** R1 usa subinterfaces en Gig0/0 para enrutar entre VLANs.
> - **EtherChannel (LACP):** SW1↔SW2 (3 enlaces, Po1) y SW1↔SW3 (3 enlaces, Po2).
> - **Red inalámbrica:** SSID `LAB-WIFI`, seguridad WPA2/AES, contraseña `Lab6Redes2026`.

## 2. GUÍA TÉCNICA

Favor seguir los pasos de la [guía técnica](./Ejemplo%20Practico%20Semana%208.pdf) de este mismo directorio.
Y realizar los cambios en el archivo `Topologia_Redes.pkt` ya que este no ha sido configurado, es solamente la topología.

## 3. PRUEBAS Y DEMOSTRACIÓN

Secuencia recomendada para demostrar todos los protocolos:

### 3.1 Verificar VTP y VLANs

1. En cualquier switch: `show vtp status` → confirmar dominio `LAB-REDES` y modo correcto
2. `show vlan brief` → verificar VLANs 10, 20, 30, 99 presentes en los tres switches
3. `show interfaces trunk` → confirmar puertos troncales activos

### 3.2 Verificar EtherChannel

1. En SW1: `show etherchannel summary` → Po1 y Po2 deben mostrar flags (SU)
2. Verificar que los 3 puertos de cada grupo aparezcan como miembros activos (P)

### 3.3 Verificar OSPF

1. En R1: `show ip ospf neighbor` → debe aparecer R2 con estado FULL
2. En R1: `show ip route ospf` → debe verse 172.16.0.0/24 como ruta O y 172.16.1.0/24 como ruta O E2
3. En R2: `show ip route ospf` → debe verse 192.168.10.0/24, .20.0/24 y .30.0/24

### 3.4 Verificar DNS + HTTP

1. Desde PC1: `nslookup www.lab6redes.com` → debe resolver a 192.168.30.20
2. Abrir Web Browser → `http://www.lab6redes.com` → debe cargar la página

### 3.5 Verificar Red Inalámbrica (prueba de conectividad extremo a extremo)

1. En Laptop1: verificar IP obtenida por DHCP (`ipconfig`) → debe ser 172.16.1.x
2. Desde Laptop1: `ping 192.168.10.10` → debe llegar a PC1 (atraviesa WiFi → WR1 → R2 → R1 → SW → PC1)
3. Desde Laptop1: abrir Web Browser → `http://www.lab6redes.com` → debe cargar la página
4. Desde PC1: `ping 172.16.1.100` → debe llegar al dispositivo inalámbrico

## 4. COMANDOS DE TROUBLESHOOTING

### 4.1 Comandos generales

```
show running-config
show ip interface brief
show vlan brief
show interfaces trunk
show ip route
show arp
```

### 4.2 Problemas comunes

| Problema                             | Solución                                                                                                       |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------- |
| VLANs no se propagan a SW2/SW3       | Verificar trunks activos antes de VTP, mismo dominio y contraseña en todos los switches                        |
| EtherChannel no se forma (flags SD)  | Verificar mismo modo LACP (active/active), misma velocidad y configuración troncal en ambos lados              |
| No hay conectividad inter-VLAN       | Verificar subinterfaces en R1 con `encapsulation dot1Q`, interfaz Gig0/0 con `no shutdown`                     |
| OSPF no forma adyacencia             | Verificar misma área (0), redes declaradas con wildcard correcta, interfaces up/up                             |
| DNS no resuelve nombres              | Verificar servicio DNS en ON, registros A existentes, clientes con DNS server 192.168.30.10                    |
| No carga la página web               | Verificar HTTP en ON en SRV-HTTP, conectividad con `ping 192.168.30.20`, DNS correcto                          |
| WiFi no se conecta a red cableada    | **Cambiar WRT300N de modo NAT a modo Router** en Setup > Advanced Routing, verificar ruta estática en R2       |
| Laptop no ve la red WiFi             | Verificar módulo WPC300N instalado (apagar laptop, cambiar tarjeta), SSID broadcast habilitado                 |
| Dispositivos WiFi sin IP             | Verificar DHCP habilitado en WR1, rango correcto (172.16.1.100–.150)                                           |
| Paquetes se pierden en R2 hacia WiFi | Verificar ruta estática `ip route 172.16.1.0 255.255.255.0 172.16.0.2` y `redistribute static subnets` en OSPF |
