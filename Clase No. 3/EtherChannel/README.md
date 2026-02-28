# Configuración Etherchannel

## 1. EtherChannel con LACP

**Protocolo:** LACP (Link Aggregation Control Protocol)

**Enlace:**

- **MS1 → MS3**
  - FastEthernet 0/1
  - FastEthernet 0/2
  - FastEthernet 0/3
  - FastEthernet 0/4

## 2. EtherChannel con PAgP

**Protocolo:** PAgP (Port Aggregation Protocol)

**Enlace:**

- **MS3 → MS2**
  - FastEthernet 0/22
  - FastEthernet 0/23
  - FastEthernet 0/24

## 3. EtherChannel Estático

**Modo:** On (sin negociación)

**Enlace:**

- **MS1 → MS2**
  - FastEthernet 0/10
  - FastEthernet 0/11

---

> 📌 **Nota:** Hay algunos switches como el caso del 3560 que no aceptan el comando `switchport mode trunk` solo así, antes hay que definir la encapsulación manualmente. Esto se hace con el comando `switchport trunk encapsulation dot1q`

### MS1

```bash
enable
configure terminal
    hostname MS1
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab3
    vtp password 123
    vtp mode server

    ! vlan config
    vlan 10
        name VLAN_TEST
    exit

    ! LACP config
    interface Port-Channel 1
        description trunk hacia MS3
    exit
    interface range FastEthernet 0/1 - 4
        channel-group 1 mode active
    exit

    ! Estatic config
    interface Port-Channel 2
        description trunk hacia MS2
    exit
    interface range FastEthernet 0/10 - 11
        channel-group 2 mode on
    exit

    ! port config
    interface Port-Channel 1
        switchprot trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10
        no shutdown
    exit

    interface Port-Channel 2
        switchprot trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10
        no shutdown
    exit

    interface FastEthernet 0/15
        switchport mode access
        switchport access vlan 10
        no shutdown
    exit

    do write memory
end
```

### MS2

```bash
enable
configure terminal
    hostname MS2
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab3
    vtp password 123
    vtp mode client

    ! PAgP config
    interface Port-Channel 1
        description trunk hacia MS3
    exit
    interface range FastEthernet 0/22 - 24
        channel-group 1 mode desirable
    exit

    ! Estatic config
    interface Port-Channel 2
        description trunk hacia MS1
    exit
    interface range FastEthernet 0/10 - 11
        channel-group 2 mode on
    exit

    ! port config
    interface Port-Channel 1
        switchprot trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10
        no shutdown
    exit

    interface Port-Channel 2
        switchprot trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10
        no shutdown
    exit

    interface FastEthernet 0/15
        switchport mode access
        switchport access vlan 10
        no shutdown
    exit

    do write memory
end
```

### MS3

```bash
enable
configure terminal
    hostname MS3
    no ip domain-lookup

    ! vtp config
    vtp version 2
    vtp domain lab3
    vtp password 123
    vtp mode client

    ! LACP config
    interface Port-Channel 1
        description trunk hacia MS1
    exit
    interface range FastEthernet 0/1 - 4
        channel-group 1 mode active
    exit

    ! PAgP config
    interface Port-Channel 2
        description trunk hacia MS2
    exit
    interface range FastEthernet 0/22 - 24
        channel-group 2 mode desirable
    exit

    ! port config
    interface Port-Channel 1
        switchprot trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10
        no shutdown
    exit

    interface Port-Channel 2
        switchprot trunk encapsulation dot1q
        switchport mode trunk
        switchport trunk allowed vlan 10
        no shutdown
    exit

    do write memory
end
```

## Comandos de revisión

```bash
show ehterchannel                    ! Configuración detallada de EtherChannel
show etherchannel summary            ! Resumen de configuración EtherChannel
show etherchannel port-channel       ! Información detallada de Port-Channels creados

show lacp neighbor                   ! Indica si LACP está configurado correctamente en el dispositivo vecino
show pagp neighbor                   ! Indica si PAgP está configurado correctamente en el dispositivo vecino
```
