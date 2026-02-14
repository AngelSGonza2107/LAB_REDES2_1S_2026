# STP y Port Security

> Por defecto el protocolo STP estará activo en los switches, pero este estará habilitado en **modo PVST**. Si necesitamos que sea Rapid PVST debemos asignarlo **manualmente** en cada switch de la red local.

## SW1_STP (Port Security - Mode Protect & Restrict)

```
enable
configure terminal
    no ip domain-lookup
    hostname SW1_STP

    interface range Fa0/2 - 4
        switchport mode trunk
    exit

    interface Fa0/5
        switchport mode access
        switchport port-security
        switchport port-security maximum 1
        switchport port-security mac-address sticky
        switchport port-security violation protect
    exit

    do write memory
exit
```

## SW2_STP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW2_STP

    interface range Fa0/1 - 4
        switchport mode trunk
    exit

    interface Fa0/5
        switchport mode access
        switchport port-security
        switchport port-security maximum 1
        switchport port-security mac-address 0001.64D3.8B64
        switchport port-security violation restrict
    exit

    do write memory
exit
```

## SW3_STP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW3_STP

    interface range Fa0/2 - 4
        switchport mode trunk
    exit

    do write memory
exit
```

## SW1_RSTP (Port Security - Mode Shutdown)

```
enable
configure terminal
    no ip domain-lookup
    hostname SW1_RSTP

    spanning-tree mode rapid-pvst

    interface range Fa0/2 - 4
        switchport mode trunk
    exit

    interface Fa0/5
        switchport mode access
        switchport port-security
        switchport port-security maximum 1
        switchport port-security mac-address sticky
        switchport port-security violation shutdown
    exit

    do write memory
exit
```

## SW2_RSTP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW2_RSTP

    spanning-tree mode rapid-pvst

    interface range Fa0/1 - 4
        switchport mode trunk
    exit

    interface Fa0/5
        switchport mode access
        switchport port-security
        switchport port-security maximum 1
        switchport port-security mac-address sticky
        switchport port-security violation shutdown
    exit

    do write memory
exit
```

## SW3_RSTP

```
enable
configure terminal
    no ip domain-lookup
    hostname SW3_RSTP

    spanning-tree mode rapid-pvst

    interface range Fa0/2 - 4
        switchport mode trunk
    exit

    do write memory
exit
```

## Comandos para revisión de protocolos

```
show spanning-tree summary       ! ver resumen de STP y modo
show spanning-tree               ! ver estado STP de interfaces
show spanning-tree {interfaz}    ! ver STP en una interfaz determinada

show port-security                   ! ver puertos con port security habilitado
show port-security interface fa0/1   ! ver estado port security del puerto especificado

(Seguridad del switch)
enable secret contrasena         ! establece una contraseña encriptada al acceso root del switch
```
