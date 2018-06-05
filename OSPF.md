# OSPF

## Variablen

8922:8922:8922:9::/64
8922:8922:8922:5::/64
8922:8922:8922:4::/64
7354:7354:7354:9::/64
7354:7354:7354:5::/64
5094:5094:5094:9::/64
5094:5094:5094:5::/64

## Router 1

```PacketTracer
enable
    conf t
        hostname R1
        ipv6 unicast-routing
        ipv6 route ::/0 lo0
        int loopback 0
            ip address 11.11.11.1 255.255.255.0
            ipv6 enable
            ipv6 address 1:1:1:1::/64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        int se0/2/0
            bandwidth 2000
            ipv6 enable
            ipv6 add fe80::1 link-local
            ipv6 add autoconfig
            description .
            no shutdown
            exit
        int se0/2/1
            shutdown
            exit
        int fa0/0
            ipv6 ospf 1 area 0
            ipv6 ospf priority 0
            no shutdown
            description .
            exit
        int fa0/1
            shutdown
            exit
        ipv6 router ospf 1
            default-information originate
            exit
        exit
        cop ru st
```

## Router 2

```PacketTracer
enable
    conf t
        hostname R2
        ipv6 unicast-routing
        int loopback 0
            ip address 11.11.11.2 255.255.255.0
            no shutdown
            description .
            exit
        int se0/2/0
            clock rate 2000000
            bandwidth 2000
            ipv6 enable
            ipv6 add fe80::2 link-local
            ipv6 ospf 1 area 0
            description .
            no shutdown
            exit
        int se0/2/1
            shutdown
            exit
        int fa0/0
            ipv6 ospf 1 area 0
            ipv6 ospf priority 255
            no shutdown
            description .
            exit
        int fa0/1
            ipv6 enable
            ipv6 add 6219:6219:6219:8::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        ipv6 router ospf 1
            exit
        exit
        cop ru st
```

## Router 3

```PacketTracer
enable
    conf t
        hostname R3
        ipv6 unicast-routing
        int loopback 0
            ip address 11.11.11.3 255.255.255.0
            no shutdown
            description .
            exit
        int se0/2/0
            shutdown
            exit
        int se0/2/1
            shutdown
            exit
        int fa0/0
            ipv6 ospf 1 area 0
            ipv6 ospf priority 100
            no shutdown
            description .
            exit
        int fa0/1
            ipv6 enable
            ipv6 add 8922:8922:8922:5::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
        ipv6 router ospf 1
            exit
        exit
        cop ru st
```

## Router 4

```PacketTracer
enable
    conf t
        hostname R4
        ipv6 unicast-routing
        int loopback 0
            ip address 11.11.11.4 255.255.255.0
            no shutdown
            description .
            exit
        int se0/2/0
            clock rate 2000000
            bandwidth 2000
            ipv6 enable
            ipv6 add fe80::1 link-local
            ipv6 ospf 1 area 0
            description .
            no shutdown
            exit
        int se0/2/1
            shutdown
            exit
        int fa0/0
            ipv6 ospf 1 area 0
            ipv6 ospf priority 254
            no shutdown
            description .
            exit
        int fa0/1
            ipv6 enable
            ipv6 add 8922:8922:8922:4::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        ipv6 router ospf 1
            exit
        exit
        cop ru st
```

## Router 5

```PacketTracer
enable
    conf t
        hostname R5
        ipv6 unicast-routing
        int loopback 0
            ip address 11.11.11.5 255.255.255.0
            no shutdown
            description .
            exit
        int se0/0/0
            bandwidth 2000
            ipv6 enable
            ipv6 add fe80::1 link-local
            ipv6 ospf 1 area 0
            description .
            no shutdown
            exit
        int se0/0/1
            shutdown
            exit
        int se0/1/0
            shutdown
            exit
        int se0/1/1
            shutdown
            exit
        int fa0/0
            ipv6 enable
            ipv6 add 7354:7354:7354:5::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        int fa0/1
            ipv6 enable
            ipv6 add 7354:7354:7354:9::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        ipv6 router ospf 1
            exit
        exit
        cop ru st
```

## Router 6

```PacketTracer
enable
    conf t
        hostname R6
        ipv6 unicast-routing
        int loopback 0
            ip address 11.11.11.6 255.255.255.0
            no shutdown
            description .
            exit
        int se0/0/0
            bandwidth 2000
            ipv6 enable
            ipv6 add fe80::2 link-local
            ipv6 ospf 1 area 0
            description .
            no shutdown
            exit
        int se0/0/1
            shutdown
            exit
        int se0/1/0
            shutdown
            exit
        int se0/1/1
            shutdown
            exit
        int fa0/0
            ipv6 enable
            ipv6 add 5094:5094:5094:5::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        int fa0/1
            ipv6 enable
            ipv6 add 5094:5094:5094:9::/64 eui-64
            ipv6 ospf 1 area 0
            no shutdown
            description .
            exit
        ipv6 router ospf 1
            exit
        exit
        cop ru st
```