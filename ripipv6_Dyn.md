# ripipv6_Dyn

## Variablen

```PacketTracer
PROZESSNAME     z.B.: cyx
IP1             z.B.: 2001:0:1310:1310::/64
IP2             z.B.: 2001:3785:0:3785::/64
IP3             z.B.: 2001:5778:5778::/64
IPEXT           z.B.: 2001:7211:7211:7211::1/64
```

## Router 1

```PacketTracer
enable
    conf t
        hostname Router1
        ipv6 unicast-routing
        ipv6 route ::/0 fa0/1

        ipv6 router rip PROZESSNAME
            exit

        int fa0/0
            ipv6 enable
            ipv6 add IP1 eui-64
            ipv6 rip PROZESSNAME enable
            no shutdown

        int fa0/1
            ipv6 enable
            ipv6 add IPEXT
            no shutdown

        int se0/1/0
            clock rate 4000000
            desc Router1
            ipv6 enable
            ipv6 add FE80::1 link-local
            ipv6 rip PROZESSNAME enable
            ipv6 rip PROZESSNAME default-information originate
            no shutdown

        int se0/1/1
            clock rate 2000000
            desc Router1
            ipv6 enable
            ipv6 add FE80::2 link-local
            ipv6 rip PROZESSNAME enable
            ipv6 rip PROZESSNAME default-information originate
            no shutdown
            exit
        exit
    cop ru st
```

## Router 2

```PacketTracer
enable
    conf t
        hostname Router2
        ipv6 unicast-routing

        ipv6 router rip PROZESSNAME
            exit

        int fa0/0
            ipv6 enable
            ipv6 add IP2 eui-64
            ipv6 rip PROZESSNAME enable
            no shutdown

        int se0/1/0
            clock rate 2000000
            desc Router2
            ipv6 enable
            ipv6 add FE80::2 link-local
            ipv6 rip PROZESSNAME enable
            no shutdown

        int se0/1/1
            clock rate 2000000
            desc Router2
            ipv6 enable
            ipv6 add FE80::1 link-local
            ipv6 rip PROZESSNAME enable
            no shutdown
            exit
        exit
    cop r s
```

## Router 3

```PacketTracer
enable
    conf t
        hostname Router3
        ipv6 unicast-routing

        ipv6 router rip PROZESSNAME
            exit

        int fa0/0
            ipv6 enable
            ipv6 add IP3 eui-64
            ipv6 rip PROZESSNAME enable
            no shutdown

        int se0/1/0
            clock rate 4000000
            desc Router3
            ipv6 enable
            ipv6 add FE80::2 link-local
            ipv6 rip PROZESSNAME enable
            no shutdown

        int se0/1/1
            clock rate 2000000
            desc Router3
            ipv6 enable
            ipv6 add FE80::1 link-local
            ipv6 rip PROZESSNAME enable
            no shutdown
            exit
        exit
    cop r s
```