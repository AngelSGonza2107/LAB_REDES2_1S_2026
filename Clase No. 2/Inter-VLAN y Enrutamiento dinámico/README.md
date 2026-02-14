## Configuración VTP utilizada

- Domain: lab2
- Password: 123

## VLANs creadas

#### ARRIBA

VERDE - 10  
CELESTE - 20

#### DERECHA

VERDE - 11  
CELESTE - 21

#### IZQUIERDA

VERDE - 12  
CELESTE - 22

## Direcciones de Red

### ARRIBA

|  VLAN   | ID de VLAN | Direccion de Red |
| :-----: | :--------: | :--------------: |
|  VERDE  |     10     | 192.168.10.0 /24 |
| CELESTE |     20     | 192.168.20.0 /24 |

### DERECHA

|  VLAN   | ID de VLAN | Direccion de Red |
| :-----: | :--------: | :--------------: |
|  VERDE  |     11     | 192.168.11.0 /24 |
| CELESTE |     21     | 192.168.21.0 /24 |

### IZQUIERDA

|  VLAN   | ID de VLAN | Direccion de Red |
| :-----: | :--------: | :--------------: |
|  VERDE  |     12     | 192.168.12.0 /24 |
| CELESTE |     22     | 192.168.22.0 /24 |

### CORE/BACKBONE

| Protocolo |   ID de red   | Subnet Mask (/24) | Primer Host |
| :-------: | :-----------: | :---------------: | :---------: |
|   OSPF    | 10.0.10.0 /24 |   255.255.255.0   |  10.0.10.1  |
|   EIGRP   | 10.0.20.0 /24 |   255.255.255.0   |  10.0.20.1  |
|    RIP    | 10.0.30.0 /24 |   255.255.255.0   |  10.0.30.1  |

## Direcciones IP utilizadas

### VERDE

| Nombre del dispositivo | Direccion IP asignada |  Subnet Mask  | Default Gateway |
| :--------------------: | :-------------------: | :-----------: | :-------------: |
|          PC1           |     192.168.12.2      | 255.255.255.0 |  192.168.12.1   |
|          PC2           |     192.168.11.3      | 255.255.255.0 |  192.168.11.1   |
|          PC5           |     192.168.10.4      | 255.255.255.0 |  192.168.10.1   |

### CELESTE

| Nombre del dispositivo | Direccion IP asignada |  Subnet Mask  | Default Gateway |
| :--------------------: | :-------------------: | :-----------: | :-------------: |
|          PC3           |     192.168.21.2      | 255.255.255.0 |  192.168.21.1   |
|          PC4           |     192.168.20.3      | 255.255.255.0 |  192.168.20.1   |
|          PC6           |     192.168.22.4      | 255.255.255.0 |  192.168.22.1   |

#### SW3_ARRIBA

```bash
enable
configure terminal
    hostname SW3_ARRIBA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode server

    ! vlan config
    vlan 10
        name VERDE
    vlan 20
        name CELESTE
    exit

    ! port config
    interface range FastEthernet 0/2 - 3
        switchport mode trunk
        switchport trunk allowed vlan 10,20
        no shutdown
    exit

    interface GigabitEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 10,20
        no shutdown
    exit

    do write memory
end
```

#### SW1_ARRIBA

```bash
enable
configure terminal
    hostname SW1_ARRIBA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 10,20
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 20
        no shutdown
    exit

    do write memory
end
```

#### SW2_ARRIBA

```bash
enable
configure terminal
    hostname SW2_ARRIBA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 10,20
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 10
        no shutdown
    exit

    do write memory
end
```

#### SW1_DERECHA

```bash
enable
configure terminal
    hostname SW1_DERECHA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode server

    ! vlan config
    vlan 11
        name VERDE
    vlan 21
        name CELESTE
    exit

    ! port config
    interface range FastEthernet 0/2 - 3
        switchport mode trunk
        switchport trunk allowed vlan 11,21
        no shutdown
    exit

    interface GigabitEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 11,21
        no shutdown
    exit

    do write memory
end
```

#### SW2_DERECHA

```bash
enable
configure terminal
    hostname SW2_DERECHA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 11,21
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 11
        no shutdown
    exit

    do write memory
end
```

#### SW3_DERECHA

```bash
enable
configure terminal
    hostname SW3_DERECHA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 11,21
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 21
        no shutdown
    exit

    do write memory
end
```

#### SW1_IZQUIERDA

```bash
enable
configure terminal
    hostname SW1_IZQUIERDA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 12,22
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 22
        no shutdown
    exit

    do write memory
end
```

#### SW2_IZQUIERDA

```bash
enable
configure terminal
    hostname SW2_IZQUIERDA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode client

    ! port config
    interface FastEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 12,22
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 12
        no shutdown
    exit

    do write memory
end
```

#### SW3_IZQUIERDA

```bash
enable
configure terminal
    hostname SW3_IZQUIERDA
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab2
    vtp password 123
    vtp mode server

    ! vlan config
    vlan 12
        name VERDE
    vlan 22
        name CELESTE
    exit

    ! port config
    interface range FastEthernet 0/2 - 3
        switchport mode trunk
        switchport trunk allowed vlan 12,22
        no shutdown
    exit

    interface GigabitEthernet 0/1
        switchport mode trunk
        switchport trunk allowed vlan 12,22
        no shutdown
    exit

    do write memory
end
```

### Core/Backbone

#### R1

```bash
enable
configure terminal
    hostname R1
    no ip domain-lookup

    ! port config
    interface GigabitEthernet 0/0
        no shutdown
    exit

    interface GigabitEthernet 0/0.12
        encapsulation dot1Q 12
        ip address 192.168.12.1 255.255.255.0
        no shutdown
    exit

    interface GigabitEthernet 0/0.22
        encapsulation dot1Q 22
        ip address 192.168.22.1 255.255.255.0
        no shutdown
    exit

    ! EIGRP config
    interface GigabitEthernet 0/3/0
        ip address 10.0.20.1 255.255.255.0
        no shutdown
    exit

    router EIGRP 10
        network 10.0.20.0 0.0.0.255
        network 10.0.10.0 0.0.0.255
        network 192.168.12.0 0.0.0.255
        network 192.168.22.0 0.0.0.255
        no auto-summary
        redistribute ospf 1 metric 5 0 255 1 1500
    exit

    ! OSPF config
    interface GigabitEthernet 0/2/0
        ip address 10.0.10.1 255.255.255.0
        no shutdown
    exit

    router OSPF 1
        network 10.0.10.0 0.0.0.255 area 0
        network 10.0.20.0 0.0.0.255 area 0
        network 192.168.12.0 0.0.0.255 area 0
        network 192.168.22.0 0.0.0.255 area 0
        redistribute eigrp 10 subnets
    exit

    do write memory
end
```

#### R2

```bash
enable
configure terminal
    hostname R2
    no ip domain-lookup

    ! port config
    interface GigabitEthernet 0/0
        no shutdown
    exit

    interface GigabitEthernet 0/0.11
        encapsulation dot1Q 11
        ip address 192.168.11.1 255.255.255.0
        no shutdown
    exit

    interface GigabitEthernet 0/0.21
        encapsulation dot1Q 21
        ip address 192.168.21.1 255.255.255.0
        no shutdown
    exit

    ! OSPF config
    interface GigabitEthernet 0/2/0
        ip address 10.0.10.2 255.255.255.0
        no shutdown
    exit

    router OSPF 1
        network 10.0.10.0 0.0.0.255 area 0
        network 10.0.30.0 0.0.0.255 area 0
        network 192.168.11.0 0.0.0.255 area 0
        network 192.168.21.0 0.0.0.255 area 0
        redistribute rip metric 5 subnets
    exit

    ! RIP config
    interface GigabitEthernet 0/3/0
        ip address 10.0.30.1 255.255.255.0
        no shutdown
    exit

    router RIP
        version 2
        network 10.0.10.0
        network 10.0.30.0
        network 192.168.11.0
        network 192.168.21.0
        no auto-summary
        redistribute ospf 1 metric 5
    exit

    do write memory
end
```

#### R3

```bash
enable
configure terminal
    hostname R3
    no ip domain-lookup

    ! port config
    interface GigabitEthernet 0/0
        no shutdown
    exit

    interface GigabitEthernet 0/0.10
        encapsulation dot1Q 10
        ip address 192.168.10.1 255.255.255.0
        no shutdown
    exit

    interface GigabitEthernet 0/0.20
        encapsulation dot1Q 20
        ip address 192.168.20.1 255.255.255.0
        no shutdown
    exit

    ! EIGRP config
    interface GigabitEthernet 0/3/0
        ip address 10.0.20.2 255.255.255.0
        no shutdown
    exit

    router EIGRP 10
        network 10.0.20.0 0.0.0.255
        network 10.0.30.0 0.0.0.255
        network 192.168.10.0 0.0.0.255
        network 192.168.20.0 0.0.0.255
        no auto-summary
    exit

    ! RIP config
    interface GigabitEthernet 0/2/0
        ip address 10.0.30.2 255.255.255.0
        no shutdown
    exit

    router RIP
        version 2
        network 10.0.30.0
        network 10.0.20.0
        network 192.168.10.0
        network 192.168.20.0
        no auto-summary
    exit

    do write memory
end
```
