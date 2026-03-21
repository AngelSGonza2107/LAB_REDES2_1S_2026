# GUÍA PRÁCTICA — Laboratorio de Redes 2

## Cisco Packet Tracer

**Protocolos:** DHCP • HSRP/VRRP • DNS • HTTP  
**Dispositivos:** Routers • Switches L2 • Switches L3 • Servidores PT

## 1. TOPOLOGÍA Y DISPOSITIVOS SUGERIDOS

A continuación se detalla la topología recomendada y el direccionamiento IP para la práctica. Se utilizan dos routers para redundancia (HSRP), dos switches capa 3 para inter-VLAN routing, switches capa 2 para acceso, y servidores PT para los servicios DHCP, DNS y HTTP.

### 1.1 Tabla de dispositivos y direccionamiento

| Dispositivo  | Interfaz    | Dirección IP        | Función                        |
| ------------ | ----------- | ------------------- | ------------------------------ |
| Router R1    | Gig0/0      | 10.0.1.2/24         | Gateway HSRP (Active)          |
| Router R2    | Gig0/0      | 10.0.1.3/24         | Gateway HSRP (Standby)         |
| SW-L3-1      | VLAN 10     | 192.168.10.2/24     | SVI - VLAN Datos               |
| SW-L3-2      | VLAN 10     | 192.168.10.3/24     | SVI - VLAN Datos               |
| **HSRP VIP** | **VLAN 10** | **192.168.10.1/24** | **Virtual IP (Gateway PCs)**   |
| **HSRP VIP** | **VLAN 20** | **192.168.20.1/24** | **Virtual IP (Gateway Serv.)** |
| SRV-DHCP/DNS | NIC         | 192.168.20.10/24    | Servidor DHCP y DNS            |
| SRV-HTTP     | NIC         | 192.168.20.20/24    | Servidor Web (HTTP)            |
| SW-L2-1      | ---         | ---                 | Switch acceso (VLAN 10)        |
| SW-L2-2      | ---         | ---                 | Switch acceso (VLAN 20)        |
| PCs (2-4)    | DHCP        | 192.168.10.x/24     | Clientes DHCP                  |

> **ℹ️ Nota sobre la topología:**
>
> - **HSRP Virtual IP (VIP):** es la IP que los clientes usarán como gateway.
> - Los switches L3 hacen inter-VLAN routing entre VLAN 10 y VLAN 20.
> - Los switches L2 solo proveen acceso y asignan puertos a VLANs.
> - VRRP es la alternativa abierta a HSRP (misma lógica, comandos diferentes).

## 2. GUÍA TÉCNICA

Favor seguir los pasos de la [guía técnica](Ejemplo%20Practico%20Semana%209.pdf) de este mismo directorio.
Y realizar los cambios en el archivo `Topologia_HSRP-VRRP-DNS.pkt` ya que este no ha sido configurado, es solamente la topología.

## 3. PRUEBAS Y DEMOSTRACIÓN

Secuencia recomendada para demostrar todos los protocolos en clase:

### 3.1 Verificar DHCP

1. En los PCs, configurar la interfaz de red como **DHCP**
2. Ejecutar `ipconfig /all` y verificar IP, gateway (192.168.10.1) y DNS (192.168.20.10)

### 3.2 Verificar DNS + HTTP

1. Desde un PC: `nslookup www.redes2.com` → debe resolver a 192.168.20.20
2. Abrir Web Browser → `http://www.redes2.com` → debe cargar la página

### 3.3 Verificar HSRP (prueba de failover)

1. `show standby brief` en ambos SW-L3 → confirmar Active y Standby
2. Desde un PC: `ping -t 192.168.20.20` (ping continuo al servidor web)
3. **Apagar SW-L3-1** (shutdown en las SVIs o apagar el dispositivo)
4. Observar que el ping se recupera → SW-L3-2 asumió el rol Active
5. **Reactivar SW-L3-1** → con preempt, retoma el rol Active

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

| Problema                  | Solución                                                                                    |
| ------------------------- | ------------------------------------------------------------------------------------------- |
| PCs no obtienen IP        | Verificar `ip helper-address` en la SVI, que el servidor DHCP esté ON y el pool correcto    |
| No resuelve DNS           | Verificar que el DNS esté ON, los registros existan y los PCs tengan el DNS server correcto |
| No carga la web           | Verificar HTTP ON en el servidor, y que haya conectividad (ping) entre VLANs                |
| HSRP no funciona          | Verificar misma VIP, mismo número de grupo, `standby version 2` en ambos                    |
| No hay inter-VLAN routing | Verificar `ip routing` en el switch L3, SVIs con `no shutdown`, trunk configurado           |
