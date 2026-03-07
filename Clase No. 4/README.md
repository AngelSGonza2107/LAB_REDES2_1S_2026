# Configuración ACLs

## Lista de Accesos (ACL) Estandar

```bash
access-list <access-list-number> { deny | permit | remark } <source> <source-wildcard>

! Ejemplo
access-list 10 deny 192.168.1.0 0.0.0.255
access-list 10 permit any
```

## Lista de Accesos (ACL) Extendida

```bash
access-list <numero> <permit | deny> <protocolo> <origen> <wildcard> [operador] [puerto] <destino> <wildcard> [operador] [puerto]

! Ejemplo línea única
access-list 100 deny tcp 192.168.1.0 0.0.0.255 host 192.168.2.10 eq 80
access-list 100 permit ip any any

! Ejemplo multilínea
ip access-list extended SEGURIDAD
    remark === Permitir protocolos de enrutamiento ===
    permit eigrp any any

    remark === Permitir TODO el trafico originado en Seguridad ===
    permit ip 172.16.34.128 0.0.0.63 any
exit
```

---

# Ejemplos Clase No. 4

### Tabla IP's

| Nombre           | IP            | CIDR | VLAN |
| ---------------- | ------------- | ---- | ---- |
| PC0              | 192.168.10.10 | /24  | 10   |
| PC1              | 192.168.30.10 | /24  | 30   |
| PC4              | 192.168.20.10 | /24  | 20   |
| PC5              | 192.168.40.10 | /24  | 40   |
| Interfaz VLAN 10 | 192.168.10.1  | /24  | -    |
| Interfaz VLAN 20 | 192.168.20.1  | /24  | -    |
| Interfaz VLAN 30 | 192.168.30.1  | /24  | -    |
| Interfaz VLAN 40 | 192.168.40.1  | /24  | -    |
| Core-Dist 1      | 10.0.30.1     | /30  | -    |
| Core-Dist 1      | 10.0.30.2     | /30  | -    |
| Core-Dist 2      | 10.0.40.1     | /30  | -    |
| Core-Dist 2      | 10.0.40.2     | /30  | -    |
| Core 1           | 10.0.10.1     | /30  | -    |
| Core 1           | 10.0.10.2     | /30  | -    |
| Core 2           | 10.0.20.1     | /30  | -    |
| Core 2           | 10.0.20.2     | /30  | -    |

## CAPA DISTRIBUCION

### SWD4

```bash
enable
configure terminal
    hostname SWD4
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode server

    ! vlan config
    vlan 10
        name VLAN_10
    exit
    vlan 30
        name VLAN_30
    exit

    ! port config
    interface range FastEthernet0/1 - 4
        switchport trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10,30
    exit

    do write memory
end
```

### SWD2 - SWD3

```bash
enable
configure terminal
    hostname SWD3
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface range FastEthernet0/1 - 2
        switchport trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10,30
    exit

    do write memory
end
```

### SWD1

```bash
enable
configure terminal
    hostname SWD1
    no ip domain-lookup
    ip routing

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface range FastEthernet 0/2 - 3
        switchport trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10,30
        no shutdown
    exit

    interface FastEthernet 0/1
        no switchport
        ip address 10.0.30.1 255.255.255.252
        no shutdown
    exit

    ! interfaces VLAN
    interface vlan 10
        ip address 192.168.10.1 255.255.255.0
        no shutdown
    exit

    interface vlan 30
        ip address 192.168.30.1 255.255.255.0
        no shutdown
    exit

    ! dynamic routing config
    router ospf 1
        network 192.168.10.0 0.0.0.255 area 1
        network 192.168.30.0 0.0.0.255 area 1
        network 10.0.30.0 0.0.0.3 area 1
    exit

    ! access-list config
    ! Bloquea los pings de la red 192.168.10.0 (VLAN 10) hacia 192.168.30.0 (VLAN 30)
    access-list 100 deny icmp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 echo

    ! Bloquea los pings de la red 192.168.30.0 (VLAN 30) hacia 192.168.10.0 (VLAN 10)
    access-list 100 deny icmp 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255 echo-reply

    ! Bloquea los pings de la red 192.168.40.0 (VLAN 40) hacia 192.168.10.0 (VLAN 10)
    access-list 100 deny icmp 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255 echo

    ! Bloquea los pings de la red 192.168.20.0 (VLAN 20) hacia 192.168.30.0 (VLAN 30)
    access-list 100 deny icmp 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255 echo

    ! Permite todo el tráfico IP
    access-list 100 permit ip any any

    ! Aplicar la ACL en la interfaz virtual de salida
    interface vlan 10
        ip access-group 100 out
    exit

    interface vlan 30
        ip access-group 100 out
    exit

    do write memory
end
```

### SWD6

```bash
enable
configure terminal
    hostname SWD6
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode server

    ! vlan config
    vlan 20
        name VLAN_20
    exit
    vlan 40
        name VLAN_40
    exit

    ! port config
    interface range FastEthernet0/1 - 3
        switchport trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 20,40
    exit

    do write memory
end
```

### SWD5

```bash
enable
configure terminal
    hostname SWD5
    no ip domain-lookup
    ip routing

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/2
        switchport trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 20,40
        no shutdown
    exit

    interface FastEthernet 0/1
        no switchport
        ip address 10.0.40.1 255.255.255.252
        no shutdown
    exit

    ! interfaces VLAN
    interface vlan 20
        ip address 192.168.20.1 255.255.255.0
        no shutdown
    exit

    interface vlan 40
        ip address 192.168.40.1 255.255.255.0
        no shutdown
    exit

    ! dynamic routing config
    router ospf 1
        network 192.168.20.0 0.0.0.255 area 1
        network 192.168.40.0 0.0.0.255 area 1
        network 10.0.40.0 0.0.0.3 area 1
    exit

    ! acces-list config
    ip access-list extended LADO_DERECHO
        remark === Bloquea los pings de la red 192.168.20.0 (VLAN 20) hacia 192.168.40.0 (VLAN 40) ===
        deny icmp 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255 echo

        remark === Bloquea los pings de la red 192.168.40.0 (VLAN 40) hacia 192.168.20.0 (VLAN 20) ===
        deny icmp 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255 echo-reply

        remark === Bloquea los pings de la red 192.168.10.0 (VLAN 10) hacia 192.168.40.0 (VLAN 40) ===
        deny icmp 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255 echo

        remark === Bloquea los pings de la red 192.168.30.0 (VLAN 30) hacia 192.168.20.0 (VLAN 20) ===
        deny icmp 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255 echo

        remark === Permite todo el tráfico IP ===
        permit ip any any
    exit

    ! Aplicar la ACL en la interfaz virtual de salida
    interface vlan 20
        ip access-group LADO_DERECHO out
    exit

    interface vlan 40
        ip access-group LADO_DERECHO out
    exit

    do write memory
end
```

## CAPA ACCESO

### SW1

```bash
enable
configure terminal
    hostname SW1
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet0/1
        switchport mode trunk
        switchport trunk allowed vlan 10
    exit

    interface FastEthernet0/2
        switchport mode access
        switchport access vlan 10
    exit

    do write memory
end
```

### SW2

```bash
enable
configure terminal
    hostname SW2
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet0/1
        switchport mode trunk
        switchport trunk allowed vlan 30
    exit

    interface FastEthernet0/2
        switchport mode access
        switchport access vlan 30
    exit

    do write memory
end
```

### SW3

```bash
enable
configure terminal
    hostname SW3
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet0/1
        switchport mode trunk
        switchport trunk allowed vlan 20
    exit

    interface FastEthernet0/2
        switchport mode access
        switchport access vlan 20
    exit

    do write memory
end
```

### SW4

```bash
enable
configure terminal
    hostname SW4
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab4
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet0/1
        switchport mode trunk
        switchport trunk allowed vlan 40
    exit

    interface FastEthernet0/2
        switchport mode access
        switchport access vlan 40
    exit

    do write memory
end
```

## CAPA CORE

### SWC1

```bash
enable
configure terminal
    hostname SWC1
    no ip domain-lookup
    ip routing

    ! port config
    interface FastEthernet0/1
        no switchport
        ip address 10.0.30.2 255.255.255.252
    exit

    interface FastEthernet0/2
        no switchport
        ip address 10.0.10.1 255.255.255.252
    exit

    router ospf 1
        network 10.0.10.0 0.0.0.3 area 1
        network 10.0.30.0 0.0.0.3 area 1
    exit

    do write memory
end
```

### SWC2

```bash
enable
configure terminal
    hostname SWC2
    no ip domain-lookup
    ip routing

    ! port config
    interface FastEthernet0/1
        no switchport
        ip address 10.0.20.1 255.255.255.252
    exit

    interface FastEthernet0/2
        no switchport
        ip address 10.0.40.2 255.255.255.252
    exit

    router ospf 1
        network 10.0.20.0 0.0.0.3 area 1
        network 10.0.40.0 0.0.0.3 area 1
    exit

    do write memory
end
```

### SWC0

```bash
enable
configure terminal
    hostname SWC0
    no ip domain-lookup
    ip routing

    ! port config
    interface FastEthernet0/1
        no switchport
        ip address 10.0.20.2 255.255.255.252
    exit

    interface FastEthernet0/2
        no switchport
        ip address 10.0.10.2 255.255.255.252
    exit

    router ospf 1
        network 10.0.10.0 0.0.0.3 area 1
        network 10.0.20.0 0.0.0.3 area 1
    exit

    do write memory
end
```
