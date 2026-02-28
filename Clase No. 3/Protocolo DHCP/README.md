# Protocolo DHCP

### MS1

```bash
enable
configure terminal
    hostname MS1
    no ip domain-lookup

    ! activar enrutamiento en switch multicapa
    ip routing

    ! vlan config
    vlan 10
        name VLAN_IZQ
    exit
    vlan 20
        name VLAN_DER
    exit

    ! port config (switches)
    interface FastEthernet 0/1
        switchport mode access
        switchport access vlan 10
        no shutdown
    exit

    interface FastEthernet 0/2
        switchport mode access
        switchport access vlan 20
        no shutdown
    exit

    ! port config (servers)
    interface FastEthernet 0/5
        no switchport
        ip address 10.0.10.1 255.255.255.252
        no shutdown
    exit

    interface FastEthernet 0/6
        no switchport
        ip address 10.0.20.1 255.255.255.252
        no shutdown
    exit

    ! interfaces VLAN
    interface vlan 10
        ip address 192.168.10.1 255.255.255.0
        ip helper-address 10.0.10.2
        no shutdown
    exit

    interface vlan 20
        ip address 192.168.20.1 255.255.255.0
        ip helper-address 10.0.20.2
        no shutdown
    exit

    do write memory
end
```

### S1

```bash
enable
configure terminal
    hostname SW1
    no ip domain-lookup

    vlan 10
        name VLAN_IZQ
    exit

    ! port config
    interface FastEthernet 0/1
        switchport mode access
        switchport access vlan 10
        no shutdown
    exit

    interface range FastEthernet 0/2 - 3
        switchport mode access
        switchport access vlan 10
        no shutdown
    exit

    do write memory
end
```

### S2

```bash
enable
configure terminal
    hostname SW2
    no ip domain-lookup

    vlan 20
        name VLAN_DER
    exit

    ! port config
    interface FastEthernet 0/1
        switchport mode access
        switchport access vlan 20
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
