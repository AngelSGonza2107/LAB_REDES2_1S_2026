# VLANs e Inter-VLAN routing

## Vlans

| No  | Vlan         | Id Red       | Mascara de red | Puerta de enlace predeterminada |
| --- | ------------ | ------------ | -------------- | ------------------------------- |
| 10  | PRIMARIA     | 192.168.10.0 | /24            | 192.168.10.1                    |
| 20  | BASICOS      | 192.168.20.0 | /24            | 192.168.20.1                    |
| 30  | BACHILLERATO | 192.168.30.0 | /24            | 192.168.30.1                    |

## Tabla de IP's

| Equipo | Vlan         | IP            | Mascara de red |
| ------ | ------------ | ------------- | -------------- |
| PC0    | PRIMARIA     | 192.168.10.10 | /24            |
| PC1    | BASICOS      | 192.168.20.10 | /24            |
| PC2    | BACHILLERATO | 192.168.30.10 | /24            |
| PC3    | PRIMARIA     | 192.168.10.11 | /24            |
| PC4    | PRIMARIA     | 192.168.10.12 | /24            |
| PC5    | BASICOS      | 192.168.20.11 | /24            |
| PC6    | BACHILLERATO | 192.168.30.11 | /24            |

## SW2

#### Configuracion VLANs

```
enable
configure terminal
    no ip domain-lookup
    hostname SW2
    enable secret lab1

    vlan 10
        name PRIMARIA
    exit
    vlan 20
        name BASICOS
    exit
    vlan 30
        name BACHILLERATO
    exit
```

#### Configuracion VTP

```
    vtp version 2
    vtp mode server
    vtp domain redes
    vtp password redes
```

#### Configuracion interfaces troncales

```
    interface range fastEthernet 0/1-3
        switchport mode trunk
        switchport trunk allow vlan 10,20,30
    exit

    do write memory
exit
```

## SW0

#### Configuracion VTP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW0

    vtp version 2
    vtp mode client
    vtp domain redes
    vtp password redes
```

#### Configuracion interfaces troncales

```
    interface fastEthernet 0/4
        switchport mode trunk
        switchport trunk allow vlan 10,20,30
    exit
```

#### Configuracion interfaces de acceso

```
    interface fastEthernet 0/1
        switchport mode access
        switchport access vlan 10
    exit
    interface fastEthernet 0/2
        switchport mode access
        switchport access vlan 20
    exit
    interface fastEthernet 0/3
        switchport mode access
        switchport access vlan 30
    exit

    do write memory
exit
```

## SW1

#### Configuracion VTP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW0

    vtp version 2
    vtp mode client
    vtp domain redes
    vtp password redes
```

#### Configuracion interfaces troncales

```
    interface fastEthernet 0/5
        switchport mode trunk
        switchport trunk allow vlan 10,20,30
    exit
```

#### Configuracion interfaces de acceso

```
    interface range fastEthernet 0/1-2
        switchport mode access
        switchport access vlan 10
    exit
    interface fastEthernet 0/3
        switchport mode access
        switchport access vlan 20
    exit
    interface fastEthernet 0/4
        switchport mode access
        switchport access vlan 30
    exit

    do write memory
exit
```

## R1

#### Configuracion inicial

```
enable
configure terminal
    no ip domain-lookup
    hostname R1

    interface gigabitEthernet 0/0
        no shutdown
    exit
```

#### Configuracion inter-VLAN

```
    interface gigabitEthernet 0/0.10
        encapsulation dot1Q 10
        ip address 192.168.10.1 255.255.255.0
        no shutdown
    exit
    interface gigabitEthernet 0/0.20
        encapsulation dot1Q 20
        ip address 192.168.20.1 255.255.255.0
        no shutdown
    exit
    interface gigabitEthernet 0/0.30
        encapsulation dot1Q 30
        ip address 192.168.30.1 255.255.255.0
        no shutdown
    exit

    do write memory
exit
```

## Comandos para revisión de protocolos

```
show run                         ! ver configuración global en ejecución
show vtp status                  ! ver estado VTP
show vlan brief                  ! ver resumen de VLANs
show interfaces trunk            ! ver qué puertos son trunk y más información
```

---

# STP

> Por defecto el protocolo STP estará activo en los switches, pero este estará habilitado en **modo PVST**. Si necesitamos que sea Rapid PVST debemos asignarlo **manualmente** en cada switch de la red local.

## SW1_STP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW1_STP

    spanning-tree mode rapid-pvst

    do write memory
exit
```

## SW2_STP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW2_STP

    spanning-tree mode rapid-pvst

    do write memory
exit
```

## SW3_STP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW3_STP

    spanning-tree mode rapid-pvst

    do write memory
exit
```

## Comandos para revisión de protocolos

```
show spanning-tree summary       ! ver resumen de STP
show spanning-tree               ! ver estado STP global
show spanning-tree {interfaz}    ! ver STP en una interfaz determinada
```
