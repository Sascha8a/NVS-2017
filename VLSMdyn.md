# VLSMdyn

## Variablen

## Router 1

```PacketTracer
enable
    conf t
        hostname R1
        banner motd #Authorized entry only!#
        int s0/0
            no shutdown
            description Router1
            clock rate 2000000
            bandwidth 2000
            ip addr 195.196.120.225 255.255.255.252
        int fa0/0
            no shutdown
            ip addr 195.196.120.222 255.255.255.224
            exit
        ip route 195.196.120.0 255.255.255.128 Se0/0
        ip route 195.196.120.128 255.255.255.192 Se0/0
        exit
    cop ru st
```

## Router 2

```PacketTracer
enable
    conf t
        hostname R2
        banner motd #Authorized entry only!#
        int s0/0
            no shutdown
            description Router2
            bandwidth 2000
            ip addr 195.196.120.226 255.255.255.252
        int s0/1
            no shutdown
            description Router2
            clock rate 4000000
            bandwidth 4000
            ip addr 195.196.120.229 255.255.255.252
        int fa0/0
            no shutdown
            ip addr 195.196.120.126 255.255.255.128
            exit
        ip route 195.196.120.192 255.255.255.224 Se0/0
        ip route 195.196.120.128 255.255.255.192 Se0/1
        exit
    cop ru st
```

## Router 3

```PacketTracer
enable
    conf t
        hostname R3
        banner motd #Authorized entry only!#
        int s0/0
            no shutdown
            description Router3
            bandwidth 4000
            ip addr 195.196.120.230 255.255.255.252
        int fa0/0
            no shutdown
            ip addr 195.196.120.190 255.255.255.192
            exit
        ip route 195.196.120.192 255.255.255.224 Se0/0
        ip route 195.196.120.0 255.255.255.128 Se0/0
        exit
    cop ru st
```